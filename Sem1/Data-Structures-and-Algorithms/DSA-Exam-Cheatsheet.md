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

Case 1: f(n) < n^(log_b a)           → T(n) = Θ(n^(log_b a))              [recursion dominates]
Case 2: f(n) = n^(log_b a)           → T(n) = Θ(n^(log_b a) · log n)      [tie → multiply by log n]
Case 2 EXTENDED: f(n) = n^(log_b a) · (log n)^k
                                      → T(n) = Θ(n^(log_b a) · (log n)^(k+1)) [add 1 to log exponent]
Case 3: f(n) > n^(log_b a)           → T(n) = Θ(f(n))                      [work dominates]
```

**Extended Case 2 — Quick Examples:**

| Recurrence | n^(log_b a) | f(n) | k | Answer |
|---|---|---|---|---|
| T(n)=2T(n/2)+n log n | n | n·log n | 1 | **Θ(n log² n)** |
| T(n)=4T(n/2)+n² log n | n² | n²·log n | 1 | **Θ(n² log² n)** |
| T(n)=T(n/2)+log²n | 1 | log²n | 2 | **Θ(log³ n)** |
| T(n)=2T(n/2)+n log²n | n | n·log²n | 2 | **Θ(n log³ n)** |

> **When to use:** f(n) matches n^(log_b a) but has an extra (log n)^k factor. Don't say "doesn't apply" — use Extended Case 2!

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
| T(n)=2T(n/2)+n log n | 2 | 2 | n | 2-ext (k=1) | **Θ(n log² n)** |
| T(n)=4T(n/2)+n² log n | 4 | 2 | n² | 2-ext (k=1) | **Θ(n² log² n)** |
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

## 12. ALGORITHM FUNDAMENTALS

**Definition:** An algorithm is a finite set of well-defined instructions to solve a problem.

**5 Characteristics:**

| Characteristic | Meaning |
|---|---|
| **Input** | Zero or more quantities externally supplied |
| **Output** | At least one quantity is produced |
| **Definiteness** | Each instruction is clear and unambiguous |
| **Finiteness** | Algorithm terminates after a finite number of steps |
| **Effectiveness** | Every instruction is basic enough to be carried out |

> 🧠 Memory trick: **"I O D F E — I Only Do Fine Exams"**

**RAM Model (Random Access Machine):**
- Single processor, instructions execute one after another
- Each simple operation (add, compare, assign) takes **constant time**
- Flat memory — accessing any memory location costs the same

**3 Cases of Analysis:**

| Case | Meaning | When Used |
|---|---|---|
| **Best Case** | Minimum time (most favorable input) | Rarely useful — too optimistic |
| **Average Case** | Expected time over all inputs | Useful but needs probability assumptions |
| **Worst Case** | Maximum time (least favorable input) | **Most commonly used** — gives guarantee |

**Why worst case?** Gives an **upper bound guarantee** — no input can cause worse performance. No probability assumptions needed. Safe for critical systems.

---

## 13. STACK — Complete Details

**Definition:** LIFO (Last In First Out) — the element added last is removed first.

**Operations — all O(1):**

| Operation | Description | Time |
|---|---|---|
| `push(x)` | Add x to top | O(1) |
| `pop()` | Remove and return top | O(1) |
| `peek()`/`top()` | Return top without removing | O(1) |
| `isEmpty()` | Check if stack is empty | O(1) |

**Advantages:**
- Simple to implement
- All operations O(1)
- Natural for nested/recursive structures

**Disadvantages:**
- No random access (can't reach middle element)
- Fixed size in array-based implementation — can overflow
- Must pop everything above to access an element below

**Applications:**
- 🔙 Browser back button
- ↩️ Undo mechanism (Ctrl+Z)
- 📞 Function call stack (recursion)
- 🔢 Expression evaluation (postfix/prefix)
- 🔄 Parenthesis matching
- 🌲 DFS traversal
- 🔀 Backtracking (maze solving, N-queens)

**Array vs Linked List Implementation:**

| | Array-based | Linked List-based |
|---|---|---|
| **Size** | Fixed (can overflow) | Dynamic (grows as needed) |
| **Memory** | Contiguous, cache-friendly | Extra pointer per node |
| **Overflow** | Yes (when full) | Only when memory exhausted |
| **Pick when** | Size known, performance critical | Size unknown, frequent resize |

> 🧠 Memory trick: **"Stack = Stack of plates — add/remove from TOP only"**

> ⚠️ Common exam trap: `pop()` on empty stack = **underflow** (not overflow!)

---

## 14. QUEUE — Complete Details

**Definition:** FIFO (First In First Out) — the element added first is removed first.

**Operations — all O(1):**

| Operation | Description | Time |
|---|---|---|
| `enqueue(x)` | Add x to rear | O(1) |
| `dequeue()` | Remove and return front | O(1) |
| `front()` | Return front without removing | O(1) |

**Types of Queues:**

| Type | Description |
|---|---|
| **Linear Queue** | Simple FIFO, front and rear pointers |
| **Circular Queue** | Wraps around — solves phantom overflow |
| **Deque** | Insert/delete at both ends |
| **Priority Queue** | Elements dequeued by priority, not arrival order |

**Linear Queue Problem — Phantom Overflow:**
Front moves right as we dequeue → space on the left is wasted → queue appears "full" even though there's space.
**Solution: Circular Queue** — wraps around to reuse freed space.

**Circular Queue Formulas:**
```
rear  = (rear + 1) % capacity
front = (front + 1) % capacity
isEmpty: front == rear
isFull:  (rear + 1) % capacity == front
```

**Priority Queue — Implementation Comparison:**

| Implementation | Insert | Delete-max/min | Find-max/min |
|---|---|---|---|
| **Unsorted Array** | **O(1)** | O(n) | O(n) |
| **Sorted Array** | O(n) | **O(1)** | **O(1)** |
| **Binary Heap** | O(log n) | O(log n) | **O(1)** |

> Heap is the best overall — balanced insert AND delete.

**Applications:**
- 🌐 BFS traversal
- 💻 OS process scheduling (round-robin)
- 🖨️ Print spooling
- 🏭 Producer-consumer buffer
- 📡 Packet scheduling in networks

> 🧠 Memory trick: **"Queue = Movie ticket line — first person in line gets served first"**

---

## 15. LINKED LIST — Types Comparison

| Type | Traverse Forward | Traverse Backward | Insert Head | Delete Any (given ptr) | Circular |
|---|---|---|---|---|---|
| **Singly** | ✅ O(n) | ❌ No | ✅ O(1) | ❌ O(n) need predecessor | ❌ No |
| **Doubly** | ✅ O(n) | ✅ O(n) | ✅ O(1) | ✅ **O(1)** | ❌ No |
| **Circular Singly** | ✅ O(n) | ❌ No | ✅ O(1) | ❌ O(n) need predecessor | ✅ Yes |
| **Circular Doubly** | ✅ O(n) | ✅ O(n) | ✅ O(1) | ✅ **O(1)** | ✅ Yes |

**Singly Linked List:**
- Node = `[data | next]`
- O(1) insert at head, O(n) delete (need predecessor to relink)
- Simple, low memory overhead

**Doubly Linked List:**
- Node = `[prev | data | next]`
- **O(1) delete given pointer** (can access predecessor directly)
- Used in **LRU Cache** (doubly linked list + hashmap)

**Circular Linked List:**
- Last node points back to head (last→head)
- Used in **round-robin scheduling**, **Josephus problem**
- No NULL at end — traverse indefinitely

**When to Pick: Array vs Linked List**

| | Array | Linked List |
|---|---|---|
| **Random access** | ✅ O(1) | ❌ O(n) |
| **Insert/Delete (middle)** | ❌ O(n) shift | ✅ O(1) given pointer |
| **Memory** | Contiguous, cache-friendly | Scattered, extra pointer overhead |
| **Size** | Fixed (or costly resize) | Dynamic |
| **Pick when** | Frequent access by index | Frequent insert/delete, unknown size |

---

## 16. BINARY TREE — Types & Properties

| Type | Definition | Property |
|---|---|---|
| **Full** | Every node has 0 or 2 children | No node has exactly 1 child |
| **Complete** | All levels full except last; last level filled **left to right** | Used for heap storage in array |
| **Perfect** | All internal nodes have 2 children AND all leaves at same level | Nodes = 2^(h+1) − 1 |
| **Balanced** | Height difference between left and right subtrees **≤ 1** | AVL tree guarantees this |
| **Degenerate** | Every internal node has exactly 1 child | Essentially a **linked list** — O(n) everything |

**Key Properties:**

| Property | Formula |
|---|---|
| Max nodes at level i | **2^i** |
| Max nodes in tree of height h | **2^(h+1) − 1** |
| Min height with n nodes | **⌊log₂ n⌋** |
| Leaves = Internal + 1 (full tree) | **L = I + 1** |

**BST (Binary Search Tree):**
- Rule: **left < root < right** (for all nodes, not just immediate children)
- Search / Insert / Delete: **O(h)** where h = height
  - Worst case (skewed): **O(n)** — degenerates to linked list
  - Best case (balanced): **O(log n)**

**BST Delete — 3 Cases:**

| Case | Action |
|---|---|
| **Leaf node** | Simply remove it |
| **One child** | Replace node with its child |
| **Two children** | Replace with **inorder successor** (smallest in right subtree) or inorder predecessor |

> 🧠 Memory trick: **"In BST, Inorder traversal = Sorted order. Always."**

---

## 17. HEAP — Complete Details

**Max-Heap:** Parent ≥ children. Root = **maximum**.
**Min-Heap:** Parent ≤ children. Root = **minimum**.

> A heap is ALWAYS a **complete binary tree** — this is why it maps perfectly to an array.

**Array Representation (1-indexed):**

| Relationship | Formula |
|---|---|
| Parent of i | **⌊i/2⌋** |
| Left child of i | **2i** |
| Right child of i | **2i + 1** |

**Operations:**

| Operation | How | Time |
|---|---|---|
| **Insert** | Add at end → **bubble UP** (swap with parent while violating heap property) | O(log n) |
| **Delete-max** | Swap root with last → remove last → **bubble DOWN** (swap with larger child) | O(log n) |
| **Build-heap** | Bottom-up heapify from ⌊n/2⌋ down to root | **O(n)** ← NOT O(n log n)! |
| **Find-max** | Return root | **O(1)** |

> ⚠️ Build-heap is **O(n)**, not O(n log n). Most nodes are near the bottom and bubble down very little.

**Heap Sort:**
1. Build max-heap → O(n)
2. Repeatedly extract max (swap with last, heapify root) → n × O(log n)
3. **Total: O(n log n)**. In-place. **NOT stable.**

**BST vs Heap — When to Pick:**

| Need | Pick |
|---|---|
| Sorted order traversal | **BST** — inorder gives sorted O(n) |
| Fast max/min extraction | **Heap** — O(1) find, O(log n) extract |
| Search for arbitrary element | **BST** — O(log n) balanced |
| Priority queue | **Heap** — designed for this |

---

## 18. GRAPH — Key Properties

**Handshaking Lemma:** Sum of all vertex degrees = **2|E|**
(Every edge contributes to the degree of 2 vertices)

**Maximum Edges:**

| Graph Type | Max Edges |
|---|---|
| Undirected simple graph | **n(n−1)/2** |
| Directed simple graph | **n(n−1)** |

**Key Definitions:**

| Term | Meaning |
|---|---|
| **Connected** | Path exists between every pair of vertices (undirected) |
| **Strongly connected** | Path exists in both directions between every pair (directed) |
| **Tree** | Connected + no cycles + exactly **n−1 edges** |
| **DAG** | Directed Acyclic Graph — has topological ordering |

**Representation Comparison:**

| | Adjacency Matrix | Adjacency List |
|---|---|---|
| **Space** | O(V²) | O(V+E) |
| **Edge lookup** | **O(1)** | O(deg(v)) |
| **Add edge** | O(1) | O(1) |
| **Iterate neighbors** | O(V) | O(deg(v)) |
| **Pick when** | **Dense graphs** (E ≈ V²) | **Sparse graphs** (most real-world) |

**Floyd-Warshall:**
- **All-pairs** shortest paths
- Time: **O(V³)**
- Works with **negative weights** (but no negative cycles)
- Formula: `dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j])`

---

## 19. AMORTIZED ANALYSIS — Memory Trick

```
"Expensive operations are RARE, cheap operations are FREQUENT.
Average them out → cheap per operation."

MULTIPOP: Can't pop more than you pushed → total pops ≤ total pushes → O(1)
Dynamic Array: Doubling → geometric series < 2n → O(1)
Two-Stack Queue: Each element moves ≤ 4 times → O(1)
```

**Detailed Breakdown:**

| Scenario | Expensive Op | Why Amortized O(1) |
|---|---|---|
| **MULTIPOP stack** | Pop up to k elements at once | Total pops across ALL operations ≤ total pushes. n pushes → at most n total pops → O(1) each |
| **Dynamic array (doubling)** | Copy all elements when full | Copy costs: 1+2+4+...+n = 2n−1 < 2n. Spread over n inserts → O(1) each |
| **Two-stack queue** | Transfer all from inbox to outbox | Each element: push inbox (1) + pop inbox (1) + push outbox (1) + pop outbox (1) = 4 moves max → O(1) each |

---

*Good luck with your exam!* 🎯
