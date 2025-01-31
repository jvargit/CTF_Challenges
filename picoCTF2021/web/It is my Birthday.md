# It is my Birthday - web 

## Challenge

I sent out 2 invitations to all of my friends for my birthday! I'll know if they get stolen because the two invites look similar, and they even have the same md5 hash, but they are slightly different! You wouldn't believe how long it took me to find a collision. Anyway, see if you're invited by submitting 2 PDFs to my website.

## Solve

The website provides the options to upload two pdf files. After a bit of experimentation, it is apparent the only criteria for upload is that the file has a `.pdf` extension.

So, I tried creating two text files with the `.pdf` extension containing plaintext pairs for a known MD5 collision:

`TEXTCOLLBYfGiJUETHQ4hAcKSMd5zYpgqf1YRDhkmxHkhPWptrkoyz28wnI9V0aHeAuaKnak`

`TEXTCOLLBYfGiJUETHQ4hEcKSMd5zYpgqf1YRDhkmxHkhPWptrkoyz28wnI9V0aHeAuaKnak`

Submitting them to the webpage:

![birthday](https://github.com/user-attachments/assets/93405abc-f151-4f76-82ae-39f0612045af)


`picoCTF{c0ngr4ts_u_r_1nv1t3d_3d3e4c57}`
