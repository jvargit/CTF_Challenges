# Eavesdrop - forensics

## Case

Provided packet capture and asked to find flag.

## Solution

Viewing the packet capture, there is only 75 packets - it shouldn't be too hard to manually check through the contents.

Locating the first TCP packet, I followed TCP stream and saw the following conversation right away:

img

So, we can see that some kind of encrypted file has been transferred to port 9002.

Applying the filter `tcp.dstport == 9002` we can see packet number 57 is where the data transfer takes place, the earlier / later packets are just parts of the TCP opening / closing of the session.

img2

In order to extract this encrypted file, I:

 - copied data as a hex dump

```
0000   53 61 6c 74 65 64 5f 5f 3c 4b 26 e8 b8 f9 1e 2c
0010   4a f8 03 1c fa f5 f8 f1 6f d4 0c 25 d4 03 14 e6
0020   49 7b 39 37 58 08 ab a1 86 f4 8d a4 2e ef a8 95
```

 - removed offset markers and spaces

```
53616c7465645f5f3c4b26e8b8f91e2c4af8031cfaf5f8f16fd40c25d40314e6497b39375808aba186f48da42eefa895
```

 - created empty file using touch 

 ```bash
 touch file.des3
 ```
 - pasted data in with hexedit

```bash
hexedit file.des3
```
im3

Now we simply need to run the `openssl` command found in tcp stream:

```bash
openssl des3 -d -salt -in file.des3 -out file.txt -k supersecretpassword123
```

Then check the contents of the newly created `file.txt`:

`picoCTF{nc_73115_411_0ee7267a}`