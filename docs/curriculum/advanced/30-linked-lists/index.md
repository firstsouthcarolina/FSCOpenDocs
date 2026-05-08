---
icon: lucide/link
---

# Linked Lists

A linked list stores values in nodes. Each node points to the next node.

```java
public class Node {
    int value;
    Node next;

    Node(int value) {
        this.value = value;
    }
}
```

## Traversal

```java
public static void printList(Node head) {
    Node current = head;

    while (current != null) {
        System.out.println(current.value);
        current = current.next;
    }
}
```

## Insert at Front

```java
public static Node prepend(Node head, int value) {
    Node newHead = new Node(value);
    newHead.next = head;
    return newHead;
}
```

## ArrayList vs LinkedList

| Operation | ArrayList | Linked List |
| --- | --- | --- |
| Access by index | Fast | Slow |
| Add/remove at front | Slow | Fast if head is known |
| Memory overhead | Lower | Higher |

In Java application code, `ArrayList` is often the better default. Linked lists are still important because they teach references and pointer-style structure.

## Practice

Implement a method that counts the nodes in a linked list. Then implement a method that finds whether a value exists.
