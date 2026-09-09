# Chapter 3: Non-Linear Data Structures

> BITS Pilani — Department of Computer Science and Information Systems

### Prescribed Textbooks

| Code | Book | Authors | Edition |
|------|------|---------|---------|
| **T1** | Algorithm Design: Foundations, Analysis and Internet Examples | Michael T. Goodrich, Roberto Tamassia | 2006, Wiley |
| **R1** | Introduction to Algorithms (CLRS) | T.H. Cormen, C.E. Leiserson, R.L. Rivest, C. Stein | 3rd Ed, 2009, PHI |
| **R2** | Data Structures, Algorithms and Applications in Java | Sartaj Sahni | 2nd Ed, 2005, Universities Press |

### Reading Map

| Syllabus Topic | R1 Reference |
|----------------|-------------|
| 3.1 Trees — Terminology, Properties | R1: B.5 |
| 3.2 Binary Trees — BST, Traversals | R1: 12.1–12.3 |
| 3.3 Heaps — Operations, Heap Sort | R1: 6.1–6.5 |
| 3.4 Graphs — BFS, DFS | R1: 22.1–22.3 |
| 3.5 Transitive Closure — Warshall's Algorithm | R1: 25.2 |

---

## Table of Contents

- [3.1 Trees](#31-trees)
  - [3.1.1 Linear vs Non-Linear Data Structures](#311-linear-vs-non-linear-data-structures)
  - [3.1.2 Tree Terminology](#312-tree-terminology)
  - [3.1.3 Tree Applications](#313-tree-applications)
- [3.2 Binary Trees](#32-binary-trees)
  - [3.2.1 Properties and Types](#321-properties-and-types)
  - [3.2.2 Important Mathematical Properties](#322-important-mathematical-properties)
  - [3.2.3 Representations](#323-representations)
  - [3.2.4 Binary Search Tree (BST) Operations](#324-binary-search-tree-bst-operations)
  - [3.2.5 Tree Traversals](#325-tree-traversals)
  - [3.2.6 Expression Trees](#326-expression-trees)
  - [3.2.7 Constructing a Tree from Traversals](#327-constructing-a-tree-from-traversals)
- [3.3 Heaps](#33-heaps)
  - [3.3.1 Heap Concept and Types](#331-heap-concept-and-types)
  - [3.3.2 Heap Representation](#332-heap-representation)
  - [3.3.3 Heap Operations](#333-heap-operations)
  - [3.3.4 Building a Heap — Heapify](#334-building-a-heap--heapify)
  - [3.3.5 Heap Sort](#335-heap-sort)
  - [3.3.6 Priority Queue using Heap](#336-priority-queue-using-heap)
- [3.4 Graphs](#34-graphs)
  - [3.4.1 Terms and Definitions](#341-terms-and-definitions)
  - [3.4.2 Graph Properties](#342-graph-properties)
  - [3.4.3 Graph Representations](#343-graph-representations)
  - [3.4.4 Breadth-First Search (BFS)](#344-breadth-first-search-bfs)
  - [3.4.5 Depth-First Search (DFS)](#345-depth-first-search-dfs)
- [3.5 Directed Graphs, Reachability, and Transitive Closure](#35-directed-graphs-reachability-and-transitive-closure)
  - [3.5.1 Reachability](#351-reachability)
  - [3.5.2 Warshall's Algorithm for Transitive Closure](#352-warshalls-algorithm-for-transitive-closure)
- [Key Comparisons Table](#key-comparisons-table)
- [Glossary of Key Terms](#glossary-of-key-terms)

---
---

# 3.1 Trees

> **R1 Reference: Appendix B.5 — Trees**

## 3.1.1 Linear vs Non-Linear Data Structures

In Chapters 1 and 2, we studied **linear data structures** — arrays, stacks, queues, and linked lists — where elements are arranged in a sequential, one-after-another fashion. Each element has exactly one predecessor and one successor (except the first and last). This linearity makes traversal straightforward but limits how we can represent relationships between data.

Many real-world relationships are **hierarchical** or **networked**, not sequential. A company's reporting structure, a file system's directory layout, the structure of an HTML document — none of these fit neatly into a line. This is where **non-linear data structures** come in.

| Aspect | Linear Data Structures | Non-Linear Data Structures |
|--------|----------------------|---------------------------|
| **Arrangement** | Elements in a sequence | Elements in hierarchical or network form |
| **Levels** | Single level | Multiple levels |
| **Traversal** | Single run (one pass) | Multiple approaches (pre/in/post/level order) |
| **Examples** | Array, Stack, Queue, Linked List | Tree, Graph, Heap |
| **Memory utilisation** | Often inefficient for hierarchical data | Efficient for hierarchical/relational data |
| **Relationships** | One-to-one (predecessor → successor) | One-to-many (tree) or many-to-many (graph) |
| **Implementation complexity** | Simpler | More complex |

## 3.1.2 Tree Terminology

A **tree** is a non-linear hierarchical data structure consisting of a collection of **nodes** connected by **edges**. A tree with n nodes has exactly n − 1 edges.

### Labelled Tree Diagram Example

```
                    A  ← Root (depth 0, level 0)
                  / | \
                B   C   D  ← Children of A (depth 1)
               / \     / \
              E   F   G   H  ← depth 2
             /       / \
            I       J   K  ← depth 3 (leaves)
```

### Terminology Table

| Term | Definition | Example (from diagram above) |
|------|-----------|------------------------------|
| **Root** | The topmost node with no parent | A |
| **Parent** | A node that has one or more children | B is parent of E, F |
| **Child** | A node directly connected below another node | E, F are children of B |
| **Sibling** | Nodes that share the same parent | E and F are siblings |
| **Leaf (External node)** | A node with no children (degree 0) | F, C, H, I, J, K |
| **Internal node** | A node with at least one child | A, B, D, E, G |
| **Ancestor** | Any node on the path from a node to the root | Ancestors of J: G, D, A |
| **Descendant** | Any node in the subtree rooted at a node | Descendants of D: G, H, J, K |
| **Depth** | Number of edges from the root to the node | depth(G) = 2, depth(I) = 3 |
| **Height** | Number of edges on the longest path from node to a leaf | height(A) = 3, height(D) = 2 |
| **Degree** | Number of children a node has | degree(A) = 3, degree(B) = 2, degree(C) = 0 |
| **Level** | Set of all nodes at a given depth | Level 2: {E, F, G, H} |
| **Subtree** | A tree formed by a node and all its descendants | Subtree rooted at D: {D, G, H, J, K} |
| **Forest** | A collection of disjoint trees | Removing root A gives forest: {B,E,F,I}, {C}, {D,G,H,J,K} |
| **Edge** | Connection between a parent and child | (A,B), (A,C), (B,E), etc. |
| **Path** | Sequence of nodes connected by edges | Path A→D→G→J |
| **Height of tree** | Height of the root node | 3 |

**Key relationships:**
- A tree with n nodes has exactly **n − 1 edges**
- **Height of tree** = maximum depth of any node
- **Depth of root** = 0, **Height of leaf** = 0

## 3.1.3 Tree Applications

| Application | How Trees Are Used |
|-------------|-------------------|
| **File Systems** | Directories and files form a tree; root directory at top, subdirectories as children |
| **HTML DOM** | HTML tags form a tree; `<html>` is root, `<head>` and `<body>` are children |
| **Organisation Charts** | CEO at root, VPs as children, managers below them |
| **Decision Trees** | Each internal node is a decision, branches are outcomes, leaves are final decisions |
| **Compiler Parse Trees** | Source code is parsed into a syntax tree for analysis and code generation |
| **Database Indexes** | B-trees and B+ trees enable efficient searching in databases |
| **Network Routing** | Spanning trees determine efficient routes in computer networks |
| **Game Trees** | Possible moves in games like chess are represented as trees for AI search |

---
---

# 3.2 Binary Trees

> **R1 Reference: Chapter 12 — Binary Search Trees**

## 3.2.1 Properties and Types

### Definition

A **binary tree** is a tree in which each node has **at most two children**, referred to as the **left child** and the **right child**. This is the most commonly studied tree structure because of its simplicity and the rich set of algorithms it supports.

A **Binary Search Tree (BST)** is a binary tree with an additional ordering property:
- For every node x:
  - All values in x's **left subtree** are **less than** x's value
  - All values in x's **right subtree** are **greater than** x's value

```
         BST Example:
              8
            /   \
           3     10
          / \      \
         1   6     14
            / \    /
           4   7  13
```

**Verify BST property:** Take node 3 — left subtree has {1} (all < 3 ✓), right subtree has {6, 4, 7} (all > 3 ✓). Take node 10 — left subtree is empty, right subtree has {14, 13} (all > 10 ✓).

### Types of Binary Trees

**1. Full Binary Tree (Proper / Strictly Binary Tree)**

Every node has **0 or 2 children** — no node has exactly one child.

```
        1               1
       / \             / \
      2   3           2   3
     / \             / \
    4   5           4   5
                   / \
                  6   7

  Full ✓           Full ✓
```

```
        1
       / \
      2   3
     /
    4

  NOT Full ✗ (node 2 has only 1 child)
```

**2. Complete Binary Tree**

All levels are completely filled **except possibly the last level**, which is filled **from left to right**.

```
        1               1
       / \             / \
      2   3           2   3
     / \ /           / \
    4  5 6          4   5

  Complete ✓       Complete ✓
```

```
        1
       / \
      2   3
       \
        5

  NOT Complete ✗ (level 2 not filled left to right)
```

**3. Perfect Binary Tree**

All internal nodes have **exactly 2 children** AND all leaf nodes are at the **same level**.

```
        1
       / \
      2   3
     / \ / \
    4  5 6  7

  Perfect ✓ (all leaves at level 2, all internal nodes have 2 children)
```

A perfect binary tree of height h has exactly 2^(h+1) − 1 nodes.

**4. Balanced Binary Tree (Height-Balanced)**

For every node, the height of its left subtree and right subtree differ by **at most 1**.

```
        1                   1
       / \                 / \
      2   3               2   3
     / \                 /
    4   5               4
                       /
  Balanced ✓          5

  |h(left) - h(right)| ≤ 1    NOT Balanced ✗
  for every node               (node 1: left height=2, right height=1 → diff=1 ✓
                                but node 2: left height=2, right height=0 → diff=2 ✗)
```

### Types Comparison Table

| Type | Rule | Nodes at height h | Key Property |
|------|------|-------------------|-------------|
| Full | Every node has 0 or 2 children | Variable | No single-child nodes |
| Complete | All levels filled except last (left-to-right) | Between 2^h and 2^(h+1)−1 | Can be stored efficiently in array |
| Perfect | All internals have 2 children, leaves at same level | Exactly 2^(h+1)−1 | Both full AND complete |
| Balanced | Height difference of subtrees ≤ 1 at every node | Variable | Guarantees O(log n) height |

## 3.2.2 Important Mathematical Properties

### Property 1: Maximum nodes at level i = 2^i

**Proof by induction:**
- **Base case (i = 0):** Level 0 has just the root = 1 node = 2^0 = 1 ✓
- **Inductive step:** Assume level k has at most 2^k nodes. Each node at level k can have at most 2 children at level k+1. So level k+1 has at most 2 × 2^k = 2^(k+1) nodes. ✓

### Property 2: Maximum nodes in a binary tree of height h = 2^(h+1) − 1

**Proof:**
Total nodes = sum of maximum nodes at each level from 0 to h
```
= 2^0 + 2^1 + 2^2 + ... + 2^h
= Σ(i=0 to h) 2^i
= 2^(h+1) − 1          (geometric series formula)
```

**Example:** Height 3 → max nodes = 2^4 − 1 = 15

### Property 3: Minimum height for n nodes = ⌊log₂(n)⌋

**Proof:**
A binary tree of height h has at most 2^(h+1) − 1 nodes.
So n ≤ 2^(h+1) − 1 → n + 1 ≤ 2^(h+1) → h + 1 ≥ log₂(n+1) → h ≥ log₂(n+1) − 1 = ⌊log₂(n)⌋

**Maximum height** for n nodes = n − 1 (degenerate/skewed tree — each node has one child).

### Property 4: In a full binary tree, leaf nodes = internal nodes with 2 children + 1

**Statement:** If L = number of leaf nodes and I = number of internal nodes (with 2 children), then L = I + 1.

**Proof:**
Let n = total nodes, L = leaves, I = internal nodes with 2 children.
- n = L + I (in a full binary tree, every node has 0 or 2 children)
- Total edges = n − 1 (every node except root has one incoming edge)
- Each internal node contributes 2 edges (to its 2 children): total edges = 2I
- So n − 1 = 2I → n = 2I + 1
- But n = L + I → L + I = 2I + 1 → **L = I + 1** ✓

**Example:** A full binary tree with 4 internal nodes has 5 leaves.

### Summary of Properties

| Property | Formula |
|----------|---------|
| Max nodes at level i | 2^i |
| Max nodes for height h | 2^(h+1) − 1 |
| Min height for n nodes | ⌊log₂(n)⌋ |
| Max height for n nodes | n − 1 |
| Leaves in full binary tree | I + 1 (where I = internal nodes) |
| Nodes in perfect tree of height h | 2^(h+1) − 1 |
| Height of complete tree with n nodes | ⌊log₂(n)⌋ |

---

## 3.2.3 Representations

### Array-Based Representation

For a node stored at index **i** (0-indexed):
- **Left child** is at index **2i + 1**
- **Right child** is at index **2i + 2**
- **Parent** is at index **⌊(i − 1) / 2⌋**

```
Tree:        1
           /   \
          2     3
         / \   /
        4   5 6

Array (0-indexed):
Index:  0   1   2   3   4   5
Value: [1] [2] [3] [4] [5] [6]

Verify: Node at index 1 (value 2)
  Left child:  2(1)+1 = 3 → value 4 ✓
  Right child: 2(1)+2 = 4 → value 5 ✓
  Parent:      (1-1)/2 = 0 → value 1 ✓
```

**Pseudocode — Array-Based Operations:**

```
PARENT(i):
    return ⌊(i - 1) / 2⌋

LEFT_CHILD(i):
    return 2i + 1

RIGHT_CHILD(i):
    return 2i + 2

FIND_HEIGHT_ARRAY(tree[], n):
    // Height = ⌊log₂(n)⌋ for a complete binary tree
    // For general tree stored in array:
    h = 0
    i = n - 1                    // last node index
    while i > 0:
        i = PARENT(i)
        h = h + 1
    return h

FIND_DEGREE_ARRAY(tree[], n, i):
    degree = 0
    left = 2i + 1
    right = 2i + 2
    if left < n and tree[left] ≠ NULL:
        degree = degree + 1
    if right < n and tree[right] ≠ NULL:
        degree = degree + 1
    return degree
```

| Advantage | Disadvantage |
|-----------|-------------|
| O(1) parent/child access via arithmetic | Wastes space for non-complete trees |
| Cache-friendly (contiguous memory) | Skewed tree of height h needs 2^(h+1)−1 array size |
| Simple implementation | Insertion/deletion may require shifting |
| Ideal for complete binary trees (heaps) | Empty positions waste memory |

### Linked Representation

Each node is a structure with three fields: data, a pointer to the left child, and a pointer to the right child.

```
struct Node:
    data
    left  → Node (or NULL)
    right → Node (or NULL)
```

```
Tree:        8
           /   \
          3     10

Linked Representation:
  [3|•|•] ← [8|•|•] → [10|/|/]
   ↓   ↓                  (no children)
  NULL NULL
```

**Pseudocode — Linked Representation Operations:**

```
FIND_HEIGHT_LINKED(node):
    if node == NULL:
        return -1
    leftHeight = FIND_HEIGHT_LINKED(node.left)
    rightHeight = FIND_HEIGHT_LINKED(node.right)
    return 1 + max(leftHeight, rightHeight)

FIND_DEGREE_LINKED(node):
    if node == NULL:
        return 0
    degree = 0
    if node.left ≠ NULL:
        degree = degree + 1
    if node.right ≠ NULL:
        degree = degree + 1
    return degree

FIND_PARENT_LINKED(root, target):
    if root == NULL or root == target:
        return NULL
    if root.left == target or root.right == target:
        return root
    leftResult = FIND_PARENT_LINKED(root.left, target)
    if leftResult ≠ NULL:
        return leftResult
    return FIND_PARENT_LINKED(root.right, target)

FIND_CHILDREN_LINKED(node):
    if node == NULL:
        return (NULL, NULL)
    return (node.left, node.right)
```

| Advantage | Disadvantage |
|-----------|-------------|
| Space-efficient for any tree shape | Extra memory for 2 pointers per node |
| Easy insertion/deletion | No O(1) parent access (need traversal or parent pointer) |
| Dynamic — grows/shrinks as needed | Not cache-friendly (nodes scattered in memory) |
| Works well for sparse/skewed trees | Slightly more complex to implement |

### Representation Comparison

| Aspect | Array-Based | Linked |
|--------|------------|--------|
| Space for complete tree of n nodes | O(n) | O(n) but with pointer overhead |
| Space for skewed tree of n nodes | O(2^n) wasted | O(n) |
| Parent access | O(1) — ⌊(i−1)/2⌋ | O(n) without parent pointer |
| Child access | O(1) — 2i+1, 2i+2 | O(1) — follow pointer |
| Best use case | Heaps, complete trees | General binary trees, BSTs |

---

## 3.2.4 Binary Search Tree (BST) Operations

### Search in BST

**Idea:** Start at the root. If the target equals the current node, found. If target is smaller, go left. If larger, go right. Repeat until found or reach NULL.

**Pseudocode (Recursive):**

```
BST_SEARCH(node, key):
    if node == NULL:
        return NULL                  // key not found
    if key == node.data:
        return node                  // found
    else if key < node.data:
        return BST_SEARCH(node.left, key)
    else:
        return BST_SEARCH(node.right, key)
```

**Pseudocode (Iterative):**

```
BST_SEARCH_ITERATIVE(root, key):
    current = root
    while current ≠ NULL:
        if key == current.data:
            return current
        else if key < current.data:
            current = current.left
        else:
            current = current.right
    return NULL                      // not found
```

**Worked Example:** Search for 6 in the BST:

```
         8
       /   \
      3     10
     / \      \
    1   6     14
       / \    /
      4   7  13
```

| Step | Current Node | Comparison | Action |
|------|-------------|------------|--------|
| 1 | 8 | 6 < 8 | Go left |
| 2 | 3 | 6 > 3 | Go right |
| 3 | 6 | 6 == 6 | **Found!** ✓ |

**Complexity:**
- **Best case:** O(1) — target is at root
- **Average case:** O(log n) — balanced BST
- **Worst case:** O(n) — skewed tree (degenerate, looks like a linked list)

### Insert in BST

**Idea:** Search for the position where the new key should go. When you reach NULL, that's the insertion point. The new node is always inserted as a **leaf**.

**Pseudocode (Recursive):**

```
BST_INSERT(node, key):
    if node == NULL:
        return CREATE_NODE(key)      // create new leaf node
    if key < node.data:
        node.left = BST_INSERT(node.left, key)
    else if key > node.data:
        node.right = BST_INSERT(node.right, key)
    // if key == node.data, duplicate — do nothing (or handle as needed)
    return node
```

**Pseudocode (Iterative):**

```
BST_INSERT_ITERATIVE(root, key):
    newNode = CREATE_NODE(key)
    if root == NULL:
        return newNode
    current = root
    parent = NULL
    while current ≠ NULL:
        parent = current
        if key < current.data:
            current = current.left
        else if key > current.data:
            current = current.right
        else:
            return root              // duplicate, no insert
    if key < parent.data:
        parent.left = newNode
    else:
        parent.right = newNode
    return root
```

**Worked Example:** Insert 5 into the BST:

```
         8                          8
       /   \                      /   \
      3     10        →          3     10
     / \      \                 / \      \
    1   6     14               1   6     14
       / \    /                   / \    /
      4   7  13                  4   7  13
                                /
                               5  ← new leaf
```

| Step | Current | Comparison | Action |
|------|---------|------------|--------|
| 1 | 8 | 5 < 8 | Go left |
| 2 | 3 | 5 > 3 | Go right |
| 3 | 6 | 5 < 6 | Go left |
| 4 | 4 | 5 > 4 | Go right |
| 5 | NULL | — | Insert 5 as right child of 4 |

**Complexity:** O(h) where h = height of the tree. O(log n) average, O(n) worst case.

### Delete in BST

Deletion is the most complex BST operation because removing a node must preserve the BST property. There are **three cases**:

**Case 1: Node is a leaf (no children)**
Simply remove the node.

**Case 2: Node has one child**
Replace the node with its single child.

**Case 3: Node has two children**
Find the **inorder successor** (smallest node in the right subtree) OR the **inorder predecessor** (largest node in the left subtree). Copy the successor's value into the node, then delete the successor (which will be Case 1 or Case 2).

**Pseudocode:**

```
BST_DELETE(node, key):
    if node == NULL:
        return NULL

    if key < node.data:
        node.left = BST_DELETE(node.left, key)
    else if key > node.data:
        node.right = BST_DELETE(node.right, key)
    else:
        // Found the node to delete
        // Case 1: Leaf node
        if node.left == NULL and node.right == NULL:
            free(node)
            return NULL
        // Case 2: One child
        else if node.left == NULL:
            temp = node.right
            free(node)
            return temp
        else if node.right == NULL:
            temp = node.left
            free(node)
            return temp
        // Case 3: Two children
        else:
            successor = FIND_MIN(node.right)    // inorder successor
            node.data = successor.data
            node.right = BST_DELETE(node.right, successor.data)
    return node

FIND_MIN(node):
    while node.left ≠ NULL:
        node = node.left
    return node
```

**Worked Example — Delete 3 (Case 3: two children):**

```
         8                          8
       /   \                      /   \
      3     10        →          4     10
     / \      \                 / \      \
    1   6     14               1   6     14
       / \    /                   / \    /
      4   7  13                  5   7  13

Step 1: Find node 3 — it has two children
Step 2: Find inorder successor = smallest in right subtree of 3
        Right subtree root = 6, go left → 4 (no left child) → successor = 4
Step 3: Replace 3's data with 4
Step 4: Delete 4 from right subtree (Case 1: leaf) — but wait, if 4 had a
        right child 5, it becomes Case 2: replace 4 with 5
```

**Detailed trace for deleting 3:**

| Step | Action | Tree State |
|------|--------|------------|
| 1 | Search: 3 < 8, go left | At node 3 |
| 2 | Node 3 has two children → Case 3 | — |
| 3 | Find min in right subtree of 3: 6→4 | Inorder successor = 4 |
| 4 | Copy 4 into node 3's position | Node becomes 4 |
| 5 | Delete 4 from right subtree | 4 was leaf → removed |

**Complexity:** O(h). Average O(log n), worst O(n).

### Find Minimum and Maximum in BST

**Minimum:** The leftmost node in the tree (keep going left from root).
**Maximum:** The rightmost node in the tree (keep going right from root).

```
FIND_MIN(node):
    if node == NULL:
        return NULL
    while node.left ≠ NULL:
        node = node.left
    return node

FIND_MAX(node):
    if node == NULL:
        return NULL
    while node.right ≠ NULL:
        node = node.right
    return node
```

**Example:** In the BST below, min = 1 (leftmost), max = 14 (rightmost).
```
         8
       /   \
      3     10
     / \      \
    1   6     14
```

**Complexity:** O(h) for both operations.

### BST Operations Complexity Summary

| Operation | Best Case | Average Case | Worst Case (Skewed) |
|-----------|-----------|-------------|---------------------|
| Search | O(1) | O(log n) | O(n) |
| Insert | O(1) | O(log n) | O(n) |
| Delete | O(log n) | O(log n) | O(n) |
| Find Min | O(1)* | O(log n) | O(n) |
| Find Max | O(1)* | O(log n) | O(n) |

*O(1) if tree is right-skewed (min) or left-skewed (max); practically O(log n) for balanced trees.

---

## 3.2.5 Tree Traversals

Traversal means visiting every node in the tree exactly once. There are four standard traversals, and they are among the **most important topics in this chapter**.

### Sample Tree for All Traversals

```
            1
          /   \
         2     3
        / \   / \
       4   5 6   7
```

### Preorder Traversal (NLR — Node, Left, Right)

Visit the **current node first**, then traverse the left subtree, then the right subtree.

**Recursive Pseudocode:**
```
PREORDER(node):
    if node == NULL:
        return
    VISIT(node)                    // process current node
    PREORDER(node.left)            // traverse left subtree
    PREORDER(node.right)           // traverse right subtree
```

**Iterative Pseudocode (using stack):**
```
PREORDER_ITERATIVE(root):
    if root == NULL:
        return
    create Stack S
    S.push(root)
    while S is not empty:
        node = S.pop()
        VISIT(node)
        if node.right ≠ NULL:      // push right FIRST (so left is processed first)
            S.push(node.right)
        if node.left ≠ NULL:
            S.push(node.left)
```

**Step-by-step trace on sample tree:**

| Step | Action | Output So Far | Stack (iterative) |
|------|--------|--------------|-------------------|
| 1 | Visit 1 | 1 | [3, 2] |
| 2 | Visit 2 | 1, 2 | [3, 5, 4] |
| 3 | Visit 4 | 1, 2, 4 | [3, 5] |
| 4 | Visit 5 | 1, 2, 4, 5 | [3] |
| 5 | Visit 3 | 1, 2, 4, 5, 3 | [7, 6] |
| 6 | Visit 6 | 1, 2, 4, 5, 3, 6 | [7] |
| 7 | Visit 7 | 1, 2, 4, 5, 3, 6, 7 | [] |

**Preorder: 1, 2, 4, 5, 3, 6, 7**

### Inorder Traversal (LNR — Left, Node, Right)

Traverse the left subtree, then **visit the current node**, then traverse the right subtree. For a BST, this produces nodes in **sorted (ascending) order**.

**Recursive Pseudocode:**
```
INORDER(node):
    if node == NULL:
        return
    INORDER(node.left)             // traverse left subtree
    VISIT(node)                    // process current node
    INORDER(node.right)            // traverse right subtree
```

**Iterative Pseudocode (using stack):**
```
INORDER_ITERATIVE(root):
    create Stack S
    current = root
    while current ≠ NULL or S is not empty:
        // Go as far left as possible
        while current ≠ NULL:
            S.push(current)
            current = current.left
        // Backtrack
        current = S.pop()
        VISIT(current)
        current = current.right
```

**Step-by-step trace on sample tree:**

| Step | Action | Output So Far | Stack (iterative) |
|------|--------|--------------|-------------------|
| 1 | Push 1, push 2, push 4 | — | [1, 2, 4] |
| 2 | Pop 4, visit 4, right=NULL | 4 | [1, 2] |
| 3 | Pop 2, visit 2, right=5 | 4, 2 | [1] |
| 4 | Push 5, left=NULL | 4, 2 | [1, 5] |
| 5 | Pop 5, visit 5, right=NULL | 4, 2, 5 | [1] |
| 6 | Pop 1, visit 1, right=3 | 4, 2, 5, 1 | [] |
| 7 | Push 3, push 6, left=NULL | 4, 2, 5, 1 | [3, 6] |
| 8 | Pop 6, visit 6, right=NULL | 4, 2, 5, 1, 6 | [3] |
| 9 | Pop 3, visit 3, right=7 | 4, 2, 5, 1, 6, 3 | [] |
| 10 | Push 7, left=NULL | 4, 2, 5, 1, 6, 3 | [7] |
| 11 | Pop 7, visit 7, right=NULL | 4, 2, 5, 1, 6, 3, 7 | [] |

**Inorder: 4, 2, 5, 1, 6, 3, 7**

**BST Example — Inorder gives sorted output:**
```
         8
       /   \
      3     10
     / \      \
    1   6     14

Inorder: 1, 3, 6, 8, 10, 14 ← sorted! ✓
```

### Postorder Traversal (LRN — Left, Right, Node)

Traverse the left subtree, then the right subtree, then **visit the current node last**.

**Recursive Pseudocode:**
```
POSTORDER(node):
    if node == NULL:
        return
    POSTORDER(node.left)           // traverse left subtree
    POSTORDER(node.right)          // traverse right subtree
    VISIT(node)                    // process current node
```

**Iterative Pseudocode (using two stacks):**
```
POSTORDER_ITERATIVE(root):
    if root == NULL:
        return
    create Stack S1, Stack S2
    S1.push(root)
    while S1 is not empty:
        node = S1.pop()
        S2.push(node)
        if node.left ≠ NULL:
            S1.push(node.left)
        if node.right ≠ NULL:
            S1.push(node.right)
    while S2 is not empty:
        node = S2.pop()
        VISIT(node)
```

**Step-by-step trace on sample tree:**

Recursive trace (call stack visualization):
```
POSTORDER(1)
  POSTORDER(2)
    POSTORDER(4)
      POSTORDER(NULL) → return
      POSTORDER(NULL) → return
      Visit 4 ← output: 4
    POSTORDER(5)
      POSTORDER(NULL) → return
      POSTORDER(NULL) → return
      Visit 5 ← output: 4, 5
    Visit 2 ← output: 4, 5, 2
  POSTORDER(3)
    POSTORDER(6)
      POSTORDER(NULL) → return
      POSTORDER(NULL) → return
      Visit 6 ← output: 4, 5, 2, 6
    POSTORDER(7)
      POSTORDER(NULL) → return
      POSTORDER(NULL) → return
      Visit 7 ← output: 4, 5, 2, 6, 7
    Visit 3 ← output: 4, 5, 2, 6, 7, 3
  Visit 1 ← output: 4, 5, 2, 6, 7, 3, 1
```

**Postorder: 4, 5, 2, 6, 7, 3, 1**

### Level-Order Traversal (BFS — Breadth-First)

Visit nodes level by level, from top to bottom, left to right within each level. Uses a **queue** (not a stack).

**Pseudocode:**
```
LEVEL_ORDER(root):
    if root == NULL:
        return
    create Queue Q
    Q.enqueue(root)
    while Q is not empty:
        node = Q.dequeue()
        VISIT(node)
        if node.left ≠ NULL:
            Q.enqueue(node.left)
        if node.right ≠ NULL:
            Q.enqueue(node.right)
```

**Step-by-step trace on sample tree:**

```
            1
          /   \
         2     3
        / \   / \
       4   5 6   7
```

| Step | Dequeue | Visit | Queue After |
|------|---------|-------|-------------|
| 1 | 1 | 1 | [2, 3] |
| 2 | 2 | 2 | [3, 4, 5] |
| 3 | 3 | 3 | [4, 5, 6, 7] |
| 4 | 4 | 4 | [5, 6, 7] |
| 5 | 5 | 5 | [6, 7] |
| 6 | 6 | 6 | [7] |
| 7 | 7 | 7 | [] |

**Level-Order: 1, 2, 3, 4, 5, 6, 7**

### Traversal Summary

| Traversal | Order | Data Structure | Use Case |
|-----------|-------|---------------|----------|
| Preorder | Node → Left → Right | Stack (implicit/explicit) | Copy tree, prefix expression |
| Inorder | Left → Node → Right | Stack (implicit/explicit) | Sorted order for BST, infix expression |
| Postorder | Left → Right → Node | Stack (implicit/explicit) | Delete tree, postfix expression, compute height |
| Level-order | Level by level | Queue | BFS, shortest path, find width of tree |

**Complexity for all traversals:** O(n) time, O(n) space (O(h) stack space for recursive; O(w) queue space for level-order, where w = max width).

---

## 3.2.6 Expression Trees

An **expression tree** is a binary tree that represents an arithmetic expression. **Operands** (numbers/variables) are at the leaves, and **operators** (+, −, ×, /) are at the internal nodes.

```
Expression: (A + B) * (C - D)

Expression Tree:
            *
          /   \
         +     -
        / \   / \
       A   B C   D
```

### Traversals and Expression Notations

The three standard traversals of an expression tree produce the three standard expression notations:

| Traversal | Notation | Result for (A+B)*(C−D) |
|-----------|----------|------------------------|
| **Preorder** (NLR) | **Prefix** (Polish) | * + A B - C D |
| **Inorder** (LNR) | **Infix** (standard) | A + B * C - D (needs parentheses for correct meaning) |
| **Postorder** (LRN) | **Postfix** (Reverse Polish) | A B + C D - * |

**Why this works:**
- **Preorder** visits the operator before its operands → prefix notation
- **Inorder** visits the operator between its operands → infix notation
- **Postorder** visits the operator after its operands → postfix notation

**Important:** Inorder traversal of an expression tree does NOT automatically produce correct parenthesised infix. You need to add parentheses around each subtree:

```
INORDER_EXPRESSION(node):
    if node is leaf:
        print node.data
    else:
        print "("
        INORDER_EXPRESSION(node.left)
        print node.data
        INORDER_EXPRESSION(node.right)
        print ")"
```

This gives: `((A+B)*(C-D))` — correctly parenthesised.

### Building an Expression Tree from Postfix

**Algorithm:** Scan postfix expression left to right. Push operands as leaf nodes. When you see an operator, pop two nodes, create a new node with the operator, attach the popped nodes as children.

```
BUILD_EXPRESSION_TREE(postfix):
    create Stack S (of tree nodes)
    for each token in postfix:
        if token is operand:
            node = CREATE_NODE(token)
            S.push(node)
        else:                              // token is operator
            right = S.pop()
            left = S.pop()
            node = CREATE_NODE(token)
            node.left = left
            node.right = right
            S.push(node)
    return S.pop()                         // root of expression tree
```

**Worked Example:** Build tree from postfix `A B + C D - *`

| Token | Action | Stack (trees) |
|-------|--------|---------------|
| A | Push leaf A | [A] |
| B | Push leaf B | [A, B] |
| + | Pop B, A → create + node | [+{A,B}] |
| C | Push leaf C | [+{A,B}, C] |
| D | Push leaf D | [+{A,B}, C, D] |
| - | Pop D, C → create - node | [+{A,B}, -{C,D}] |
| * | Pop -{C,D}, +{A,B} → create * node | [*{+{A,B}, -{C,D}}] |

**Result:** The tree rooted at `*` with left child `+{A,B}` and right child `-{C,D}`.

---

## 3.2.7 Constructing a Tree from Traversals

A binary tree can be **uniquely reconstructed** from two traversal sequences, provided one of them is **inorder**. You need either:
- **Preorder + Inorder**, or
- **Postorder + Inorder**

**Why inorder is required:** Preorder or postorder alone tells you the root (first or last element), but you need inorder to determine which nodes belong to the left vs right subtree.

**Why preorder + postorder is NOT sufficient:** Without inorder, you cannot distinguish left from right when a node has only one child.

### Method 1: Preorder + Inorder

**Algorithm:**
1. The **first element** of preorder is the **root**
2. Find the root in the inorder sequence
3. Everything **left** of the root in inorder = left subtree
4. Everything **right** of the root in inorder = right subtree
5. Recursively construct left and right subtrees

**Worked Example:**

```
Preorder: A, B, D, E, C, F, G
Inorder:  D, B, E, A, F, C, G
```

**Step 1:** Root = A (first in preorder)
```
Inorder split: [D, B, E] A [F, C, G]
               left subtree  right subtree

Left subtree preorder:  B, D, E  (next 3 elements from preorder)
Right subtree preorder: C, F, G  (remaining elements)
```

**Step 2:** Left subtree — Root = B (first in left preorder)
```
Left inorder: [D, B, E]
Split at B: [D] B [E]

B's left child = D, B's right child = E
```

**Step 3:** Right subtree — Root = C (first in right preorder)
```
Right inorder: [F, C, G]
Split at C: [F] C [G]

C's left child = F, C's right child = G
```

**Result:**
```
            A
          /   \
         B     C
        / \   / \
       D   E F   G
```

**Verification:**
- Preorder (NLR): A, B, D, E, C, F, G ✓
- Inorder (LNR): D, B, E, A, F, C, G ✓

### Method 2: Postorder + Inorder

**Algorithm:**
1. The **last element** of postorder is the **root**
2. Find the root in the inorder sequence
3. Everything **left** of the root in inorder = left subtree
4. Everything **right** of the root in inorder = right subtree
5. Recursively construct (process right subtree first from postorder, then left)

**Worked Example:**

```
Postorder: D, E, B, F, G, C, A
Inorder:   D, B, E, A, F, C, G
```

**Step 1:** Root = A (last in postorder)
```
Inorder split: [D, B, E] A [F, C, G]

Left subtree postorder:  D, E, B
Right subtree postorder: F, G, C
```

**Step 2:** Right subtree — Root = C (last in right postorder)
```
Right inorder: [F, C, G]
Split at C: [F] C [G]

C's left child = F, C's right child = G
```

**Step 3:** Left subtree — Root = B (last in left postorder)
```
Left inorder: [D, B, E]
Split at B: [D] B [E]

B's left child = D, B's right child = E
```

**Result:**
```
            A
          /   \
         B     C
        / \   / \
       D   E F   G
```

**Pseudocode for Preorder + Inorder Construction:**

```
BUILD_TREE(preorder, inorder, preStart, preEnd, inStart, inEnd):
    if preStart > preEnd:
        return NULL

    rootVal = preorder[preStart]
    root = CREATE_NODE(rootVal)

    // Find root in inorder
    rootIndex = index of rootVal in inorder[inStart..inEnd]
    leftSize = rootIndex - inStart

    root.left = BUILD_TREE(preorder, inorder,
                           preStart + 1, preStart + leftSize,
                           inStart, rootIndex - 1)
    root.right = BUILD_TREE(preorder, inorder,
                            preStart + leftSize + 1, preEnd,
                            rootIndex + 1, inEnd)
    return root
```

**Complexity:** O(n²) naive (searching inorder each time), O(n) with a hash map for inorder positions.

---
---

# 3.3 Heaps

> **R1 Reference: Chapter 6 — Heapsort (6.1–6.5)**

## 3.3.1 Heap Concept and Types

A **heap** is a **complete binary tree** that satisfies the **heap property**. Because it is complete, a heap can be efficiently stored in an array with no wasted space.

### Max-Heap

Every parent node's value is **greater than or equal to** its children's values. The **root contains the maximum** element.

```
Max-Heap:
         50
       /    \
      30     40
     / \    / \
    10  20 15  25

Parent ≥ Children at every node ✓
Root (50) = maximum element
```

### Min-Heap

Every parent node's value is **less than or equal to** its children's values. The **root contains the minimum** element.

```
Min-Heap:
         5
       /   \
      10    15
     / \   / \
    20  25 30  40

Parent ≤ Children at every node ✓
Root (5) = minimum element
```

**Important:** A heap is **NOT** a BST. In a BST, left child < parent < right child. In a heap, both children can be larger (max-heap) or smaller (min-heap) than the parent — there is no ordering between left and right siblings.

| Property | Max-Heap | Min-Heap |
|----------|----------|----------|
| Root | Maximum element | Minimum element |
| Parent vs Children | Parent ≥ children | Parent ≤ children |
| Shape | Complete binary tree | Complete binary tree |
| Sibling ordering | None | None |
| Common use | Heap sort, max-priority queue | Min-priority queue, Dijkstra's |

---

## 3.3.2 Heap Representation

### Array Representation (Standard)

Since a heap is a **complete binary tree**, it maps perfectly to an array with no gaps. This is the standard and preferred representation.

For a node at index **i** (0-indexed):
- **Parent:** ⌊(i − 1) / 2⌋
- **Left child:** 2i + 1
- **Right child:** 2i + 2

```
Max-Heap Tree:
         50
       /    \
      30     40
     / \    / \
    10  20 15  25

Array representation:
Index:  0    1    2    3    4    5    6
Value: [50] [30] [40] [10] [20] [15] [25]

Verify: Index 1 (value 30)
  Parent:      (1-1)/2 = 0 → value 50 (50 ≥ 30 ✓)
  Left child:  2(1)+1 = 3 → value 10 (30 ≥ 10 ✓)
  Right child: 2(1)+2 = 4 → value 20 (30 ≥ 20 ✓)
```

### Linked Representation

Each node stores data, left pointer, right pointer. Less common for heaps because:
- Array gives O(1) parent access; linked needs O(log n) to find parent
- Array is more cache-friendly
- Finding the "last position" for insert/delete is O(1) in array (index = size), O(log n) in linked

Linked representation is used only when the heap size is highly dynamic and memory must be allocated on demand.

---

## 3.3.3 Heap Operations

### Insert (Percolate Up / Bubble Up / Sift Up)

**Idea:** Add the new element at the **end** of the array (to maintain completeness), then **swap it up** with its parent repeatedly until the heap property is restored.

**Pseudocode (Max-Heap):**

```
HEAP_INSERT(A, heapSize, key):
    heapSize = heapSize + 1
    A[heapSize - 1] = key          // place at end
    // Percolate up
    i = heapSize - 1
    while i > 0 and A[i] > A[PARENT(i)]:
        SWAP(A[i], A[PARENT(i)])
        i = PARENT(i)
```

**Worked Example: Insert 45 into max-heap [50, 30, 40, 10, 20, 15, 25]**

```
Step 0: Add 45 at end
Array: [50, 30, 40, 10, 20, 15, 25, 45]

         50
       /    \
      30     40
     / \    / \
    10  20 15  25
   /
  45  ← new element at index 7

Step 1: Compare 45 with parent A[3]=10. 45 > 10 → SWAP
Array: [50, 30, 40, 45, 20, 15, 25, 10]

         50
       /    \
      30     40
     / \    / \
    45  20 15  25
   /
  10

Step 2: Compare 45 with parent A[1]=30. 45 > 30 → SWAP
Array: [50, 45, 40, 30, 20, 15, 25, 10]

         50
       /    \
      45     40
     / \    / \
    30  20 15  25
   /
  10

Step 3: Compare 45 with parent A[0]=50. 45 < 50 → STOP

Final heap: [50, 45, 40, 30, 20, 15, 25, 10] ✓
```

**Complexity:** O(log n) — at most we traverse the height of the tree.

### Delete Max / Extract Max (Percolate Down / Bubble Down / Sift Down)

**Idea:** The maximum is at the root. Replace the root with the **last element**, reduce the heap size, then **swap the new root down** with its larger child repeatedly until the heap property is restored.

**Pseudocode (Max-Heap):**

```
EXTRACT_MAX(A, heapSize):
    if heapSize == 0:
        error "Heap underflow"
    max = A[0]
    A[0] = A[heapSize - 1]        // move last element to root
    heapSize = heapSize - 1
    MAX_HEAPIFY(A, 0, heapSize)   // percolate down
    return max

MAX_HEAPIFY(A, i, heapSize):
    left = 2i + 1
    right = 2i + 2
    largest = i

    if left < heapSize and A[left] > A[largest]:
        largest = left
    if right < heapSize and A[right] > A[largest]:
        largest = right

    if largest ≠ i:
        SWAP(A[i], A[largest])
        MAX_HEAPIFY(A, largest, heapSize)    // continue down
```

**Worked Example: Extract max from [50, 45, 40, 30, 20, 15, 25, 10]**

```
Step 0: Save max = 50. Move last element (10) to root.
Array: [10, 45, 40, 30, 20, 15, 25]

         10  ← violates heap property
       /    \
      45     40
     / \    / \
    30  20 15  25

Step 1: MAX_HEAPIFY at index 0
  left=45, right=40, largest=left(45). 45 > 10 → SWAP(10, 45)
Array: [45, 10, 40, 30, 20, 15, 25]

         45
       /    \
      10     40
     / \    / \
    30  20 15  25

Step 2: MAX_HEAPIFY at index 1
  left=30, right=20, largest=left(30). 30 > 10 → SWAP(10, 30)
Array: [45, 30, 40, 10, 20, 15, 25]

         45
       /    \
      30     40
     / \    / \
    10  20 15  25

Step 3: MAX_HEAPIFY at index 3
  left index = 7 (out of bounds) → STOP

Final heap: [45, 30, 40, 10, 20, 15, 25] ✓
Extracted value: 50
```

**Complexity:** O(log n) — at most traverse the height of the tree.

---

## 3.3.4 Building a Heap — Heapify

### Naive Approach: Repeated Insertion

Insert elements one by one into an empty heap. Each insertion is O(log n), so building a heap from n elements is O(n log n).

### Optimal Approach: Bottom-Up Build (Floyd's Algorithm)

**Key insight:** Leaf nodes (indices ⌊n/2⌋ to n−1) are already trivially valid heaps. We only need to call MAX_HEAPIFY on internal nodes, starting from the **last internal node** up to the root.

**Pseudocode:**

```
BUILD_MAX_HEAP(A, n):
    heapSize = n
    for i = ⌊n/2⌋ - 1 downto 0:     // start from last internal node
        MAX_HEAPIFY(A, i, heapSize)
```

**Why O(n) and not O(n log n)?**

At first glance, we call MAX_HEAPIFY O(n/2) times, each taking O(log n). But the cost varies by level:

| Level from bottom | Nodes at this level | Max heapify cost (swaps) | Total work |
|-------------------|--------------------|-----------------------|------------|
| 0 (leaves) | ~n/2 | 0 (skip) | 0 |
| 1 | ~n/4 | 1 | n/4 |
| 2 | ~n/8 | 2 | n/4 |
| 3 | ~n/16 | 3 | 3n/16 |
| ... | ... | ... | ... |
| h | 1 | h | h |

Total = Σ(i=0 to h) ⌈n / 2^(i+1)⌉ × i = n × Σ(i=0 to h) i / 2^(i+1) ≤ n × Σ(i=0 to ∞) i / 2^(i+1) = n × 2 = **O(n)**

The infinite series Σ i/2^i converges to 2, so BUILD_MAX_HEAP is **O(n)**.

**Worked Example: Build max-heap from [4, 10, 3, 5, 1]**

```
Initial array: [4, 10, 3, 5, 1]
n = 5, last internal node = ⌊5/2⌋ - 1 = 1

         4
       /   \
      10    3
     / \
    5   1

Step 1: MAX_HEAPIFY(A, 1, 5) — node 10
  left=5, right=1. 10 > 5 > 1 → already valid. No swap.

         4
       /   \
      10    3
     / \
    5   1

Step 2: MAX_HEAPIFY(A, 0, 5) — node 4
  left=10, right=3. largest=10. 10 > 4 → SWAP(4, 10)

         10
       /    \
      4      3
     / \
    5   1

  Continue MAX_HEAPIFY(A, 1, 5) — node 4
  left=5, right=1. largest=5. 5 > 4 → SWAP(4, 5)

         10
       /    \
      5      3
     / \
    4   1

  Continue MAX_HEAPIFY(A, 3, 5) — node 4
  left index = 7 (out of bounds) → STOP

Final max-heap: [10, 5, 3, 4, 1] ✓
```

---

## 3.3.5 Heap Sort

Heap sort uses the max-heap property to sort an array in ascending order.

**Algorithm:**
1. Build a max-heap from the input array — O(n)
2. Repeatedly extract the maximum (root) and place it at the end of the array — O(n log n)

**Pseudocode:**

```
HEAP_SORT(A, n):
    BUILD_MAX_HEAP(A, n)           // Step 1: O(n)
    heapSize = n
    for i = n - 1 downto 1:       // Step 2: n-1 extractions
        SWAP(A[0], A[i])           // move current max to end
        heapSize = heapSize - 1
        MAX_HEAPIFY(A, 0, heapSize) // restore heap on reduced array
```

**Worked Example: Sort [4, 10, 3, 5, 1]**

**Phase 1: Build Max-Heap**
(From previous example) → [10, 5, 3, 4, 1]

**Phase 2: Repeated Extract-Max**

```
Iteration 1: SWAP(A[0], A[4]) → [1, 5, 3, 4, | 10]
  MAX_HEAPIFY(A, 0, 4): 1 swaps with 5, then 1 swaps with 4
  → [5, 4, 3, 1, | 10]

Iteration 2: SWAP(A[0], A[3]) → [1, 4, 3, | 5, 10]
  MAX_HEAPIFY(A, 0, 3): 1 swaps with 4
  → [4, 1, 3, | 5, 10]

Iteration 3: SWAP(A[0], A[2]) → [3, 1, | 4, 5, 10]
  MAX_HEAPIFY(A, 0, 2): 3 > 1 → no swap needed
  → [3, 1, | 4, 5, 10]

Iteration 4: SWAP(A[0], A[1]) → [1, | 3, 4, 5, 10]
  Heap size = 1 → done

Sorted array: [1, 3, 4, 5, 10] ✓
```

| Step | Array State | Sorted Portion |
|------|-------------|---------------|
| Build heap | [10, 5, 3, 4, 1] | — |
| Extract 10 | [5, 4, 3, 1, **10**] | {10} |
| Extract 5 | [4, 1, 3, **5, 10**] | {5, 10} |
| Extract 4 | [3, 1, **4, 5, 10**] | {4, 5, 10} |
| Extract 3 | [1, **3, 4, 5, 10**] | {3, 4, 5, 10} |
| Done | [**1, 3, 4, 5, 10**] | {1, 3, 4, 5, 10} |

### Heap Sort Complexity Analysis

| Aspect | Complexity |
|--------|-----------|
| Build heap | O(n) |
| Extract max × (n−1) | O(n log n) |
| **Total time** | **O(n log n)** |
| Space | O(1) — in-place |
| Stable? | **No** — swapping can change relative order of equal elements |
| Best case | O(n log n) |
| Worst case | O(n log n) |
| Average case | O(n log n) |

**Advantages of Heap Sort:**
- O(n log n) guaranteed (no worst-case degradation like quicksort)
- In-place (O(1) extra space)
- Good for external sorting and priority-based scheduling

**Disadvantages:**
- Not stable
- Poor cache performance compared to quicksort (array access patterns are not sequential)
- Higher constant factors than quicksort in practice

---

## 3.3.6 Priority Queue using Heap

A **priority queue** is an ADT where each element has a priority, and the element with the highest (or lowest) priority is served first.

**Why use a heap?** A heap gives the best balance of insertion and extraction performance for a priority queue:

| Implementation | Insert | Extract Max/Min | Peek |
|---------------|--------|----------------|------|
| Unsorted array | O(1) | O(n) | O(n) |
| Sorted array | O(n) | O(1) | O(1) |
| Unsorted linked list | O(1) | O(n) | O(n) |
| Sorted linked list | O(n) | O(1) | O(1) |
| **Binary Heap** | **O(log n)** | **O(log n)** | **O(1)** |

The heap provides O(log n) for both insert and extract, making it the optimal choice when both operations are frequent.

**Priority Queue Operations using Max-Heap:**

```
PQ_INSERT(A, heapSize, key):
    HEAP_INSERT(A, heapSize, key)       // O(log n)

PQ_EXTRACT_MAX(A, heapSize):
    return EXTRACT_MAX(A, heapSize)      // O(log n)

PQ_PEEK(A):
    return A[0]                          // O(1)

PQ_INCREASE_KEY(A, i, newKey):
    if newKey < A[i]:
        error "New key is smaller than current key"
    A[i] = newKey
    while i > 0 and A[i] > A[PARENT(i)]:
        SWAP(A[i], A[PARENT(i)])
        i = PARENT(i)
```

**Applications of Priority Queues:**
- **CPU scheduling:** Highest-priority process runs first
- **Dijkstra's shortest path:** Extracts the vertex with minimum distance
- **Huffman coding:** Repeatedly extract the two minimum-frequency nodes
- **Event-driven simulation:** Process events in order of their scheduled time
- **Median maintenance:** Use two heaps (max-heap for lower half, min-heap for upper half)

---
---

# 3.4 Graphs

> **R1 Reference: Chapter 22 — Elementary Graph Algorithms (22.1–22.3)**

## 3.4.1 Terms and Definitions

A **graph** G = (V, E) consists of:
- **V** = a set of **vertices** (also called nodes)
- **E** = a set of **edges** (also called arcs), where each edge connects two vertices

Graphs are the most general non-linear data structure — trees are a special case of graphs (connected, acyclic graphs).

### Fundamental Terms

| Term | Definition | Example |
|------|-----------|---------|
| **Vertex (Node)** | A fundamental unit of a graph | Cities in a road network |
| **Edge (Arc)** | A connection between two vertices | Road between two cities |
| **Undirected graph** | Edges have no direction; (u,v) = (v,u) | Friendship network (mutual) |
| **Directed graph (Digraph)** | Edges have direction; (u,v) ≠ (v,u) | Twitter follow (one-way) |
| **Weighted graph** | Each edge has an associated numerical value (weight/cost) | Road network with distances |
| **Unweighted graph** | All edges are treated equally (or weight = 1) | Social network connections |
| **Adjacent vertices** | Two vertices connected by an edge | If edge (A,B) exists, A and B are adjacent |
| **Incident edge** | An edge is incident on the vertices it connects | Edge (A,B) is incident on A and B |
| **Degree** | Number of edges connected to a vertex | — |
| **In-degree** | (Directed) Number of edges coming INTO a vertex | — |
| **Out-degree** | (Directed) Number of edges going OUT from a vertex | — |

### Path-Related Terms

| Term | Definition |
|------|-----------|
| **Walk** | A sequence of vertices where each adjacent pair is connected by an edge. Vertices and edges may repeat. |
| **Trail** | A walk in which **no edge is repeated** (but vertices may repeat). |
| **Path** | A walk in which **no vertex is repeated** (and therefore no edge repeats). |
| **Circuit** | A closed trail — a trail that starts and ends at the same vertex. |
| **Cycle** | A closed path — a path that starts and ends at the same vertex (length ≥ 3 for simple graphs). |

### Connectivity Terms

| Term | Definition |
|------|-----------|
| **Connected graph** | (Undirected) There exists a path between every pair of vertices. |
| **Disconnected graph** | At least one pair of vertices has no path between them. |
| **Strongly connected** | (Directed) For every pair (u,v), there is a path from u to v AND from v to u. |
| **Weakly connected** | (Directed) The graph is connected if we ignore edge directions. |
| **Connected component** | A maximal connected subgraph. |

### Special Graph Types

| Type | Definition |
|------|-----------|
| **Simple graph** | No self-loops, no multiple edges between the same pair of vertices. |
| **Multigraph** | Multiple edges allowed between the same pair of vertices. |
| **Complete graph (Kₙ)** | Every pair of vertices is connected. n vertices → n(n−1)/2 edges. |
| **Bipartite graph** | Vertices can be divided into two disjoint sets such that every edge connects a vertex from one set to the other. |
| **Subgraph** | A graph formed from a subset of vertices and edges of the original graph. |
| **Spanning subgraph** | A subgraph that includes ALL vertices of the original graph. |
| **Null graph** | A graph with no edges (but may have vertices). |
| **Trivial graph** | A graph with exactly one vertex and no edges. |
| **Acyclic graph** | A graph with no cycles. A connected acyclic undirected graph is a tree. |
| **DAG** | Directed Acyclic Graph — a directed graph with no directed cycles. |

### Graph Diagrams

```
Undirected Graph:           Directed Graph (Digraph):
  A --- B                     A → B
  |   / |                     ↑   ↓
  |  /  |                     D ← C
  | /   |
  C --- D                   

Weighted Undirected:        Complete Graph K₄:
  A -5- B                     A --- B
  |     |                     |\ /|
  3     2                     | X |
  |     |                     |/ \|
  C -4- D                     C --- D
```

---

## 3.4.2 Graph Properties

### Handshaking Lemma

**Theorem:** In an undirected graph, the sum of degrees of all vertices equals twice the number of edges.

```
Σ deg(v) = 2|E|    for all v ∈ V
```

**Proof:** Each edge (u, v) contributes 1 to the degree of u and 1 to the degree of v. So each edge is counted exactly twice in the sum of degrees.

**Corollary:** The number of vertices with odd degree is always even.

**Example:**
```
  A --- B
  |   / |
  |  /  |
  | /   |
  C --- D

deg(A)=2, deg(B)=3, deg(C)=3, deg(D)=2
Sum = 2+3+3+2 = 10 = 2×5 = 2|E| ✓ (5 edges)
```

### For Directed Graphs

```
Σ in-deg(v) = Σ out-deg(v) = |E|
```

Each edge contributes 1 to the out-degree of its source and 1 to the in-degree of its destination.

### Maximum Edges

| Graph Type | Maximum Edges | Formula |
|-----------|---------------|---------|
| Undirected simple graph | Every pair connected | n(n−1)/2 |
| Directed simple graph | Every ordered pair connected | n(n−1) |
| Complete undirected Kₙ | n(n−1)/2 | K₄ = 6, K₅ = 10 |
| Tree with n vertices | n − 1 | Always |
| Connected undirected graph | At least n − 1 | |E| ≥ |V| − 1 |

**Dense vs Sparse:**
- **Dense graph:** |E| ≈ |V|² (close to maximum edges)
- **Sparse graph:** |E| ≈ |V| (few edges relative to vertices)

---

## 3.4.3 Graph Representations

### Adjacency Matrix

A 2D array `M[V][V]` where `M[i][j] = 1` if there is an edge from vertex i to vertex j, and `M[i][j] = 0` otherwise. For weighted graphs, `M[i][j]` stores the edge weight (or ∞ if no edge).

**Example — Undirected Graph:**
```
Graph:
  0 --- 1
  |   / |
  |  /  |
  | /   |
  2 --- 3

Adjacency Matrix:
     0  1  2  3
  0 [0  1  1  0]
  1 [1  0  1  1]
  2 [1  1  0  1]
  3 [0  1  1  0]

Note: Matrix is SYMMETRIC for undirected graphs (M[i][j] = M[j][i])
```

**Example — Directed Graph:**
```
Graph:
  0 → 1
  ↑   ↓
  3 ← 2

Adjacency Matrix:
     0  1  2  3
  0 [0  1  0  0]
  1 [0  0  1  0]
  2 [0  0  0  1]
  3 [1  0  0  0]

Note: NOT symmetric for directed graphs
```

**Properties:**
- Space: O(V²)
- Check if edge (u,v) exists: O(1)
- Find all neighbors of v: O(V) — scan entire row
- Add edge: O(1)
- Remove edge: O(1)
- Count edges: O(V²)

### Adjacency List

An array of lists. `adj[v]` is a list of all vertices adjacent to v.

**Example — Undirected Graph (same as above):**
```
adj[0]: 1 → 2
adj[1]: 0 → 2 → 3
adj[2]: 0 → 1 → 3
adj[3]: 1 → 2
```

**Example — Directed Graph (same as above):**
```
adj[0]: 1
adj[1]: 2
adj[2]: 3
adj[3]: 0
```

**Properties:**
- Space: O(V + E) — much better for sparse graphs
- Check if edge (u,v) exists: O(degree(u)) — scan u's adjacency list
- Find all neighbors of v: O(degree(v))
- Add edge: O(1) — append to list
- Remove edge: O(degree(u))

### Representation Comparison

| Aspect | Adjacency Matrix | Adjacency List |
|--------|-----------------|----------------|
| **Space** | O(V²) | O(V + E) |
| **Edge lookup** | O(1) | O(degree) |
| **All neighbors** | O(V) | O(degree) |
| **Add edge** | O(1) | O(1) |
| **Remove edge** | O(1) | O(degree) |
| **Best for** | Dense graphs, small V | Sparse graphs, large V |
| **Edge weight** | M[i][j] = weight | Store (vertex, weight) pairs |
| **Memory for sparse** | Wasteful | Efficient |
| **Memory for dense** | Efficient | Overhead from pointers |

**Rule of thumb:** If |E| ≈ |V|², use adjacency matrix. If |E| << |V|², use adjacency list. Most real-world graphs are sparse, so adjacency list is more common.

---

## 3.4.4 Breadth-First Search (BFS)

BFS explores a graph **level by level** — it visits all vertices at distance d from the source before visiting any vertex at distance d+1. This makes it ideal for finding **shortest paths in unweighted graphs**.

BFS uses a **queue** to maintain the frontier of vertices to explore next.

### Pseudocode

```
BFS(G, source):
    for each vertex u in G.V:
        u.color = WHITE           // unvisited
        u.distance = ∞
        u.parent = NULL
    source.color = GRAY           // discovered
    source.distance = 0
    source.parent = NULL
    create Queue Q
    Q.enqueue(source)

    while Q is not empty:
        u = Q.dequeue()
        for each vertex v in G.adj[u]:     // all neighbors of u
            if v.color == WHITE:           // not yet discovered
                v.color = GRAY
                v.distance = u.distance + 1
                v.parent = u
                Q.enqueue(v)
        u.color = BLACK                    // fully explored
```

**Color coding:**
- **WHITE** = undiscovered
- **GRAY** = discovered but not fully explored (in the queue)
- **BLACK** = fully explored (all neighbors discovered)

### Worked Example

```
Graph (undirected):
  0 --- 1 --- 2
  |           |
  3 --- 4 --- 5

Adjacency List:
  0: [1, 3]
  1: [0, 2]
  2: [1, 5]
  3: [0, 4]
  4: [3, 5]
  5: [2, 4]

BFS from source = 0
```

| Step | Dequeue | Process Neighbors | Queue After | Distances |
|------|---------|-------------------|-------------|-----------|
| Init | — | Enqueue 0 | [0] | 0:0, rest:∞ |
| 1 | 0 | 1(new, d=1), 3(new, d=1) | [1, 3] | 0:0, 1:1, 3:1 |
| 2 | 1 | 0(black), 2(new, d=2) | [3, 2] | 0:0, 1:1, 2:2, 3:1 |
| 3 | 3 | 0(black), 4(new, d=2) | [2, 4] | 0:0, 1:1, 2:2, 3:1, 4:2 |
| 4 | 2 | 1(black), 5(new, d=3) | [4, 5] | 0:0, 1:1, 2:2, 3:1, 4:2, 5:3 |
| 5 | 4 | 3(black), 5(gray) | [5] | (no change) |
| 6 | 5 | 2(black), 4(black) | [] | (no change) |

**BFS visit order: 0, 1, 3, 2, 4, 5**

**BFS Tree (shortest path tree):**
```
        0 (d=0)
       / \
      1   3 (d=1)
      |   |
      2   4 (d=2)
      |
      5 (d=3)
```

### BFS Properties

| Property | Value |
|----------|-------|
| **Time complexity** | O(V + E) — each vertex and edge processed once |
| **Space complexity** | O(V) — for queue, color, distance, parent arrays |
| **Gives shortest path?** | Yes, for **unweighted** graphs |
| **Uses** | Queue |
| **Produces** | BFS tree / shortest path tree |

**Applications of BFS:**
- Shortest path in unweighted graphs
- Level-order traversal of trees
- Finding connected components
- Testing bipartiteness
- Web crawling (visit pages layer by layer)
- Social networking (find people within k connections)

---

## 3.4.5 Depth-First Search (DFS)

DFS explores a graph by going **as deep as possible** along each branch before backtracking. It uses a **stack** (or recursion, which implicitly uses the call stack).

### Pseudocode (Recursive)

```
DFS(G):
    for each vertex u in G.V:
        u.color = WHITE
        u.parent = NULL
    time = 0
    for each vertex u in G.V:
        if u.color == WHITE:
            DFS_VISIT(G, u)

DFS_VISIT(G, u):
    time = time + 1
    u.discovery = time             // timestamp when first discovered
    u.color = GRAY
    for each vertex v in G.adj[u]:
        if v.color == WHITE:
            v.parent = u
            DFS_VISIT(G, v)
    u.color = BLACK
    time = time + 1
    u.finish = time                // timestamp when fully explored
```

### Pseudocode (Iterative — using explicit stack)

```
DFS_ITERATIVE(G, source):
    for each vertex u in G.V:
        u.color = WHITE
    create Stack S
    S.push(source)

    while S is not empty:
        u = S.pop()
        if u.color == WHITE:
            u.color = GRAY
            VISIT(u)
            for each vertex v in G.adj[u]:
                if v.color == WHITE:
                    S.push(v)
```

### Worked Example

```
Graph (undirected — same as BFS example):
  0 --- 1 --- 2
  |           |
  3 --- 4 --- 5

Adjacency List (neighbors in order):
  0: [1, 3]
  1: [0, 2]
  2: [1, 5]
  3: [0, 4]
  4: [3, 5]
  5: [2, 4]

DFS from source = 0 (recursive)
```

**Call stack trace:**

```
DFS_VISIT(0)                         discovery=1, color=GRAY
  neighbor 1 is WHITE → DFS_VISIT(1)   discovery=2, color=GRAY
    neighbor 0 is GRAY → skip
    neighbor 2 is WHITE → DFS_VISIT(2)   discovery=3, color=GRAY
      neighbor 1 is GRAY → skip
      neighbor 5 is WHITE → DFS_VISIT(5)   discovery=4, color=GRAY
        neighbor 2 is GRAY → skip
        neighbor 4 is WHITE → DFS_VISIT(4)   discovery=5, color=GRAY
          neighbor 3 is WHITE → DFS_VISIT(3)   discovery=6, color=GRAY
            neighbor 0 is GRAY → skip
            neighbor 4 is GRAY → skip
            3 finish=7, color=BLACK    ←backtrack
          4 finish=8, color=BLACK      ←backtrack
        5 finish=9, color=BLACK        ←backtrack
      2 finish=10, color=BLACK         ←backtrack
    1 finish=11, color=BLACK           ←backtrack
  neighbor 3 is BLACK → skip
  0 finish=12, color=BLACK             ←backtrack
```

**DFS visit order: 0, 1, 2, 5, 4, 3**

**Discovery/Finish times:**

| Vertex | Discovery | Finish |
|--------|-----------|--------|
| 0 | 1 | 12 |
| 1 | 2 | 11 |
| 2 | 3 | 10 |
| 3 | 6 | 7 |
| 4 | 5 | 8 |
| 5 | 4 | 9 |

**DFS Tree:**
```
  0
  |
  1
  |
  2
  |
  5
  |
  4
  |
  3
```

### DFS Properties

| Property | Value |
|----------|-------|
| **Time complexity** | O(V + E) |
| **Space complexity** | O(V) — stack depth in worst case |
| **Gives shortest path?** | No |
| **Uses** | Stack (explicit or recursive call stack) |
| **Produces** | DFS tree / DFS forest, discovery/finish times |

### BFS vs DFS Comparison

| Aspect | BFS | DFS |
|--------|-----|-----|
| **Strategy** | Explore level by level (breadth) | Explore as deep as possible (depth) |
| **Data structure** | Queue | Stack (or recursion) |
| **Shortest path** | Yes (unweighted graphs) | No |
| **Time** | O(V + E) | O(V + E) |
| **Space** | O(V) — width of graph | O(V) — depth of graph |
| **Best for** | Shortest path, level-order, bipartite check | Cycle detection, topological sort, connected components |
| **Visit order** | Closer vertices first | Deeper vertices first |
| **Completeness** | Complete (always finds a solution if one exists) | Complete for finite graphs |

### Edge Classification in DFS

DFS classifies edges of the graph into four types based on discovery/finish times:

| Edge Type | Definition | Detection |
|-----------|-----------|-----------|
| **Tree edge** | Edge in the DFS tree (u→v where v is discovered via u) | v is WHITE when explored |
| **Back edge** | Edge to an ancestor in the DFS tree | v is GRAY when explored |
| **Forward edge** | Edge to a descendant (non-tree edge) | v is BLACK and u.discovery < v.discovery |
| **Cross edge** | Edge to a vertex in a different subtree | v is BLACK and u.discovery > v.discovery |

**Key fact:** An undirected graph has a **cycle** if and only if DFS finds a **back edge**.
A directed graph has a **cycle** if and only if DFS finds a **back edge**.

---
---

# 3.5 Directed Graphs, Reachability, and Transitive Closure

> **R1 Reference: Chapter 25.2 — Transitive Closure**

## 3.5.1 Reachability

**Reachability** asks: given a directed graph G and vertices u, v, can we reach v starting from u by following directed edges?

Vertex v is **reachable** from u if there exists a directed path from u to v in G.

**Transitive Closure** of a directed graph G = (V, E) is a graph G* = (V, E*) where there is an edge (u, v) in E* if and only if there is a **directed path** from u to v in G (including the trivial path from any vertex to itself).

In matrix terms: the transitive closure matrix T has T[i][j] = 1 if vertex j is reachable from vertex i.

```
Original Graph:                Transitive Closure:
  0 → 1                         0 → 1
  ↓   ↓                         0 → 2 (via 0→1→2)
  3   2                         0 → 3 (direct)
                                 1 → 2 (direct)

  Adjacency Matrix:             Transitive Closure Matrix:
       0  1  2  3                     0  1  2  3
  0 [  0  1  0  1 ]             0 [  1  1  1  1 ]
  1 [  0  0  1  0 ]             1 [  0  1  1  0 ]
  2 [  0  0  0  0 ]             2 [  0  0  1  0 ]
  3 [  0  0  0  0 ]             3 [  0  0  0  1 ]
```

**Naive approach:** Run BFS or DFS from every vertex. Time: O(V × (V + E)).

---

## 3.5.2 Warshall's Algorithm for Transitive Closure

Warshall's algorithm computes the transitive closure using **dynamic programming**. It is a boolean version of the Floyd-Warshall all-pairs shortest paths algorithm.

**Key idea:** Consider intermediate vertices one at a time. For each intermediate vertex k, check if adding k as an intermediate step creates a new path from i to j.

**If we can go from i to k AND from k to j, then we can go from i to j.**

### Pseudocode

```
WARSHALL(G):
    n = |V|
    // Initialize T from adjacency matrix
    for i = 0 to n-1:
        for j = 0 to n-1:
            T[i][j] = G.adjMatrix[i][j]
        T[i][i] = 1                   // every vertex reaches itself

    // Main algorithm: consider each vertex k as intermediate
    for k = 0 to n-1:
        for i = 0 to n-1:
            for j = 0 to n-1:
                T[i][j] = T[i][j] OR (T[i][k] AND T[k][j])

    return T
```

**Intuition behind the triple loop:**
- Outer loop (k): "Can we use vertex k as a stepping stone?"
- Middle loop (i): "Starting from vertex i..."
- Inner loop (j): "...can we reach vertex j?"
- `T[i][j] = T[i][j] OR (T[i][k] AND T[k][j])`: "Either we could already reach j from i, OR we can reach k from i AND then reach j from k."

### Worked Example

```
Directed Graph:
  0 → 1 → 2 → 3
  0 → 2

Adjacency Matrix:
     0  1  2  3
  0 [0  1  1  0]
  1 [0  0  1  0]
  2 [0  0  0  1]
  3 [0  0  0  0]

Initialise T (copy adjacency matrix, add self-loops on diagonal):
     0  1  2  3
  0 [1  1  1  0]
  1 [0  1  1  0]
  2 [0  0  1  1]
  3 [0  0  0  1]
```

**k = 0 (use vertex 0 as intermediate):**
Check all (i,j): Can we go i→0→j?
- T[i][0] is 1 only for i=0. So only paths via 0 start from 0.
- 0 already reaches 1, 2 → no new paths.
```
T unchanged after k=0.
```

**k = 1 (use vertex 1 as intermediate):**
Check: T[i][1] AND T[1][j]
- i=0: T[0][1]=1. T[1][j]: j=1(1), j=2(1). 0→1→2 already known. No change.
```
T unchanged after k=1.
```

**k = 2 (use vertex 2 as intermediate):**
Check: T[i][2] AND T[2][j]
- i=0: T[0][2]=1. T[2][3]=1 → T[0][3] = 0 OR (1 AND 1) = **1** ← NEW!
- i=1: T[1][2]=1. T[2][3]=1 → T[1][3] = 0 OR (1 AND 1) = **1** ← NEW!

```
T after k=2:
     0  1  2  3
  0 [1  1  1  1]  ← 0 can now reach 3 (via 2)
  1 [0  1  1  1]  ← 1 can now reach 3 (via 2)
  2 [0  0  1  1]
  3 [0  0  0  1]
```

**k = 3 (use vertex 3 as intermediate):**
T[i][3] AND T[3][j]: T[3][j] is 0 for j=0,1,2. No new paths.
```
T unchanged after k=3.
```

**Final Transitive Closure Matrix:**
```
     0  1  2  3
  0 [1  1  1  1]    0 can reach all vertices
  1 [0  1  1  1]    1 can reach 1, 2, 3
  2 [0  0  1  1]    2 can reach 2, 3
  3 [0  0  0  1]    3 can reach only itself
```

### Complexity Analysis

| Aspect | Complexity |
|--------|-----------|
| **Time** | O(V³) — three nested loops, each running V times |
| **Space** | O(V²) — for the T matrix |

**Execution count table:**

| Line | Operation | Count |
|------|-----------|-------|
| Initialise T | Copy + set diagonal | O(V²) |
| Outer loop (k) | V iterations | V |
| Middle loop (i) | V iterations per k | V² |
| Inner loop (j) | V iterations per (k,i) | V³ |
| OR/AND operation | 1 per (k,i,j) | V³ |
| **Total** | | **O(V³)** |

**When to use Warshall's vs BFS/DFS from each vertex:**
- Warshall's: O(V³) — simple, good for dense graphs
- BFS/DFS from each vertex: O(V × (V + E)) — better for sparse graphs where E << V²

---
---

# Key Comparisons Table

## Data Structure Operations Comparison

| Data Structure | Insert | Delete | Search | Space | Notes |
|---------------|--------|--------|--------|-------|-------|
| **BST (average)** | O(log n) | O(log n) | O(log n) | O(n) | Degrades to O(n) if skewed |
| **BST (worst — skewed)** | O(n) | O(n) | O(n) | O(n) | Essentially a linked list |
| **Balanced BST (AVL/RB)** | O(log n) | O(log n) | O(log n) | O(n) | Guaranteed O(log n) height |
| **Max-Heap** | O(log n) | O(log n)* | O(n) | O(n) | *Delete max is O(log n); arbitrary delete is O(n) |
| **Min-Heap** | O(log n) | O(log n)* | O(n) | O(n) | *Delete min is O(log n) |
| **Graph (adj matrix)** | O(1) edge | O(1) edge | O(1) edge | O(V²) | Good for dense graphs |
| **Graph (adj list)** | O(1) edge | O(deg) edge | O(deg) edge | O(V+E) | Good for sparse graphs |

## Sorting Algorithm Comparison (Including Heap Sort)

| Algorithm | Best | Average | Worst | Space | Stable? | Method |
|-----------|------|---------|-------|-------|---------|--------|
| Insertion Sort | O(n) | O(n²) | O(n²) | O(1) | Yes | Incremental |
| Merge Sort | O(n log n) | O(n log n) | O(n log n) | O(n) | Yes | Divide & Conquer |
| **Heap Sort** | O(n log n) | O(n log n) | O(n log n) | O(1) | No | Selection via heap |
| Quick Sort | O(n log n) | O(n log n) | O(n²) | O(log n) | No | Divide & Conquer |

## Traversal Comparison

| Traversal | Data Structure | Time | Space | Gives Shortest Path? |
|-----------|---------------|------|-------|---------------------|
| BFS | Queue | O(V+E) | O(V) | Yes (unweighted) |
| DFS | Stack / Recursion | O(V+E) | O(V) | No |
| Preorder | Stack / Recursion | O(n) | O(h) | N/A |
| Inorder | Stack / Recursion | O(n) | O(h) | N/A |
| Postorder | Stack / Recursion | O(n) | O(h) | N/A |
| Level-order | Queue | O(n) | O(w) | N/A |

Where h = height of tree, w = maximum width, n = number of nodes.

## Tree Types Quick Reference

| Type | Shape Constraint | Key Use |
|------|-----------------|---------|
| Binary Tree | Each node ≤ 2 children | General hierarchical data |
| BST | Left < Node < Right | Efficient searching |
| Full Binary Tree | Every node has 0 or 2 children | Expression trees |
| Complete Binary Tree | All levels full except last (left-filled) | Heaps |
| Perfect Binary Tree | All leaves at same level, all internal nodes have 2 children | Theoretical analysis |
| Balanced Binary Tree | Height of subtrees differ by ≤ 1 | AVL trees, guaranteed O(log n) |
| Heap | Complete + heap property | Priority queues, sorting |

---

# Glossary of Key Terms

| Term | Definition |
|------|-----------|
| **Acyclic** | A graph with no cycles. |
| **Adjacency** | Two vertices are adjacent if connected by an edge. |
| **Adjacency List** | Graph representation using an array of lists of neighbors. O(V+E) space. |
| **Adjacency Matrix** | Graph representation using a 2D boolean/integer matrix. O(V²) space. |
| **Ancestor** | Any node on the path from a given node to the root. |
| **AVL Tree** | A self-balancing BST where heights of subtrees differ by at most 1. |
| **Back Edge** | In DFS, an edge to an ancestor. Indicates a cycle. |
| **Balanced Binary Tree** | A binary tree where height of left and right subtrees differ by at most 1 at every node. |
| **BFS** | Breadth-First Search. Explores graph level by level using a queue. O(V+E). |
| **Binary Search Tree (BST)** | Binary tree where left subtree < node < right subtree for every node. |
| **Binary Tree** | A tree where each node has at most two children. |
| **Bipartite Graph** | A graph whose vertices can be split into two sets with edges only between sets. |
| **Bubble Up / Percolate Up** | Moving a node upward in a heap to restore the heap property after insertion. |
| **Bubble Down / Percolate Down** | Moving a node downward in a heap to restore the heap property after deletion. |
| **Child** | A node directly connected below another node in a tree. |
| **Circuit** | A closed trail — starts and ends at the same vertex, no repeated edges. |
| **Complete Binary Tree** | All levels filled except possibly the last, which is filled left to right. |
| **Complete Graph (Kₙ)** | A graph where every pair of vertices is connected. n(n−1)/2 edges. |
| **Connected Component** | A maximal connected subgraph of an undirected graph. |
| **Connected Graph** | An undirected graph where a path exists between every pair of vertices. |
| **Cross Edge** | In DFS, an edge to a vertex in a different subtree. |
| **Cycle** | A closed path — starts and ends at same vertex, no repeated vertices. |
| **DAG** | Directed Acyclic Graph — a directed graph with no cycles. |
| **Degree** | Number of edges incident on a vertex. In-degree + out-degree for directed. |
| **Dense Graph** | A graph where |E| is close to |V|². |
| **Depth** | Number of edges from the root to a node. |
| **Descendant** | Any node in the subtree rooted at a given node. |
| **DFS** | Depth-First Search. Explores as deep as possible before backtracking. O(V+E). |
| **Digraph** | Directed graph — edges have a direction. |
| **Edge** | A connection between two vertices in a graph, or parent-child link in a tree. |
| **Expression Tree** | A binary tree representing an arithmetic expression. |
| **Extract Max/Min** | Remove and return the root element of a max-heap/min-heap. O(log n). |
| **Forest** | A collection of disjoint trees (removing the root of a tree creates a forest). |
| **Forward Edge** | In DFS, a non-tree edge to a descendant. |
| **Full Binary Tree** | Every node has 0 or 2 children. |
| **Graph** | G = (V, E). A set of vertices V and edges E connecting pairs of vertices. |
| **Handshaking Lemma** | Sum of all vertex degrees = 2 × number of edges. |
| **Heap** | A complete binary tree satisfying the heap property (max or min). |
| **Heap Sort** | Sort by building a max-heap and repeatedly extracting the max. O(n log n), in-place, not stable. |
| **Heapify** | Convert an arbitrary array into a heap. O(n) using bottom-up approach. |
| **Height** | Number of edges on the longest path from a node to a leaf. |
| **In-degree** | Number of edges directed into a vertex. |
| **Infix** | Standard notation: operator between operands. A + B. |
| **Inorder** | Tree traversal: Left → Node → Right. Gives sorted order for BST. |
| **Internal Node** | A node with at least one child. |
| **Leaf** | A node with no children (degree 0). Also called external node. |
| **Level** | Set of all nodes at the same depth. |
| **Level-Order** | Tree traversal using a queue — visits nodes level by level. |
| **Max-Heap** | Heap where every parent ≥ its children. Root = maximum. |
| **MAX_HEAPIFY** | Procedure to fix a single violation of the max-heap property. O(log n). |
| **Min-Heap** | Heap where every parent ≤ its children. Root = minimum. |
| **Multigraph** | A graph allowing multiple edges between the same pair of vertices. |
| **Out-degree** | Number of edges directed out from a vertex. |
| **Parent** | A node directly above another node in a tree. |
| **Path** | A walk with no repeated vertices. |
| **Perfect Binary Tree** | All internal nodes have 2 children and all leaves are at the same level. |
| **Postfix** | Reverse Polish notation: operator after operands. A B +. |
| **Postorder** | Tree traversal: Left → Right → Node. Used for deletion and postfix expressions. |
| **Prefix** | Polish notation: operator before operands. + A B. |
| **Preorder** | Tree traversal: Node → Left → Right. Used for copying and prefix expressions. |
| **Priority Queue** | ADT where elements are served by priority, not insertion order. Best implemented with a heap. |
| **Reachability** | Whether there exists a directed path from vertex u to vertex v. |
| **Root** | The topmost node of a tree (has no parent). |
| **Sibling** | Nodes that share the same parent. |
| **Simple Graph** | No self-loops, no multiple edges between same pair. |
| **Skewed Tree** | A degenerate tree where every node has at most one child (looks like a list). |
| **Sparse Graph** | A graph where |E| is much less than |V|². |
| **Strongly Connected** | In a directed graph, every vertex is reachable from every other vertex. |
| **Subgraph** | A graph formed from a subset of vertices and edges of another graph. |
| **Subtree** | The tree formed by a node and all its descendants. |
| **Trail** | A walk with no repeated edges. |
| **Transitive Closure** | Graph G* where (u,v) ∈ E* iff there is a path from u to v in G. |
| **Tree** | A connected, acyclic undirected graph. n nodes, n−1 edges. |
| **Tree Edge** | In DFS, an edge in the DFS tree. |
| **Vertex (Node)** | Fundamental unit of a graph or tree. |
| **Walk** | A sequence of vertices where consecutive pairs are connected by edges. |
| **Warshall's Algorithm** | Computes transitive closure of a directed graph in O(V³). |
| **Weighted Graph** | A graph where edges have associated numerical weights. |