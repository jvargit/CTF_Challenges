# Cookies - Web Exploitation

## Case: 

Who doesn't love cookies? Try to figure out the best one. http://mercury.picoctf.net:27177/

## Solve:

Pretty simple solve, but my first time using BurpSuite Intruder which was cool.

Firstly I entered the placeholder 'snickerdoodle' and received this output:

Looking at the requests in BurpSuite, we can see that for the Cookie value 'name' is assigned -1:

I sent the request to the Repeater and tried changing the value of 'name' to 1:

Assuming there would be many different cookies, I then sent the request to the Intruder and sent requests with values ranging from 0 to 50:


After the attack was complete, one response had significnatly different length to the other responses with successful matches. Checking this reponse showed the flag:

**picoCTF{3v3ry1_l0v3s_c00k135_064663be}**