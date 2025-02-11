# Irish-Name-Repo 1 - web

## Case

Provided website link, asked to try and log in.

## Solve

Navigating to the log in page, I tried a simple SQL injection:

```
username: test
password: ' OR '1'='1'
```

While this was unsuccessful, viewing the request in BurpSuite showed a `debug` parameter being sent to the server:

![1](https://github.com/user-attachments/assets/2c467823-16dd-489d-8e03-3997c0ca9ed4)


I tried setting this to `1` to see if it had any impact on the servers reponse. It did:

![2](https://github.com/user-attachments/assets/2dcfffb4-e22a-4360-8dfa-f682bf6f2b7f)


Now we can see how the database is being queried to authenticate a users login details:

```
username: test
password: ' OR '1'='1'
SQL query: SELECT * FROM users WHERE name='test' AND password='' OR '1'='1''
```

Since the username is first selected, and then the password is checked, it may be possible to inject some SQL to bypass the password stage. With a bit of experimentation, and guessing a username of `admin`, I was able to log in with the following details:

```
username: admin'--
password: any
```

![3](https://github.com/user-attachments/assets/8d222eb5-992a-47f3-b0d1-411df55c4572)


The ' effectively closes the string `admin` and the -- comments out the rest of the query. The database matches a user with the username `admin` and does not check for a corresponding password. This gives the flag:

`picoCTF{s0m3_SQL_f8adf3fb}`
