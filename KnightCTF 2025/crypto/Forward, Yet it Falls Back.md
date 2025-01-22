# Forward, Yet it Falls Back - Crypto

## Challenge

We discovered a peculiar string that appears standard but yields gibberish under normal decoding. Some analysts detect suspicious symmetry, hinting at reflection or an inverted dimension. Others suspect hidden block boundaries or a “backwards encoding,” while a few insist it's “rotated on a different axis.”

Our only clue: “Symbols may shift forward, but the key is often in reversing what we think is correct.”

Good luck peeling back the layers—sometimes you must step backward to see what’s right before you.

## Solve

We are provided with the following values:

```
base32: G7G2DGQ5SY4DCK5YVUDRROJI3UOAUUNTVR6XKDOKQO4CAAKK2MJA====
key = 0123456789ABCDEF
iv  = FEDCBA9876543210
```

The key and IV are both 16 bytes (128 bits) long. This suits the requirement for AES-128. In addition, the existence of an IV suggests Cipher Block Chaining (CBC) mode, so I attempted decryption using this mechanism:

```py
from Cryptodome.Cipher import AES
from Cryptodome.Util.Padding import unpad
import base64


def aes_decrypt(ct, key, iv):    
    c = base64.b32decode(ct)
    cipher = AES.new(key, AES.MODE_CBC, iv)
    decrypted = cipher.decrypt(c)
    unpadded = unpad(decrypted, AES.block_size)
    return unpadded.decode()

key = b'0123456789ABCDEF'  
iv = b'FEDCBA9876543210'  
c = 'G7G2DGQ5SY4DCK5YVUDRROJI3UOAUUNTVR6XKDOKQO4CAAKK2MJA===='

decrypted_data = aes_decrypt(c, key, iv)
reversed = decrypted_data[::-1]
print(f"Decrypted data: {reversed}")
```
> ` c = base64.b32decode(ct)`
- Base32 decode the encoded ciphertext
> `cipher = AES.new(key, AES.MODE_CBC, iv)`
- Create an AES cipher object, with the mode set to CBC
> `decrypted = cipher.decrypt(c)`
- Decrypt the ciphertext bytes
> `unpadded = unpad(decrypted, AES.block_size)`
- Remove padding (which is to fulfil block size of 16 bytes)

The output of the `aes_decrypt()` function is reversed, hence `reversed = decrypted_data[::-1]`.

Running the program presents the flag:

`KCTF{R3vers3_R3vers3_D3C0DE}`


