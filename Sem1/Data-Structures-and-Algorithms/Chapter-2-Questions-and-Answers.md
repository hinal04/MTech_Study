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
---

## Practice Problems — Stacks, Queues & Linked Lists (Q31–Q44)

> Additional practice questions based on class material covering stack problems, queue problems, and linked list operations.

---

### Q31. Reverse a string using a stack. Trace the execution for the string "HELLO".

**Answer:**

```
Algorithm ReverseString(str, n)
    S ← empty stack
    for i ← 0 to n-1 do
        S.push(str[i])
    result ← ""
    while S is not empty do
        result ← result + S.pop()
    return result
```

**Trace for "HELLO":**

| Step | Operation | Stack | Result |
|------|-----------|-------|--------|
| 1 | push('H') | [H] | "" |
| 2 | push('E') | [H, E] | "" |
| 3 | push('L') | [H, E, L] | "" |
| 4 | push('L') | [H, E, L, L] | "" |
| 5 | push('O') | [H, E, L, L, O] | "" |
| 6 | pop() → 'O' | [H, E, L, L] | "O" |
| 7 | pop() → 'L' | [H, E, L] | "OL" |
| 8 | pop() → 'L' | [H, E] | "OLL" |
| 9 | pop() → 'E' | [H] | "OLLE" |
| 10 | pop() → 'H' | [] | "OLLEH" |

**Result: "OLLEH"** ✓

**Time: O(n). Space: O(n).**

---

### Q32. Check if a string is a palindrome using a stack. Trace for "RACECAR" and "HELLO".

**Answer:**

```
Algorithm IsPalindrome(str, n)
    S ← empty stack
    for i ← 0 to n-1 do
        S.push(str[i])
    for i ← 0 to n-1 do
        if str[i] ≠ S.pop() then
            return FALSE
    return TRUE
```

**Trace for "RACECAR" (n=7):**

| Step | str[i] | pop() | Match? |
|------|--------|-------|--------|
| 1 | R | R | ✓ |
| 2 | A | A | ✓ |
| 3 | C | C | ✓ |
| 4 | E | E | ✓ |
| 5 | C | C | ✓ |
| 6 | A | A | ✓ |
| 7 | R | R | ✓ |

**Result: TRUE** — "RACECAR" is a palindrome ✓

**Trace for "HELLO":**
Stack after pushing: [H, E, L, L, O]

| Step | str[i] | pop() | Match? |
|------|--------|-------|--------|
| 1 | H | O | ✗ → return FALSE |

**Result: FALSE** — "HELLO" is not a palindrome ✓

**Time: O(n). Space: O(n).**

**Optimization:** Only push first half, then compare with second half → halves the comparisons.

---

### Q33. Write an algorithm to find the sum of all elements in a stack without losing the data. Trace for stack [5, 10, 15, 20] (20 on top).

**Answer:**

```
Algorithm SumOfStack(S)
    tempStack ← empty stack
    sum ← 0
    // Pop all, accumulate sum, save to temp
    while S is not empty do
        val ← S.pop()
        sum ← sum + val
        tempStack.push(val)
    // Restore original stack
    while tempStack is not empty do
        S.push(tempStack.pop())
    return sum
```

**Trace:**

| Step | S.pop() | sum | S | tempStack |
|------|---------|-----|---|-----------|
| 1 | 20 | 20 | [5, 10, 15] | [20] |
| 2 | 15 | 35 | [5, 10] | [20, 15] |
| 3 | 10 | 45 | [5] | [20, 15, 10] |
| 4 | 5 | 50 | [] | [20, 15, 10, 5] |

Restore: push 5, 10, 15, 20 back → S = [5, 10, 15, 20] (original order restored).

**Sum = 50** ✓. Stack preserved.

**Time: O(n). Space: O(n)** (for temp stack).

---

### Q34. Insert an element at the bottom of a stack using recursion. Trace inserting 0 at the bottom of [1, 2, 3] (3 on top).

**Answer:**

```
Algorithm InsertAtBottom(S, item)
    if S.isEmpty() then
        S.push(item)
    else
        top ← S.pop()
        InsertAtBottom(S, item)
        S.push(top)
```

**Trace: Insert 0 at bottom of [1, 2, 3]:**

```
InsertAtBottom(S=[1,2,3], 0)
  top = 3, pop → S = [1, 2]
  InsertAtBottom(S=[1,2], 0)
    top = 2, pop → S = [1]
    InsertAtBottom(S=[1], 0)
      top = 1, pop → S = []
      InsertAtBottom(S=[], 0)
        S is empty → push(0) → S = [0]
      push(1) → S = [0, 1]
    push(2) → S = [0, 1, 2]
  push(3) → S = [0, 1, 2, 3]
```

**Result: [0, 1, 2, 3]** (0 at bottom, 3 on top) ✓

**Time: O(n). Space: O(n)** recursion stack.

---

### Q35. Search for an element in a stack. Return its position from top (1-indexed). Trace searching for 15 in [5, 10, 15, 20, 25] (25 on top).

**Answer:**

```
Algorithm SearchStack(S, key)
    tempStack ← empty stack
    position ← 0
    found ← FALSE
    while S is not empty do
        position ← position + 1
        val ← S.pop()
        tempStack.push(val)
        if val = key then
            found ← TRUE
            break
    // Restore stack
    while tempStack is not empty do
        S.push(tempStack.pop())
    if found then return position
    else return -1
```

**Trace: Search for 15 in [5, 10, 15, 20, 25]:**

| Step | pop() | position | Match? | S | tempStack |
|------|-------|----------|--------|---|-----------|
| 1 | 25 | 1 | No | [5,10,15,20] | [25] |
| 2 | 20 | 2 | No | [5,10,15] | [25,20] |
| 3 | 15 | 3 | **Yes** ✓ | [5,10] | [25,20,15] |

Restore: push 15, 20, 25 back.

**Result: position = 3** (3rd from top) ✓

---

### Q36. Convert a decimal number to binary using a stack. Trace for decimal 25.

**Answer:**

```
Algorithm DecimalToBinary(n)
    S ← empty stack
    while n > 0 do
        S.push(n mod 2)
        n ← n / 2    (integer division)
    result ← ""
    while S is not empty do
        result ← result + S.pop()
    return result
```

**Trace for n = 25:**

| Step | n | n mod 2 | n / 2 | Stack |
|------|---|---------|-------|-------|
| 1 | 25 | 1 | 12 | [1] |
| 2 | 12 | 0 | 6 | [1, 0] |
| 3 | 6 | 0 | 3 | [1, 0, 0] |
| 4 | 3 | 1 | 1 | [1, 0, 0, 1] |
| 5 | 1 | 1 | 0 | [1, 0, 0, 1, 1] |

Pop all: 1, 1, 0, 0, 1 → **"11001"**

**Verification:** 1×16 + 1×8 + 0×4 + 0×2 + 1×1 = 16 + 8 + 1 = **25** ✓

---

### Q37. Check if parentheses are balanced in the expression `{[a + b] * (c - d) / (e + f)}`. Support `()`, `[]`, `{}`.

**Answer:**

```
Algorithm IsBalanced(expr, n)
    S ← empty stack
    for i ← 0 to n-1 do
        ch ← expr[i]
        if ch ∈ {'(', '[', '{'} then
            S.push(ch)
        else if ch ∈ {')', ']', '}'} then
            if S.isEmpty() then return FALSE
            top ← S.pop()
            if not isMatchingPair(top, ch) then return FALSE
    return S.isEmpty()
```

**Trace for `{[a + b] * (c - d) / (e + f)}`:**

| Char | Action | Stack |
|------|--------|-------|
| { | Push | [{] |
| [ | Push | [{, [] |
| a,+,b | Skip (not bracket) | [{, [] |
| ] | Pop [ → matches ] ✓ | [{] |
| *,(,c,-,d | Push ( | [{, (] |
| ) | Pop ( → matches ) ✓ | [{] |
| /,(,e,+,f | Push ( | [{, (] |
| ) | Pop ( → matches ) ✓ | [{] |
| } | Pop { → matches } ✓ | [] |

**Stack empty → Balanced ✓**

**Unbalanced example: `{[a + b])`**
At `)`: pop `{` — mismatch! `{` does not match `)`. → **FALSE**

---

### Q38. Count the number of elements in a queue without using a size variable. Trace for queue [10, 20, 30, 40] (front=10).

**Answer:**

```
Algorithm CountQueue(Q)
    count ← 0
    marker ← Q.dequeue()
    Q.enqueue(marker)
    count ← 1
    while Q.front() ≠ marker do
        Q.enqueue(Q.dequeue())
        count ← count + 1
    return count
```

**Trace for [10, 20, 30, 40]:**

| Step | Dequeue | Enqueue | Queue | count |
|------|---------|---------|-------|-------|
| 0 | 10 (marker) | 10 | [20, 30, 40, 10] | 1 |
| 1 | 20 | 20 | [30, 40, 10, 20] | 2 |
| 2 | 30 | 30 | [40, 10, 20, 30] | 3 |
| 3 | 40 | 40 | [10, 20, 30, 40] | 4 |
| 4 | front = 10 = marker → stop | | [10, 20, 30, 40] | 4 |

**Count = 4** ✓. Queue restored to original order.

**Time: O(n). Space: O(1)** (no extra data structure).

---

### Q39. Simulate an ATM queue. Three customers (C1, C2, C3) arrive in order. C1 needs 3 mins, C2 needs 2 mins, C3 needs 5 mins. Show the queue state at each minute.

**Answer:**

| Time | Event | Queue | Being Served | Wait Time |
|------|-------|-------|-------------|-----------|
| t=0 | C1 arrives, starts service | [] | C1 (3 min) | C1: 0 |
| t=1 | C2 arrives, joins queue | [C2] | C1 (2 min left) | |
| t=2 | C3 arrives, joins queue | [C2, C3] | C1 (1 min left) | |
| t=3 | C1 done. Dequeue C2 | [C3] | C2 (2 min) | C2 waited 2 min |
| t=5 | C2 done. Dequeue C3 | [] | C3 (5 min) | C3 waited 3 min |
| t=10 | C3 done | [] | — | |

**Summary:**

| Customer | Arrival | Service Start | Wait | Service Time | Finish |
|----------|---------|---------------|------|-------------|--------|
| C1 | 0 | 0 | 0 | 3 | 3 |
| C2 | 1 | 3 | 2 | 2 | 5 |
| C3 | 2 | 5 | 3 | 5 | 10 |

**Average wait time = (0 + 2 + 3) / 3 = 1.67 minutes**

---

### Q40. Simulate a printer queue. Three print jobs arrive: J1 (5 pages), J2 (2 pages), J3 (8 pages). The printer processes 1 page/minute. Show how the queue manages jobs.

**Answer:**

```
Queue at start: [J1(5), J2(2), J3(8)]    (FIFO order)
```

| Time | Event | Queue | Pages Done |
|------|-------|-------|------------|
| t=0 | Start J1 | [J2(2), J3(8)] | J1: 0/5 |
| t=5 | J1 complete. Start J2 | [J3(8)] | J1: done |
| t=7 | J2 complete. Start J3 | [] | J2: done |
| t=15 | J3 complete | [] | J3: done |

**Total time: 15 minutes.**

**With priority queue (shortest job first):** J2(2) → J1(5) → J3(8).
- J2 finishes at t=2, J1 at t=7, J3 at t=15.
- Average completion: (2+7+15)/3 = 8 vs FIFO: (5+7+15)/3 = 9.
- **SJF reduces average completion time.**

---

### Q41. Simulate a customer service system with two queues: Regular and VIP. VIP customers are served first. Show the processing for: R1, V1, R2, V2, R3.

**Answer:**

```
Algorithm ServeCustomers()
    regularQ ← empty queue
    vipQ ← empty queue
    // Arrivals in order: R1, V1, R2, V2, R3
    
    Serving logic: always dequeue from vipQ first; if empty, dequeue from regularQ.
```

| Step | Arrival | regularQ | vipQ | Serve Next |
|------|---------|----------|------|------------|
| 1 | R1 arrives | [R1] | [] | Serve R1 (only customer) |
| 2 | V1 arrives during R1 service | [R2] | [V1] | — |
| 3 | R2 arrives | [R2] | [V1] | — |
| 4 | R1 done. VIP queue not empty → | [R2] | [] | **Serve V1** |
| 5 | V2 arrives during V1 service | [R2, R3] | [V2] | — |
| 6 | R3 arrives | [R2, R3] | [V2] | — |
| 7 | V1 done. VIP queue not empty → | [R2, R3] | [] | **Serve V2** |
| 8 | V2 done. VIP empty → regular | [R3] | [] | **Serve R2** |
| 9 | R2 done | [] | [] | **Serve R3** |

**Service order: R1, V1, V2, R2, R3**

Note: VIP customers "jump" ahead of regular customers already waiting. This is essentially a **priority queue**.

---

### Q42. Demonstrate a double-ended queue (deque) for managing railway coaches. Coaches can be added/removed from both front and rear. Show operations: addRear(A), addRear(B), addFront(C), removeFront(), addRear(D), removeRear().

**Answer:**

```
Deque operations (front ← → rear):
```

| Step | Operation | Deque State | Returned |
|------|-----------|-------------|----------|
| 1 | addRear(A) | [A] | — |
| 2 | addRear(B) | [A, B] | — |
| 3 | addFront(C) | [**C**, A, B] | — |
| 4 | removeFront() | [A, B] | **C** |
| 5 | addRear(D) | [A, B, **D**] | — |
| 6 | removeRear() | [A, B] | **D** |

**Final deque: [A, B]** (front=A, rear=B)

**Railway analogy:**
- addRear = attach coach at the end of train
- addFront = attach engine/coach at the front
- removeFront = detach from front (e.g., engine change)
- removeRear = detach last coach (e.g., route split)

**All operations: O(1)** using a doubly-linked list implementation.

---

### Q43. Implement a circular queue for a music playlist rotation. Capacity = 4. Show: enqueue(Song1), enqueue(Song2), enqueue(Song3), enqueue(Song4), dequeue(), enqueue(Song5), dequeue(). Show how the circular array wraps around.

**Answer:**

**Circular queue array (0-indexed, capacity 4):**

| Step | Operation | front | rear | Array [0][1][2][3] | Size |
|------|-----------|-------|------|--------------------|------|
| 0 | init | 0 | -1 | [_, _, _, _] | 0 |
| 1 | enqueue(S1) | 0 | 0 | [**S1**, _, _, _] | 1 |
| 2 | enqueue(S2) | 0 | 1 | [S1, **S2**, _, _] | 2 |
| 3 | enqueue(S3) | 0 | 2 | [S1, S2, **S3**, _] | 3 |
| 4 | enqueue(S4) | 0 | 3 | [S1, S2, S3, **S4**] | 4 (full) |
| 5 | dequeue()→S1 | 1 | 3 | [_, S2, S3, S4] | 3 |
| 6 | enqueue(S5) | 1 | **0** ← wraps! | [**S5**, S2, S3, S4] | 4 (full) |
| 7 | dequeue()→S2 | 2 | 0 | [S5, _, S3, S4] | 3 |

**Key: rear = (rear + 1) % capacity** → after index 3, wraps to index 0.

**Playlist state after all operations: [S3, S4, S5]** (front at index 2)

```
Array visual:
Index:  [0]  [1]  [2]  [3]
        S5    _   S3   S4
              ↑front     
        ↑rear
```

**Circular queue advantage over linear:** Slot 0 was reused after S1 was dequeued — no wasted space!

---

### Q44. Insert nodes 10, 20, 30 into a singly linked list, then delete the node with value 20. Show the pointer changes at each step.

**Answer:**

**Insertion (at end):**

```
Step 1: Insert 10
    head → [10 | NULL]

Step 2: Insert 20
    head → [10 | •] → [20 | NULL]

Step 3: Insert 30
    head → [10 | •] → [20 | •] → [30 | NULL]
```

**Deletion of node with value 20:**

```
Algorithm DeleteNode(head, key)
    if head.data = key then
        head = head.next         // delete head
        return head
    curr = head
    while curr.next ≠ NULL do
        if curr.next.data = key then
            curr.next = curr.next.next    // bypass the node
            return head
        curr = curr.next
    return head    // key not found
```

**Trace:**

| Step | curr | curr.next | Check |
|------|------|-----------|-------|
| 1 | 10 | 20 | 20.data = 20 = key? **Yes!** |
| 2 | Set 10.next = 20.next = 30 | — | Node 20 bypassed |

```
Before: head → [10 | •] → [20 | •] → [30 | NULL]
After:  head → [10 | •] ————————→ [30 | NULL]
                          (20 removed / freed)
```

**Result:** head → [10 | •] → [30 | NULL] ✓

**Time: O(n) to find the node. Space: O(1).**

---

*End of Chapter 2 Questions and Answers*
