# The Sound of Birds - osint

## Challenge

I just love the sound of birds in my backyard. These magpies are the cutest and visit me everyday. Sometimes I feel like they're trying to give me a message through their song... Oh well, I'm sure it's nothing important.

Provided `birds.wav` file

## Solve

This one was a matter of finding the right resource, which happened to be [here](https://futureboy.us/stegano/decinput.html).

Visiting the page and uploading the `.wav` file outputted the string:

`Y3lzZWF7c2luZ2luZ19pbl90aGVfcjRpbl85OHd5ZGY3OX0=`

Decoding from `base64` provides the flag:

`cysea{singing_in_the_r4in_98wydf79}`