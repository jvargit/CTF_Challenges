# Lottery ticket - net

## Challenge

The leader of the Centrist Union, Alyssa Chen knows that a political career is a life full of gambles. To remind herself of how much luck plays a part in her political fortunes she buys a ticket in the Freedonia State Lottery every month.

This month she left a copy of her ticket on the Centrist Union's office server. Can you find the ticket and repeat the numbers back to her?

(It is recommended that you solve "A telling tale" before attempting this challenge)

## Solve

From the previous challenge, we have two 'clue' messages:

- `Got off at the wrong stop? Maybe you should try a different form of transport.`

- `If you want your luck to hold, make sure you have the winning numbers.`

And also the results of the `nmap` scan:

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

Focusing on the first clue, it's highly suggestive of switching transport protocols from `TCP` to `UDP`. Quickly trying to send a `UDP` packet to each port showed the `echo` service was a hit:

`nc -u office.centrist.freedonia.vote 7`

`cysea{fr33d0n14_5ta73_l0773ry_numb3r5_202,97,131,1033}`