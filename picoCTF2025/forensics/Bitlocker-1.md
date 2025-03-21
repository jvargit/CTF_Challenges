# Bit-locker 1

## Challenge

Jacky is not very knowledgable about the best security passwords and used a simple password to encrypt their BitLocker drive. See if you can break through the encryption!

## Solve

*Credit to my teammate `Chensenn` for the solve!* 

Downloading the file we are provided with `bitlocker-1.dd` (raw disk image) file which we know is BitLocker encrypted.
The decryption key hash can be obtained using John the Ripper’s `bitlocker2john` tool:

```
sudo bitlocker2john -i bitlocker-1.dd > hashes.txt
```

Running the command returns 4 hashes:

- \$bitlocker\$0\$….	(User password hash)
- \$bitlocker\$1\$…. 	(Recovery password hash)
- \$bitlocker\$2\$…. 	(Startup key hash)
- \$bitlocker\$3\$…. 	(Volume Master Key (VMK) Hash)


We are trying to crack the user password, so we copy the hash beginning with `$bitlocker$0$` to a new file `hash.txt` and move over to `hashcat` to begin cracking.

Viewing the [examples page](https://hashcat.net/wiki/doku.php?id=example_hashes) shows that the relevant Hash-Mode for Bitlocker hashes is `22100`. We crack the hash with:

```bash
hashcat -m 22100 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
```

We find that the password used to decrypt the BitLocker drive is:

```
jacqueline
```

From here we can:

- Create a directory to store the mounted drive

```bash
sudo mkdir -p /mnt/bitlocker
```

- Decrypt the drive using `dislocker`:

```bash
sudo dislocker -r -V bitlocker-1.dd -u”jacqueline” -- /mnt/bitlocker
```

- Create directory to mount decrypted drive

```bash
sudo mkdir -p /mnt/decrypted
```

- Mount the decrypted drive
  
```bash
sudo mount -o loop /mnt/bitlocker/dislocker-file /mnt/decrypted
```

- View contents of decrypted drive

```bash
ls decrypted
```

- Read flag

```bash
cat decrypted/flag.txt
```

```
picoCTF{us3_b3tt3r_p4ssw0rd5_pl5!_3242adb1}
```
