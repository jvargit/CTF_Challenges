# Dear Diary - forensics

## Case

If you can find the flag on this disk image, we can close the case for good!

Disk image can be downloaded via: https://artifacts.picoctf.net/c_titan/63/disk.flag.img.gz

## Solution

'Close case' in the scenario suggested using Autopsy, so I imported the disk image and ran analysis.

Located empty text file 'innocuous-file.txt'. Tried a number of methods to gain further information about the file, including exporting and running exiftool on it, locating the files directory and viewing others, and working through files of the same 'MIME' type.

As I have had issues with Windows Autopsy GUI, decided to try analyse the image with the Linux version:

```
sudo autopsy 
```

Connected to local host:

Imported disk image:

Performed keyword search for 'innocuous-file.txt':

Looking over the results, we can see the string 'pic' contained after the header in unit 1423344's hex contents:

Scanning through the following units, the remaining pieces of the flag can be assembled to achieve:

**picoCTF{1_533_n4m35_80d24b30}

