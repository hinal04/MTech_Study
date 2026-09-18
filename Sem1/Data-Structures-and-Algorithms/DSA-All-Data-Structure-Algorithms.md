# All Data Structure Algorithms — Super Simple Explanation

> Every algorithm for every data structure, explained so simply you can remember and write in the exam.
> Each has: **What it does → Steps → Pseudocode → Time complexity**

---
---

# 1. ARRAY

> **Think of it as:** A row of numbered boxes. Box 0, Box 1, Box 2, ...
> **Key property:** Access any box instantly by its number (index).

### Insert at End — O(1)

```
WHAT: Add element at the last position
STEPS:
  1. Put value at index [size]
  2. Increase size by 1

INSERT_END(A, value):
    A[size] = value
    size = size + 1
```

**Example:**
```
Array: [10, 20, 30, _, _]   size=3
INSERT_END(A, 40)
  A[3] = 40, size = 4
Result: [10, 20, 30, 40, _]   size=4
```

### Insert at Position i — O(n)

```
WHAT: Add element at position i, shift everything after it to the right
STEPS:
  1. Start from the LAST element
  2. Shift each element ONE position to the right
  3. Stop when you reach position i
  4. Put new value at position i
  5. Increase size by 1

INSERT_AT(A, i, value):
    for j = size-1 down to i:       ← start from end, go backwards
        A[j+1] = A[j]               ← shift right
    A[i] = value                     ← put new value
    size = size + 1
```

**Why O(n)?** In worst case (insert at position 0), you shift ALL n elements.

**Example: Insert 15 at position 1:**
```
Before: [10, 20, 30, _, _]   size=3
  Shift: A[3]=A[2]=30, A[2]=A[1]=20
  Place: A[1]=15, size=4
After:  [10, 15, 20, 30, _]   size=4
```

### Delete at Position i — O(n)

```
WHAT: Remove element at position i, shift everything after it to the left
STEPS:
  1. Start from position i+1
  2. Shift each element ONE position to the left
  3. Decrease size by 1

DELETE_AT(A, i):
    for j = i to size-2:            ← start from i, go forward
        A[j] = A[j+1]              ← shift left
    size = size - 1
```

**Example: Delete at position 1:**
```
Before: [10, 15, 20, 30]   size=4
  Shift: A[1]=A[2]=20, A[2]=A[3]=30
  size=3
After:  [10, 20, 30, _]   size=3
```

### Search — O(n)

```
WHAT: Find if a value exists in the array
STEPS:
  1. Check each element one by one
  2. If found, return index
  3. If reached end without finding, return -1

SEARCH(A, value):
    for i = 0 to size-1:
        if A[i] == value:
            return i
    return -1
```

**Example: Search for 20:**
```
Array: [10, 20, 30]
  i=0: A[0]=10 ≠ 20
  i=1: A[1]=20 == 20 → return 1 ✓
```

```
Just return A[i]. That's it. Arrays give INSTANT access by index.
```

---
---

# 2. STACK (LIFO — Last In, First Out)

> **Think of it as:** A stack of plates. You can only add/remove from the TOP.
> **Key property:** Last plate put on = first plate taken off.

### Push (Add to top) — O(1)

```
WHAT: Add element on top of stack
STEPS:
  1. Increase top by 1
  2. Put value at top position

PUSH(S, value):
    if top == capacity - 1:
        ERROR "Stack Overflow"      ← stack is full
    top = top + 1
    S[top] = value
```

**Example:**
```
Stack: [10, 20, _]  top=1
PUSH(S, 30): top=2, S[2]=30
Stack: [10, 20, 30]  top=2
```

```
WHAT: Remove and return the top element
STEPS:
  1. Save the top value
  2. Decrease top by 1
  3. Return saved value

POP(S):
    if top == -1:
        ERROR "Stack Underflow"     ← stack is empty
    value = S[top]
    top = top - 1
    return value
```

**Example:**
```
Stack: [10, 20, 30]  top=2
POP(S): value=30, top=1, return 30
Stack: [10, 20, _]  top=1
```

```
PEEK(S):
    if top == -1:
        ERROR "Stack is empty"
    return S[top]                   ← just look, don't remove
```

### isEmpty — O(1)

```
IS_EMPTY(S):
    return (top == -1)
```

**Memory trick:** Stack = pile of plates. Push = put plate on top. Pop = take plate from top. Can't touch bottom plates.

---
---

# 3. QUEUE (FIFO — First In, First Out)

> **Think of it as:** A line at a ticket counter. First person in line = first to get ticket.
> **Key property:** First in = first out. Add at REAR, remove from FRONT.

### Enqueue (Add at rear) — O(1)

```
WHAT: Add element at the back of the queue
STEPS:
  1. Increase rear by 1
  2. Put value at rear position

ENQUEUE(Q, value):
    if rear == capacity - 1:
        ERROR "Queue Overflow"
    rear = rear + 1
    Q[rear] = value
```

**Example:**
```
Queue: [10, 20, _, _]  front=0, rear=1
ENQUEUE(Q, 30): rear=2, Q[2]=30
Queue: [10, 20, 30, _]  front=0, rear=2
```

```
WHAT: Remove and return the front element
STEPS:
  1. Save the front value
  2. Increase front by 1
  3. Return saved value

DEQUEUE(Q):
    if front > rear:
        ERROR "Queue Underflow"
    value = Q[front]
    front = front + 1
    return value
```

**Example:**
```
Queue: [10, 20, 30]  front=0, rear=2
DEQUEUE(Q): value=10, front=1, return 10
Queue: [_, 20, 30]  front=1, rear=2
``` When you dequeue, front moves right. Slots before front are WASTED. Even if there's space at the beginning, rear says "full". This is **phantom overflow** → solved by circular queue.

---
---

# 4. CIRCULAR QUEUE

> **Think of it as:** People sitting in a CIRCLE. After the last seat, you go back to seat 0.
> **Key property:** Uses modular arithmetic `(index + 1) % capacity` to wrap around.

### Enqueue — O(1)

```
WHAT: Add element at rear, wrapping around if needed
STEPS:
  1. Calculate new rear: (rear + 1) % capacity
  2. If new rear == front → queue is FULL
  3. Otherwise, put value at new rear

ENQUEUE(Q, value):
    newRear = (rear + 1) % capacity
    if newRear == front:
        ERROR "Queue Full"
    rear = newRear
    Q[rear] = value
    count = count + 1
```

**Example (capacity=4):**
```
Queue: [10, 20, 30, _]  front=0, rear=2, count=3
ENQUEUE(Q, 40):
  newRear = (2+1)%4 = 3
  Q[3]=40, count=4
Queue: [10, 20, 30, 40]  front=0, rear=3, count=4 (FULL)

ENQUEUE(Q, 50):
  newRear = (3+1)%4 = 0 → 0 == front → FULL! Can't insert.
```
STEPS:
  1. Save front value
  2. Move front: (front + 1) % capacity
  3. Return saved value

DEQUEUE(Q):
    if count == 0:
        ERROR "Queue Empty"
    value = Q[front]
    front = (front + 1) % capacity
    count = count - 1
    return value
```

**Example (capacity=4, queue was full):**
```
Queue: [10, 20, 30, 40]  front=0, rear=3, count=4
DEQUEUE(Q):
  value=Q[0]=10, front=(0+1)%4=1, count=3
Queue: [_, 20, 30, 40]  front=1, rear=3, count=3

Now ENQUEUE(Q, 50):
  newRear=(3+1)%4=0 → Q[0]=50 ← WRAPS AROUND!
Queue: [50, 20, 30, 40]  front=1, rear=0, count=4
```

```
Capacity = 5, indices 0-4

After 5 enqueues:  [10, 20, 30, 40, 50]  front=0, rear=4 (FULL)
After 2 dequeues:  [__, __, 30, 40, 50]  front=2, rear=4

Now enqueue 60:
  rear = (4 + 1) % 5 = 0  ← WRAPS to index 0!
  Result: [60, __, 30, 40, 50]  front=2, rear=0

This is the magic — rear wraps around to use the empty space that dequeue freed!
```

**Full vs Empty detection (3 approaches):**
1. **Count variable:** full = (count == capacity), empty = (count == 0) ← simplest
2. **Waste one slot:** full = ((rear+1)%cap == front), empty = (front == rear)
3. **Boolean flag:** keep a `isFull` boolean

---
---

# 5. DOUBLE-ENDED QUEUE (DEQUE)

> **Think of it as:** A line where you can enter/leave from BOTH ENDS.
> **Key property:** Insert and delete at BOTH front and rear — O(1) each.

### Insert at Front — O(1)

```
INSERT_FRONT(D, value):
    front = (front - 1 + capacity) % capacity   ← move front BACKWARDS (with wrap)
    D[front] = value
    count = count + 1
```

**Example (capacity=5):**
```
Deque: [_, 10, 20, _, _]  front=1, rear=2
INSERT_FRONT(D, 5):
  front = (1-1+5)%5 = 0, D[0]=5
Deque: [5, 10, 20, _, _]  front=0, rear=2
```

```
INSERT_REAR(D, value):
    rear = (rear + 1) % capacity                ← move rear FORWARD (with wrap)
    D[rear] = value
    count = count + 1
```

**Example:**
```
Deque: [5, 10, 20, _, _]  front=0, rear=2
INSERT_REAR(D, 30):
  rear=(2+1)%5=3, D[3]=30
Deque: [5, 10, 20, 30, _]  front=0, rear=3
```

```
DELETE_FRONT(D):
    value = D[front]
    front = (front + 1) % capacity              ← move front FORWARD
    count = count - 1
    return value
```

**Example:**
```
Deque: [5, 10, 20, 30, _]  front=0, rear=3
DELETE_FRONT(D): value=5, front=(0+1)%5=1, return 5
Deque: [_, 10, 20, 30, _]  front=1, rear=3
```

```
DELETE_REAR(D):
    value = D[rear]
    rear = (rear - 1 + capacity) % capacity     ← move rear BACKWARDS (with wrap)
    count = count - 1
    return value
```

**Example:**
```
Deque: [_, 10, 20, 30, _]  front=1, rear=3
DELETE_REAR(D): value=30, rear=(3-1+5)%5=2, return 30
Deque: [_, 10, 20, _, _]  front=1, rear=2
```
- **Input-restricted deque:** Insert at ONE end only, delete from BOTH
- **Output-restricted deque:** Delete from ONE end only, insert at BOTH

---
---

# 6. PRIORITY QUEUE

> **Think of it as:** A hospital emergency room. Patient with highest priority gets treated first, not the one who came first.
> **Key property:** Each element has a priority. Highest priority comes out first.

### Using Unsorted Array

```
INSERT — O(1):  Just add at end (no ordering needed)
    A[size] = value
    size = size + 1

DELETE_MAX — O(n):  Scan entire array to find max, remove it
    maxIdx = 0
    for i = 1 to size-1:
        if A[i] > A[maxIdx]:
            maxIdx = i
    swap A[maxIdx] with A[size-1]
    size = size - 1
```

**Example (Unsorted Array):**
```
Insert 30, 10, 50, 20:
  Array: [30, 10, 50, 20]   ← just add, no sorting

DELETE_MAX:
  Scan: max is 50 at index 2
  Swap 50 ↔ 20 → [30, 10, 20, 50], size=3
  Array: [30, 10, 20]   returned 50
```

```
INSERT — O(n):  Find correct position, shift right, insert (like insertion sort)
    Find position i where A[i-1] ≤ value ≤ A[i]
    Shift A[i..size-1] right by 1
    A[i] = value
    size = size + 1

DELETE_MAX — O(1):  Max is always at the end (sorted ascending)
    size = size - 1
    return A[size]      ← last element is max
```

### Using Binary Heap (BEST approach) — see Heap section

```
INSERT — O(log n):  Add at end, bubble up
DELETE_MAX — O(log n):  Swap root with last, bubble down (heapify)
FIND_MAX — O(1):  Just return root
```

| Implementation | Insert | Delete Max | Find Max |
|---|---|---|---|
| Unsorted Array | **O(1)** | O(n) | O(n) |
| Sorted Array | O(n) | **O(1)** | **O(1)** |
| **Binary Heap** | O(log n) | O(log n) | **O(1)** |

> **Use Binary Heap** — best overall balance.

---
---

# 7. SINGLY LINKED LIST

> **Think of it as:** A chain of boxes. Each box has DATA + an ARROW pointing to the next box. Last box's arrow points to NULL.
> **Key property:** No fixed size. Can grow/shrink. But can only go FORWARD (no going back).

```
Structure:
  [DATA | NEXT] → [DATA | NEXT] → [DATA | NEXT] → NULL
       ↑
      HEAD
```

### Insert at Head — O(1)

```
WHAT: Add new node at the beginning
STEPS:
  1. Create new node with value
  2. Point new node's NEXT to current HEAD
  3. Update HEAD to point to new node

INSERT_HEAD(value):
    newNode = create node(value)
    newNode.next = head             ← new node points to old head
    head = newNode                  ← head now points to new node
```

**Example:**
```
Before: HEAD → [10] → [20] → NULL
INSERT_HEAD(5):
  Create [5], [5].next = HEAD([10])
  HEAD = [5]
After:  HEAD → [5] → [10] → [20] → NULL
```

### Insert at Tail — O(n)

```
WHAT: Add new node at the end
STEPS:
  1. Create new node with value, next = NULL
  2. If list is empty, head = newNode, done
  3. Otherwise, walk to the LAST node (the one whose next is NULL)
  4. Point last node's NEXT to new node

INSERT_TAIL(value):
    newNode = create node(value)
    newNode.next = NULL
    if head == NULL:                ← empty list
        head = newNode
        return
    current = head
    while current.next != NULL:    ← walk to end
        current = current.next
    current.next = newNode         ← last node now points to new node
```

**Example:**
```
Before: HEAD → [5] → [10] → NULL
INSERT_TAIL(20):
  Walk: [5].next=[10], [10].next=NULL → [10] is last
  [10].next = [20], [20].next = NULL
After:  HEAD → [5] → [10] → [20] → NULL
```

**Why O(n)?** Must walk through ALL nodes to find the last one. (Can be O(1) if you keep a TAIL pointer.)

### Insert at Position i — O(n)

```
INSERT_AT(i, value):
    if i == 0:
        INSERT_HEAD(value)
        return
    newNode = create node(value)
    current = head
    for j = 0 to i-2:              ← walk to node BEFORE position i
        current = current.next
    newNode.next = current.next     ← new node points to what was at position i
    current.next = newNode          ← previous node points to new node
```

### Delete Head — O(1)

```
DELETE_HEAD():
    if head == NULL:
        ERROR "List empty"
    value = head.data
    head = head.next                ← head now points to second node
    return value
```

**Example:**
```
Before: HEAD → [5] → [10] → [20] → NULL
DELETE_HEAD(): value=5, HEAD = [10]
After:  HEAD → [10] → [20] → NULL   returned 5
```

```
DELETE_AT(i):
    if i == 0:
        return DELETE_HEAD()
    current = head
    for j = 0 to i-2:              ← walk to node BEFORE the one to delete
        current = current.next
    toDelete = current.next
    current.next = toDelete.next    ← skip over the deleted node
    return toDelete.data
```

**Visual (delete position 1):**
```
Before: HEAD → [5] → [10] → [20] → NULL
                      ↑ delete this
  current = [5]
  [5].next = [10].next = [20]
After:  HEAD → [5] → [20] → NULL
```

### Search — O(n)

```
SEARCH(value):
    current = head
    while current != NULL:
        if current.data == value:
            return current          ← found!
        current = current.next
    return NULL                     ← not found
```

**Example: Search for 20:**
```
HEAD → [5] → [10] → [20] → NULL
  [5].data=5 ≠ 20 → next
  [10].data=10 ≠ 20 → next
  [20].data=20 == 20 → FOUND! return [20]
```

```
WHAT: Reverse the entire list
STEPS:
  1. Keep 3 pointers: prev, current, next
  2. For each node: save next, flip arrow to prev, advance all 3

REVERSE():
    prev = NULL
    current = head
    while current != NULL:
        next = current.next         ← save next before we break the link
        current.next = prev         ← FLIP the arrow (point backwards)
        prev = current              ← advance prev
        current = next              ← advance current
    head = prev                     ← prev is now the new head
```

**Visual:**
```
Before: [5] → [10] → [20] → NULL
Step 1: NULL ← [5]   [10] → [20] → NULL   (flip [5]'s arrow)
Step 2: NULL ← [5] ← [10]   [20] → NULL   (flip [10]'s arrow)
Step 3: NULL ← [5] ← [10] ← [20]          (flip [20]'s arrow)
HEAD = [20]
After:  [20] → [10] → [5] → NULL
```

---
---

# 8. CIRCULAR LINKED LIST

> **Think of it as:** Same as singly linked list, BUT the last node points BACK to HEAD (not NULL).
> **Key property:** No NULL anywhere. You detect the end by checking if you're back at HEAD.

```
Structure:
  HEAD → [10] → [20] → [30] ──┐
           ↑                    │
           └────────────────────┘
  (last node's NEXT points to HEAD)
```

### Insert at Head — O(n) or O(1) with tail pointer

```
WHAT: Add node at beginning. Must also update LAST node's next to point to new head.

INSERT_HEAD(value):
    newNode = create node(value)
    if head == NULL:                    ← empty list
        head = newNode
        newNode.next = newNode          ← points to itself
        return
    
    // Find the last node (the one whose next == head)
    last = head
    while last.next != head:
        last = last.next
    
    newNode.next = head                 ← new node points to old head
    last.next = newNode                 ← last node now points to new head
    head = newNode                      ← update head
```

**With tail pointer (O(1)):**
```
INSERT_HEAD(value):
    newNode = create node(value)
    if head == NULL:
        head = newNode
        tail = newNode
        newNode.next = newNode
        return
    newNode.next = head
    tail.next = newNode                 ← tail already known, just update
    head = newNode
```

### Insert at Tail — O(n) or O(1) with tail pointer

```
INSERT_TAIL(value):
    newNode = create node(value)
    if head == NULL:
        head = newNode
        newNode.next = newNode
        return
    
    last = head
    while last.next != head:            ← find last node
        last = last.next
    
    last.next = newNode                 ← old last points to new node
    newNode.next = head                 ← new node points back to head
```

### Delete Head — O(n) or O(1) with tail pointer

```
DELETE_HEAD():
    if head == NULL: ERROR
    if head.next == head:               ← only one node
        head = NULL
        return
    
    last = head
    while last.next != head:            ← find last node
        last = last.next
    
    value = head.data
    head = head.next                    ← move head forward
    last.next = head                    ← last now points to new head
    return value
```

### Traversal — O(n)

```
WHAT: Visit every node exactly once. Stop when you return to HEAD.

TRAVERSE():
    if head == NULL: return
    current = head
    do:
        print current.data
        current = current.next
    while current != head               ← STOP when back at head
```

> **Key difference from singly linked list:** Use `do...while` (not `while`), because the stopping condition `current != head` is false at the start.

---
---

# 9. DOUBLY LINKED LIST

> **Think of it as:** A chain of boxes, but each box has TWO arrows — one to NEXT and one to PREV. You can go BOTH directions.
> **Key property:** Can traverse forward AND backward. Delete a node in O(1) if you have its pointer.

```
Structure:
  NULL ← [PREV | DATA | NEXT] ⇄ [PREV | DATA | NEXT] ⇄ [PREV | DATA | NEXT] → NULL
              ↑                                                    
             HEAD                                                 
```

### Insert at Head — O(1)

```
INSERT_HEAD(value):
    newNode = create node(value)
    newNode.prev = NULL
    newNode.next = head
    if head != NULL:
        head.prev = newNode             ← old head's prev points to new node
    head = newNode
```

**Example:**
```
Before: NULL ← [10] ⇄ [20] → NULL
INSERT_HEAD(5):
  [5].prev=NULL, [5].next=[10]
  [10].prev=[5], head=[5]
After:  NULL ← [5] ⇄ [10] ⇄ [20] → NULL
```

### Insert at Tail — O(n) or O(1) with tail pointer

```
INSERT_TAIL(value):
    newNode = create node(value)
    newNode.next = NULL
    if head == NULL:
        newNode.prev = NULL
        head = newNode
        return
    
    current = head
    while current.next != NULL:         ← walk to last node
        current = current.next
    
    current.next = newNode
    newNode.prev = current
```

### Delete a Node (given pointer to it) — O(1) ⭐

```
WHAT: Delete a node when you already have a pointer to it. This is the BIG advantage of doubly linked list.

DELETE_NODE(node):
    if node.prev != NULL:
        node.prev.next = node.next      ← previous node skips over this one
    else:
        head = node.next                ← deleting head
    
    if node.next != NULL:
        node.next.prev = node.prev      ← next node's prev skips over this one
    
    free(node)
```

**Example (delete [20]):**
```
Before: NULL ← [10] ⇄ [20] ⇄ [30] → NULL
DELETE_NODE([20]):
  [20].prev=[10], [20].next=[30]
  [10].next = [30]     ← skip forward over [20]
  [30].prev = [10]     ← skip backward over [20]
After:  NULL ← [10] ⇄ [30] → NULL
```

> **Why O(1)?** In singly linked list, to delete a node you need the PREVIOUS node (must walk from head to find it — O(n)). In doubly linked list, the node itself has a prev pointer, so you already know the previous node.

### Delete Head — O(1)

```
DELETE_HEAD():
    if head == NULL: ERROR
    value = head.data
    head = head.next
    if head != NULL:
        head.prev = NULL
    return value
```

### Reverse Traversal (from tail to head) — O(n)

```
REVERSE_TRAVERSE():
    current = tail                      ← start from end
    while current != NULL:
        print current.data
        current = current.prev          ← go backwards using prev pointer
```

### Search — O(n)

Same as singly linked list — walk through nodes checking data.

---
---

# 10. CIRCULAR DOUBLY LINKED LIST

> **Think of it as:** A ring of boxes. Each box has PREV and NEXT arrows. Last connects to first, first connects to last. You can go around in BOTH directions forever.
> **Key property:** No NULL anywhere. head.prev = last node. last.next = head.

```
Structure:
  ┌──────────────────────────────────────────────┐
  │                                              │
  ↓                                              │
  [PREV | DATA | NEXT] ⇄ [PREV | DATA | NEXT] ⇄ [PREV | DATA | NEXT]
  ↑          HEAD                                          ↓
  │                                                        │
  └────────────────────────────────────────────────────────┘
```

### Insert at Head — O(1)

```
INSERT_HEAD(value):
    newNode = create node(value)
    if head == NULL:                    ← empty list
        head = newNode
        newNode.next = newNode          ← points to itself
        newNode.prev = newNode          ← points to itself
        return
    
    last = head.prev                    ← last node is just head.prev! (no walking needed)
    
    newNode.next = head                 ← new node points forward to old head
    newNode.prev = last                 ← new node points backward to last
    last.next = newNode                 ← last node points forward to new head
    head.prev = newNode                 ← old head points backward to new head
    head = newNode                      ← update head
```

**Example (list has [10] ⇄ [20], circular):**
```
Before: [10] ⇄ [20] (head=[10], [20].next=[10], [10].prev=[20])
INSERT_HEAD(5):
  last = head.prev = [20]
  [5].next=[10], [5].prev=[20]
  [20].next=[5], [10].prev=[5]
  head=[5]
After: [5] ⇄ [10] ⇄ [20] → back to [5]
```

> **Beautiful thing:** We get the last node in O(1) via `head.prev`. No need to walk through the entire list!

### Insert at Tail — O(1)

```
INSERT_TAIL(value):
    newNode = create node(value)
    if head == NULL:
        head = newNode
        newNode.next = newNode
        newNode.prev = newNode
        return
    
    last = head.prev                    ← current last node
    
    newNode.next = head                 ← new node points forward to head
    newNode.prev = last                 ← new node points backward to old last
    last.next = newNode                 ← old last points forward to new node
    head.prev = newNode                 ← head points backward to new last
```

**Example:**
```
Before: [5] ⇄ [10] ⇄ [20] → back to [5]
INSERT_TAIL(30):
  last=[20] (via head.prev)
  [30].next=[5], [30].prev=[20]
  [20].next=[30], [5].prev=[30]
After: [5] ⇄ [10] ⇄ [20] ⇄ [30] → back to [5]
```

```
DELETE_HEAD():
    if head == NULL: ERROR
    if head.next == head:               ← only one node
        head = NULL
        return
    
    last = head.prev
    newHead = head.next
    
    last.next = newHead                 ← last now points to second node
    newHead.prev = last                 ← second node now points back to last
    head = newHead                      ← update head
```

### Delete a Node (given pointer) — O(1)

```
DELETE_NODE(node):
    if node.next == node:               ← only one node in list
        head = NULL
        return
    
    node.prev.next = node.next          ← previous skips over
    node.next.prev = node.prev          ← next skips over
    
    if node == head:                    ← if deleting head, update it
        head = node.next
    
    free(node)
```

### Traversal (forward) — O(n)

```
TRAVERSE_FORWARD():
    if head == NULL: return
    current = head
    do:
        print current.data
        current = current.next
    while current != head               ← stop when back at head
```

### Traversal (backward) — O(n)

```
TRAVERSE_BACKWARD():
    if head == NULL: return
    current = head.prev                 ← start from last node
    do:
        print current.data
        current = current.prev
    while current != head.prev          ← stop when back at last node
```

---
---

# 📋 MASTER COMPARISON TABLE

| Data Structure | Insert Head | Insert Tail | Delete Head | Delete Any | Search | Access by Index |
|---|---|---|---|---|---|---|
| **Array** | O(n) | O(1) | O(n) | O(n) | O(n) | **O(1)** |
| **Stack** | O(1) push | — | O(1) pop | — | O(n) | O(n) |
| **Queue** | — | O(1) enq | O(1) deq | — | O(n) | O(n) |
| **Circular Queue** | — | O(1) | O(1) | — | O(n) | O(n) |
| **Deque** | O(1) | O(1) | O(1) | — | O(n) | O(n) |
| **Priority Queue (Heap)** | O(log n) | — | O(log n) | — | O(n) | — |
| **Singly Linked List** | **O(1)** | O(n)* | **O(1)** | O(n) | O(n) | O(n) |
| **Circular Linked List** | O(n)** | O(n)** | O(n)** | O(n) | O(n) | O(n) |
| **Doubly Linked List** | **O(1)** | O(n)* | **O(1)** | **O(1)*** | O(n) | O(n) |
| **Circular Doubly LL** | **O(1)** | **O(1)** | **O(1)** | **O(1)*** | O(n) | O(n) |

*O(1) with tail pointer
**O(1) with tail pointer
***O(1) if you have pointer to the node; O(n) if you need to find it first

---

# 🧠 MEMORY TRICKS

| Structure | Remember As |
|---|---|
| **Array** | Numbered boxes. Instant access by number. Hard to insert/delete in middle (shift). |
| **Stack** | Plate stack. LIFO. push/pop from TOP only. |
| **Queue** | Ticket line. FIFO. Enter at REAR, leave from FRONT. |
| **Circular Queue** | Circular track. Rear wraps to 0 after last index. `(rear+1) % capacity`. |
| **Deque** | Double door. Enter/leave from BOTH ends. |
| **Priority Queue** | Hospital ER. Highest priority first, not first-come. Use Heap. |
| **Singly LL** | Chain. Each box has DATA + one arrow (NEXT). Can only go forward. |
| **Circular LL** | Ring chain. Last box arrows back to first. No NULL. Use do-while to traverse. |
| **Doubly LL** | Two-way chain. Each box has PREV + DATA + NEXT. Delete in O(1) with pointer. |
| **Circular Doubly LL** | Two-way ring. head.prev = last, last.next = head. Insert at tail in O(1). Most flexible. |

---

# 11. BINARY TREE — Linked Representation & Operations

> **Think of it as:** Each node is a box with THREE things: LEFT pointer, DATA, RIGHT pointer.
> **Key property:** Each node has at most 2 children (left and right).

```
Node Structure:
  [LEFT | DATA | RIGHT]

Tree Example:
            [50]
           /    \
        [30]    [70]
        /  \      \
     [20] [40]   [80]
```

### Node Structure (Linked Representation)

```
struct Node:
    data        ← the value stored
    left        ← pointer to left child (NULL if no left child)
    right       ← pointer to right child (NULL if no right child)
```

### Count Nodes — O(n)

```
COUNT(node):
    if node == NULL:
        return 0
    return 1 + COUNT(node.left) + COUNT(node.right)
```

### Find Height — O(n)

```
HEIGHT(node):
    if node == NULL:
        return -1                   ← empty tree has height -1
    leftH = HEIGHT(node.left)
    rightH = HEIGHT(node.right)
    return 1 + max(leftH, rightH)
```

### Find Number of Leaves — O(n)

```
COUNT_LEAVES(node):
    if node == NULL:
        return 0
    if node.left == NULL AND node.right == NULL:
        return 1                    ← this IS a leaf
    return COUNT_LEAVES(node.left) + COUNT_LEAVES(node.right)
```

---
---

# 12. BINARY TREE TRAVERSALS

> **4 ways to visit every node.** Each visits ALL nodes, just in different ORDER.
> **Memory trick:** The name tells you when you visit the ROOT — Pre=first, In=middle, Post=last.

### Preorder (Root → Left → Right) — NLR — O(n)

```
Visit the root FIRST, then go left, then go right.
Mnemonic: "NLR" = Node, Left, Right

PREORDER(node):
    if node == NULL: return
    PRINT node.data              ← visit root FIRST
    PREORDER(node.left)          ← then left subtree
    PREORDER(node.right)         ← then right subtree
```

**Example:**
```
        50
       /  \
      30   70
     / \     \
    20  40   80

Preorder: 50, 30, 20, 40, 70, 80
(Visit root 50 → go left to 30 → go left to 20 → back → go right to 40 → back → go right to 70 → go right to 80)
```

### Inorder (Left → Root → Right) — LNR — O(n)

```
Go left FIRST, then visit root, then go right.
⭐ For a BST, inorder gives SORTED output!
Mnemonic: "LNR" = Left, Node, Right

INORDER(node):
    if node == NULL: return
    INORDER(node.left)           ← go left first
    PRINT node.data              ← visit root in MIDDLE
    INORDER(node.right)          ← then go right
```

**Example (same tree):**
```
Inorder: 20, 30, 40, 50, 70, 80   ← SORTED! (because it's a BST)
```

### Postorder (Left → Right → Root) — LRN — O(n)

```
Go left, then right, visit root LAST.
Used for: deleting a tree (delete children before parent)
Mnemonic: "LRN" = Left, Right, Node

POSTORDER(node):
    if node == NULL: return
    POSTORDER(node.left)         ← go left first
    POSTORDER(node.right)        ← then right
    PRINT node.data              ← visit root LAST
```

**Example (same tree):**
```
Postorder: 20, 40, 30, 80, 70, 50
```

### Level-Order (BFS — level by level) — O(n)

```
Visit level 0, then level 1, then level 2, ...
Uses a QUEUE.

LEVEL_ORDER(root):
    if root == NULL: return
    queue = empty queue
    enqueue(root)
    while queue is not empty:
        node = dequeue()
        PRINT node.data
        if node.left != NULL:  enqueue(node.left)
        if node.right != NULL: enqueue(node.right)
```

**Example:**
```
Level-order: 50, 30, 70, 20, 40, 80
(Level 0: 50 → Level 1: 30, 70 → Level 2: 20, 40, 80)
```

### Quick Summary Table

| Traversal | Order | Mnemonic | BST Gives | Used For |
|---|---|---|---|---|
| **Preorder** | Root→Left→Right | NLR | — | Copy tree, serialize |
| **Inorder** | Left→Root→Right | LNR | **Sorted output** | Print sorted BST |
| **Postorder** | Left→Right→Root | LRN | — | Delete tree, evaluate expression |
| **Level-order** | Level by level | Queue | — | BFS, shortest depth |

---
---

# 13. RECONSTRUCT TREE FROM TRAVERSALS

> Given TWO traversals, can you build the UNIQUE tree? Here are the rules and algorithms.

### Rule: Which Combinations Work?

| Combination | Can Build Unique Tree? | Why |
|---|---|---|
| **Preorder + Inorder** | ✅ Yes | Preorder gives root; inorder splits left/right subtrees |
| **Postorder + Inorder** | ✅ Yes | Postorder gives root (last element); inorder splits left/right |
| **Preorder + Postorder** | ❌ No (not unique) | Can't determine left vs right for nodes with only one child |

### Algorithm: Build Tree from Preorder + Inorder — O(n)

```
KEY INSIGHT:
  - First element of PREORDER = ROOT
  - Find root in INORDER → everything LEFT of it = left subtree, RIGHT of it = right subtree
  - Recurse for left and right subtrees

BUILD_FROM_PRE_IN(preorder, inorder):
    if preorder is empty: return NULL
    
    root = preorder[0]                           ← first of preorder is always the root
    rootIndex = find root in inorder             ← find where root sits in inorder
    
    leftInorder  = inorder[0 ... rootIndex-1]    ← everything LEFT of root in inorder
    rightInorder = inorder[rootIndex+1 ... end]  ← everything RIGHT of root in inorder
    
    leftSize = length of leftInorder
    leftPreorder  = preorder[1 ... leftSize]     ← next leftSize elements in preorder
    rightPreorder = preorder[leftSize+1 ... end] ← remaining elements in preorder
    
    node = create Node(root)
    node.left  = BUILD_FROM_PRE_IN(leftPreorder, leftInorder)
    node.right = BUILD_FROM_PRE_IN(rightPreorder, rightInorder)
    return node
```

**Worked Example:**
```
Preorder: A B D E C F
Inorder:  D B E A F C

Step 1: Root = A (first of preorder)
        Find A in inorder: index 3
        Left inorder:  [D B E]     Right inorder:  [F C]
        Left preorder: [B D E]     Right preorder: [C F]

Step 2 (left): Root = B (first of [B D E])
        Find B in [D B E]: index 1
        Left: [D]    Right: [E]

Step 3 (right): Root = C (first of [C F])
        Find C in [F C]: index 1
        Left: [F]    Right: []

Result:
        A
       / \
      B   C
     / \ /
    D  E F
```

### Algorithm: Build Tree from Postorder + Inorder — O(n)

```
KEY INSIGHT:
  - LAST element of POSTORDER = ROOT
  - Find root in INORDER → splits left/right
  - Build RIGHT subtree first, then LEFT (process postorder from end)

BUILD_FROM_POST_IN(postorder, inorder):
    if postorder is empty: return NULL
    
    root = postorder[LAST]                       ← LAST of postorder is the root
    rootIndex = find root in inorder
    
    leftInorder  = inorder[0 ... rootIndex-1]
    rightInorder = inorder[rootIndex+1 ... end]
    
    leftSize = length of leftInorder
    leftPostorder  = postorder[0 ... leftSize-1]
    rightPostorder = postorder[leftSize ... end-1]  ← exclude last (root)
    
    node = create Node(root)
    node.left  = BUILD_FROM_POST_IN(leftPostorder, leftInorder)
    node.right = BUILD_FROM_POST_IN(rightPostorder, rightInorder)
    return node
```

**Worked Example:**
```
Postorder: D E B F C A
Inorder:   D B E A F C

Step 1: Root = A (last of postorder)
        Find A in inorder: index 3
        Left inorder:  [D B E]     Right inorder:  [F C]
        Left postorder: [D E B]    Right postorder: [F C]

Step 2 (left): Root = B (last of [D E B])
        Left: [D]    Right: [E]

Step 3 (right): Root = C (last of [F C])
        Left: [F]    Right: []

Same result:  A → B(D,E), C(F,_)
```

### Why Preorder + Postorder is NOT Unique

```
Example: Preorder = [A, B]   Postorder = [B, A]

This could be:      OR this:
    A                   A
   /                     \
  B                       B

Both give Preorder [A, B] and Postorder [B, A].
Without inorder, you can't tell if B is left or right child.
```

---
---

# 14. BINARY SEARCH TREE (BST)

> **Think of it as:** A binary tree where LEFT < ROOT < RIGHT for EVERY node.
> **Key property:** Inorder traversal gives sorted output. Search is O(log n) on balanced tree.

```
BST Rule:  Left subtree values < Node < Right subtree values

Example:
        50
       /  \
      30   70
     / \   / \
    20  40 60  80

Inorder: 20, 30, 40, 50, 60, 70, 80  ← SORTED!
```

### BST Search — O(h) where h = height

```
WHAT: Find if a value exists. Start at root, go left or right based on comparison.

BST_SEARCH(node, key):
    if node == NULL:
        return NULL                  ← not found
    if key == node.data:
        return node                  ← found!
    if key < node.data:
        return BST_SEARCH(node.left, key)    ← go LEFT (smaller)
    else:
        return BST_SEARCH(node.right, key)   ← go RIGHT (bigger)
```

**Why O(h)?** You go down ONE path from root to leaf. On balanced tree h = log n → O(log n). On skewed tree h = n → O(n).

### BST Insert — O(h)

```
WHAT: Insert a new value. Search for the right position, then add as a LEAF.

BST_INSERT(node, key):
    if node == NULL:
        return create Node(key)      ← found the spot! Create leaf here
    if key < node.data:
        node.left = BST_INSERT(node.left, key)    ← go left
    else if key > node.data:
        node.right = BST_INSERT(node.right, key)   ← go right
    // if key == node.data, duplicate — do nothing (or handle as needed)
    return node
```

**Example: Insert 35 into BST:**
```
        50                        50
       /  \                      /  \
      30   70     →             30   70
     / \                       / \
    20  40                    20  40
                                 /
                                35  ← new leaf!

Path: 50(go left) → 30(go right) → 40(go left) → NULL → insert here
```

### BST Find Min / Max — O(h)

```
FIND_MIN(node):                  FIND_MAX(node):
    while node.left != NULL:         while node.right != NULL:
        node = node.left                 node = node.right
    return node                      return node

Min = leftmost node               Max = rightmost node
```

### BST Delete — O(h) ⭐ (Most important — 3 cases)

```
WHAT: Delete a node. 3 cases depending on how many children it has.

CASE 1: Node is a LEAF (no children)
  → Just remove it. Set parent's pointer to NULL.

CASE 2: Node has ONE child
  → Replace node with its only child. (Child takes its place.)

CASE 3: Node has TWO children ⭐ (tricky case)
  → Find INORDER SUCCESSOR (smallest value in RIGHT subtree)
  → Copy successor's value to the node being deleted
  → Delete the successor (which will be Case 1 or Case 2)

BST_DELETE(node, key):
    if node == NULL: return NULL
    
    if key < node.data:
        node.left = BST_DELETE(node.left, key)
    else if key > node.data:
        node.right = BST_DELETE(node.right, key)
    else:
        // Found the node to delete
        
        // CASE 1 & 2: Zero or one child
        if node.left == NULL:
            return node.right        ← replace with right child (or NULL)
        if node.right == NULL:
            return node.left         ← replace with left child
        
        // CASE 3: Two children
        successor = FIND_MIN(node.right)       ← smallest in right subtree
        node.data = successor.data             ← copy successor's value
        node.right = BST_DELETE(node.right, successor.data)  ← delete successor
    
    return node
```

**Example: Delete 50 (two children):**
```
        50  ← delete this           60  ← successor takes its place
       /  \                         /  \
      30   70         →           30   70
     / \   / \                   / \     \
    20  40 60  80               20  40   80

Step 1: Find inorder successor of 50 = smallest in right subtree = 60
Step 2: Copy 60 into node (replace 50 with 60)
Step 3: Delete the original 60 from right subtree (it was a leaf → Case 1)
```

**Example: Delete 30 (one child):**
```
        50                        50
       /  \                      /  \
      30   70     →             20   70
     /
    20

30 has only left child (20). Replace 30 with 20.
```

### BST Inorder Successor — O(h)

```
WHAT: Find the next node in sorted order.

Two cases:
1. Node HAS right subtree → successor = leftmost node in right subtree
2. Node has NO right subtree → successor = nearest ancestor where node is in LEFT subtree

INORDER_SUCCESSOR(root, node):
    if node.right != NULL:
        return FIND_MIN(node.right)    ← Case 1: go right, then all the way left
    
    // Case 2: walk up from root
    successor = NULL
    current = root
    while current != NULL:
        if node.data < current.data:
            successor = current        ← this could be the successor
            current = current.left     ← go left (looking for closer ancestor)
        else if node.data > current.data:
            current = current.right
        else:
            break                      ← found the node
    return successor
```

### BST Complexity Summary

| Operation | Average (balanced) | Worst (skewed) |
|---|---|---|
| Search | **O(log n)** | O(n) |
| Insert | **O(log n)** | O(n) |
| Delete | **O(log n)** | O(n) |
| Find Min/Max | **O(log n)** | O(n) |
| Inorder (sorted) | **O(n)** | O(n) |

> **When does worst case happen?** When you insert sorted data (1,2,3,4,5) → BST becomes a straight line (linked list) → height = n.

---
---

# 15. MAX-HEAP

> **Think of it as:** A COMPLETE binary tree where every parent ≥ both children. Root = MAXIMUM.
> **Key property:** Root is always the largest. Used for priority queues and heap sort.
> **Array representation (1-indexed):** Parent = ⌊i/2⌋, Left child = 2i, Right child = 2i+1

```
Max-Heap Example:
        90
       /  \
      70   80
     / \   /
    50  60 40

Array (1-indexed): [_, 90, 70, 80, 50, 60, 40]
                       1   2   3   4   5   6
```

### Max-Heap Insert (Bubble UP) — O(log n)

```
WHAT: Add element at the END, then BUBBLE UP by swapping with parent while larger.

HEAP_INSERT(H, value):
    H.size = H.size + 1
    H[H.size] = value               ← add at end (maintains complete tree shape)
    
    // BUBBLE UP
    i = H.size
    while i > 1 AND H[i] > H[i/2]:  ← while current > parent
        swap(H[i], H[i/2])          ← swap with parent
        i = i / 2                    ← move up to parent position
```

**Example: Insert 95 into [90, 70, 80, 50, 60, 40]:**
```
Step 1: Add at end → [90, 70, 80, 50, 60, 40, 95]
                                                 ↑ index 7

Step 2: Bubble up:
  95 > parent(80 at index 3)? YES → swap → [90, 70, 95, 50, 60, 40, 80]
  95 > parent(90 at index 1)? YES → swap → [95, 70, 90, 50, 60, 40, 80]
  i = 1 (root) → stop

Final: [95, 70, 90, 50, 60, 40, 80]

        95
       /  \
      70   90
     / \   /
    50  60 80 40 → wait, let me recalculate...

Actually: [95, 70, 90, 50, 60, 40, 80]
Index:      1   2   3   4   5   6   7

        95
       /  \
      70   90
     / \  / \
    50 60 40 80
```

### Max-Heap Delete Max / Extract Max (Bubble DOWN) — O(log n)

```
WHAT: Remove the root (maximum). Replace with LAST element, then BUBBLE DOWN (heapify).

HEAP_EXTRACT_MAX(H):
    if H.size == 0: ERROR "Heap empty"
    
    max = H[1]                       ← save the root (maximum value)
    H[1] = H[H.size]                ← replace root with LAST element
    H.size = H.size - 1             ← reduce size
    
    MAX_HEAPIFY(H, 1)               ← bubble down from root
    return max

MAX_HEAPIFY(H, i):
    left = 2 * i
    right = 2 * i + 1
    largest = i
    
    if left <= H.size AND H[left] > H[largest]:
        largest = left
    if right <= H.size AND H[right] > H[largest]:
        largest = right
    
    if largest != i:                 ← child is bigger than parent
        swap(H[i], H[largest])       ← swap with larger child
        MAX_HEAPIFY(H, largest)      ← continue bubbling down
```

**Example: Extract max from [95, 70, 90, 50, 60, 40, 80]:**
```
Step 1: Save max = 95
Step 2: Move last to root: [80, 70, 90, 50, 60, 40]
Step 3: Heapify from root:
  80 vs children 70, 90 → largest = 90 → swap → [90, 70, 80, 50, 60, 40]
  80 vs children 40 → 80 > 40 → stop

Final: [90, 70, 80, 50, 60, 40]   max returned = 95
```

### Build Max-Heap (Bottom-Up Heapify) — O(n) ⭐

```
WHAT: Convert a random array into a max-heap. Heapify from BOTTOM to TOP.

BUILD_MAX_HEAP(A):
    H.size = length(A)
    for i = H.size/2 down to 1:     ← start from LAST NON-LEAF, go up to root
        MAX_HEAPIFY(H, i)

Why start from H.size/2?
  → Nodes from H.size/2+1 to H.size are LEAVES (no children to heapify)
  → Only need to heapify internal nodes
```

**Why O(n) and NOT O(n log n)?**
Most nodes are near the bottom and heapify very little. Nodes at height h do O(h) work. Sum = Σ (n/2^h) × h ≈ 2n → O(n).

**Example: Build max-heap from [4, 10, 3, 5, 1]:**
```
Array:       4, 10, 3, 5, 1
Indices:     1   2  3  4  5

Start from i = 5/2 = 2 (last non-leaf)

Heapify(2): Node 10, children 5(idx 4), 1(idx 5). 10 > both → no swap
Heapify(1): Node 4, children 10(idx 2), 3(idx 3). Largest = 10 → swap 4↔10
            → [10, 4, 3, 5, 1]
            Continue at idx 2: Node 4, children 5(idx 4), 1(idx 5). Largest = 5 → swap 4↔5
            → [10, 5, 3, 4, 1]

Final max-heap: [10, 5, 3, 4, 1]

        10
       /  \
      5    3
     / \
    4   1
```

### Heap Sort — O(n log n)

```
WHAT: Sort array using a max-heap.
STEPS:
  1. Build max-heap from array → O(n)
  2. Repeatedly: swap root (max) with last → reduce size → heapify → O(n log n)

HEAP_SORT(A):
    BUILD_MAX_HEAP(A)                ← Step 1: O(n)
    
    for i = A.size down to 2:        ← Step 2: repeat n-1 times
        swap(A[1], A[i])             ← move max to end (sorted position)
        A.size = A.size - 1          ← shrink heap
        MAX_HEAPIFY(A, 1)            ← fix the heap from root

    // After: array is sorted in ASCENDING order
```

**Properties:** Time = O(n log n), Space = O(1) in-place, NOT stable.

---
---

# 16. MIN-HEAP

> **Same as max-heap but REVERSED: every parent ≤ both children. Root = MINIMUM.**

### Min-Heap vs Max-Heap — The Only Difference

| | Max-Heap | Min-Heap |
|---|---|---|
| Property | Parent ≥ Children | Parent ≤ Children |
| Root | Maximum element | Minimum element |
| Bubble UP condition | Swap if child > parent | Swap if child < parent |
| Heapify condition | Swap with **larger** child | Swap with **smaller** child |
| Use | Max priority queue, heap sort | Min priority queue, Dijkstra's |

### Min-Heap Insert (Bubble UP) — O(log n)

```
MIN_HEAP_INSERT(H, value):
    H.size = H.size + 1
    H[H.size] = value
    
    i = H.size
    while i > 1 AND H[i] < H[PARENT(i)]:    ← LESS THAN parent (opposite of max)
        swap(H[i], H[PARENT(i)])
        i = PARENT(i)
```

### Min-Heap Extract Min (Bubble DOWN) — O(log n)

```
HEAP_EXTRACT_MIN(H):
    min = H[1]
    H[1] = H[H.size]
    H.size = H.size - 1
    MIN_HEAPIFY(H, 1)
    return min

MIN_HEAPIFY(H, i):
    left = 2 * i
    right = 2 * i + 1
    smallest = i                              ← find SMALLEST (not largest)
    
    if left <= H.size AND H[left] < H[smallest]:
        smallest = left
    if right <= H.size AND H[right] < H[smallest]:
        smallest = right
    
    if smallest != i:
        swap(H[i], H[smallest])
        MIN_HEAPIFY(H, smallest)
```

### Build Min-Heap — O(n)

```
BUILD_MIN_HEAP(A):
    H.size = length(A)
    for i = H.size/2 down to 1:
        MIN_HEAPIFY(H, i)
```

**Example: Build min-heap from [9, 6, 5, 0, 8, 2]:**
```
Start: [9, 6, 5, 0, 8, 2]

Heapify(3): 5 vs child 2 → swap → [9, 6, 2, 0, 8, 5]
Heapify(2): 6 vs children 0, 8 → smallest 0 → swap → [9, 0, 2, 6, 8, 5]
Heapify(1): 9 vs children 0, 2 → smallest 0 → swap → [0, 9, 2, 6, 8, 5]
            then 9 vs children 6, 8 → smallest 6 → swap → [0, 6, 2, 9, 8, 5]

Final: [0, 6, 2, 9, 8, 5]

        0
       / \
      6   2
     / \ /
    9  8 5
```

### Heap Increase Key / Decrease Key — O(log n)

```
MAX-HEAP:
  INCREASE_KEY(H, i, newKey):        ← make a key BIGGER in max-heap
      H[i] = newKey
      BUBBLE UP from i              ← bigger key might need to go up

  DECREASE_KEY(H, i, newKey):        ← make a key SMALLER in max-heap
      H[i] = newKey
      MAX_HEAPIFY(H, i)             ← smaller key might need to go down

MIN-HEAP:
  DECREASE_KEY(H, i, newKey):        ← make a key SMALLER in min-heap
      H[i] = newKey
      BUBBLE UP from i              ← smaller key might need to go up

  INCREASE_KEY(H, i, newKey):        ← make a key BIGGER in min-heap
      H[i] = newKey
      MIN_HEAPIFY(H, i)             ← bigger key might need to go down
```

### Heap Delete (arbitrary position) — O(log n)

```
WHAT: Delete element at any position i (not just root).

MAX_HEAP_DELETE(H, i):
    H[i] = H[H.size]                ← replace with last element
    H.size = H.size - 1
    
    if H[i] > H[PARENT(i)]:
        BUBBLE_UP(H, i)             ← if new value is bigger than parent, go up
    else:
        MAX_HEAPIFY(H, i)           ← if new value is smaller, go down
```

> ⚠️ Common exam question: Why does DELETE need BOTH bubble up AND heapify down? Because the last element could be bigger OR smaller than the replaced node's parent.

---
---

# 17. HEAPIFY — Detailed Step-by-Step

> **Heapify (also called Sift-Down / Percolate-Down)** is the CORE operation of heaps.
> It fixes ONE violation: when a node is smaller (max-heap) or larger (min-heap) than its children.

### Max-Heapify — Full Algorithm with Trace

```
MAX_HEAPIFY(A, n, i):
    // A = array, n = heap size, i = index to fix
    
    largest = i                      ← assume current node is largest
    left = 2 * i                     ← left child index
    right = 2 * i + 1                ← right child index
    
    // Step 1: Compare with left child
    if left <= n AND A[left] > A[largest]:
        largest = left
    
    // Step 2: Compare with right child
    if right <= n AND A[right] > A[largest]:
        largest = right
    
    // Step 3: If a child is larger, swap and continue
    if largest != i:
        swap(A[i], A[largest])
        MAX_HEAPIFY(A, n, largest)   ← recurse on the swapped position
```

**Complete Trace: Max-Heapify on [4, 10, 3, 5, 1] at index 1**

```
Array (1-indexed): [_, 4, 10, 3, 5, 1]
                       1   2  3  4  5

Call MAX_HEAPIFY(A, 5, 1):
  i=1, A[1]=4
  left=2, A[2]=10
  right=3, A[3]=3
  
  largest = 1 (initially)
  A[2]=10 > A[1]=4? YES → largest = 2
  A[3]=3 > A[2]=10? NO → largest stays 2
  
  largest(2) != i(1) → SWAP A[1]↔A[2]
  Array becomes: [_, 10, 4, 3, 5, 1]
  
  Recurse: MAX_HEAPIFY(A, 5, 2)
    i=2, A[2]=4
    left=4, A[4]=5
    right=5, A[5]=1
    
    largest = 2
    A[4]=5 > A[2]=4? YES → largest = 4
    A[5]=1 > A[4]=5? NO → largest stays 4
    
    largest(4) != i(2) → SWAP A[2]↔A[4]
    Array becomes: [_, 10, 5, 3, 4, 1]
    
    Recurse: MAX_HEAPIFY(A, 5, 4)
      i=4, left=8, right=9
      left=8 > n=5? YES → no children → STOP
    
DONE! Final: [_, 10, 5, 3, 4, 1]

        10
       /  \
      5    3
     / \
    4   1
Max-heap property restored ✓
```

### Min-Heapify — Same but flip comparisons

```
MIN_HEAPIFY(A, n, i):
    smallest = i
    left = 2 * i
    right = 2 * i + 1
    
    if left <= n AND A[left] < A[smallest]:     ← LESS THAN (flipped)
        smallest = left
    if right <= n AND A[right] < A[smallest]:
        smallest = right
    
    if smallest != i:
        swap(A[i], A[smallest])
        MIN_HEAPIFY(A, n, smallest)
```

---
---

# 18. HEAP SORT — Complete Worked Trace

> **Idea:** Build a max-heap, then repeatedly extract the maximum (swap root with last, shrink heap, heapify).
> After all extractions, array is sorted in **ascending** order.

### Algorithm

```
HEAP_SORT(A, n):
    // Phase 1: BUILD MAX-HEAP — O(n)
    for i = n/2 down to 1:
        MAX_HEAPIFY(A, n, i)
    
    // Phase 2: EXTRACT MAX REPEATEDLY — O(n log n)
    for i = n down to 2:
        swap(A[1], A[i])             ← move current max to sorted position
        MAX_HEAPIFY(A, i-1, 1)       ← fix heap (size reduced by 1)
```

### Complete Trace: Sort [4, 10, 3, 5, 1]

```
Input: A = [_, 4, 10, 3, 5, 1]   (1-indexed, n=5)

═══ PHASE 1: BUILD MAX-HEAP ═══

Heapify from i = 5/2 = 2 down to 1:

Heapify(i=2): A[2]=10, children A[4]=5, A[5]=1. 10 > both → no swap.
Heapify(i=1): A[1]=4, children A[2]=10, A[3]=3. Largest = 10 → swap A[1]↔A[2]
  → [_, 10, 4, 3, 5, 1]
  Continue at i=2: A[2]=4, children A[4]=5, A[5]=1. Largest = 5 → swap A[2]↔A[4]
  → [_, 10, 5, 3, 4, 1]

Max-heap built:
        10
       /  \
      5    3
     / \
    4   1

═══ PHASE 2: EXTRACT MAX ═══

--- Iteration 1 (i=5): swap A[1]↔A[5], heapify size=4 ---
  Swap 10↔1: [_, 1, 5, 3, 4, |10|]        sorted: [10]
  Heapify(1) on [1, 5, 3, 4]:
    1 vs 5, 3 → swap 1↔5 → [5, 1, 3, 4]
    1 vs 4 → swap 1↔4 → [5, 4, 3, 1]
  Heap: [_, 5, 4, 3, 1, |10|]

--- Iteration 2 (i=4): swap A[1]↔A[4], heapify size=3 ---
  Swap 5↔1: [_, 1, 4, 3, |5, 10|]          sorted: [5, 10]
  Heapify(1) on [1, 4, 3]:
    1 vs 4, 3 → swap 1↔4 → [4, 1, 3]
  Heap: [_, 4, 1, 3, |5, 10|]

--- Iteration 3 (i=3): swap A[1]↔A[3], heapify size=2 ---
  Swap 4↔3: [_, 3, 1, |4, 5, 10|]          sorted: [4, 5, 10]
  Heapify(1) on [3, 1]:
    3 vs 1 → 3 > 1 → no swap
  Heap: [_, 3, 1, |4, 5, 10|]

--- Iteration 4 (i=2): swap A[1]↔A[2], heapify size=1 ---
  Swap 3↔1: [_, 1, |3, 4, 5, 10|]          sorted: [3, 4, 5, 10]
  Heapify(1) on [1]: single element → stop
  
FINAL: [_, 1, 3, 4, 5, 10]

SORTED (ascending): 1, 3, 4, 5, 10 ✓
```

### Heap Sort Properties

| Property | Value |
|---|---|
| Time (all cases) | **O(n log n)** |
| Space | **O(1)** — in-place |
| Stable? | **No** — swapping can reorder equal elements |
| When to use | Need guaranteed O(n log n) + in-place + no extra memory |

---
---

# 19. BFS (Breadth-First Search) — Graph Traversal

> **Think of it as:** Exploring a graph LEVEL BY LEVEL, like ripples from a stone dropped in water.
> **Uses:** Queue. Visit all neighbors first, then neighbors' neighbors.
> **Finds:** Shortest path in UNWEIGHTED graphs.

### Algorithm

```
BFS(Graph, startVertex):
    create empty Queue Q
    create visited[] array, all false
    
    visited[start] = true
    enqueue(Q, start)
    
    while Q is not empty:
        u = dequeue(Q)
        PRINT u                          ← process/visit this vertex
        
        for each neighbor v of u:
            if visited[v] == false:
                visited[v] = true        ← mark visited BEFORE enqueuing
                enqueue(Q, v)
```

### Complete Trace — BFS from vertex A

```
Graph (Adjacency List):
  A: [B, C]
  B: [A, D, E]
  C: [A, F]
  D: [B]
  E: [B, F]
  F: [C, E]

  A --- B --- D
  |     |
  C --- F --- E
  (B also connects to E)

BFS from A:

Step | Dequeue | Process Neighbors        | Queue After    | Visited
-----|---------|--------------------------|----------------|--------
  0  | —       | Start: enqueue A         | [A]            | {A}
  1  | A       | Neighbors B,C → enqueue  | [B, C]         | {A,B,C}
  2  | B       | Neighbors D,E → enqueue  | [C, D, E]      | {A,B,C,D,E}
  3  | C       | Neighbor F → enqueue     | [D, E, F]      | {A,B,C,D,E,F}
  4  | D       | Neighbor B → already visited | [E, F]      | {A,B,C,D,E,F}
  5  | E       | Neighbors F → already visited | [F]        | {A,B,C,D,E,F}
  6  | F       | All neighbors visited    | []             | {A,B,C,D,E,F}

BFS ORDER: A → B → C → D → E → F

BFS Tree (shortest path tree):
        A
       / \
      B   C
     / \   \
    D   E   F
```

### BFS from Adjacency Matrix — Trace

```
Adjacency Matrix:
    A  B  C  D  E
A [ 0  1  1  0  0 ]
B [ 1  0  0  1  1 ]
C [ 1  0  0  0  1 ]
D [ 0  1  0  0  0 ]
E [ 0  1  1  0  0 ]

BFS from A (index 0):
  Dequeue A → check row A: B(1)✓, C(1)✓ → enqueue B, C
  Dequeue B → check row B: A(visited), D(1)✓, E(1)✓ → enqueue D, E
  Dequeue C → check row C: A(visited), E(visited) → nothing new
  Dequeue D → check row D: B(visited) → nothing
  Dequeue E → check row E: B(visited), C(visited) → nothing

BFS ORDER: A, B, C, D, E ✓
```

### BFS Key Properties

| Property | Value |
|---|---|
| Data structure | **Queue** |
| Time | **O(V + E)** (visit each vertex + each edge once) |
| Space | **O(V)** (queue + visited array) |
| Shortest path | ✅ Yes — in **unweighted** graphs |
| Complete | ✅ Yes — finds all reachable vertices |
| Use when | Shortest path, level-order, connected components, bipartiteness |

---
---

# 20. DFS (Depth-First Search) — Graph Traversal

> **Think of it as:** Go as DEEP as possible before backtracking. Like exploring a maze — follow one path until you hit a dead end, then go back and try another.
> **Uses:** Stack (or recursion = implicit stack).

### Algorithm (Recursive)

```
DFS(Graph, startVertex):
    create visited[] array, all false
    DFS_VISIT(Graph, startVertex, visited)

DFS_VISIT(Graph, u, visited):
    visited[u] = true
    PRINT u                              ← process/visit this vertex
    
    for each neighbor v of u:
        if visited[v] == false:
            DFS_VISIT(Graph, v, visited)  ← recurse (go deeper)
```

### Algorithm (Iterative with Stack)

```
DFS_ITERATIVE(Graph, start):
    create empty Stack S
    create visited[] array, all false
    
    push(S, start)
    
    while S is not empty:
        u = pop(S)
        if visited[u] == false:
            visited[u] = true
            PRINT u                      ← process/visit
            
            for each neighbor v of u (in REVERSE order for same output as recursive):
                if visited[v] == false:
                    push(S, v)
```

### Complete Trace — DFS from vertex A (Recursive)

```
Graph (same as BFS example):
  A: [B, C]
  B: [A, D, E]
  C: [A, F]
  D: [B]
  E: [B, F]
  F: [C, E]

DFS from A (alphabetical adjacency order):

Call Stack Trace:
DFS_VISIT(A):
  Visit A → print A. Neighbors: B, C
  │
  ├── DFS_VISIT(B):       ← first unvisited neighbor of A
  │   Visit B → print B. Neighbors: A(visited), D, E
  │   │
  │   ├── DFS_VISIT(D):   ← first unvisited neighbor of B
  │   │   Visit D → print D. Neighbors: B(visited)
  │   │   No unvisited neighbors → BACKTRACK to B
  │   │
  │   └── DFS_VISIT(E):   ← next unvisited neighbor of B
  │       Visit E → print E. Neighbors: B(visited), F
  │       │
  │       └── DFS_VISIT(F):   ← first unvisited neighbor of E
  │           Visit F → print F. Neighbors: C(unvisited!), E(visited)
  │           │
  │           └── DFS_VISIT(C):   ← first unvisited neighbor of F
  │               Visit C → print C. Neighbors: A(visited), F(visited)
  │               No unvisited → BACKTRACK
  │
  All of A's reachable vertices visited → DONE

DFS ORDER: A → B → D → E → F → C

DFS Tree:
  A → B → D
       ↓
       E → F → C
```

### DFS Edge Classification

```
When running DFS on a DIRECTED graph, edges are classified as:

Tree Edge:    u → v where v is discovered for the first time
              (forms the DFS tree)

Back Edge:    u → v where v is an ANCESTOR of u in DFS tree
              ⭐ BACK EDGE = CYCLE EXISTS

Forward Edge: u → v where v is a DESCENDANT of u (but not tree edge)

Cross Edge:   u → v where v is neither ancestor nor descendant
              (different branch of DFS tree)
```

### DFS Cycle Detection

```
WHAT: Check if a directed graph has a cycle.
KEY: A cycle exists if and only if DFS finds a BACK EDGE.

HAS_CYCLE(Graph):
    create color[] array, all WHITE
    // WHITE = unvisited, GRAY = in progress, BLACK = finished
    
    for each vertex u:
        if color[u] == WHITE:
            if DFS_CYCLE(Graph, u, color):
                return TRUE          ← cycle found!
    return FALSE

DFS_CYCLE(Graph, u, color):
    color[u] = GRAY                  ← start processing u
    
    for each neighbor v of u:
        if color[v] == GRAY:
            return TRUE              ← BACK EDGE → CYCLE!
        if color[v] == WHITE:
            if DFS_CYCLE(Graph, v, color):
                return TRUE
    
    color[u] = BLACK                 ← finished processing u
    return FALSE
```

> **GRAY = currently being explored (on the current path). If you reach a GRAY node → you've found a cycle (you went in a circle back to a node on the current path).**

### DFS Topological Sort

```
WHAT: Order vertices so that for every edge u→v, u comes before v.
      Only works on DAGs (Directed Acyclic Graphs — no cycles).

TOPOLOGICAL_SORT(Graph):
    create empty Stack S
    create visited[] array, all false
    
    for each vertex u:
        if visited[u] == false:
            TOPO_DFS(Graph, u, visited, S)
    
    // Pop stack → topological order
    while S is not empty:
        PRINT pop(S)

TOPO_DFS(Graph, u, visited, S):
    visited[u] = true
    for each neighbor v of u:
        if visited[v] == false:
            TOPO_DFS(Graph, v, visited, S)
    push(S, u)                       ← push AFTER all descendants are processed
```

**Example:**
```
Graph: A→B, A→C, B→D, C→D

TOPO_DFS(A):
  TOPO_DFS(B):
    TOPO_DFS(D): push D. Stack: [D]
  push B. Stack: [D, B]
  TOPO_DFS(C):
    D already visited
  push C. Stack: [D, B, C]
push A. Stack: [D, B, C, A]

Pop all: A, C, B, D ← valid topological order
(A before B, A before C, B before D, C before D ✓)
```

### DFS Key Properties

| Property | Value |
|---|---|
| Data structure | **Stack** (or recursion = implicit stack) |
| Time | **O(V + E)** |
| Space | **O(V)** (stack + visited) |
| Shortest path | ❌ No — DFS does NOT guarantee shortest path |
| Cycle detection | ✅ Yes — via back edges (GRAY node detection) |
| Topological sort | ✅ Yes — reverse postorder |
| Use when | Cycle detection, topological sort, path existence, connected components |

### BFS vs DFS — Quick Comparison

| | BFS | DFS |
|---|---|---|
| **Data structure** | Queue | Stack / Recursion |
| **Strategy** | Level by level (breadth) | Go deep, then backtrack (depth) |
| **Visits** | Closest vertices first | Deepest vertices first |
| **Shortest path** | ✅ (unweighted) | ❌ |
| **Cycle detection** | ✅ (via visited check) | ✅ (via back edges — more standard) |
| **Topological sort** | ✅ (Kahn's algorithm) | ✅ (reverse postorder) |
| **Memory** | O(V) — can be large if many neighbors | O(V) — proportional to longest path |
| **Pick when** | Shortest path, level-order | Cycle detection, topological sort, maze solving |

---
---

# 📋 UPDATED MASTER COMPARISON TABLE

| Data Structure | Insert | Delete | Search | Find Min/Max | Special |
|---|---|---|---|---|---|
| **Array** | O(1) end, O(n) position | O(n) | O(n) | O(n) | O(1) access by index |
| **Stack** | O(1) push | O(1) pop | O(n) | — | LIFO |
| **Queue** | O(1) enqueue | O(1) dequeue | O(n) | — | FIFO |
| **Circular Queue** | O(1) | O(1) | O(n) | — | Wraps around |
| **Deque** | O(1) both ends | O(1) both ends | O(n) | — | Both ends |
| **Singly LL** | O(1) head | O(1) head, O(n) any | O(n) | O(n) | Forward only |
| **Doubly LL** | O(1) head | O(1) with pointer | O(n) | O(n) | Both directions |
| **Circular LL** | O(1)* | O(1)* | O(n) | O(n) | No NULL |
| **Circular Doubly LL** | O(1) both ends | O(1) with pointer | O(n) | O(n) | Most flexible LL |
| **BST (balanced)** | O(log n) | O(log n) | **O(log n)** | O(log n) | Sorted order |
| **BST (skewed)** | O(n) | O(n) | O(n) | O(n) | Worst case |
| **Max-Heap** | O(log n) | O(log n) | O(n) | **O(1) max** | Priority queue |
| **Min-Heap** | O(log n) | O(log n) | O(n) | **O(1) min** | Dijkstra's |

*with tail pointer

---

# 🧠 TREE & HEAP MEMORY TRICKS

| Concept | Remember As |
|---|---|
| **Preorder** | "Me first, then my kids" (Root → Left → Right) |
| **Inorder** | "Left friend, then me, then right friend" — gives **SORTED** for BST |
| **Postorder** | "Kids first, then me" (Left → Right → Root) — used to DELETE tree |
| **BST Insert** | Search for the spot, add as a LEAF (never in the middle) |
| **BST Delete 3 cases** | 0 kids = just remove. 1 kid = replace with kid. 2 kids = replace with inorder successor |
| **Inorder successor** | Smallest in right subtree (go right once, then all the way left) |
| **Heap insert** | Add at END, BUBBLE UP (swap with parent while bigger/smaller) |
| **Heap delete max** | Swap root↔last, remove last, BUBBLE DOWN (heapify from root) |
| **Build heap** | Start from last non-leaf, heapify downward. O(n) NOT O(n log n) |
| **Max vs Min heap** | Max = parent ≥ children (root=max). Min = parent ≤ children (root=min). Same algorithms, flip the comparison. |
| **Preorder+Inorder** | Can build unique tree ✅ |
| **Postorder+Inorder** | Can build unique tree ✅ |
| **Preorder+Postorder** | CANNOT build unique tree ❌ |

---

# ✅ EXAM WRITING TIPS

1. **Always start with:** "Check if empty / full" before any operation
2. **Show the update order:** Update pointers in the right order — if you update head.next before saving the old value, you lose the old chain
3. **Draw a picture:** For linked list and tree algorithms, drawing a before/after diagram gets partial marks even if pseudocode is wrong
4. **Mention time complexity:** Write O(1) or O(n) or O(log n) after each algorithm — examiners look for this
5. **Edge cases to mention:** Empty tree, single node, leaf node, node with one child, node with two children
6. **BST Delete:** Always mention all 3 cases explicitly — examiners check for completeness
7. **Heap:** Always mention "complete binary tree" — heap is ALWAYS complete, that's why array works
8. **Traversal questions:** Write the recursive version (3 lines) — it's the simplest and always correct

---

*Good luck with your exam!* 🎯
