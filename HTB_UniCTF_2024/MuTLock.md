# MuTLock - crypto

## Context:

Provided a `source.py` and `output.txt` file containing encryption algorithm and respective ciphertext.

## Solve:

Firstly, to begin my solution, I broke down the encryption process:

```py
def main():
    # Split the flag
    flag_half1 = FLAG[:len(FLAG)//2]
    flag_half2 = FLAG[len(FLAG)//2:]
    
    encrypted_flags = []
    
    for _ in range(2):
        key_seed, xor_key = get_timestamp_based_keys()
        key = generate_key(key_seed)
        encrypted_half = polyalphabetic_encrypt(flag_half1 if len(encrypted_flags) == 0 else flag_half2, key)
        encrypted_half = xor_cipher(encrypted_half, xor_key)
        encrypted_flags.append(encrypted_half.hex())
        time.sleep(1)
```

1. Xor key & key seed generated from `get_timestamp_based_keys()`
2. Flag split in half
3. Key generated using a seed value
4. Polyalphabet encrypt function (using generated key)
5. Returns as base64 string
6. Xor encrypt (using xor key)
7. Wait 1 second, and repeat for the second half of the plaintext

So, we can obtain the plaintext by simply reversing these steps. We can begin this by finding the xor key to reverse the xor operation taken in the final step:

```py
def get_timestamp_based_keys():
    timestamp = int(time.time())
    if timestamp % 2 == 0:
        key_seed = random.randint(1, 1000)
        xor_key = 42
    else:
        key_seed = 42
        xor_key = random.randint(1, 255)
    return key_seed, xor_key
```

As we can see in `get_timestamp_based_keys()`, the value for the `xor_key` and `key_seed` is determined by whether the value of `timestamp` (number of seconds since epoch) is odd or even. 

Consequently, one half of the plaintext will be encrypted using the 'even' set of parameters, and thanks to the `time.sleep(1)`, the other half will be encrypted using the 'odd' set of parameters.

It turns out that the first half of the flag is encrypted using the 'odd' set of parameters. It can be reversed as such:

```py
decrypted =[]

h1 = '00071134013a3c1c00423f330704382d00420d331d04383d00420134044f383300062f34063a383e0006443310043839004315340314382f004240331c043815004358331b4f3830'
h1_bytes = bytes.fromhex(h1)
seed = 42
key = generate_key(seed)

for i in range(1, 255):
    try:
        xored = xor_decrypt(h1_bytes, i)
        decoded = base64.b64decode(xored).decode('utf-8')
        plain = polyalphabetic_decrypt(decoded, key)
        decrypted.append(plain)

    except:
        pass
```

1. Reverse XOR operation:

Since the XOR key could be a random value from 1-255, a for loop is used to iterate through all these values. The try and except statements bypass errors preventing the loop from running as a result of bad key values.

2. Decode the base64 string :

The initial `b64decode(xored)` returns the original bytes, and the subsequent `.decode('utf-8')` converts those bytes to a string. 

3. Reverse the polyalphabetic encryption:

The function enumerates the ciphertext (setting index values in i and character values in char), subtracts the ASCII value which was added to the plaintext during encryption, and appends the final result to `decrypted`.

And, it works:

`HTB{timestamp_based_encrypt`

Now, we just repeat the same decryption process for the second half of the flag, with the new parameters:

```py
for i in range(1, 1000):
    key = generate_key(i) 
    xored = xor_decrypt(h2_bytes, xor)
    decoded = base64.b64decode(xored).decode('utf-8')
    plain = polyalphabetic_decrypt(decoded, key)

    if plain[len(plain)-1] == '}':
        decrypted.append(plain)


print(''.join(decrypted))
```
Since the errors don't filter out bad results for us, I use an if check to see if the last character in the decrypted output is '}' - signalling the end of the flag.

Running the code produces the following output:

![image](https://github.com/user-attachments/assets/a93d10ac-63f4-4688-83a0-3cd78ac03cc0)


Not the cleanest of solves, but wihtout too much difficulty we can see the flag:

`HTB{timestamp_based_encryption_is_so_secure_i_promise}`
