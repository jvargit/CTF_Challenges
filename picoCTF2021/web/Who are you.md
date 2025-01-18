# Who are you - web 

## Case: 

Given a link to a webpage.

## Solve:

This challenge was not too complex and mostly involved researching HTTP headers.

Visiting the website presented the page:

![1](https://github.com/user-attachments/assets/72a21945-e2b8-4fc1-ae7a-cc8a3bb8fcc8)


Sending request to BurpSuite's Repeater, I tried changing `User-Agent` to `PicoBrowser`:

![2](https://github.com/user-attachments/assets/412dad32-0e5a-4f49-9d58-99e98ac1a362)


Doesn't trust users from another site? Reading about HTTP headers shows that `Referer` allows the server to identify the pages people are visiting from:

![3](https://github.com/user-attachments/assets/30a4b0ed-8e24-457d-85a2-8b2a1bb8a146)

Only worked in 2018? The `Date` header resolved this issue:

![4](https://github.com/user-attachments/assets/edcc089b-bb26-4fd6-af8e-3f9400ab56b8)

Researching again, setting the `DNT` (do not track) header to True allows us to move to the next stage:

![5](https://github.com/user-attachments/assets/095b6237-2231-42b6-add0-ba5fec2553f7)


This next stage took the longest by far. It turns out you can spoof the originating IP address of a client connecting to a website with the `X-Forwarded-For` header. I looked up a Swedish IP address and resent the request with this included:

![6](https://github.com/user-attachments/assets/69c8037c-c694-4dae-96d6-899907e5be6f)


Lastly, the `Accept-Language` header could be used to suggest the user sending the request spoke Swedish:

![7](https://github.com/user-attachments/assets/9ecfa465-edfb-4145-8d8a-28e6a1d77724)


Which resulted in the following HTTP request:

```
GET / HTTP/1.1

Host: mercury.picoctf.net:52362

User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 PicoBrowser

Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/png,image/svg+xml,*/*;q=0.8

Date: Thu, 31 May 2018 20:35:00 GMT

DNT: 1

Referer: mercury.picoctf.net:52362

X-Forwarded-For: 103.103.84.0

Accept-Language: sv-SE

Accept-Encoding: gzip, deflate, br

Connection: close

Upgrade-Insecure-Requests: 1

Priority: u=0, i
```

Yielding the flag:

`picoCTF{http_h34d3rs_v3ry_c0Ol_much_w0w_0c0db339}`
