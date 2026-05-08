# Linked Lists

An array stores its elements in a single contiguous block of memory. A linked list stores elements in individual **nodes** scattered anywhere in memory, connected by references. This structure makes inserting and deleting elements at the front cheap — but shifts the cost elsewhere.

---

## The Node Class

Every element in a linked list lives in a node. A node holds the data and a reference to the next node.

```java
public class Node {
    int data;
    Node next;

    public Node(int data) {
        this.data = data;
        this.next = null;
    }
}
```

`next` starts as `null` — meaning there is no node after this one yet. The last node in any linked list always has `next == null`.

---

## The LinkedList Class

The list itself only needs to know where it starts: the **head** node.

```java
public class LinkedList {
    private Node head;
    private int size;

    public LinkedList() {
        head = null;
        size = 0;
    }

    public int size() { return size; }
    public boolean isEmpty() { return head == null; }
}
```

---

## Traversal

To visit every node, start at `head` and follow each `next` reference until you reach `null`.

```java
public void print() {
    Node current = head;
    while (current != null) {
        System.out.print(current.data);
        if (current.next != null) System.out.print(" → ");
        current = current.next;
    }
    System.out.println();
}
```

```
1 → 2 → 3 → 4 → null
```

Never modify `head` itself during traversal. Use a separate `current` variable that moves through the list.

---

## Inserting at the Head

The cheapest insertion — O(1). Point the new node at the old head, then update head.

```java
public void addFirst(int data) {
    Node newNode = new Node(data);
    newNode.next = head;
    head = newNode;
    size++;
}
```

```
Before: head → [2] → [3] → null
After:  head → [1] → [2] → [3] → null   (addFirst(1))
```

---

## Inserting at the Tail

Must walk to the last node first — O(n).

```java
public void addLast(int data) {
    Node newNode = new Node(data);
    if (head == null) {
        head = newNode;
    } else {
        Node current = head;
        while (current.next != null) {
            current = current.next;
        }
        current.next = newNode;
    }
    size++;
}
```

---

## Inserting at a Position

```java
public void addAt(int index, int data) {
    if (index < 0 || index > size) throw new IndexOutOfBoundsException("Index: " + index);
    if (index == 0) { addFirst(data); return; }

    Node newNode = new Node(data);
    Node current = head;
    for (int i = 0; i < index - 1; i++) {
        current = current.next;
    }
    newNode.next = current.next;
    current.next = newNode;
    size++;
}
```

The key step: point the new node's `next` to the current node's old `next` **before** updating `current.next`. If you reverse the order, you lose the rest of the list.

---

## Removing at the Head

O(1) — just advance head.

```java
public int removeFirst() {
    if (head == null) throw new java.util.NoSuchElementException();
    int data = head.data;
    head = head.next;
    size--;
    return data;
}
```

---

## Removing by Value

```java
public boolean remove(int target) {
    if (head == null) return false;

    if (head.data == target) {
        head = head.next;
        size--;
        return true;
    }

    Node current = head;
    while (current.next != null) {
        if (current.next.data == target) {
            current.next = current.next.next;   // skip over the target node
            size--;
            return true;
        }
        current = current.next;
    }
    return false;
}
```

```
Before: head → [1] → [2] → [3] → null
After:  head → [1] → [3] → null           (remove(2))
```

To remove a node, you need a reference to the node **before** it so you can reroute the `next` pointer around it.

---

## Searching

```java
public boolean contains(int target) {
    Node current = head;
    while (current != null) {
        if (current.data == target) return true;
        current = current.next;
    }
    return false;
}
```

---

## Getting an Element by Index

```java
public int get(int index) {
    if (index < 0 || index >= size) throw new IndexOutOfBoundsException("Index: " + index);
    Node current = head;
    for (int i = 0; i < index; i++) {
        current = current.next;
    }
    return current.data;
}
```

---

## Full Example

```java
public class LinkedListDemo {
    public static void main(String[] args) {
        LinkedList list = new LinkedList();

        list.addLast(1);
        list.addLast(2);
        list.addLast(3);
        list.addFirst(0);
        list.print();           // 0 → 1 → 2 → 3

        list.addAt(2, 99);
        list.print();           // 0 → 1 → 99 → 2 → 3

        list.remove(99);
        list.print();           // 0 → 1 → 2 → 3

        System.out.println("Size: " + list.size());     // 4
        System.out.println("Contains 2: " + list.contains(2));  // true
        System.out.println("Get(1): " + list.get(1));           // 1
    }
}
```

---

## Linked List vs Array

| Operation | Array | Singly Linked List |
|-----------|-------|-------------------|
| Access by index | O(1) | O(n) |
| Insert at head | O(n) — must shift | O(1) |
| Insert at tail | O(1) amortized (ArrayList) | O(n) — must traverse |
| Insert at middle | O(n) — must shift | O(n) — must traverse |
| Delete at head | O(n) — must shift | O(1) |
| Memory | Contiguous block | Scattered nodes + extra `next` pointer per node |
| Size | Fixed (or managed by ArrayList) | Grows/shrinks freely |

A linked list wins when you frequently insert or remove at the **front**. An array wins when you frequently access elements **by index**.

---

## `java.util.LinkedList`

Java's standard library includes a `LinkedList` class in `java.util`. It is a doubly linked list (each node has `next` and `prev`) and implements both the `List` and `Deque` interfaces.

```java
import java.util.LinkedList;

LinkedList<String> list = new LinkedList<>();
list.addFirst("Alice");
list.addLast("Bob");
list.addLast("Charlie");
System.out.println(list.removeFirst());  // Alice
```

Building your own linked list from scratch, as in this module, gives you the intuition for how it works internally. `java.util.LinkedList` is the production version you would use in real code.

---

## Exercise

1. Add a method `reverse()` to the `LinkedList` class that reverses the list **in place** (no new list). Test it.
2. Add a method `removeDuplicates()` that removes all duplicate values from the list, keeping only the first occurrence of each value.
3. Write a method `toArray()` that returns an `int[]` containing the list's elements in order. Write a corresponding static method `fromArray(int[] arr)` that builds a new `LinkedList` from an array.
