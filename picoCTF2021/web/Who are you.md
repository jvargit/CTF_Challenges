# Who are you - web 

## Case: 

Given a link to a webpage.

## Solve:

This challenge was not too complex and mostly involved researching HTTP headers.

Visiting the website presented the page:

1

Sending request to BurpSuite's Repeater, I tried changing `User-Agent` to `PicoBrowser`:

2

Doesn't trust users from another site? Reading about HTTP headers shows that `Referer` allows the server to identify the pages people are visiting from:

3

Only worked in 2018? The `Date` header resolved this issue:

4

Researching again, setting the `DNT` (do not track) header to True allows us to move to the next stage:

5

This next stage took the longest by far. It turns out you can spoof the originating IP address of a client connecting to a website with the `X-Forwarded-For` header. I looked up a Swedish IP address and resent the request with this included:

6

Lastly, the `Accept-Language` header could be used to suggest the user sending the request spoke Swedish:

7

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