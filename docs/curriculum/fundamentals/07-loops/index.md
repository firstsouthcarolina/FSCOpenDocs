---
icon: lucide/repeat
---

# Loops

Loops repeat work. They are useful for processing lists, retrying input, and running simulations.

## `while`

Use `while` when you do not know the number of repetitions ahead of time.

```java
int count = 0;

while (count < 5) {
    System.out.println(count);
    count++;
}
```

## `for`

Use `for` when you know the count or are walking through indexes.

```java
for (int i = 0; i < 5; i++) {
    System.out.println(i);
}
```

Indexes usually start at `0`. A loop that uses `< length` avoids going past the end.

## Enhanced `for`

Use enhanced `for` when you only need each value.

```java
int[] ports = {1, 2, 3};

for (int port : ports) {
    System.out.println(port);
}
```

## Break and Continue

```java
for (int i = 0; i < 10; i++) {
    if (i == 5) {
        break;
    }
}
```

`break` exits the loop. `continue` skips to the next iteration.

## Practice

Print all even numbers from `0` through `20`, then print their sum.
