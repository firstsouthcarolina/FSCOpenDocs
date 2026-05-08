---
icon: lucide/layers
---

# Stacks

A stack is a last-in, first-out data structure. The most recently added value is removed first.

## Operations

- `push`: add to the top.
- `pop`: remove from the top.
- `peek`: read the top without removing it.
- `isEmpty`: check whether the stack has values.

## Java Deque as a Stack

Prefer `ArrayDeque` over the old `Stack` class.

```java
import java.util.ArrayDeque;
import java.util.Deque;

Deque<String> stack = new ArrayDeque<>();
stack.push("disabled");
stack.push("teleop");

String current = stack.pop();
```

## Uses

Stacks are useful for:

- Undo history.
- Parsing nested syntax.
- Depth-first search.
- Tracking method calls in the runtime call stack.

## Balanced Brackets

```java
public static boolean hasBalancedParens(String text) {
    Deque<Character> stack = new ArrayDeque<>();

    for (char ch : text.toCharArray()) {
        if (ch == '(') {
            stack.push(ch);
        } else if (ch == ')') {
            if (stack.isEmpty()) {
                return false;
            }
            stack.pop();
        }
    }

    return stack.isEmpty();
}
```

## Practice

Extend the balanced-parentheses method to support `()`, `[]`, and `{}`.
