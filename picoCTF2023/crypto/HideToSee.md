# HideToSee - cryptography

## Case

How about some hide and seek heh?

Image: https://artifacts.picoctf.net/c/240/atbash.jpg

## Solution

Initially, ran strings, exiftool and binwalk on 'atbash.jpg' and did not observe any obvious irregularities in the output.

Looked back at scenario and decided to try extracting via steghide:

```bash
steghide extract -sf atbash.jpg
```

>Steghide is a steganography program that can embed / extract data in JPEG, BMP, WAV and AU files.

![steghide](https://github.com/user-attachments/assets/7c67ae3a-98e9-473a-b149-e220f47958e0)


Obtained string:

```
krxlXGU{zgyzhs_xizxp_xz00558y}
```

As file is named 'atbash.jpg', I used CyberChef's Atbash decode tool to obtain the flag:

![atbash](https://github.com/user-attachments/assets/fd725730-8936-42eb-968f-445a0d2f33f3)

**picoCTF{atbash_crack_ca00558b}**

