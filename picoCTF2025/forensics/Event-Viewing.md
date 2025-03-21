#  Event-Viewing - forensics

## Challenge

One of the employees at your company has their computer infected by malware! Turns out every time they try to switch on the computer, it shuts down right after they log in. The story given by the employee is as follows:
They installed software using an installer they downloaded online
They ran the installed software but it seemed to do nothing
Now every time they bootup and login to their computer, a black command prompt screen quickly opens and closes and their computer shuts down instantly.
See if you can find evidence for the each of these events and retrieve the flag (split into 3 pieces) from the correct logs!

## Solve

To begin with, I filtered for events with the event ID 1074. These events relate to shut downs initiated by users or applications. We can see in the comment field of one of the shutdowns the following string:

```
dDawbF84MWJhM2ZlOX0=
```

base64 decoding provides us with the final part of the flag:

```
t00l_81ba3fe9}
```

Next I filtered for event ID 1033 - which logs successful msi installs. In one of the events we can see the following manufacturer:

```
MXNfYV9wcjN0dHlfdXMzZnVsXw==
```

base64 decoding provides us with the middle part of the flag:

```
1s_a_pr3tty_us3ful_
```

In this event, we can see that the program installed was called `Totally_Legit_Software`. 

So, I did a generic search for `Totally_Legit_Software` and found events containing `Totally_Legit_Software.exe`. In one of the events where the malicious program is run we can find the beginning of the flag:

```
picoCTF{Ev3nt_vi3w3r_
```
Assembling the flag presents:

```
picoCTF{Ev3nt_vi3w3r_1s_a_pr3tty_us3ful_t00l_81ba3fe9}
```
