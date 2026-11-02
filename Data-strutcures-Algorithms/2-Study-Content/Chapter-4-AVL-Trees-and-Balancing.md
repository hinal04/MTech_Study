# Chapter 4: AVL Trees and Self-Balancing Binary Search Trees

> BITS Pilani — **CSSZG501: Data Structures and Algorithms**

---

## Table of Contents

- [4.1 Need for Balanced Trees](#41-need-for-balanced-trees)
- [4.2 AVL Tree Properties](#42-avl-tree-properties)
- [4.3 Balance Factor and Rotations](#43-balance-factor-and-rotations)
- [4.4 Insertion in AVL Trees](#44-insertion-in-avl-trees)
- [4.5 Deletion in AVL Trees](#45-deletion-in-avl-trees)
- [4.6 Complexity Analysis](#46-complexity-analysis)
- [4.7 AVL Tree Applications](#47-avl-tree-applications)

---

## 4.1 Need for Balanced Trees

### Problem with Binary Search Trees

Unbalanced BST degenerates to linked list:

```
Insert: 1, 2, 3, 4, 5

Unbalanced:
    1
     \
      2
       \
        3
         \
          4
           \
            5

Search: O(n) - walks chain!
```

Balanced tree:
```
      3
     / \
    2   4
   /     \
  1       5

Search: O(log n) - much better
```

### Solution: Self-Balancing Trees

Automatically maintain balance after insertions/deletions:
- AVL trees (strict balancing)
- Red-Black trees (relaxed balancing)
- B-trees (for databases)

---

## 4.2 AVL Tree Properties

### Definition

**AVL Tree (Adelson-Velsky and Landis):**
- Binary Search Tree
- For every node: |height(left) - height(right)| ≤ 1
- Height difference called "balance factor"

### Balance Factor

```
       10
      /  \
     5    15
    / \
   3   7

Node 10: height(left)=2, height(right)=1, BF = 2-1 = 1 ✓
Node 5:  height(left)=1, height(right)=1, BF = 1-1 = 0 ✓
Node 15: height(left)=0, height(right)=0, BF = 0-0 = 0 ✓

All BF in range [-1, 1] → Valid AVL tree
```

### Properties

- **Balanced:** Height always O(log n)
- **Self-maintaining:** Operations maintain balance
- **Strict:** Any imbalance immediately corrected

---

## 4.3 Balance Factor and Rotations

### Identifying Imbalance

**BF = height(left) - height(right)**

- BF > 1: Left-heavy (left subtree too tall)
- BF < -1: Right-heavy (right subtree too tall)
- BF ∈ [-1, 0, 1]: Balanced

### Rotation Operations

**Right Rotation:**
```
    z                                      y
   / \                                   /   \
  y   T4      Right Rotate(z)          x      z
 / \          --------->              / \    / \
x   T3                               T1  T2 T3  T4
/ \
T1  T2
```

**Left Rotation:**
```
  x                                            y
   \                                         /   \
    y          Left Rotate(x)              x      z
   / \         --------->                / \    / \
  T1   z                                T1  T2 T3  T4
      / \
    T2   T3
```

### Four Imbalance Cases

**Case 1: Left-Left (BF=+2, left child BF=+1)**
```
      z                y
     /                / \
    y        →        x   z
   /
  x

Fix: Right rotate z
```

**Case 2: Right-Right (BF=-2, right child BF=-1)**
```
    x                  y
     \                / \
      y       →      x   z
       \
        z

Fix: Left rotate x
```

**Case 3: Left-Right (BF=+2, left child BF=-1)**
```
      z                z              y
     /                /              / \
    x        →       y      →       x   z
     \              /
      y            x

Fix: Left rotate x, then right rotate z
```

**Case 4: Right-Left (BF=-2, right child BF=+1)**
```
    x                 x              y
     \                 \            / \
      z       →        y      →    x   z
     /                  \
    y                    z

Fix: Right rotate z, then left rotate x
```

---

## 4.4 Insertion in AVL Trees

### Insertion Algorithm

```
1. Insert node like normal BST
2. Update balance factors along path to root
3. If any node BF > 1 or < -1:
   - Identify case (LL, RR, LR, RL)
   - Perform appropriate rotation(s)
4. Continue up tree until root
```

### Example: Insert 15

```
Initial tree:
      10
     /  \
    5    20

Insert 15:
      10
     /  \
    5    20
        /
       15

BF(20) = 1 - 0 = 1 ✓
BF(10) = 1 - 2 = -1 ✗ (Right-Left case)

Step 1: Right rotate 20
      10
     /  \
    5    15
         \
         20

Step 2: Left rotate 10
      15
     /  \
    10   20
   /  \
  5   (null)

Final: Balanced ✓
```

### Pseudocode

```
insert(key):
    node = new Node(key)
    root = insertBST(root, node)
    root = balanceTree(root)

insertBST(node, key):
    if node == null:
        return key
    if key < node.key:
        node.left = insertBST(node.left, key)
    else if key > node.key:
        node.right = insertBST(node.right, key)
    return node

balanceTree(node):
    updateHeight(node)
    balance = getBalance(node)
    
    if balance > 1:  // Left-heavy
        if getBalance(node.left) < 0:  // Left-Right
            node.left = rotateLeft(node.left)
        return rotateRight(node)
    
    if balance < -1:  // Right-heavy
        if getBalance(node.right) > 0:  // Right-Left
            node.right = rotateRight(node.right)
        return rotateLeft(node)
    
    return node
```

---

## 4.5 Deletion in AVL Trees

### Deletion Algorithm

```
1. Delete node like normal BST
   a) Leaf: Remove directly
   b) One child: Replace with child
   c) Two children: Replace with inorder successor/predecessor
2. Update balance factors
3. Perform rotations to restore balance
4. Continue up tree
```

### Example: Delete 5

```
Initial:
      10
     /  \
    5    20
   / \
  3   7

Delete 5 (has two children):
- Inorder successor: 7
- Replace 5 with 7
- Delete 7

After deletion:
      10
     /  \
    7    20
   /
  3

BF(10) = 1 - 1 = 0 ✓
```

### Special Cases

**Deletion can cause worse imbalance** than insertion:
- Single insertion → at most one rotation
- Single deletion → may need multiple rotations

---

## 4.6 Complexity Analysis

### Time Complexity

| Operation | Unbalanced BST | AVL Tree |
|-----------|---|---|
| Search | O(n) | O(log n) |
| Insert | O(n) | O(log n) |
| Delete | O(n) | O(log n) |
| Min/Max | O(n) | O(log n) |

### Space Complexity

- O(n) for n nodes
- O(log n) recursive call stack during operations

### Height

For n nodes:
- Minimum height: log₂(n+1)
- Maximum height: 1.44 × log₂(n+2)
- Strictly O(log n)

### Proof: Height Bound

For AVL tree with height h:
- Minimum nodes: N(h) = N(h-1) + N(h-2) + 1
- This is Fibonacci sequence
- N(h) ≥ Fib(h+2)
- Fib(n) ≈ φⁿ/√5, where φ = 1.618 (golden ratio)
- Therefore: n ≥ φʰ/√5
- h ≤ log_φ(√5 × n) ≈ 1.44 × log₂(n)

---

## 4.7 AVL Tree Applications

### Use Cases

**Database Indexing:**
- B-trees (variant of AVL) used in databases
- Ensures O(log n) access

**File Systems:**
- Directory structures
- Maintain performance with many files

**Virtual Memory:**
- Page replacement algorithms
- Balanced tree for efficient lookups

**Priority Queues:**
- Implement with AVL trees
- O(log n) insertion/deletion

### Comparison with Alternatives

| Tree Type | Height | Insert | Delete | Use Case |
|-----------|--------|--------|--------|----------|
| **BST** | O(n) | O(n) | O(n) | Unbalanced, limited data |
| **AVL** | O(log n) | O(log n) | O(log n) | Balanced, many operations |
| **Red-Black** | O(log n) | O(log n) | O(log n) | Relaxed balance, fewer rotations |
| **B-Tree** | O(log n) | O(log n) | O(log n) | Disk-based, many children |

---

## Summary

AVL trees maintain strict balance through height constraints and rotations, ensuring O(log n) operations. While more complex than BSTs and requiring more rotations than Red-Black trees, they provide guaranteed balance suitable for read-heavy workloads and applications requiring predictable performance.

