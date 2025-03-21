# RED - forensics

## Challenge

We are provided an image `red.png`.
Running `exiftool` on the image shows a poem in its metadata:

```
Crimson heart, vibrant and bold,.Hearts flutter at your sight..Evenings glow softly red,.Cherries burst with sweet life..Kisses linger with your warmth..Love deep as merlot..Scarlet leaves falling softly,.Bold in every stroke.
```

Singling out the capitalisations, we see:

```
CHECK LSB
```

Hmmmm…

Running `zsteg -a red.png` shows a big base64 encoded text hidden via LSB stego:

```
cGljb0NURntyM2RfMXNfdGgzX3VsdDFtNHQzX2N1cjNfZjByXzU0ZG4zNTVffQ==cGljb0NURntyM2RfMXNfdGgzX3VsdDFtNHQzX2N1cjNfZjByXzU0ZG4zNTVffQ==cGljb0NURntyM2RfMXNfdGgzX3VsdDFtNHQzX2N1cjNfZjByXzU0ZG4zNTVffQ==cGljb0NURntyM2RfMXNfdGgzX3VsdDFtNHQzX2N1cjNfZjByXzU0ZG4zNTVffQ==
```

Decoding gives us the flag:


```
picoCTF{r3d_1s_th3_ult1m4t3_cur3_f0r_54dn355_}
```
