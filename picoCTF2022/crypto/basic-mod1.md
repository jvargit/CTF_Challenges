# basic-mod1 - cryptography

## Case:

We found this weird message being passed around on the servers, we think we have a working decryption scheme.

Take each number mod 37 and map it to the following character set: 0-25 is the alphabet (uppercase), 26-35 are the decimal digits, and 36 is an underscore.

Message:

```
350 63 353 198 114 369 346 184 202 322 94 235 114 110 185 188 225 212 366 374 261 213
```

## Solution: 

To decode this message, I wrote a simple piece of Java code to both perform the mod37 operation on each number in the message and then convert it to its relevant character based on the information given:

```java 
public class Main {
    public static void main(String[] args) {
        int[] cipher = { 350, 63, 353, 198, 114, 369, 346, 184, 202, 322, 94, 235, 114, 110, 185, 188, 225, 212, 366,
                374, 261, 213 };
        char[] alphabet = { 'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R',
                'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z' };
        int[] numbers = { 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 };
        int number;

        for (int i = 0; i < cipher.length; i++) {
            number = cipher[i] % 37;
            if (number <= 25) {
                System.out.print(alphabet[number]);
            } else if (number < 36) {
                System.out.print(numbers[number - 26]);
            } else {
                System.out.print("_");
            }
        }
    }
}
```
> To run code from terminal:
> - Save the code in a text file named 'Main.java' (file name must match public class name)
> - Then compile with the command 'javac Main.java'
> - Then run with command 'java Main'

Running the code results in the following message:

```
R0UND_N_R0UND_ADD17EC2
```

Which can be wrapped to obtain the flag:

**picoCTF{R0UND_N_R0UND_ADD17EC2}**
