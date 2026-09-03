# DSA Exam-Style Questions — BITS Pilani Pattern

> **Based on:** BITS Pilani CS F211 (Data Structures & Algorithms) exam papers from 2019-20 and 2022-23 semesters, including mid-semester and comprehensive examinations.
>
> **Exam Pattern Observed:**
> - **Mid-Semester:** 6 questions, 90 minutes, 90 marks (30% weightage). Closed book.
> - **Comprehensive:** 6-8 questions, 3 hours, ~90 marks (45% weightage). Closed book.
> - Heavy emphasis on: Heap operations, Quick Sort variants, recurrence solving, hashing, algorithm correctness proofs, True/False with justification, C-code completion.
>
> **Source references:** [BITS Pilani Library](https://library.bits-pilani.ac.in), [Studocu BITS CS F211](https://www.studocu.com/in/document/birla-institute-of-technology-and-science-pilani/data-structures-and-algorithms/data-structures-and-algorithms-midsemester-paper/10459314). Content was rephrased for compliance with licensing restrictions.

---
---

## SECTION A — Heap Operations (15-20 marks)

*Heaps are the single most tested topic in BITS DSA exams. Expect questions on DELETE, correctness proofs, and algorithm completion.*

---

### A1. MAX-HEAP DELETE — Correctness Analysis [15 marks]

Let A[1..n] be an n-element MAX-Heap. You have implemented the following five standard operations:
1. MAX-HEAPIFY(A, i)
2. BUILD-MAX-HEAP(A)
3. HEAP-EXTRACT-MAX(A)
4. HEAP-INCREASE-KEY(A, i, key)
5. MAX-HEAP-INSERT(A, key)

Now consider DELETE(A, i) which removes the i-th element from the max-heap.

**Part (a) [5 marks]:** The following implementation is proposed. Is it correct? Prove your answer or provide a counter-example.

```
DELETE_v1(A, i):
    A[i] = A[A.heap-size]
    A.heap-size = A.heap-size - 1
    MAX-HEAPIFY(A, i)
```

**Answer:**

**This implementation is INCORRECT.**

**Counter-example:**
```
Original max-heap:
        16
       /  \
      14    10
     / \   /
    8   7  9

Array: [16, 14, 10, 8, 7, 9]
```

Delete i = 5 (value 7):
1. A[5] = A[6] = 9. Array: [16, 14, 10, 8, 9, _]. heap-size = 5.
2. MAX-HEAPIFY(A, 5): Node 5 has no children (it's a leaf now). No swap needed.
3. Result: [16, 14, 10, 8, 9]

But 9 > 8 (its parent at index 2→4), and the heap property requires parent ≥ child. Wait — parent of index 5 is index 2 (14). 14 > 9, so that's fine. Let's pick a different example.

```
        16
       /  \
      4    10
     / \   /
    1   3  9

Array: [16, 4, 10, 1, 3, 9]
```

Delete i = 4 (value 1):
1. A[4] = A[6] = 9. Array: [16, 4, 10, 9, 3, _]. heap-size = 5.
2. MAX-HEAPIFY(A, 4): Node 4 (value 9) has child at index 8,9 — no children (leaf). No swap.
3. Result: [16, 4, 10, 9, 3]

**Problem:** Node 4 (value 9) > Node 2 (value 4, its parent). Heap property violated! MAX-HEAPIFY only sifts DOWN but the replaced value (9) needs to go UP.

---

**Part (b) [5 marks]:** Complete the following correct implementation using exactly two of the five operations listed above (with correct arguments):

```
DELETE_v2(A, i):
    /* Write two operations here */
```

**Answer:**

```
DELETE_v2(A, i):
    HEAP-INCREASE-KEY(A, i, +∞)    // Bubble element to root
    HEAP-EXTRACT-MAX(A)             // Remove root
```

This always works: increasing the key to +∞ moves it to the root via sift-up, then EXTRACT-MAX removes the root and restores the heap.

---

**Part (c) [5 marks]:** Complete the following implementation by writing one statement each for S1, S2, and S3:

```
DELETE_v3(A, i):
    A[i] = A[A.heap-size]
    A.heap-size = A.heap-size - 1
    if (i > 1 and S1)
        then S2
    else S3
```

**Answer:**

- **S1:** `A[i] > A[PARENT(i)]`
- **S2:** `HEAP-INCREASE-KEY(A, i, A[i])`
- **S3:** `MAX-HEAPIFY(A, i)`

Logic: After replacing A[i] with the last element, the new value might be larger than its parent (needs sift-up via HEAP-INCREASE-KEY) or smaller than a child (needs sift-down via MAX-HEAPIFY).

---

### A2. BUILD-MAX-HEAP Analysis [8 marks]

**Part (a) [4 marks]:** The naive analysis says BUILD-MAX-HEAP calls MAX-HEAPIFY n/2 times, each O(log n), giving O(n log n). Prove the tighter O(n) bound.

**Answer:**

The key insight: MAX-HEAPIFY at height h takes O(h) time, and most nodes are at small heights.

- Number of nodes at height h in a complete binary tree: ≤ ⌈n / 2^(h+1)⌉
- Total work = Σ (h=0 to ⌊log n⌋) ⌈n/2^(h+1)⌉ · O(h)
- = O(n · Σ (h=0 to ∞) h/2^h)
- The series Σ h/2^h = 2 (converges).
- **Total = O(n · 2) = O(n).**

**Part (b) [4 marks]:** Would building a heap by calling MAX-HEAP-INSERT n times also be O(n)? Justify.

**Answer:**

**No.** MAX-HEAP-INSERT inserts at the end and sifts UP. Each insertion takes O(log k) where k is the current heap size.

Total = Σ (k=1 to n) log k = log(n!) = **Θ(n log n)**.

The top-down approach (repeated inserts) is Θ(n log n). The bottom-up approach (BUILD-MAX-HEAP) is O(n). They are not equivalent.

---

### A3. Min-Heap for k-th Smallest [5 marks]

**Question:** You have 1 million distinct integers in a file and want to find the smallest 15 integers. Compare using: (a) sorted array, (b) max-heap of size 15, (c) min-heap of all elements. Which is most efficient?

**Answer:**

| Approach | Time | Space |
|---|---|---|
| (a) Sort entire array | O(n log n) ≈ 20 million ops | O(1) in-place |
| (b) Max-heap of size 15 | O(n log 15) ≈ 4 million ops | O(15) = O(1) |
| (c) Min-heap of all n | Build: O(n), Extract 15 mins: O(15 log n) ≈ n + 300 | O(n) |

**Most efficient: (b) Max-heap of size 15.**

Algorithm: Scan through all elements. Maintain a max-heap of the 15 smallest seen so far. For each new element: if it's smaller than the heap's max, replace the max and heapify. Uses O(1) extra space and O(n) time with a very small constant.

Option (c) is theoretically O(n) too but uses O(n) space for the heap. Option (b) is preferred for its O(1) space.

---
---

## SECTION B — Sorting: Quick Sort & Analysis (15-20 marks)

*Quick Sort is the second most tested topic. Expect code completion, worst-case analysis, and stack depth optimisation.*

---

### B1. PARTITION Code Completion [6 marks]

**Question:** Complete Line-1, Line-2, and Line-3 in the following Quick Sort implementation:

```
QUICKSORT(A, p, r):
    while (p < r):
        q = PARTITION(A, p, r)
        QUICKSORT(A, p, q-1)
        // Line-1

PARTITION(A, p, r):
    x = A[r]
    i = p - 1
    for j = p to r-1:
        if A[j] <= x:
            i = i + 1
            exchange A[i] with A[j]
    // Line-2
    // Line-3
```

**Answer:**

- **Line-1:** `p = q + 1`
  (This is tail-call optimisation — instead of a second recursive call, update p and let the while loop handle the right partition.)

- **Line-2:** `exchange A[i+1] with A[r]`
  (Place the pivot in its correct position.)

- **Line-3:** `return i + 1`
  (Return the pivot's final index.)

---

### B2. Quick Sort Stack Depth Analysis [7 marks]

**Part (a) [3 marks]:** Describe a scenario where the given QUICKSORT's stack depth is O(n).

**Answer:**

**Scenario:** The input array is already sorted in ascending order, and the pivot is always the last element.

- PARTITION always produces partitions of size (n-1, 0).
- The recursive call QUICKSORT(A, p, q-1) processes n-1 elements.
- The while loop sets p = q+1 = r, so the loop terminates immediately after.
- Recurrence: T(n) = T(n-1) + O(n).
- **Stack depth = O(n)** because each recursive call reduces the problem by only 1.

**Part (b) [4 marks]:** Modify the code so worst-case stack depth is O(log n) while maintaining O(n log n) expected time.

**Answer:**

Always recurse on the **smaller** partition. Handle the larger partition via the while loop:

```
QUICKSORT(A, p, r):
    while (p < r):
        q = PARTITION(A, p, r)
        if (q - p) < (r - q):          // Left partition is smaller
            QUICKSORT(A, p, q - 1)      // Recurse on smaller
            p = q + 1                    // Loop handles larger
        else:                            // Right partition is smaller
            QUICKSORT(A, q + 1, r)       // Recurse on smaller
            r = q - 1                    // Loop handles larger
```

The recursive call is always on a partition of size ≤ n/2. Stack depth = **O(log n)**.

Expected running time is unchanged because PARTITION still runs in O(n) and the total work across all levels is O(n log n).

---

### B3. Quick Sort Recurrence with Unbalanced Partitions [7 marks]

**Question:** Suppose PARTITION always produces a split where one side has ⌊n/10⌋ elements and the other has ⌈9n/10⌉ elements. Prove the running time is still O(n log n).

**Answer:**

**Recurrence:** T(n) = T(n/10) + T(9n/10) + cn

**Recursion tree analysis:**
- At each level, total work = cn (sum of all sub-problem costs).
- Shortest path (following n/10): depth = log₁₀ n.
- Longest path (following 9n/10): depth = log₁₀/₉ n.

Both are O(log n). Total work = cn × O(log n) = **O(n log n)**.

**Key insight:** Any constant-fraction split (not just 50-50) yields O(n log n). Only a 0 vs n-1 split (or close to it) causes O(n²). This is why randomised Quick Sort with expected constant-fraction splits is O(n log n) in expectation.

---
---

## SECTION C — Recurrences & Complexity Analysis (15 marks)

---

### C1. Recursion Tree for General Recurrence [8 marks]

**Question:** For the recurrence T(N) = T(αN) + T((1-α)N) + cN, where 0 < α < 1 and c > 0:

(a) Draw the recursion tree (at least 3 levels), showing sub-problem sizes.
(b) Derive the height of the tree for cases α > (1-α) and α ≤ (1-α).
(c) Derive the time complexity for both cases.

**Answer:**

**(a) Recursion tree:**

```
Level 0:                    cN                          Total: cN
                           / \
Level 1:            cαN        c(1-α)N                  Total: cN
                   / \          / \
Level 2:    cα²N  cα(1-α)N  c(1-α)αN  c(1-α)²N        Total: cN
```

Every level sums to **cN**.

**(b) Height of tree:**

The tree is not balanced — one side shrinks faster than the other.

- **Shortest path** (always follow the smaller fraction): Let β = min(α, 1-α).
  - Sub-problem size at depth d: βᵈ · N. Reaches base case when βᵈ · N = 1 → d = log_{1/β} N.
  - **Shortest height = log_{1/min(α, 1-α)} N.**

- **Longest path** (always follow the larger fraction): Let γ = max(α, 1-α).
  - **Longest height = log_{1/max(α, 1-α)} N.**

**(c) Time complexity:**

Since each level costs cN and there are between log_{1/β} N and log_{1/γ} N levels, and both are Θ(log N) for any constant 0 < α < 1:

**T(N) = Θ(N log N).**

---

### C2. Master Theorem Applications [7 marks]

Solve each recurrence. State which case of the Master Theorem applies.

**(a)** T(n) = 9T(n/3) + n

**Answer:**
- a=9, b=3, c_crit = log₃ 9 = 2.
- f(n) = n = O(n^(2-1)) → **Case 1.**
- **T(n) = Θ(n²).**

**(b)** T(n) = T(2n/3) + 1

**Answer:**
- a=1, b=3/2, c_crit = log_{3/2} 1 = 0.
- f(n) = 1 = Θ(n⁰) → **Case 2** with k=0.
- **T(n) = Θ(log n).**

**(c)** T(n) = 3T(n/4) + n log n

**Answer:**
- a=3, b=4, c_crit = log₄ 3 ≈ 0.793.
- f(n) = n log n = Ω(n^(0.793 + ε)) for ε ≈ 0.2 → Check regularity: 3 · (n/4) log(n/4) ≤ c · n log n for c = 3/4 < 1. ✓
- **Case 3.** T(n) = **Θ(n log n).**

**(d)** T(n) = 2T(n/2) + n log n

**Answer:**
- a=2, b=2, c_crit = log₂ 2 = 1.
- f(n) = n log n = Θ(n¹ · log¹ n) → **Case 2** with k=1.
- **T(n) = Θ(n log² n).**

---
---

## SECTION D — Hashing (10-15 marks)

---

### D1. Open Addressing: Quadratic Probing & Double Hashing [10 marks]

**Question:** Insert the keys 10, 22, 31, 4, 15, 28, 17, 88, 59 into a hash table of size m = 11 with h'(k) = k mod 11.

**(a) [5 marks]** Draw the hash table using **quadratic probing** with c₁ = 1, c₂ = 3.

h(k, i) = (h'(k) + c₁·i + c₂·i²) mod 11

**(b) [5 marks]** Draw the hash table using **double hashing** with h₁(k) = k mod 11, h₂(k) = 1 + (k mod 10).

h(k, i) = (h₁(k) + i · h₂(k)) mod 11

**Answer (a) — Quadratic Probing:**

| Key | h'(k) | i=0 | i=1 (h'+1+3=h'+4) | i=2 (h'+2+12=h'+14) | Placed at |
|---|---|---|---|---|---|
| 10 | 10 | 10 ✓ | | | **10** |
| 22 | 0 | 0 ✓ | | | **0** |
| 31 | 9 | 9 ✓ | | | **9** |
| 4 | 4 | 4 ✓ | | | **4** |
| 15 | 4 | 4 ✗ | 8 ✓ | | **8** |
| 28 | 6 | 6 ✓ | | | **6** |
| 17 | 6 | 6 ✗ | 10 ✗ | (6+14)%11 = 20%11 = 9 ✗ → i=3: (6+3+27)%11=36%11=3 ✓ | **3** |
| 88 | 0 | 0 ✗ | 4 ✗ | 14%11=3 ✗ → i=3: (0+3+27)%11=30%11=8 ✗ → i=4: (0+4+48)%11=52%11=8 ✗ → i=5: (0+5+75)%11=80%11=3 ✗ → i=6: (0+6+108)%11=114%11=4 ✗ → i=7: (0+7+147)%11=154%11=0 ✗ ... | Continue probing until slot 1 or other free slot. Eventually: **1** |
| 59 | 4 | 4 ✗ | 8 ✗ | (4+14)%11=7 ✓ | **7** |

```
Index:  0    1    2    3    4    5    6    7    8    9    10
Value: [22] [88] [ ] [17] [4]  [ ] [28] [59] [15] [31] [10]
```

**Answer (b) — Double Hashing:**

| Key | h₁ | h₂ | Probe sequence | Placed at |
|---|---|---|---|---|
| 10 | 10 | 1 | 10 | **10** |
| 22 | 0 | 3 | 0 | **0** |
| 31 | 9 | 2 | 9 | **9** |
| 4 | 4 | 5 | 4 | **4** |
| 15 | 4 | 6 | 4→10→5 | **5** |
| 28 | 6 | 9 | 6 | **6** |
| 17 | 6 | 8 | 6→3 | **3** |
| 88 | 0 | 9 | 0→9→7 | **7** |
| 59 | 4 | 10 | 4→3→2 | **2** |

```
Index:  0    1    2    3    4    5    6    7    8    9    10
Value: [22] [ ] [59] [17] [4] [15] [28] [88] [ ] [31] [10]
```

---
---

## SECTION E — True/False with Justification (10-15 marks)

*This is a signature BITS exam section. Each question typically carries 3-5 marks. Full marks only with correct justification.*

---

### E1. [3 marks] There exists a comparison-based sorting algorithm with worst-case time O(n log n) that is also stable and in-place.

**Answer: False.**

- Merge Sort: O(n log n), stable, but NOT in-place (requires O(n) space).
- Heap Sort: O(n log n), in-place, but NOT stable.
- No known comparison-based sort achieves all three simultaneously. (Block merge sort comes close with O(1) extra space but has a higher constant factor.)

---

### E2. [3 marks] If a graph has V vertices and E edges, DFS takes O(V + E) time while BFS takes O(V²) time.

**Answer: False.**

Both BFS and DFS take **O(V + E)** time when using an adjacency list. Only if using an adjacency matrix does iterating over neighbours take O(V) per vertex, giving O(V²) total. The representation matters, not the algorithm.

---

### E3. [3 marks] The number of comparisons needed to find both the maximum and minimum of n elements is 3⌊n/2⌋ - 2.

**Answer: True.**

Compare elements in pairs: each pair takes 3 comparisons (1 to compare with each other, then compare smaller with current min and larger with current max). For n elements: ⌊n/2⌋ pairs × 3 comparisons - 2 (first pair only needs 1 comparison). Total = **3⌊n/2⌋ - 2.** This is optimal.

---

### E4. [3 marks] In a Red-Black tree with n internal nodes, the longest root-to-leaf path is at most twice the shortest.

**Answer: True.**

Property 5 of Red-Black trees states all root-to-leaf paths have the same number of black nodes (black-height). Property 4 says no two consecutive red nodes. Therefore:
- Shortest path: all black = black-height (bh).
- Longest path: alternating red-black = 2 × bh.
- Longest ≤ 2 × shortest. ✓

---

### E5. [3 marks] Quick Sort with the median-of-three pivot selection strategy has O(n log n) worst-case time.

**Answer: False.**

Median-of-three (pivot = median of first, middle, last elements) **improves** the average case significantly and avoids the worst case for sorted/reverse-sorted inputs. However, adversarial inputs can still produce O(n²) worst case. Only **median-of-medians** (deterministic linear-time selection) guarantees O(n log n) worst case (but with a large constant, so it's rarely used in practice).

---

### E6. [3 marks] A binary heap can be used to implement a priority queue where both INSERT and EXTRACT-MIN run in O(1) amortised time.

**Answer: False.**

In any comparison-based priority queue, either INSERT or EXTRACT-MIN must take Ω(log n) amortised time. This follows from the sorting lower bound: if both were O(1), we could sort n elements in O(n) by inserting all then extracting all — contradicting the Ω(n log n) lower bound.

A Fibonacci heap achieves O(1) amortised INSERT and O(log n) amortised EXTRACT-MIN, which is optimal.

---
---

## SECTION F — Algorithm Design & Coding (15-25 marks)

---

### F1. Stack Cloning Without Extra Space [15 marks]

**Question:** Write a function `Transfer(stack, stack2)` that copies the contents of `stack` to `stack2`, maintaining the same order, **without using extra space** proportional to the stack size. Only push() and pop() operations are available.

Example: If stack = [2, 5, 3, 4, 8] (top = 8), then after Transfer, stack2 = [2, 5, 3, 4, 8].

Complete statements S1 through S7:

```c
int isEmpty(struct Stack* stack) {
    return S1;
}

void push(struct Stack* stack, int item) {
    if (isFull(stack)) return;
    S2;
}

int pop(struct Stack* stack) {
    if (isEmpty(stack)) return INT_MIN;
    S3;
}

void Transfer(struct Stack* stack, struct Stack* stack2) {
    int temp, i;
    int size = stack->top;
    while (size >= 0) {
        for (i = size; i > 0; i--)
            S4;                    // push-pop operation
        temp = pop(stack);
        S5;                        // push-pop operation
        for (i = size; i > 0; i--)
            S6;                    // push-pop operation
        S7;                        // push-pop operation
        size--;
    }
}
```

**Answer:**

- **S1:** `stack->top == -1`
- **S2:** `stack->array[++stack->top] = item;` (or two statements: `stack->top++; stack->array[stack->top] = item;`)
- **S3:** `return stack->array[stack->top--];` (or two statements: `int val = stack->array[stack->top]; stack->top--; return val;`)
- **S4:** `push(stack2, pop(stack));` — Move top `size` elements from stack to stack2 (temporarily).
- **S5:** `push(stack2, temp);` — Push the bottom element of this batch onto stack2.
- **S6:** `push(stack, pop(stack2));` — Move the `size` elements back from stack2 to stack.
- **S7:** *(no operation needed — or `size--` is already in the while loop update)*

**How it works:** For each layer of the stack (from bottom to top), move all elements above it to stack2, extract the target element, place it in stack2, move the above elements back. This is O(n²) time but O(1) extra space.

---

### F2. Modify PARTITION for Three-Way Partitioning [10 marks]

**Question:** The standard PARTITION produces two partitions (≤ pivot and > pivot). Modify it to produce three partitions: (< pivot), (= pivot), (> pivot). This is useful when the array has many duplicate keys (Dutch National Flag problem).

Give pseudocode and analyse the time complexity.

**Answer:**

```
THREE-WAY-PARTITION(A, p, r):
    pivot = A[r]
    lt = p        // A[p..lt-1] contains elements < pivot
    eq = p        // A[lt..eq-1] contains elements = pivot  
    gt = r        // A[gt+1..r] contains elements > pivot
    
    while eq <= gt:
        if A[eq] < pivot:
            swap(A[lt], A[eq])
            lt++; eq++
        else if A[eq] == pivot:
            eq++
        else:  // A[eq] > pivot
            swap(A[eq], A[gt])
            gt--
    
    return (lt, gt)  // lt = start of pivot region, gt = end of pivot region
```

**Quick Sort using three-way partition:**
```
QUICKSORT_3WAY(A, p, r):
    if p < r:
        (lt, gt) = THREE-WAY-PARTITION(A, p, r)
        QUICKSORT_3WAY(A, p, lt - 1)   // Sort elements < pivot
        QUICKSORT_3WAY(A, gt + 1, r)   // Sort elements > pivot
        // Elements = pivot are already in place
```

**Time:** O(n) per partition call. Overall O(n log n) average. For arrays with many duplicates, significantly better than standard Quick Sort (which degrades to O(n²) on all-equal inputs).

---
---

## SECTION G — Graph & DP Questions (15-20 marks)

---

### G1. DFS-Based Cycle Detection [5 marks]

**Question:** Write pseudocode for detecting whether a directed graph has a cycle using DFS. Explain the edge classification that indicates a cycle.

**Answer:**

```
HAS_CYCLE(G):
    for each vertex u in G:
        colour[u] = WHITE
    for each vertex u in G:
        if colour[u] == WHITE:
            if DFS_VISIT(G, u) == True:
                return True
    return False

DFS_VISIT(G, u):
    colour[u] = GRAY
    for each v adjacent to u:
        if colour[v] == GRAY:     // Back edge found → CYCLE
            return True
        if colour[v] == WHITE:
            if DFS_VISIT(G, v) == True:
                return True
    colour[u] = BLACK
    return False
```

**A back edge** (u → v where v is GRAY, meaning v is an ancestor on the current DFS path) indicates a cycle. This is because v was discovered before u and hasn't finished yet — there's a path from v to u in the DFS tree, and the edge u→v closes the cycle.

---

### G2. DP — Optimal BST / Matrix Chain Style [10 marks]

**Question:** Given matrices A₁(10×30), A₂(30×5), A₃(5×60), find the optimal parenthesisation that minimises the number of scalar multiplications.

**Answer:**

**Dimensions:** p = [10, 30, 5, 60]

**DP table m[i][j] = minimum multiplications for A_i...A_j:**

| | 1 | 2 | 3 |
|---|---|---|---|
| 1 | 0 | 1500 | **4500** |
| 2 | — | 0 | 9000 |
| 3 | — | — | 0 |

**Calculations:**

- m[1][1] = m[2][2] = m[3][3] = 0
- m[1][2] = p₀ × p₁ × p₂ = 10 × 30 × 5 = **1500**
- m[2][3] = p₁ × p₂ × p₃ = 30 × 5 × 60 = **9000**
- m[1][3] = min(
    m[1][1] + m[2][3] + 10×30×60 = 0 + 9000 + 18000 = 27000,
    m[1][2] + m[3][3] + 10×5×60 = 1500 + 0 + 3000 = **4500**
  ) = **4500** at k=2.

**Optimal parenthesisation: (A₁ × A₂) × A₃**

Multiply A₁(10×30) × A₂(30×5) first → result is 10×5, costs 1500.
Then multiply (10×5) × A₃(5×60) → result is 10×60, costs 3000.
Total: 4500 scalar multiplications.

---

### G3. Shortest Path with Negative Weights [5 marks]

**Question:** Run one iteration of Bellman-Ford on the following graph from source S:

```
S →(6)→ A →(-2)→ C
S →(7)→ B →(3)→ C
A →(5)→ B
B →(-4)→ A
```

Show dist[] after each pass (V-1 = 3 passes over all edges).

**Answer:**

Edges: (S,A,6), (S,B,7), (A,C,-2), (B,C,3), (A,B,5), (B,A,-4)

| Pass | dist[S] | dist[A] | dist[B] | dist[C] |
|---|---|---|---|---|
| Init | 0 | ∞ | ∞ | ∞ |
| Pass 1 | 0 | 6 | 7 | 4 |
| Pass 2 | 0 | 3 | 7 | 1 |
| Pass 3 | 0 | 3 | 7 | 1 |

**Pass 1 detail:**
- (S,A,6): dist[A] = min(∞, 0+6) = 6
- (S,B,7): dist[B] = min(∞, 0+7) = 7
- (A,C,-2): dist[C] = min(∞, 6+(-2)) = 4
- (B,C,3): dist[C] = min(4, 7+3) = 4 (no change)
- (A,B,5): dist[B] = min(7, 6+5) = 7 (no change)
- (B,A,-4): dist[A] = min(6, 7+(-4)) = 3

**Pass 2:** dist[A] updated to 3 (from B→A). dist[C] updated to 1 (from A→C: 3+(-2)=1).

**Pass 3:** No changes → converged. No negative cycle.

---
---

## SECTION H — Comprehensive Exam Rapid-Fire (1-2 marks each)

---

### H1. What is the time complexity of finding the median of an unsorted array?

**Answer:** O(n) using the **Median of Medians** (deterministic selection) algorithm, or O(n) expected using Randomised Quickselect.

---

### H2. What is the maximum number of nodes in a binary tree of height h?

**Answer:** 2^(h+1) - 1. (A perfect binary tree.)

---

### H3. In a max-heap stored as an array (1-indexed), what are the parent and children of node at index i?

**Answer:** Parent = ⌊i/2⌋. Left child = 2i. Right child = 2i + 1.

---

### H4. What is the time complexity of DFS on a graph with V vertices and E edges?

**Answer:** O(V + E) with adjacency list. O(V²) with adjacency matrix.

---

### H5. Name the algorithm that solves the all-pairs shortest path problem in O(V³).

**Answer:** **Floyd-Warshall** algorithm.

---

### H6. What is the difference between a stable and unstable sort?

**Answer:** A **stable** sort preserves the relative order of elements with equal keys. An **unstable** sort does not guarantee this.

---

### H7. What recurrence does Merge Sort satisfy? What is its solution?

**Answer:** T(n) = 2T(n/2) + O(n). Solution: **Θ(n log n)** by Master Theorem (Case 2, k=0).

---

### H8. In a hash table with open addressing and load factor α, what is the expected number of probes for an unsuccessful search?

**Answer:** 1/(1-α) for uniform hashing. Example: α = 0.5 → 2 probes; α = 0.75 → 4 probes; α = 0.9 → 10 probes.

---

### H9. What is the optimal time complexity for the matrix chain multiplication problem?

**Answer:** **O(n³)** time, **O(n²)** space using dynamic programming, where n is the number of matrices.

---

### H10. Can Dijkstra's algorithm detect negative-weight cycles?

**Answer:** **No.** Dijkstra assumes non-negative weights and does not process vertices more than once. Use **Bellman-Ford** for negative-weight cycle detection.

---
---

## Exam Preparation Tips (BITS Pilani CS F211 Pattern)

1. **Heap operations are critical** — Know DELETE, BUILD, EXTRACT, INCREASE-KEY inside-out. Be ready to prove correctness or give counter-examples.
2. **Quick Sort** — Expect code completion, worst-case stack depth analysis, and tail-call optimisation every exam.
3. **Recurrences** — Master Theorem, recursion tree, and substitution method. At least 2 recurrence-solving questions per paper.
4. **Hashing** — Practice double hashing and quadratic probing by hand with specific c₁, c₂ values.
5. **True/False with justification** — These are free marks if you know the concepts deeply. Partial credit for reasoning even if the answer is wrong.
6. **C-code completion** — Not pseudocode. Know pointer syntax, stack struct operations, edge cases (empty, full).
7. **Time complexity proofs** — Not just stating O(n log n), but proving it with recurrences or summations.
8. **Graph algorithms** — DFS edge classification, topological sort, and shortest path algorithms are regularly tested.

---

*End of Exam-Style Questions*
