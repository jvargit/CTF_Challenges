# Master of Time and Space - web

While the government has provided the source code to the world, the mobile app won't run properly because of geolocation and time restrictions! Can you identify the time and coordinates required for spoofing? Don't forget to submit it to the relevant APIs to double check it's correct (and get the flag).

img

## Challenge

This challenge is fairly straight forward as the time and location is provided for us in the image. 

Looking up Croydon Public School we find the location in latitude and longitude as `(-33.87975533355316, 151.11492977422782)`.

In the source we can see how location and time is checked:

**Location**
```py
    def check_geolocation_in_polling_area(self, latitude, longitude):
        conn = self.get_connection()
        try:
            cursor = conn.cursor()
            cursor.execute("""
                SELECT * FROM polling_locations 
                WHERE ? BETWEEN lat_sw AND lat_nw 
                AND ? BETWEEN lng_nw AND lng_ne
            """, (latitude, longitude))
            row = cursor.fetchone()
            
            if row:
                location_data = {
                    "id": row["id"],
                    "key": row["key"],
                    "state": row["state"],
                    "polling_location": row["polling_location"],
                    "voting_status": row["voting_status"],
                    "geolocation_box": {
                        "northwest": {"lat": row["lat_nw"], "lng": row["lng_nw"]},
                        "northeast": {"lat": row["lat_ne"], "lng": row["lng_ne"]},
                        "southwest": {"lat": row["lat_sw"], "lng": row["lng_sw"]},
                        "southeast": {"lat": row["lat_se"], "lng": row["lng_se"]}
                    }
                }
                return True, location_data
```

**Time**
```py
    def check_timestamp_in_polling_hours(self, timestamp):
        try:
            dt = datetime.fromisoformat(timestamp.replace("Z", "+00:00"))
            hour = dt.hour
            polling_start = 8
            polling_end = 18
            
            if polling_start <= hour < polling_end:
                return True, f"Polling location is open"
            else:
                return False, f"Polling location is closed"
```

We can see that we submit the information to `/check-location` for the flag:

```py
@voting_bp.route("/check-location", methods=["POST"])
def check_location_in_polling_area():
    """
    Check if given coordinates are within any polling location"s geolocation box.
    """
    try:
        data = request.get_json()
        if not data:
            return jsonify({"error": "No JSON data provided"}), 400
            
        latitude = data.get("latitude")
        longitude = data.get("longitude")
        ...
        ...
        db = Database()
        found, location_data = db.check_geolocation_in_polling_area(lat, lng)
        
        if found:
            return jsonify({
                "within_polling_area": True,
                "polling_location": location_data,
                "master_of_time_and_space_flag": current_app.config["MASTER_OF_TIME_AND_SPACE_FLAG"]
```

So we just submit our lat and long coordinates to the endpoint for the flag:

```
POST /api/voting/check-location HTTP/1.1
Host: mobile-app.commission.freedonia.vote
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:140.0) Gecko/20100101 Firefox/140.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Upgrade-Insecure-Requests: 1
Priority: u=0, i
Content-Type: application/json
Content-Length: 68

{"latitude":-33.87975533355316,  "longitude":151.11492977422782}
```

`cysea{what_time_is_it_fr_tho_faca748a2d}`
            