# CanYouSee - forensics

## Case

How about some hide and seek?

Challenge file can be downloaded via: https://artifacts.picoctf.net/c_titan/128/unknown.zip

## Solution

Run exiftool on file to examine metadata:

```
exiftool ukn_reality.jpg
```

Note the abnormal 'Attribution URL' field containing the text:

```
cGljb0NURntNRTc0RDQ3QV9ISUREM05fM2I5MjA5YTJ9Cg==
```

![canyousee](https://github.com/jvargit/CTF_Challenges/assets/161411434/2cd99c42-c0bd-4bcc-b05f-1fe8c10c5a6f)


The equals **'=='** signs at the end of the string suggest it may be base64 encoded, as they are used as padding to ensure equal groups of 3 bytes. This is confirmed using CyberChef's 'From Base64' feature which reveals the flag:

**picoCTF{ME74D47A_HIDD3N_3b9209a2}**
