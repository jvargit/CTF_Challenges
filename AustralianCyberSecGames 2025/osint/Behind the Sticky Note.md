# Behind the Sticky Note - osint

## Challenge

A confidential focus group session was held last summer by the Freedonian Heritage League to refine their campaign messaging ahead of the election.

A leaked photo from the event surfaced online, but something feels off... Beyond just the people in the room.

<img width="3600" height="2400" alt="focus_group_FINALFINALFINALV3" src="https://github.com/user-attachments/assets/0984d474-007e-4a0b-ae4b-b5379e513e06" />


## Solve

Viewing the image, we can just zoom for one part of the flag:

<img width="1018" height="456" alt="Screenshot 2025-07-05 145741" src="https://github.com/user-attachments/assets/115f04d0-da66-4b2b-8af0-b79aa0b585d7" />


`k33p5_th3_d0ct0r_4w4y}`

The other half of the flag can be found by checking for the string `cysea` in the flag:

```bash
grep -a 'cysea' focus_group_FINALFINALFINALV3.png
```

<img width="759" height="112" alt="Screenshot 2025-07-05 150026" src="https://github.com/user-attachments/assets/4a9401fa-6eb0-4c9f-86e4-1122c8d724fc" />


`cysea{a_f0cu5_4_d4y_`

Assemble the two for the completed flag:

`cysea{a_f0cu5_4_d4y_k33p5_th3_d0ct0r_4w4y}`
