# No Sql Injection - web exploitation

## Case

Can you try to get access to this website to get the flag?

## Solve:

Viewing the website, we have a login page. Viewing the source code, we can see that it is a Mongo DB web server. WE can see that if a user successfully logs in, a bunch of parameters should be returned in the server response, one of which being a token which should contain the flag:

```python
 token: { type: String, required: false, default: "{{Flag}}" },
 ...
 ...
 ...
if (user) {
          res.json({
            success: true,
            email: user.email,
            token: user.token,
            firstName: user.firstName,
            lastName: user.lastName,
          });
```

The source code also shows how the server handles login data:

```python
app.post("/login", async (req, res) => {
      const { email, password } = req.body;

      try {
        const user = await User.findOne({
          email:
            email.startsWith("{") && email.endsWith("}")
              ? JSON.parse(email)
              : email,
          password:
            password.startsWith("{") && password.endsWith("}")
              ? JSON.parse(password)
              : password,
        });
```

If the email or password is wrapped in `{}`, the server will parse them as JSON. So, we can use the payload:

```JSON
{ "$gt": "0"}
```
>$gt operator stands for greater than. If any password exists with a value greater than "0", the query will succeed and we can log in

Entering this payload in both the username and password field successfully logs me in as a user. 

Checking the server response to the successful log in, a base64 value can be found in the token:

![nosql](https://github.com/user-attachments/assets/c27dcfb7-6762-4b2d-b4f7-e5a0e37c2a2d)


Which can be decoded to reveal the flag:

`picoCTF{jBhD2y7XoNzPv_1YxS9Ew5qL0uI6pasql_injection_67b1a3c8}`

