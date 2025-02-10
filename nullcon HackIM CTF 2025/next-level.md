# next-level - crypto

## Challenge

With this challenge we are taking multiprime-RSA to the next level.

```py
from Crypto.Util import number

def nextprime(n):
    p = n
    while True:
        if number.isPrime(p := p+1): return p

p = number.getPrime(512)
q = nextprime(p)
r = nextprime(q)
n = p*q*r
e = 0x10001
flag = int.from_bytes(open('flag.txt','r').read().encode())
c = pow(flag,e,n)
print(n)
print(c)
```

```
n = 1707887790173107239993340150066249370611521480723859747371251143868125510516833525194011455954104451413157693902435149079520663760296892523995908641364563480691997021027834147324006217750569086797013085351230314474845335103342030679293031838045130892214824458330434647142271511788492544677799366925445866641725140164953175257048031805244169963683528749402824630615292446517164360864610162795615708352545850573927966056356269371441764892958677636364401248671392241

c = 1568991097303585492151879879378118636899250575774558330520050867078057985733425914538497219780720647986008733536389153432365728543796011225219634188464818360628269730140752438376450351842551660660477223348136162796130008685828421398621262400575467121129251231436853853588685674302154581001127811450563438631678018611247993338467601312374927819032669967588189760041932682345596666494984563749269138424985566260579978402264513192793300751408779989096341756119301877
```


## Solve

Viewing the encryption algorithm, we can see that three primes have been used in this RSA scheme. The most notably flawed part is that the primes are sequential in order and are therefore close together:

```py
def nextprime(n):
    p = n
    while True:
        if number.isPrime(p := p+1): return p

p = number.getPrime(512)
q = nextprime(p)
r = nextprime(q)
```
- `p` is a randomly chosen 512-bit prime
- `q` is the next found prime after that
- `r` is the next found prime after that

These values are used to compute `n`:

`n = p*q*r`

Since the three primes are close together, `p`, `q` and `r` should be approximately near the cube root of `n` - with `p` being below the cube root, `r` being above it and `q` somewhere in between.

I wrote the following loop to brute force the values of `p`, `q` and `r` used to generate `n`:

```py
cube, found = iroot(n, 3)
for p in range(cube - 1000, cube):
    if n % p == 0 and isPrime(p):  
        q = nextprime(p)
        r = nextprime(q)
        if q*r*p ==n:  
            print(f"Found p: {p}")
            print(f"Found q: {q}")
            print(f"Found r: {r}")
            break
```
- `iroot` returns a tuple containing the integer cube root of `n` and a boolean which is `True` if the root is exact. In this case, `found` is just a placeholder for the boolean and is not used
- `for p in range(cube - 1000, cube)`
    - the loop checks for values starting from `cube-1000` up to `cube`. Although `q` and `r` are greater than `q`, the loop only needs to identify potential `p` values from which `q` and `r` are then calculated.
- `if n % p == 0 and isPrime(p)`
    - checks if the candidate `p` value is a factor of `n` and if it is prime
    - If so, `q` and `r` are calculated and verified with `n` to determine if the correct combination of primes has been found

Running the code with the provided parameters produces the flag:

`ENO{1_l0ve_7h3_pr1me_numb3r_the0r3m}`