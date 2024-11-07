# rsa_oracle - cryptography

## Case

Can you abuse the oracle?
An attacker was able to intercept communications between a bank and a fintech company. They managed to get the message (ciphertext) and the password that was used to encrypt the message.
Additional details will be available after launching your challenge instance.

File(s): https://artifacts.picoctf.net/c_titan/33/secret.enc
https://artifacts.picoctf.net/c_titan/33/password.enc

## Solution

To solve this, I utilised the homomorphic property of RSA:

$RSA(x) \cdot RSA(y) = RSA(xy)$

We are given the password, say $c_m$:

```
1634668422544022562287275254811184478161245548888973650857381112077711852144181630709254123963471597994127621183174673720047559236204808750789430675058597
```

We can bypass the oracle's restriction on decrypting this by:
- Multiplying $c_m$ with any ciphertext $c_{m'}$ from another chosen message
- Decrypting the result of the multiplication using the oracle
- Dividing the given message by $m'$

Firstly, I choose $m' = a$ and use the oracle to find $RSA(a)$: 

```
1894792376935242028465556366618011019548511575881945413668351305441716829547731248120542989065588556431978903597240454296152579184569578379625520200356186
```

Then, I use PARI/GP to compute the product of $RSA(a)$ and $RSA(m)$:

```
3097357265853171086762577146485788014213956517380252195632957286301939282740082358975030469184398860527445348937879783156475536146366758154420391810256611847120173988337373791004871904106220076227501246689936260227532178819705625123684123321350185635500398774070100065478919921540000252731735761585821431042
```
Then, I use the oracle to decrypt $RSA(am)$ to retrieve the hex value:

```
0x13c9ad293b45
```

Then, I divide that value by 0x61 - the hexadecimal representation of $m'$:

```
224298087781
```

Converting this to hex, and then to ASCII characters reveals the password '4955e':

![alt text](image.png)

![alt text](image-1.png)

I confirmed this was the correct password by using the oracle to encrypt the number, and sure enough, the given password ciphertext matched.

The ciphertext given in __secret.enc__ is in OpenSSL salted format:

```
Salted__��(��-�v\rAj�6<C����^&���򃤱q��\�/K����:��L��3'�~ 
```
> This can be identified since it starts with the word 'Salted'
> This form of encryption combines the salt and passphrase to derive the key and IV

Finally, the following command can be run to decrypt the flag:

```
openssl enc -aes-256-cbc -d -in secret.enc -out plaintext.txt -pass pass:4955e
```

**Flag: picoCTF{su((3ss_(r@ck1ng_r3@_92d53250}**









