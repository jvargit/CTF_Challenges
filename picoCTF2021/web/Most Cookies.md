# Most Cookies - web exploitation

## Case

Alright, enough of using my own encryption. Flask session cookies should be plenty secure!

Provided `server.py` file containing flask server config info.

## Solve

Viewing the `server.py` file the noteworthy items are:

```py
cookie_names = ["snickerdoodle", "chocolate chip", "oatmeal raisin", "gingersnap", "shortbread", "peanut butter", "whoopie pie", "sugar", "molasses", "kiss", "biscotti", "butter", "spritz", "snowball", "drop", "thumbprint", "pinwheel", "wafer", "macaroon", "fortune", "crinkle", "icebox", "gingerbread", "tassie", "lebkuchen", "macaron", "black and white", "white chocolate macadamia"]
app.secret_key = random.choice(cookie_names)
```

The Flask secret key used for signing cookies is one of these cookie names. 

```py
def flag():
	if session.get("very_auth"):
		check = session["very_auth"]
		if check == "admin":
			resp = make_response(render_template("flag.html", value=flag_value, title=title))
			return resp
```

If the `very_auth` cookie is set to `admin`, then we can view the flag.

Firstly, I captured a default cookie in BurpSuite when accessing the website and decoded it using [Flask-Unsign](https://github.com/Paradoxis/Flask-Unsign):

![mostcookies1](https://github.com/user-attachments/assets/ba53fac0-a8bf-419f-b502-75acf4022cb7)


We can see that this is the `very_auth` identifier and just need to change the value from `blank` to `admin`. If we know the Flask secret key, we can use it to sign cookies and forge our own. So, I created a wordlist from the `cookie_names` list and brute forced with `flask-unsign`:

![mostcookies2](https://github.com/user-attachments/assets/e78229ff-b19f-460a-97b9-9bcf71609e5d)


Now that we know the secret key is `fortune` we can use it to create our own cookie:

![mostcookies3](https://github.com/user-attachments/assets/326f73fb-6f66-4d24-b057-122cac3ab057)


Embedding this cookie in the GET request for the webpage produces the flag:

`picoCTF{pwn_4ll_th3_cook1E5_743c20eb}`

