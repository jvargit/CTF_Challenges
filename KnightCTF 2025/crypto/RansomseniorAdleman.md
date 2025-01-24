# RandomseniorAdelman - crypto 

## Challenge

```
c = 4744275480125761149475439652189568532719997985297643024045625900665305682630452044004013647350658428069718952801828651333880186841289630294789054322237585
p = (A random 256 bit prime number)
q = 81950208731605030173072901497240676460946134422613059941413476068465656250011

e=15537

Hint: Alice left three peculiar phrases—"usedistofindouttheseed," "thisisthekeytogetyourseed," and "anothersecrethere." She whispered about performing some “Axe-Ore” swirl among them then I thought my Friend obscure has cryptic incantation—resulting in a curious final integer. Is it a seed? Who knows...

good luck.....
```

Despite all the hints, it turns out this challenge is simply solved by ignoring `p` and treating `q` as the modulus:

```py
from Cryptodome.Util.number import bytes_to_long, long_to_bytes

q = 81950208731605030173072901497240676460946134422613059941413476068465656250011
e = 15537
c = 4744275480125761149475439652189568532719997985297643024045625900665305682630452044004013647350658428069718952801828651333880186841289630294789054322237585

""" -- later found to be useless -- 
p1 = b"usedistofindouttheseed" 
p2 = b"thisisthekeytogetyourseed"
p3 = b"anothersecrethere"

p1_int = bytes_to_long(p1)
p2_int = bytes_to_long(p2)
p3_int = bytes_to_long(p3)
"""

phi = q - 1
d = pow(e, -1, phi)
plain = pow(c, d, q)

print(long_to_bytes(plain).decode())
```
> when the modulus is prime, `phi` is simply `x - 1`

`KCTF{Don't_CA#ll_Me_Herp}`
