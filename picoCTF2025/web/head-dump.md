# head-dump - web

## Challenge

Welcome to the challenge! In this challenge, you will explore a web application and find an endpoint that exposes a file containing a hidden flag.

The application is a simple blog website where you can read articles about various topics, including an article about API Documentation. Your goal is to explore the application and find the endpoint that generates files holding the server’s memory, where a secret flag is hidden.

## Solve

Accessing the website we can see a page ‘Explore backend development with us’ that contains the endpoints available.

 Of them, we can see the `/heapdump` endpoint. 
 
Visiting the page, a file is downloaded (the heap dump which contains a snapshot of all the objects that are in memory in JVM at a certain moment).
Opening the file, we can simply search for our flag with `Ctrl+f 'picoCTF'` and obtain the flag:

```
picoCTF{Pat!3nt_15_Th3_K3y_388d10f7}
```
