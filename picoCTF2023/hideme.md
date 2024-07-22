# HideMe - forensics

## Case

Every file gets a flag. The SOC analyst saw one image been sent back and forth between two people. They decided to investigate and found out that there was more than what meets the eye 

Image: https://artifacts.picoctf.net/c/261/flag.png

## Solution

As an inital check on the image, I ran exiftool to analyse its metadata:

```bash
exiftool flag.png
```

!exiftool output

Notice warning message 'Trailer data after PNG IEND chunk'. Ran xxd to check hex data:

```bash 
xxd flag.png
```

!xxd output

Notice strings 'secret' and 'flag.png' in hex data after IEND chunk. This is suggestive of hidden files within the file, which can be extracted via binwalk:

```bash
binwalk -e flag.png
```

!binwalk output

Navigating to the extracted directory, we can use tree to view the location of the embedded 'flag.png':

!tree output

Open the image to reveal the flag:

**picoCTF{Hiddinng_An_imag3_within_@n_ima9e_96539bea}**


