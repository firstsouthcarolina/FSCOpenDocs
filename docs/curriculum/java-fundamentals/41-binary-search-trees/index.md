# Binary Search Trees

A **binary search tree (BST)** is a tree data structure where each node has at most two children, and the arrangement obeys a strict ordering property: every value in a node's left subtree is smaller than the node's value, and every value in the right subtree is larger.

---

## Terminology

```
        8           ← root
       / \
      3   10        ← children of root
     / \    \
    1   6    14     ← leaves and internal nodes
       / \
      4   7
```

| Term | Definition |
|------|-----------|
| **Root** | The top node — the entry point to the tree |
| **Node** | A single element; contains data and references to children |
| **Leaf** | A node with no children |
| **Height** | The number of edges from root to the deepest leaf |
| **Subtree** | A node and all its descendants |
| **Parent / Child** | A node directly above / directly below another |

---

## The BST Property

For every node `N` in the tree:
- All values in `N`'s **left** subtree are **less than** `N.data`
- All values in `N`'s **right** subtree are **greater than** `N.data`

This property is what makes search, insert, and delete efficient — each comparison eliminates an entire subtree.

---

## The Node Class

```java
public class BSTNode {
    int data;
    BSTNode left;
    BSTNode right;

    public BSTNode(int data) {
        this.data = data;
        this.left = null;
        this.right = null;
    }
}
```

---

## The BST Class

```java
public class BST {
    private BSTNode root;

    public BST() {
        root = null;
    }
}
```

---

## Insert

Start at the root and follow the BST property to find where the new value belongs.

```java
public void insert(int value) {
    root = insertRec(root, value);
}

private BSTNode insertRec(BSTNode node, int value) {
    if (node == null) return new BSTNode(value);  // found the insertion point
    if (value < node.data)      node.left  = insertRec(node.left,  value);
    else if (value > node.data) node.right = insertRec(node.right, value);
    // if value == node.data: duplicate, do nothing (BSTs typically store unique values)
    return node;
}
```

Inserting `{8, 3, 10, 1, 6, 4, 7, 14}` in order builds the tree shown above.

---

## Search

```java
public boolean contains(int value) {
    return containsRec(root, value);
}

private boolean containsRec(BSTNode node, int value) {
    if (node == null) return false;              // reached a dead end
    if (value == node.data) return true;         // found
    if (value < node.data) return containsRec(node.left, value);
    else                   return containsRec(node.right, value);
}
```

Each recursive call moves down exactly one level, eliminating half the remaining nodes at each step. For a **balanced** BST, this is O(log n).

---

## Traversals

A **traversal** visits every node. The order determines what you see:

### In-Order (Left → Node → Right)

Visits nodes in **ascending sorted order** — the most useful traversal for a BST.

```java
public void inOrder() {
    inOrderRec(root);
    System.out.println();
}

private void inOrderRec(BSTNode node) {
    if (node == null) return;
    inOrderRec(node.left);
    System.out.print(node.data + " ");
    inOrderRec(node.right);
}
```

On the example tree: `1 3 4 6 7 8 10 14`

### Pre-Order (Node → Left → Right)

Visits the root before its subtrees. Useful for copying or serializing a tree.

```java
private void preOrderRec(BSTNode node) {
    if (node == null) return;
    System.out.print(node.data + " ");
    preOrderRec(node.left);
    preOrderRec(node.right);
}
```

On the example tree: `8 3 1 6 4 7 10 14`

### Post-Order (Left → Right → Node)

Visits the root after its subtrees. Useful for deleting a tree (delete children before parent).

```java
private void postOrderRec(BSTNode node) {
    if (node == null) return;
    postOrderRec(node.left);
    postOrderRec(node.right);
    System.out.print(node.data + " ");
}
```

On the example tree: `1 4 7 6 3 14 10 8`

---

## Finding Min and Max

The minimum is always the **leftmost** node; the maximum is always the **rightmost** node.

```java
public int min() {
    if (root == null) throw new java.util.NoSuchElementException();
    BSTNode current = root;
    while (current.left != null) current = current.left;
    return current.data;
}

public int max() {
    if (root == null) throw new java.util.NoSuchElementException();
    BSTNode current = root;
    while (current.right != null) current = current.right;
    return current.data;
}
```

---

## Complexity

| Operation | Balanced BST | Degenerate BST (sorted input) |
|-----------|-------------|-------------------------------|
| Search | O(log n) | O(n) |
| Insert | O(log n) | O(n) |
| Min/Max | O(log n) | O(n) |
| In-order traversal | O(n) | O(n) |

A BST degenerates when elements are inserted in sorted order — every new element becomes the rightmost child, producing a linked list with no branching. Inserting `{1, 2, 3, 4, 5}` in order creates:

```
1
 \
  2
   \
    3
     \
      4
       \
        5
```

**Self-balancing trees** (AVL trees, Red-Black trees) prevent this automatically. Java's `TreeMap` and `TreeSet` use a Red-Black tree internally. Exploring self-balancing trees is beyond this module, but knowing the degeneration risk is essential.

---

## Full Example

```java
public class BSTDemo {
    public static void main(String[] args) {
        BST tree = new BST();
        int[] values = {8, 3, 10, 1, 6, 4, 7, 14};
        for (int v : values) tree.insert(v);

        System.out.print("In-order: ");
        tree.inOrder();              // 1 3 4 6 7 8 10 14

        System.out.println("Contains 6: " + tree.contains(6));   // true
        System.out.println("Contains 5: " + tree.contains(5));   // false

        System.out.println("Min: " + tree.min());    // 1
        System.out.println("Max: " + tree.max());    // 14
    }
}
```

---

## Exercise

1. Build a `BST` and insert `{50, 30, 70, 20, 40, 60, 80}`. Draw the resulting tree, then verify by running `inOrder()` — the output should be sorted.
2. Add a `height()` method that returns the height of the tree (the number of edges from root to the deepest leaf). For an empty tree, return -1; for a single-node tree, return 0.
3. Add a `count()` method that returns the total number of nodes in the tree.
4. Insert `{1, 2, 3, 4, 5}` in sorted order and draw the resulting tree. What is the height? What does `contains(3)` have to do to find the value? Compare this to inserting the same values in a random order like `{3, 1, 5, 2, 4}`.
