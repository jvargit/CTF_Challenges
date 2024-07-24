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

!image

Obtained string:

```
krxlXGU{zgyzhs_xizxp_xz00558y}
```

As file is named 'atbash.jpg', I used CyberChef's Atbash decode tool to obtain the flag:

!image

**picoCTF{atbash_crack_ca00558b}**

