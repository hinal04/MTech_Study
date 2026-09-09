# Chapter 3: Non-Linear Data Structures — Questions and Answers

> BITS Pilani — DSA Exam Preparation
> 30 questions covering Trees, Binary Trees, BST, Heaps, Heap Sort, Graphs, BFS, DFS, Floyd-Warshall.
> References: T1 (Goodrich & Tamassia), R1 (CLRS) Ch 6, 12, 22, 25

---

## Table of Contents

- [Section A: Tree Terminology & Concepts (Q1–Q5)](#section-a-tree-terminology--concepts-q1q5)
- [Section B: Binary Tree Traversals (Q6–Q10)](#section-b-binary-tree-traversals-q6q10)
- [Section C: BST Operations (Q11–Q15)](#section-c-bst-operations-q11q15)
- [Section D: Heaps & Heap Sort (Q16–Q20)](#section-d-heaps--heap-sort-q16q20)
- [Section E: Graph Representations (Q21–Q23)](#section-e-graph-representations-q21q23)
- [Section F: BFS & DFS Traversals (Q24–Q27)](#section-f-bfs--dfs-traversals-q24q27)
- [Section G: Floyd-Warshall & Comparisons (Q28–Q30)](#section-g-floyd-warshall--comparisons-q28q30)

---
---

# Section A: Tree Terminology & Concepts (Q1–Q5)

---

### Q1. Define the following tree terminology with examples: root, leaf, internal node, parent, child, sibling, ancestor, descendant, depth, height, subtree, degree.

**Answer:**

Consider this tree:
```
            A           ← root (depth 0)
          / | \
         B  C   D       ← depth 1
        / \     |
       E   F    G       ← depth 2
           |
           H            ← depth 3
```

| Term | Definition | Example |
|------|-----------|---------|
| **Root** | The topmost node with no parent | A |
| **Leaf (external node)** | A node with no children | E, H, C, G |
| **Internal node** | A node with at least one child | A, B, D, F |
| **Parent** | The node directly above a given node | Parent of E is B |
| **Child** | A node directly below a given node | Children of A are B, C, D |
| **Sibling** | Nodes sharing the same parent | B, C, D are siblings |
| **Ancestor** | Any node on the path from a node to the root | Ancestors of H: F, B, A |
| **Descendant** | Any node in the subtree rooted at a node | Descendants of B: E, F, H |
| **Depth** | Number of edges from root to the node | Depth of F = 2 |
| **Height** | Number of edges on the longest path from node to a leaf | Height of B = 2 (B→F→H) |
| **Height of tree** | Height of the root | Height of tree = 3 (A→B→F→H) |
| **Subtree** | Tree formed by a node and all its descendants | Subtree rooted at B: B(E, F(H)) |
| **Degree** | Number of children of a node | Degree of A = 3, degree of E = 0 |

---

### Q2. What is a binary tree? State the properties and types of binary trees.

**Answer:**

A **binary tree** is a tree in which every node has at most 2 children, called the **left child** and **right child**.

**Properties:**

| Property | Formula |
|----------|---------|
| Max nodes at level i | 2^i |
| Max nodes in tree of height h | 2^(h+1) − 1 |
| Min height for n nodes | ⌊log₂ n⌋ |
| For n₀ leaves and n₂ internal nodes with 2 children | n₀ = n₂ + 1 |
| Number of edges | n − 1 (for n nodes) |

**Types:**

| Type | Definition | Example |
|------|-----------|---------|
| **Full (proper) binary tree** | Every node has 0 or 2 children (no node has exactly 1 child) | Decision tree |
| **Complete binary tree** | All levels fully filled except possibly the last; last level filled left to right | Binary heap |
| **Perfect binary tree** | All internal nodes have 2 children and all leaves at the same level | Exactly 2^(h+1) − 1 nodes |
| **Degenerate (skewed) tree** | Every internal node has exactly 1 child (essentially a linked list) | Worst-case BST |
| **Balanced binary tree** | Height difference between left and right subtrees of every node ≤ 1 | AVL tree |

---

### Q3. What is the relationship between the number of leaves and internal nodes in a full binary tree?

**Answer:**

In a **full binary tree** (every node has 0 or 2 children):

**n₀ = n₂ + 1**

Where:
- n₀ = number of leaf nodes (0 children)
- n₂ = number of internal nodes (2 children)

**Proof:**

Total nodes n = n₀ + n₂ (no nodes with 1 child in a full binary tree).

Count edges: Every node except the root has exactly one incoming edge → total edges = n − 1.
Each internal node contributes exactly 2 outgoing edges → total edges = 2 · n₂.

So:
```
n − 1 = 2 · n₂
(n₀ + n₂) − 1 = 2 · n₂
n₀ = n₂ + 1  ✓
```

**Example:** A full binary tree with 7 internal nodes has 8 leaves.

---

### Q4. Given a binary tree with 20 nodes, what is the minimum and maximum possible height?

**Answer:**

**Minimum height (complete binary tree):**
```
h_min = ⌊log₂ 20⌋ = ⌊4.32⌋ = 4
```
A complete binary tree of height 4 can hold between 2⁴ = 16 and 2⁵ − 1 = 31 nodes. 20 fits. ✓

```
Level 0:  1 node   (total: 1)
Level 1:  2 nodes  (total: 3)
Level 2:  4 nodes  (total: 7)
Level 3:  8 nodes  (total: 15)
Level 4:  5 nodes  (total: 20)  ← last level partially filled
```

**Maximum height (degenerate/skewed tree):**
```
h_max = n − 1 = 19
```
Each node has exactly one child, forming a chain.

---

### Q5. A binary tree has 10 leaf nodes. What is the minimum number of nodes it can have? What if it must be a full binary tree?

**Answer:**

**Full binary tree:** Since n₀ = n₂ + 1, we need n₂ = 10 − 1 = 9 internal nodes.
Total = 10 + 9 = **19 nodes**.

**Any binary tree (not necessarily full):** We can have nodes with 1 child. The minimum is when we have the maximum possible nodes with 1 child arranged optimally. However, the absolute minimum number of nodes is still constrained. With 10 leaves, we need at least 10 leaves + 9 internal nodes = **19 nodes** (a full binary tree is actually minimal for leaf count since nodes with 1 child add nodes without adding leaves).

Wait — if a node has exactly 1 child, it adds a node but doesn't increase the leaf count. So having nodes with only 1 child *increases* total nodes for the same leaf count. Therefore the **minimum** is the full binary tree: **19 nodes**.

---
---

# Section B: Binary Tree Traversals (Q6–Q10)

---

### Q6. Perform preorder, inorder, postorder, and level-order traversals on the following tree:

```
         15
        /  \
       10    25
      / \   / \
     5  12 20  30
       /     \
      11      22
```

**Answer:**

**Preorder (Root → Left → Right):**
```
Visit root first, then recursively visit left subtree, then right subtree.

15 → 10 → 5 → 12 → 11 → 25 → 20 → 22 → 30

Result: 15, 10, 5, 12, 11, 25, 20, 22, 30
```

**Inorder (Left → Root → Right):**
```
Recursively visit left subtree, then root, then right subtree.

5 → 10 → 11 → 12 → 15 → 20 → 22 → 25 → 30

Result: 5, 10, 11, 12, 15, 20, 22, 25, 30
(Note: For a BST, inorder always gives sorted order ✓)
```

**Postorder (Left → Right → Root):**
```
Recursively visit left subtree, then right subtree, then root.

5 → 11 → 12 → 10 → 22 → 20 → 30 → 25 → 15

Result: 5, 11, 12, 10, 22, 20, 30, 25, 15
```

**Level-order (BFS):**
```
Visit nodes level by level, left to right.

Level 0: 15
Level 1: 10, 25
Level 2: 5, 12, 20, 30
Level 3: 11, 22

Result: 15, 10, 25, 5, 12, 20, 30, 11, 22
```

---

### Q7. Given the preorder and inorder traversals, construct the unique binary tree.

**Preorder:** A, B, D, E, H, C, F, G
**Inorder:** D, B, H, E, A, F, C, G

**Answer:**

**Step-by-step construction:**

**Step 1:** First element of preorder = **A** → A is the root.
In inorder, A splits: Left subtree = {D, B, H, E}, Right subtree = {F, C, G}.

```
         A
        / \
   {D,B,H,E} {F,C,G}
```

**Step 2:** Next preorder element = **B** → B is root of left subtree.
In inorder {D, B, H, E}, B splits: Left = {D}, Right = {H, E}.

```
         A
        / \
       B   {F,C,G}
      / \
    {D} {H,E}
```

**Step 3:** Next preorder = **D** → D is root of B's left subtree. D has no children (single element).

**Step 4:** Next preorder = **E** → E is root of {H, E}. In inorder, E splits: Left = {H}, Right = {}.

**Step 5:** Next preorder = **H** → H is left child of E.

**Step 6:** Next preorder = **C** → C is root of right subtree {F, C, G}.
In inorder {F, C, G}, C splits: Left = {F}, Right = {G}.

**Step 7:** F is left child of C, G is right child of C.

**Final tree:**
```
            A
          /   \
         B     C
        / \   / \
       D   E F   G
          /
         H
```

**Verification:**
- Preorder: A, B, D, E, H, C, F, G ✓
- Inorder: D, B, H, E, A, F, C, G ✓

---

### Q8. Given the inorder and postorder traversals, construct the binary tree.

**Inorder:** 4, 2, 5, 1, 6, 3, 7
**Postorder:** 4, 5, 2, 6, 7, 3, 1

**Answer:**

**Key insight:** The last element of postorder is the root. Process postorder from right to left.

**Step 1:** Last of postorder = **1** → root.
Inorder splits at 1: Left = {4, 2, 5}, Right = {6, 3, 7}.

**Step 2:** Next from end of postorder = **3** → root of right subtree {6, 3, 7}.
Inorder splits at 3: Left = {6}, Right = {7}.

**Step 3:** Next = **7** → right child of 3.
**Step 4:** Next = **6** → left child of 3.

**Step 5:** Next = **2** → root of left subtree {4, 2, 5}.
Inorder splits at 2: Left = {4}, Right = {5}.

**Step 6:** Next = **5** → right child of 2.
**Step 7:** Next = **4** → left child of 2.

**Final tree:**
```
           1
         /   \
        2     3
       / \   / \
      4   5 6   7
```

**Verification:**
- Inorder: 4, 2, 5, 1, 6, 3, 7 ✓
- Postorder: 4, 5, 2, 6, 7, 3, 1 ✓

---

### Q9. Can you construct a unique binary tree from preorder and postorder traversals alone? Why or why not?

**Answer:**

**No.** Preorder and postorder together do NOT uniquely determine a binary tree (unless the tree is a full binary tree).

**Counterexample:** Consider these two different trees:

```
Tree 1:       Tree 2:
    A             A
   /               \
  B                 B
```

- Tree 1: Preorder = A, B | Postorder = B, A
- Tree 2: Preorder = A, B | Postorder = B, A

Both trees have identical preorder AND postorder, but they are different (B is left child vs. right child).

**Why?** Preorder tells you root → left → right, postorder tells you left → right → root. Neither tells you where the left subtree ends and the right subtree begins. **Inorder is needed** because the root's position in inorder divides nodes into left and right subtrees.

**Exception:** If the tree is a **full binary tree** (every node has 0 or 2 children), then preorder + postorder uniquely determine the tree because there's no ambiguity about which side a single child belongs to.

---

### Q10. Write pseudocode for iterative inorder traversal using a stack. Trace it on:

```
       4
      / \
     2   6
    / \   \
   1   3   7
```

**Answer:**

**Pseudocode:**
```
IterativeInorder(root):
    stack = empty stack
    curr = root
    while curr ≠ NULL or stack is not empty:
        while curr ≠ NULL:         // go as far left as possible
            stack.push(curr)
            curr = curr.left
        curr = stack.pop()         // backtrack
        print(curr.data)           // visit node
        curr = curr.right          // go right
```

**Trace:**

| Step | curr | Stack | Output |
|------|------|-------|--------|
| 1 | 4 | [4] | |
| 2 | 2 | [4, 2] | |
| 3 | 1 | [4, 2, 1] | |
| 4 | NULL (1.left) | [4, 2, 1] | |
| 5 | pop 1, go right | [4, 2] | **1** |
| 6 | NULL (1.right) | [4, 2] | |
| 7 | pop 2, go right | [4] | **2** |
| 8 | 3 | [4, 3] | |
| 9 | NULL (3.left) | [4, 3] | |
| 10 | pop 3, go right | [4] | **3** |
| 11 | NULL (3.right) | [4] | |
| 12 | pop 4, go right | [] | **4** |
| 13 | 6 | [6] | |
| 14 | NULL (6.left) | [6] | |
| 15 | pop 6, go right | [] | **6** |
| 16 | 7 | [7] | |
| 17 | NULL (7.left) | [7] | |
| 18 | pop 7, go right | [] | **7** |
| 19 | NULL, stack empty → done | [] | |

**Output: 1, 2, 3, 4, 6, 7** ✓ (sorted order for BST)

---
---

# Section C: BST Operations (Q11–Q15)

---

### Q11. Insert the following keys into an initially empty BST in the given order: 50, 30, 70, 20, 40, 60, 80, 35, 45. Show the tree after each insertion.

**Answer:**

**BST Insert Rule:** If key < current node → go left; if key > current node → go right; insert at the NULL position found.

```
Insert 50:          50

Insert 30:          50
                   /
                  30

Insert 70:          50
                   / \
                  30  70

Insert 20:          50
                   / \
                  30  70
                 /
                20

Insert 40:          50
                   / \
                  30  70
                 / \
                20  40

Insert 60:          50
                   / \
                  30  70
                 / \ /
                20 40 60

Insert 80:          50
                   /  \
                  30   70
                 / \  / \
                20 40 60 80

Insert 35:          50
                   /  \
                  30   70
                 / \  / \
                20 40 60 80
                  /
                 35

Insert 45:          50
                   /  \
                  30   70
                 / \  / \
                20 40 60 80
                  / \
                 35  45
```

**Final BST:**
```
             50
           /    \
         30      70
        / \     / \
       20  40  60  80
          / \
         35  45
```

**Inorder traversal (sorted): 20, 30, 35, 40, 45, 50, 60, 70, 80** ✓

---

### Q12. Search for key 35 and key 55 in the BST from Q11. Show the comparison trace.

**Answer:**

**Search for 35:**

| Step | Current Node | Comparison | Action |
|------|-------------|------------|--------|
| 1 | 50 | 35 < 50 | Go left |
| 2 | 30 | 35 > 30 | Go right |
| 3 | 40 | 35 < 40 | Go left |
| 4 | 35 | 35 = 35 | **Found!** ✓ |

**Comparisons: 4** (path length from root to node + 1)

**Search for 55 (not in tree):**

| Step | Current Node | Comparison | Action |
|------|-------------|------------|--------|
| 1 | 50 | 55 > 50 | Go right |
| 2 | 70 | 55 < 70 | Go left |
| 3 | 60 | 55 < 60 | Go left |
| 4 | NULL | — | **Not found** ✗ |

**Comparisons: 3** (reached NULL after 3 comparisons)

**Time complexity:** O(h) where h = height of BST. For this balanced tree, h = 3, so O(log n). For a skewed BST, h = n−1, so O(n) worst case.

---

### Q13. Delete keys 20, 40, and 50 (in that order) from the BST in Q11. Show the tree after each deletion.

**Answer:**

**Three cases for BST deletion:**
1. **Leaf node** — simply remove it.
2. **One child** — replace node with its child.
3. **Two children** — replace with inorder successor (smallest in right subtree) or inorder predecessor (largest in left subtree), then delete that successor/predecessor.

**Starting tree:**
```
             50
           /    \
         30      70
        / \     / \
       20  40  60  80
          / \
         35  45
```

**Delete 20 (Case 1: leaf node):**
Node 20 has no children → simply remove it.
```
             50
           /    \
         30      70
          \     / \
          40  60  80
         / \
        35  45
```

**Delete 40 (Case 3: two children):**
Node 40 has two children (35 and 45). Find inorder successor of 40 → **45** (smallest in right subtree of 40, but 40's right child is 45 which is a leaf).

Replace 40 with 45:
```
             50
           /    \
         30      70
          \     / \
          45  60  80
         /
        35
```

**Delete 50 (Case 3: two children):**
Node 50 has two children (30 and 70). Find inorder successor of 50 → **60** (leftmost node in right subtree).

Replace 50 with 60, then delete 60 from its original position (Case 1: leaf after moving):
```
             60
           /    \
         30      70
          \       \
          45      80
         /
        35
```

**Inorder of final tree: 30, 35, 45, 60, 70, 80** ✓

---

### Q14. What is the worst-case and best-case height of a BST with n nodes? When does each occur?

**Answer:**

| Aspect | Best Case | Worst Case |
|--------|-----------|------------|
| **Height** | ⌊log₂ n⌋ | n − 1 |
| **Shape** | Complete / balanced tree | Skewed tree (degenerate) |
| **Occurs when** | Keys inserted in a balanced order (e.g., median first) | Keys inserted in sorted or reverse-sorted order |
| **Search time** | O(log n) | O(n) |

**Example for n = 7:**

Best case (insert: 4, 2, 6, 1, 3, 5, 7):
```
        4
      /   \
     2     6         height = 2 = ⌊log₂ 7⌋
    / \   / \
   1   3 5   7
```

Worst case (insert: 1, 2, 3, 4, 5, 6, 7):
```
1
 \
  2
   \
    3               height = 6 = n − 1
     \
      4
       \
        5
         \
          6
           \
            7
```

**Solution:** Use self-balancing BSTs (AVL, Red-Black) to guarantee O(log n) height.

---

### Q15. Compare BST with a sorted array and an unsorted array for search, insert, and delete operations.

**Answer:**

| Operation | Unsorted Array | Sorted Array | BST (balanced) | BST (worst) |
|-----------|---------------|-------------|----------------|-------------|
| Search | O(n) | O(log n) binary search | O(log n) | O(n) |
| Insert | O(1) append | O(n) shift elements | O(log n) | O(n) |
| Delete | O(n) find + shift | O(n) shift elements | O(log n) | O(n) |
| Min/Max | O(n) | O(1) first/last | O(log n) | O(n) |
| Sorted traversal | O(n log n) sort | O(n) scan | O(n) inorder | O(n) |

**Key insight:** A balanced BST offers O(log n) for all three operations — the best overall trade-off. Sorted arrays are great for search-heavy workloads but poor for frequent inserts/deletes.

---
---

# Section D: Heaps & Heap Sort (Q16–Q20)

---

### Q16. Define a max-heap. What are its properties? How is it stored in an array?

**Answer:**

A **max-heap** is a complete binary tree where every node's value is ≥ the values of its children.

**Heap property:** For every node i (except root): `A[parent(i)] ≥ A[i]`

**Array representation** (1-indexed):
```
Parent of node i:      ⌊i/2⌋
Left child of node i:  2i
Right child of node i: 2i + 1
```

**Example:**
```
Tree:           90
              /    \
            80      70
           / \     /
          50  60  30

Array (1-indexed): [_, 90, 80, 70, 50, 60, 30]
                       1   2   3   4   5   6
```

| Property | Value |
|----------|-------|
| Root (max element) | A[1] |
| Last element | A[n] |
| Last internal node | A[⌊n/2⌋] |
| Height | ⌊log₂ n⌋ |
| Shape | Always a complete binary tree |

---

### Q17. Insert the values 10, 20, 15, 30, 25, 5, 35 into an initially empty max-heap. Show the heap after each insertion.

**Answer:**

**Heap insert:** Add element at the end (next available position in complete tree), then **bubble up** (swap with parent while larger than parent).

**Insert 10:**
```
Array: [10]
Tree:  10
```

**Insert 20:** Add at end, bubble up (20 > 10 → swap).
```
Array: [20, 10]
Tree:   20
       /
      10
```

**Insert 15:** Add at end (right child of 20). 15 < 20 → no swap needed.
```
Array: [20, 10, 15]
Tree:   20
       / \
      10  15
```

**Insert 30:** Add at position 4 (left child of 10). 30 > 10 → swap. 30 > 20 → swap.
```
Step 1: [20, 10, 15, 30] → 30 > parent 10 → swap → [20, 30, 15, 10]
Step 2: 30 > parent 20 → swap → [30, 20, 15, 10]

Tree:   30
       / \
      20  15
     /
    10
```

**Insert 25:** Add at position 5 (right child of 20). 25 > 20 → swap. 25 < 30 → stop.
```
[30, 20, 15, 10, 25] → swap 25 and 20 → [30, 25, 15, 10, 20]

Tree:   30
       / \
      25  15
     / \
    10  20
```

**Insert 5:** Add at position 6 (left child of 15). 5 < 15 → no swap.
```
Array: [30, 25, 15, 10, 20, 5]
Tree:   30
       / \
      25  15
     / \ /
    10 20 5
```

**Insert 35:** Add at position 7 (right child of 15). 35 > 15 → swap. 35 > 30 → swap.
```
Step 1: [30, 25, 15, 10, 20, 5, 35] → swap 35 and 15 → [30, 25, 35, 10, 20, 5, 15]
Step 2: swap 35 and 30 → [35, 25, 30, 10, 20, 5, 15]

Final tree:     35
              /    \
            25      30
           / \     / \
          10  20  5   15
```

**Final max-heap array: [35, 25, 30, 10, 20, 5, 15]** ✓

---

### Q18. Delete the maximum element twice from the max-heap [35, 25, 30, 10, 20, 5, 15]. Show each step.

**Answer:**

**Heap delete-max:** Replace root with last element, reduce size, then **bubble down** (sift-down / heapify) — swap with the larger child while smaller than a child.

**First delete-max (remove 35):**

Step 1: Replace root 35 with last element 15. Heap size: 6.
```
Array: [15, 25, 30, 10, 20, 5]
Tree:    15
       /    \
      25     30
     / \    /
    10  20  5
```

Step 2: Bubble down 15. Children: 25, 30. Larger child: 30. 15 < 30 → swap.
```
Array: [30, 25, 15, 10, 20, 5]
Tree:    30
       /    \
      25     15
     / \    /
    10  20  5
```

Step 3: Bubble down 15. Children: 5. 15 > 5 → stop.

**Extracted: 35. Heap: [30, 25, 15, 10, 20, 5]** ✓

**Second delete-max (remove 30):**

Step 1: Replace root 30 with last element 5. Heap size: 5.
```
Array: [5, 25, 15, 10, 20]
Tree:    5
       /   \
      25    15
     / \
    10  20
```

Step 2: Bubble down 5. Children: 25, 15. Larger: 25. 5 < 25 → swap.
```
Array: [25, 5, 15, 10, 20]
Tree:    25
       /   \
       5    15
     / \
    10  20
```

Step 3: Bubble down 5. Children: 10, 20. Larger: 20. 5 < 20 → swap.
```
Array: [25, 20, 15, 10, 5]
Tree:    25
       /   \
      20    15
     / \
    10   5
```

**Extracted: 30. Heap: [25, 20, 15, 10, 5]** ✓

---

### Q19. Perform Heap Sort on the array [4, 10, 3, 5, 1, 8, 7, 2, 9, 6]. Show each step.

**Answer:**

**Heap Sort algorithm:**
1. **Build max-heap** from the array using bottom-up heapify.
2. **Repeatedly extract max:** swap root (max) with last element, reduce heap size, heapify root.

**Phase 1: Build Max-Heap**

Array: [4, 10, 3, 5, 1, 8, 7, 2, 9, 6] (n = 10)

Start heapifying from last internal node = ⌊10/2⌋ = index 5 (value 1, 1-indexed).

Actually, let me use 0-indexed: parent = ⌊(n-2)/2⌋ = 4 (value 1).

**0-indexed array:** [4, 10, 3, 5, 1, 8, 7, 2, 9, 6]
```
Parent(i) = ⌊(i-1)/2⌋, Left(i) = 2i+1, Right(i) = 2i+2
```

**Heapify index 4 (value 1):** Children: 9 (idx 9), no right. 1 < 6 → swap 1 and 6.
```
[4, 10, 3, 5, 6, 8, 7, 2, 9, 1]
```

**Heapify index 3 (value 5):** Children: 2 (idx 7), 9 (idx 8). Largest = 9 → swap 5 and 9.
```
[4, 10, 3, 9, 6, 8, 7, 2, 5, 1]
```

**Heapify index 2 (value 3):** Children: 8 (idx 5), 7 (idx 6). Largest = 8 → swap 3 and 8.
```
[4, 10, 8, 9, 6, 3, 7, 2, 5, 1]
```

**Heapify index 1 (value 10):** Children: 9 (idx 3), 6 (idx 4). Largest = 10 → no swap.
```
[4, 10, 8, 9, 6, 3, 7, 2, 5, 1]
```

**Heapify index 0 (value 4):** Children: 10 (idx 1), 8 (idx 2). Largest = 10 → swap 4 and 10.
```
[10, 4, 8, 9, 6, 3, 7, 2, 5, 1]
```
Bubble down 4 at index 1: Children: 9 (idx 3), 6 (idx 4). Largest = 9 → swap 4 and 9.
```
[10, 9, 8, 4, 6, 3, 7, 2, 5, 1]
```
Bubble down 4 at index 3: Children: 2 (idx 7), 5 (idx 8). Largest = 5 → swap 4 and 5.
```
[10, 9, 8, 5, 6, 3, 7, 2, 4, 1]
```

**Max-heap built: [10, 9, 8, 5, 6, 3, 7, 2, 4, 1]**

**Phase 2: Extract Max Repeatedly**

| Step | Swap root with last | Heap (after heapify) | Sorted portion |
|------|-------------------|---------------------|----------------|
| 1 | swap 10↔1 | [9, 6, 8, 5, 1, 3, 7, 2, 4] | [10] |
| 2 | swap 9↔4 | [8, 6, 7, 5, 1, 3, 4, 2] | [9, 10] |
| 3 | swap 8↔2 | [7, 6, 4, 5, 1, 3, 2] | [8, 9, 10] |
| 4 | swap 7↔2 | [6, 5, 4, 2, 1, 3] | [7, 8, 9, 10] |
| 5 | swap 6↔3 | [5, 3, 4, 2, 1] | [6, 7, 8, 9, 10] |
| 6 | swap 5↔1 | [4, 3, 1, 2] | [5, 6, 7, 8, 9, 10] |
| 7 | swap 4↔2 | [3, 2, 1] | [4, 5, 6, 7, 8, 9, 10] |
| 8 | swap 3↔1 | [2, 1] | [3, 4, 5, 6, 7, 8, 9, 10] |
| 9 | swap 2↔1 | [1] | [2, 3, 4, 5, 6, 7, 8, 9, 10] |

**Sorted array: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]** ✓

**Time complexity:**
- Build heap: O(n)
- n extractions × O(log n) heapify each = O(n log n)
- **Total: O(n log n)** — in-place, not stable

---

### Q20. Compare Heap Sort with other sorting algorithms.

**Answer:**

| Property | Heap Sort | Merge Sort | Quick Sort | Insertion Sort |
|----------|----------|------------|------------|----------------|
| Worst case | O(n log n) | O(n log n) | O(n²) | O(n²) |
| Average case | O(n log n) | O(n log n) | O(n log n) | O(n²) |
| Best case | O(n log n) | O(n log n) | O(n log n) | O(n) |
| Space | O(1) in-place | O(n) | O(log n) stack | O(1) in-place |
| Stable? | No | Yes | No (typical) | Yes |
| Adaptive? | No | No | No | Yes |

**When to use Heap Sort:**
- When O(n log n) worst-case guarantee is needed AND O(1) extra space is required.
- Not preferred when stability matters (use Merge Sort) or when average-case speed matters (Quick Sort is typically faster due to better cache performance).

**BST vs Heap comparison:**

| Aspect | BST | Max-Heap |
|--------|-----|----------|
| Structure | BST property (left < root < right) | Heap property (parent ≥ children) |
| Find max | O(log n) — rightmost node | O(1) — root |
| Find min | O(log n) — leftmost node | O(n) — must search leaves |
| Search any key | O(log n) | O(n) — no search structure |
| Insert | O(log n) | O(log n) |
| Delete specific | O(log n) | O(n) — must find first |
| Sorted order | O(n) inorder traversal | O(n log n) — repeated extraction |
| Use case | Dictionary, ordered set | Priority queue |

---
---

# Section E: Graph Representations (Q21–Q23)

---

### Q21. Define a graph. Explain directed vs undirected, weighted vs unweighted, and give the adjacency matrix and adjacency list for:

```
Undirected graph:
    A ---3--- B
    |         |
    2         4
    |         |
    C ---1--- D ---5--- E
```

**Answer:**

**Definitions:**

| Term | Definition |
|------|-----------|
| **Graph G = (V, E)** | A set of vertices V and edges E connecting pairs of vertices |
| **Undirected** | Edges have no direction: (u,v) = (v,u) |
| **Directed (digraph)** | Edges have direction: (u,v) ≠ (v,u) |
| **Weighted** | Each edge has an associated numerical weight/cost |
| **Unweighted** | All edges have equal weight (or weight = 1) |
| **Degree** | Number of edges incident to a vertex (in undirected graph) |
| **In-degree / Out-degree** | Number of incoming / outgoing edges (in directed graph) |

**Adjacency Matrix (weighted):**

|   | A | B | C | D | E |
|---|---|---|---|---|---|
| **A** | 0 | 3 | 2 | ∞ | ∞ |
| **B** | 3 | 0 | ∞ | 4 | ∞ |
| **C** | 2 | ∞ | 0 | 1 | ∞ |
| **D** | ∞ | 4 | 1 | 0 | 5 |
| **E** | ∞ | ∞ | ∞ | 5 | 0 |

(∞ means no direct edge; matrix is symmetric for undirected graphs)

**Adjacency List:**

```
A → [(B, 3), (C, 2)]
B → [(A, 3), (D, 4)]
C → [(A, 2), (D, 1)]
D → [(B, 4), (C, 1), (E, 5)]
E → [(D, 5)]
```

---

### Q22. Compare adjacency matrix vs adjacency list representations.

**Answer:**

| Aspect | Adjacency Matrix | Adjacency List |
|--------|-----------------|----------------|
| Space | O(V²) | O(V + E) |
| Check if edge (u,v) exists | O(1) — direct lookup | O(deg(u)) — scan u's list |
| List all neighbours of u | O(V) — scan entire row | O(deg(u)) — scan u's list |
| Add edge | O(1) | O(1) |
| Remove edge | O(1) | O(deg(u)) |
| Better for | Dense graphs (E ≈ V²) | Sparse graphs (E << V²) |
| Memory for 1000 vertices, 5000 edges | 1,000,000 entries | ~11,000 entries (V + 2E) |

**Decision guide:**
- **Use adjacency matrix** when: the graph is dense, you need O(1) edge lookup, or V is small.
- **Use adjacency list** when: the graph is sparse (most real-world graphs), you iterate over neighbours often (BFS/DFS), or memory is a concern.

---

### Q23. Given the following directed graph, write the adjacency matrix and adjacency list. Also state the in-degree and out-degree of each vertex.

```
Directed graph:
    A → B → D
    ↓ ↗   ↓
    C ← ← E
```

Edges: A→B, A→C, B→D, C→B, D→E, E→C

**Answer:**

**Adjacency Matrix:**

|   | A | B | C | D | E |
|---|---|---|---|---|---|
| **A** | 0 | 1 | 1 | 0 | 0 |
| **B** | 0 | 0 | 0 | 1 | 0 |
| **C** | 0 | 1 | 0 | 0 | 0 |
| **D** | 0 | 0 | 0 | 0 | 1 |
| **E** | 0 | 0 | 1 | 0 | 0 |

(NOT symmetric — this is a directed graph)

**Adjacency List:**

```
A → [B, C]
B → [D]
C → [B]
D → [E]
E → [C]
```

**Degree table:**

| Vertex | In-degree | Out-degree | Total degree |
|--------|-----------|------------|-------------|
| A | 0 | 2 | 2 |
| B | 2 (from A, C) | 1 | 3 |
| C | 2 (from A, E) | 1 | 3 |
| D | 1 (from B) | 1 | 2 |
| E | 1 (from D) | 1 | 2 |

**Sum of in-degrees = Sum of out-degrees = |E| = 6** ✓

---
---

# Section F: BFS & DFS Traversals (Q24–Q27)

---

### Q24. Perform BFS on the following graph starting from vertex A. Show the queue state and discovery order.

```
        A
       / \
      B   C
     / \   \
    D   E   F
        |   |
        G   H
```

Edges: A-B, A-C, B-D, B-E, C-F, E-G, F-H

**Answer:**

**BFS uses a queue (FIFO) and visits vertices level by level.**

| Step | Dequeue | Process | Enqueue unvisited neighbours | Queue | Visited |
|------|---------|---------|------------------------------|-------|---------|
| 0 | — | — | A | [A] | {A} |
| 1 | A | visit A | B, C | [B, C] | {A, B, C} |
| 2 | B | visit B | D, E | [C, D, E] | {A, B, C, D, E} |
| 3 | C | visit C | F | [D, E, F] | {A, B, C, D, E, F} |
| 4 | D | visit D | (none new) | [E, F] | {A, B, C, D, E, F} |
| 5 | E | visit E | G | [F, G] | {A, B, C, D, E, F, G} |
| 6 | F | visit F | H | [G, H] | {A, B, C, D, E, F, G, H} |
| 7 | G | visit G | (none new) | [H] | {A, B, C, D, E, F, G, H} |
| 8 | H | visit H | (none new) | [] | {A, B, C, D, E, F, G, H} |

**BFS order: A → B → C → D → E → F → G → H**

**BFS tree edges:** A-B, A-C, B-D, B-E, C-F, E-G, F-H

**Properties of BFS:**
- Visits vertices in order of distance from source
- Finds shortest path (in unweighted graphs)
- Time: O(V + E) with adjacency list

---

### Q25. Perform DFS on the same graph starting from vertex A. Show the stack/recursion state and discovery order.

```
        A
       / \
      B   C
     / \   \
    D   E   F
        |   |
        G   H
```

**Answer:**

**DFS uses a stack (or recursion) and goes as deep as possible before backtracking.**

**Recursive DFS trace (visiting neighbours in alphabetical order):**

```
DFS(A):
  Visit A. Recurse on unvisited neighbours: B, C.
    DFS(B):
      Visit B. Recurse on unvisited: D, E.
        DFS(D):
          Visit D. No unvisited neighbours. Backtrack.
        DFS(E):
          Visit E. Recurse on unvisited: G.
            DFS(G):
              Visit G. No unvisited neighbours. Backtrack.
          Backtrack from E.
      Backtrack from B.
    DFS(C):
      Visit C. Recurse on unvisited: F.
        DFS(F):
          Visit F. Recurse on unvisited: H.
            DFS(H):
              Visit H. No unvisited neighbours. Backtrack.
          Backtrack from F.
      Backtrack from C.
  Done.
```

**DFS order: A → B → D → E → G → C → F → H**

**DFS tree edges:** A-B, B-D, B-E, E-G, A-C, C-F, F-H

| Step | Current | Stack (call stack) | Visited |
|------|---------|-------------------|---------|
| 1 | A | [A] | {A} |
| 2 | B | [A, B] | {A, B} |
| 3 | D | [A, B, D] | {A, B, D} |
| 4 | backtrack | [A, B] | {A, B, D} |
| 5 | E | [A, B, E] | {A, B, D, E} |
| 6 | G | [A, B, E, G] | {A, B, D, E, G} |
| 7 | backtrack×3 | [A] | {A, B, D, E, G} |
| 8 | C | [A, C] | {A, B, C, D, E, G} |
| 9 | F | [A, C, F] | {A, B, C, D, E, F, G} |
| 10 | H | [A, C, F, H] | {A, B, C, D, E, F, G, H} |

---

### Q26. Compare BFS and DFS.

**Answer:**

| Aspect | BFS | DFS |
|--------|-----|-----|
| Data structure | Queue (FIFO) | Stack (LIFO) / Recursion |
| Strategy | Level by level (breadth-first) | Go deep, then backtrack (depth-first) |
| Time complexity | O(V + E) | O(V + E) |
| Space complexity | O(V) — queue can hold entire level | O(V) — stack depth = longest path |
| Shortest path? | Yes (unweighted graphs) | No guarantee |
| Complete? | Yes (finds goal if it exists) | Yes (in finite graphs) |
| When to use | Shortest path, level-order, nearby nodes | Cycle detection, topological sort, connected components, path existence |
| Memory in wide graphs | High (stores all nodes at a level) | Low (only stores current path) |
| Memory in deep graphs | Low | High (deep recursion stack) |

**Example where they differ:**
```
Graph:  1 — 2 — 3 — 4
        |
        5 — 6

Finding path from 1 to 4:
  BFS: 1 → 2 → 5 → 3 → 6 → 4  (finds shortest: 1-2-3-4, length 3)
  DFS: 1 → 2 → 3 → 4           (may find it directly, but not guaranteed shortest)
```

---

### Q27. Perform BFS and DFS on the following directed graph from vertex 0. Also identify any back edges (DFS).

```
0 → 1 → 3
↓ ↗     ↓
2       4
```

Edges: 0→1, 0→2, 1→3, 2→1, 3→4

**Answer:**

**BFS from vertex 0:**

| Step | Dequeue | Enqueue | Queue | Visited |
|------|---------|---------|-------|---------|
| 0 | — | 0 | [0] | {0} |
| 1 | 0 | 1, 2 | [1, 2] | {0, 1, 2} |
| 2 | 1 | 3 | [2, 3] | {0, 1, 2, 3} |
| 3 | 2 | (1 visited) | [3] | {0, 1, 2, 3} |
| 4 | 3 | 4 | [4] | {0, 1, 2, 3, 4} |
| 5 | 4 | (none) | [] | {0, 1, 2, 3, 4} |

**BFS order: 0 → 1 → 2 → 3 → 4**

**DFS from vertex 0:**

```
DFS(0): Visit 0. Neighbours: 1, 2.
  DFS(1): Visit 1. Neighbours: 3.
    DFS(3): Visit 3. Neighbours: 4.
      DFS(4): Visit 4. No unvisited neighbours. Backtrack.
    Backtrack from 3.
  Backtrack from 1.
  DFS(2): Visit 2. Neighbours: 1 (already visited → this edge 2→1 is a cross edge).
  Backtrack from 2.
Done.
```

**DFS order: 0 → 1 → 3 → 4 → 2**

**Edge classification (DFS):**
- **Tree edges:** 0→1, 1→3, 3→4, 0→2
- **Cross edge:** 2→1 (1 is already fully processed, not an ancestor)
- **No back edges** → the graph has **no cycles** ✓

(A back edge in DFS indicates a cycle in a directed graph.)

---
---

# Section G: Floyd-Warshall & Comparisons (Q28–Q30)

---

### Q28. Apply the Floyd-Warshall algorithm to find all-pairs shortest paths for:

```
Directed weighted graph:
    1 →(3)→ 2
    ↑       ↓
   (7)     (1)
    ↑       ↓
    4 ←(2)← 3
    
    Also: 1→4 weight 8, 2→4 weight ∞, 3→1 weight ∞, 4→2 weight ∞
```

Edge list: (1→2, w=3), (2→3, w=1), (3→4, w=2), (4→1, w=7)

**Answer:**

**Floyd-Warshall finds shortest paths between ALL pairs of vertices.**

**Algorithm:**
```
for k = 1 to V:
    for i = 1 to V:
        for j = 1 to V:
            dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j])
```

**Initial distance matrix D⁰ (direct edges):**

|   | 1 | 2 | 3 | 4 |
|---|---|---|---|---|
| **1** | 0 | 3 | ∞ | ∞ |
| **2** | ∞ | 0 | 1 | ∞ |
| **3** | ∞ | ∞ | 0 | 2 |
| **4** | 7 | ∞ | ∞ | 0 |

**k = 1 (allow paths through vertex 1):**
Check if going through vertex 1 improves any path.
- D[4][2] = min(∞, D[4][1] + D[1][2]) = min(∞, 7+3) = **10**
- D[4][3]: min(∞, D[4][1]+D[1][3]) = min(∞, 7+∞) = ∞ (no improvement)

D¹:

|   | 1 | 2 | 3 | 4 |
|---|---|---|---|---|
| **1** | 0 | 3 | ∞ | ∞ |
| **2** | ∞ | 0 | 1 | ∞ |
| **3** | ∞ | ∞ | 0 | 2 |
| **4** | 7 | **10** | ∞ | 0 |

**k = 2 (allow paths through vertices 1, 2):**
- D[1][3] = min(∞, D[1][2] + D[2][3]) = min(∞, 3+1) = **4**
- D[4][3] = min(∞, D[4][2] + D[2][3]) = min(∞, 10+1) = **11**

D²:

|   | 1 | 2 | 3 | 4 |
|---|---|---|---|---|
| **1** | 0 | 3 | **4** | ∞ |
| **2** | ∞ | 0 | 1 | ∞ |
| **3** | ∞ | ∞ | 0 | 2 |
| **4** | 7 | 10 | **11** | 0 |

**k = 3 (allow paths through vertices 1, 2, 3):**
- D[1][4] = min(∞, D[1][3] + D[3][4]) = min(∞, 4+2) = **6**
- D[2][4] = min(∞, D[2][3] + D[3][4]) = min(∞, 1+2) = **3**
- D[4][4] = min(0, D[4][3] + D[3][4]) = min(0, 11+2) = 0 (no change)

D³:

|   | 1 | 2 | 3 | 4 |
|---|---|---|---|---|
| **1** | 0 | 3 | 4 | **6** |
| **2** | ∞ | 0 | 1 | **3** |
| **3** | ∞ | ∞ | 0 | 2 |
| **4** | 7 | 10 | 11 | 0 |

**k = 4 (allow paths through all vertices):**
- D[2][1] = min(∞, D[2][4] + D[4][1]) = min(∞, 3+7) = **10**
- D[3][1] = min(∞, D[3][4] + D[4][1]) = min(∞, 2+7) = **9**
- D[3][2] = min(∞, D[3][4] + D[4][2]) = min(∞, 2+10) = **12**
- D[1][1] = min(0, D[1][4] + D[4][1]) = min(0, 6+7) = 0 (no change — no negative cycle)

**Final D⁴ (all-pairs shortest paths):**

|   | 1 | 2 | 3 | 4 |
|---|---|---|---|---|
| **1** | 0 | 3 | 4 | 6 |
| **2** | 10 | 0 | 1 | 3 |
| **3** | 9 | 12 | 0 | 2 |
| **4** | 7 | 10 | 11 | 0 |

**Verification:** Shortest 1→4: 1→2→3→4 with cost 3+1+2 = 6 ✓
Shortest 4→2: 4→1→2 with cost 7+3 = 10 ✓

**Time: O(V³). Space: O(V²).**

---

### Q29. Compare Floyd-Warshall with Dijkstra's algorithm.

**Answer:**

| Aspect | Floyd-Warshall | Dijkstra's |
|--------|---------------|------------|
| **Problem** | All-pairs shortest paths | Single-source shortest paths |
| **Algorithm type** | Dynamic programming | Greedy |
| **Negative weights** | Handles negative edges (detects negative cycles) | Cannot handle negative weights |
| **Time complexity** | O(V³) | O((V + E) log V) with min-heap |
| **Space** | O(V²) | O(V + E) |
| **For all-pairs** | O(V³) — single run | Run V times: O(V(V+E) log V) |
| **Better when** | Dense graphs, negative weights, or all-pairs needed | Sparse graphs, single source, non-negative weights |
| **Implementation** | Simple triple loop | More complex (priority queue) |

**When to use which:**
- Use **Floyd-Warshall** when you need shortest paths between ALL pairs and V is small (≤ ~500) or the graph has negative edges.
- Use **Dijkstra** (run V times) when the graph is sparse (E << V²) and weights are non-negative. Total: O(V · (V+E) log V) which can be better than O(V³) for sparse graphs.

---

### Q30. Comprehensive comparison: BST vs Heap, BFS vs DFS, Adjacency Matrix vs Adjacency List.

**Answer:**

**BST vs Heap:**

| Aspect | BST (balanced) | Max-Heap |
|--------|---------------|----------|
| **Ordering** | Left < Root < Right (total order) | Parent ≥ Children (partial order) |
| **Purpose** | Searching, ordered operations | Priority queue (get max/min fast) |
| **Find max** | O(log n) — go right | O(1) — root |
| **Find any key** | O(log n) | O(n) |
| **Insert** | O(log n) | O(log n) |
| **Delete max** | O(log n) | O(log n) |
| **Delete any** | O(log n) | O(n) find + O(log n) fix |
| **Sorted output** | O(n) inorder | O(n log n) repeated extract |
| **Shape** | Depends on insertion order (can be skewed) | Always complete binary tree |
| **Array storage** | Awkward (may have gaps) | Natural (level-by-level) |

**BFS vs DFS (repeated for reference):**

| Aspect | BFS | DFS |
|--------|-----|-----|
| **Data structure** | Queue | Stack / Recursion |
| **Order** | Level-by-level | Deep as possible first |
| **Shortest path (unweighted)** | ✓ Yes | ✗ No guarantee |
| **Cycle detection** | Can detect | Natural (back edges) |
| **Topological sort** | Kahn's algorithm (BFS-based) | Reverse postorder (DFS-based) |
| **Memory** | O(branching factor^d) | O(depth × branching factor) |
| **Connected components** | ✓ | ✓ |

**Adjacency Matrix vs Adjacency List:**

| Aspect | Matrix | List |
|--------|--------|------|
| **Space** | O(V²) always | O(V + E) |
| **Edge lookup** | O(1) | O(deg(v)) |
| **Iterate neighbours** | O(V) | O(deg(v)) |
| **Add edge** | O(1) | O(1) |
| **Best for** | Dense graphs, frequent edge queries | Sparse graphs, BFS/DFS traversals |
| **Real-world graphs** | Rarely used (most are sparse) | Standard choice |

---

*End of Chapter 3 Questions and Answers*

*Q&A prepared for Chapter 3: Non-Linear Data Structures — BITS Pilani*
*References: Goodrich & Tamassia (2006) Ch 7, 8, 9; CLRS (2009) Ch 6, 12, 22, 25*
