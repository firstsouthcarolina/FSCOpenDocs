---
icon: lucide/corner-down-right
---

# Recursion

Recursion happens when a method calls itself. A recursive solution needs a base case and a recursive step.

## Countdown

```java
public static void countDown(int value) {
    if (value <= 0) {
        System.out.println("Done");
        return;
    }

    System.out.println(value);
    countDown(value - 1);
}
```

The base case stops the recursion. Without it, the program eventually crashes with a stack overflow.

## Factorial

```java
public static int factorial(int n) {
    if (n <= 1) {
        return 1;
    }

    return n * factorial(n - 1);
}
```

## Recursion and Trees

Recursion is especially natural for tree-shaped data because each subtree has the same structure as the whole tree.

```java
public static int countNodes(Node node) {
    if (node == null) {
        return 0;
    }

    return 1 + countNodes(node.left()) + countNodes(node.right());
}
```

## Practice

Write a recursive method that returns the sum of numbers from `1` to `n`.
