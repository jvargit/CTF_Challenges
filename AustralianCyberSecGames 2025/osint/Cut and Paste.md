# Cut and Paste - osint

## Challenge

A confidential memo from Governor Henrik Stahl of the National Renewal Party has surfaced just before the election. It appears routine but investigators believe crucial data is hidden within.

The memo references an earlier operation involving a mid-April strategy drop, which may still be accessible.

Your task, if you choose to accept it is to uncover and reconstruct the full flag, hidden across multiple layers of this document and one external source.

Provided file `memo.pdf`

## Solve

Opening the pdf, we noticed the cursor change shape when hovering over **'momentum24_v3.pdf'**. Clicking the link, it directs you to a pastebin page where part of the flag can be found:

img

`4klbn56} `

The spacing on the word 'alive' also appears strange. Since the name of the challenge is Cut and Paste, we try to copy the whole document and see what happens: 

cpimg

`_alive_`

Lastly, the final part of the flag is found in the metadata of the pdf:

```bash
exiftool memo.pdf
```

cp2

Assemble the parts for the completed flag:

`cysea{a_time_to_be_alive_4klbn56}`