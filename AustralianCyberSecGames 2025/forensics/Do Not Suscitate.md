# Do Not Suscitate - forensics

## Challenge

What is the difference between a DNS server and a politician? One of them will actually answer your queries.

L did you know? HTTP data exfiltration is too noisy?

Provided `challenge.pcap`

## Solve

The mention of queries, description of HTTP exfiltration being too 'noisy' and the acronym of the challenge title (DNS) strongly suggested the idea of DNS text record exfiltration.
- Used by attackers when inside a victim machine - DNS outbound request queries are likely permitted
- Encode information in subdomain, then DNS request attackers server
- Log request, decode information

In Wireshark, DNS TXT queries can be specified with `dns.qry.type == 16`:

dns.png

`base64` decoding the subdomain gives the flag:

`secedu{dns_3xf1l_1nv1s1bl3_1n_http_l0gs}`