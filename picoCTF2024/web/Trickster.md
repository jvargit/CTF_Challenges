# Trickster - web exploitation

## Case

I found a web app that can help process images: PNG images only!

## Solve:

Since we have a file upload my first thought is to try and achieve a web shell by embedding a payload within the file uploaded.

Firstly, I did some initial enumeration using `gobuster` and found the /uploads page:

```bash
gobuster dir -u http://atlas.picoctf.net:53251/ -w /usr/share/dirb/wordlists/common.txt
```

img

Presumably this is where any uploaded files will be stored and theoretically where a reverse shell could be accessed if achieved.

Then, I started to experiment uploading different files to gain an understanding of the criteria the application was using to determine whether a file was valid or not.

Uploading JPEG file:

img

So I renamed my JPEG file to include the `.png` extension and tried again:

img

The application prints the magic numbers of a JPEG file - so it likely expects the magic numbers of a PNG which are `89 50 4e 47`. 

Using this information, I can begin trying to embed php code into a file meeting these two conditions to successfully achieve a web shell:

1. Create empty png file in hexedit:

I created `empty.png` and wrote in the magic numbers `89 50 4e 47` using `hexedit`.

2. Sourced [shellcode](https://gist.github.com/joswr1ght/22f40787de19d80d110b37fb79ac3985):

3. Appended shellcode to `empty.png`:

```bash
cat shellcode.php >> empty.png
```

4. Renamed `empty.png` so application recognises as php while still fulfilling file upload criteria:

```bash
mv empty.png empty.png.php
```

5. Upload file to application

Success! The file was uploaded. Visiting `/uploads/empty.png.php` presents a web shell which can be interacted with to search the file system:

```bash
cd ../ && ls
```

The `MQZWCYZWGI2WE.txt` seems strange and visiting the directory presents the flag:

**picoCTF{c3rt!fi3d_Xp3rt_tr1ckst3r_d3ac625b}**


