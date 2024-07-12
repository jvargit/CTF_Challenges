# Dear Diary - forensics

## Case

If you can find the flag on this disk image, we can close the case for good!

Disk image can be downloaded via: https://artifacts.picoctf.net/c_titan/63/disk.flag.img.gz

## Solution

'Close case' in the scenario suggested using Autopsy, so I imported the disk image and ran analysis.

![autopsygui](https://github.com/user-attachments/assets/3b4a673d-e6b4-4b0d-8fe1-eeab1abee0eb)


Located empty text file 'innocuous-file.txt'. Tried a number of methods to gain further information about the file, including exporting and running exiftool on it, locating the files directory and viewing others, and working through files of the same 'MIME' type - no success.

As I have had issues with Windows Autopsy GUI, decided to try analyse the image with the Linux version:

```sh
sudo autopsy 
```

![autopsy](https://github.com/user-attachments/assets/bca8f5a3-9359-4afd-a12d-50819b3910b4)


Connected to local host:

![createcase](https://github.com/user-attachments/assets/ef193fa5-7136-4439-94e4-831795018a62)

Imported disk image:
![importimage](https://github.com/user-attachments/assets/66e6193c-6492-4eaa-a481-3ceb1dfaf2a2)

Performed keyword search for 'innocuous-file.txt':
![keywordsearch](https://github.com/user-attachments/assets/8e4fc83e-8cec-4ff1-a9c3-4498194366ee)

Looking over the results, we can see the string 'pic' contained after the header in unit 1423344's hex contents:
![results](https://github.com/user-attachments/assets/38c3dcb0-270b-4e4b-9341-c3fae895a5ea)

Scanning through the following units, the remaining pieces of the flag can be assembled to achieve:

**picoCTF{1_533_n4m35_80d24b30}**

