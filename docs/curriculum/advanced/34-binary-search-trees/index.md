---
icon: lucide/git-fork
---

# Binary Search Trees

A binary search tree stores values in nodes. Each node has at most two children. Values smaller than a node go left; larger values go right.

## Node

```java
public class Node {
    int value;
    Node left;
    Node right;

    Node(int value) {
        this.value = value;
    }
}
```

## Insert

```java
public static Node insert(Node root, int value) {
    if (root == null) {
        return new Node(value);
    }

    if (value < root.value) {
        root.left = insert(root.left, value);
    } else if (value > root.value) {
        root.right = insert(root.right, value);
    }

    return root;
}
```

## Search

```java
public static boolean contains(Node root, int value) {
    if (root == null) {
        return false;
    }

    if (value == root.value) {
        return true;
    }

    if (value < root.value) {
        return contains(root.left, value);
    }

    return contains(root.right, value);
}
```

## Traversal

In-order traversal visits values in sorted order.

```java
public static void printInOrder(Node root) {
    if (root == null) {
        return;
    }

    printInOrder(root.left);
    System.out.println(root.value);
    printInOrder(root.right);
}
```

## Practice

Insert several team numbers into a binary search tree and print them in sorted order.
