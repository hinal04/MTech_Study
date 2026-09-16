# DSA — Exam Cheatsheet (Quick Reference Before Exam)

> Scan this 30 minutes before the exam. All decisions, formulas, complexities in one place.

---

## 1. WHEN TO PICK WHAT — Data Structure Selection

| Scenario | Pick | Why |
|---|---|---|
| Need LIFO (last in, first out) — undo, backtracking, DFS | **Stack** | Push/Pop O(1). Last added = first removed. Function call stack, parenthesis matching, expression evaluation, browser back button. |
| Need FIFO (first in, first out) — scheduling, BFS | **Queue** | Enqueue/Dequeue O(1). First added = first removed. OS process scheduling, BFS traversal, print spooling, producer-consumer buffer. |
| Need insert/delete at both ends | **Deque** | O(1) at both ends. Sliding window max, 0-1 BFS, palindrome check. |
| Need fast search + sorted order | **BST (Balanced)** | Search/Insert/Delete O(log n). Inorder gives sorted output. Dictionaries, ordered sets, range queries. |
| Need fast max/min extraction | **Heap (Priority Queue)** | Insert O(log n), Extract-max O(log n), Find-max O(1). OS scheduling, Dijkstra's, Huffman encoding, heap sort. |
| Need fast insert/delete at arbitrary positions, no random access | **Linked List** | Insert/Delete O(1) at known position. Dynamic size. LRU cache (doubly linked + hashmap), polynomial representation. |
| Need fast random access by index | **Array** | Access O(1) by index. Cache-friendly (contiguous memory). Binary search on sorted array. When size is known/predictable. |
| Need key-value lookup | **Hash Table** | Insert/Search/Delete O(1) average. Dictionaries, caches, counting frequencies, detecting duplicates. |
| Need to represent hierarchical data | **Tree** | File systems, HTML DOM, org charts, parse trees. BST for search, heap for priority, trie for prefix matching. |
| Need to represent connections/relationships | **Graph** | Social networks, maps, dependencies, web pages. BFS for shortest path (unweighted), DFS for cycle detection. |
| Need sorted data + frequent insert/delete | **Balanced BST (AVL/Red-Black)** | All ops O(log n) guaranteed. Never degrades to O(n) like plain BST. Databases use B-trees (balanced tree variant). |

---

## 2. WHEN TO PICK WHAT — Algorithm Selection

| Problem | Pick | Why |
|---|---|---|
| Find shortest path in unweighted graph | **BFS** | Queue-based, visits level by level. Guarantees shortest path. O(V+E). |
| Detect cycle in graph | **DFS** | Back edge in DFS tree = cycle. O(V+E). |
| Topological ordering (course prerequisites) | **DFS** (reverse postorder) or **Kahn's BFS** | DAG only. DFS: reverse finish order. BFS: repeatedly remove 0-indegree nodes. |
| Find shortest path in weighted graph (no negative) | **Dijkstra's** | Greedy + priority queue. O((V+E) log V). |
| Find shortest path with negative weights | **Bellman-Ford** | Relaxes all edges V-1 times. O(VE). Detects negative cycles. |
| Find ALL-pairs shortest paths | **Floyd-Warshall** | DP, O(V³). Works with negative weights (no negative cycles). |
| Minimum Spanning Tree | **Kruskal's** (sort edges) or **Prim's** (grow tree) | Kruskal: sort edges + union-find. Prim: priority queue from a start vertex. Both O(E log V). |
| Sort an array (general purpose) | **Merge Sort** (stable, O(n log n) guaranteed) or **Quick Sort** (fast average) | Merge Sort: stable, predictable. Quick Sort: faster in practice (cache-friendly) but O(n²) worst case. |
| Sort + in-place + O(n log n) guaranteed | **Heap Sort** | In-place, O(n log n) worst case. Not stable. Use when memory matters. |
| Search in sorted array | **Binary Search** | O(log n). Halves search space each step. |
| Solve recurrence T(n)=aT(n/b)+f(n) | **Master Theorem** | Compare f(n) with n^(log_b a). Case 1/2/3. Instant answer for standard forms. |

---

## 3. MASTER THEOREM — Quick Decision

```
T(n) = aT(n/b) + f(n)

Step 1: Compute n^(log_b a)
Step 2: Compare f(n) with n^(log_b a)

Case 1: f(n) < n^(log_b a)  → T(n) = Θ(n^(log_b a))         [recursion dominates]
Case 2: f(n) = n^(log_b a)  → T(n) = Θ(n^(log_b a) · log n)  [tie → multiply by log n]
Case 3: f(n) > n^(log_b a)  → T(n) = Θ(f(n))                  [work dominates]
```

**Key log values to memorize:**

| log₂2=1 | log₂4=2 | log₂8=3 | log₂16=4 |
|---|---|---|---|
| **log₃9=2** | **log₃27=3** | **log₅25=2** | **log₁₀1000=3** |

**Common recurrences:**

| Recurrence | a | b | n^(log_b a) | Case | Answer |
|---|---|---|---|---|---|
| T(n)=T(n/2)+1 | 1 | 2 | n⁰=1 | 2 | **Θ(log n)** — Binary Search |
| T(n)=2T(n/2)+n | 2 | 2 | n¹ | 2 | **Θ(n log n)** — Merge Sort |
| T(n)=4T(n/2)+n | 4 | 2 | n² | 1 | **Θ(n²)** |
| T(n)=4T(n/2)+n² | 4 | 2 | n² | 2 | **Θ(n² log n)** |
| T(n)=4T(n/2)+n³ | 4 | 2 | n² | 3 | **Θ(n³)** |
| T(n)=7T(n/2)+n² | 7 | 2 | n^2.807 | 1 | **Θ(n^2.807)** — Strassen |
| T(n)=9T(n/3)+n² | 9 | 3 | n² | 2 | **Θ(n² log n)** |

---

## 4. COMPLEXITY TABLE — All Data Structures

| Operation | Array | Linked List | BST (balanced) | BST (worst) | Heap | Hash Table |
|---|---|---|---|---|---|---|
| **Access by index** | **O(1)** | O(n) | O(n) | O(n) | O(n) | N/A |
| **Search** | O(n) | O(n) | **O(log n)** | O(n) | O(n) | **O(1)** avg |
| **Insert (beginning)** | O(n) | **O(1)** | O(log n) | O(n) | O(log n) | O(1) avg |
| **Insert (end)** | **O(1)** amort | O(n)* | O(log n) | O(n) | O(log n) | O(1) avg |
| **Delete** | O(n) | **O(1)** given ptr | O(log n) | O(n) | O(log n) | O(1) avg |
| **Find min/max** | O(n) | O(n) | O(log n) | O(n) | **O(1)** | O(n) |
| **Sorted output** | O(n log n) | O(n log n) | **O(n)** inorder | O(n) | O(n log n) | N/A |

*O(1) with tail pointer

## 5. SORTING ALGORITHMS — Comparison

| Algorithm | Best | Average | Worst | Space | Stable? | In-place? | When to Pick |
|---|---|---|---|---|---|---|---|
| **Bubble Sort** | O(n) | O(n²) | O(n²) | O(1) | Yes | Yes | Never in practice (educational only) |
| **Insertion Sort** | **O(n)** | O(n²) | O(n²) | O(1) | Yes | Yes | Small arrays, nearly sorted data |
| **Merge Sort** | O(n log n) | O(n log n) | O(n log n) | **O(n)** | **Yes** | No | Need stable sort, guaranteed O(n log n) |
| **Quick Sort** | O(n log n) | **O(n log n)** | O(n²) | O(log n) | No | Yes | Default choice (fastest in practice) |
| **Heap Sort** | O(n log n) | O(n log n) | O(n log n) | **O(1)** | No | **Yes** | Need in-place + guaranteed O(n log n) |

---

## 6. TRAVERSALS — Quick Reference

### Tree Traversals

| Traversal | Order | Mnemonic | Gives | Use For |
|---|---|---|---|---|
| **Preorder** | Root → Left → Right | **NLR** | Prefix expression | Copy tree, serialize |
| **Inorder** | Left → Root → Right | **LNR** | **Sorted order (BST)** | Print BST sorted |
| **Postorder** | Left → Right → Root | **LRN** | Postfix expression | Delete tree, evaluate expression |
| **Level-order** | Level by level (BFS) | Queue-based | Breadth-first | Shortest depth, level printing |

### Graph Traversals

| | BFS | DFS |
|---|---|---|
| **Data structure** | Queue | Stack / Recursion |
| **Strategy** | Level by level (breadth) | Go deep, backtrack (depth) |
| **Time** | O(V+E) | O(V+E) |
| **Shortest path (unweighted)** | ✅ Yes | ❌ No |
| **Cycle detection** | Via visited check | Via **back edges** |
| **Topological sort** | Kahn's algorithm | Reverse postorder |
| **Pick when** | Shortest path, level-order | Cycle detection, topological sort, path existence |

---

## 7. EXPRESSION CONVERSION — Quick Steps

| Conversion | Method |
|---|---|
| **Infix → Postfix** | Shunting-yard: scan L→R, operand→output, operator→stack (pop higher/equal precedence), `(`→push, `)`→pop until `(` |
| **Infix → Prefix** | Reverse → swap `(`↔`)` → apply infix-to-postfix → reverse result |
| **Postfix evaluation** | Scan L→R, push operands; on operator: pop 2 (op2 first!), compute, push result |
| **Prefix evaluation** | Reverse → scan L→R, push operands; on operator: pop 2 (op1 first!), compute, push |

**Operator precedence:** `^` (3, right) > `* /` (2, left) > `+ -` (1, left)

---

## 8. HEAP — Quick Operations

```
Array (1-indexed): Parent = ⌊i/2⌋, Left = 2i, Right = 2i+1

INSERT:  Add at end → Bubble UP (swap with parent while larger)     → O(log n)
DELETE-MAX: Swap root with last → Remove last → Bubble DOWN          → O(log n)
BUILD-HEAP: Bottom-up heapify from ⌊n/2⌋ down to 1                  → O(n)
HEAP SORT: Build max-heap + n extract-max operations                 → O(n log n)
```

---

## 9. AMORTIZED ANALYSIS — Quick Formulas

| Scenario | Key Insight | Amortized Cost |
|---|---|---|
| **MULTIPOP stack** | Total pops ≤ total pushes across all operations | **O(1)** per operation |
| **Dynamic array (doubling)** | Copy costs: 1+2+4+...+n < 2n (geometric series) | **O(1)** per insert |
| **Two-stack queue** | Each element moves at most 4 times in its lifetime | **O(1)** per operation |

**Why doubling → O(1) but constant-add → O(n):**
Doubling: geometric series → sum < 2n → O(1) amortized
Adding constant k: arithmetic series → sum = O(n²/k) → O(n) amortized

---

## 10. KEY FORMULAS

| Formula | Value |
|---|---|
| Max nodes at level i of binary tree | **2ⁱ** |
| Max nodes in binary tree of height h | **2^(h+1) − 1** |
| Min height of binary tree with n nodes | **⌊log₂ n⌋** |
| Leaves = Internal nodes + 1 (full BT) | **L = I + 1** |
| Edges in tree with n nodes | **n − 1** |
| Handshaking lemma (graph) | **Σ degree = 2|E|** |
| Max edges undirected simple graph | **n(n-1)/2** |
| Max edges directed simple graph | **n(n-1)** |
| Floyd-Warshall | **dist[i][j] = min(dist[i][j], dist[i][k]+dist[k][j])** |
| Circular queue advance | **(index + 1) % capacity** |
| Geometric series sum | **1 + r + r² + ... = 1/(1-r)** for |r| < 1 |
| Arithmetic series sum | **1+2+...+n = n(n+1)/2** |

---

## 11. GROWTH RATE ORDERING

```
1  <  log n  <  √n  <  n  <  n log n  <  n²  <  n³  <  2ⁿ  <  n!
```

**Asymptotic notation:**
- **O(f)** = upper bound (≤), "at most"
- **Ω(f)** = lower bound (≥), "at least"
- **Θ(f)** = tight bound (=), "exactly"

---

*Good luck with your exam!* 🎯
