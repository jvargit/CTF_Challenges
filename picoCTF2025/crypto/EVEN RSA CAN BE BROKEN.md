
# EVEN RSA CAN BE BROKEN??

## Challenge

This service provides you an encrypted flag. Can you decrypt it with just N & e?
Provided nc connection to service alongside program source code.

## Solve

Viewing the source code, we can see that `getPrime()` isn’t imported via a cryptographically secure library and might be vulnerable to attacks. 
This is apparent by checking the value of `N`:
```
if(N % 2 == 0) :
	print(“Easy flag”)
```
Since `N` is even, and it was generated via the function `getPrime()`, either one of `p` or `q` is 2 or `getPrime()` does not always return a prime. Solving with `p = 2` shows the former is true:

$$
N // 2 = q
$$

Then we can use this to find `phi(n)`:

$$
phi = (p - 1) * (q - 1)
$$

Then `d` and the flag:
```py
d = pow(e, -1, phi)
flag = long_to_bytes(pow(c, d, N)).decode()
```

```
picoCTF{tw0_1$_pr!m3993b4dd0}
```
