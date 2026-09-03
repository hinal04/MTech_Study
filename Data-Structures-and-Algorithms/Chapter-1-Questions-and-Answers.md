# Chapter 1: Analyzing Algorithms — Questions & Answers

> BITS Pilani — DSA Exam Preparation
> References: T1 (Goodrich & Tamassia), R1 (CLRS 4.3, 4.4, 4.5)

---

## Table of Contents

- [Section A: Conceptual / Theory Questions](#section-a-conceptual--theory-questions)
- [Section B: Counting Primitive Operations](#section-b-counting-primitive-operations)
- [Section C: Asymptotic Notation — Proofs & Comparisons](#section-c-asymptotic-notation--proofs--comparisons)
- [Section D: Analyzing Iterative Algorithms](#section-d-analyzing-iterative-algorithms)
- [Section E: Loop Invariants & Correctness Proofs](#section-e-loop-invariants--correctness-proofs)
- [Section F: Writing Recurrence Relations](#section-f-writing-recurrence-relations)
- [Section G: Master Theorem](#section-g-master-theorem)
- [Section H: Recursion Tree Method](#section-h-recursion-tree-method)
- [Section I: Substitution Method](#section-i-substitution-method)
- [Section J: Mixed / Exam-Style Problems](#section-j-mixed--exam-style-problems)

---

# Section A: Conceptual / Theory Questions

### Q1. What is an algorithm? State its five essential properties.

**Answer:**

An algorithm is a well-defined, finite sequence of computational steps that takes some input and produces an output.

Five essential properties:

1. **Input** — Zero or more quantities are externally supplied
2. **Output** — At least one quantity is produced
3. **Definiteness** — Each instruction is clear and unambiguous
4. **Finiteness** — The algorithm terminates after a finite number of steps
5. **Effectiveness** — Every instruction is basic enough to be carried out in practice (using pencil and paper, in principle)

---

### Q2. What are the limitations of experimental analysis of algorithms? Why do we prefer theoretical analysis?

**Answer:**

**Limitations of experimental analysis:**
1. The algorithm must be **fully implemented** before it can be studied, which can be time-consuming
2. Results are only valid for the **specific inputs tested** — other inputs may behave differently
3. Comparisons between algorithms require the **same hardware and software environment**
4. Results are **not portable** across different machines

**Why theoretical analysis is preferred:**
1. Takes into account **all possible inputs**, not just a sample
2. Evaluates speed **independent of hardware/software** environment
3. Can study an algorithm at the **pseudocode level** without implementing it
4. Characterizes running time as a **function of input size n**, making algorithms universally comparable

---

### Q3. Explain the Random Access Machine (RAM) model. What are its key assumptions?

**Answer:**

The RAM model is a theoretical model of computation used for algorithm analysis. It provides a machine-independent framework.

**Key assumptions:**
1. **Single processor** — instructions execute sequentially, one at a time
2. **Constant-time operations** — each simple operation (arithmetic, comparison, assignment, array indexing, control flow) takes exactly 1 time unit
3. **Flat memory** — unlimited memory with constant-time access to any cell; no distinction between cache, RAM, or disk
4. **Fixed word size** — typically w = c·log n bits, large enough to hold an index into the input

**What it ignores:** Memory hierarchy, caching effects, parallelism, compiler optimizations, pipelining.

**Why it's still useful:** Despite these simplifications, the RAM model is an excellent predictor of real-world performance. An O(n log n) algorithm virtually always beats an O(n²) algorithm in practice for large n.

---

### Q4. Differentiate between best case, worst case, and average case analysis. Which one is most commonly used and why?

**Answer:**

| Aspect | Best Case | Worst Case | Average Case |
|--------|-----------|------------|-------------|
| **Definition** | Minimum T over all inputs of size n | Maximum T over all inputs of size n | Expected T over a probability distribution of inputs |
| **Formula** | T_best(n) = min{T(I) : \|I\| = n} | T_worst(n) = max{T(I) : \|I\| = n} | T_avg(n) = Σ P(I)·T(I) |
| **Usefulness** | Rarely informative | Gives an upper bound guarantee | Realistic performance prediction |
| **Difficulty** | Easy to compute | Moderate | Hard (needs probability distribution) |

**Worst case is most commonly used** because:
- It gives a **guarantee**: the algorithm will never take longer than this
- It does **not require probability assumptions** about the input
- For many algorithms (e.g., database searches for absent keys), the worst case occurs frequently
- It is the **fairest basis for comparison** between algorithms

---

### Q5. What is the difference between partial correctness and total correctness?

**Answer:**

| | Partial Correctness | Total Correctness |
|---|---|---|
| **Definition** | IF the algorithm terminates, THEN the output is correct | The algorithm ALWAYS terminates AND the output is correct |
| **Termination required?** | No — the algorithm may loop forever | Yes — must be guaranteed |
| **Relationship** | Necessary but not sufficient | Complete guarantee |

**Total Correctness = Partial Correctness + Termination**

To prove total correctness, you must prove two things separately:
1. That the algorithm produces the right answer (using loop invariants or induction)
2. That the algorithm terminates (usually by showing a decreasing measure, like a loop counter approaching its bound)

---

### Q6. What is a loop invariant? State and explain its three properties.

**Answer:**

A loop invariant is a logical assertion (a property) about the state of the algorithm that holds at a specific point in the loop. It is used to prove algorithm correctness, analogous to mathematical induction.

**Three properties:**

1. **Initialization (Base Case):** The invariant is true before the first iteration of the loop. This corresponds to the base case of induction.

2. **Maintenance (Inductive Step):** If the invariant is true before an iteration of the loop, it remains true after that iteration. This corresponds to the inductive step.

3. **Termination:** When the loop terminates, the invariant, combined with the reason the loop terminated, gives us a useful property that helps prove the algorithm is correct. This is where we "cash in" the invariant.

---

### Q7. What is the divide-and-conquer paradigm? Give three examples of algorithms that use it.

**Answer:**

Divide-and-conquer is an algorithm design paradigm with three steps at each level of recursion:

1. **Divide** — Break the problem into smaller subproblems that are instances of the same problem
2. **Conquer** — Solve the subproblems recursively. If small enough, solve directly (base case)
3. **Combine** — Merge the subproblem solutions into a solution for the original problem

**Examples:**

| Algorithm | Divide | Conquer | Combine | Recurrence | Complexity |
|-----------|--------|---------|---------|------------|------------|
| Merge Sort | Split array in half | Sort each half recursively | Merge two sorted halves | T(n) = 2T(n/2) + O(n) | O(n log n) |
| Binary Search | Compare with middle element | Search one half | No combining needed | T(n) = T(n/2) + O(1) | O(log n) |
| Strassen's Matrix Multiplication | Partition into submatrices | 7 recursive multiplications | Add/subtract subresults | T(n) = 7T(n/2) + O(n²) | O(n^2.807) |

---

### Q8. When does the Master Theorem NOT apply? Give examples.

**Answer:**

The Master Theorem does not apply in the following situations:

**1. Subproblems of unequal sizes:**
```
T(n) = T(n/3) + T(2n/3) + n
```
The two subproblems have different sizes (n/3 and 2n/3), so the standard form T(n) = aT(n/b) + f(n) doesn't fit.

**2. Non-polynomial gap between f(n) and n^(log_b a):**
```
T(n) = 2T(n/2) + n/log n
```
Here n^(log_b a) = n, and f(n) = n/log n. The function f(n) is smaller than n but NOT polynomially smaller (n/log n is not O(n^(1-ε)) for any ε > 0). This falls in the gap between Case 1 and Case 2.

```
T(n) = 4T(n/2) + n² log n
```
Here n^(log_b a) = n², and f(n) = n² log n. The function f(n) is larger than n² but not polynomially larger. Gap between Case 2 and Case 3.

**3. Subtraction instead of division:**
```
T(n) = T(n-1) + n          (no division by constant)
T(n) = 2T(n-1) + 1         (Towers of Hanoi)
```

**4. Non-constant number of subproblems:**
```
T(n) = nT(n/2) + n         (a depends on n)
```

Use the **Recursion Tree** or **Substitution Method** instead.

---

# Section B: Counting Primitive Operations

### Q9. Count the primitive operations and determine the Big-Θ running time:

```
Algorithm arrayMax(A, n)
    currentMax ← A[0]
    for i ← 1 to n-1 do
        if A[i] > currentMax then
            currentMax ← A[i]
    return currentMax
```

**Answer:**

| Line | Operation | Count (worst case) |
|------|-----------|-------------------|
| `currentMax ← A[0]` | 1 index + 1 assign | 2 |
| `for i ← 1 to n-1` | 1 assign + (n-1) increments + n comparisons | 2n |
| `if A[i] > currentMax` | 1 index + 1 compare, runs (n-1) times | 2(n-1) |
| `currentMax ← A[i]` | 1 index + 1 assign, runs (n-1) times worst case | 2(n-1) |
| `return currentMax` | 1 return | 1 |
| **Total** | | **6n - 3** |

**Best case** (max at index 0): Line 4 never executes → **4n - 1** ops

In either case, T(n) = **Θ(n)** — linear time.

---

### Q10. Count the primitive operations and give the Big-Θ running time:

```
Algorithm PrefixAverages(X, n)
    Let A be an array of n numbers
    for i ← 0 to n-1 do
        s ← 0
        for j ← 0 to i do
            s ← s + X[j]
        A[i] ← s / (i + 1)
    return A
```

**Answer:**

| Line | Operation | Count |
|------|-----------|-------|
| Array creation | n allocations | n |
| Outer loop `for i ← 0 to n-1` | Runs n times | n |
| `s ← 0` | 1 assign, n times | n |
| Inner loop `for j ← 0 to i` | Runs (i+1) times for each i | Σ(i+1) for i=0 to n-1 = n(n+1)/2 |
| `s ← s + X[j]` | 1 add + 1 index + 1 assign, inner iterations | 3 · n(n+1)/2 |
| `A[i] ← s / (i+1)` | 1 divide + 1 add + 1 assign + 1 index, n times | 4n |
| `return A` | 1 | 1 |

**Dominant term:** The inner loop contributes n(n+1)/2 iterations = Θ(n²)

**T(n) = Θ(n²)**

---

### Q11. Analyze this improved version and compare:

```
Algorithm PrefixAverages2(X, n)
    Let A be an array of n numbers
    s ← 0
    for i ← 0 to n-1 do
        s ← s + X[i]
        A[i] ← s / (i + 1)
    return A
```

**Answer:**

| Line | Operation | Count |
|------|-----------|-------|
| Array creation | n | n |
| `s ← 0` | 1 | 1 |
| Outer loop | n iterations | n |
| `s ← s + X[i]` | 1 add + 1 index + 1 assign | 3n |
| `A[i] ← s / (i+1)` | 1 div + 1 add + 1 assign + 1 index | 4n |
| `return A` | 1 | 1 |

**T(n) = Θ(n)** — linear time!

**Comparison:** PrefixAverages is Θ(n²), PrefixAverages2 is Θ(n). The improved version is faster by a factor of n because it maintains a running sum instead of recomputing from scratch each time.

---

# Section C: Asymptotic Notation — Proofs & Comparisons

### Q12. Prove that 5n² + 3n + 20 = O(n²).

**Answer:**

We need to find constants c > 0 and n₀ > 0 such that:
```
5n² + 3n + 20 ≤ c·n²   for all n ≥ n₀
```

For n ≥ 1:
```
5n² + 3n + 20 ≤ 5n² + 3n² + 20n²    (since n ≤ n² and 20 ≤ 20n² for n ≥ 1)
              = 28n²
```

Choose **c = 28, n₀ = 1**. ✓

Alternatively, a tighter bound: for n ≥ 5:
```
3n ≤ n²  and  20 ≤ n²
So 5n² + 3n + 20 ≤ 5n² + n² + n² = 7n²
```
Choose **c = 7, n₀ = 5**. ✓

---

### Q13. Prove that 3n³ + 2n² + 5 = Θ(n³).

**Answer:**

**Upper bound (O):** For n ≥ 1:
```
3n³ + 2n² + 5 ≤ 3n³ + 2n³ + 5n³ = 10n³
```
So 3n³ + 2n² + 5 = O(n³) with c₂ = 10, n₀ = 1. ✓

**Lower bound (Ω):** For n ≥ 1:
```
3n³ + 2n² + 5 ≥ 3n³
```
So 3n³ + 2n² + 5 = Ω(n³) with c₁ = 3, n₀ = 1. ✓

**Since both O(n³) and Ω(n³) hold:**
```
3n³ + 2n² + 5 = Θ(n³)    with c₁ = 3, c₂ = 10, n₀ = 1.  ✓
```

---

### Q14. Prove that n² ≠ O(n).

**Answer:**

Suppose, for contradiction, that n² = O(n). Then there exist c > 0 and n₀ such that:
```
n² ≤ c·n    for all n ≥ n₀
```

Dividing both sides by n (valid since n > 0):
```
n ≤ c    for all n ≥ n₀
```

But n grows without bound, so no finite constant c can satisfy this. **Contradiction.**

Therefore n² ≠ O(n). ✓

---

### Q15. Prove or disprove: 2^(n+1) = O(2ⁿ).

**Answer:**

**TRUE.** We need c and n₀ such that 2^(n+1) ≤ c·2ⁿ.

```
2^(n+1) = 2 · 2ⁿ ≤ 2 · 2ⁿ
```

Choose **c = 2, n₀ = 1**. ✓

Note: 2^(n+1) = Θ(2ⁿ) — the constant factor of 2 is absorbed by Big-Oh.

---

### Q16. Prove or disprove: 2^(2n) = O(2ⁿ).

**Answer:**

**FALSE.** Suppose 2^(2n) ≤ c·2ⁿ for all n ≥ n₀.

```
2^(2n) = (2ⁿ)²
```

So we need (2ⁿ)² ≤ c·2ⁿ, which gives 2ⁿ ≤ c. But 2ⁿ grows without bound. **Contradiction.**

Therefore 2^(2n) ≠ O(2ⁿ). ✓

**Key lesson:** 2^(2n) is NOT a constant multiple of 2ⁿ. In fact, 2^(2n) = (2ⁿ)², which is exponentially larger. Don't confuse this with 2^(n+1) = 2·2ⁿ which IS O(2ⁿ).

---

### Q17. Order the following functions by growth rate (slowest to fastest):

```
n², 2ⁿ, n log n, n^(1/2), log n, n!, (log n)², n, 1, n³, n log² n
```

**Answer:**

```
1  ≺  log n  ≺  (log n)²  ≺  n^(1/2)  ≺  n  ≺  n log n  ≺  n log² n  ≺  n²  ≺  n³  ≺  2ⁿ  ≺  n!
```

**Justification for tricky comparisons:**
- (log n)² vs n^(1/2): Any polynomial of log n grows slower than any positive power of n. lim (log n)² / √n = 0.
- n log n vs n log² n: log n ≺ log² n, so n log n ≺ n log² n.
- n³ vs 2ⁿ: Any polynomial grows slower than any exponential. lim n³/2ⁿ = 0.
- 2ⁿ vs n!: Using Stirling's approximation, n! ≈ (n/e)ⁿ · √(2πn), which grows much faster than 2ⁿ.

---

### Q18. For each pair, state whether f(n) = O(g(n)), f(n) = Ω(g(n)), or f(n) = Θ(g(n)):

| # | f(n) | g(n) | Answer |
|---|------|------|--------|
| a | n² | n³ | **f = O(g)** — n² grows slower |
| b | n² log n | n² | **f = Ω(g)** — the log n factor makes f larger |
| c | 3n² + 7n | n² | **f = Θ(g)** — same growth rate, lower terms drop |
| d | 2^n | 3^n | **f = O(g)** — (2/3)ⁿ → 0 |
| e | log₂ n | log₃ n | **f = Θ(g)** — differ by constant factor log₂3 |
| f | n! | 2ⁿ | **f = Ω(g)** — n! grows faster |
| g | n | n^(1 + 1/log n) | **f = Θ(g)** — n^(1/log n) = n^(1/log n) = 2 (constant), so g = 2n |

**Detailed justification for (g):**
```
n^(1 + 1/log n) = n · n^(1/log n)
n^(1/log₂ n) = 2^(log₂ n · 1/log₂ n) = 2^1 = 2
So g(n) = 2n = Θ(n)
```

---

### Q19. Using limits, determine the asymptotic relationship between f(n) = n² and g(n) = n² log n.

**Answer:**

```
lim (n→∞) f(n)/g(n) = lim n² / (n² log n) = lim 1/log n = 0
```

Since the limit is 0: **f(n) = o(g(n))**, meaning n² grows strictly slower than n² log n.

Equivalently: n² = O(n² log n) but n² ≠ Θ(n² log n).

---

### Q20. Prove that log(n!) = Θ(n log n).

**Answer:**

**Upper bound:** Since n! ≤ nⁿ:
```
log(n!) ≤ log(nⁿ) = n log n
→ log(n!) = O(n log n)  ✓
```

**Lower bound:** Consider only the top half of the factorial product:
```
n! = 1 · 2 · 3 · ... · n ≥ (n/2)^(n/2)

(Taking only the terms n/2, n/2+1, ..., n, each of which is ≥ n/2)

log(n!) ≥ log((n/2)^(n/2))
        = (n/2) · log(n/2)
        = (n/2) · (log n - 1)
        = (n/2) log n - n/2
        = Ω(n log n)  ✓
```

Since both bounds hold: **log(n!) = Θ(n log n)**. ✓

---

# Section D: Analyzing Iterative Algorithms

### Q21. What is the time complexity of the following code?

```
for i ← 1 to n do
    for j ← 1 to n do
        for k ← 1 to n do
            x ← x + 1
```

**Answer:**

Three nested loops, each running n times. The innermost statement executes n × n × n = n³ times.

**T(n) = Θ(n³)**

---

### Q22. Analyze the following:

```
for i ← 1 to n do
    for j ← 1 to i do
        x ← x + 1
```

**Answer:**

For each value of i, the inner loop runs i times.

```
Total = Σ i  for i = 1 to n
      = 1 + 2 + 3 + ... + n
      = n(n+1)/2
```

**T(n) = Θ(n²)**

---

### Q23. Analyze the following:

```
i ← n
while i ≥ 1 do
    j ← i
    while j ≤ n do
        x ← x + 1
        j ← 2 * j
    i ← i / 2
```

**Answer:**

**Outer loop:** i takes values n, n/2, n/4, ..., 1 → runs log₂ n + 1 times.

**Inner loop:** For a given i, j starts at i and doubles each time until j > n. Number of inner iterations = log₂(n/i) + 1.

```
Total = Σ (log₂(n/i) + 1)   for i = n, n/2, n/4, ..., 1

Let i = n/2^k where k = 0, 1, 2, ..., log n:
    log₂(n/i) + 1 = log₂(2^k) + 1 = k + 1

Total = Σ (k+1)  for k = 0 to log n
      = 1 + 2 + 3 + ... + (log n + 1)
      = (log n + 1)(log n + 2) / 2
```

**T(n) = Θ(log² n)**

---

### Q24. Analyze the following:

```
for i ← 1 to n do
    j ← 1
    while j < n do
        j ← j * 2
```

**Answer:**

**Outer loop:** Runs n times.

**Inner loop:** j takes values 1, 2, 4, 8, ..., until j ≥ n. This takes ⌈log₂ n⌉ iterations.

**Total:** n × log₂ n

**T(n) = Θ(n log n)**

---

### Q25. Analyze the following:

```
for i ← 1 to n do
    for j ← 1 to i² do
        x ← x + 1
```

**Answer:**

For each i, the inner loop runs i² times.

```
Total = Σ i²  for i = 1 to n
      = 1² + 2² + 3² + ... + n²
      = n(n+1)(2n+1) / 6
```

**T(n) = Θ(n³)**

---

### Q26. Analyze the following code with two separate loops:

```
for i ← 1 to n do
    for j ← 1 to n do
        A[i][j] ← 0

for i ← 1 to n do
    A[i][i] ← 1
```

**Answer:**

- First block: nested loops → Θ(n²)
- Second block: single loop → Θ(n)

By the **sum rule**: T(n) = Θ(n²) + Θ(n) = **Θ(n²)**

The dominant term is n².

---

# Section E: Loop Invariants & Correctness Proofs

### Q27. Prove the correctness of the following selection sort algorithm using a loop invariant:

```
SelectionSort(A, n)
    for i ← 0 to n-2 do
        min ← i
        for j ← i+1 to n-1 do
            if A[j] < A[min] then
                min ← j
        swap(A[i], A[min])
```

**Answer:**

**Loop Invariant:** At the start of each iteration of the outer loop with index i, the subarray A[0..i-1] contains the i smallest elements of the original array, in sorted order.

**Initialization (i = 0):**
Before the first iteration, A[0..-1] is empty. An empty subarray trivially satisfies the invariant. ✓

**Maintenance:**
Assume the invariant holds at the start of iteration i: A[0..i-1] contains the i smallest elements in sorted order.

The inner loop finds the index `min` of the smallest element in A[i..n-1]. After swapping A[i] with A[min]:
- A[i] now holds the (i+1)-th smallest element in the original array
- A[0..i] contains the (i+1) smallest elements in sorted order
- All elements in A[i+1..n-1] are ≥ A[i]

The invariant holds for i+1. ✓

**Termination:**
The loop terminates when i = n-1. The invariant tells us A[0..n-2] contains the (n-1) smallest elements in sorted order. The remaining element A[n-1] must be the largest, so A[0..n-1] is completely sorted. ✓

---

### Q28. State a loop invariant for binary search and prove correctness:

```
BinarySearch(A, n, key)
    lo ← 0
    hi ← n - 1
    while lo ≤ hi do
        mid ← ⌊(lo + hi) / 2⌋
        if A[mid] = key then return mid
        else if key < A[mid] then hi ← mid - 1
        else lo ← mid + 1
    return -1
```

**Answer:**

**Precondition:** A is sorted in non-decreasing order.

**Loop Invariant:** At the start of each iteration, if `key` exists in A, then key is in A[lo..hi].

**Initialization:** lo = 0, hi = n-1. A[0..n-1] is the entire array. If key is in A, it's certainly in A[0..n-1]. ✓

**Maintenance:** Assume if key exists in A, it's in A[lo..hi].
- If A[mid] = key: we return mid. Correct! ✓
- If key < A[mid]: since A is sorted, key cannot be in A[mid..hi]. So key (if it exists) must be in A[lo..mid-1]. Setting hi ← mid-1 maintains the invariant. ✓
- If key > A[mid]: since A is sorted, key cannot be in A[lo..mid]. So key (if it exists) must be in A[mid+1..hi]. Setting lo ← mid+1 maintains the invariant. ✓

**Termination:** The loop terminates when lo > hi, meaning A[lo..hi] is empty. By the invariant, if key existed, it would be in this empty range — a contradiction. So key is not in A, and returning -1 is correct. ✓

**Termination proof:** The quantity (hi - lo) strictly decreases in each iteration (since mid is removed from the range), so the loop must terminate.

---

### Q29. Prove that the following algorithm correctly computes aⁿ:

```
Power(a, n)
    if n = 0 then return 1
    if n is even then
        half ← Power(a, n/2)
        return half × half
    else
        return a × Power(a, n-1)
```

**Answer:**

**Proof by strong induction on n:**

**Base case (n = 0):** Power(a, 0) returns 1. Since a⁰ = 1 by definition, this is correct. ✓

**Inductive hypothesis:** Assume Power(a, k) correctly computes aᵏ for all k < n.

**Inductive step:** For n > 0, two cases:

**Case 1: n is even.**
Power(a, n) calls Power(a, n/2). Since n/2 < n, by the inductive hypothesis, half = a^(n/2).
Returns half × half = a^(n/2) · a^(n/2) = a^(n/2 + n/2) = aⁿ. ✓

**Case 2: n is odd.**
Power(a, n) calls Power(a, n-1). Since n-1 < n, by the inductive hypothesis, Power(a, n-1) = a^(n-1).
Returns a × a^(n-1) = aⁿ. ✓

**Termination:** In each recursive call, the argument strictly decreases (either halved or reduced by 1), and the base case n = 0 is reached in finite steps. ✓

---

# Section F: Writing Recurrence Relations

### Q30. Write the recurrence relation for each algorithm and state the solution:

**(a) Factorial**
```
Fact(n)
    if n = 0 return 1
    return n × Fact(n-1)
```

**Answer:**
```
T(n) = T(n-1) + O(1)
T(0) = O(1)

Solution: T(n) = O(n)
```

**(b) Fibonacci (naive)**
```
Fib(n)
    if n ≤ 1 return n
    return Fib(n-1) + Fib(n-2)
```

**Answer:**
```
T(n) = T(n-1) + T(n-2) + O(1)
T(0) = T(1) = O(1)

Solution: T(n) = O(2ⁿ)  (more precisely, O(φⁿ) where φ = (1+√5)/2 ≈ 1.618)
```

**(c) Merge Sort**
```
MergeSort(A, lo, hi)
    if lo < hi then
        mid ← ⌊(lo+hi)/2⌋
        MergeSort(A, lo, mid)
        MergeSort(A, mid+1, hi)
        Merge(A, lo, mid, hi)
```

**Answer:**
```
T(n) = 2T(n/2) + O(n)       (Merge takes O(n))
T(1) = O(1)

Solution: T(n) = O(n log n)
```

**(d) Binary Search**
```
BinSearch(A, lo, hi, key)
    if lo > hi return -1
    mid ← ⌊(lo+hi)/2⌋
    if A[mid] = key return mid
    else if key < A[mid]
        return BinSearch(A, lo, mid-1, key)
    else
        return BinSearch(A, mid+1, hi, key)
```

**Answer:**
```
T(n) = T(n/2) + O(1)        (only ONE recursive call)
T(1) = O(1)

Solution: T(n) = O(log n)
```

**(e) Towers of Hanoi**
```
Hanoi(n, src, dst, aux)
    if n = 1 then move disk from src to dst
    else
        Hanoi(n-1, src, aux, dst)
        move disk from src to dst
        Hanoi(n-1, aux, dst, src)
```

**Answer:**
```
T(n) = 2T(n-1) + O(1)
T(1) = O(1)

Solution: T(n) = O(2ⁿ)

(By unrolling: T(n) = 2ⁿ - 1 moves exactly)
```

**(f) Fast Exponentiation**
```
Power(a, n)
    if n = 0 return 1
    if n is even
        half ← Power(a, n/2)
        return half × half
    else
        return a × Power(a, n-1)
```

**Answer:**
```
T(n) = T(n/2) + O(1)        (even case dominates; odd just adds 1 step then even)
T(0) = O(1)

Solution: T(n) = O(log n)
```

---

# Section G: Master Theorem

### Q31. Solve each using the Master Theorem. State a, b, f(n), n^(log_b a), the case, and the answer.

**(a) T(n) = 4T(n/2) + n**

```
a = 4, b = 2, f(n) = n
n^(log₂ 4) = n² 

Compare: f(n) = n vs n²
n = O(n^(2-ε)) with ε = 1 → Case 1

T(n) = Θ(n²)
```

**(b) T(n) = 4T(n/2) + n²**

```
a = 4, b = 2, f(n) = n²
n^(log₂ 4) = n²

Compare: f(n) = n² = Θ(n²) → Case 2

T(n) = Θ(n² log n)
```

**(c) T(n) = 4T(n/2) + n³**

```
a = 4, b = 2, f(n) = n³
n^(log₂ 4) = n²

Compare: f(n) = n³ = Ω(n^(2+ε)) with ε = 1 → check regularity
Regularity: 4·(n/2)³ = 4n³/8 = n³/2 ≤ (1/2)·n³ → c = 1/2 < 1 ✓

Case 3: T(n) = Θ(n³)
```

**(d) T(n) = 2T(n/4) + √n**

```
a = 2, b = 4, f(n) = √n = n^(1/2)
n^(log₄ 2) = n^(1/2)

Compare: f(n) = n^(1/2) = Θ(n^(1/2)) → Case 2

T(n) = Θ(√n · log n)
```

**(e) T(n) = 7T(n/2) + n²**

```
a = 7, b = 2, f(n) = n²
n^(log₂ 7) = n^2.807

Compare: f(n) = n² = O(n^(2.807-ε)) with ε = 0.8 → Case 1

T(n) = Θ(n^(log₂ 7)) ≈ Θ(n^2.807)     ← Strassen's algorithm
```

**(f) T(n) = T(2n/3) + 1**

```
a = 1, b = 3/2, f(n) = 1
n^(log_{3/2} 1) = n⁰ = 1

Compare: f(n) = 1 = Θ(1) = Θ(n⁰) → Case 2

T(n) = Θ(log n)
```

**(g) T(n) = 16T(n/4) + n²**

```
a = 16, b = 4, f(n) = n²
n^(log₄ 16) = n²

Compare: f(n) = n² = Θ(n²) → Case 2

T(n) = Θ(n² log n)
```

**(h) T(n) = 3T(n/9) + √n**

```
a = 3, b = 9, f(n) = √n = n^(1/2)
n^(log₉ 3) = n^(1/2)

Compare: f(n) = n^(1/2) = Θ(n^(1/2)) → Case 2

T(n) = Θ(√n · log n)
```

---

### Q32. For which of these can the Master Theorem NOT be applied? Explain why.

**(a) T(n) = 2T(n/2) + n log n**

Cannot directly apply the **basic** Master Theorem. Here a = 2, b = 2, n^(log_b a) = n.
f(n) = n log n is larger than n, but NOT polynomially larger: n log n ≠ Ω(n^(1+ε)) for any ε > 0.
This falls in the **gap between Case 2 and Case 3**.

However, the **extended Case 2** applies: f(n) = Θ(n · log¹ n), so T(n) = Θ(n log² n).

**(b) T(n) = T(n-2) + n²**

Cannot apply — this is a **subtraction** recurrence (n-2 not n/b).
Solve by unrolling: T(n) = n² + (n-2)² + (n-4)² + ... ≈ n/2 terms of order n² → T(n) = Θ(n³).

**(c) T(n) = T(n/2) + T(n/3) + n**

Cannot apply — **different subproblem sizes** (n/2 and n/3), doesn't fit T(n) = aT(n/b) + f(n).

**(d) T(n) = 0.5T(n/2) + n**

Cannot apply — **a = 0.5 < 1**, but actually the Master Theorem requires a ≥ 1. Having less than one "subproblem" doesn't make physical sense. (Some versions allow fractional a; this would be Case 3: T(n) = Θ(n).)

---

# Section H: Recursion Tree Method

### Q33. Solve T(n) = 4T(n/2) + cn using the recursion tree method.

**Answer:**

```
Level 0:                   cn                           Total: cn
                      / |  | \
Level 1:         cn/2  cn/2  cn/2  cn/2                Total: 4·cn/2 = 2cn
                /||\   /||\   /||\   /||\
Level 2:    16 nodes, each cn/4                        Total: 16·cn/4 = 4cn = 2²·cn
                      ...
Level i:    4ⁱ nodes, each cn/2ⁱ                       Total: 4ⁱ·cn/2ⁱ = 2ⁱ·cn
                      ...
Level lg n: 4^(lg n) = n² leaves                       Total: Θ(n²)
```

**Summing all levels:**
```
T(n) = cn · Σ 2ⁱ   for i = 0 to lg n - 1  +  Θ(n²)
     = cn · (2^(lg n) - 1)/(2-1)  +  Θ(n²)
     = cn · (n - 1)  +  Θ(n²)
     = cn² - cn + Θ(n²)
     = Θ(n²)
```

The series is **increasing** (ratio = 2 > 1), so the **leaves dominate**. This matches Master Theorem Case 1 (a=4, b=2, f(n)=n, n^(log₂4) = n²).

---

### Q34. Solve T(n) = T(n/3) + T(2n/3) + cn using the recursion tree method.

**Answer:**

This has **unequal splits** — the Master Theorem doesn't apply.

```
Level 0:                         cn                          Total: cn
                               /     \
Level 1:                  cn/3        2cn/3                  Total: cn/3 + 2cn/3 = cn
                         /    \      /     \
Level 2:            cn/9   2cn/9  2cn/9   4cn/9              Total: cn
                     ...
```

**Key observations:**
- Every complete level sums to exactly cn
- **Shortest path** (always left): n → n/3 → n/9 → ... → 1, depth = log₃ n
- **Longest path** (always right): n → 2n/3 → (2/3)²n → ... → 1, depth = log_{3/2} n

**Bounds:**
```
Lower: cn · log₃ n = Ω(n log n)
Upper: cn · log_{3/2} n = O(n log n)
```

Since log₃ n and log_{3/2} n are both Θ(log n):

**T(n) = Θ(n log n)**

---

### Q35. Solve T(n) = T(n/2) + cn² using the recursion tree method.

**Answer:**

```
Level 0:     cn²                 Total: cn²
             |
Level 1:     c(n/2)² = cn²/4   Total: cn²/4
             |
Level 2:     c(n/4)² = cn²/16  Total: cn²/16
             |
Level i:     c(n/2ⁱ)²          Total: (1/4)ⁱ · cn²
             |
Level lg n:  c · 1              Total: c
```

**Summing:**
```
T(n) = cn² · Σ (1/4)ⁱ   for i = 0 to lg n
     < cn² · Σ (1/4)ⁱ   for i = 0 to ∞
     = cn² · 1/(1 - 1/4)
     = cn² · 4/3
     = Θ(n²)
```

The series is **decreasing** (ratio = 1/4 < 1), so the **root dominates**.

---

### Q36. Solve T(n) = 3T(n/2) + cn using the recursion tree method.

**Answer:**

```
Level 0:               cn                     Total: cn
                     / | \
Level 1:         cn/2  cn/2  cn/2             Total: 3·cn/2 = (3/2)cn
                /|\   /|\   /|\
Level 2:    9 nodes, each cn/4                Total: 9·cn/4 = (3/2)²cn = (9/4)cn
                   ...
Level i:    3ⁱ nodes, each cn/2ⁱ              Total: (3/2)ⁱ · cn
                   ...
Level lg n: 3^(lg n) = n^(lg 3) leaves        Total: Θ(n^(lg 3))
```

**Summing:**
```
T(n) = cn · Σ (3/2)ⁱ  for i = 0 to lg n - 1  +  Θ(n^(lg 3))
     = cn · [(3/2)^(lg n) - 1] / (3/2 - 1)  +  Θ(n^(lg 3))
     = 2cn · [n^(lg(3/2)) - 1]  +  Θ(n^(lg 3))
     = Θ(n^(lg 3))
```

The series is **increasing** (ratio = 3/2 > 1), so the **leaves dominate**.

**T(n) = Θ(n^(lg 3)) ≈ Θ(n^1.585)**

This matches Master Theorem Case 1 (a=3, b=2, n^(log₂3) = n^1.585 > n¹).

---

# Section I: Substitution Method

### Q37. Prove T(n) = 2T(n/2) + n is O(n log n) using the substitution method.

**Answer:**

**Guess:** T(n) ≤ c · n · lg n for some constant c > 0.

**Inductive hypothesis:** Assume T(k) ≤ c · k · lg k for all k < n.

**Inductive step:**
```
T(n) = 2T(n/2) + n
     ≤ 2 · c · (n/2) · lg(n/2) + n
     = c · n · lg(n/2) + n
     = c · n · (lg n - 1) + n
     = c · n · lg n - cn + n
     = c · n · lg n + n(1 - c)
     ≤ c · n · lg n                  (when c ≥ 1)  ✓
```

**Base case:** T(2) = 2T(1) + 2 = 4. We need 4 ≤ c · 2 · lg 2 = 2c. So c ≥ 2. ✓

**Conclusion:** T(n) = O(n lg n) with c = 2.

---

### Q38. Prove T(n) = T(n-1) + n is O(n²) using the substitution method.

**Answer:**

**Guess:** T(n) ≤ c · n²

**Inductive step:**
```
T(n) = T(n-1) + n
     ≤ c(n-1)² + n
     = cn² - 2cn + c + n
     = cn² + n(1 - 2c) + c
     ≤ cn²                      (when c ≥ 1, since 1 - 2c < 0 and c ≥ 0)  ✓
```

**Base case:** T(1) = 1 ≤ c · 1² = c. True for c ≥ 1. ✓

**T(n) = O(n²)**

---

### Q39. Prove T(n) = 4T(n/2) + n is O(n²) using the substitution method.

**Answer:**

**Attempt 1 (fails):** Guess T(n) ≤ cn².
```
T(n) ≤ 4c(n/2)² + n = cn² + n
```
We get cn² + n, which is NOT ≤ cn². **Stuck!**

**Attempt 2 (works — subtract lower-order term):** Guess T(n) ≤ cn² - dn.
```
T(n) = 4T(n/2) + n
     ≤ 4(c(n/2)² - d(n/2)) + n
     = 4(cn²/4 - dn/2) + n
     = cn² - 2dn + n
     = cn² - dn - dn + n
     = cn² - dn + n(1 - d)
     ≤ cn² - dn                  (when d ≥ 1)  ✓
```

Since cn² - dn ≤ cn², we have T(n) = O(n²). ✓

---

### Q40. Solve T(n) = 3T(√n) + log n by changing variables.

**Answer:**

**Step 1: Change variables.** Let m = lg n, so n = 2^m and √n = 2^(m/2).

```
T(2^m) = 3T(2^(m/2)) + m
```

**Step 2: Rename.** Let S(m) = T(2^m):

```
S(m) = 3S(m/2) + m
```

**Step 3: Apply Master Theorem:**
```
a = 3, b = 2, f(m) = m
m^(log₂ 3) = m^1.585

Compare: f(m) = m = O(m^(1.585-ε)) with ε = 0.5 → Case 1

S(m) = Θ(m^(lg 3))
```

**Step 4: Substitute back:** m = lg n

```
T(n) = Θ((lg n)^(lg 3)) ≈ Θ((lg n)^1.585)
```

---

### Q41. Prove T(n) = 2T(⌊n/2⌋ + 17) + n is O(n lg n) using substitution.

**Answer:**

**Guess:** T(n) ≤ c(n - a) lg(n - a) for constants c, a.

```
T(n) = 2T(⌊n/2⌋ + 17) + n
     ≤ 2c(⌊n/2⌋ + 17 - a) lg(⌊n/2⌋ + 17 - a) + n
     ≤ 2c(n/2 + 17 - a) lg(n/2 + 17 - a) + n
     = c(n + 34 - 2a) lg((n + 34 - 2a)/2) + n
     = c(n + 34 - 2a) lg(n + 34 - 2a) - c(n + 34 - 2a) + n
```

Choosing **a = 34** makes n + 34 - 2a = n - 34:
```
     = c(n - 34) lg(n - 34) - c(n - 34) + n
     = c(n - 34) lg(n - 34) + n(1 - c) + 34c
     ≤ c(n - 34) lg(n - 34)    (for c > 1 and n large enough)
     = c(n - a) lg(n - a)      ✓
```

Since c(n-a) lg(n-a) = O(n lg n), we conclude **T(n) = O(n lg n)**. ✓

This example from CLRS 4.3-6 shows that the "+17" in the subproblem size doesn't affect the asymptotic result.

---

# Section J: Mixed / Exam-Style Problems

### Q42. Given the following algorithm, find the time complexity:

```
Algorithm Mystery(A, n)
    if n ≤ 1 then return A[0]
    mid ← n/2
    left ← Mystery(A[0..mid-1], mid)
    right ← Mystery(A[mid..n-1], n-mid)
    if left > right then return left
    else return right
```

**Answer:**

**Step 1: Write the recurrence.**
- Two recursive calls, each on half the array → 2T(n/2)
- Non-recursive work: comparison, return → O(1)
```
T(n) = 2T(n/2) + O(1)
T(1) = O(1)
```

**Step 2: Apply Master Theorem.**
a = 2, b = 2, f(n) = 1, n^(log₂ 2) = n.
f(n) = 1 = O(n^(1-ε)) with ε = 1 → Case 1.

**T(n) = Θ(n)**

This algorithm finds the maximum element in an array using divide and conquer. It takes Θ(n) time — same as the iterative version, but with O(log n) stack space.

---

### Q43. Consider the recurrence T(n) = T(n/4) + T(n/2) + n². Solve using the recursion tree.

**Answer:**

```
Level 0:                    cn²                              Total: cn²
                          /      \
Level 1:           c(n/4)²    c(n/2)²                       Total: cn²/16 + cn²/4 = 5cn²/16
                   /    \      /    \
Level 2:   c(n/16)² c(n/8)² c(n/8)² c(n/4)²                Total: (5/16)² · cn² (approx)
                          ...
```

**Level cost ratio:** At each level, the total cost is multiplied by approximately 5/16.

Since 5/16 < 1, the series is **geometrically decreasing**.

```
T(n) ≤ cn² · Σ (5/16)ⁱ  for i = 0 to ∞
     = cn² · 1/(1 - 5/16)
     = cn² · 16/11
     = O(n²)
```

The root dominates. **T(n) = Θ(n²)**.

---

### Q44. An algorithm divides a problem of size n into 5 subproblems of size n/3, with O(n log n) overhead. What is its time complexity?

**Answer:**

**Recurrence:** T(n) = 5T(n/3) + O(n log n)

**Master Theorem:**
```
a = 5, b = 3, f(n) = n log n
n^(log₃ 5) = n^1.465

Compare: f(n) = n log n vs n^1.465.
Since n log n = O(n^(1.465 - ε)) for ε ≈ 0.4 (because n log n = O(n^1.1), and 1.1 < 1.465):
```
Wait — we need to be more careful. Is n log n = O(n^(1.465-ε))?

For any ε such that 1 < 1.465 - ε: choose ε = 0.4, then 1.465 - 0.4 = 1.065.
Is n log n = O(n^1.065)? Since log n = O(n^0.065) for large n (any positive power dominates log), yes. ✓

**Case 1:** T(n) = Θ(n^(log₃ 5)) ≈ **Θ(n^1.465)**

---

### Q45. Prove that insertion sort has a worst-case running time of Θ(n²) and a best-case running time of Θ(n).

**Answer:**

```
InsertionSort(A, n)
    for j ← 1 to n-1 do            // c₁, runs n-1 times
        key ← A[j]                  // c₂, runs n-1 times
        i ← j - 1                   // c₃, runs n-1 times
        while i ≥ 0 and A[i] > key  // c₄, runs Σ tⱼ times
            A[i+1] ← A[i]           // c₅, runs Σ (tⱼ - 1) times
            i ← i - 1               // c₆, runs Σ (tⱼ - 1) times
        A[i+1] ← key                // c₇, runs n-1 times
```

Where tⱼ is the number of times the while-loop test executes for each j.

**General running time:**
```
T(n) = c₁(n-1) + c₂(n-1) + c₃(n-1) + c₄·Σtⱼ + c₅·Σ(tⱼ-1) + c₆·Σ(tⱼ-1) + c₇(n-1)
```

**Best case (already sorted):** For each j, A[j-1] ≤ key, so the while condition fails immediately. tⱼ = 1 for all j.
```
T(n) = (c₁+c₂+c₃+c₄+c₇)(n-1) = Θ(n)
```

**Worst case (reverse sorted):** For each j, every element A[0..j-1] must shift. tⱼ = j+1.
```
Σ tⱼ = Σ (j+1) for j=1 to n-1 = 2+3+...+n = n(n+1)/2 - 1
Σ (tⱼ-1) = Σ j for j=1 to n-1 = n(n-1)/2

T(n) = c₄·n(n+1)/2 + (c₅+c₆)·n(n-1)/2 + linear terms = Θ(n²)
```

---

### Q46. True or False (with justification):

**(a) n² + 10⁶n = Θ(n²)**

**TRUE.** For large n, the n² term dominates. The 10⁶n term is lower-order. Specifically, n² + 10⁶n ≤ 2n² for all n ≥ 10⁶.

**(b) If f(n) = O(g(n)), then g(n) = O(f(n))**

**FALSE.** Counterexample: n = O(n²) but n² ≠ O(n). Big-Oh is not symmetric.

**(c) If f(n) = O(g(n)) and g(n) = O(f(n)), then f(n) = Θ(g(n))**

**TRUE.** By definition, f = Θ(g) iff f = O(g) and f = Ω(g). And g = O(f) is equivalent to f = Ω(g).

**(d) n^(1/2) = O(n^(2/3))**

**TRUE.** lim n^(1/2) / n^(2/3) = lim n^(-1/6) = 0. So n^(1/2) = o(n^(2/3)), which implies O.

**(e) 10ⁿ = O(2ⁿ)**

**FALSE.** 10ⁿ/2ⁿ = (10/2)ⁿ = 5ⁿ → ∞. So 10ⁿ is not bounded by any constant times 2ⁿ.

**(f) The Master Theorem can solve T(n) = T(n-1) + 1**

**FALSE.** This is a subtraction recurrence (n-1, not n/b). The Master Theorem requires division by a constant.

**(g) The worst-case time of an algorithm gives a lower bound on its running time**

**FALSE.** The worst-case time is an **upper bound** on running time (it's the maximum over all inputs). A lower bound on running time for all inputs would be the best case.

**(h) O(n) ⊂ O(n log n) ⊂ O(n²)**

**TRUE.** Every function that is O(n) is also O(n log n), and every function that is O(n log n) is also O(n²). The inclusions are strict: n log n ∈ O(n log n) but n log n ∉ O(n).

---

### Q47. Solve by unrolling (repeated substitution): T(n) = 3T(n/3) + n

**Answer:**

```
T(n) = 3T(n/3) + n

Substitute T(n/3) = 3T(n/9) + n/3:
T(n) = 3[3T(n/9) + n/3] + n
     = 9T(n/9) + n + n
     = 9T(n/9) + 2n

Substitute T(n/9) = 3T(n/27) + n/9:
T(n) = 9[3T(n/27) + n/9] + 2n
     = 27T(n/27) + n + 2n
     = 27T(n/27) + 3n

Pattern after k steps:
T(n) = 3ᵏ · T(n/3ᵏ) + k·n

The recursion bottoms out when n/3ᵏ = 1, i.e., k = log₃ n.

T(n) = 3^(log₃ n) · T(1) + n·log₃ n
     = n · O(1) + n·log₃ n
     = O(n) + O(n log n)
     = O(n log n)
```

**T(n) = Θ(n log n)**

This matches Master Theorem Case 2 (a=3, b=3, f(n)=n, n^(log₃3) = n). ✓

---

### Q48. A recursive algorithm makes 2 recursive calls on problems of size n-1 each, with O(1) non-recursive work. What is its time complexity? What well-known problem has this recurrence?

**Answer:**

**Recurrence:** T(n) = 2T(n-1) + O(1), T(0) = O(1)

**By unrolling:**
```
T(n) = 2T(n-1) + c
     = 2[2T(n-2) + c] + c = 4T(n-2) + 2c + c = 4T(n-2) + 3c
     = 4[2T(n-3) + c] + 3c = 8T(n-3) + 4c + 3c = 8T(n-3) + 7c
     ...
After k steps: T(n) = 2ᵏ · T(n-k) + (2ᵏ - 1)·c

When k = n: T(n) = 2ⁿ · T(0) + (2ⁿ - 1)·c = 2ⁿ · O(1) + O(2ⁿ) = O(2ⁿ)
```

**T(n) = Θ(2ⁿ)** — exponential time.

This is the recurrence for the **Towers of Hanoi** problem. The minimum number of moves required is exactly 2ⁿ - 1.

---

### Q49. Consider the following two algorithms. Which is better for large n?

**Algorithm A:** T_A(n) = 100n log n
**Algorithm B:** T_B(n) = 2n²

**Answer:**

Set T_A(n) = T_B(n): 100n log n = 2n², so 50 log n = n.

For n < approximately 400: Algorithm B is faster (the constant 100 in A hurts).
For n > approximately 400: Algorithm A is faster.

**For large n, Algorithm A is better** because O(n log n) grows slower than O(n²). The constant factors only affect where the crossover point is.

| n | T_A = 100n log₂ n | T_B = 2n² | Winner |
|---|-------------------|-----------|--------|
| 100 | 66,438 | 20,000 | B |
| 500 | 448,856 | 500,000 | **A** |
| 1000 | 996,578 | 2,000,000 | **A** |
| 10000 | 13,287,712 | 200,000,000 | **A by 15x** |

---

### Q50. Given the recurrence T(n) = 4T(n/2) + n² log n, can you apply the Master Theorem? If not, find T(n).

**Answer:**

**Master Theorem analysis:**
```
a = 4, b = 2, f(n) = n² log n
n^(log₂ 4) = n²

Compare: f(n) = n² log n vs n²
```

n² log n is larger than n², but is it polynomially larger? We would need n² log n = Ω(n^(2+ε)), which means log n = Ω(n^ε). But log n is NOT Ω(n^ε) for any ε > 0. So this falls in the **gap between Case 2 and Case 3**. The basic Master Theorem does **not apply**.

**Solving by substitution:**

Guess: T(n) ≤ c · n² · log² n

```
T(n) = 4T(n/2) + n² log n
     ≤ 4 · c · (n/2)² · log²(n/2) + n² log n
     = 4 · c · n²/4 · (log n - 1)² + n² log n
     = c · n² · (log n - 1)² + n² log n
     = c · n² · (log² n - 2 log n + 1) + n² log n
     = c · n² · log² n - 2c · n² · log n + c · n² + n² log n
     = c · n² · log² n + n² log n(1 - 2c) + cn²
     ≤ c · n² · log² n          (when c ≥ 1)  ✓
```

**T(n) = O(n² log² n)**

*(Using the extended Case 2 of the Master Theorem with f(n) = Θ(n^(log_b a) · log^k n), we get T(n) = Θ(n^(log_b a) · log^(k+1) n) = Θ(n² log² n).)*

---

## Quick Reference: Key Formulas

### Summation Formulas
```
Σ (i=1 to n) 1 = n
Σ (i=1 to n) i = n(n+1)/2
Σ (i=1 to n) i² = n(n+1)(2n+1)/6
Σ (i=0 to n) rⁱ = (r^(n+1) - 1)/(r - 1)     for r ≠ 1
Σ (i=0 to ∞) rⁱ = 1/(1 - r)                   for |r| < 1
```

### Logarithm Rules
```
log(ab) = log a + log b
log(a/b) = log a - log b
log(aⁿ) = n · log a
log_b(a) = log_c(a) / log_c(b)
```

### Master Theorem: T(n) = aT(n/b) + f(n)
```
Compare f(n) with n^(log_b a):

Case 1: f(n) = O(n^(log_b a - ε))       → T(n) = Θ(n^(log_b a))
Case 2: f(n) = Θ(n^(log_b a))           → T(n) = Θ(n^(log_b a) · log n)
Case 3: f(n) = Ω(n^(log_b a + ε))       → T(n) = Θ(f(n))   [+ regularity]
```

---

*Q&A prepared for Chapter 1: Analyzing Algorithms — BITS Pilani*
*References: Goodrich & Tamassia (2006) Ch 1.1, 1.2; CLRS (2009) Ch 4.3, 4.4, 4.5*
