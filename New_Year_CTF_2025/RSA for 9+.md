# RSA for 9+ - Misc 

## Challenge

Provided nc command to connect to server. Server provides RSA params n, d, e, c and asks for base64 encoded plaintext. Times out after 5 seconds, with approximately 50 rounds of repetition.

## Solve

The core of this challenge is to write a script which can automatically receive parameters from the server, decrypt the plaintext and send it back to server. This was my first time using `pwntools` and was very enjoyable to learn about its functionality.

Firstly, I wrote a function to decrypt the base64 encoded ciphertext provided in the servers message:

```py
def decrypt(d, n, c):
    try:
        base_64_decoded = base64.b64decode(c)
        cip_num = int.from_bytes(base_64_decoded, 'little')  
        p_int = pow(cip_num, d, n)
        p_bytes = long_to_bytes(p_int)
        reversed_plaintext = p_bytes[::-1]  
        return base64.b64encode(reversed_plaintext).decode()
    except Exception as e:
        print(f"Error during decryption: {e}")
        return ''
```

It took a bit of testing to make the ciphertext suit the servers requirements to achieve the correct answer:
- Specifying little endian encoding in `int.from_bytes()`
    - Big endian (default for `int.from_bytes()`) encoding provided a nonsense output
- Reversing the plaintext 
    - I found that when I had decoded the plaintext and printed output, it was written backwards

Now that the `decrypt()` function was working, I began to experiment with the `pwntools` library:

> `server = remote('ctf.mf.grsu.by', 9019)`
- Establishes connection to given domain and stores object in `server`. Allows for various methods to be called on the object:

> `message = server.recv()`
- Receives continuous byte stream of data until server stops sending

> `remainder = server.recvline().decode()`
- Receives a line of data from the server

> `challenge = server.recvuntil(b'phrase')`
- Receives incoming byte stream from server until a passed `phrase` is read and stores in `challenge`. Can be converted to plaintext by appending `.decode()`

> `server.sendline(plain)`
- Sends the chosen plaintext back to the server

> `server.close()`
- Closes connection with server in case server does not already

Using the various methods called on the object returned by `remote()` I was able to set up an automated connection with the server. Now, I had to find a way to extract the values `c`, `d` and `n` from the servers output. It turned out this was possible through regular expressions:

```py
        d = int(re.search(r"d: (0x[0-9a-fA-F]+)", challenge).group(1), 16)
        n = int(re.search(r"n: (0x[0-9a-fA-F]+)", challenge).group(1), 16)
        c = re.search(r"secret ciphertext \(b64\): (.+)", challenge).group(1)
```
> `r"x:(0x[0-9a-fA-F]+)"`
- Searches for `x` (`c`, `d`, `n`) and subsequent string containing hexadecimal characters (`(0x[0-9a-fA-F]+)`)

> `, challenge`
- Specifies challenge as the source string to search for regex

>`.group(1)`
- Captures just the value from the matched regex (`group(0)` would include `d`, `n`, `c` etc)

>`, 16)`
- Specifies that the number being read is a base16 hex number so that it can be correctly converted through `int()`

Using these components I was able to construct this code to solve the challenge:

```py
import base64
from Cryptodome.Util.number import long_to_bytes
from pwn import *
import re


def decrypt(d, n, c):
    try:
        base_64_decoded = base64.b64decode(c)
        cip_num = int.from_bytes(base_64_decoded, 'little')  
        p_int = pow(cip_num, d, n)
        p_bytes = long_to_bytes(p_int)
        reversed_plaintext = p_bytes[::-1]  
        return base64.b64encode(reversed_plaintext).decode()
    except Exception as e:
        print(f"Error during decryption: {e}")
        return ''

server = remote('ctf.mf.grsu.by', 9019)
count = 100

for i in range(60):
        
        #Check if last round. If so, receive data without 'until' requirements
        if i == count:
             message = server.recv().decode()
             print(message)
             break
             
        # Capture whole challenge
        challenge = server.recvuntil(b"Plaintext is (b64):").decode()
        print(f"Challenge {i + 1} received:\n{challenge}")

        # Extract values 
        e = int(re.search(r"e: (0x[0-9a-fA-F]+)", challenge).group(1), 16)
        d = int(re.search(r"d: (0x[0-9a-fA-F]+)", challenge).group(1), 16)
        n = int(re.search(r"n: (0x[0-9a-fA-F]+)", challenge).group(1), 16)
        c = re.search(r"secret ciphertext \(b64\): (.+)", challenge).group(1)

        # On first iteration, check how many rounds will be played
        if i == 0:
            count = int(re.search(r"Раунд 1/(\d+)", challenge).group(1))
        
      
        plain = decrypt(d, n, c)
        server.sendline(plain)

        # Receive server response confiriming correct plaintext
        partial_response = server.recvuntil("Правильно".encode()).decode()

        # Read the rest of the line after 'Правильно'
        remainder = server.recvline().decode()
        response = partial_response + remainder
        print(f"Server response for challenge {i + 1}:\n{response}")

server.close()
```

One part which took a while was managing how the script would handle completing the final round. I realised that by using regex to find the amount of final rounds I could avoid the error I was encountering as the program continued looking for certain bytes in the servers response. Running the script produced the flag:

`grodno{fb3f00Take_y0urself_the_b1ggest_candy1ffce6}`