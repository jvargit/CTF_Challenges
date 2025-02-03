# alphascii clashing - crypto

## Context:

Provided a `server.py` file containing source code for spawned docker.

## Solve:

Looking at the contents of `server.py`, we are able to see that the server expects JSON data as input:

```python
def get_option():
    return input('''
    Welcome to my login application scaredy cat ! I am using MD5 to save the passwords in the database.
                          I am more than certain that this is secure.                       
                                   You can't prove me wrong!          
    
    [1] Login
    [2] Register
    [3] Exit

    Option (json format) :: ''')
```

We can also see how data is stored in the database. MD5 hashes are generated to store usernames and passwords are stored in plain text:

```python
Data format:
{
    username: [md5(username).hexdigest(), password],
    .
    .
    .
}
```

Looking in `main()` we can then see that if a user logs in, and the generated hash matches a different username, the flag will be printed. Basically, we need a collision to occur:

```python
usr, pwd = creds['username'], creds['password']
            usr_hash = md5(usr.encode()).hexdigest()
            for db_user, v in users.items():
                if [usr_hash, pwd] == v:
                    if usr == db_user:
                        print(f'[+] welcome, {usr} 🤖!')
                    else:
                        print(f"[+] what?! this was unexpected. shutting down the system :: {open('flag.txt').read()} 👽")
                        exit()
                    break
            else:
                print('[-] invalid username and/or password!')
```
> .items() splits the dictionary into key value pairs. `db_user` is the key (plaintext stored username), and `v` is the value [usr hash, password]

Since we have the ability to register users, I did a bit of research for strings with known MD5 collisions, and found the following pair:

`TEXTCOLLBYfGiJUETHQ4hAcKSMd5zYpgqf1YRDhkmxHkhPWptrkoyz28wnI9V0aHeAuaKnak`

`TEXTCOLLBYfGiJUETHQ4hEcKSMd5zYpgqf1YRDhkmxHkhPWptrkoyz28wnI9V0aHeAuaKnak`

Firstly, I confirmed that the strings do result in identical MD5 digests:

```
print(md5(b"TEXTCOLLBYfGiJUETHQ4hAcKSMd5zYpgqf1YRDhkmxHkhPWptrkoyz28wnI9V0aHeAuaKnak").hexdigest() == md5(b"TEXTCOLLBYfGiJUETHQ4hEcKSMd5zYpgqf1YRDhkmxHkhPWptrkoyz28wnI9V0aHeAuaKnak").hexdigest())
```

Great! Now, we can connect to the server to proceed with registering the users:

`{"option": "register"}`

`{"username": "TEXTCOLLBYfGiJUETHQ4hAcKSMd5zYpgqf1YRDhkmxHkhPWptrkoyz28wnI9V0aHeAuaKnak", "password": "anything"}`

And then logged in with the second username:

`{"option": "login"}`

`{"username": "TEXTCOLLBYfGiJUETHQ4hEcKSMd5zYpgqf1YRDhkmxHkhPWptrkoyz28wnI9V0aHeAuaKnak", "password": "anything"}`

Doing so reveals the flag ;))))))):

```
HTB{finding_alphanumeric_md5_collisions_for_fun_https://x.com/realhashbreaker/status/1770161965006008570_6007a7825ee990b8050f35316a6c5022}
```

