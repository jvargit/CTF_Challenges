# Ping Me Maybe - forensics

## Challenge

Whilst the elections are in full swing, our computers on the network keep late night texting amongst themselves. Most of the traffic looks routine - but there are subtle oddities in how some of the “everyday” protocols are being used.

We had our AI agent take a look at the capture files and it said there were signs of a file exfiltration through a funny internet protocol.

Provide `challenge.pcap`

## Solve

Since `ping` is mentioned in the challenge title we filter for `icmp`:

<img width="1419" height="523" alt="ping" src="https://github.com/user-attachments/assets/521e4562-15ee-4ee1-984c-1a93bd0dab93" />


Navigating through the ping packets we can find the full flag:

`secedu{p1ng_m3_m4yb3}`
