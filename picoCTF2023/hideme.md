# HideMe - forensics

## Case

Every file gets a flag. The SOC analyst saw one image been sent back and forth between two people. They decided to investigate and found out that there was more than what meets the eye 

Image: https://artifacts.picoctf.net/c/261/flag.png

## Solution

As an inital check on the image, I ran exiftool to analyse its metadata:

```bash
exiftool flag.png
```

![hidemeexif](https://github.com/user-attachments/assets/a55ec82d-64b6-4899-85c8-88b0f7b156af)

Notice warning message 'Trailer data after PNG IEND chunk'. Ran xxd to check hex data:

```bash 
xxd flag.png
```

![hidemexxd](https://github.com/user-attachments/assets/9fa64576-39f8-4a2c-be2a-2ec80eb7ba62)

Notice strings 'secret' and 'flag.png' in hex data after IEND chunk. This is suggestive of hidden files within the file, which can be extracted via binwalk:

```bash
binwalk -e flag.png
```

![hidemebinwalk](https://github.com/user-attachments/assets/6b4baae3-56f1-4da4-9024-1e8c902c3267)

Navigating to the extracted directory, we can use tree to view the location of the embedded 'flag.png':

![hidemetree](https://github.com/user-attachments/assets/ee1752f3-636d-40b5-9c63-0f7b91ac6012)

Open the image to reveal the flag:

**picoCTF{Hiddinng_An_imag3_within_@n_ima9e_96539bea}**


