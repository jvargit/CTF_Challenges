# I can be anyone! - web

## Challenge

Among the various issues that occured during the voting process, generating a vote code wasn't possible as the system would say "Citizen <> has already voted". It seems like someone is taking everyone elses votes! Can you find out how an attacker may have done this and submit a vote for the citizen ID CIT100 who hasn't voted yet?

## Solve

Viewing the `README` file we can see that admin info is available to us:

```bash
curl -X POST http://localhost:80/api/admin/validate-secret-key \
    -H "Content-Type: application/json" \
    -d '{
      "secret_key": "7f9c2e4a8b1d6f3e9c5a2b8f1d4e7a9c"
    }'
```

The secret key is still valid allowing us to access admin API endpoints.

Then in `admin_api.py` we can generate vote tokens with the `/generate-qr` endpoint:

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
```

Then in `voting_api.py` we can see that the generated token is used in the `Authorization` header to submit a vote at the `/submit-vote` endpoint:

```py
auth_header = request.headers.get("Authorization")
        if not auth_header:
            return jsonify({"error": "Missing Authorization header"}), 401
            
        payload = decode_jwt_token(auth_header)
```

So to solve this challenge we:

- Generate a voting token with `citizen_id` set to `CIT100`
- Submit the token to `/submit-vote` for the flag:

`cysea{impersonation_is_a_crime_b9a43d54e0}`