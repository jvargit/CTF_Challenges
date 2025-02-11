flag = 'UFJKXQZQUNB'
key = 'SOLVECRYPTO'
plain = []
alphabet = 'abcdefghijklmnopqrstuvwxyz'
for i in range(len(flag)):
    plain.append(alphabet[(((ord(flag[i])-65) - (ord(key[i])-65)) % 26)])

print(''.join(plain))