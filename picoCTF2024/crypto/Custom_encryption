# Custom encryption - cryptography

## Case

Can you get sense of this code file and write the function that will decode the given encrypted file content.

Encrypted flag: https://artifacts.picoctf.net/c_titan/17/enc_flag
Source code: https://artifacts.picoctf.net/c_titan/17/custom_encryption.py

## Solution

Excerpt from source code:

'''python
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

1. Generate keys based off two prime numbers (p, g), as well as two random numbers ranging from (p-10, p), (g-10, g).

```
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

2. Perform XOR encryption on the input text, using another given string as a text key. In this case, the given text key is "trudeau":

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

3. Perform a further level of encryption using the 'encrypt' function:

```python
def encrypt(plaintext, key):
    cipher = []
    for char in plaintext:
        cipher.append(((ord(char) * key*311)))
    return cipher
```
    - Takes input text
    - For each letter in the text:
        - Converts to ASCII code for corresponding character
        - Multiplies by key
        - Multiplies by 311

