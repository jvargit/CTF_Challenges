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
!exiftool date and time images

There are a number of time stamps which can be modified via exiftool:

```
exiftool -AllDates='1970:01:01 00:00:00.001' -CreateDate='1970:01:01 00:00:00.001' -DateTimeOriginal='1970:01:01 00:00:00.001' -ModifyDate='1970:01:01 00:00:00.001' -SubSecCreateDate='1970:01:01 00:00:00.001' -SubSecDateTimeOriginal='1970:01:01 00:00:00.001' -SubSecModifyDate='1970:01:01 00:00:00.001' original.jpg
```

> AllDates is an exiftool shortcut that edits the three major EXIF tags - DateTimeOriginal, CreateDate and ModifyDate.

However, exiftool can only modify 'tags', not 'descriptions', and the remaining 'Time Stamp' description still needs to be changed:

!time stamp image

Doing a verbose rerun of exiftool filtering for 'Time' reveals a value for 'TimeStamp':

```
exiftool -v3 original.jpg | grep Time
```

!timestmap image

We can also see the location of this value in the hex code:

!hexcode image

Asking ChatGpt, the timestamp appears to be in milliseconds since the Unix epoch - which is our desired date (January 1, 1970). As a result, the value of the date portion of the timestamp must be changed to is all zero. The scenario requests the time is set to 00:00:00.001 (so one millisecond) and the updated hex code should also reflect this.

This can be done via hexedit:

```
hexedit original.jpg
```

Tab to toggle to ASCII, ctrl+S to search for '1700513181420'.

Replace hex code for '1700513181420' to '1700513181421':

!img

Ctrl+X to save and exit....

> Submit modified image to given nc connection to receive flag:

**picoCTF{71m3_7r4v311ng_p1c7ur3_a25174ab}**

