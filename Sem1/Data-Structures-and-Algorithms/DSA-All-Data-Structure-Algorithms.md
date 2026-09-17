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

### Access by Index — O(1)

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

### Pop (Remove from top) — O(1)

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

### Peek (Look at top without removing) — O(1)

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

### Dequeue (Remove from front) — O(1)

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

**Problem with linear queue:** When you dequeue, front moves right. Slots before front are WASTED. Even if there's space at the beginning, rear says "full". This is **phantom overflow** → solved by circular queue.

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

### Dequeue — O(1)

```
WHAT: Remove element from front, wrapping around if needed
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

### How wrapping works — Example

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

### Insert at Rear — O(1)

```
INSERT_REAR(D, value):
    rear = (rear + 1) % capacity                ← move rear FORWARD (with wrap)
    D[rear] = value
    count = count + 1
```

### Delete from Front — O(1)

```
DELETE_FRONT(D):
    value = D[front]
    front = (front + 1) % capacity              ← move front FORWARD
    count = count - 1
    return value
```

### Delete from Rear — O(1)

```
DELETE_REAR(D):
    value = D[rear]
    rear = (rear - 1 + capacity) % capacity     ← move rear BACKWARDS (with wrap)
    count = count - 1
    return value
```

**Two restricted types:**
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

### Using Sorted Array

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

**Visual:**
```
Before: HEAD → [10] → [20] → [30] → NULL
Insert 5:
  newNode [5].next = HEAD (which is [10])
  HEAD = newNode
After:  HEAD → [5] → [10] → [20] → [30] → NULL
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

### Delete at Position i — O(n)

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

### Reverse — O(n)

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

**Visual:**
```
Before: NULL ← [10] ⇄ [20] ⇄ [30] → NULL
Insert 5:
After:  NULL ← [5] ⇄ [10] ⇄ [20] ⇄ [30] → NULL
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

**Visual (delete [20]):**
```
Before: NULL ← [10] ⇄ [20] ⇄ [30] → NULL
                        ↑ delete this

  [10].next = [20].next = [30]         ← skip forward
  [30].prev = [20].prev = [10]         ← skip backward

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

### Delete Head — O(1)

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

# ✅ EXAM WRITING TIPS

1. **Always start with:** "Check if empty / full" before any operation
2. **Show the update order:** Update pointers in the right order — if you update head.next before saving the old value, you lose the old chain
3. **Draw a picture:** For linked list algorithms, drawing a before/after diagram gets partial marks even if pseudocode is wrong
4. **Mention time complexity:** Write O(1) or O(n) after each algorithm — examiners look for this
5. **Edge cases to mention:** Empty list, single element, insert/delete at head, insert/delete at tail

---

*Good luck with your exam!* 🎯
