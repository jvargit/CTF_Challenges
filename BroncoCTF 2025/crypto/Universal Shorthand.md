# Universal Shorthand - crypto

## Challenge

I just made an amazing shorthand that I can use to write ANY language.

So, I thought I'd spend some time copying down my favorite poem.

And then follow it up with the flag. Not that you'll ever know what it is.

## Solve

For the challenge file, we are provided with an encrypted version of T.S Eliot's 'The Love Song of J Alfred Prufrock':

```
The Love Song of J. Alfred Prufrock:

_`i [R HPO ]`U, ZBh NUY ^F,
L`U ]M kMhEUFD FJ Rgd`Y ^Oi PkH`URi ]P RQ^F
_^FQ P kgXFclUi kMhKPd^FJY PkgSU P kiXFVl_;
_`i [R HPO, KdBh kRehilU \bhC-YFkJehiFY RidMhiR,
]P kT[iPdFD dFkidMhiR
SE kd`Ri_PR U^FiR FU L[U-U^Fi AMhg \POki`_J
NUY kRGhY[Ri kd`RildSUiR LF] kGFRiP-c`_J:
RidMhiR ]Ni kCS_PO _^FQ P kiMhYMPR kbhHZPTPUi
SE FUkRFYMPR FUki`Ui
iBh _MhY ZBh iBh PU mPOEPkL`_TFD kQL`RAPU ...
```

At the bottom of the file we are provided with the encrypted flag:

```
VMh bhd PO `U RMh PO kPOglU VdXFR ^F `_ PO EMh KdMh gMh XFic kJFPdPO `U KdMh iMh L[U RMh `R Q_POJY VdXFR
```

The poem is about 5,500 characters long, providing a large amount of data for known plaintext / ciphertext analysis. I found the original poem [here](https://www.poetryfoundation.org/poetrymagazine/poems/44212/the-love-song-of-j-alfred-prufrock) and began comparing the two versions.

Initial analysis shows that the encryption method is operating like a substitution in some way, for example, the singular plaintext letter `I` can be seen to be consistently replaced with `^F` in the ciphertext. However, when the plaintext `I` is included as part of another word, its correspondent `^F` is not always apparent. 

- E.g plaintext `In` = ciphertext `FU`
 
This was pretty confusing, and since it was determined that singular plaintext characters did not map to singular ciphertext characters, I decided to focus on words instead of individual characters.

After removing the flag and title from the provided `poem.txt` file, the word counts of my cipher and plain text poems were identical - so each plain/cipher word map to each other on a one-to-one basis. I then wrote some code to:

- identify instances of ciphertext words contained in the flag
- identify their plaintext correspondent 
- create a new list with found combinations replaced 

```py
with open('poem.txt', "rb") as file:
    cipher = file.read().split()

with open('plain.txt', "rb") as file:
    plain = file.read().split()

flag = b"VMh bhd PO `U RMh PO kPOglU VdXFR ^F `_ PO EMh KdMh gMh XFic kJFPdPO `U KdMh iMh L[U RMh `R Q_POJY VdXFR"

flag_words = flag.split()

flag_substituted = []

for word in flag_words:
    if word in cipher:
        index = cipher.index(word)
        flag_substituted.append(plain[index])
    else:
        flag_substituted.append(word)

print(flag_words)
print(flag_substituted)
```
![pru](https://github.com/user-attachments/assets/88a6ffed-34e2-4f09-a023-f20d3f88fe9a)


The output shows that some of the words in the flag could easily be found just by considering their positions in the plain and ciphertext.

Then, I began to manually search through the ciphertext for instances of the unreconciled words, and began creating a dictionary containing potential plain/cipher pairs. 

Slowly it became apparent that the ciphertext was not using text as symbols for letters or words, but rather for phonetic sounds. This also made sense as all the words which were matched immediately had letter correspondents, e.g:

- `be` --> `b`
- `sea` --> `c`
- `tea` --> `t`
- `one` --> `1`

After many command F's and much manual checking between plain / cipher texts I had assembled the dictionary:

```py
my_map = {
    b'bhd' : 'r',
    b'PO' : 'o',
    b'`U' : 'n',
    b'lU' : ['en', 'on', 'un'],
    b'`_' : 'el',
    b'SU' : 'on',
    b'Pkg' : 'up',
    b'J' : 's',
    b'Vd' : 'br',
    b'V' : 'b',
    b'd' : 'r',
    b'XFR' : 'ace',
    b'VdXFR' : 'brace',
    b'Mh' : 'e',
    b'dMh' : 're',
    b'gMh' :'pea',
    b'KMh' :'thea',
    b'KdMh' :'threa',
    b'"\"' : 'h',
    b'E' : 'v',
    b'EMh' :'vea',
    b'K' : 'th',
    b'XF' : 'a',
    b'i' : 'te',
    b'c' : 'sh',
    b'XFic' : 'h',
    b'`R': 's',
    b'Q_': 'cl',
    b'JY' : 'ed',
    b'Q_POJY': 'cloed',
    b'kJFPdPO' : 'zero'
}
```

Which I implemented with:

```py
flag_rerolled = []

for word in flag_substituted:
    if word in my_map:
        flag_rerolled.append(my_map[word])
    else:
        flag_rerolled.append(word)
```

Running the complete program resulted in the list:

`[b'be', 'r', 'o', 'n', b'sea', 'o', b'kPOglU', 'brace', b'I', 'el', 'o', 'vea', 'threa', 'pea', 'h', 'zero', 'n', 'threa', b'tea', b'one', b'sea', 's', 'cloed', 'brace']`

Which can be manually converted to:

`bronco{ilov3ph0n3t1cs}`
