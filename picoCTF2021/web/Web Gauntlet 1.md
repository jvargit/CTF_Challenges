# Web Gauntlet 1 - web 

## Challenge

Provided website, asked to login as `admin`. Also provided `/filter.php` endpoint which displays key words / symbols which are filtered out of requests.

## Solve:

Viewing the webpage, we are prompted to log in phase 1/5. Doing a test we see how the database is queried:

img 

The respective filter is "or".

This can be bypassed with the password:

```
admin'--
```

For round 2, the filters are updated to "or and like = --". This prevents the current injection from working by filtering out the line comment. This can be bypassed with a block comment:

```
admin'/*
```

In round 3, the filters are updated to "or and = like > < --". None of this impacts on the solution to last round, so we can reuse the injection payload.

In round 4, the filters then become "or and = like > < -- admin". This took the longest time to bypass. I experimented with using `CHAR()` and `CHR()` functions to pass ASCII values to generate characters, and then concatenate with `+`. I also tried submitting the hexadecimal values for `admin` themselves as a string with no success. I also tried concatenating the words `ad+min` with no luck. 

After checking the hint and seeing SQLite, I realised that concatenation was not done with `+` but with `||`. The subsequent payload was:

```
adm'||'in'/*
```

The filters for round 5 then updated to "or and = like > < -- union admin". This doesn't affect the injected payload so we can resubmit to obtain the flag:

`picoCTF{y0u_m4d3_1t_a5f58d5564fce237fbcc978af033c11b}`

