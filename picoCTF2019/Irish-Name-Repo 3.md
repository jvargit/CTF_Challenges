# Irish-Name-Repo 3 - web

## Case

Provided website link, asked to try and log in.

## Solve

Viewing the webpage, we are provided a single text box to log our details into:

ir1

Doing a test run and capturing in BurpSuite, we can see that the `debug` option is still available as in the first and second stage of this challenge. 

Setting `debug` to 1, we can see how the database is queried to authenticate users:

ir2

So, I tried logging in with password 'gfrg':

ir3

It seems like our input is being warped in some way. I decided to find out all the mappings for the alphabet:

ir4

And then confirmed it was a consistent subsitution cipher:

ir5

It seems so. Then, I wrote a program so that I could generate text in the substituted alphabet that the server would interpret as plaintext:

```py
let = "abcdefghijklmnopqrstuvwxyz '1=-"
alp = "nopqrstuvwxyzabcdefghijklm '1=-"

let = let.upper()
alp = alp.upper()

text = input("Welcome! Please enter your text to be substituted:")
substituted = []

print("Your text is:", text)

for i in text:
    for j in range(len(let)):
        if i == let[j]:
            substituted.append(alp[j])

print("Substituted:",''.join(substituted))
```

By playing around with a few different payload combinations, I found:

ir6

- The initial apostrophe closes the opening one which wraps the users input 
- The OR 1=1 creates an always true condition 
- The -- comments out the closing apostrophe which wraps user input

Submitting to browser:

ir7

Gives flag:

`picoCTF{3v3n_m0r3_SQL_06a9db19}`



