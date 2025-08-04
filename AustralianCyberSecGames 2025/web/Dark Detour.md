# Dark Detour - web

## Challenge

Everyone wants to be in the room where it happens! You can always wait for the invite, or just go in blind!

## Solve

Visiting the provided there is a login page where I experimented with some basic 'blind' payloads.

This challenge ended up being a standard SQL injection:

- username: `'OR 1=1--`
- password: any

This logs us in to `/admin`:

sqlinject.png 

`secedu{sql_d03s_n0t_s33_r0l3s}`

