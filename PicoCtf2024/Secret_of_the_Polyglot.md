# Secret of the Polyglot - forensics

## Case

The Network Operations Center (NOC) of your local institution picked up a suspicious file, they're getting conflicting information on what type of file it is. They've brought you in as an external expert to examine the file. Can you extract all the information from this strange file?

Challenge file can be downloaded via: https://artifacts.picoctf.net/c_titan/9/flag2of2-final.pdf

## Solution

Viewing the pdf file in GUI, it appears half of the flag is contained on the screen:

```
1n_pn9_&_pdf_7f9bccd1}
```

To investigate the file further, exiftool is used to read its metadata:

```
exiftool flag2of2-final.pdf
```
Note the 'File-Type' field identifies the file as a *png*, not pdf:

![Exiftool Output](.assets\Screenshot 2024-07-02 104249.png)

To change file extension:

```
mv flag2of2-final.pdf realflag.png
```
Open image to reveal first half of the flag:

```
picoCTF{f1u3n7_
```

Combine for finalised flag:

**picoCTF{f1u3n7_1n_pn9_&_pdf_7f9bccd1}**