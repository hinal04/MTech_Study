# Data Structures & Algorithms - Mid-Semester Questions & Answers
## CSI ZG528 - Chapters 1-3 (Algorithm Analysis, Elementary Data Structures, Non-Linear Structures)

---

## Q1. Algorithm Definition and Properties [2.5 Marks]

### Question:
Define what an algorithm is and list the five essential properties that an algorithm must satisfy. For each property, provide a brief explanation and a practical example.

### Answer:

**Definition of Algorithm [0.5 marks]:**
An algorithm is a well-defined computational procedure that takes some value or set of values as input and produces some value or set of values as output. It is a sequence of computational steps that transforms the input into the output.

**Five Essential Properties [2 marks - 0.4 each]:**

1. **Input:** Zero or more quantities are externally supplied to the algorithm.
   - *Example:* Sorting algorithm receives an array and size as input.

2. **Output:** At least one quantity is produced as a result.
   - *Example:* Sorting algorithm produces sorted array as output.

3. **Definiteness:** Each instruction is clear, unambiguous, and precisely defined.
   - *Example:* "Sort the array in ascending order" is definite; "Sort the array nicely" is not.

4. **Finiteness:** The algorithm must terminate after a finite number of steps.
   - *Example:* Loop must have valid termination condition; infinite loops violate this property.

5. **Effectiveness:** Every instruction must be basic enough to be carried out by a person using pencil and paper.
   - *Example:* Arithmetic operations, comparisons, assignments are effective; undefined operations are not.

---

## Q2. Asymptotic Notation and Complexity Analysis [3 Marks]

### Question:
Explain Big-O, Big-Omega (Ω), and Big-Theta (Θ) notations. For each: (i) provide formal definition, (ii) explain meaning, (iii) give practical example with function. Also distinguish between best-case, average-case, and worst-case complexity.

### Answer:

**Big-O Notation (Upper Bound) [0.75 marks]:**
- **Definition:** f(n) = O(g(n)) if there exist positive constants c and n₀ such that f(n) ≤ c·g(n) for all n ≥ n₀.
- **Meaning:** f(n) grows no faster than g(n); upper bound on growth rate
- **Interpretation:** Worst-case scenario; algorithm will not exceed this time
- **Example:** 3n² + 2n + 1 = O(n²) because 3n² + 2n + 1 ≤ 4n² for n ≥ 1

**Big-Omega Notation (Lower Bound) [0.75 marks]:**
- **Definition:** f(n) = Ω(g(n)) if there exist positive constants c and n₀ such that f(n) ≥ c·g(n) for all n ≥ n₀.
- **Meaning:** f(n) grows at least as fast as g(n); lower bound on growth rate
- **Interpretation:** Best-case scenario; algorithm will take at least this much time
- **Example:** 3n² + 2n + 1 = Ω(n²) because 3n² + 2n + 1 ≥ 2n² for n ≥ 1

**Big-Theta Notation (Tight Bound) [0.75 marks]:**
- **Definition:** f(n) = Θ(g(n)) if f(n) = O(g(n)) AND f(n) = Ω(g(n))
- **Meaning:** f(n) grows exactly like g(n) (same growth rate); tight upper and lower bounds
- **Interpretation:** Precise complexity; algorithm grows exactly this rate
- **Example:** 3n² + 2n + 1 = Θ(n²) because 2n² ≤ 3n² + 2n + 1 ≤ 4n² for all n ≥ 1

**Best, Average, Worst Case [1.5 marks - covered in explanation]:**
- **Best-case:** Minimum time algorithm takes (optimal input)
- **Average-case:** Expected time for random input (statistical average)
- **Worst-case:** Maximum time algorithm takes (adversarial input)

---

## Q3. Recurrence Relations and Master Theorem [4 Marks]

### Question:
What are recurrence relations? Explain how to solve them using: (i) substitution method, (ii) recursion tree method, (iii) Master Theorem. Solve T(n) = 2T(n/2) + n using all three methods. Apply Master Theorem to T(n) = 3T(n/4) + n² and state the result.

### Answer:

**What are Recurrence Relations? [0.5 marks]:**
A recurrence relation expresses the runtime of a recursive algorithm in terms of its runtime on smaller inputs. It describes how the total time depends on the time for subproblems.

Example: Merge sort has T(n) = 2T(n/2) + n (divide into 2 problems of size n/2, then merge in O(n) time).

**Method 1: Substitution Method [1 mark]**

For T(n) = 2T(n/2) + n with T(1) = 1:

Expand recursively:
```
T(n) = 2T(n/2) + n
     = 2[2T(n/4) + n/2] + n
     = 4T(n/4) + 2n
     = 8T(n/8) + 3n
     ...
     = 2^k·T(n/2^k) + k·n
```

Stop when n/2^k = 1, so k = log n:
```
T(n) = n·T(1) + n·log n
     = n + n·log n
     = Θ(n log n)
```

**Method 2: Recursion Tree Method [1 mark]**

For T(n) = 2T(n/2) + n:

```
                Level 0:        n              Cost: n
               /       \
            n/2         n/2       Cost: n
           /   \       /   \
          n/4   n/4   n/4   n/4    Cost: n
           ...
         (n leaves of T(1))        Cost: n

Height: log n levels
Total cost: n · log n = Θ(n log n)
```

**Method 3: Master Theorem [1 mark]**

For T(n) = aT(n/b) + f(n):
- a = 2, b = 2, f(n) = n
- log_b(a) = log₂(2) = 1
- Compare f(n) = n with n^1: they're equal
- Case 2 applies: T(n) = Θ(n log n)

**Application to T(n) = 3T(n/4) + n²:**
- a = 3, b = 4, f(n) = n²
- log_b(a) = log₄(3) ≈ 0.792
- n² vs. n^0.792: n² is larger (2 > 0.792)
- Case 3 applies: **T(n) = Θ(n²)**

---

## Q4. Stack ADT and Implementation [3.5 Marks]

### Question:
Define the Stack ADT. Explain: (i) stack concept (LIFO), (ii) core operations (push, pop, peek, isEmpty), (iii) two implementation approaches (array-based and linked-list), (iv) time complexity for each implementation, (v) compare advantages and disadvantages.

### Answer:

**Stack Concept [0.5 marks]:**
A stack is a collection of elements following LIFO (Last In, First Out) principle. The last element inserted is the first one removed. Real-world analogy: stack of plates in cafeteria; undo feature in text editors.

**Core Operations [0.75 marks]:**
- **push(x):** Insert element x onto top of stack
- **pop():** Remove and return top element; error if empty
- **peek():** Return top element without removing; error if empty
- **isEmpty():** Check if stack is empty

**Array-Based Implementation [1.25 marks]:**
```
struct Stack {
    Element[] arr;      // Array to store elements
    int top;            // Index of top element (-1 if empty)
}

push(x):
    if (top == arr.length - 1) resize();
    arr[++top] = x;

pop():
    if (isEmpty()) error();
    return arr[top--];

peek():
    if (isEmpty()) error();
    return arr[top];
```
- **Advantage:** O(1) access time; fixed memory overhead
- **Disadvantage:** Fixed size; must resize when full (amortized O(1) for push)
- **Time Complexity:** O(1) for push, pop, peek (amortized)

**Linked-List Implementation [1.25 marks]:**
```
struct Node {
    Element data;
    Node* next;
}

struct Stack {
    Node* top;          // Pointer to top node
}

push(x):
    Node* newNode = new Node(x);
    newNode->next = top;
    top = newNode;

pop():
    if (isEmpty()) error();
    Element x = top->data;
    top = top->next;
    return x;
```
- **Advantage:** Dynamic size; no wasted space
- **Disadvantage:** Extra memory for pointers; no random access
- **Time Complexity:** O(1) for push, pop, peek

**Comparison:**

| Aspect | Array-Based | Linked-List |
|--------|------------|------------|
| **Time Complexity** | O(1) amortized | O(1) always |
| **Space Complexity** | O(n) + waste | O(n) + pointer overhead |
| **Size Flexibility** | Fixed/resizable | Dynamic |
| **Cache Locality** | Good (contiguous memory) | Poor (scattered memory) |
| **When to use** | Fixed size, performance critical | Dynamic size, flexibility needed |

---

## Q5. Queue ADT and Variations [3.5 Marks]

### Question:
Define Queue ADT and explain FIFO principle. Describe: (i) linear queue limitations, (ii) circular queue advantages, (iii) double-ended queue (Deque), (iv) priority queue. For each: provide operations, implementation considerations, and use cases.

### Answer:

**Queue ADT and FIFO [0.5 marks]:**
Queue is a collection following FIFO (First In, First Out) principle. First element inserted is first one removed. Real-world analogy: checkout line at store; print queue in OS.

**Linear Queue Limitations [0.75 marks]:**
- **Problem:** Dequeue removes from front; rear keeps moving; wasted space at front
- **Example:**
  ```
  Initial:  [1, 2, 3, _, _]   front=0, rear=2
  After dequeue: [_, 2, 3, _, _]  front=1, rear=2 (wasted space)
  After 2 dequeues: [_, _, 3, _, _]  front=2, rear=2 (50% wasted)
  ```
- **Solution needed:** Reuse dequeued space

**Circular Queue [1.25 marks]:**
Indices wrap around using modulo arithmetic; front and rear pointers rotate.
```
Indices: (front + 1) % size and (rear + 1) % size

Enqueue: rear = (rear + 1) % size; arr[rear] = x
Dequeue: front = (front + 1) % size; return arr[front]

Full condition: (rear + 1) % size == front
Empty condition: front == rear
```
- **Advantages:** No wasted space; constant reuse of array
- **Use cases:** OS job queues, printer queues, buffer management

**Double-Ended Queue (Deque) [0.75 marks]:**
Allows insertion and deletion at both ends (front and rear).
- **Operations:** insertFront, insertRear, removeFront, removeRear
- **Use cases:** Sliding window problems, undo/redo with forward/backward, palindrome checking
- **Example:** Deque for browser history (back and forward buttons)

**Priority Queue [0.75 marks]:**
Elements have associated priorities; higher priority elements dequeued first.
- **Operations:** enqueue(element, priority), dequeue()
- **Implementation:** Min-heap or max-heap provides O(log n) enqueue/dequeue
- **Use cases:** Task scheduling, Dijkstra's algorithm, Huffman coding
- **Example:** OS process scheduling by priority

---

## Q6. Amortized Analysis [3 Marks]

### Question:
What is amortized analysis and why is it useful? Explain the aggregate method. Analyze the amortized time complexity of: (i) dynamic array grow/push operation, (ii) stack push/pop operations. For dynamic array with doubling strategy, show that amortized O(1) per operation despite occasional O(n) resize.

### Answer:

**What is Amortized Analysis? [0.75 marks]:**
Amortized analysis computes average time per operation over a sequence of operations, even if individual operations have different costs. Useful when most operations are cheap but some are expensive.

**Why Useful:** Some data structure operations are O(1) most of the time but occasionally O(n). Amortized analysis shows average cost is still O(1) across many operations.

**Aggregate Method [0.75 marks]:**
Calculate total cost of n operations, then divide by n to get amortized cost per operation.

**Dynamic Array Push with Doubling [1.5 marks]:**

Strategy: When array is full, double its size and copy all elements.

Cost analysis for n push operations:
- Operations 1: push (cost 1)
- Operation 2: push (cost 1)
- Operation 3: push (array full, resize; cost = 1 + copy 4 elements = 5)
- Operations 4: push (cost 1)
- ...
- Operation 8: push (array full, resize; cost = 1 + copy 8 elements = 9)

Resizes occur at: n = 1, 2, 4, 8, 16, ...

Total cost for n operations:
```
Push operations: n × 1 = n
Resize 1 (size 2): 2 copy operations
Resize 2 (size 4): 4 copy operations
Resize 3 (size 8): 8 copy operations
...
Resize k (size 2^k): 2^k copy operations

Total resize cost: 2 + 4 + 8 + ... + 2^(log n) = 2(2^(log n) - 1) = 2n - 2

Total cost: n + (2n - 2) = 3n - 2
Amortized cost per operation: (3n - 2) / n ≈ 3 = O(1)
```

**Conclusion:** Even though some pushes cost O(n), amortized cost is O(1) because expensive resizes are infrequent.

---

## Q7. Linked List Types and Operations [3 Marks]

### Question:
Explain different types of linked lists: (i) singly linked list, (ii) doubly linked list, (iii) circular linked list, (iv) circular doubly linked list. For each: describe structure, insertion/deletion operations, advantages/disadvantages, and use cases.

### Answer:

**Singly Linked List [0.75 marks]:**
- **Structure:** Each node has data and pointer to next node only
- **Operations:** Insert/delete at head O(1); insert/delete at tail requires traversal O(n) unless tail pointer maintained
- **Advantages:** Simple; minimal memory overhead; forward iteration only
- **Use cases:** Stack implementation, simple queue, Fibonacci lists
- **Disadvantage:** Cannot traverse backward; to find previous node must traverse from head

**Doubly Linked List [0.75 marks]:**
- **Structure:** Each node has data, pointer to next, and pointer to previous
- **Operations:** Insert/delete at head or tail both O(1); can traverse both directions
- **Advantages:** Bidirectional traversal; can easily find previous node
- **Use cases:** Browser back/forward history, LRU cache, deque implementation
- **Disadvantage:** Extra memory for previous pointer; more complex operations

**Circular Linked List [0.75 marks]:**
- **Structure:** Singly linked list where last node points back to first node (forms circle)
- **Operations:** No NULL pointer to detect end; use sentinel or size counter
- **Advantages:** Can reach any node starting from any node; simulates circular structures
- **Use cases:** Round-robin scheduling, carousel/circular buffers, gaming circles
- **Disadvantage:** Must track size or use sentinel to avoid infinite loops

**Circular Doubly Linked List [0.75 marks]:**
- **Structure:** Doubly linked list where last node's next points to first, first node's prev points to last
- **Operations:** Full bidirectional with circular property; efficient access from both ends
- **Advantages:** Combines benefits of doubly linked and circular lists
- **Use cases:** Complex data structures, optimal for problems requiring both ends access
- **Example:** Music playlist with next/previous navigation in circular manner

---

## Q8. Binary Search Trees - Definition and Operations [3.5 Marks]

### Question:
Define Binary Search Tree (BST). Explain: (i) BST property, (ii) search operation (algorithm and complexity), (iii) insertion operation (algorithm and complexity), (iv) deletion operation with three cases, (v) in-order traversal produces sorted sequence.

### Answer:

**BST Definition and Property [0.75 marks]:**
Binary Search Tree is a binary tree where for each node:
- All elements in left subtree are smaller than node's value
- All elements in right subtree are larger than node's value
- No duplicate values (typically)

**Search Operation [0.75 marks]:**
```
search(node, value):
    if (node == NULL) return FALSE
    if (value == node.data) return TRUE
    if (value < node.data) 
        return search(node.left, value)
    else 
        return search(node.right, value)
```
- **Time Complexity:** Average O(log n) when balanced; worst case O(n) if skewed (degenerate tree)
- **Comparison:** Systematic elimination of half the remaining nodes each level

**Insertion Operation [0.75 marks]:**
```
insert(node, value):
    if (node == NULL) 
        return new Node(value)
    if (value < node.data)
        node.left = insert(node.left, value)
    else if (value > node.data)
        node.right = insert(node.right, value)
    return node
```
- **Time Complexity:** Same as search; O(log n) average, O(n) worst case
- **Process:** Recursively find correct position maintaining BST property

**Deletion Operation [1 mark]:**

Three cases:

1. **Node is leaf (no children):** Simply remove it
   ```
   Delete 5:     4          4
                / \        /
               2   5  →   2
              / \
             1   3
   ```

2. **Node has one child:** Replace node with its child
   ```
   Delete 2:      4          4
                 / \        / \
                2   5  →   1   5
               /
              1
   ```

3. **Node has two children:** Replace with in-order predecessor (largest in left subtree) or successor (smallest in right subtree)
   ```
   Delete 4 (has 2 children):
   Find in-order predecessor (3 from left subtree):
        4                3
       / \              / \
      2   5     →      2   5
     / \
    1   3
   ```

**In-order Traversal Produces Sorted Sequence [0.5 marks]:**
In-order traversal: visit left subtree → visit node → visit right subtree

```
Example BST:
        4
       / \
      2   6
     / \ / \
    1  3 5  7

In-order: 1, 2, 3, 4, 5, 6, 7 (sorted!)
```

This is sorted because:
- Visit left subtree (all values < node)
- Visit node
- Visit right subtree (all values > node)
- Recursively, this maintains sorted order

---

## Q9. Binary Tree Traversals [3 Marks]

### Question:
Explain four binary tree traversal methods: (i) in-order, (ii) pre-order, (iii) post-order, (iv) level-order. For each: provide pseudocode, explain when to use, give example, and state time/space complexity.

### Answer:

**In-Order Traversal (Left-Root-Right) [0.75 marks]:**
```
inOrder(node):
    if (node == NULL) return
    inOrder(node.left)
    visit(node)
    inOrder(node.right)
```
- **Use case:** Get sorted sequence from BST; print expression tree as infix notation
- **Example:** BST [4, 2, 6, 1, 3, 5, 7] → Output: 1, 2, 3, 4, 5, 6, 7
- **Time/Space:** O(n) time, O(h) space where h is height (recursion stack)

**Pre-Order Traversal (Root-Left-Right) [0.75 marks]:**
```
preOrder(node):
    if (node == NULL) return
    visit(node)
    preOrder(node.left)
    preOrder(node.right)
```
- **Use case:** Create copy of tree; serialize tree for storage; prefix notation
- **Example:** BST [4, 2, 6, 1, 3, 5, 7] → Output: 4, 2, 1, 3, 6, 5, 7
- **Time/Space:** O(n) time, O(h) space

**Post-Order Traversal (Left-Right-Root) [0.75 marks]:**
```
postOrder(node):
    if (node == NULL) return
    postOrder(node.left)
    postOrder(node.right)
    visit(node)
```
- **Use case:** Delete tree; evaluate expression tree (postfix notation); disk space calculation
- **Example:** BST [4, 2, 6, 1, 3, 5, 7] → Output: 1, 3, 2, 5, 7, 6, 4
- **Time/Space:** O(n) time, O(h) space

**Level-Order Traversal (BFS) [0.75 marks]:**
```
levelOrder(root):
    queue Q = empty
    Q.enqueue(root)
    while (Q not empty):
        node = Q.dequeue()
        visit(node)
        if (node.left != NULL) Q.enqueue(node.left)
        if (node.right != NULL) Q.enqueue(node.right)
```
- **Use case:** Find shortest path in tree; print tree level by level; find closest ancestor
- **Example:** BST [4, 2, 6, 1, 3, 5, 7] → Output: 4, 2, 6, 1, 3, 5, 7
- **Time/Space:** O(n) time, O(w) space where w is maximum width

---

## Q10. Balanced Trees and AVL Trees [3.5 Marks]

### Question:
Explain the need for balanced trees. What is an AVL tree? Define: (i) balance factor, (ii) balance condition, (iii) types of rotations (LL, RR, LR, RR), (iv) when rotations are triggered. Provide example of insertion that requires rotation.

### Answer:

**Need for Balanced Trees [0.75 marks]:**
Binary Search Tree search/insert/delete is O(log n) only if balanced. If we insert sorted data (1, 2, 3, ..., n), BST degenerates into linked list with O(n) complexity.

Balanced trees maintain O(log n) guarantee by rebalancing after insertions/deletions.

**AVL Tree Definition [0.75 marks]:**
AVL (Adelson-Velsky-Landis) Tree is a self-balancing BST where:
- For every node, height difference between left and right subtrees ≤ 1
- This balance condition is maintained through rotations

**Balance Factor [0.5 marks]:**
- Balance Factor (BF) = height(left subtree) - height(right subtree)
- Valid range: -1, 0, +1
- If BF ∉ [-1, 0, +1], tree is unbalanced; rotation needed

**Rotation Types [1 mark]:**

1. **LL Rotation (Left-Left case):** Inserted in left subtree of left child
   ```
   Before:          After:
        z                y
       /               /   \
      y          →    x     z
     /
    x
   ```

2. **RR Rotation (Right-Right case):** Inserted in right subtree of right child
   ```
   Before:          After:
    x                  y
     \               /   \
      y       →     x     z
       \
        z
   ```

3. **LR Rotation (Left-Right case):** Inserted in right subtree of left child
   - First rotate left child left (LR → LL)
   - Then rotate parent right

4. **RL Rotation (Right-Left case):** Inserted in left subtree of right child
   - First rotate right child right (RL → RR)
   - Then rotate parent left

**When Rotations Triggered [0.5 marks]:**
After insertion/deletion, traverse from modified node up to root. If any node has BF ∉ [-1, 0, +1], perform appropriate rotation and continue checking.

**Insertion Example Requiring Rotation:**

Insert 10, 20, 30 into initially empty AVL:

```
Insert 10:       10 (BF=0, balanced)

Insert 20:       10 (BF=-1, balanced)
                   \
                    20

Insert 30:       10 (BF=-2, UNBALANCED!)
                   \
                    20
                     \
                      30

Apply RR Rotation:       20 (Balanced, BF=0)
                       /    \
                      10    30
```

---

## Q11. Heap Data Structure [3 Marks]

### Question:
Define heap. Explain: (i) min-heap and max-heap properties, (ii) heap as complete binary tree, (iii) array representation with index formulas, (iv) heapify operation (down-heapify), (v) complexity of build heap from n elements.

### Answer:

**Heap Definition [0.5 marks]:**
Heap is a complete binary tree where each parent node's value satisfies heap property relative to children. Used for priority queues, heap sort.

**Min-Heap vs. Max-Heap [0.75 marks]:**
- **Min-Heap:** Parent ≤ children; smallest element at root
- **Max-Heap:** Parent ≥ children; largest element at root
- **Example:**
  ```
  Min-Heap:        1              Max-Heap:        50
                 /   \                            /   \
                3     5                          30    20
               / \                              / \
              7   4                            10  15
  ```

**Complete Binary Tree Property [0.5 marks]:**
Heap is complete binary tree: all levels filled except possibly last, which is filled left-to-right. This allows efficient array representation without gaps.

**Array Representation [0.75 marks]:**
Store heap level-by-level in array (1-indexed for formula simplicity):
```
Array: [_, 1, 3, 5, 7, 4, ...]

For node at index i:
- Left child: 2i
- Right child: 2i + 1
- Parent: i/2 (integer division)

Example: Node at index 2 has:
- Left child at 2×2 = 4 (value 7)
- Right child at 2×2+1 = 5 (value 4)
- Parent at 2/2 = 1 (value 1)
```

**Heapify (Down-Heapify) [0.75 marks]:**
Restore heap property when parent violates it.

```
downHeapify(i):
    smallest = i
    left = 2i
    right = 2i + 1
    
    if (left < size AND arr[left] < arr[smallest])
        smallest = left
    if (right < size AND arr[right] < arr[smallest])
        smallest = right
    
    if (smallest != i):
        swap(arr[i], arr[smallest])
        downHeapify(smallest)
```

**Complexity:** O(log n) - at most h recursive calls where h = height = log n

---

## Q12. Graph Representation Methods [2.5 Marks]

### Question:
Explain two main ways to represent graphs: (i) adjacency matrix, (ii) adjacency list. For each: explain structure, time complexity for common operations (add edge, check if edge exists, find neighbors), space complexity, advantages, and when to use.

### Answer:

**Adjacency Matrix [1.25 marks]:**
2D array where A[i][j] = weight if edge exists, 0 or ∞ if not

```
Example graph: 0→1, 0→2, 1→2
     0  1  2
0  [ 0  1  1 ]
1  [ 0  0  1 ]
2  [ 0  0  0 ]
```

- **Add edge:** O(1) - set A[u][v] = weight
- **Check if edge (u,v) exists:** O(1) - check A[u][v]
- **Find all neighbors of u:** O(V) - scan row u
- **Space:** O(V²)
- **Advantages:** O(1) edge queries; good for dense graphs
- **When to use:** Dense graphs, frequent edge lookups needed

**Adjacency List [1.25 marks]:**
Array of linked lists; A[i] contains list of neighbors of vertex i

```
Example graph: 0→1, 0→2, 1→2
0: [1, 2]
1: [2]
2: []
```

- **Add edge:** O(1) - append to list
- **Check if edge (u,v) exists:** O(degree(u)) - scan u's list
- **Find all neighbors of u:** O(degree(u))
- **Space:** O(V + E)
- **Advantages:** Space-efficient for sparse graphs; fast neighbor iteration
- **When to use:** Sparse graphs, traversal-heavy algorithms (DFS, BFS)

**Comparison Table:**

| Operation | Adjacency Matrix | Adjacency List |
|-----------|-----------------|-----------------|
| **Add edge** | O(1) | O(1) |
| **Check edge** | O(1) | O(degree) |
| **Find neighbors** | O(V) | O(degree) |
| **Space** | O(V²) | O(V+E) |
| **Best for** | Dense graphs, frequent lookups | Sparse graphs, traversals |

---

## Q13. Graph Traversal - DFS and BFS [3.5 Marks]

### Question:
Explain Depth-First Search (DFS) and Breadth-First Search (BFS). For each: (i) algorithm pseudocode, (ii) data structure used, (iii) time complexity, (iv) use cases, (v) provide example with output.

### Answer:

**DFS - Depth-First Search [1.75 marks]:**

```
dfs(v, visited):
    visited[v] = TRUE
    process(v)
    for each neighbor u of v:
        if (NOT visited[u]):
            dfs(u, visited)

// Iterative using stack:
dfs(start):
    stack S = empty
    S.push(start)
    visited[start] = TRUE
    while (S not empty):
        v = S.pop()
        process(v)
        for each neighbor u of v:
            if (NOT visited[u]):
                visited[u] = TRUE
                S.push(u)
```

- **Data structure:** Recursion stack (implicit) or explicit stack
- **Time:** O(V + E) - visit each vertex once, traverse each edge once
- **Use cases:** Topological sort, detect cycles, connected components, maze solving
- **Example Graph:**
  ```
  Graph: 0→1,2  1→3  2→1
  DFS from 0: 0, 1, 3, 2
  ```

**BFS - Breadth-First Search [1.75 marks]:**

```
bfs(start):
    queue Q = empty
    Q.enqueue(start)
    visited[start] = TRUE
    while (Q not empty):
        v = Q.dequeue()
        process(v)
        for each neighbor u of v:
            if (NOT visited[u]):
                visited[u] = TRUE
                Q.enqueue(u)
```

- **Data structure:** Queue
- **Time:** O(V + E) - same as DFS
- **Use cases:** Shortest path (unweighted), level-order traversal, connected components
- **Example Graph:**
  ```
  Graph: 0→1,2  1→3  2→1
  BFS from 0: 0, 1, 2, 3 (level by level)
  ```

**Comparison:**

| Aspect | DFS | BFS |
|--------|-----|-----|
| **Data Structure** | Stack | Queue |
| **Traversal Order** | Deep first | Level by level |
| **Memory** | O(h) height | O(w) width |
| **Path Finding** | Any path | Shortest path |
| **Use Case** | Cycle detection, topological sort | Shortest path, level-order |

---

## Q14. Sorting Algorithm Comparison [3 Marks]

### Question:
Compare four sorting algorithms: (i) bubble sort, (ii) insertion sort, (iii) merge sort, (iv) quick sort. Create table showing: time complexity (best/average/worst), space complexity, stability, in-place, use cases, and explain why some are preferred.

### Answer:

**Sorting Algorithm Comparison Table [3 marks]:**

| Algorithm | Best Case | Average Case | Worst Case | Space | Stable | In-Place | Use Case |
|-----------|-----------|------------|-----------|-------|--------|----------|----------|
| **Bubble Sort** | O(n) | O(n²) | O(n²) | O(1) | Yes | Yes | Educational; nearly sorted small data |
| **Insertion Sort** | O(n) | O(n²) | O(n²) | O(1) | Yes | Yes | Small arrays; nearly sorted; online sorting |
| **Merge Sort** | O(n log n) | O(n log n) | O(n log n) | O(n) | Yes | No | Large datasets; stable sorting needed; external sort |
| **Quick Sort** | O(n log n) | O(n log n) | O(n²) | O(log n) | No | Yes | General purpose; cache-friendly; average O(n log n) |

**Why Some Are Preferred:**

1. **Merge Sort:** 
   - Guaranteed O(n log n) even in worst case
   - Stable (preserves relative order)
   - External sorting (can sort files larger than RAM)
   - Trade-off: requires O(n) extra space

2. **Quick Sort:**
   - Average O(n log n) with good constants
   - In-place (O(log n) space for recursion)
   - Cache-friendly (locality of reference)
   - Often faster in practice than merge sort
   - Risk: O(n²) on bad pivots (mitigated by random/median-of-3)

3. **Insertion Sort:**
   - Best for small arrays (n < 50)
   - Best for nearly-sorted data (O(n) best case)
   - Online (can sort stream without knowing full data)
   - Used in hybrid sorts (introsort combines quick + insertion)

4. **Bubble Sort:**
   - Rarely used in practice
   - Educational value only
   - Simple implementation; slow performance

---

## Q15. String Matching - Naive Algorithm [2.5 Marks]

### Question:
Explain the naive string matching algorithm. Provide: (i) algorithm pseudocode, (ii) example with pattern "AB" in text "AAAB", (iii) time complexity analysis (best/average/worst), (iv) why naive can be inefficient, (v) brief mention of better algorithms.

### Answer:

**Naive String Matching Algorithm [0.75 marks]:**

```
naiveStringMatch(text, pattern):
    n = length(text)
    m = length(pattern)
    for i = 0 to n - m:          // Try each position in text
        match = TRUE
        for j = 0 to m - 1:      // Check if pattern matches at position i
            if (text[i+j] != pattern[j]):
                match = FALSE
                break
        if (match):
            return i             // Found match at position i
    return NOT_FOUND
```

**Example with text="AAAB", pattern="AB" [0.75 marks]:**

```
Position 0: Compare "AA" with "AB"
  A == A ✓
  A != B ✗  → Move to next position

Position 1: Compare "AA" with "AB"
  A == A ✓
  A != B ✗  → Move to next position

Position 2: Compare "AB" with "AB"
  A == A ✓
  B == B ✓  → MATCH FOUND at position 2
```

**Time Complexity Analysis [1 mark]:**

- **Best case:** O(n) - pattern found immediately
- **Average case:** O(n*m) - checking m characters at each of n positions
- **Worst case:** O(n*m) - pattern never matches; check all n positions with m comparisons each
- **Example:** Text "AAAAA...AAB" (n-1 A's then B), pattern "AAAB" requires comparing at every position

**Why Naive Can Be Inefficient [0.25 marks]:**
For each mismatch, we restart matching from first character of pattern, discarding information about already-matched characters. We don't use patterns' structure to skip positions.

**Better Algorithms:**
- **KMP (Knuth-Morris-Pratt):** O(n+m) using failure function
- **Boyer-Moore:** O(n/m) average; skips characters based on pattern
- **Rabin-Karp:** O(n+m) average using rolling hash

---

## Marks Summary
- Q1: 2.5 marks
- Q2: 3 marks
- Q3: 4 marks
- Q4: 3.5 marks
- Q5: 3.5 marks
- Q6: 3 marks
- Q7: 3 marks
- Q8: 3.5 marks
- Q9: 3 marks
- Q10: 3.5 marks
- Q11: 3 marks
- Q12: 2.5 marks
- Q13: 3.5 marks
- Q14: 3 marks
- Q15: 2.5 marks

**Total: 46 marks**

