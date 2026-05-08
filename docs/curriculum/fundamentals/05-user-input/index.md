---
icon: lucide/keyboard
---

# User Input

Console programs can read user input with `Scanner`. Robot programs usually read joysticks and sensors instead, but console input is useful for practice.

```java
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        System.out.print("Enter your team number: ");
        int teamNumber = scanner.nextInt();

        System.out.println("Team " + teamNumber);
    }
}
```

## Reading Different Types

```java
int count = scanner.nextInt();
double voltage = scanner.nextDouble();
boolean enabled = scanner.nextBoolean();
String word = scanner.next();
String line = scanner.nextLine();
```

`next()` reads one token. `nextLine()` reads the rest of the line.

## The `nextLine()` Trap

After `nextInt()` or `nextDouble()`, the newline is still waiting in the input buffer. Consume it before reading a full line.

```java
int teamNumber = scanner.nextInt();
scanner.nextLine();

String teamName = scanner.nextLine();
```

## Practice

Ask the user for:

- Team number
- Team name
- Robot weight in pounds

Print a summary sentence using all three values.
