# Miku's Autograph - web

## Challenge

I am so proud of the fact that I have Miku's autograph. Ha! You don't!

## Solve

Capturing the traffic made to the webpage, we can see that when we attempt to log in, our request is denied with the message

```
You are not miku_admin!!!
```

We can also see our request is sent with a `magic_token`:

![miku1](https://github.com/user-attachments/assets/66abe73c-64c9-418d-86ac-149f900c9950)


This is a JSON web token (JWT) which has the following structure:

```
header.payload.signature
```

The header and payload are just base64 encoded which we can see contain:

***Header***

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

***Payload***

```json
{
  "sub": "miku_user",
  "exp": 1739771575
}
```

The signature component of the token takes the base64 encoded header and payload, a secret and signs with the algorithm specified in the `alg` header.

It turns out we can bypass this signature by setting the `alg` header to `none` and can modify the payload as we like.

The newly crafted token (unencoded) looks like this:

***Header***

```json
{
  "alg": "none",
  "typ": "JWT"
}
```

***Payload***

```json
{
  "sub": "miku_admin",
  "exp": 1739771575
}
```

We can then base64 encode each section, reassemble the token and send our request to the server. Since the signature won't be verified, we can leave it as is. The updated token looks like this:

```
eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0=.eyJzdWIiOiJtaWt1X2FkbWluIiwiZXhwIjoxNzM5NzcxNTc1fQ==.yLyTGi_VSOnqfYLO3o6KHtoCMUKjESlqxj7WAZYiEq8
```

Sending the request we can successfully obtain the flag:

![miku2](https://github.com/user-attachments/assets/2ddd3cff-c6e5-486a-9c0f-0783b590df18)


`bronco{miku_miku_beaaaaaaaaaaaaaaaaaam!}`
