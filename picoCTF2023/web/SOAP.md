# SOAP - web exploitation

## Case: 

The web project was rushed and no security assessment was done. Can you read the /etc/passwd file?

## Solve:

Viewing the provided website, three buttons can be clicked. Monitoring the websites behaviour in BurpSuite shows that for each button pressed, XML code is sent to the server in a POST request with a specified ID which determines the output:

![SOAP1](https://github.com/user-attachments/assets/6d200473-701d-41c0-9e61-ce93dc3c3ea1)


This suggests an XXE attack may be possible. I send the POST request to the Repeater and adjust the code with the /etc/passwd folder set as the payload:

```xml
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE any [<!ENTITY payload SYSTEM "/etc/passwd"> ]>

<data><ID>&payload;</ID></data>
```

The XXE attack is a success:

![SOAP2](https://github.com/user-attachments/assets/e3e1adb7-4193-48e2-80f0-16eee679547d)


And we can see the flag:

`picoCTF{XML_3xtern@l_3nt1t1ty_e79a75d4}` 
