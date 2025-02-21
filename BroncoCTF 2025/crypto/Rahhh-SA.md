# Rahhh-SA - crypto

## Challenge

Behold! A modern take on an old crypto classic!

I call it RAHHH-SA! That's because with just a simple numerical inversion of RSA's rules, it's now unbreakable! RAHHH!

```
e = 65537
n = 3429719
c = [-53102, -3390264, -2864697, -3111409, -2002688, -2864697, -1695722, -1957072, -1821648, -1268305, -3362005, -712024, -1957072, -1821648, -1268305, -732380, -2002688, -967579, -271768, -3390264, -712024, -1821648, -3069724, -732380, -892709, -271768, -732380, -2062187, -271768, -292609, -1599740, -732380, -1268305, -712024, -271768, -1957072, -1821648, -3418677, -732380, -2002688, -1821648, -3069724, -271768, -3390264, -1847282, -2267004, -3362005, -1764589, -293906, -1607693]

p = -811
```

## Solve

The main key in solving this RSA is knowing that the calculation of `phi(n)` becomes inverted as we are provided with a negative `p`:

Standard RSA (positive `p` and `q`):

$$
phi(n) = (p - 1) * (q - 1)
$$

Modified RSA (negative `p` and `q`):

$$
phi(n) = (-p - 1) * (-q - 1)
$$
$$
phi(n) = -(p + 1) * -(q + 1)
$$
$$
phi(n) = (p + 1) * (q + 1)
$$

Since `n` is positive, `q` must also be negative.

Using this information, we can solve for RSA as per usual:

***First calculate `q`***

```py
q = n // p
```

***Then `phi(n)`***

```py
phi = (p+1) * (q+1)
```

***Then `d`***

```py
d = pow(e, -1, phi)
```

Then we can iterate over the list `c` and apply `d` to decrypt each individual character:

```py
plain = []
for i in c:
    plain.append(long_to_bytes(pow(i, d, n)).decode())
```

And print the assembled flag:

```py
print(''.join(plain))
```

`bronco{m4th3m4t1c5_r34l1y_1s_qu1t3_m4g1c4l_raAhH!}`