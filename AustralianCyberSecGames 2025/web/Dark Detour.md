# Dark Detour - web

## Challenge

Everyone wants to be in the room where it happens! You can always wait for the invite, or just go in blind!

## Solve

Visiting the provided there is a login page where I experimented with some basic 'blind' payloads.

This challenge ended up being a standard SQL injection:

- username: `'OR 1=1--`
- password: any

This logs us in to `/admin`:

<img width="751" height="193" alt="sqlinject" src="https://github.com/user-attachments/assets/83507202-2646-4805-a057-a56c2a5b1bed" />


`secedu{sql_d03s_n0t_s33_r0l3s}`

