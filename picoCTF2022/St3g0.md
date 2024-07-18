# St3g0 - forensics

## Case

Download this image and find the flag.

Image: https://artifacts.picoctf.net/c/216/pico.flag.png

## Solution

Firstly, I tried exiftool to examine the metadata of the file:

```bash
exiftool pico.flag.png
```

![$t3g0 exif](https://github.com/user-attachments/assets/2e090146-1bd6-4814-8f55-7c4c03c7bd99)


The output appears normal, so I tried strings to see if there is any human-readable information contained within the files hex data:

```bash 
strings pico.flag.png
```

![$t3g0 strings](https://github.com/user-attachments/assets/802e83bb-178f-4770-94b7-771bf8a3b35a)


Again, I don't notice anything suspicious in the output. I then try zsteg as a general scan for data hidden / concealed using steganographic techniques:

```bash
zsteg -a pico.flag.png
```

![$t3g0 zsteg](https://github.com/user-attachments/assets/ab9fd143-21bd-48db-b1ff-36095be59315)


The flag can be clearly seen in the first line of the output. Initially, I did not understand how this data was concealed, so here is how it works:

### Least Significant Bit (LSB) Encoding
