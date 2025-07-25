# Buried in the Cipher - crypto

## Challenge

The message lies deep beneath the surface — obscured by twisted letters, layered encryption, and ancient languages of the deep. First, it was scrambled with a twist on a classic rotation. Then, a tough key led it astray, a repeating current twisted the letters. And finally, it dove into the language of the deep - where only the most echolocative minds can decode it.

But nothing stays buried forever. To retrieve the truth, you must trace its path in reverse - from the echoes back to the source.

```
EEEEEEEEEeeEeEEeEEEEEEEEEeeeeEeEEEEEEEEEEeeeeEeEEEEEEEEEEeeeeEEEEEEEEEEEEeeeEeEeEEEEEEEEEeeeeEeeEEEEEEEEEEeeEeEEEEEEEEEEEeEeeeeeEEEEEEEEEeeEeEEeEEEEEEEEEeeEeEeEEEEEEEEEEeeeEeeeEEEEEEEEEeeEEEEeEEEEEEEEEEeeEEeeEEEEEEEEEeeEeeEEEEEEEEEEEeEeeeeeEEEEEEEEEEeeEeEEEEEEEEEEEeEeeeeeEEEEEEEEEeeEeEeEEEEEEEEEEEeeEeEEEEEEEEEEEeeeeEeEEEEEEEEEEeEeeeeeEEEEEEEEEeeeEEeEEEEEEEEEEEeeEEeeEEEEEEEEEEeeEEeeEEEEEEEEEeeEeEEeEEEEEEEEEeeEeeEeEEEEEEEEEeEeeeeeEEEEEEEEEeeeeEeEEEEEEEEEEeeEeEEeEEEEEEEEEeeEeeEEEEEEEEEEEeEeeeeeEEEEEEEEEeeeEeeeEEEEEEEEEEeeEEEEEEEEEEEEEeeeEeeeEEEEEEEEEeeeeEeEEEEEEEEEEEeeEEEEEEEEEEEEEeeeEEeeEEEEEEEEEeEeeeeeEEEEEEEEEEeeEeEEEEEEEEEEEeeEEeEEEEEEEEEEEEeeEeEEEEEEEEEEEeeeEEeEEEEEEEEEEeEeeeeeEEEEEEEEEeeeeEEEEEEEEEEEEeeEeeEEEEEEEEEEEeeeEeEEEEEEEEEEEEeeEeeeEEEEEEEEEeeEEEEeEEEEEEEEEeeEeeEEEEEEEEEEEeeeeEEEEEEEEEEEEeeEeeEEEEEEEEEEEeeEeEEEEEEEEEEEEEeeEeeeEEEEEEEEEeeeeeEe
```

## Solve

Pasting the text into CyberChef immediately gave the following with Magic identifying the use of a Cetacean Cipher 🐋:

`izzxu{4_ijwa3l_4_j4z_r33im_zil_w0wz0s_4d4r_xlt7alxlh7}`

The scenario mentions a 'tough key'. Vigenere cipher is a classic cipher requiring a 'repeating' key which is vulnerable to a known plaintext attack. 

> Vigenere cipher involves shifting plaintext characters by the amount of the correspondent key character's position in the alphabet, e.g plaintext `a` and key `c` would result in ciphertext `c` (since the index is 0 based)

Knowing this we can identify the first 5 characters of the key as `GBHTU`:

- `i` --> `c` = difference of 6 = `g`,
- `z` --> `y` = difference of 1 = `b`,
- `z` --> `s` = difference of 7 = `h`,
- `x` --> `e` = difference of 19 = `t`,
- `u` --> `a` = difference of 20 = `u`.

Inputting this into CyberChef gives the flag:

`cysea{4_ciph3r_4_d4y_k33ps_the_d0ct0r_4w4y_dfs7tsdfg7}`