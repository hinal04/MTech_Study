# DSA — Quick Revision Notes
> BITS Pilani | All 3 Chapters | Last-Minute Reference

---

## Chapter 1: Analyzing Algorithms

### Algorithm — Definition & 5 Characteristics

- **Algorithm**: A finite set of well-defined instructions to solve a problem
- Five characteristics:

| # | Characteristic | Meaning |
|---|---------------|---------|
| 1 | **Input** | Zero or more externally supplied quantities |
| 2 | **Output** | At least one quantity is produced |
| 3 | **Definiteness** | Each step is precisely and unambiguously defined |
| 4 | **Finiteness** | Terminates after a finite number of steps |
| 5 | **Effectiveness** | Every step is basic enough to be carried out exactly |

### RAM Model Assumptions

- **Single processor** — instructions execute one after another (no parallelism)
- **Constant-time operations** — arithmetic, comparison, assignment, memory access all take O(1)
- **Flat memory** — no cache hierarchy; every memory access costs the same
- No operating system overhead

### Best / Average / Worst Case

| Case | Definition |
|------|-----------|
| **Best** | Minimum time over all inputs of size n |
| **Average** | Expected time averaged over all possible inputs of size n |
| **Worst** | Maximum time over all inputs of size n (most commonly used) |

### Asymptotic Notations — Formal Definitions

| Notation | Formal Definition | Intuition |
|----------|------------------|-----------|
| **O(f(n))** | ∃ c > 0, n₀ > 0 such that 0 ≤ g(n) ≤ c·f(n) ∀ n ≥ n₀ | Upper bound (≤) |
| **Ω(f(n))** | ∃ c > 0, n₀ > 0 such that 0 ≤ c·f(n) ≤ g(n) ∀ n ≥ n₀ | Lower bound (≥) |
| **Θ(f(n))** | ∃ c₁, c₂ > 0, n₀ > 0 such that c₁·f(n) ≤ g(n) ≤ c₂·f(n) ∀ n ≥ n₀ | Tight bound (=) |
| **o(f(n))** | ∀ c > 0, ∃ n₀ > 0 such that 0 ≤ g(n) < c·f(n) ∀ n ≥ n₀ | Strictly less than (<) |
| **ω(f(n))** | ∀ c > 0, ∃ n₀ > 0 such that 0 ≤ c·f(n) < g(n) ∀ n ≥ n₀ | Strictly greater than (>) |

> **Tip**: O and Ω use **∃c** (exists), little-o and ω use **∀c** (for all)

### Common Growth Rates (Slowest → Fastest)

```
1  <  log n  <  √n  <  n  <  n log n  <  n²  <  n³  <  2ⁿ  <  n!
```

### Key Log Values to Memorise

| Expression | Value | | Expression | Value |
|-----------|-------|-|-----------|-------|
| log₂ 2 | 1 | | log₂ 1024 | 10 |
| log₂ 4 | 2 | | log₃ 9 | 2 |
| log₂ 8 | 3 | | log₃ 27 | 3 |
| log₂ 16 | 4 | | log₁₀ 1000 | 3 |

### Master Theorem

For recurrences of the form: **T(n) = aT(n/b) + f(n)** where a ≥ 1, b > 1

**Golden Rule**: Compute **n^(log_b a)** and compare with **f(n)**

| Case | Condition | Result |
|------|-----------|--------|
| **Case 1** | f(n) = O(n^(log_b(a) − ε)) for some ε > 0 | T(n) = **Θ(n^(log_b a))** |
| **Case 2** | f(n) = Θ(n^(log_b a)) | T(n) = **Θ(n^(log_b a) · log n)** |
| **Case 3** | f(n) = Ω(n^(log_b(a) + ε)) for some ε > 0 **AND** af(n/b) ≤ cf(n) for c < 1 | T(n) = **Θ(f(n))** |

> **Memory trick**: Compare f(n) vs n^(log_b a) — whichever is "heavier" dominates. Tie → multiply by log n.

**Quick examples**:
- T(n) = 2T(n/2) + n → log₂2 = 1, f(n) = n = Θ(n¹) → **Case 2** → Θ(n log n) ← Merge Sort
- T(n) = 4T(n/2) + n → log₂4 = 2, f(n) = n = O(n^(2−1)) → **Case 1** → Θ(n²)
- T(n) = T(n/2) + 1 → log₂1 = 0, f(n) = 1 = Θ(n⁰) → **Case 2** → Θ(log n) ← Binary Search

### Recursion Tree Method

1. **Draw the tree** — each node represents cost of non-recursive work at that level
2. **Compute per-level cost** — sum all nodes at each level
3. **Count levels** — tree has log_b(n) levels (for T(n) = aT(n/b) + f(n))
4. **Sum all levels** — total cost = Σ (cost at each level)
5. **Identify the pattern** — geometric series? arithmetic? all levels equal?
6. **Derive closed form** using series formulas

### Substitution Method

1. **Guess** the form of the solution (e.g., T(n) = O(n log n))
2. **Assume** T(k) ≤ c·k·log(k) for all k < n (inductive hypothesis)
3. **Substitute** into the recurrence and prove T(n) ≤ c·n·log(n)
4. **Choose c** large enough to satisfy the base case
5. If the guess fails → try a stronger/weaker bound

### Step Counting

- T(n) = count all **primitive operations** (assignments, comparisons, arithmetic, returns)
- Keep only the **dominant term** and drop constants
- Example: T(n) = 3n² + 5n + 2 → **O(n²)**

---

## Chapter 2: Elementary Data Structures

### Stack (LIFO — Last In, First Out)

| Operation | Time | Description |
|-----------|------|-------------|
| `push(x)` | O(1) | Add element on top |
| `pop()` | O(1) | Remove & return top element |
| `peek()`/`top()` | O(1) | View top without removing |
| `isEmpty()` | O(1) | Check if stack is empty |

**Implementation**: Array (fixed size, simple) or Linked List (dynamic, extra pointer overhead)

### Stack Applications

- **Parenthesis matching** — push opening, pop on closing, check match
- **Infix → Postfix** — Shunting-yard algorithm (operator stack)
- **Postfix evaluation** — scan left-to-right, push operands, pop-and-apply on operator
- **Prefix evaluation** — scan right-to-left, push operands, pop-and-apply on operator
- **Infix evaluation** — two stacks (operand + operator), apply based on precedence
- **DFS** — uses stack (explicit or call stack recursion)
- **Function call stack** — stores return addresses, local variables, parameters
- **Undo mechanism** — push actions, pop to undo

### Expression Conversion — Summary Table

| From → To | Method |
|-----------|--------|
| **Infix → Postfix** | Shunting-yard: scan L→R, operand→output, operator→stack (pop higher/equal precedence first), `(`→push, `)`→pop until `(` |
| **Infix → Prefix** | ① Reverse string ② Swap `(` ↔ `)` ③ Apply Infix→Postfix ④ Reverse result |
| **Postfix → Infix** | Scan L→R, push operands; on operator: pop 2, form `(op1 ○ op2)`, push back |
| **Prefix → Infix** | Scan R→L, push operands; on operator: pop 2, form `(op1 ○ op2)`, push back |
| **Postfix → Prefix** | Scan L→R, push operands; on operator: pop 2, form `○ op1 op2`, push back |
| **Prefix → Postfix** | Scan R→L, push operands; on operator: pop 2, form `op1 op2 ○`, push back |

> **Operator precedence** (high→low): `^` > `* /` > `+ -` | `^` is right-associative, others left-associative

### Queue (FIFO — First In, First Out)

| Operation | Time | Description |
|-----------|------|-------------|
| `enqueue(x)` | O(1) | Add at rear |
| `dequeue()` | O(1) | Remove from front |
| `front()` | O(1) | View front element |

### Linear Queue → Circular Queue

- **Problem**: Linear array queue suffers **phantom overflow** — front advances, space before front is wasted even when not full
- **Solution**: Circular queue using **modular arithmetic**
  - `rear = (rear + 1) % capacity`
  - `front = (front + 1) % capacity`
  - **Full**: `(rear + 1) % capacity == front`
  - **Empty**: `front == rear` (or use a count variable)

### Deque (Double-Ended Queue)

- Insert and remove at **both ends** — O(1)
- Two restricted types:
  - **Input-restricted**: insert at one end only, delete from both
  - **Output-restricted**: delete from one end only, insert at both

### Priority Queue — Implementation Comparison

| Implementation | Insert | Delete-min/max | Find-min/max |
|---------------|--------|---------------|-------------|
| **Unsorted array** | O(1) | O(n) | O(n) |
| **Sorted array** | O(n) | O(1) | O(1) |
| **Binary Heap** | O(log n) | O(log n) | O(1) |
| **BST (balanced)** | O(log n) | O(log n) | O(log n) |

### Amortised Analysis — Aggregate Method

| Scenario | Key Insight | Amortised Cost |
|----------|------------|----------------|
| **MULTIPOP stack** | Total pops across all ops ≤ total pushes. If n operations → at most n pops total | **O(1)** per op |
| **Two-stack queue** | Each element is pushed ≤ 1 time to S1, popped ≤ 1 from S1, pushed ≤ 1 to S2, popped ≤ 1 from S2 → 4 ops max per element | **O(1)** per op |
| **Dynamic array (doubling)** | Insertions: 1+1+1+…+copy costs. Copy at size 1,2,4,8…n → total copies = 1+2+4+…+n < 2n | **O(1)** per insert |

**Why doubling → O(1) but constant increment → O(n)**:
- Doubling: copy costs form geometric series → sum < 2n → amortised O(1)
- Adding constant k: copy costs form arithmetic series → sum = O(n²/k) → amortised **O(n)**

### Linked Lists — Comparison Table

| Type | Insert Head | Delete Head | Search/Delete Any | Traverse Both Directions | Circular |
|------|:-----------:|:-----------:|:-----------------:|:-----------------------:|:--------:|
| **Singly** | O(1) | O(1) | O(n) | ✗ | ✗ |
| **Doubly** | O(1) | O(1) | O(n) (delete O(1) with pointer) | ✓ | ✗ |
| **Circular Singly** | O(1) | O(1) | O(n) | ✗ | ✓ |
| **Circular Doubly** | O(1) | O(1) | O(n) (delete O(1) with pointer) | ✓ | ✓ |

### Array vs Linked List

| Feature | Array | Linked List |
|---------|-------|-------------|
| Access by index | **O(1)** | O(n) |
| Insert/Delete at beginning | O(n) | **O(1)** |
| Insert/Delete at end | O(1) amortised (dynamic) | O(1) with tail pointer |
| Memory | Contiguous, **cache-friendly** | Scattered, pointer overhead |
| Size | Fixed (static) or resize (dynamic) | Naturally dynamic |

---

## Chapter 3: Non-Linear Data Structures

### Tree Terminology

| Term | Definition |
|------|-----------|
| **Root** | Topmost node (no parent) |
| **Leaf** | Node with no children (degree 0) |
| **Height of node** | Longest path from that node to a leaf |
| **Height of tree** | Height of root |
| **Depth of node** | Number of edges from root to that node |
| **Degree of node** | Number of children |
| **Level** | Depth of the node (root is level 0) |
| **Internal node** | Node with at least one child |
| **Siblings** | Nodes with the same parent |

### Binary Tree Properties

- Max nodes at **level i** = **2ⁱ** (root at level 0)
- Max nodes in tree of **height h** = **2^(h+1) − 1**
- Min height with n nodes = **⌊log₂ n⌋**
- In a **full binary tree**: leaves = internal nodes + 1 → **L = I + 1**
- **Full binary tree**: every node has 0 or 2 children
- **Complete binary tree**: all levels full except possibly last, filled left to right
- **Perfect binary tree**: all levels completely full

### BST (Binary Search Tree)

**Property**: Left subtree < Root < Right subtree (for every node)

| Operation | Average | Worst (skewed) |
|-----------|:-------:|:--------------:|
| Search | O(log n) | O(n) |
| Insert | O(log n) | O(n) |
| Delete | O(log n) | O(n) |

**Deletion — 3 cases**:
1. **Leaf node** → simply remove
2. **One child** → replace node with its child
3. **Two children** → replace with **inorder successor** (smallest in right subtree) or inorder predecessor (largest in left subtree), then delete that node

### Tree Traversals

| Traversal | Order | Mnemonic | Use Case |
|-----------|-------|----------|----------|
| **Preorder** | Root → Left → Right | **NLR** | Copy tree, prefix expression |
| **Inorder** | Left → Root → Right | **LNR** | **Sorted order for BST** |
| **Postorder** | Left → Right → Root | **LRN** | Delete tree, postfix expression |
| **Level-order** | Level by level (BFS) | Queue-based | Breadth-first, shortest depth |

### Reconstruct Tree from Traversals

- Need **Inorder + Preorder** → ✓ Unique tree
- Need **Inorder + Postorder** → ✓ Unique tree
- Preorder + Postorder alone → ✗ **Not unique** (without inorder)
- **Method**: First/last element of preorder/postorder = root → split inorder into left & right subtrees → recurse

### Heap (Binary Heap)

**Definition**: A **complete binary tree** satisfying the heap property

| Type | Property | Root |
|------|----------|------|
| **Max-Heap** | Parent ≥ Children | Maximum element |
| **Min-Heap** | Parent ≤ Children | Minimum element |

**Array representation (1-indexed)**:

| Relationship | Index |
|-------------|-------|
| Parent of i | **⌊i/2⌋** |
| Left child of i | **2i** |
| Right child of i | **2i + 1** |

| Operation | Time | Method |
|-----------|------|--------|
| **Insert** | O(log n) | Add at end → **bubble UP** (swap with parent while violated) |
| **Delete-max/min** | O(log n) | Swap root with last → remove last → **bubble DOWN** (heapify) |
| **Build-heap** | **O(n)** | Bottom-up heapify from ⌊n/2⌋ down to 1 |
| **Find-max/min** | O(1) | Just return root |

> **Why build-heap is O(n)**: Most nodes are near the bottom and heapify very little. Sum = Σ h/2^h → O(n)

**Heap Sort**:
1. Build max-heap → O(n)
2. Repeatedly extract max, place at end → n × O(log n)
3. **Total: O(n log n)** | In-place: ✓ | Stable: ✗

### Graph — G = (V, E)

| Type | Edges |
|------|-------|
| **Undirected** | {u, v} — bidirectional |
| **Directed** | (u, v) — u → v only |
| **Weighted** | Each edge has a weight/cost |
| **Unweighted** | All edges treated equally |

**Handshaking Lemma**: Σ deg(v) = **2|E|** (undirected graph)

### Graph Representation

| | Adjacency Matrix | Adjacency List |
|-|-------------------|----------------|
| **Space** | O(V²) | O(V + E) |
| **Edge lookup** | O(1) | O(deg(v)) |
| **Add edge** | O(1) | O(1) |
| **Best for** | Dense graphs | Sparse graphs |

### BFS (Breadth-First Search)

- **Data structure**: Queue
- **Strategy**: Visit level by level (all neighbours before going deeper)
- **Time**: O(V + E)
- **Finds**: Shortest path in **unweighted** graphs
- **Applications**: Shortest path, level-order traversal, connected components, bipartiteness check

### DFS (Depth-First Search)

- **Data structure**: Stack (or recursion)
- **Strategy**: Go as deep as possible, then backtrack
- **Time**: O(V + E)
- **Edge types**: Tree edge, Back edge (→ cycle!), Forward edge, Cross edge
- **Applications**: Cycle detection (back edge exists), topological sort, connected components, path finding

### BFS vs DFS — Quick Comparison

| Feature | BFS | DFS |
|---------|-----|-----|
| Data structure | Queue | Stack/Recursion |
| Explores | Level by level | Depth first |
| Shortest path (unweighted) | ✓ Yes | ✗ No |
| Cycle detection | Via visited check | Via **back edges** |
| Space | O(V) — stores full level | O(V) — stores path |
| Time | O(V + E) | O(V + E) |

### Floyd-Warshall Algorithm

- **Purpose**: All-pairs shortest paths
- **Time**: **O(V³)** | Space: O(V²)
- Works with **negative weights** (but no negative cycles)
- **DP recurrence**:
  ```
  dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j])
  ```
  for each intermediate vertex k from 1 to V
- **Method**: Try every vertex k as intermediate, update all (i, j) pairs

---

## Quick Formula Sheet

| What | Formula |
|------|---------|
| Master Theorem comparison | n^(log_b a) vs f(n) |
| Circular queue advance | (index + 1) % capacity |
| Heap parent (1-indexed) | ⌊i/2⌋ |
| Heap children (1-indexed) | 2i, 2i+1 |
| Max nodes height h | 2^(h+1) − 1 |
| Nodes at level i | 2ⁱ |
| Full BT: Leaves vs Internal | L = I + 1 |
| Handshaking Lemma | Σ deg = 2|E| |
| Floyd-Warshall | dist[i][j] = min(dist[i][j], dist[i][k]+dist[k][j]) |

---

*Good luck with your exam!* 🎯
