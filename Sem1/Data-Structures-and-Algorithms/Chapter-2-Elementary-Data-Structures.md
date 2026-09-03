# Chapter 2: Elementary Data Structures

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
| 2.1 Stacks — ADT, Implementation, Applications | R1: 10.1 |
| 2.2 Queues — ADT, Implementation, Applications | R1: 10.1 |
| 2.3 Amortised Analysis — Aggregate Method | R1: 17.1 |
| 2.4 Lists — ADT, Implementation, Applications | R1: 10.2 |

---

## Table of Contents

- [2.1 Stacks](#21-stacks)
  - [2.1.1 Stack ADT](#211-stack-adt)
  - [2.1.2 Array-Based Implementation](#212-array-based-implementation)
  - [2.1.3 Linked-List Implementation](#213-linked-list-implementation)
  - [2.1.4 Stack Applications](#214-stack-applications)
- [2.2 Queues](#22-queues)
  - [2.2.1 Queue ADT](#221-queue-adt)
  - [2.2.2 Circular Array Implementation](#222-circular-array-implementation)
  - [2.2.3 Linked-List Implementation](#223-linked-list-implementation)
  - [2.2.4 Double-Ended Queue (Deque)](#224-double-ended-queue-deque)
  - [2.2.5 Queue Applications](#225-queue-applications)
- [2.3 Amortised Analysis](#23-amortised-analysis)
  - [2.3.1 Why Amortised Analysis?](#231-why-amortised-analysis)
  - [2.3.2 The Aggregate Method](#232-the-aggregate-method)
  - [2.3.3 Stack Operations — Amortised Analysis](#233-stack-operations--amortised-analysis)
  - [2.3.4 Queue Operations — Amortised Analysis](#234-queue-operations--amortised-analysis)
  - [2.3.5 Dynamic Array — Amortised Analysis](#235-dynamic-array--amortised-analysis)
- [2.4 Lists](#24-lists)
  - [2.4.1 List ADT](#241-list-adt)
  - [2.4.2 Singly Linked List — Implementation](#242-singly-linked-list--implementation)
  - [2.4.3 Doubly Linked List — Implementation](#243-doubly-linked-list--implementation)
  - [2.4.4 Circular Linked List](#244-circular-linked-list)
  - [2.4.5 Array vs. Linked List — Comparison](#245-array-vs-linked-list--comparison)
  - [2.4.6 List Applications](#246-list-applications)
- [Practice Problems with Solutions](#practice-problems-with-solutions)

---
---

# 2.1 Stacks

> **R1 Reference: Chapter 10.1 — Stacks and Queues**

## 2.1.1 Stack ADT

### What is an Abstract Data Type (ADT)?

Before we discuss stacks specifically, it's important to understand what an **Abstract Data Type (ADT)** means. An ADT is a mathematical model for a data type that is defined purely by its **behaviour** — what operations it supports and what those operations do — without specifying **how** those operations are implemented internally. The ADT separates the "what" from the "how."

For example, the Stack ADT says "you can push an element and pop the most recent element" — but it does not say whether you should use an array, a linked list, or any other internal structure. This separation is fundamental to software engineering: it allows you to change the implementation without affecting any code that uses the stack.

### The Stack Concept

A **stack** is a collection of elements that follows the **LIFO (Last In, First Out)** principle — the last element inserted is the first one removed.

The most intuitive real-world analogy is a **stack of plates in a cafeteria**. You place new plates on top of the pile, and when you need a plate, you take the one from the top. You cannot pull a plate from the middle or bottom without first removing all the plates above it. This restricted access pattern — where you can only interact with the topmost element — is exactly what defines a stack.

Another analogy is the **"undo" feature** in any text editor. Every action you perform is "pushed" onto an undo stack. When you press Ctrl+Z, the most recent action is "popped" and reversed. The older actions remain underneath, waiting their turn.

### Why is restricted access useful?

At first, it might seem like restricting access to only the top element is a limitation. But this restriction is precisely what makes stacks powerful and efficient:

1. **Simplicity:** Because you only ever touch the top, every operation (push, pop, peek) takes O(1) time. There's no searching, no shifting, no traversal.
2. **Natural modelling of nested structures:** Function calls are nested (function A calls B, which calls C). Parentheses are nested. Recursive problems are nested. A stack naturally mirrors this nesting — the most recently opened context is the first one closed.
3. **Automatic ordering:** The LIFO property guarantees that elements are processed in reverse order of their insertion. This is exactly what you need for undo operations, backtracking, and expression evaluation.

### Operations

| Operation | Description | Time Complexity |
|-----------|-------------|-----------------|
| `push(x)` | Insert element x on top of the stack | O(1) |
| `pop()` | Remove and return the top element | O(1) |
| `top()` / `peek()` | Return the top element without removing it | O(1) |
| `isEmpty()` | Return true if the stack has no elements | O(1) |
| `size()` | Return the number of elements | O(1) |

### Formal ADT Definition

```
ADT Stack:
    Data:
        A sequence of elements with a designated "top"
    
    Operations:
        push(x)    : Add x to the top
        pop()      : Remove and return the top element
                     Precondition: stack is not empty
        top()      : Return the top element without removing
                     Precondition: stack is not empty
        isEmpty()  : Return true if no elements exist
        size()     : Return the count of elements
    
    Errors:
        pop() or top() on empty stack → Underflow Error
        push() on full stack (fixed-size) → Overflow Error
```

---

## 2.1.2 Array-Based Implementation

The simplest and most common way to implement a stack is with an **array** and a single integer variable `top` that tracks the index of the topmost element.

### How it works conceptually

Imagine an array of fixed size (say, 100 elements). We designate one end of the array as the "bottom" of the stack (index 0) and grow the stack towards higher indices. The variable `top` always points to the index of the element currently on top of the stack. When the stack is empty, `top` is set to -1 (a sentinel value indicating "no element").

When we **push** an element, we increment `top` by 1 and place the new element at `array[top]`. When we **pop**, we read the element at `array[top]` and then decrement `top` by 1. The element isn't physically erased from memory — it's simply no longer considered part of the stack because `top` has moved below it.

This approach is elegant because both push and pop only modify a single index and a single array cell — making them **O(1)** operations regardless of how many elements are in the stack.

### The overflow/underflow problem

The main limitation of a fixed-size array is that you must decide the maximum capacity upfront. If the stack grows beyond this capacity, you get a **stack overflow** — there's no room for the new element. Conversely, if you try to pop from an empty stack (`top == -1`), you get a **stack underflow**.

In practice, this is often acceptable when you know the maximum size in advance (for example, expression evaluation where the stack can never exceed the number of operands). When the size is unpredictable, you either use a **dynamic array** (which doubles in size when full — see Section 2.3.5 on amortised analysis) or switch to a linked-list implementation.

### Data Structure

```
struct Stack:
    array[0..capacity-1]   // fixed-size array
    top = -1               // index of top element; -1 means empty
    capacity               // maximum size
```

### Operations

```
push(S, x):
    if S.top == S.capacity - 1:
        error "Stack Overflow"
    S.top = S.top + 1
    S.array[S.top] = x

pop(S):
    if S.top == -1:
        error "Stack Underflow"
    x = S.array[S.top]
    S.top = S.top - 1
    return x

top(S):
    if S.top == -1:
        error "Stack Underflow"
    return S.array[S.top]

isEmpty(S):
    return (S.top == -1)

size(S):
    return S.top + 1
```

### C Implementation

```c
#define MAX 100

struct Stack {
    int arr[MAX];
    int top;
};

void init(struct Stack* s) {
    s->top = -1;
}

int isEmpty(struct Stack* s) {
    return s->top == -1;
}

int isFull(struct Stack* s) {
    return s->top == MAX - 1;
}

void push(struct Stack* s, int x) {
    if (isFull(s)) { printf("Overflow\n"); return; }
    s->arr[++(s->top)] = x;
}

int pop(struct Stack* s) {
    if (isEmpty(s)) { printf("Underflow\n"); return -1; }
    return s->arr[(s->top)--];
}

int peek(struct Stack* s) {
    if (isEmpty(s)) { printf("Empty\n"); return -1; }
    return s->arr[s->top];
}
```

### Pros and Cons

| Pros | Cons |
|------|------|
| O(1) for all operations | Fixed size — must know maximum in advance |
| Cache-friendly (contiguous memory) | Wastes space if stack is mostly empty |
| Simple implementation | Overflow if capacity exceeded |

---

## 2.1.3 Linked-List Implementation

An alternative to the array-based approach is to implement a stack using a **singly linked list**. The key insight is that if you always insert and remove at the **head** of the linked list, both operations take O(1) time — no traversal is needed.

### How it works conceptually

Each element in the stack is stored in a **node** that contains two things: the data itself and a pointer (`next`) to the node below it in the stack. The stack maintains a single pointer called `head` (or `top`) that points to the topmost node.

When you **push** a new element, you create a new node, set its `next` pointer to the current head, and update the head to point to this new node. The new node is now on top. When you **pop**, you save the head node's data, update the head to point to `head.next` (the node below), and free the old head node.

### Why choose a linked list over an array?

The linked-list approach solves the fixed-size problem: the stack can grow as large as available memory allows, with no pre-determined capacity limit. There's no overflow (unless the entire system runs out of memory). Each push allocates exactly one node, and each pop frees exactly one node — so there's no wasted space.

However, this flexibility comes with costs:

1. **Memory overhead:** Each node stores an extra pointer (4 or 8 bytes depending on the architecture). For small data types like integers, this overhead can double the memory usage compared to an array.
2. **Cache performance:** Array elements are stored contiguously in memory, so accessing consecutive elements benefits from CPU cache prefetching. Linked list nodes are scattered across memory, leading to frequent cache misses. In practice, this makes linked-list operations 2-5x slower than array operations for the same logical work.
3. **Allocation cost:** Each push requires a `malloc` call (dynamic memory allocation), which is significantly slower than simply incrementing an index.

**Rule of thumb:** Use the array-based stack when you can estimate the maximum size. Use the linked-list stack when the size is completely unpredictable or when the stack needs to grow/shrink frequently with no wasted capacity.

### Data Structure

```
struct Node:
    data
    next → Node (or NULL)

struct Stack:
    head → Node (or NULL)
    size = 0
```

### Operations

```
push(S, x):
    newNode = create Node with data = x
    newNode.next = S.head
    S.head = newNode
    S.size++

pop(S):
    if S.head == NULL:
        error "Stack Underflow"
    x = S.head.data
    temp = S.head
    S.head = S.head.next
    free(temp)
    S.size--
    return x
```

### Pros and Cons

| Pros | Cons |
|------|------|
| Dynamic size — no overflow (until memory exhausted) | Extra memory for pointers |
| No wasted space | Not cache-friendly (nodes scattered in memory) |
| O(1) push/pop at head | Slightly slower due to memory allocation per push |

---

## 2.1.4 Stack Applications

### Application 1: Parenthesis / Bracket Matching

**Problem:** Given a string of brackets `({[]})`, check if every opening bracket has a correct closing bracket.

**Algorithm:**
```
for each character c in the string:
    if c is an opening bracket ( '(' or '{' or '[' ):
        push(c)
    else if c is a closing bracket:
        if stack is empty:
            return "Unbalanced"
        top = pop()
        if top does not match c:
            return "Unbalanced"
if stack is not empty:
    return "Unbalanced"
return "Balanced"
```

**Example:**
```
Input: "({[]})"
Step 1: '(' → push → Stack: ['(']
Step 2: '{' → push → Stack: ['(', '{']
Step 3: '[' → push → Stack: ['(', '{', '[']
Step 4: ']' → pop '[' → matches ✓ → Stack: ['(', '{']
Step 5: '}' → pop '{' → matches ✓ → Stack: ['(']
Step 6: ')' → pop '(' → matches ✓ → Stack: []
Stack empty → ✅ Balanced
```

**Time:** O(n). **Space:** O(n) worst case.

---

### Application 2: Infix to Postfix Conversion (Shunting-Yard Algorithm)

**Infix:** `A + B * C` (operators between operands)
**Postfix:** `A B C * +` (operators after operands — no parentheses needed)

**Algorithm:**
```
for each token in the infix expression:
    if token is an operand:
        append to output
    else if token is '(':
        push onto stack
    else if token is ')':
        while top of stack ≠ '(':
            pop and append to output
        pop '(' (discard)
    else (token is an operator):
        while stack is not empty AND top has higher/equal precedence:
            pop and append to output
        push token
while stack is not empty:
    pop and append to output
```

**Operator Precedence:** `^` (3, right-assoc) > `* /` (2, left-assoc) > `+ -` (1, left-assoc)

**Example: `A + B * C - D`**

| Token | Action | Stack | Output |
|-------|--------|-------|--------|
| A | Output | | A |
| + | Push | [+] | A |
| B | Output | [+] | A B |
| * | Push (* > +) | [+, *] | A B |
| C | Output | [+, *] | A B C |
| - | Pop * (≥), Pop + (≥), Push - | [-] | A B C * + |
| D | Output | [-] | A B C * + D |
| End | Pop - | [] | A B C * + D - |

**Postfix result:** `A B C * + D -`

---

### Application 3: Postfix Expression Evaluation

**Algorithm:**
```
for each token in postfix expression:
    if token is an operand:
        push(token)
    else (token is operator):
        b = pop()    // second operand (popped first!)
        a = pop()    // first operand
        result = a operator b
        push(result)
return pop()    // final result
```

**Example: Evaluate `5 3 2 * + 4 -`**

| Token | Action | Stack |
|-------|--------|-------|
| 5 | push | [5] |
| 3 | push | [5, 3] |
| 2 | push | [5, 3, 2] |
| * | pop 2,3 → 3*2=6 → push | [5, 6] |
| + | pop 6,5 → 5+6=11 → push | [11] |
| 4 | push | [11, 4] |
| - | pop 4,11 → 11-4=7 → push | [7] |

**Result: 7** ✓

---

### Application 4: Function Call Stack

Every time a function is called, the system pushes a **stack frame** containing:
- Local variables
- Return address
- Parameters

When the function returns, its frame is popped. This is why:
- **Recursion** works — each recursive call gets its own frame.
- **Stack overflow** occurs when recursion is too deep (too many frames).

---

### Application 5: Iterative DFS (Depth-First Search)

```
DFS_Iterative(graph, start):
    stack = empty Stack
    push(start)
    while stack is not empty:
        v = pop()
        if v is not visited:
            mark v as visited
            process v
            for each neighbour u of v:
                if u is not visited:
                    push(u)
```

---
---

# 2.2 Queues

> **R1 Reference: Chapter 10.1 — Stacks and Queues**

## 2.2.1 Queue ADT

### The Queue Concept

A **queue** is a collection of elements that follows the **FIFO (First In, First Out)** principle — the first element inserted is the first one removed. This is the exact opposite of a stack's LIFO behaviour.

The real-world analogy is any **waiting line**: a queue at a bank, a ticket counter, or a grocery checkout. The person who arrives first is served first. New arrivals join at the **rear** (back) of the line, and the person at the **front** is the next to be served.

### Why FIFO matters

FIFO ordering is essential whenever **fairness** or **order preservation** matters:

1. **Process scheduling in operating systems:** When multiple processes are ready to run, the OS places them in a ready queue. The process that has been waiting the longest gets CPU time first. This prevents starvation — no process waits forever while newer processes keep jumping ahead.

2. **Breadth-First Search (BFS):** In graph traversal, BFS explores all vertices at distance d from the source before exploring any vertex at distance d+1. This distance-ordering is naturally maintained by a queue: vertices discovered first (closer) are dequeued and processed before vertices discovered later (farther).

3. **Buffering between components:** In any system where a producer generates data faster than a consumer can process it (network packets, print jobs, message queues), a FIFO queue acts as a buffer that preserves the order of arrival.

### Stack vs. Queue: A mental model

Think of a **stack** as a vertical pile where you can only touch the top. Think of a **queue** as a horizontal pipe where items enter at one end and exit at the other. The stack reverses order (LIFO), while the queue preserves order (FIFO). Many algorithms need one or the other depending on whether they require depth-first (stack) or breadth-first (queue) exploration.

### Operations

| Operation | Description | Time Complexity |
|-----------|-------------|-----------------|
| `enqueue(x)` | Insert element x at the rear | O(1) |
| `dequeue()` | Remove and return the front element | O(1) |
| `front()` / `peek()` | Return the front element without removing | O(1) |
| `isEmpty()` | Return true if queue has no elements | O(1) |
| `size()` | Return the number of elements | O(1) |

### Formal ADT Definition

```
ADT Queue:
    Data:
        A sequence of elements with a designated "front" and "rear"
    
    Operations:
        enqueue(x) : Add x to the rear
        dequeue()  : Remove and return the front element
                     Precondition: queue is not empty
        front()    : Return the front element without removing
                     Precondition: queue is not empty
        isEmpty()  : Return true if no elements exist
        size()     : Return the count of elements
    
    Errors:
        dequeue() or front() on empty queue → Underflow Error
        enqueue() on full queue (fixed-size) → Overflow Error
```

---

## 2.2.2 Circular Array Implementation

### The problem with a naive linear array

If you implement a queue using a simple array with a `front` pointer and a `rear` pointer, something wasteful happens over time. When you dequeue an element, the `front` pointer advances to the right. The slot that was freed at the beginning of the array can never be reused — it sits empty forever. After many enqueue/dequeue cycles, the "active" portion of the array has drifted to the right, leaving a growing trail of wasted space on the left.

You could fix this by shifting all remaining elements to the left after each dequeue, but that would make dequeue an O(n) operation — unacceptable for a data structure whose whole point is O(1) operations.

### The circular array solution

A **circular array** (also called a ring buffer) solves this elegantly. Instead of treating the array as a straight line from index 0 to capacity-1, we treat it as a **circle** where index `capacity-1` wraps around back to index 0. This is achieved with a single trick: the **modulo operator**.

```
rear = (rear + 1) mod capacity
front = (front + 1) mod capacity
```

When `rear` or `front` would go past the last index, the `mod` wraps it back to 0. This means the array is fully utilised — freed slots at the beginning are reused when the rear pointer wraps around.

### Detecting full vs. empty

One subtle issue: both "completely full" and "completely empty" could look the same (front == rear). There are three common solutions:

1. **Count variable:** Maintain a separate `count` of elements. Full when count == capacity. Empty when count == 0. This is the simplest and most common approach.
2. **Waste one slot:** Keep one slot always empty. Full when `(rear + 1) % capacity == front`. Empty when `front == rear`. This avoids the extra variable but wastes one slot.
3. **Boolean flag:** Use a flag that indicates whether the last operation was an enqueue or dequeue, to distinguish full from empty.

### Data Structure

```
struct Queue:
    array[0..capacity-1]
    front = 0          // index of front element
    rear = 0           // index of next insertion point
    count = 0          // number of elements
    capacity
```

### Operations

```
enqueue(Q, x):
    if Q.count == Q.capacity:
        error "Queue Overflow"
    Q.array[Q.rear] = x
    Q.rear = (Q.rear + 1) mod Q.capacity    // wrap around!
    Q.count++

dequeue(Q):
    if Q.count == 0:
        error "Queue Underflow"
    x = Q.array[Q.front]
    Q.front = (Q.front + 1) mod Q.capacity  // wrap around!
    Q.count--
    return x
```

### Visualization of Circular Array

```
Initial state (capacity=5, empty):
  [_] [_] [_] [_] [_]
   ↑front              ↑rear (both at 0)

After enqueue(10), enqueue(20), enqueue(30):
  [10] [20] [30] [_] [_]
   ↑front          ↑rear (at 3)

After dequeue() → returns 10:
  [_] [20] [30] [_] [_]
       ↑front     ↑rear

After enqueue(40), enqueue(50):
  [_] [20] [30] [40] [50]
       ↑front              ↑rear (at 0, wrapped!)

After enqueue(60):
  [60] [20] [30] [40] [50]
        ↑front ↑rear (at 1)    // rear wrapped to fill the gap
```

The `mod` operator makes it circular — when rear or front reaches the end, it wraps back to 0.

### C Implementation

```c
#define MAX 100

struct Queue {
    int arr[MAX];
    int front, rear, count;
};

void init(struct Queue* q) {
    q->front = q->rear = q->count = 0;
}

int isEmpty(struct Queue* q) { return q->count == 0; }
int isFull(struct Queue* q) { return q->count == MAX; }

void enqueue(struct Queue* q, int x) {
    if (isFull(q)) { printf("Overflow\n"); return; }
    q->arr[q->rear] = x;
    q->rear = (q->rear + 1) % MAX;
    q->count++;
}

int dequeue(struct Queue* q) {
    if (isEmpty(q)) { printf("Underflow\n"); return -1; }
    int x = q->arr[q->front];
    q->front = (q->front + 1) % MAX;
    q->count--;
    return x;
}
```

---

## 2.2.3 Linked-List Implementation

Enqueue at **tail**, dequeue at **head** — both O(1) with a tail pointer.

```
struct Queue:
    head → Node (or NULL)
    tail → Node (or NULL)
    size = 0

enqueue(Q, x):
    newNode = create Node with data = x, next = NULL
    if Q.tail ≠ NULL:
        Q.tail.next = newNode
    Q.tail = newNode
    if Q.head == NULL:
        Q.head = newNode
    Q.size++

dequeue(Q):
    if Q.head == NULL:
        error "Underflow"
    x = Q.head.data
    temp = Q.head
    Q.head = Q.head.next
    if Q.head == NULL:
        Q.tail = NULL    // queue became empty
    free(temp)
    Q.size--
    return x
```

---

## 2.2.4 Double-Ended Queue (Deque)

A **deque** (pronounced "deck") allows insertion and removal at **both** ends.

| Operation | Time |
|-----------|------|
| `insertFront(x)` | O(1) |
| `insertRear(x)` | O(1) |
| `removeFront()` | O(1) |
| `removeRear()` | O(1) |

**Implementation:** Doubly linked list or circular array.

**Use case:** A deque can function as both a stack AND a queue. It's used in algorithms like BFS with 0-1 edge weights (0-1 BFS) and sliding window maximum.

---

## 2.2.5 Queue Applications

### Application 1: BFS (Breadth-First Search)

```
BFS(graph, start):
    queue = empty Queue
    enqueue(start)
    mark start as visited
    while queue is not empty:
        v = dequeue()
        process v
        for each neighbour u of v:
            if u is not visited:
                mark u as visited
                enqueue(u)
```

BFS visits all vertices at distance d before any vertex at distance d+1. This gives **shortest paths in unweighted graphs**.

### Application 2: Task Scheduling (FIFO)

Operating systems use queues for:
- **Process scheduling** — Ready queue of processes waiting for CPU time.
- **Print spooling** — Print jobs served in order of arrival.
- **I/O request handling** — Disk requests queued and served in order.

### Application 3: Level-Order Tree Traversal

```
LevelOrder(root):
    if root == NULL: return
    queue = empty Queue
    enqueue(root)
    while queue is not empty:
        node = dequeue()
        print node.data
        if node.left ≠ NULL: enqueue(node.left)
        if node.right ≠ NULL: enqueue(node.right)
```

### Application 4: Producer-Consumer Buffer

A queue acts as a buffer between a producer (adding items) and a consumer (processing items). Used in multithreaded programming, message passing, and streaming systems.

---
---

# 2.3 Amortised Analysis

> **R1 Reference: Chapter 17.1 — Aggregate Analysis**

## 2.3.1 Why Amortised Analysis?

### The problem with worst-case analysis

Standard worst-case analysis asks: "What is the maximum cost of a **single** operation?" This is often too pessimistic for data structures where most operations are cheap but occasionally one operation is expensive.

Consider a dynamic array (like Java's `ArrayList`). Most insertions simply place an element at the next available slot — cost O(1). But occasionally, when the array is full, the system allocates a new array of double the size and copies all existing elements — cost O(n). If we judge every insertion by the worst case, we'd say "insertion is O(n)" and conclude that n insertions cost O(n²). But this dramatically over-counts the actual work, because the expensive resizing happens very rarely.

### What amortised analysis actually measures

**Amortised analysis** gives the **average cost per operation over an entire worst-case sequence** of n operations. It answers the question: "If I perform n operations starting from an empty data structure, what is the total cost divided by n?"

This is fundamentally different from **average-case analysis**:

- **Average-case analysis** assumes a probability distribution over inputs (e.g. "each element is equally likely to be the search target") and computes the expected cost. It requires probabilistic assumptions that may not hold in practice.
- **Amortised analysis** makes **no probability assumptions whatsoever**. It works for the absolute worst-case sequence of operations. It just recognises that within that worst-case sequence, the expensive operations are necessarily rare enough that the average stays low.

Think of it like a monthly subscription: some months you use the service a lot (expensive), some months barely at all (cheap). The amortised cost is the flat monthly fee that covers your total usage over the year.

### Three methods of amortised analysis

CLRS (R1: Chapter 17) presents three methods. For this course, we focus on the **Aggregate Method**, but it's helpful to know all three exist:

| Method | Idea | Difficulty |
|--------|------|-----------|
| **Aggregate method** | Compute total cost of n operations, divide by n. | Simplest; works when all operations have the same amortised cost. |
| **Accounting method** | Assign a "charge" to each operation. Cheap operations are overcharged (creating "credit") that pays for expensive ones later. | More flexible; allows different amortised costs for different operations. |
| **Potential method** | Define a "potential function" Φ on the data structure state. Amortised cost = actual cost + ΔΦ. The potential stores "prepaid work" that can be released later. | Most powerful; used in advanced proofs (Fibonacci heaps, splay trees). |

All three methods give the same amortised bounds — they're just different proof techniques.

---

## 2.3.2 The Aggregate Method

The aggregate method is the simplest of the three amortised analysis techniques. The procedure is straightforward:

1. **Identify the sequence:** Consider a worst-case sequence of n operations starting from an empty data structure.
2. **Compute total cost:** Calculate the total cost T(n) of all n operations combined. This is the hard part — you need to find a clever way to bound the total, usually by finding an invariant (something that limits how much total work can accumulate).
3. **Divide:** The amortised cost per operation is T(n) / n.

The key insight that makes this work is that expensive operations are always "paid for" by the many cheap operations that precede them. The total cost captures this balance.

```
Amortised cost per operation = (Total cost of n operations) / n
```

In the aggregate method, every operation gets the **same** amortised cost (T(n)/n). This is a simplification — the accounting and potential methods allow different operations to have different amortised costs — but for the three examples in this chapter (MULTIPOP stack, two-stack queue, dynamic array), the aggregate method is perfectly sufficient.

---

## 2.3.3 Stack Operations — Amortised Analysis

### The MULTIPOP Operation

Consider a stack with three operations:
- `PUSH(x)` — cost 1
- `POP()` — cost 1
- `MULTIPOP(k)` — pop min(k, stack_size) elements. Cost = min(k, stack_size).

**Question:** Starting from an empty stack, what is the amortised cost per operation over a sequence of n PUSH, POP, and MULTIPOP operations?

### Naive Worst-Case Analysis (Too Pessimistic)

A single MULTIPOP can cost O(n) (pop everything). So worst case for n operations = O(n²)?

**This is too pessimistic!**

### Aggregate Analysis (Correct)

**Key insight:** Each element can be popped **at most once** for each time it is pushed.

- Over n operations, the number of PUSHes ≤ n.
- The total number of POPs (from POP + MULTIPOP combined) ≤ total PUSHes ≤ n.
- Total cost of all POPs and MULTIPOPs ≤ n.
- Total cost of all PUSHes ≤ n.

**Total cost ≤ 2n.**

**Amortised cost per operation = 2n / n = O(1).**

### Worked Example

```
Sequence: PUSH(1), PUSH(2), PUSH(3), MULTIPOP(2), PUSH(4), PUSH(5), MULTIPOP(5)

PUSH(1)      cost = 1   stack: [1]
PUSH(2)      cost = 1   stack: [1, 2]
PUSH(3)      cost = 1   stack: [1, 2, 3]
MULTIPOP(2)  cost = 2   stack: [1]         (popped 3, 2)
PUSH(4)      cost = 1   stack: [1, 4]
PUSH(5)      cost = 1   stack: [1, 4, 5]
MULTIPOP(5)  cost = 3   stack: []           (popped 5, 4, 1 — only 3 elements)

Total operations: 7
Total cost: 1 + 1 + 1 + 2 + 1 + 1 + 3 = 10
Amortised cost: 10 / 7 ≈ 1.43 = O(1)
```

Even though MULTIPOP can be expensive in isolation, over a sequence it averages out because you can only pop what you've pushed.

---

## 2.3.4 Queue Operations — Amortised Analysis

### Queue with Two Stacks

A queue can be implemented using two stacks (S1 and S2):

```
enqueue(x):
    push x onto S1

dequeue():
    if S2 is empty:
        while S1 is not empty:
            pop from S1 and push onto S2     // Transfer all elements
    return pop from S2
```

**How it works:**
- S1 is the "input" stack (new elements go here).
- S2 is the "output" stack (elements come out from here).
- When S2 is empty, we "flip" S1 into S2 — this reverses the order, giving FIFO behaviour.

**Naive analysis:** A single dequeue might transfer n elements from S1 to S2 → O(n). So n operations could be O(n²)?

**Aggregate analysis:**
- Each element is pushed onto S1 exactly **once** (cost 1).
- Each element is transferred from S1 to S2 exactly **once** (cost 1).
- Each element is popped from S2 exactly **once** (cost 1).
- Total cost per element = **3**.
- For n enqueue/dequeue operations involving at most n elements: total cost ≤ **3n**.

**Amortised cost per operation = 3n / n = O(1).**

### Worked Example

```
enqueue(1)     S1: [1]       S2: []         cost: 1
enqueue(2)     S1: [1, 2]    S2: []         cost: 1
enqueue(3)     S1: [1, 2, 3] S2: []         cost: 1
dequeue()      Transfer 3 elements:
               S1: []        S2: [3, 2, 1]  cost: 3 (transfer)
               Pop from S2 → returns 1
               S1: []        S2: [3, 2]     cost: 1 (pop)
               Total for this dequeue: 4
dequeue()      S2 not empty:
               Pop from S2 → returns 2      cost: 1
dequeue()      Pop from S2 → returns 3      cost: 1

Total: 6 operations, total cost = 1+1+1+4+1+1 = 9
Amortised: 9/6 = 1.5 = O(1)
```

---

## 2.3.5 Dynamic Array — Amortised Analysis

A **dynamic array** (like C++ `vector` or Java `ArrayList`) doubles its capacity when full.

| Operation | What happens | Cost |
|-----------|-------------|------|
| Insert (space available) | Place element at next slot | **1** |
| Insert (array full) | Allocate new array of 2× capacity, copy all elements, insert | **n + 1** |

**Aggregate analysis of n insertions starting from capacity 1:**

Resize happens when size = 1, 2, 4, 8, 16, ..., i.e., at insertions 1, 2, 4, 8, ...

Total copy cost = 1 + 2 + 4 + 8 + ... + n/2 + n ≤ 2n (geometric series sum)

Plus n insertions at cost 1 each = n.

**Total cost ≤ 3n.**

**Amortised cost per insertion = 3n / n = O(1).**

```
Insert 1:  capacity 1 → FULL → resize to 2, copy 1.  Cost: 1+1 = 2
Insert 2:  capacity 2 → FULL → resize to 4, copy 2.  Cost: 2+1 = 3
Insert 3:  capacity 4 → fits.                          Cost: 1
Insert 4:  capacity 4 → FULL → resize to 8, copy 4.  Cost: 4+1 = 5
Insert 5:  capacity 8 → fits.                          Cost: 1
Insert 6:  capacity 8 → fits.                          Cost: 1
Insert 7:  capacity 8 → fits.                          Cost: 1
Insert 8:  capacity 8 → FULL → resize to 16, copy 8. Cost: 8+1 = 9

Total for 8 insertions: 2+3+1+5+1+1+1+9 = 23
Amortised: 23/8 ≈ 2.875 = O(1) ✓
```

### Why Doubling? Why Not Add a Constant?

If we increase capacity by a constant c (e.g. add 10 each time):

- Resizes happen at n/c, 2n/c, 3n/c, ... insertions.
- Total copy cost = c + 2c + 3c + ... = c · k(k+1)/2 where k = n/c → **O(n²/c) = O(n²)**.
- **Amortised cost = O(n).** Not constant!

**Doubling gives O(1) amortised. Adding a constant gives O(n) amortised.** That's why all standard implementations double.

---
---

# 2.4 Lists

> **R1 Reference: Chapter 10.2 — Linked Lists**

## 2.4.1 List ADT

### What is a List?

A **list** (sometimes called a sequence) is the most general of the three data structures in this chapter. While stacks restrict access to the top and queues restrict access to the front/rear, a list allows insertion, deletion, and access at **any position**.

A list is an **ordered collection** — the elements have a definite sequence (first, second, third, ...) and each element is identified by its **index** (position). This is different from a set, where elements have no order.

### Lists vs. Stacks vs. Queues

All three are linear data structures (elements arranged in a sequence), but they differ in what operations they allow:

| Data Structure | Access Pattern | Restriction |
|----------------|---------------|-------------|
| **Stack** | Top only | LIFO — can only push/pop at one end. |
| **Queue** | Front and rear | FIFO — enqueue at rear, dequeue at front. |
| **List** | Any position | No restriction — insert, delete, access anywhere. |

A stack and a queue are **restricted lists**. They give up generality to gain simplicity and guaranteed O(1) operations. A general list is more flexible but some operations (like inserting in the middle) require traversal.

### Two implementation strategies

A list can be implemented in two fundamentally different ways:

1. **Array-based list (ArrayList):** Elements stored in a contiguous array. Access by index is O(1) (direct computation), but insertion/deletion in the middle requires shifting elements — O(n).
2. **Linked list:** Elements stored in nodes scattered across memory, connected by pointers. Insertion/deletion at a known position is O(1) (just rewire pointers), but access by index requires traversal — O(n).

The choice between them is one of the most common engineering decisions in programming, and the right answer depends entirely on the access pattern of your application.

### Operations

| Operation | Description |
|-----------|-------------|
| `get(i)` | Return element at position i |
| `set(i, x)` | Replace element at position i with x |
| `insert(i, x)` | Insert x at position i, shifting subsequent elements |
| `remove(i)` | Remove element at position i, shifting subsequent elements |
| `indexOf(x)` | Return the position of the first occurrence of x |
| `size()` | Return the number of elements |
| `isEmpty()` | Return true if no elements exist |

---

## 2.4.2 Singly Linked List — Implementation

### The structure

A singly linked list is a chain of **nodes** where each node contains two things:
1. **Data** — the actual value being stored.
2. **Next pointer** — a reference (address) to the next node in the chain.

The last node's `next` pointer is `NULL`, signalling the end of the list. The list itself is represented by a single **head pointer** that points to the first node. If `head` is NULL, the list is empty.

### Why "singly"?

It's called "singly" linked because each node has only **one** link — to the next node. You can traverse the list in only one direction: from head towards the tail. You cannot go backwards. This has a critical consequence: if you're standing at a node and want to delete it, you need its **predecessor's** pointer (to bypass the current node). But you can't get to the predecessor without traversing from the head — making deletion O(n) in the general case.

### Memory layout

Unlike an array where elements sit side-by-side in memory (contiguous), linked list nodes can be **anywhere** in memory. Each node is individually allocated by `malloc`. The `next` pointers stitch them together into a logical sequence. This means:

- **Advantage:** Inserting a new node never requires moving existing elements. Just allocate a new node and rewire two pointers. No shifting.
- **Disadvantage:** To access the k-th element, you must follow k pointers starting from the head (O(k) traversal). There's no way to jump directly to a position like `array[k]`.

```
struct Node:
    data
    next → Node (or NULL)

struct SinglyLinkedList:
    head → Node (or NULL)
    size = 0
```

### Core Operations

```
insertAtHead(L, x):                           // O(1)
    newNode = create Node(data=x, next=L.head)
    L.head = newNode
    L.size++

insertAtTail(L, x):                           // O(n) without tail pointer, O(1) with tail pointer
    newNode = create Node(data=x, next=NULL)
    if L.head == NULL:
        L.head = newNode
    else:
        current = L.head
        while current.next ≠ NULL:
            current = current.next
        current.next = newNode
    L.size++

insertAtPosition(L, i, x):                    // O(i) — traverse to position
    if i == 0:
        insertAtHead(L, x)
        return
    current = L.head
    for j = 0 to i-2:                         // stop at node before position i
        current = current.next
    newNode = create Node(data=x, next=current.next)
    current.next = newNode
    L.size++

deleteAtHead(L):                               // O(1)
    if L.head == NULL: error "Empty"
    temp = L.head
    L.head = L.head.next
    free(temp)
    L.size--

deleteAtPosition(L, i):                        // O(i) — traverse to node before position i
    if i == 0:
        deleteAtHead(L)
        return
    current = L.head
    for j = 0 to i-2:
        current = current.next
    temp = current.next
    current.next = temp.next
    free(temp)
    L.size--

search(L, x):                                 // O(n)
    current = L.head
    position = 0
    while current ≠ NULL:
        if current.data == x:
            return position
        current = current.next
        position++
    return -1                                  // not found
```

### Complexity Summary — Singly Linked List

| Operation | Time |
|-----------|------|
| Insert at head | O(1) |
| Insert at tail (without tail ptr) | O(n) |
| Insert at tail (with tail ptr) | O(1) |
| Insert at position i | O(i) |
| Delete at head | O(1) |
| Delete at position i | O(i) |
| Delete given node (no predecessor) | O(n) — must find predecessor |
| Search | O(n) |
| Access by index | O(n) |

---

## 2.4.3 Doubly Linked List — Implementation

### Why do we need a doubly linked list?

The singly linked list has one fundamental weakness: to delete a node, you need its **predecessor**. Since each node only points forward (`next`), finding the predecessor requires traversing from the head — O(n). This is a deal-breaker for algorithms that need to delete specific nodes frequently.

A **doubly linked list** solves this by giving each node **two** pointers:
1. **next** — points to the next node (forward).
2. **prev** — points to the previous node (backward).

The list also typically maintains both a **head** pointer (first node) and a **tail** pointer (last node).

### The O(1) deletion advantage

With a `prev` pointer, deleting a node becomes trivial even if you only have a pointer to the node itself (not its predecessor):

```
To delete node X:
    X.prev.next = X.next    // predecessor skips over X
    X.next.prev = X.prev    // successor's prev skips back over X
    free(X)
```

No traversal needed. O(1).

This is not a minor optimisation — it's the foundation of several important data structures and algorithms:

- **LRU Cache:** When a cache entry is accessed, it must be moved to the front of a "recently used" list. With a doubly linked list, the move is O(1): delete from current position (O(1)) and insert at head (O(1)). Combined with a hash map for O(1) lookup, this gives an O(1) LRU cache.
- **OS process lists:** The operating system maintains doubly linked lists of processes. When a process changes state (running → blocked → ready), it's removed from one list and inserted into another — both O(1).
- **Text editor gap buffer alternative:** Some text editors represent the document as a doubly linked list of lines, allowing O(1) insertion and deletion of lines.

### The cost of the extra pointer

Each node now stores two pointers instead of one. On a 64-bit system, that's 16 bytes of overhead per node (vs. 8 bytes for singly linked). For large data items this is negligible, but for small data (like a list of integers), the pointer overhead can exceed the data size. Memory usage is roughly 2-3x higher than an array storing the same data.

```
struct Node:
    data
    prev → Node (or NULL)
    next → Node (or NULL)

struct DoublyLinkedList:
    head → Node (or NULL)
    tail → Node (or NULL)
    size = 0
```

### Operations

```
insertAtHead(L, x):                           // O(1)
    newNode = create Node(data=x, prev=NULL, next=L.head)
    if L.head ≠ NULL:
        L.head.prev = newNode
    else:
        L.tail = newNode
    L.head = newNode
    L.size++

insertAtTail(L, x):                           // O(1) — have tail pointer
    newNode = create Node(data=x, prev=L.tail, next=NULL)
    if L.tail ≠ NULL:
        L.tail.next = newNode
    else:
        L.head = newNode
    L.tail = newNode
    L.size++

deleteNode(L, node):                          // O(1) — given a pointer to the node
    if node.prev ≠ NULL:
        node.prev.next = node.next
    else:
        L.head = node.next               // deleting head
    if node.next ≠ NULL:
        node.next.prev = node.prev
    else:
        L.tail = node.prev               // deleting tail
    free(node)
    L.size--
```

### Key Advantage: O(1) Deletion Given a Node Pointer

In a singly linked list, deleting a node requires finding its predecessor → O(n).
In a doubly linked list, the node already knows its predecessor → O(1).

This is critical for algorithms like **LRU Cache** (Least Recently Used), which uses a doubly linked list + hash map.

### Complexity Summary — Doubly Linked List

| Operation | Time |
|-----------|------|
| Insert at head / tail | O(1) |
| Insert after given node | O(1) |
| Delete given node | **O(1)** — key advantage |
| Delete at head / tail | O(1) |
| Search | O(n) |
| Access by index | O(n) |

---

## 2.4.4 Circular Linked List

### What makes it circular?

In a standard linked list, the last node's `next` pointer is `NULL`, marking the end. In a **circular linked list**, the last node's `next` pointer loops back to the **first node**, forming a closed ring. There is no "end" — traversal continues indefinitely around the circle.

```
Singly Circular:
    A → B → C → D → A → B → C → D → ...  (infinite loop)

Doubly Circular:
    ... ⇄ A ⇄ B ⇄ C ⇄ D ⇄ A ⇄ B ⇄ ...
    (head.prev = tail, tail.next = head)
```

### When to use circular lists

Circular lists are the natural choice when the data itself is cyclic:

1. **Round-robin scheduling:** In an OS, processes take turns using the CPU. After the last process gets its turn, you go back to the first. A circular list lets you just keep following `next` — you never need to check for NULL or reset to the head.

2. **Circular buffers:** Audio/video streaming uses ring buffers where new data overwrites the oldest data. The producer and consumer chase each other around the circle.

3. **Game turn order:** In a multiplayer game where turns cycle (Player 1 → Player 2 → Player 3 → Player 1 → ...), a circular list models this naturally.

4. **Josephus problem:** A classic algorithmic problem where people standing in a circle are eliminated in a fixed counting pattern. Circular linked list is the textbook implementation.

### Implementation considerations

The main change is that there's no NULL check during traversal — you must detect when you've completed a full loop by comparing the current node to the starting node. Without this check, traversal would loop forever.

---

## 2.4.5 Array vs. Linked List — Comparison

This is one of the most fundamental trade-offs in computer science. The choice affects performance, memory usage, and code complexity in nearly every program.

### The core trade-off

**Arrays give you O(1) random access but O(n) insertion/deletion.**
**Linked lists give you O(1) insertion/deletion (at known positions) but O(n) access.**

This trade-off is rooted in memory layout:

- **Array:** Contiguous block of memory. Element i lives at address `base + i × element_size`. The CPU can compute this address instantly — no traversal needed. But inserting in the middle requires shifting all subsequent elements to make room.

- **Linked list:** Nodes scattered across memory, connected by pointers. To reach element i, you must follow i pointers from the head. But inserting at a known position just requires rewiring 2-3 pointers — no shifting.

### The cache performance factor (often overlooked)

In theory, arrays and linked lists have the same O(n) time for scanning all elements. In practice, arrays are **significantly faster** due to **CPU cache locality**.

Modern CPUs don't fetch individual bytes from RAM — they fetch entire **cache lines** (typically 64 bytes). When you access `array[0]`, the CPU fetches a block of memory containing `array[0]` through `array[15]` (for 4-byte integers). Subsequent accesses to `array[1]`, `array[2]`, etc. are **cache hits** — already in the fast L1 cache.

Linked list nodes are scattered across memory. Each `node.next` access is likely a **cache miss** — the CPU must wait for data to be fetched from slower RAM. This can make linked list traversal 5-10x slower than array traversal for the same number of elements.

This is why the practical advice is: **default to arrays unless you have a specific reason to use linked lists.**

| Aspect | Array | Linked List |
|--------|-------|-------------|
| **Memory** | Contiguous block | Scattered nodes + pointer overhead |
| **Access by index** | **O(1)** — direct | O(n) — must traverse |
| **Insert at beginning** | O(n) — shift all | **O(1)** |
| **Insert at end** | O(1) amortised (dynamic array) | O(1) with tail pointer |
| **Insert in middle** | O(n) — shift | O(1) after finding position |
| **Delete** | O(n) — shift | O(1) if node pointer given (doubly) |
| **Search** | O(n) unsorted, O(log n) sorted | O(n) — cannot binary search |
| **Cache performance** | **Excellent** — contiguous | Poor — scattered |
| **Memory waste** | Possible (unused capacity) | Per-node pointer overhead |
| **Size** | Fixed (or amortised growth) | Dynamic |

### When to Use What

| Use Array When | Use Linked List When |
|----------------|---------------------|
| Frequent random access (index-based). | Frequent insertions/deletions at arbitrary positions. |
| Size is known or roughly known. | Size is highly unpredictable. |
| Cache performance matters. | Memory is fragmented / allocation is per-element. |
| Need binary search on sorted data. | Implementing stacks/queues (push/pop at ends). |

---

## 2.4.6 List Applications

1. **Polynomial Representation:** Each node stores a coefficient and exponent. Addition/multiplication by traversing both lists.

2. **Sparse Matrix Representation:** Only non-zero elements stored as (row, col, value) nodes. Saves memory for matrices that are mostly zeros.

3. **Undo/Redo (Doubly Linked List):** Each state is a node. Move backward (undo) or forward (redo) along the list.

4. **LRU Cache (Doubly Linked List + Hash Map):**
   - Hash map: key → node pointer (O(1) lookup).
   - Doubly linked list: most recently used at head, least recently used at tail.
   - On access: move node to head → O(1).
   - On eviction: remove tail → O(1).

5. **Music Playlist (Circular List):** Songs cycle back to the beginning after the last song.

---
---

# Practice Problems with Solutions

---

### P1. Implement a Min-Stack

**Problem:** Design a stack that supports push, pop, top, and retrieving the minimum element — all in O(1) time.

**Solution:** Use two stacks: one for data, one for tracking minimums.

```
struct MinStack:
    dataStack = empty Stack
    minStack = empty Stack

push(x):
    dataStack.push(x)
    if minStack.isEmpty() OR x ≤ minStack.top():
        minStack.push(x)

pop():
    val = dataStack.pop()
    if val == minStack.top():
        minStack.pop()
    return val

top():
    return dataStack.top()

getMin():
    return minStack.top()
```

**Why it works:** minStack only pushes when a new minimum arrives. When that minimum is popped from dataStack, it's also popped from minStack, revealing the previous minimum.

**All operations: O(1) time. O(n) space.**

---

### P2. Evaluate `3 4 + 2 * 7 /` (Postfix)

**Trace:**

| Token | Action | Stack |
|-------|--------|-------|
| 3 | push | [3] |
| 4 | push | [3, 4] |
| + | pop 4, 3 → 3+4=7 | [7] |
| 2 | push | [7, 2] |
| * | pop 2, 7 → 7*2=14 | [14] |
| 7 | push | [14, 7] |
| / | pop 7, 14 → 14/7=2 | [2] |

**Result: 2** ✓

---

### P3. Starting from an empty stack, a sequence of n PUSH and MULTIPOP operations is performed. Prove the amortised cost per operation is O(1).

**Proof (Aggregate Method):**

1. Over n operations, the maximum number of PUSH operations ≤ n.
2. Each element is pushed at most once.
3. Each element can be popped at most once (by POP or as part of MULTIPOP).
4. Total pops across all POP and MULTIPOP calls ≤ total pushes ≤ n.
5. Total cost = (cost of all pushes) + (cost of all pops) ≤ n + n = 2n.
6. **Amortised cost = 2n / n = O(1).** ∎

---

### P4. A queue is implemented using two stacks. Show that the amortised cost of enqueue and dequeue is O(1).

**Proof (Aggregate Method):**

Consider a sequence of n enqueue and dequeue operations.

Each element goes through at most 3 moves:
1. Pushed onto S1 (during enqueue) — cost 1.
2. Popped from S1 and pushed onto S2 (during transfer) — cost 2.
3. Popped from S2 (during dequeue) — cost 1.

Total cost per element ≤ 4. At most n elements processed.

**Total cost ≤ 4n. Amortised cost = 4n / n = O(1).** ∎

---

### P5. Reverse a linked list iteratively and recursively.

**Iterative:**
```
reverse(head):
    prev = NULL
    current = head
    while current ≠ NULL:
        next = current.next
        current.next = prev     // reverse the link
        prev = current
        current = next
    return prev                 // new head
```

**Trace (1 → 2 → 3 → NULL):**
```
Step 1: prev=NULL, curr=1 → 1.next=NULL, prev=1, curr=2
Step 2: prev=1, curr=2 → 2.next=1, prev=2, curr=3
Step 3: prev=2, curr=3 → 3.next=2, prev=3, curr=NULL
Return 3. List: 3 → 2 → 1 → NULL ✓
```

**Recursive:**
```
reverse(head):
    if head == NULL or head.next == NULL:
        return head
    rest = reverse(head.next)
    head.next.next = head      // make the next node point back to us
    head.next = NULL            // remove our forward pointer
    return rest                 // rest is the new head (originally the last node)
```

**Both: O(n) time, O(1) iterative space / O(n) recursive stack space.**

---

### P6. Detect a cycle in a linked list (Floyd's Tortoise and Hare).

```
hasCycle(head):
    slow = head
    fast = head
    while fast ≠ NULL AND fast.next ≠ NULL:
        slow = slow.next           // move 1 step
        fast = fast.next.next      // move 2 steps
        if slow == fast:
            return True            // cycle detected
    return False                   // no cycle (fast reached end)
```

**Why it works:** If there's a cycle, fast will eventually "lap" slow inside the cycle. If there's no cycle, fast reaches NULL.

**Time: O(n). Space: O(1).**

**Finding the cycle start:** After detecting the cycle (slow == fast), reset slow to head. Move both one step at a time. They meet at the **cycle start**.

---

*End of Chapter 2*
