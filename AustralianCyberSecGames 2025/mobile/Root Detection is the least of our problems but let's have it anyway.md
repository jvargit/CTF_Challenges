# Root Detection is the least of our problems but let's have it anyway

## Challenge 

In an attempt to implement security best practices, the mobile developers has implemented a root detection mechanism. Can you identify all the binaries that they check for? The flag may or may not be in that list :)

Provided `voting-app.apk`

## Solve

Firstly we decompile the file with `apktool`:

```bash
apktool d voting-app.apk -o decompiled
```

We can find the binary bundle of core application JS logic at `/resources/assets/index.android.bundle`. 

Doing a search for keywords with `grep` shows `isRooted` is somewhere in the compiled code. This [resource](https://labs.cognisys.group/posts/How-to-Decompile-Hermes-React-Native-Binary/) was very useful for the rest of the solve:

- Check file type of `index.android.bundle`:

```bash
file index.android.bundle 
```
> index.android.bundle: Hermes JavaScript bytecode, version 96

- Then decompile with [`hermes_dec`](https://github.com/P1sec/hermes-dec):

```bash
python hbc_decompiler.py index.android.bundle decompiledreact
```

In the decompiled code, we search for keywords in commonly checked paths such as `/system` or `/su` and find:

```js
        r3 = ['/system/of/a/down/su', '/oops/system/bin/su', '/oops/system/xbin/su', '/oops/sbin/su', '/oops/system/su', '/oops/vendor/bin/su', '/oops/system/app/Superuser.apk', '/oops/system/app/SuperSU.apk', '/oops/system/xbin/which', '/oops/data/local/xbin/su', '/oops/data/local/bin/su', '/oops/system/sd/xbin/su', '/oops/system/bin/failsafe/su', 'Y3lzZWF7dWhfdGhpc19haW50X2FfYmluYXJ5X2J1dF9oZXJlJ3NfYV9mbGFnX2M4YWRmYmZmY2R9Cg=='];
```

Decoding from `base64` gives the flag:

`cysea{uh_this_aint_a_binary_but_here's_a_flag_c8adfbffcd}`

