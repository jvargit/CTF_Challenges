# Securesend - osint

## Challenge

Some phishing emails have been going out from the People's Solidarity front. Can you figure out why their domain isn't being protected?

## Solve

We are provided the domain `https://solidarity.freedonia.vote/`.

To investigate potential email security misconfigurations, we begin by checking the domain's DNS TXT records.

The first part of the flag is found in the Sender Policy Framework (SPF) record:

`nslookup -type=TXT solidarity.freedonia.vote`

<img width="587" height="172" alt="ss1" src="https://github.com/user-attachments/assets/6d3a1bd4-47a3-4674-bb69-93b3d90a858c" />


> SPF specifies which mail servers are authorised to send email on behalf of the domain, so it's a logical record to check if there are unauthorised phishing emails being sent using the domain name

The second part is found in the Domain-based Message Authentication, Reporting and Conformance (DMARC) policy:

`nslookup -type=TXT _dmarc.solidarity.freedonia.vote`

<img width="1047" height="199" alt="ss2" src="https://github.com/user-attachments/assets/be998959-ed8d-40f8-88ee-b76b1433c290" />


> DMARC records define how email servers should handle messages that fail SPF and DomainKeys Identified Mail (DKIM) checks. DKIM signs email headers to provide sender authentication. If DMARC records are too permissive, phishing emails can be sent unchecked

`cysea{spf_and_dmarc_l7813eDu}`
