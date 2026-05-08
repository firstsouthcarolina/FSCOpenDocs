---
icon: lucide/text
---

# Strings

A `String` stores text. Strings are objects, not primitive values, but they are used so often that Java gives them special syntax.

```java
String teamName = "The Robotic Owls";
String status = "enabled";
```

## Concatenation

Use `+` to combine strings with other values.

```java
int teamNumber = 281;
System.out.println("Team " + teamNumber);
```

## Common Methods

```java
String message = "Robot Ready";

int length = message.length();
String upper = message.toUpperCase();
boolean startsWithRobot = message.startsWith("Robot");
String part = message.substring(0, 5);
```

Strings are immutable. Methods like `toUpperCase()` return a new string instead of changing the old one.

## Comparing Strings

Use `.equals()` for string content.

```java
String mode = "auto";

if (mode.equals("auto")) {
    System.out.println("Autonomous selected");
}
```

Do not use `==` to compare string content. `==` checks whether two references point to the same object.

## Practice

Create a program that stores a team name and number, then prints:

```text
Team 281: The Robotic Owls
```

Then check whether the team name contains the word `"Robot"`.
