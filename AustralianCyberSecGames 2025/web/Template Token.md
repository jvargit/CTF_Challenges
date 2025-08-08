# Template Token - web

## Challenge

The citizens are voting, but it’s not the votes that matter - it’s the system that counts them.

Cast your vote, inspect the results… but is that all there is?

Your mission? Break into the admin panel and uncover the flag.

Provided web domain & zip file containing source code.

## Solve

Viewing the web page we can vote for candidates or view the admin page. Looking at the source code we can see that its a Flask application using a JWT for authentication at `/admin`:

```py
@app.route("/admin")
@token_required
def admin():
    try:
        with open("/flag", "r") as f:
            flag_content = f.read().strip()
    except Exception:
        flag_content = "Unable to read /flag."

    return render_template("admin.html", flag=flag_content)
```

The token is verified using `token_required()`:

```py
SECRETKEY = os.getenv("SECRETKEY")

def token_required(f):
    @wraps(f)
    def wrapper(*args, **kwargs):
        token = None 

        auth_header = request.headers.get("Authorization", None)
        if auth_header and auth_header.startswith("Bearer "):
            token = auth_header.split()[1]

        if not token: 
            return jsonify({"message":"Token is missing"}), 401

        try: 
            payload = jwt.decode(token, SECRETKEY, algorithms=["HS256"])
            request.jwt_payload = payload
        
        except jwt.ExpiredSignatureError:
            return jsonify({"message":"Token has expired"}), 401

        except jwt.InvalidTokenError:
            return jsonify({"message":"Token is invalid"}), 401

        return f(*args, **kwargs)

    return wrapper
```
- The app expects the `Authorization` header to be set and to contain `Bearer ` followed by the JWT
- The contents of the JWT are irrelevant - the application just checks to see that it is valid by attempting to decode with the secret key, and if it is, grants access

Scanning the source code further we can see a template injection vulnerability:

```py
# Confirmation page
    template_source = f"""
    {{% extends "base.html" %}}
    {{% block content %}}
      <div style='text-align:center;padding-top:2rem'>
        <h1>Vote Confirmed</h1>
        You voted for: <br><strong>{candidate}</strong></p>

      </div>
    {{% endblock %}}
    """
    return render_template_string(template_source, **globals())
```

- `candidate` is directly rendered into the page. If we can control this, we can template inject!

When we make a vote and capture the request in BurpSuite, we can see that the candidate parameter is sent with the `POST` request:

<img width="450" height="642" alt="ssti" src="https://github.com/user-attachments/assets/d6415dda-f78c-4fae-b835-52d03332df9a" />


Sending this to the repeater, I experimented with different SSTI payloads to determine whether it was possible. Soon enough I got an error with `{{1+1}}`:

<img width="933" height="481" alt="cand" src="https://github.com/user-attachments/assets/b493f4a2-bcf2-443b-b743-40e064b48299" />


We can inject! Now the attack path is clear:

- Utilise SSTI vulnerability to obtain secret key (the secret in the screenshot **isn't** it)
- Forge valid JWT
- Send request to `/admin` with `Authorization` header set to match expected syntax

While trying to complete this challenge I learnt that this line of code makes the SSTI vulnerability especially severe:

```py
render_template_string(template_source, **globals())
```

From the Flask documentation:

`flask.render_template_string(source, **context)`
- Render a template from the given source string with the given context.

**Parameters:**
- source (str) – The source code of the template to render.
- context (Any) – The variables to make available in the template.

Since our app passes `**globals` to the context, Jinja2 is able to access all global symbols, which includes imported modules, variables, etc. We can subsequently read in the secret key directly with `{{SECRETKEY}}`:

<img width="569" height="380" alt="ssti2" src="https://github.com/user-attachments/assets/6bb4c516-5ae8-4100-b15f-ad120b0a7f36" />

Our secret is `ldidyouknowshinigamisloveapples`. Let's forge a JWT!

![shinigami](https://media4.giphy.com/media/v1.Y2lkPTc5MGI3NjExcm05YTdyaTQyYmFwdjBmOWFmZDZtdjZ2YWhkYTlxZmJ1YXAyeDJmayZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/h0AhBLqVKfha8/giphy.gif)

As we mentioned before, the contents aren't important, just that it is validly signed with the secret key:

<img width="895" height="635" alt="jwt" src="https://github.com/user-attachments/assets/09debc21-28b9-495a-8247-c1b0881dbf33" />

Including the token with our `POST` to `/login` successfully redirects to `/admin`:

<img width="954" height="468" alt="admin" src="https://github.com/user-attachments/assets/a2befc97-0aae-4ff6-a0fd-782c417e20b1" />


Where we find the flag:

`secedu{h0ld_my_s1gn4tur3s_pl34s3}`
