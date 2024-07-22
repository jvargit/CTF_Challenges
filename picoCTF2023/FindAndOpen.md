# FindAndOpen - forensics

## Case

Someone might have hidden the password in the trace file.

File(s): 
Packet capture - https://artifacts.picoctf.net/c/493/dump.pcap
Locked zip - https://artifacts.picoctf.net/c/493/flag.zip

## Solution

Opening up the packet capture in Wireshark, I decided to scan through the contents of each packet as there were only 69 frames included in the capture. In doing so, I could observe 5 messages being sent across the network:

```
1. Flying on Ethernet secret: Is this the flag
2. iBwaWNvQ1RGe1Could the flag have been splitted?
3. AABBHHPJGTFRLKVGhpcyBpcyB0aGUgc2VjcmV0OiBwaWNvQ1RGe1IzNERJTkdfTE9LZF8=
4. PBwaWUvQ1RGesabababkjaASKBKSBACVVAVSDDSSSSDSKJBJS
5. PBwaWUvQ1RGe1Maybe try checking the other file
```

These were all collected from the 'Data' section of the Ethernet frames:

!pcap

I attempted to decode the string 'AABBHHPJGTFRLKVGhpcyBpcyB0aGUgc2VjcmV0OiBwaWNvQ1RGe1IzNERJTkdfTE9LZF8=' in CyberChef as it appeared to be base64 encoded, however I received an incoherent output:

!cyberchef

>Base64 encoded ciphertexts must have a length which is a multiple of 4.

After learning this, I realised the ciphertext was corrupted and began to experiment with altering it to see if I could obtain some understandable output. As the string was 70 characters long, I began by appending two additional '==' characters to the end of the text:

```
AABBHHPJGTFRLKVGhpcyBpcyB0aGUgc2VjcmV0OiBwaWNvQ1RGe1IzNERJTkdfTE9LZF8===
```

This still produced incorrect results. I then tried adding the additional '==' characters to the beginning of the string:

```
==AABBHHPJGTFRLKVGhpcyBpcyB0aGUgc2VjcmV0OiBwaWNvQ1RGe1IzNERJTkdfTE9LZF8=
```

This effectively returned the output:

```
This is the secret: picoCTF{R34DING_LOKd_
```

Entering the text 'picoCTF{R34DING_LOKd_' into the password prompt for the zip file successfully opened the file to reveal the flag:

**picoCTF{R34DING_LOKd_fil56_succ3ss_419835ef}**