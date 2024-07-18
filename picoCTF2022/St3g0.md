# St3g0 - forensics

## Case

Download this image and find the flag.

Image: https://artifacts.picoctf.net/c/216/pico.flag.png

## Solution

Firstly, I tried exiftool to examine the metadata of the file:

```bash
exiftool pico.flag.png
```

!image

The output appears normal, so I tried strings to see if there is any human-readable information contained within the files hex data:

```bash 
strings pico.flag.png
```

!image

Again, I don't notice anything suspicious in the output. I then try zsteg as a general scan for data hidden / concealed using steganographic techniques:

```bash
zsteg -a pico.flag.png
```

!image

The flag can be clearly seen in the first line of the output. Initially, I did not understand how this data was concealed, so here is how it works:

### Least Significant Bit (LSB) Encoding