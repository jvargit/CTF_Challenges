# Custom encryption - cryptography

## Case

Can you get sense of this code file and write the function that will decode the given encrypted file content.

Encrypted flag: https://artifacts.picoctf.net/c_titan/17/enc_flag
Source code: https://artifacts.picoctf.net/c_titan/17/custom_encryption.py

## Solution

Excerpt from source code - 'test' function which encrypts a given plaintext string with a text key:

```python
    p = 97
    g = 31
    if not is_prime(p) and not is_prime(g):
        print("Enter prime numbers")
        return
    a = randint(p-10, p)
    b = randint(g-10, g)
    print(f"a = {a}")
    print(f"b = {b}")
    u = generator(g, a, p)
    v = generator(g, b, p)
    key = generator(v, a, p)
    b_key = generator(u, b, p)
    shared_key = None
    if key == b_key:
        shared_key = key
    else:
        print("Invalid key")
        return
    semi_cipher = dynamic_xor_encrypt(plain_text, text_key)
    cipher = encrypt(semi_cipher, shared_key)
    print(f'cipher is: {cipher}')
```

The encryption mechanism is as follows:

#### 1. Generate keys based off two prime numbers (p, g), as well as two random numbers ranging from (p-10, p), (g-10, g).

```python
    p = 97
    g = 31
    if not is_prime(p) and not is_prime(g):
        print("Enter prime numbers")
        return
    a = randint(p-10, p)
    b = randint(g-10, g)
    print(f"a = {a}")
    print(f"b = {b}")
    u = generator(g, a, p)
    v = generator(g, b, p)
    key = generator(v, a, p)
    b_key = generator(u, b, p)
    shared_key = None
    if key == b_key:
        shared_key = key
    else:
        print("Invalid key")
        return
```


#### 2. Perform XOR encryption on the input text, using another given string as a text key. In this case, the given text key is "trudeau":

```python
def dynamic_xor_encrypt(plaintext, text_key):
    cipher_text = ""
    key_length = len(text_key)
    for i, char in enumerate(plaintext[::-1]):
        key_char = text_key[i % key_length]
        encrypted_char = chr(ord(char) ^ ord(key_char))
        cipher_text += encrypted_char
    return cipher_text
```
>enumerate () gets both the index 'i' and character 'char' of each character in plaintext

- Takes input text and given text key ("trudeau")
- Iterates through the plain text in reverse
    - XOR's plaintext char ASCII value with text_key char ASCII value
    - Converts result back to char
- Appends resulting 'encrypted_char' to cipher_text
- Stored in 'semi_cipher' variable


#### 3. Perform a further level of encryption using the 'encrypt' function:

```python
def encrypt(plaintext, key):
    cipher = []
    for char in plaintext:
        cipher.append(((ord(char) * key*311)))
    return cipher
```

- Takes input text (semi_cipher) and given key (shared key)
- For each letter in the text:
  - Converts to ASCII code for corresponding character
  - Multiplies by key
  - Multiplies by 311
- Stored inside 'cipher' variable

So, in order to decrypt the ciphertext, I reversed the steps of the algorithm to create the decryption process:

```python
def solve (message):
  key = keys("hello")
  decipher = decrypt(cipher, key)
  print(f'decrypted cipher is: {decipher}') 
  the_answer = dynamic_xor_encrypt(decipher[::-1], "trudeau")
  print(f'The answer: {the_answer}')  
  flag = the_answer[::-1]
  print(f'Flag: {flag}')
  
if __name__ == "__main__":
    message = sys.argv[1]
    solve(message)
```
![output of solved function](https://github.com/jvargit/CTF_Challenges/assets/161411434/13bc95ab-c478-4fba-8d67-287c6b59f894)

#### Firstly, I wrote a function 'decrypt()' to reverse the steps of the 'encrypt()' function:

```python
def decrypt(cipher, key):
    deciphered = []
    for num in cipher:

    	num = num / 311
    	num = num / key
    	num = int(num)
    	num = chr(num)
    	deciphered.append(num)
    return deciphered
```
- Takes ciphertext as input alongside generated key
- For each number in the ciphertext array:
    - Divide by 311
    - Divide by key 
    - Convert to int, convert to char
    - Append to 'deciphered' array

This successfully reverted the 'cipher' variable to its 'semi_cipher' state. Now, only the XOR encryption had to be decrypted before retrieving the flag:

>XOR encryption can be decrypted by rerunning the encryption process on the ciphertext using the same key that was used to encrypt it. For example:

```markdown
  1100                  
^ 1010
------
  0110

  0110
^ 1010
------
  1100
```

#### So, to decrypt the 'semi_cipher' version of the given ciphertext, I call the 'dynamic_xor_encrypt()' function with the reversed semi-cipher and the given text key:

```python
the_answer = dynamic_xor_encrypt(decipher[::-1], "trudeau")
```

The returns the string:

```
}679f14b8_d6tp0rc2d_motsuc{FTCocip
```

Which can be reversed:

```
flag = the_answer[::-1]
```

To obtain the flag:
**picoCTF{custom_d2cr0pt6d_8b41f976}**




