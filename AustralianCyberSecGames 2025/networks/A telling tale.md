# A telling tale - net

## Challenge 

One the staffers at the Centrist Union campaign office had a vist from a rather unusual visitor this week, a fortune teller! The fortune teller left a business card that mentioned something about an online fortune telling service.

The staffer gave this business card to the pary leader, Alyssa Chen, who decided to try it out.

Can you find what the fortune teller had to say to Alyssa via this service?

`office.centrist.freedonia.vote`

## Solve

Running an `nmap` scan on the domain we get the following results:

```
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-17 20:18 EDT
Nmap scan report for office.centrist.freedonia.vote (54.153.192.232)
Host is up (0.016s latency).
rDNS record for 54.153.192.232: ec2-54-153-192-232.ap-southeast-2.compute.amazonaws.com
Not shown: 995 closed tcp ports (reset)
PORT   STATE    SERVICE
7/tcp  open     echo
9/tcp  open     discard
17/tcp open     qotd
19/tcp open     chargen
22/tcp filtered ssh

Nmap done: 1 IP address (1 host up) scanned in 1.94 seconds
```

`qotd` refers to 'Quote of the Day' which refers to an old internet protocol which provides a message or quote to the connecting client. Sounds fortune tellerish...

```bash
nc office.centrist.freedonia.vote 17
```

qotd.png

Connecting we find the flag as well as some clues which are useful in the later challenges.

`cysea{y0uv3_n3773d_a_f0r7un3_c0n6ratul4tion5}`