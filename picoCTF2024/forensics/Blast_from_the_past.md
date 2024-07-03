# Blast from the past - forensics

## Case

The judge for these pictures is a real fan of antiques. Can you age this photo to the specifications?
Set the timestamps on this picture to 1970:01:01 00:00:00.001+00:00 with as much precision as possible for each timestamp. In this example, +00:00 is a timezone adjustment. Any timezone is acceptable as long as the time is equivalent. As an example, this timestamp is acceptable as well: 1969:12:31 19:00:00.001-05:00. For timestamps without a timezone adjustment, put them in GMT time (+00:00). The checker program provides the timestamp needed for each.

Picture can be accessed here: https://artifacts.picoctf.net/c_mimas/89/original.jpg 

## Solution

Note - in order to obtain flag, a netcat connection to a launched instance must be established to submit / check image metadata has been correctly altered.

To begin with, I used exiftool to view the timestamps contained in the image:

```
exiftool original.jpg
```
There are a number of time stamps which can be modified via exiftool:
![exifdate](https://github.com/jvargit/CTF_Challenges/assets/161411434/b5a7679a-8905-4948-9973-f56816a1eaf1)
![exiftime](https://github.com/jvargit/CTF_Challenges/assets/161411434/4d9df5e1-0e0f-4367-b309-95eadd36fd8d)

To change these modifiable time stamps to requested date/time:
```
exiftool -AllDates='1970:01:01 00:00:00.001' -SubSecCreateDate='1970:01:01 00:00:00.001' -SubSecDateTimeOriginal='1970:01:01 00:00:00.001' -SubSecModifyDate='1970:01:01 00:00:00.001' original.jpg
```

> AllDates is an exiftool shortcut that edits the three major EXIF tags - DateTimeOriginal, CreateDate and ModifyDate.


However, exiftool can only modify 'tags', not 'descriptions', and the remaining 'Time Stamp' description still needs to be changed:

![timestamp](https://github.com/jvargit/CTF_Challenges/assets/161411434/9b3aa721-1d03-41a3-aa56-87b1286c2253)

Doing a verbose rerun of exiftool filtering for 'Time' reveals a value for 'TimeStamp':

```
exiftool -v3 original.jpg | grep Time
```

![verbose](https://github.com/jvargit/CTF_Challenges/assets/161411434/50437953-3c65-4fbc-bb07-7053580b5b9a)

We can also see the location of this value in the hexadecimal code:

![timestamphex](https://github.com/jvargit/CTF_Challenges/assets/161411434/4ea280a1-2a46-45fe-9df1-25acf2682dab)

ChatGPT tells me this timestamp appears to be in milliseconds since the Unix epoch - which is our desired date (January 1, 1970). As a result, the value of the date portion of the timestamp must be changed to all zero. The scenario requests the time is set to 00:00:00.001 (so one millisecond) and the updated hex code should also reflect this.

> Unix time - commonly used in computing, can also measure in seconds / microseconds / nanoseconds. The amount of that unit (s, ms, etc) which has elapsed since 1st Jan 1970 00:00:00 UTC - the Unix epoch.

This can be done via hexedit:

```
hexedit original.jpg
```

Tab to toggle to ASCII, ctrl+S to search for '1700513181420'.

Replace hex code for '1700513181420' to '0000000000001':

Original:
![hexbefore](https://github.com/jvargit/CTF_Challenges/assets/161411434/c3cbf482-bb90-4f71-a02b-68e2aaa0ba89)

Modified:
![hexafter](https://github.com/jvargit/CTF_Challenges/assets/161411434/38a9caa2-a36d-4288-9f2f-9effd82f8c3a)

Ctrl+X to save and exit....

Submit modified image to given nc connection to receive flag:

**picoCTF{71m3_7r4v311ng_p1c7ur3_a25174ab}**

