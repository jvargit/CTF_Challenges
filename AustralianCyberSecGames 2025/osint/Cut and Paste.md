# Cut and Paste - osint

## Challenge

A confidential memo from Governor Henrik Stahl of the National Renewal Party has surfaced just before the election. It appears routine but investigators believe crucial data is hidden within.

The memo references an earlier operation involving a mid-April strategy drop, which may still be accessible.

Your task, if you choose to accept it is to uncover and reconstruct the full flag, hidden across multiple layers of this document and one external source.

Provided file `memo.pdf`

## Solve

Opening the pdf, we noticed the cursor change shape when hovering over **'momentum24_v3.pdf'**. Clicking the link, it directs you to a pastebin page where part of the flag can be found:

<img width="472" height="386" alt="pastebin" src="https://github.com/user-attachments/assets/e011c353-f8ea-47a0-bb8e-f9ff985b4383" />


`4klbn56} `

The spacing on the word 'alive' also appears strange. Since the name of the challenge is Cut and Paste, we try to copy the whole document and see what happens: 

<img width="1443" height="705" alt="cp" src="https://github.com/user-attachments/assets/d39188b2-9fa9-4fee-b5fe-e6ffdb4d5173" />


`_alive_`

Lastly, the final part of the flag is found in the metadata of the pdf:

```bash
exiftool memo.pdf
```

<img width="664" height="329" alt="cp1" src="https://github.com/user-attachments/assets/9aeb3eb5-458c-4f6a-a70b-d822e0059e60" />


Assemble the parts for the completed flag:

`cysea{a_time_to_be_alive_4klbn56}`
