# DSA — Topics Coverage Map (All 3 Chapters)

> BITS Pilani — **Data Structures and Algorithms**
> Quick reference showing what each chapter covers — use this to check you haven't missed any topic.

---

## Chapter 1: Analyzing Algorithms (111 slides)

| Section | Topics Covered |
|---|---|
| **Algorithm Definition** | What is an algorithm, 5 characteristics (Input, Output, Definiteness, Finiteness, Effectiveness) |
| **Why Algorithms Matter** | Backbone of software, good vs bad algorithm (Bubble Sort vs Merge Sort — 50,000x difference) |
| **Ways to Represent** | Natural language, Flowchart, Pseudocode, Programming language |
| **Worked Problems** | Print largest of 3 numbers, Electricity bill (tariff-based), Scholarship eligibility, Instagram badge |
| **RAM Model** | Single processor, constant-time operations, flat memory, limitations (no cache, no parallelism) |
| **Why Analyze Performance** | Compare algorithms, predict behavior, independent of hardware |
| **Input Size** | Performance depends on input size n |
| **Best/Average/Worst Case** | Min/expected/max time over all inputs, linear search example for all 3 cases |
| **Step Counting Method** | Count primitive operations line by line, execution count tables |
| **Step Counting Exercises** | arrayMax, SumArray, SecondLargest, IsSorted — each with execution count table |
| **Seven Important Functions** | 1, log n, √n, n, n log n, n², n³, 2ⁿ, n! — growth rates ordered |
| **Asymptotic Order** | Upper/lower performance bounds, how to characterize run time |
| **Big-O (O)** | Formal definition with c and n₀, upper bound, "at most" |
| **Big-Omega (Ω)** | Formal definition, lower bound, "at least" |
| **Big-Theta (Θ)** | Formal definition with c₁, c₂, n₀, tight bound, "exactly" |
| **Little-o and Little-ω** | Strict bounds (∀c vs ∃c), strictly less/greater |
| **Proofs** | Prove 5n²+3n+20 = O(n²), prove n² ≠ O(n), prove 2^(n+1) = O(2ⁿ), prove 2^(2n) ≠ O(2ⁿ) |
| **Growth Rate Ordering** | Complete ordering of common functions, limit-based comparisons |
| **Iterative Analysis** | Nested loops (n³), dependent loops (n²), logarithmic loops (n log n), mixed |
| **Loop Invariants** | 3 properties (initialization, maintenance, termination), correctness proofs for SelectionSort, BinarySearch |
| **Divide and Conquer** | Paradigm (divide, conquer, combine), examples (Merge Sort, Binary Search, Strassen) |
| **Recurrence Relations** | Writing recurrences from algorithms (Factorial, Fibonacci, Merge Sort, Binary Search, Towers of Hanoi, Fast Exponentiation) |
| **Master Theorem** | 3 cases with formal conditions, Golden Rule (compare f(n) vs n^(log_b a)), when it does NOT apply |
| **Master Theorem Practice** | 17 worked problems (Q1-Q17 from class), exam tips, key log values to memorize |
| **Recursion Tree Method** | Draw tree, compute per-level cost, sum levels, identify pattern (increasing/decreasing/equal) |
| **Recursion Tree Practice** | T(n)=4T(n/2)+cn, T(n)=T(n/3)+T(2n/3)+cn (unequal splits), T(n)=T(n/2)+cn², T(n)=3T(n/2)+cn |
| **Substitution Method** | Guess → assume → substitute → verify, change of variables technique |
| **Substitution Practice** | T(n)=2T(n/2)+n (O(n log n)), T(n)=T(n-1)+n (O(n²)), T(n)=4T(n/2)+n (O(n²) with lower-order term trick) |

---

## Chapter 2: Elementary Data Structures (112 slides)

| Section | Topics Covered |
|---|---|
| **ADT Concept** | Abstract Data Type — separates "what" from "how", behavior vs implementation |
| **Linear vs Non-linear** | Linear (stack, queue, list) vs Non-linear (tree, graph) |
| **Stack ADT** | LIFO, operations (push/pop/peek/isEmpty/size all O(1)), formal ADT definition |
| **Stack Array Implementation** | Array + top variable, push/pop/peek pseudocode, C implementation, overflow/underflow |
| **Stack Linked List Implementation** | Insert/remove at head, pros (dynamic size) vs cons (pointer overhead, cache-unfriendly) |
| **Stack Applications** | Parenthesis matching ({[()]}), function call stack, undo mechanism, iterative DFS |
| **Infix to Postfix** | Shunting-yard algorithm with precedence rules, operator stack, worked trace |
| **Postfix Evaluation** | EVALUATE_POSTFIX pseudocode, op2 popped first (order matters), 2 worked traces |
| **Prefix Evaluation** | EVALUATE_PREFIX pseudocode, reverse+process, op1 first (opposite of postfix), worked trace |
| **Infix Evaluation** | Two-stack approach (VALS + OPS), APPLY_TOP helper, precedence handling, worked trace |
| **Infix to Prefix** | 4-step method: reverse → swap parens → infix-to-postfix → reverse, 2 worked examples |
| **Postfix to Infix** | Stack of strings, scan L→R, worked trace |
| **Prefix to Infix** | Scan R→L, stack of strings, worked trace |
| **Balanced Parentheses** | BALANCED_PARENTHESES algorithm supporting (), [], {}, 2 trace examples |
| **Stack Errors** | Overflow (push on full), Underflow (pop on empty) |
| **Queue ADT** | FIFO, operations (enqueue/dequeue/front/isEmpty/size all O(1)), formal ADT |
| **Linear Queue Limitations** | Phantom overflow — dequeued slots wasted, why shifting is O(n), solution: circular queue |
| **Circular Queue** | Modular arithmetic (rear+1)%cap, detecting full vs empty (count variable, waste one slot, boolean flag), visualization, C implementation |
| **Queue Linked List** | Enqueue at tail, dequeue at head, both O(1) with tail pointer |
| **Deque (Double-Ended Queue)** | Insert/remove at both ends O(1), input-restricted vs output-restricted |
| **Priority Queue** | Each element has priority, max-priority vs min-priority, 5 implementations compared (unsorted array, sorted array, unsorted LL, sorted LL, binary heap), pseudocode, applications (OS scheduling, Dijkstra, Huffman, emergency triage, event simulation) |
| **Queue Applications** | BFS, task scheduling (OS), print spooling, level-order tree traversal, producer-consumer buffer |
| **Amortised Analysis** | Why needed (worst-case too pessimistic), Aggregate Method (total cost / n), 3 methods overview |
| **MULTIPOP Stack** | push O(1), pop O(1), MULTIPOP(k) O(k) — total pops ≤ total pushes → amortised O(1) |
| **Two-Stack Queue** | S1 for enqueue, S2 for dequeue, transfer on empty S2 — each element moves ≤ 4 times → O(1) |
| **Dynamic Array** | Doubling capacity, total copy cost < 2n → O(1) amortised. Why doubling O(1) but constant-add O(n). Worked trace for 8 insertions. |
| **Binary Counter** | Bit flips: bit i flips n/2ⁱ times → total < 2n → O(1) amortised |
| **List ADT** | Operations (get, set, insert, remove, indexOf), array-based vs linked list |
| **Singly Linked List** | Node (data + next), head pointer, insertAtHead/Tail/Position, deleteAtHead/Position, search — all with pseudocode and complexity |
| **Doubly Linked List** | Node (prev + data + next), O(1) deletion given node pointer, LRU Cache application, insertAtHead/Tail, deleteNode pseudocode |
| **Circular Linked List** | Last node→head, round-robin scheduling, Josephus problem, no NULL check (detect full loop) |
| **Circular Doubly Linked List** | prev + data + next, last.next=head, head.prev=last, INSERT_BEGIN/END, DELETE_BEGIN/NODE pseudocode, worked trace, comparison table vs all other list types |
| **Array vs Linked List** | Comparison table (access O(1) vs O(n), insert O(n) vs O(1), cache performance, when to use which) |
| **List Applications** | Polynomial representation, sparse matrix, undo/redo (DLL), LRU Cache (DLL + hashmap), music playlist (circular) |
| **Practice Problems** | Min-Stack, postfix evaluation trace, MULTIPOP amortised proof, two-stack queue proof, reverse linked list (iterative + recursive), Floyd's cycle detection |

---

## Chapter 3: Non-Linear Data Structures (153 slides)

| Section | Topics Covered |
|---|---|
| **Linear vs Non-linear** | Linear (sequential arrangement) vs Non-linear (hierarchical/network arrangement) |
| **Tree Definition** | Non-linear hierarchical data structure, nodes connected by edges |
| **Tree Terminology** | Root, leaf, internal node, parent, child, sibling, ancestor, descendant, depth, height, degree, level, subtree, forest — each with examples on labelled tree |
| **Tree Applications** | File systems, HTML DOM, organization charts, decision trees, compiler parse trees, database indexes (B-trees) |
| **Binary Tree Definition** | Each node has at most 2 children (left, right) |
| **Binary Tree Types** | Full (0 or 2 children), Complete (all levels filled except last, left-to-right), Perfect (all levels full), Balanced (height diff ≤ 1), Degenerate/Skewed (essentially a linked list) |
| **Binary Tree Properties** | Max nodes at level i = 2ⁱ, max nodes height h = 2^(h+1)-1, min height = ⌊log₂n⌋, leaves = internal+1 (full tree), edges = n-1 |
| **Array Representation** | Parent at i, left child at 2i+1, right child at 2i+2 (0-indexed), advantages (O(1) access, cache-friendly), disadvantages (wastes space for non-complete) |
| **Linked Representation** | Node (data + left + right pointers), space-efficient for any shape |
| **BST Property** | Left subtree < root < right subtree (for every node) |
| **BST Search** | Compare, go left/right, O(h), worst O(n) skewed, best O(log n) balanced — both recursive and iterative pseudocode |
| **BST Insert** | Search for position, insert as leaf, O(h) — pseudocode and worked trace (9 keys) |
| **BST Delete** | 3 cases: leaf (remove), one child (replace with child), two children (inorder successor/predecessor) — pseudocode and worked trace |
| **BST Min/Max** | Leftmost node = min, rightmost = max |
| **Find Parent/Children/Height/Degree** | Algorithms for both array-based and linked list representations |
| **Preorder Traversal** | Root→Left→Right (NLR), recursive + iterative (stack-based) pseudocode, expression tree = prefix |
| **Inorder Traversal** | Left→Root→Right (LNR), gives sorted order for BST, recursive + iterative pseudocode with detailed stack trace |
| **Postorder Traversal** | Left→Right→Root (LRN), used for deletion, expression tree = postfix |
| **Level-Order Traversal** | BFS with queue, level by level left to right |
| **Expression Trees** | Preorder↔prefix, inorder↔infix, postorder↔postfix relationship |
| **Tree from 2 Traversals** | Preorder+Inorder → unique tree (worked example), Postorder+Inorder → unique tree (worked example), Preorder+Postorder → NOT unique (counterexample) |
| **Heap Definition** | Complete binary tree + heap property, max-heap (parent ≥ children), min-heap (parent ≤ children) |
| **Heap Array Representation** | Parent=⌊i/2⌋, left=2i, right=2i+1 (1-indexed), works perfectly because heap is always complete |
| **Heap Insert** | Add at end → bubble UP (swap with parent while violated), O(log n), step-by-step trace (7 keys) |
| **Heap Delete-Max** | Swap root with last → remove last → bubble DOWN (heapify), O(log n), step-by-step trace |
| **Heap Insert/Delete (Linked List)** | Algorithms for linked list representation |
| **Heapify** | Convert arbitrary array into heap, bottom-up approach from ⌊n/2⌋ to 1, O(n) — proof of O(n) |
| **Heap Sort** | Build max-heap O(n) + n extractions O(n log n) = O(n log n), in-place, not stable, complete worked trace (10 elements) |
| **Priority Queue via Heap** | O(log n) insert, O(log n) delete, O(1) find-max |
| **Sorting Comparison** | Heap Sort vs Merge Sort vs Quick Sort vs Insertion Sort (worst/average/best/space/stable) |
| **BST vs Heap** | 10-dimension comparison (ordering, find max, search, insert, delete, sorted output, shape, array storage, use case) |
| **Graph Definition** | G=(V,E), vertex, edge, directed/undirected, weighted/unweighted |
| **Graph Terminology** | Degree (in/out for directed), path, walk, trail, circuit, cycle, connected, strongly connected, multigraph, simple graph, subgraph, complete, bipartite, null, trivial |
| **Connectivity/Reachability** | Connected graph, transitive closure concept |
| **Graph Properties** | Handshaking lemma (Σdeg=2|E|), max edges undirected n(n-1)/2, max edges directed n(n-1), connected graph |E|≥|V|-1 |
| **Adjacency Matrix** | O(V²) space, O(1) edge lookup, good for dense graphs, symmetric for undirected |
| **Adjacency List** | O(V+E) space, O(deg) edge lookup, good for sparse graphs |
| **Matrix vs List Comparison** | Space, edge lookup, add edge, iterate neighbours, best for — comparison table |
| **BFS** | Queue-based, level-by-level, O(V+E), shortest path in unweighted, pseudocode + worked trace with queue state table + BFS tree |
| **DFS** | Stack/recursion, go deep then backtrack, O(V+E), recursive + iterative pseudocode, call stack trace, discovery/finish times |
| **BFS vs DFS** | Comparison table (data structure, strategy, shortest path, cycle detection, memory, when to use) |
| **Edge Classification (DFS)** | Tree edges, back edges (→ cycle), forward edges, cross edges |
| **Directed Graph** | Already covered terminology applied to directed graphs |
| **Transitive Closure** | Floyd-Warshall algorithm, O(V³), dist[i][j] = min(dist[i][j], dist[i][k]+dist[k][j]), step-by-step worked example (4 matrices) |
| **Floyd-Warshall vs Dijkstra** | All-pairs vs single-source, handles negative weights, complexity comparison |

---

*Use this file to verify you haven't missed any DSA topic before the exam.*
