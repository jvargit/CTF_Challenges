# Random-Inator - crypto

## Challenge

Dr. Heinz Doofenshmirtz plans to take over the Tri-State Area. He created this super secret uncrackable encryption program with the help of his robot buttler Norm. Help Perry the Platypus decrypt the message and sabotage his evil plans.

Provided `server.py` file:

```py
from redacted import PRNG, FLAG
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad

def encrypt(key, plaintext, iv):
    cipher = AES.new(key, AES.MODE_CBC, iv)
    ciphertext = cipher.encrypt(pad(plaintext, 16))
    return iv+ciphertext

P = PRNG()
KEY = P.getBytes(16)
IV = P.getBytes(16)

print(f"Doofenshmirtz Evil Incorporated!!!\n")
print(f"All right, I don't like to repeat myself here but it just happens\nAnyhow, here's the encrypted message: {encrypt(KEY, FLAG, IV).hex()}\nOhh, How I love EVIL")
while True:
    iv = P.getBytes(16) 
    try:
        pt = input("\nPlaintext >> ")
        pt = bytes.fromhex(pt)
    except KeyboardInterrupt:
        break
    except:
        print("Invalid input")
        continue

    ct = encrypt(KEY, pt, iv)
    print(ct.hex())
```

## Solve

In the source, we can see in the `encrypt()` function that the initialisation vector is prepended to to the ciphertext:

```py
return iv+ciphertext
```

Since the IV is 16 bytes long, and is represented as hexadecimal characters, it will be represented in the first 32 characters of the ciphertext.

Further, we can see that both `IV` and `KEY` are generated using the pseudo-random number generator:

```py
P = PRNG()
KEY = P.getBytes(16)
IV = P.getBytes(16)
```

Since we are able to openly view the IV in the ciphertext, and the key is generated using the same method, if there is repetition or a small set of outcomes, we may be able to extract an IV which could be used as a key.

Connecting to the server and doing a few test encryptions immediately shows a repeated IV:

img

```
da4f91417cc40c722bea594809044f3d
```

This value is identical in both the responses, so it seems probable that the PRNG `P` only generates a small set of outcomes.

We wrote a `pwntools` script to encrypt the same message 100 times, storing unique IV's in the list `ivs`:

```py
from pwn import *

ivs = []
HOST = "kashictf.iitbhucybersec.in" 
PORT = 60534                     


server = remote(HOST, PORT)


for _ in range(100):
  
    server.recvuntil(b"Plaintext >>")
    
  
    plaintext = "a1"
    server.sendline(plaintext)  
    
    
    ciphertext = server.recvline().strip()
    iv = ciphertext[:32]
    if iv.decode() not in ivs:
        ivs.append(iv.decode())
    
server.close()

print(ivs)
```

Running the script resulted in a list of 10 unique IV's:

`['a221a2e491207cf23f755e9cbc052de8', '6be0323ac7b2f98edc19907467950a36', 'f6a9d2852e5c9c5e249188bd32776866', '5b757cc3fe4d1d51ece7f972421ca89d', 'da4f91417cc40c722bea594809044f3d', '7aba152e75735bc86567d804294acc82', '674d8fb80bce301e336476b1dd4ef695', 'b375293656a8b9f32b6789eff95003d1', '5d03b39ad3d949e73d297734a9f84b72', '963483730151eb82840db14f17159c66']`

And we could use these to brute force for the correct IV/key combination in decryption:

```py
from Cryptodome.Cipher import AES
from Cryptodome.Util.Padding import unpad

ciphertext_hex = "5d03b39ad3d949e73d297734a9f84b721201f2978bc375c2c7e05f1888f2b49051b437a971f48dce5b712dff2fbcb6552e735e8cb1c1d9af1b0df1210aa166ec13669190d85c4e640edd3ecb42bc1609f85e4ca743722348beb037f7a81611e6"
ciphertext = bytes.fromhex(ciphertext_hex)

key_iv_list = [
    'a221a2e491207cf23f755e9cbc052de8', '6be0323ac7b2f98edc19907467950a36', 'f6a9d2852e5c9c5e249188bd32776866', 
    '5b757cc3fe4d1d51ece7f972421ca89d', 'da4f91417cc40c722bea594809044f3d', '7aba152e75735bc86567d804294acc82', 
    '674d8fb80bce301e336476b1dd4ef695', 'b375293656a8b9f32b6789eff95003d1', '5d03b39ad3d949e73d297734a9f84b72', 
    '963483730151eb82840db14f17159c66'
]

for key_hex in key_iv_list:
    for iv_hex in key_iv_list:
        key = bytes.fromhex(key_hex)
        iv = bytes.fromhex(iv_hex)
        try:
            cipher = AES.new(key, AES.MODE_CBC, iv)
            plaintext = unpad(cipher.decrypt(ciphertext[16:]), 16)
            print(f"Flag: {plaintext.decode()}")
            exit()
        except Exception as e:
            continue
```

Running the script presents the flag:

`KashiCTF{Y0u_brOK3_mY_R4Nd0m_In4t0r_Curse_yOu_Perry_tH3_Pl4TYpus_O0qEJEM5}`