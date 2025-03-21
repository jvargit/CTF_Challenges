# Apriti sesamo - web

## Challenge

I found a web app that claims to be impossible to hack!

## Solve

Visiting the website we have a welcome page with a button that leads us to `/impossibleLogin.php`.

The hints for this challenge are:
- Backup files
- Rumor has it, the lead developer is a militant emacs user

Doing a search for emacs backup files led me to [this post](https://www.reddit.com/r/emacs/comments/azyrsr/what_are_the_files_emacs_creates_when_i_make/?rdt=56504) showing that emacs back up files are appended with  a tilde `~`.

Trying to visit `/impossibleLogin.php~` appears to just display the same login page. However, the page source is different:

```bash
curl -i http://verbal-sleep.picoctf.net:50313/impossibleLogin.php~
```

apriti.png

Looks like some encoded php. I used Claude to decode the message to:

```php
<?php
if (isset($_POST['username']) && isset($_POST['pwd'])) {
    $username = $_POST['username'];
    $password = $_POST['pwd'];
    
    if ($username == $password) {
        // If username equals password
        echo "<br/>Failed! No flag for you";
    } else {
        // If username doesn't equal password, but their SHA1 hashes are identical
        if (sha1($username) === sha1($password)) {
            // Read and output the flag file
            echo file_get_contents("../flag.txt");
        } else {
            // If neither condition is met
            echo "<br/>Failed! No flag for you";
        }
    }
}
?>
```

The code checks that usernames are equal (and fails if they do), then checks if their SHA-1 hashes match.

After doing a bit of research on SHA-1 collisions, it became apparent that there were no known text collisions (only files, pdf etc).

Since we have control over the input that is passed to `sha1()` we could try and engineer an error so that we get `null` == `null`, pass the check and get the flag.

It turns out this is possible by passing `username` and `password` as arrays. This will provide the flag so long as username and password contain different values (to pass the first conditional check).

Sending the POST request to `impossibleLogin.php` to the Repeater in BurpSuite and sending `username` and `password` as arrays demonstrates this:

apriti2.png

```
picoCTF{w3Ll_d3sErV3d_Ch4mp_5b269555}
```


