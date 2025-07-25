# Behind the Sticky Note - osint

## Challenge

A confidential focus group session was held last summer by the Freedonian Heritage League to refine their campaign messaging ahead of the election.

A leaked photo from the event surfaced online, but something feels off... Beyond just the people in the room.

img

## Solve

Viewing the image, we can just zoom for one part of the flag:

img

`k33p5_th3_d0ct0r_4w4y}`

The other half of the flag can be found by checking for the string `cysea` in the flag:

```bash
grep -a 'cysea' focus_group_FINALFINALFINALV3.png
```

img

`cysea{a_f0cu5_4_d4y_`

Assemble the two for the completed flag:

`cysea{a_f0cu5_4_d4y_k33p5_th3_d0ct0r_4w4y}`