# Cookie Monster Secret Recipe - web

## Challenge
Cookie Monster has hidden his top-secret cookie recipe somewhere on his website. As an aspiring cookie detective, your mission is to uncover this delectable secret. Can you outsmart Cookie Monster and find the hidden recipe?

## Solve
Accessing the website, we can capture the cookie by submitting a login request with any credentials and viewing the `Set-Cookie` header in the servers response:

```
secret_recipe=‘cGljb0NURntjMDBrMWVfbTBuc3Rlcl9sMHZlc19jMDBraWVzXzc3MUQ1RUIwfQ%3D%3D’
```

***Quickly obtain the flag by:***

- URL decode (encoding evident in the `%3D` encoding of `=`)

```
cGljb0NURntjMDBrMWVfbTBuc3Rlcl9sMHZlc19jMDBraWVzXzc3MUQ1RUIwfQ==
```

- base64 decode (encoding evident in closing `==`)

```
picoCTF{c00k1e_m0nster_l0ves_c00kies_771D5EB0}
```
