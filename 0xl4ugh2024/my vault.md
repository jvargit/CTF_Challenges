# my vault - cryptography

## Context

Given 3 ciphertexts with encryption script. We are told that each ciphertext (which is a message with a friend) was encrypted with a key in the format 'yearcountry' from when + where the friend was met.

## Solve

To begin with, looking at the encryption script, we can see that the ciphertext is produced using Fermat. So firstly, I wrote a decrypt function which could be used to try and brute force different key combinations:

```py
def decrypt_file(encrypted_file_name, password):
    # Generate a key based on the password
    key = generate_key(password)
    cipher = Fernet(key)

    # Read the encrypted file content
    with open(encrypted_file_name, 'rb') as encrypted_file:
        encrypted_data = encrypted_file.read()

    try:
        # Decrypt the data
        decrypted_data = cipher.decrypt(encrypted_data)

        # Save the decrypted content to a new file
        original_file_name = encrypted_file_name.replace("encrypted_", "")
        with open(f"decrypted_{original_file_name}", 'wb') as decrypted_file:
            decrypted_file.write(decrypted_data)

        print(f"Decryption successful with password: {password}")
        return True  # Decryption succeeded
    except Exception:
        # Handle decryption failure
        pass
        return False  # Decryption failed
```

The function generates a key and Fernet object from the password, reads in the ciphertext, attempts to decrypt, and returns true/false depending on whether a successful decryption is achieved.

Then, I start to create a wordlist which can be used to try a number of combinations of passwords in the form `'yearcountry'`:

1. Create wordlist `countries.txt` containing list of all countries in the world (I sourced the list [here](https://gist.github.com/kalinchernev/486393efcca01623b18d)). 
 - Convert all countries to lower case 

2. Create wordlist `years.txt` containing list of  [years](https://springhole.net/writing/copyable-year-lists-1-5999.html) (originally I started from the year 2000, but ended up having to extend the time range back to 1980)

3. For each year, append every country to generate completed `wordlist.txt`:

```py
with open('countries.txt', 'r') as file:
   countries = file.readlines()

for i in range(len(countries)):
   countries[i]=countries[i].strip()

with open('years.txt', 'r') as file:
   years = file.readlines()

for i in range(len(years)):
   years[i]=years[i].strip()

with open('wordlist.txt', 'w') as file:
    # Loop through each country
    for country in countries:
        # Loop through each year
        for year in years:
            # Write the combination to the file
            file.write(f"{year}{country.lower()}\n")
```

Now, I could feed `wordlist.txt` into the `decrypt_file()` function to brute force key combinations and decrypt the ciphertexts:

```py
with open('wordlist.txt', 'r') as file:
   passwords = file.readlines()

for i in range(len(passwords)):
   passwords[i]=passwords[i].strip()

encrypted_files = ['encrypted_friend1.txt', 'encrypted_friend2.txt', 'encrypted_friend3.txt']

for file in encrypted_files:
    for password in passwords:
        if decrypt_file(file, password):  
            print(f"Decrypted {file} with password: {password}")
            break  # Move to the next file after successful decryption
    else:
        print(f"Couldn't decrypt {file} ")
```

Originally, the code only worked for `encrypted_friend2.txt`. After extending the year range, and looking through the country list for potential errors ('russia' was stored as 'russian federation'), I was able to decrypt all three ciphertexts:

![Screenshot 2024-12-30 173446](https://github.com/user-attachments/assets/d0882143-099a-4e5f-830b-3da9b73f9c4f)


Looking inside each decrypted file, we can find each part of the flag to arrange:

`0xL4ugh{sad!__no_easy_challsanymore}`




