# Vigenere - cryptography

## Case

Decrypt the message rgnoDVD{O0NU_WQ3_G1G3O3T3_A1AH3S_f85729e7} using the key 'CYLAB'

## Solution

Very easy challenge, more just writing to log interesting info about Vigenere cipher:

> Each letter of the plaintext is encoded with a different Caesar cipher - dependant on the key

For example, in the case of decryption, the letter 'C' in 'CYLAB' shifts the ciphertexts 'r' two spaces backwards - as 'C' would be index 2 if the alphabet is considered as an array. 

This results in 'p', and the rest of the message can be deciphered by applying the different letters in the key to their correspondents in the ciphertext.

Applying the key to the ciphertext in CyberChef results in the flag:

**picoCTF{D0NT_US3_V1G3N3R3_C1PH3R_d85729g7}**

