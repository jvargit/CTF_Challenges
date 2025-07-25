# Blink 182 - osint

## Challenge

Have you seen the Freedonians party website?? It looks like a hot mess. Why are their buttons blinking so much? I thought it was cool at first but it looks like one of those weird 90s websites with all the odd animations.

Note: This flag does not contain cysea{}. Please wrap the phrase you find with no spaces in the flag format. E.g. "helloworld" should be wrapped in cysea{helloworld}.

Provided website `https://heritage.freedonia.vote/` in introduction.

## Solve

Viewing the flashing button in the top left, it appears to be flashing in an irregular sequence, with periods of quick flashes, pauses and slow flashes. It's strongly suggestive of morse code.

Capturing the network traffic when visiting the site shows the file `prettyanimation.js` being called:

morse.png

Looking at the code, it definitely is using morse code to relay a hidden message:

```js
document.addEventListener("DOMContentLoaded", () => {
  // Morse code dictionary
  const mC = {
    a: ".-",
    b: "-...",
    c: "-.-.",
    d: "-..",
    e: ".",
    f: "..-.",
    g: "--.",
    h: "....",
    i: "..",
    j: ".---",
    k: "-.-",
    l: ".-..",
    m: "--",
    n: "-.",
    o: "---",
    p: ".--.",
    q: "--.-",
    r: ".-.",
    s: "...",
    t: "-",
    u: "..-",
    v: "...-",
    w: ".--",
    x: "-..-",
    y: "-.--",
    z: "--..",
  };

  const DOT = 200;
  const DASH = DOT * 3;
  const SYMBOL_PAUSE = DOT;
  const LETTER_PAUSE = DOT * 3;
  const WORD_PAUSE = DOT * 7;
  const END_SIGNAL = ".-.-."; // Prosign AR
```

However, instead of trying to decode the message from morse code, we can just see where the secret is coming from and retrieve it that way:

```js
// Load secret message from image pixels
  const img = new Image();
  img.src = "assets/flag.png"; // Image path
  img.crossOrigin = "anonymous"; // Needed if hosted externally

  img.onload = () => {
    const canvas = document.createElement("canvas");
    canvas.width = img.width;
    canvas.height = img.height;
    const ctx = canvas.getContext("2d");
    ctx.drawImage(img, 0, 0);
    const data = ctx.getImageData(0, 0, img.width, 1).data;

    let message = "";
    for (let i = 0; i < data.length; i += 4) {
      const r = data[i];
      if (r === 0) break; // End of message
      message += String.fromCharCode(r);
    }
```

- The program retrieves the file `flag.png`
- Reads data in the first row with `const data = ctx.getImageData(0, 0, img.width, 1).data;`
  - start `y` = `0`, start `x` = `0`, up to `img.width`, with `height` = `1`
  - This returns an array like [R, G, B, A, R, G, B, A.....] where the repeating letters represent the colour channels for individual pixels
- Takes the value of the red channel in every pixel with `for (let i = 0; i < data.length; i += 4) {`
- Converts that value to a character and appends to message with `message += String.fromCharCode(r)`

Now we can imitate this process to obtain the hidden message:
- Visiting `https://heritage.freedonia.vote/assets/flag.png` we download the image
- Running the following script extracts the value in the red colour channel for every pixel in the first row, converts it to a character and appends it to a string:

```py
from PIL import Image

img = Image.open("flag.png")  

img = img.convert("RGBA")

pixels = img.load()
width, height = img.size

flag = ""
for x in range(width):
    r, g, b, a = pixels[x, 0]
    flag += chr(r)

print(flag)
```

This results in the message:

`ofmorseandmen`

Which is wrapped for the flag:

`cysea{ofmorseandmen}`