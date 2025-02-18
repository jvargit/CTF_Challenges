# Grandma's Secret Recipe - web

## Challenge

Grandma has been baking her world-famous cookies for decades, but she’s always kept her secret recipe locked away. Nobody—not even her most trusted kitchen helpers—knows the full list of ingredients.

She insists it’s all about "the perfect balance of love and a pinch of mystery", but deep down, you know there’s more to it. Rumors say only Grandma herself is allowed to see the recipe, hidden somewhere in her kitchen.

But, you were hired by Grandpa, who divorced her because she refused to share the recipe. Can you figure out the true secret behind her legendary cookies? 🍪👵

## Solve

Viewing the requests made to the web page in BurpSuite, we can see that when trying to access `/grandma` our `role` is set to `kitchen helper` in the cookie:

![gma1](https://github.com/user-attachments/assets/97da4cbf-25a8-4122-8158-eabc1f530cae)


We can also see there is a checksum being used to verify the value of `role`. 

Since it looks like an MD5 I check for the best case scenario and see if it is simply `kitchen helper` hashed:

```bash
echo -n 'kitchen helper' | md5sum
```

![gma2](https://github.com/user-attachments/assets/6b4d80b7-7769-4f6e-a339-5ef916561a73)


It matches, so now we can simply generate the MD5 hash for `grandma`, modify our `role` and `checksum` fields and re-request the `/grandma` page:

```bash
echo -n 'grandma' | md5sum
```

Resending the request with `checksum` set to the generated hash we can access the `/grandma` page and obtain the flag:

![gma3](https://github.com/user-attachments/assets/fab0722f-2df3-4c74-88d8-62dc2c2ad808)


`bronco{grandma-makes-b3tter-cookies-than-girl-scouts-and-i-w1ll-fight-you-over-th@t-fact}`
