# n0s4n1ty 1 - web

## Challenge 

A developer has added profile picture upload functionality to a website. However, the implementation is flawed, and it presents an opportunity for you. Your mission, should you choose to accept it, is to navigate to the provided web page and locate the file upload area. Your ultimate goal is to find the hidden flag located in the /root directory.

## Solve

Visiting the webpage we can see that we have the ability to select and upload a file at `/upload.php` which is then stored at `uploads/<file-name>`.
Doing a quick test by uploading a text file, we can see that the application processes it and stores it in `uploads` with no error. We can infer there is no input sanitisation as you would usually expect the application to at least verify the file uploaded is an image by checking file extension / magic numbers as a bare minimum.

Subsequently we can source code for a php [webshell](https://gist.github.com/joswr1ght/22f40787de19d80d110b37fb79ac3985), upload it to the webpage, visit the end point and begin to access system resources.

The flag can be obtained with:

`sudo ls /root`

`sudo cat /root/flag.txt`

```
picoCTF{wh47_c4n_u_d0_wPHP_d698d800}
```
