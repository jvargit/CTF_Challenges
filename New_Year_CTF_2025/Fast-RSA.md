# Fast-RSA - Crypto

## Challenge

Provided code file containing RSA encryption algorithm.

## Solve

Looking at the code file, the unique part about the RSA encryption is the selection of `p` and `q`:

```py
def GenerateModulo():
    p = getPrime(512)
    res = [q for q in range(p, p+100, 2) if isPrime(q)]
    shuffle(res)
    return (p, res[0])

p, q = GenerateModulo()
n = p * q
e = 65537
```

`p` is a randomly generated 512-bit prime number. `q` is some number within the range `[p-p+100]` that is also prime.

After doing a few test runs, I noticed that `p` and `q` were continuously equal. If this is the case for the given plaintext, we can simply take the square root of `n` to find `p` and subsequently `phi(n)`, `d`, `plain` etc. 

So, I wrote a loop to test this over a large amount of trials:

```py
count = 0
for _ in range(1000):
    p,q = GenerateModulo()
    if p!=q:
        count+=1
    else:
        print("same")

print(count)
```

![Screenshot 2025-01-16 134015](https://github.com/user-attachments/assets/f774de68-2379-4a55-958d-add8e2d1da7c)


125 out of 1000 attempts had different values for p and q. Unfortunately, we are in one of these situations - trying to solve by simply square rooting `n` quickly shows it will not work.
Instead , since `p` and `q` are very close together, we can try and brute force factorisation:

```py
sqrt = math.isqrt(n)

for p in range(sqrt - 100, sqrt + 100):
    if n % p == 0:  
        q = n // p
        if isPrime(p) and isPrime(q):  
            print(f"Found p: {p}")
            print(f"Found q: {q}")
            break
```

> `math.isqrt()` 
- calculates largest int $x$ such that $x^2 <= n$

> `for p in range(sqrt - 100, sqrt + 100):`
- the loop starts from 100 below the integer root, and ranges to 100 above the integer root

>`if n % p == 0:`
- checks if `n` is divisible by `p`. If so, finds corresponding `q` by computing `n // p`
    - `//` 'floor division' operator returns the largest whole number less than or equal to actual division result - negates errors with floats and `isPrime()`

>`if isPrime(p) and isPrime(q): ` 
- checks if both `p` and `q` are prime. If so, these are the values of `p` and `q` used to compute `n`

![im2](https://github.com/user-attachments/assets/5a8b7c79-de43-40cf-bb00-e106ea6e67c0)


Now `p` and `q` have been obtained, we can find `phi(n)`:

```py
phi = (p-1)*(q-1)
```

From which we can gain `d`:

```py
d = pow(e, -1, phi)
```

And thus compute the plaintext:

```py
plain_int = pow(c,d,n)
plain_bytes = long_to_bytes(plain_int)
print(plain_bytes.decode())
```

Which returns the flag:

`grodno{Ofru*YgePg8h}`
