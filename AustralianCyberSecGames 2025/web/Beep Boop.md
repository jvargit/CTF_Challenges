# Beep Boop - web

## Challenge

The site's running fine, but something feels... mechanical. Not everything here was meant for humans — but then again, you’re not just any human, are you?

Some files weren’t meant to be seen. But just because they're disallowed doesn't mean they're invisible. Can you find what the bots were told to avoid?

Provided domain `https://renewal.freedonia.vote/`.

## Solve

The description is very much suggestive of checking the `robots.txt` file. Doing so reveals the URL:

`/MN4XGZLBPNZDAYRQORZV6NDOMRPXG4BRMRSXE435`

<img width="417" height="99" alt="robots" src="https://github.com/user-attachments/assets/8c21446f-acbd-49a0-9848-e011007e3150" />


Decoding from `base32` gives the flag:

`cysea{H1dd3n_1n_Pl41n_51gh7}`
