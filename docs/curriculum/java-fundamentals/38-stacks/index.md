# Stacks

A stack is a **Last In, First Out (LIFO)** data structure. The last element added is the first one removed — like a stack of plates. You add to the top and remove from the top; the bottom is never touched directly.

---

## Core Operations

| Operation | Description |
|-----------|-------------|
| `push(value)` | Add an element to the top |
| `pop()` | Remove and return the top element |
| `peek()` | Return the top element without removing it |
| `isEmpty()` | Return `true` if the stack has no elements |
| `size()` | Return the number of elements |

---

## Stack Backed by an Array

The simplest implementation uses an array and a `top` index that tracks where the next `push` goes.

```java
public class ArrayStack {
    private int[] data;
    private int top;

    public ArrayStack(int capacity) {
        data = new int[capacity];
        top = -1;   // -1 means empty
    }

    public boolean isEmpty() { return top == -1; }
    public int size() { return top + 1; }

    public void push(int value) {
        if (top == data.length - 1) throw new RuntimeException("Stack overflow");
        data[++top] = value;
    }

    public int pop() {
        if (isEmpty()) throw new java.util.EmptyStackException();
        return data[top--];
    }

    public int peek() {
        if (isEmpty()) throw new java.util.EmptyStackException();
        return data[top];
    }
}
```

`push` increments `top` first (pre-increment), then stores. `pop` reads first, then decrements (post-decrement). This keeps `top` pointing at the last valid element at all times.

---

## Stack Backed by a Linked List

A linked list stack has **no fixed capacity**. The top of the stack is the **head** of the linked list, so push and pop are both O(1) with no array shifting.

```java
public class LinkedStack {
    private Node top;
    private int size;

    private static class Node {
        int data;
        Node next;
        Node(int data, Node next) {
            this.data = data;
            this.next = next;
        }
    }

    public boolean isEmpty() { return top == null; }
    public int size() { return size; }

    public void push(int value) {
        top = new Node(value, top);   // new node points to old top
        size++;
    }

    public int pop() {
        if (isEmpty()) throw new java.util.EmptyStackException();
        int value = top.data;
        top = top.next;
        size--;
        return value;
    }

    public int peek() {
        if (isEmpty()) throw new java.util.EmptyStackException();
        return top.data;
    }
}
```

The `static` keyword on the inner `Node` class means it does not hold a reference back to the outer `LinkedStack` instance — it is just a utility class nested for organization.

---

## Visualizing a Stack

```
push(1) → push(2) → push(3) → pop() → peek()

After push(1):   [1]          ← top
After push(2):   [1][2]       ← top
After push(3):   [1][2][3]    ← top
After pop():     [1][2]       ← top    returns 3
peek():          [1][2]       ← top    returns 2 (no removal)
```

---

## Using `java.util.Deque` as a Stack

Java's `java.util.Stack` class exists but is considered legacy. The preferred approach is to use `ArrayDeque` as a stack:

```java
import java.util.ArrayDeque;
import java.util.Deque;

Deque<Integer> stack = new ArrayDeque<>();

stack.push(1);
stack.push(2);
stack.push(3);

System.out.println(stack.peek());  // 3
System.out.println(stack.pop());   // 3
System.out.println(stack.size());  // 2
```

`ArrayDeque` is faster than `Stack` and does not have the synchronization overhead. Use `push`/`pop`/`peek` on a `Deque` when you need a stack in production code.

---

## Use Cases

**Undo / Redo** — Every action is pushed onto an undo stack. Ctrl+Z pops the last action and reverses it.

**Balanced parentheses checking** — Push open brackets; when a closing bracket appears, pop and verify it matches.

**The call stack** — Every method call is a frame pushed onto the JVM's own call stack. When the method returns, its frame is popped. A `StackOverflowError` is what happens when the JVM's call stack runs out of space (usually infinite recursion).

**Expression evaluation** — Calculators use stacks to evaluate `3 + 4 * 2` respecting precedence.

---

## Example — Balanced Brackets Checker

```java
import java.util.ArrayDeque;
import java.util.Deque;

public class BracketChecker {
    public static boolean isBalanced(String s) {
        Deque<Character> stack = new ArrayDeque<>();
        for (char c : s.toCharArray()) {
            if (c == '(' || c == '[' || c == '{') {
                stack.push(c);
            } else if (c == ')' || c == ']' || c == '}') {
                if (stack.isEmpty()) return false;
                char open = stack.pop();
                if ((c == ')' && open != '(') ||
                    (c == ']' && open != '[') ||
                    (c == '}' && open != '{')) {
                    return false;
                }
            }
        }
        return stack.isEmpty();
    }

    public static void main(String[] args) {
        System.out.println(isBalanced("({[]})"));   // true
        System.out.println(isBalanced("({[})"));    // false
        System.out.println(isBalanced("((())"));    // false
    }
}
```

---

## Complexity

| Operation | Array-backed | Linked list-backed |
|-----------|-------------|-------------------|
| `push` | O(1) | O(1) |
| `pop` | O(1) | O(1) |
| `peek` | O(1) | O(1) |
| `size` | O(1) | O(1) |

All core stack operations are O(1) regardless of implementation. The difference is capacity: array-backed stacks have a fixed upper limit; linked-list-backed stacks grow freely.

---

## Exercise

1. Using either your `ArrayStack` or `LinkedStack` implementation, write a method `reverseString(String s)` that uses a stack to reverse a string. Test it with a few examples.
2. Write a method `isBalanced(String expression)` that checks whether an expression's parentheses, brackets, and curly braces are balanced (see the example above). Test it with `"({[]})"`, `"(]"`, and `"(()"`.
3. Write a method that takes an integer and uses a stack to return its binary representation as a `String`. Push remainders from repeated division by 2, then pop them all. For example, `toBinary(10)` should return `"1010"`.
