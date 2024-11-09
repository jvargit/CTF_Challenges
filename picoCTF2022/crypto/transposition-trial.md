# transposition trial - cryptography

## Case:

Our data got corrupted on the way here. Luckily, nothing got replaced, but every block of 3 got scrambled around! The first word seems to be three letters long, maybe you can use that to recover the rest of the message.

Ciphertext = heTfl g as iicpCTo{7F4NRP051N5_16_35P3X51N3_V091B0AE}2

## Solution: 

Looking at the ciphertext, which we are told is split into blocks of three, we can see:

'The' --> 'heT' ,

' 'fl --> fl' ' ,

ag' ' --> g' 'a etc..

Each character in a block of three is shifted back one space, and if it is the first character in the block of three, its place wraps around to be the last character in the block.

I wrote the following code to reverse this:

```python
cipher = "heTfl g as iicpCTo{7F4NRP051N5_16_35P3X51N3_V091B0AE}2"

split = 3
count = 0
iterations = len(cipher) / 3
new = cipher
plain = ''
while count < iterations:
    #store block of 3
    temp = new[:3]

    #perform rotation
    first = temp[0]
    second = temp[1]
    third = temp[2]

    temp = list(temp)
    temp[0] = third
    temp[1] = first
    temp[2] = second
    temp = ''.join(temp)

    plain += temp

    #move forward for next block
    new = cipher[split:]
    split+=3
    count+=1
    print(plain)
```

The code essentially:
- Selects a block of three from `cipher`
- Rearranges the characters so that they are in their original positions
- Appends the deciphered three characters to `plain`
- Repeats until reaching end of cipher

Running the program produces the following output:

![image](https://github.com/user-attachments/assets/3f1cc3fa-a14b-4560-a678-32ac76ec4fa7)

And the flag can be clearly obtained:

**picoCTF{7R4N5P051N6_15_3XP3N51V3_109AB02E}**
