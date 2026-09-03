# Chapter 2: Elementary Data Structures — Questions and Answers

> BITS Pilani — 30 questions covering Stacks, Queues, Amortised Analysis, and Lists.
> References: R1 (CLRS) 10.1, 10.2, 17.1

---
---

## Stacks (Q1–Q8)

---

### Q1. Define the Stack ADT. What principle does it follow?

**Answer:**

A **stack** is a linear data structure following the **LIFO (Last In, First Out)** principle — the most recently inserted element is the first one removed.

| Operation | Description | Time |
|-----------|-------------|------|
| push(x) | Insert x on top | O(1) |
| pop() | Remove and return top element | O(1) |
| top() / peek() | Return top without removing | O(1) |
| isEmpty() | Check if stack is empty | O(1) |

Precondition: pop() and top() require the stack to be non-empty (underflow error otherwise).

---

### Q2. Compare array-based and linked-list-based stack implementations.

**Answer:**

| Aspect | Array-Based | Linked-List-Based |
|--------|-------------|-------------------|
| Size | Fixed (or dynamic with resizing) | Dynamic — grows as needed |
| Push/Pop | O(1) | O(1) (at head) |
| Memory | Contiguous; cache-friendly | Scattered; extra pointer per node |
| Overflow | Possible (fixed array) | Only when system memory exhausted |
| Wasted space | Unused capacity slots | Pointer overhead per element |
| Implementation | Simpler | Slightly more complex (allocation) |

**Choose array** when maximum size is known and cache performance matters.
**Choose linked list** when size is unpredictable.

---

### Q3. Convert the infix expression `A * (B + C) - D / E` to postfix using a stack.

**Answer:**

| Token | Action | Stack | Output |
|-------|--------|-------|--------|
| A | Output | | A |
| * | Push | [*] | A |
| ( | Push | [*, (] | A |
| B | Output | [*, (] | A B |
| + | Push | [*, (, +] | A B |
| C | Output | [*, (, +] | A B C |
| ) | Pop until ( | [*] | A B C + |
| - | Pop * (≥ precedence), Push - | [-] | A B C + * |
| D | Output | [-] | A B C + * D |
| / | Push (/ > -) | [-, /] | A B C + * D |
| E | Output | [-, /] | A B C + * D E |
| End | Pop all | [] | A B C + * D E / - |

**Postfix: `A B C + * D E / -`**

---

### Q4. Evaluate the postfix expression `6 2 3 + - 3 8 2 / + *`

**Answer:**

| Token | Action | Stack |
|-------|--------|-------|
| 6 | push | [6] |
| 2 | push | [6, 2] |
| 3 | push | [6, 2, 3] |
| + | pop 3,2 → 2+3=5 | [6, 5] |
| - | pop 5,6 → 6-5=1 | [1] |
| 3 | push | [1, 3] |
| 8 | push | [1, 3, 8] |
| 2 | push | [1, 3, 8, 2] |
| / | pop 2,8 → 8/2=4 | [1, 3, 4] |
| + | pop 4,3 → 3+4=7 | [1, 7] |
| * | pop 7,1 → 1*7=7 | [7] |

**Result: 7**

---

### Q5. How can you implement two stacks in a single array efficiently?

**Answer:**

Use one array of size n. Stack 1 grows from the **left** (index 0 upward). Stack 2 grows from the **right** (index n-1 downward).

```
Stack 1:  → push at top1++
Stack 2:  ← push at top2--

Array: [S1_0, S1_1, ..., S1_top1, ..., S2_top2, ..., S2_n-1]

Overflow condition: top1 + 1 == top2 (they meet in the middle)
```

```
init:
    top1 = -1
    top2 = n

push1(x):
    if top1 + 1 == top2: error "Overflow"
    top1++; A[top1] = x

push2(x):
    if top1 + 1 == top2: error "Overflow"
    top2--; A[top2] = x

pop1(): return A[top1--]
pop2(): return A[top2++]
```

**Advantage:** Space is shared — if one stack uses less, the other can use more. Total capacity = n.

---

### Q6. Check if the string `{[()]}` is balanced using a stack. Show the trace.

**Answer:**

| Char | Action | Stack |
|------|--------|-------|
| { | Push | [{] |
| [ | Push | [{, [] |
| ( | Push | [{, [, (] |
| ) | Pop ( → matches ) ✓ | [{, [] |
| ] | Pop [ → matches ] ✓ | [{] |
| } | Pop { → matches } ✓ | [] |

Stack empty at end → **Balanced ✓**

---

### Q7. How is the function call stack used during recursion? Explain with factorial(3).

**Answer:**

Each function call pushes a **stack frame** containing local variables, parameters, and the return address.

```
Call factorial(3):
    Stack: [factorial(3)]
    3 * factorial(2)

Call factorial(2):
    Stack: [factorial(3), factorial(2)]
    2 * factorial(1)

Call factorial(1):
    Stack: [factorial(3), factorial(2), factorial(1)]
    return 1  ← base case

Return to factorial(2): 2 * 1 = 2
    Stack: [factorial(3), factorial(2)]
    Pop factorial(1)

Return to factorial(3): 3 * 2 = 6
    Stack: [factorial(3)]
    Pop factorial(2)

Return 6.
    Stack: []
    Pop factorial(3)
```

**Stack overflow** occurs when recursion depth exceeds the system stack limit (e.g. factorial(100000) without tail-call optimisation).

---

### Q8. List 5 applications of stacks.

**Answer:**

1. **Parenthesis/bracket matching** — push opening, pop and match on closing.
2. **Infix to postfix conversion** — Shunting-Yard algorithm uses an operator stack.
3. **Postfix expression evaluation** — operands pushed, operators pop and compute.
4. **Function call stack** — tracks active function calls and local variables during recursion.
5. **Undo mechanism** — each action pushed; undo pops the last action.
6. **DFS (iterative)** — vertices pushed onto stack instead of using recursion.
7. **Backtracking** — exploring paths in mazes, N-Queens, Sudoku.

---
---

## Queues (Q9–Q16)

---

### Q9. Define the Queue ADT. How is it different from a stack?

**Answer:**

A **queue** follows **FIFO (First In, First Out)** — the first element inserted is the first removed.

| | Stack (LIFO) | Queue (FIFO) |
|---|---|---|
| Insert | push (top) | enqueue (rear) |
| Remove | pop (top) | dequeue (front) |
| Analogy | Stack of plates | Ticket counter line |

Both support O(1) insertion and removal, but from different ends.

---

### Q10. Why is a circular array better than a linear array for implementing a queue?

**Answer:**

**Linear array problem:** After several enqueue/dequeue operations, the front pointer moves right, leaving wasted empty slots at the beginning that cannot be reused without shifting all elements (O(n)).

```
Linear: [_, _, _, 4, 5, 6, _, _]   ← slots 0-2 wasted after dequeues
                 ↑front     ↑rear
```

**Circular array solution:** When rear reaches the end, it wraps to index 0 using modular arithmetic: `rear = (rear + 1) % capacity`.

```
Circular: [7, 8, _, 4, 5, 6]   ← slots 0-1 reused!
              ↑rear  ↑front
```

**No wasted space. No shifting. All operations remain O(1).**

---

### Q11. Implement a queue using two stacks. Show the trace for enqueue(1), enqueue(2), dequeue(), enqueue(3), dequeue().

**Answer:**

**Implementation:**
- `S1` = input stack (enqueue pushes here)
- `S2` = output stack (dequeue pops from here; if empty, transfer all from S1)

**Trace:**

| Operation | S1 | S2 | Output |
|-----------|----|----|--------|
| enqueue(1) | [1] | [] | |
| enqueue(2) | [1, 2] | [] | |
| dequeue() | [] | [2, 1] (transferred) → pop → 1 | **1** |
| | [] | [2] | |
| enqueue(3) | [3] | [2] | |
| dequeue() | [3] | [2] → pop → 2 | **2** |
| | [3] | [] | |

**Key insight:** Each element moves at most once from S1 to S2. Amortised O(1) per operation.

---

### Q12. What is a Deque? When would you use it?

**Answer:**

A **Deque (Double-Ended Queue)** allows insertion and removal at **both** front and rear in O(1).

| Operation | Time |
|-----------|------|
| insertFront(x) | O(1) |
| insertRear(x) | O(1) |
| removeFront() | O(1) |
| removeRear() | O(1) |

**Use cases:**
- **Sliding window maximum** — Maintain a deque of indices; remove from front (out of window) and rear (smaller elements).
- **0-1 BFS** — Push 0-weight edges to front, 1-weight edges to rear.
- **Palindrome check** — Compare front and rear, shrinking inward.
- A deque can simulate both a stack (use one end) and a queue (use both ends).

---

### Q13. Implement a stack using two queues. What is the time complexity?

**Answer:**

**Approach (make push expensive):**

```
push(x):
    enqueue x onto Q2
    while Q1 is not empty:
        dequeue from Q1 and enqueue onto Q2
    swap Q1 and Q2

pop():
    return dequeue from Q1
```

After push, Q1 always has elements in stack order (most recent at front).

**Trace: push(1), push(2), push(3):**

| Operation | Q1 | Q2 |
|-----------|----|----|
| push(1) | [1] | [] |
| push(2) | [2, 1] | [] |
| push(3) | [3, 2, 1] | [] |
| pop() → 3 | [2, 1] | [] |

**Complexity:** push = O(n), pop = O(1).

Alternative approach makes pop expensive: push = O(1), pop = O(n).

---

### Q14. Write BFS using a queue and trace it on this graph from vertex A.

```
A — B
|   |
C — D — E
```

**Answer:**

```
BFS(A):
    Queue: [A], Visited: {A}
    
    Dequeue A → process A. Neighbours: B, C
    Queue: [B, C], Visited: {A, B, C}
    
    Dequeue B → process B. Neighbours: A(visited), D
    Queue: [C, D], Visited: {A, B, C, D}
    
    Dequeue C → process C. Neighbours: A(visited), D(visited)
    Queue: [D]
    
    Dequeue D → process D. Neighbours: B(visited), C(visited), E
    Queue: [E], Visited: {A, B, C, D, E}
    
    Dequeue E → process E. No unvisited neighbours.
    Queue: []
```

**BFS order: A → B → C → D → E**

---

### Q15. What is a priority queue? How is it different from a regular queue?

**Answer:**

| Aspect | Regular Queue | Priority Queue |
|--------|--------------|----------------|
| Order | FIFO — first in, first out | Highest/lowest priority out first |
| Insert | O(1) | O(log n) — heap insert |
| Remove | O(1) | O(log n) — heap extract |
| Implementation | Array/linked list | Binary heap (most common) |
| Use cases | BFS, scheduling | Dijkstra's, Huffman coding, event simulation |

A priority queue does NOT preserve insertion order — it serves elements by priority regardless of when they arrived.

---

### Q16. List 5 applications of queues.

**Answer:**

1. **BFS graph traversal** — vertices discovered in order of distance.
2. **Level-order tree traversal** — visit nodes level by level.
3. **Task scheduling (OS)** — ready queue, I/O request queue.
4. **Print spooling** — print jobs served in arrival order.
5. **Producer-consumer buffer** — decouples data producers from consumers.
6. **Simulation** — modelling real-world queues (banks, traffic).

---
---

## Amortised Analysis (Q17–Q23)

---

### Q17. What is amortised analysis? How is it different from average-case analysis?

**Answer:**

| Aspect | Amortised Analysis | Average-Case Analysis |
|--------|-------------------|----------------------|
| What it measures | Average cost per operation over a **worst-case sequence** of n operations. | Expected cost of a single operation assuming a **probability distribution** over inputs. |
| Probability? | **No** — no assumptions about input distribution. | **Yes** — requires a probabilistic model. |
| Guarantee | Guaranteed bound for any sequence. | Expected bound over random inputs. |
| Example | Dynamic array insert = O(1) amortised | Quick Sort = O(n log n) average |

Amortised analysis is **deterministic** — it works for the worst possible sequence of operations.

---

### Q18. Explain the Aggregate Method with the MULTIPOP stack example.

**Answer:**

**Setup:** Stack supports PUSH (cost 1), POP (cost 1), MULTIPOP(k) (cost min(k, stack_size)).

**Naive worst case:** MULTIPOP can cost O(n) → n operations could be O(n²). **Too pessimistic!**

**Aggregate method:**
- Each element is pushed at most once → total push cost ≤ n.
- Each element is popped at most once (whether by POP or MULTIPOP) → total pop cost ≤ n.
- Total cost ≤ 2n.
- **Amortised cost = 2n / n = O(1) per operation.**

The key insight: **you can't pop more than you've pushed**.

---

### Q19. Prove that a queue implemented with two stacks has O(1) amortised cost per operation.

**Answer:**

**Implementation:** S1 for enqueue (push), S2 for dequeue (pop). When S2 is empty, transfer all elements from S1 to S2.

**Aggregate analysis:** Each element undergoes at most 4 operations in its lifetime:
1. Push onto S1 (during enqueue) — cost 1
2. Pop from S1 (during transfer) — cost 1
3. Push onto S2 (during transfer) — cost 1
4. Pop from S2 (during dequeue) — cost 1

Total cost per element = 4. Over n operations with at most n elements:

**Total cost ≤ 4n. Amortised cost = 4n/n = O(1).** ∎

---

### Q20. Prove that dynamic array insertion has O(1) amortised cost using the Aggregate Method.

**Answer:**

Starting from capacity 1, doubling when full.

**Cost of n insertions:**
- Normal insert (no resize): cost 1. Happens n times → total n.
- Resize at sizes 1, 2, 4, 8, ..., 2^k where 2^k ≤ n:
  - Copy costs: 1 + 2 + 4 + 8 + ... + 2^k = 2^(k+1) - 1 < 2n.

**Total cost < n + 2n = 3n.**

**Amortised cost = 3n / n = O(1).** ∎

---

### Q21. Why does doubling capacity give O(1) amortised, but adding a constant gives O(n) amortised?

**Answer:**

**Doubling (multiply by 2):**
- Resizes at insertions: 1, 2, 4, 8, ..., n.
- Total copies: 1 + 2 + 4 + ... + n = O(n) (geometric series).
- Amortised: O(n)/n = **O(1)**.

**Adding constant c:**
- Resizes at insertions: c, 2c, 3c, ..., n.
- Number of resizes = n/c.
- Total copies: c + 2c + 3c + ... + (n/c)·c = c · (n/c)(n/c + 1)/2 = **O(n²/c)** = O(n²).
- Amortised: O(n²)/n = **O(n)**.

**Geometric growth (doubling)** makes resize costs decrease relative to the number of insertions between resizes. **Arithmetic growth** does not.

---

### Q22. Trace the amortised cost of 8 insertions into a dynamic array starting from capacity 1.

**Answer:**

| Insert # | Capacity before | Resize? | Copy cost | Insert cost | Total step cost | Array |
|----------|----------------|---------|-----------|-------------|-----------------|-------|
| 1 | 1 | Yes → 2 | 1 | 1 | 2 | [1] |
| 2 | 2 | Yes → 4 | 2 | 1 | 3 | [1, 2] |
| 3 | 4 | No | 0 | 1 | 1 | [1, 2, 3] |
| 4 | 4 | Yes → 8 | 4 | 1 | 5 | [1, 2, 3, 4] |
| 5 | 8 | No | 0 | 1 | 1 | [1, 2, 3, 4, 5] |
| 6 | 8 | No | 0 | 1 | 1 | [..., 6] |
| 7 | 8 | No | 0 | 1 | 1 | [..., 7] |
| 8 | 8 | Yes → 16 | 8 | 1 | 9 | [..., 8] |

**Total cost: 2+3+1+5+1+1+1+9 = 23**
**Amortised: 23/8 ≈ 2.88 = O(1) ✓**

---

### Q23. A binary counter increments from 0 to n. Prove the amortised cost per increment is O(1).

**Answer:**

A k-bit binary counter: each INCREMENT flips some bits from 1 to 0 and one bit from 0 to 1.

**Aggregate analysis:**
- Bit 0 flips every increment → n flips total.
- Bit 1 flips every 2nd increment → n/2 flips.
- Bit 2 flips every 4th increment → n/4 flips.
- Bit i flips n/2^i times.

**Total flips = n + n/2 + n/4 + ... = n · (1 + 1/2 + 1/4 + ...) < 2n.**

**Amortised cost per increment = 2n/n = O(1).** ∎

---
---

## Lists (Q24–Q30)

---

### Q24. What is the key advantage of a doubly linked list over a singly linked list?

**Answer:**

**O(1) deletion given a node pointer.**

In a singly linked list, to delete node X, you must traverse from the head to find X's predecessor (O(n)). In a doubly linked list, X already knows its predecessor via `X.prev`, so you can update the links in O(1):

```
X.prev.next = X.next
X.next.prev = X.prev
free(X)
```

This is critical for:
- **LRU Cache** — move accessed node to front in O(1).
- **Efficient removal** during graph algorithms.

---

### Q25. Reverse a singly linked list. Trace on 1→2→3→NULL.

**Answer:**

```
reverse(head):
    prev = NULL, curr = head
    while curr ≠ NULL:
        next = curr.next
        curr.next = prev
        prev = curr
        curr = next
    return prev
```

| Step | prev | curr | next | List state |
|------|------|------|------|------------|
| 0 | NULL | 1 | — | 1→2→3→NULL |
| 1 | NULL | 1 | 2 | NULL←1  2→3→NULL |
| 2 | 1 | 2 | 3 | NULL←1←2  3→NULL |
| 3 | 2 | 3 | NULL | NULL←1←2←3 |
| End | 3 | NULL | — | 3→2→1→NULL ✓ |

**New head: 3. Time: O(n). Space: O(1).**

---

### Q26. Detect a cycle in a linked list using Floyd's algorithm. Why does it work?

**Answer:**

```
hasCycle(head):
    slow = fast = head
    while fast ≠ NULL AND fast.next ≠ NULL:
        slow = slow.next          // 1 step
        fast = fast.next.next     // 2 steps
        if slow == fast: return True
    return False
```

**Why it works:**
- If no cycle: fast reaches NULL → return False.
- If cycle exists: once both pointers are inside the cycle, fast gains 1 step on slow each iteration. Since the cycle has finite length L, fast catches slow within L iterations.

**Finding cycle start:** After detection (slow == fast), reset slow to head. Move both one step at a time. They meet at the **cycle start**.

**Proof:** Let d = distance from head to cycle start, and k = distance from cycle start to meeting point. When they meet: slow traveled d+k steps, fast traveled 2(d+k) steps. The difference d+k is a multiple of cycle length L. So d+k = mL → d = mL - k. Starting from head (distance d to cycle start) and from meeting point (distance L-k to cycle start): both reach the cycle start after d steps.

---

### Q27. Design an LRU Cache using a doubly linked list + hash map.

**Answer:**

**Data structures:**
- **Doubly linked list:** Stores key-value pairs. Most recently used at **head**, least recently used at **tail**.
- **Hash map:** Maps key → node pointer for O(1) lookup.

**Operations:**

```
get(key):
    if key in hashmap:
        node = hashmap[key]
        moveToHead(node)       // mark as recently used
        return node.value
    return -1                  // not found

put(key, value):
    if key in hashmap:
        node = hashmap[key]
        node.value = value
        moveToHead(node)
    else:
        newNode = create Node(key, value)
        hashmap[key] = newNode
        addToHead(newNode)
        if size > capacity:
            tail = removeTail()    // evict LRU
            delete hashmap[tail.key]
```

**All operations: O(1).**

The doubly linked list enables O(1) removal (for moveToHead and removeTail). The hash map enables O(1) lookup.

---

### Q28. When should you use an array vs a linked list?

**Answer:**

| Use Array When | Use Linked List When |
|----------------|---------------------|
| Frequent random access (index-based lookups). | Frequent insertions/deletions at arbitrary positions. |
| Size is known or predictable. | Size is highly dynamic/unpredictable. |
| Cache performance matters (contiguous memory). | Memory fragmentation is acceptable. |
| Need binary search on sorted data. | Implementing stacks/queues (operations at ends). |
| Memory efficiency matters (no pointer overhead). | Need O(1) deletion given a node pointer (doubly linked). |

**General rule:** Default to arrays. Use linked lists only when insertion/deletion at arbitrary positions is frequent and access-by-index is rare.

---

### Q29. Implement a stack using a singly linked list. Write push, pop, and isEmpty in C.

**Answer:**

```c
struct Node {
    int data;
    struct Node* next;
};

struct Stack {
    struct Node* head;
    int size;
};

void init(struct Stack* s) {
    s->head = NULL;
    s->size = 0;
}

int isEmpty(struct Stack* s) {
    return s->head == NULL;
}

void push(struct Stack* s, int x) {
    struct Node* newNode = (struct Node*)malloc(sizeof(struct Node));
    newNode->data = x;
    newNode->next = s->head;
    s->head = newNode;
    s->size++;
}

int pop(struct Stack* s) {
    if (isEmpty(s)) {
        printf("Underflow\n");
        return -1;
    }
    struct Node* temp = s->head;
    int val = temp->data;
    s->head = s->head->next;
    free(temp);
    s->size--;
    return val;
}
```

**All operations O(1). No overflow (until system memory exhausted).**

---

### Q30. Find the middle element of a linked list in one pass.

**Answer:**

Use the **slow-fast pointer** technique:

```
findMiddle(head):
    slow = head
    fast = head
    while fast ≠ NULL AND fast.next ≠ NULL:
        slow = slow.next           // 1 step
        fast = fast.next.next      // 2 steps
    return slow                    // slow is at the middle
```

**Why it works:** When fast reaches the end (having traversed n nodes), slow has traversed n/2 nodes — exactly the middle.

**Example: 1→2→3→4→5**
| Step | slow | fast |
|------|------|------|
| 0 | 1 | 1 |
| 1 | 2 | 3 |
| 2 | 3 | 5 |
| 3 | — | NULL (fast.next = NULL, stop) |

**Middle: 3 ✓**

**Time: O(n). Space: O(1). Single pass.**

---

*End of Chapter 2 Questions and Answers*
