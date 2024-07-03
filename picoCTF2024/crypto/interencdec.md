# interencdec - cryptography

## Case

Can you get the real meaning from this file.

File: https://artifacts.picoctf.net/c_titan/111/enc_flag

## Solution

The file appears to contain base64 encoded plaintext:

```
YidkM0JxZGtwQlRYdHFhR3g2YUhsZmF6TnFlVGwzWVROclh6ZzVNR3N5TXpjNWZRPT0nCg==
```

Decrypt using:

```
base64 -d enc_flag
```

Resulting in new string:

```
b'd3BqdkpBTXtqaGx6aHlfazNqeTl3YTNrXzg5MGsyMzc5fQ==
```

Which still appears to be base64 encoded. Removing the prefix '_b'_' and placing in CyberChef, get the string:

> '_b'_' indicates the string is a bytes object in Python. This prefix is placed when decoding a Base64 string as it is returned as a bytes object.

```
wpjvJAM{jhlzhy_k3jy9wa3k_890k2379}
```

Apply Caesar's Cipher at amount 19 to retrieve flag:

**picoCTF{caesar_d3cr9pt3d_890d2379}**


