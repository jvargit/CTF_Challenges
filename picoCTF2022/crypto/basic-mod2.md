# basic-mod2 - cryptography

## Case:

Take each number mod 41 and find the modular inverse for the result. Then map to the following character set: 1-26 are the alphabet, 27-36 are the decimal digits, and 37 is an underscore.

Message:

```
432 331 192 108 180 50 231 188 105 51 364 168 344 195 297 342 292 198 448 62 236 342 63
```

## Solution: 

To decode this message, I wrote a simple piece of Python code to carry out the instructions given in the challenge. The most interesting part about this challenge was computing the modular inverse of a number using python, which I learned could be achieved by:

```python 
pow(x, -1, n)
```

Where `x` is the number you want to find the inverse for, and `n` is the value of the modular operation.

This was my code for decoding the message:

```python
nums = [432,331,192,108,180,50,231,188,105,51,364,168,344,195,297,342,292,198,448,62,236,342,63]
modded = []
inv = []
chars = list('0abcdefghijklmnopqrstuvwxyz0123456789_')
plain = []

for num in nums:
    modded.append(num % 41)

for num in modded:
    inv.append(pow(num, -1, 41))

for num in inv:
    plain.append(chars[num])

print(''.join(plain))
```

The code stores the original numbers in `nums`, the numbers after the modular operation with 41 in `modded`, and the modular inverse of each number in `inv`. 

The inverses of each number are then used as indexes to map to the characters of the plaintext message.

Running the program produces the flag:

**picoCTF{1nv3r53ly_h4rd_c680bdc1}**





