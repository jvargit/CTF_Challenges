# Looking for Secrets - web

## Challenge

We're here to spread great information about our candidates yet someone manages to get access to our configuration page every time. How are they doing this?!

It makes no sense, we've checked for everything that can be bad... right? Find out how, tell me and I'll reward you with a secret.

## Solve

Straight away visiting the site we can see that as we navigate across pages a query parameter `page` is used to direct the display that is presented to us:

<img width="805" height="196" alt="lookingforsecrets" src="https://github.com/user-attachments/assets/9790ffcf-3cdb-4241-8482-82864ba6ae55" />


Testing for LFI shows that it is possible:

<img width="568" height="633" alt="lfs" src="https://github.com/user-attachments/assets/19e5cc6a-030a-4584-a358-3d11180c2017" />


The description mentions the config page, so we try `config.php`. The page displays a blank screen, so we try [this trick](https://stackoverflow.com/questions/58208037/curling-with-php-base64-encoder-decoder-instead-of-just-curling-why/58219713) which forces the PHP to `base64` encode the source if it does exist before attempting to execute it:

```
http://3.105.27.130:8080/?page=php://filter/convert.base64-encode/resource=config.php
```
<img width="812" height="252" alt="config" src="https://github.com/user-attachments/assets/88d54cc4-a467-4910-9a49-91e8c6284d51" />

`config.php` does exist!

Decoding the `base64` text we get:

```
<?php
#this is a configuration file
$secret = "secedu{php_f1lt3rs_r_s0m3t1m35_fun_..._sometimes...}"
?>
```

`secedu{php_f1lt3rs_r_s0m3t1m35_fun_..._sometimes...}`





