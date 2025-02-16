# lucky-flag - web

## Challenge

Just click the flag :)

![luckyflag](https://github.com/user-attachments/assets/306dad41-a24e-49fa-b6f2-5c56195b32ed)


## Solve

When capturing traffic visiting the page we can see that `/main.js` is accessed. Visiting the page, we can see the source is openly accessible:

```js
const $ = q => document.querySelector(q);
const $a = q => document.querySelectorAll(q);

const boxes = $a('.box');
let flagbox = boxes[Math.floor(Math.random() * boxes.length)];

for (const box of boxes) {
  if (box === flagbox) {
    box.onclick = () => {
      let enc = `"\\u000e\\u0003\\u0001\\u0016\\u0004\\u0019\\u0015V\\u0011=\\u000bU=\\u000e\\u0017\\u0001\\t=R\\u0010=\\u0011\\t\\u000bSS\\u001f"`;
      for (let i = 0; i < enc.length; ++i) {
        try {
          enc = JSON.parse(enc);
        } catch (e) { }
      }
      let rw = [];
      for (const e of enc) {
        rw['\x70us\x68'](e['\x63har\x43ode\x41t'](0) ^ 0x62);
      }
      const x = rw['\x6dap'](x => String['\x66rom\x43har\x43ode'](x));
      alert(`Congrats ${x['\x6aoin']('')}`);
    };
    flagbox = null;
  } else {
    box.onclick = () => alert('no flag here');
  }
};
```
- `enc` is a series of JSON-escaped Unicode characters
  - Double slashes `\\` escape non printable characters e.g `u000e`
- `JSON.parse()` unescapes the string to just represent the unicode characters
- `rw['\x70us\x68'](e['\x63har\x43ode\x41t'](0) ^ 0x62);` is an obfuscated `rw.push(e.charCodeAt(0) ^ 0x62)`
  - Each character is xored with `0x62` and stored as a number in `rw[]`
- `const x = rw['\x6dap'](x => String['\x66rom\x43har\x43ode'](x));` is an obfuscated `const x = rw.map(x => String.fromCharCode(x));`
  - Converts each numeral to a String with `String.fromCharCode(x)`
  - Creates new arrary containing decoded characters with `rw.map()` that is stored in `x`


A box is randomly selected using:

```js
boxes[Math.floor(Math.random() * boxes.length)];
```

If the user clicks on the box, the program will decode `enc` and print the flag. Since we have `enc` and the decoding scheme, we can simply decode it ourselves:

```py
dec = []
enc = "\u000e\u0003\u0001\u0016\u0004\u0019\u0015V\u0011=\u000bU=\u000e\u0017\u0001\t=R\u0010=\u0011\t\u000bSS\u001f"

for c in enc:
  xored = chr(ord(c) ^ 0x62)
  dec.append(xored)

print(''.join(dec))
```

`lactf{w4s_i7_luck_0r_ski11}`
