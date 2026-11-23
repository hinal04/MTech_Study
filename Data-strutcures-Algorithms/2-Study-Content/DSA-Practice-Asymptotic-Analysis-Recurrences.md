# DSA Practice: Asymptotic Analysis & Recurrence Relations

> BITS Pilani M.Tech — Data Structures & Algorithms (CSI ZG525)
> 
> **Focus:** Asymptotic Notation, Master Theorem, Recurrence Solving Methods
> 
> **Difficulty Levels:** Beginner → Intermediate → Advanced

---

## Table of Contents

1. [Big-O, Big-Omega, Theta Notation](#1-big-o-big-omega-theta-notation)
   - Definitions & Visual Comparisons
   - Practice Problems (10 problems)
2. [Master Theorem](#2-master-theorem)
   - Theorem Statement & Cases
   - Practice Problems (8 problems)
3. [Recurrence Relations: Substitution Method](#3-recurrence-relations-substitution-method)
   - Method Explanation & Examples
   - Practice Problems (6 problems)
4. [Recurrence Relations: Recursion Tree Method](#4-recurrence-relations-recursion-tree-method)
   - Method Explanation & Visual Examples
   - Practice Problems (8 problems)
5. [Combined Practice (All Methods)](#5-combined-practice-all-methods)

---

## 1. Big-O, Big-Omega, Theta Notation

### 1.1 Definitions & Formal Notations

#### Big-O Notation (Upper Bound)

**Definition:** O(g(n)) is the set of functions f(n) such that there exist positive constants c and n₀ where:

```
0 ≤ f(n) ≤ c·g(n) for all n ≥ n₀
```

**Meaning:** f(n) grows **at most as fast** as g(n) (up to a constant factor). Used for **worst-case** analysis.

**Example:** 
- 3n² + 2n + 5 = O(n²)
  - Here, c = 4, n₀ = 1
  - Proof: 3n² + 2n + 5 ≤ 4n² for n ≥ 1

#### Big-Omega Notation (Lower Bound)

**Definition:** Ω(g(n)) is the set of functions f(n) such that there exist positive constants c and n₀ where:

```
0 ≤ c·g(n) ≤ f(n) for all n ≥ n₀
```

**Meaning:** f(n) grows **at least as fast** as g(n) (up to a constant factor). Used for **best-case** analysis.

**Example:**
- 3n² + 2n + 5 = Ω(n²)
  - Here, c = 1, n₀ = 1
  - Proof: 3n² ≤ 3n² + 2n + 5 for n ≥ 1

#### Theta Notation (Tight Bound)

**Definition:** Θ(g(n)) is the set of functions f(n) such that there exist positive constants c₁, c₂, and n₀ where:

```
0 ≤ c₁·g(n) ≤ f(n) ≤ c₂·g(n) for all n ≥ n₀
```

**Meaning:** f(n) grows **exactly as fast** as g(n) (up to constant factors). Used when upper and lower bounds match.

**Example:**
- 3n² + 2n + 5 = Θ(n²)
  - Here, c₁ = 1, c₂ = 4, n₀ = 1

### 1.2 Comparison of Growth Rates

| Notation | Growth Rate | Intuition | Complexity Class |
|----------|------------|-----------|------------------|
| O(1) | Constant | Does not depend on input size | Best |
| O(log n) | Logarithmic | Dividing problem by 2 each time (binary search) | Very Good |
| O(n) | Linear | Processing each element once | Good |
| O(n log n) | Linearithmic | Divide-and-conquer sorting | Good |
| O(n²) | Quadratic | Nested loops (bubble sort) | Acceptable |
| O(n³) | Cubic | Triple nested loops | Slow |
| O(2ⁿ) | Exponential | Generating all subsets | Very Slow |
| O(n!) | Factorial | Generating all permutations | Infeasible |

### 1.3 Visual Representation

```
Time
 |     O(n!)
 |        /
 |       / O(2ⁿ)
 |      /  /
 |     /  / O(n³)
 |    /  /  /
 |   /  /  / O(n²)
 |  /  /  /  /
 | /  /  /  / O(n log n)
 |/  /  /  /  /
 +--+--+--+--+-------> n
    O(n), O(log n), O(1)
```

### 1.4 Practice Problems: Big-O, Big-Omega, Theta

#### **Problem 1.1 (Beginner)**
Classify the following time complexities using Big-O notation:

a) 5n + 3  
b) 2n² + n + 1  
c) n³ + n² + n  
d) 10  
e) n log n + n

**Solutions:**
- a) O(n)
- b) O(n²)
- c) O(n³)
- d) O(1)
- e) O(n log n)

---

#### **Problem 1.2 (Beginner)**
For the function f(n) = 4n² + 3n + 2, prove that:

a) f(n) = O(n²)  
b) f(n) = Ω(n²)  
c) f(n) = Θ(n²)

**Solutions:**

a) **Prove f(n) = O(n²):**
   - Need to find c and n₀ such that: 4n² + 3n + 2 ≤ c·n²
   - Choose c = 5, n₀ = 2
   - Check: 4n² + 3n + 2 ≤ 5n²
   - At n = 2: 16 + 6 + 2 = 24 ≤ 20 (FALSE, try n₀ = 3)
   - At n = 3: 36 + 9 + 2 = 47 ≤ 45 (FALSE, try c = 6)
   - With c = 6: At n = 2: 24 ≤ 24 ✓
   - **Answer:** c = 6, n₀ = 2

b) **Prove f(n) = Ω(n²):**
   - Need to find c and n₀ such that: c·n² ≤ 4n² + 3n + 2
   - Choose c = 1, n₀ = 1
   - Check: n² ≤ 4n² + 3n + 2 (Always true for n ≥ 1) ✓
   - **Answer:** c = 1, n₀ = 1

c) **Prove f(n) = Θ(n²):**
   - From (a) and (b): 1·n² ≤ 4n² + 3n + 2 ≤ 6·n² for n ≥ 2
   - **Answer:** c₁ = 1, c₂ = 6, n₀ = 2

---

#### **Problem 1.3 (Intermediate)**
Arrange the following functions in increasing order of growth:

- f₁(n) = n log n
- f₂(n) = n√n
- f₃(n) = n² / log n
- f₄(n) = n log² n
- f₅(n) = 2ⁿ

**Solution:**

Simplify and compare:
- f₁(n) = n log n
- f₂(n) = n^(3/2)
- f₃(n) = n² / log n
- f₄(n) = n log² n
- f₅(n) = 2ⁿ

Growth order:
1. **n log n** (fastest polynomial-like)
2. **n^(3/2)** (between n and n²)
3. **n log² n** (n multiplied by log² n)
4. **n² / log n** (n² reduced by log factor, still grows as n²)
5. **2ⁿ** (exponential, fastest)

**Answer:** f₁ < f₂ < f₄ < f₃ < f₅

---

#### **Problem 1.4 (Intermediate)**
Prove or disprove: n³ = O(2ⁿ)

**Solution:**

We need to check if there exist constants c and n₀ such that:
```
n³ ≤ c·2ⁿ for all n ≥ n₀
```

Check for large n:
- At n = 10: 10³ = 1,000 vs 2¹⁰ = 1,024 ✓
- At n = 20: 20³ = 8,000 vs 2²⁰ = 1,048,576 ✓
- At n = 100: 100³ = 1,000,000 vs 2¹⁰⁰ ≈ 1.27 × 10³⁰ ✓

Exponential functions eventually dominate polynomial functions.

**Answer:** TRUE — n³ = O(2ⁿ) with c = 1, n₀ = 2

---

#### **Problem 1.5 (Intermediate)**
Prove or disprove: 2ⁿ = O(n!)

**Solution:**

We need: 2ⁿ ≤ c·n! for all n ≥ n₀

Check:
- At n = 1: 2¹ = 2 vs 1! = 1 (2 > 1, fails)
- At n = 2: 2² = 4 vs 2! = 2 (4 > 2, fails)
- At n = 3: 2³ = 8 vs 3! = 6 (8 > 6, fails)
- At n = 4: 2⁴ = 16 vs 4! = 24 ✓
- At n = 5: 2⁵ = 32 vs 5! = 120 ✓

Factorial grows faster than exponential. With c = 1, n₀ = 4:

**Answer:** TRUE — 2ⁿ = O(n!) with c = 1, n₀ = 4

---

#### **Problem 1.6 (Beginner)**
Determine if these statements are true or false:

a) n² = O(n)  
b) n = O(n²)  
c) n = Θ(n²)  
d) n² + n = Θ(n²)

**Solutions:**
- a) **FALSE** — n² grows faster than n
- b) **TRUE** — n ≤ 1·n² for n ≥ 1
- c) **FALSE** — n ≠ Θ(n²) because we can't lower bound n by n²
- d) **TRUE** — Both n² and n² + n grow as Θ(n²)

---

#### **Problem 1.7 (Intermediate)**
For the function f(n) = 5n² log n + 10n³, determine:

a) Big-O  
b) Big-Omega  
c) Theta

**Solution:**

a) **Big-O:** Dominant term is 10n³
   - f(n) = O(n³)

b) **Big-Omega:** Lower bound is also 10n³
   - f(n) = Ω(n³)

c) **Theta:** Both bounds are n³
   - f(n) = Θ(n³)

---

#### **Problem 1.8 (Advanced)**
Prove that log(n!) = Θ(n log n)

**Solution:**

Using Stirling's approximation: n! ≈ √(2πn) · (n/e)ⁿ

```
log(n!) ≈ log(√(2πn)) + n·log(n/e)
        = (1/2)log(2πn) + n·log(n) - n·log(e)
        = (1/2)log(2πn) + n·log(n) - n·log(e)
```

The dominant term is **n log n**.

**Answer:** Θ(n log n)

---

#### **Problem 1.9 (Intermediate)**
Compare f(n) = 2^(log n) and g(n) = n

**Solution:**

2^(log n) = 2^(log₂ n) = n (by definition of logarithm)

**Answer:** f(n) = g(n) = Θ(n)

---

#### **Problem 1.10 (Intermediate)**
Which is true: 2^(n+1) = O(2ⁿ) or 2^(n+1) = Θ(2ⁿ)?

**Solution:**

2^(n+1) = 2 · 2ⁿ

So we need: 2 · 2ⁿ ≤ c · 2ⁿ

This gives: 2 ≤ c, which is true for c = 2, n₀ = 1

**Answer:** Both are true. Specifically, 2^(n+1) = Θ(2ⁿ) (they differ by only a constant factor)

---

---

## 2. Master Theorem

### 2.1 Statement & Three Cases

The Master Theorem applies to recurrences of the form:

```
T(n) = a·T(n/b) + f(n)

where:
- a ≥ 1: number of subproblems
- b > 1: factor by which problem size reduces
- f(n): cost of dividing and combining subproblems
```

#### **Case 1: f(n) = O(n^(log_b a - ε)) for some ε > 0**

```
T(n) = Θ(n^(log_b a))
```

**Intuition:** Subproblem cost dominates. Recursion tree grows exponentially.

---

#### **Case 2: f(n) = Θ(n^(log_b a) · log^k n) for k ≥ 0**

```
T(n) = Θ(n^(log_b a) · log^(k+1) n)
```

**Intuition:** Subproblem cost and combining cost are balanced.

---

#### **Case 3: f(n) = Ω(n^(log_b a + ε)) for some ε > 0, and a·f(n/b) ≤ c·f(n)**

```
T(n) = Θ(f(n))
```

**Intuition:** Dividing/combining cost dominates.

---

### 2.2 Visual Summary Table

| Case | Condition | Result |
|------|-----------|--------|
| **Case 1** | f(n) is polynomially smaller than n^(log_b a) | T(n) = Θ(n^(log_b a)) |
| **Case 2** | f(n) = Θ(n^(log_b a) · log^k n) | T(n) = Θ(n^(log_b a) · log^(k+1) n) |
| **Case 3** | f(n) is polynomially larger than n^(log_b a) | T(n) = Θ(f(n)) |

---

### 2.3 Practice Problems: Master Theorem

#### **Problem 2.1 (Beginner)**
Apply Master Theorem to: T(n) = 2·T(n/2) + n

**Solution:**

Identify parameters:
- a = 2
- b = 2
- f(n) = n
- n^(log_b a) = n^(log₂ 2) = n¹ = n

Compare f(n) with n^(log_b a):
- f(n) = n
- n^(log_b a) = n
- They are equal!

This is **Case 2** with k = 0:
```
T(n) = Θ(n^1 · log^(0+1) n) = Θ(n log n)
```

**Answer:** T(n) = Θ(n log n) [Merge Sort, Binary Search Tree Insert]

---

#### **Problem 2.2 (Beginner)**
Apply Master Theorem to: T(n) = 4·T(n/2) + n

**Solution:**

- a = 4, b = 2, f(n) = n
- n^(log_b a) = n^(log₂ 4) = n²
- f(n) = n = O(n^(2-ε)) for ε = 1

This is **Case 1**:
```
T(n) = Θ(n²)
```

**Answer:** T(n) = Θ(n²)

---

#### **Problem 2.3 (Beginner)**
Apply Master Theorem to: T(n) = 2·T(n/2) + n²

**Solution:**

- a = 2, b = 2, f(n) = n²
- n^(log_b a) = n¹ = n
- f(n) = n² = Ω(n^(1+ε)) for ε = 1
- Check regularity: a·f(n/b) = 2·(n/2)² = 2·n²/4 = n²/2 ≤ c·n² ✓

This is **Case 3**:
```
T(n) = Θ(n²)
```

**Answer:** T(n) = Θ(n²)

---

#### **Problem 2.4 (Intermediate)**
Apply Master Theorem to: T(n) = 9·T(n/3) + n

**Solution:**

- a = 9, b = 3, f(n) = n
- n^(log_b a) = n^(log₃ 9) = n²
- f(n) = n = O(n^(2-ε)) for ε = 1

This is **Case 1**:
```
T(n) = Θ(n²)
```

**Answer:** T(n) = Θ(n²)

---

#### **Problem 2.5 (Intermediate)**
Apply Master Theorem to: T(n) = T(n/2) + 1

**Solution:**

- a = 1, b = 2, f(n) = 1
- n^(log_b a) = n^(log₂ 1) = n⁰ = 1
- f(n) = 1 = Θ(1 · log⁰ n)

This is **Case 2** with k = 0:
```
T(n) = Θ(1 · log¹ n) = Θ(log n)
```

**Answer:** T(n) = Θ(log n) [Binary Search]

---

#### **Problem 2.6 (Intermediate)**
Apply Master Theorem to: T(n) = 16·T(n/4) + n²

**Solution:**

- a = 16, b = 4, f(n) = n²
- n^(log_b a) = n^(log₄ 16) = n²
- f(n) = n² = Θ(n² · log⁰ n)

This is **Case 2** with k = 0:
```
T(n) = Θ(n² · log¹ n) = Θ(n² log n)
```

**Answer:** T(n) = Θ(n² log n)

---

#### **Problem 2.7 (Advanced)**
Apply Master Theorem to: T(n) = 8·T(n/4) + n log n

**Solution:**

- a = 8, b = 4, f(n) = n log n
- n^(log_b a) = n^(log₄ 8) = n^(3/2)
- f(n) = n log n vs n^(3/2): n log n = O(n^(1.5-ε)) for ε = 0.5? 

Actually, n log n = o(n^(3/2)) since n log n grows slower than n^(3/2)

This is **Case 1**:
```
T(n) = Θ(n^(3/2))
```

**Answer:** T(n) = Θ(n^(3/2))

---

#### **Problem 2.8 (Advanced)**
Apply Master Theorem to: T(n) = 2·T(n/4) + √n

**Solution:**

- a = 2, b = 4, f(n) = √n = n^(1/2)
- n^(log_b a) = n^(log₄ 2) = n^(1/2)
- f(n) = n^(1/2) = Θ(n^(1/2) · log⁰ n)

This is **Case 2** with k = 0:
```
T(n) = Θ(n^(1/2) · log¹ n) = Θ(√n · log n)
```

**Answer:** T(n) = Θ(√n log n)

---

---

## 3. Recurrence Relations: Substitution Method

### 3.1 Method Overview

The **substitution method** (also called **guess-and-check**) works in two steps:

1. **Guess** the form of the solution
2. **Prove** the guess by mathematical induction

#### Step 1: Make an Educated Guess

Common patterns:
- Linear recurrences → linear or logarithmic solutions
- Tree-like recurrences → polynomial or exponential solutions
- Divide-and-conquer → log n or n log n solutions

#### Step 2: Prove by Induction

**Base case:** Show the guess holds for small n  
**Inductive step:** Assume T(k) matches the guess for k < n, then show T(n) also matches

---

### 3.2 Practice Problems: Substitution Method

#### **Problem 3.1 (Beginner)**
Solve T(n) = T(n-1) + 1 with T(1) = 1 using substitution method.

**Solution:**

**Step 1: Guess**
- T(1) = 1
- T(2) = T(1) + 1 = 2
- T(3) = T(2) + 1 = 3
- Pattern: **Guess T(n) = n**

**Step 2: Prove by Induction**

*Base case:* T(1) = 1 ✓

*Inductive step:* Assume T(k) = k for k < n. Show T(n) = n:
```
T(n) = T(n-1) + 1
     = (n-1) + 1          [by inductive hypothesis]
     = n                  ✓
```

**Answer:** T(n) = Θ(n)

---

#### **Problem 3.2 (Beginner)**
Solve T(n) = 2·T(n/2) + 1 with T(1) = 1 using substitution method.

**Solution:**

**Step 1: Guess**
- T(1) = 1
- T(2) = 2·T(1) + 1 = 2·1 + 1 = 3
- T(4) = 2·T(2) + 1 = 2·3 + 1 = 7
- T(8) = 2·T(4) + 1 = 2·7 + 1 = 15
- Pattern: 1, 3, 7, 15, ... = 2ⁿ - 1
- **Guess T(n) = 2n - 1** (for powers of 2)

**Step 2: Prove by Induction** (for n = 2^k)

*Base case:* T(1) = 1 = 2¹ - 1 ✓

*Inductive step:* Assume T(n) = 2n - 1. Show T(2n) = 4n - 1:
```
T(2n) = 2·T(n) + 1
      = 2·(2n - 1) + 1     [by inductive hypothesis]
      = 4n - 2 + 1
      = 4n - 1             ✓
```

**Answer:** T(n) = Θ(n)

---

#### **Problem 3.3 (Intermediate)**
Solve T(n) = 2·T(n/2) + n with T(1) = 1 using substitution method.

**Solution:**

**Step 1: Guess**
- T(1) = 1
- T(2) = 2·T(1) + 2 = 2·1 + 2 = 4
- T(4) = 2·T(2) + 4 = 2·4 + 4 = 12
- T(8) = 2·T(4) + 8 = 2·12 + 8 = 32
- Pattern: 1, 4, 12, 32, ...
- Let's guess **T(n) = n log₂ n**

**Step 2: Prove by Induction** (for n = 2^k)

*Base case:* T(1) = 1 · log₂ 1 = 0 (doesn't match, T(1) = 1)

Let's adjust: **Guess T(n) = n log₂ n + n**

*Base case:* T(2) = 2·log₂ 2 + 2 = 2·1 + 2 = 4 ✓

*Inductive step:* Assume T(n) = n log₂ n + n. Show T(2n) = 2n log₂(2n) + 2n:
```
T(2n) = 2·T(n) + 2n
      = 2·(n log₂ n + n) + 2n      [by hypothesis]
      = 2n log₂ n + 2n + 2n
      = 2n log₂ n + 4n
      = 2n(log₂ n + 1) + 2n
      = 2n·log₂(2n) + 2n           ✓
```

**Answer:** T(n) = Θ(n log n)

---

#### **Problem 3.4 (Intermediate)**
Prove that T(n) = 2·T(n/2) + n satisfies T(n) = O(n log n).

**Solution:**

We want to prove: T(n) ≤ cn log n for some constants c and n₀

**Induction:**

*Base case:* T(2) = 2·T(1) + 2 = 2·1 + 2 = 4
- Need: 4 ≤ c·2·log 2 = 2c
- So c ≥ 2. Choose c = 2.
- Check: 4 ≤ 2·2·1 = 4 ✓

*Inductive step:* Assume T(n/2) ≤ c·(n/2)·log(n/2). Show T(n) ≤ cn log n:
```
T(n) = 2·T(n/2) + n
     ≤ 2·c·(n/2)·log(n/2) + n         [by hypothesis]
     = cn·log(n/2) + n
     = cn·(log n - 1) + n
     = cn log n - cn + n
     ≤ cn log n                       [if cn ≥ n, i.e., c ≥ 1]
```

**Answer:** T(n) = O(n log n) ✓

---

#### **Problem 3.5 (Intermediate)**
Solve T(n) = 3·T(n/4) + n with T(1) = 1.

**Solution:**

**Step 1: Guess**

Using Master Theorem as a guide:
- n^(log_b a) = n^(log₄ 3) ≈ n^0.792
- f(n) = n = Ω(n^0.792)

This suggests Case 3: **T(n) = Θ(n)**

**Step 2: Prove by Induction**

Guess: T(n) = cn for some c

*Base case:* T(1) = 1, so need c ≥ 1. Choose c = 1.

*Inductive step:* Assume T(n/4) ≤ c·(n/4). Show T(n) ≤ cn:
```
T(n) = 3·T(n/4) + n
     ≤ 3·c·(n/4) + n          [by hypothesis]
     = (3c/4)·n + n
     = ((3c/4) + 1)·n
```

For this to be ≤ cn, we need:
```
(3c/4) + 1 ≤ c
1 ≤ c/4
c ≥ 4
```

**Answer:** T(n) = Θ(n) with c = 4

---

#### **Problem 3.6 (Advanced)**
Solve T(n) = T(n-1) + log n with T(1) = 1.

**Solution:**

**Step 1: Guess**
```
T(n) = T(n-1) + log n
     = T(n-2) + log(n-1) + log n
     = T(1) + log 2 + log 3 + ... + log n
     = 1 + Σ(i=2 to n) log i
```

Using the approximation: Σ(i=1 to n) log i ≈ n log n

**Guess: T(n) = Θ(n log n)**

**Step 2: Prove Upper Bound**

Assume T(n) ≤ cn log n. Show T(n+1) ≤ c(n+1)log(n+1):
```
T(n+1) = T(n) + log(n+1)
       ≤ cn log n + log(n+1)          [by hypothesis]
```

For large n, cn log n grows much faster than log(n+1), so the bound holds.

**Answer:** T(n) = Θ(n log n)

---

---

## 4. Recurrence Relations: Recursion Tree Method

### 4.1 Method Overview

The **recursion tree method** visualizes the recurrence as a tree:
- Each node represents a subproblem
- Edge labels show cost of subproblem
- Level i shows all subproblems of size n/b^i
- Total cost = sum of costs at all levels

#### General Steps:

1. Draw the recursion tree
2. Determine: number of levels, cost per level, total number of leaves
3. Sum costs across all levels

---

### 4.2 Practice Problems: Recursion Tree Method

#### **Problem 4.1 (Beginner)**
Solve T(n) = 2·T(n/2) + n using recursion tree method.

**Solution:**

**Step 1: Draw the Tree**

```
Level 0:                  n              Cost: n
                         / \
Level 1:               n/2   n/2         Cost: 2·(n/2) = n
                      /  \  /  \
Level 2:            n/4  n/4 n/4 n/4    Cost: 4·(n/4) = n
                   /|    |\ /|    |\
Level 3:        ...                ...   Cost: 8·(n/8) = n
```

**Step 2: Analyze Tree Structure**

- Number of levels: log₂ n + 1 (including level 0)
- Cost per level: n
- Number of leaves: 2^(log n) = n

**Step 3: Total Cost**

```
T(n) = Cost of all levels + Cost of leaves
     = n·(log n) + n·1              [leaves have cost 1 each]
     = n·(log n + 1)
     = Θ(n log n)
```

**Answer:** T(n) = Θ(n log n)

---

#### **Problem 4.2 (Beginner)**
Solve T(n) = 4·T(n/2) + n using recursion tree method.

**Solution:**

**Step 1: Draw the Tree**

```
Level 0:                  n                        Cost: n
                    / /  | \  \ \
Level 1:        n/2  n/2  n/2  n/2               Cost: 4·(n/2) = 2n
              /|  \|  |\ /|  |\ /|
Level 2:   ...subproblems of size n/4...         Cost: 16·(n/4) = 4n
```

**Step 2: Analyze**

- At level i: 4^i subproblems of size n/2^i
- Cost at level i: 4^i · (n/2^i) = n·(4/2)^i = n·2^i
- Number of levels: log₂ n

**Step 3: Total Cost**

```
T(n) = Σ(i=0 to log n) n·2^i
     = n · Σ(i=0 to log n) 2^i
     = n · (2^(log n + 1) - 1)           [geometric series]
     = n · (2n - 1)
     = Θ(n²)
```

**Answer:** T(n) = Θ(n²)

---

#### **Problem 4.3 (Intermediate)**
Solve T(n) = 3·T(n/3) + n using recursion tree method.

**Solution:**

**Step 1: Analyze Tree**

```
Level 0:              n                    Cost: n
                  /   |   \
Level 1:        n/3  n/3  n/3             Cost: 3·(n/3) = n
              / | \ / | \ / | \
Level 2:    ...subproblems...             Cost: 9·(n/9) = n
```

**Step 2: Pattern**

- At level i: 3^i subproblems of size n/3^i
- Cost at level i: 3^i · (n/3^i) = n
- Number of levels: log₃ n + 1

**Step 3: Total Cost**

```
T(n) = n·(log₃ n + 1) + n·(base case cost)
     = Θ(n log n)
```

**Answer:** T(n) = Θ(n log n)

---

#### **Problem 4.4 (Intermediate)**
Solve T(n) = 2·T(n/2) + n² using recursion tree method.

**Solution:**

**Step 1: Analyze Tree**

```
Level 0:              n²                       Cost: n²
                    /   \
Level 1:        (n/2)² (n/2)²                Cost: 2·(n/2)² = n²/2
                /  \    /  \
Level 2:    (n/4)²...                        Cost: 4·(n/4)² = n²/4
```

**Step 2: Pattern**

- At level i: 2^i subproblems of size n/2^i
- Cost at level i: 2^i · (n/2^i)² = 2^i · n²/4^i = n²/2^i
- Number of levels: log₂ n + 1

**Step 3: Total Cost**

```
T(n) = Σ(i=0 to log n) n²/2^i
     = n² · Σ(i=0 to log n) (1/2)^i
     = n² · (1 - (1/2)^(log n + 1)) / (1 - 1/2)    [geometric series]
     ≈ n² · 2
     = Θ(n²)
```

**Answer:** T(n) = Θ(n²)

---

#### **Problem 4.5 (Intermediate)**
Solve T(n) = T(n/3) + T(2n/3) + n using recursion tree method.

**Solution:**

**Step 1: Visualize Tree**

```
Level 0:              n                   Cost: n
                    /   \
Level 1:         n/3     2n/3             Cost: n/3 + 2n/3 = n
                /  \    /   \
Level 2:    n/9  2n/9 2n/9  4n/9          Cost: n
```

**Step 2: Observe**

- Cost at each level: n
- Tree is not balanced (left branch has depth log₃ n, right has depth log₁.₅ n)
- Maximum depth: log₁.₅ n (when we keep multiplying by 2/3)

**Step 3: Total Cost**

```
T(n) = n · log₁.₅ n (approximate)
     = Θ(n log n)
```

**Answer:** T(n) = Θ(n log n)

---

#### **Problem 4.6 (Advanced)**
Solve T(n) = 4·T(n/2) + n² using recursion tree method and compare with Master Theorem.

**Solution:**

**Recursion Tree Method:**

```
Level 0:              n²                 Cost: n²
                  / / | \ \ \
Level 1:       (n/2)² for each          Cost: 4·(n/2)² = n²
                (4 subproblems)
Level 2:       ...                       Cost: 16·(n/4)² = n²
```

**Pattern:**
- At level i: 4^i subproblems of size n/2^i
- Cost at level i: 4^i · (n/2^i)² = 4^i · n²/4^i = n²
- Number of levels: log₂ n + 1

**Total Cost:**
```
T(n) = n² · (log n + 1)
     = Θ(n² log n)
```

**Master Theorem Check:**
- a = 4, b = 2, f(n) = n²
- n^(log_b a) = n²
- f(n) = n² = Θ(n² · log⁰ n)
- Case 2: T(n) = Θ(n² · log¹ n) = Θ(n² log n) ✓

**Answer:** T(n) = Θ(n² log n)

---

#### **Problem 4.7 (Advanced)**
Solve T(n) = 2·T(n/2) + n log n using recursion tree method.

**Solution:**

**Step 1: Analyze Tree**

```
Level 0:              n log n              Cost: n log n
                    /      \
Level 1:       (n/2)log(n/2)  ...         Cost: 2·(n/2)·log(n/2)
             (each subproblem)
```

**Step 2: Cost at Each Level**

At level i:
- 2^i subproblems of size n/2^i
- Each costs (n/2^i)·log(n/2^i)
- Total: 2^i · (n/2^i) · log(n/2^i) = n · log(n/2^i) = n·(log n - i)

**Step 3: Sum Across Levels**

```
T(n) = Σ(i=0 to log n) n·(log n - i)
     = n·Σ(i=0 to log n) (log n - i)
     = n·[log n·log n - (0+1+2+...+log n)]
     = n·[log² n - log n·(log n + 1)/2]
     = n·[log² n/2 + log n/2]
     = Θ(n log² n)
```

**Answer:** T(n) = Θ(n log² n)

---

#### **Problem 4.8 (Advanced)**
Solve T(n) = T(n/2) + T(n/4) + T(n/8) + n using recursion tree method.

**Solution:**

**Step 1: Analyze Tree**

At level i, we have subproblems of total size:
```
Level 0: n
Level 1: n/2 + n/4 + n/8 = 7n/8
Level 2: (7/8)·(7n/8) = (7/8)²·n
```

**Step 2: Pattern**

Cost at level i = (7/8)^i · n

Number of levels ≈ log₂ n (deepest branch is T(n/8)^k)

**Step 3: Total Cost**

```
T(n) = n · Σ(i=0 to log₂ n) (7/8)^i
     = n · [1 - (7/8)^(log n + 1)] / (1 - 7/8)
     = n · 8 · [1 - (7/8)^(log n)]
     ≈ 8n                          [since (7/8)^(log n) → 0]
     = Θ(n)
```

**Answer:** T(n) = Θ(n)

---

---

## 5. Combined Practice (All Methods)

### 5.1 Comparison Exercise

**Problem 5.1 (Advanced)**

Solve **T(n) = 4·T(n/2) + n²** using all three methods:

a) **Master Theorem**  
b) **Substitution Method**  
c) **Recursion Tree Method**

Verify that all three give the same answer.

**Solutions:**

#### a) Master Theorem

- a = 4, b = 2, f(n) = n²
- n^(log_b a) = n^(log₂ 4) = n²
- f(n) = Θ(n²)

**Case 2:** T(n) = Θ(n² log n)

---

#### b) Substitution Method

Guess: T(n) = cn² log n

*Prove:* T(n) ≤ cn² log n

```
T(n) = 4·T(n/2) + n²
     ≤ 4·c·(n/2)²·log(n/2) + n²      [by hypothesis]
     = 4·c·(n²/4)·log(n/2) + n²
     = cn²·(log n - 1) + n²
     = cn² log n - cn² + n²
```

For this to be ≤ cn² log n, need: n² ≤ cn², so c ≥ 1.

Choose c = 2. Then: cn² log n - cn² + n² = 2n² log n - 2n² + n² = 2n² log n - n² < 2n² log n ✓

**Result:** T(n) = Θ(n² log n)

---

#### c) Recursion Tree Method

```
Level 0:             n²              Cost: n²
                 /  /  \  \
Level 1:      (n/2)² × 4             Cost: 4·(n/2)² = n²
                  / \ / \
Level 2:    (n/4)² × 16               Cost: 16·(n/4)² = n²
```

- Cost per level: n²
- Number of levels: log₂ n + 1

```
T(n) = n² · (log n + 1)
     = Θ(n² log n)
```

---

**All three methods confirm:** T(n) = Θ(n² log n) ✓

---

### 5.2 Real-World Examples

#### **Sorting Algorithms**

| Algorithm | Recurrence | Solution | Method |
|-----------|-----------|----------|--------|
| Merge Sort | T(n) = 2T(n/2) + n | Θ(n log n) | Master Th. |
| Quick Sort (avg) | T(n) = T(n/2) + T(n/2) + n | Θ(n log n) | Master Th. |
| Insertion Sort | T(n) = T(n-1) + n | Θ(n²) | Substitution |

---

### 5.3 Final Summary Table

| Recurrence | Solution | Typical Algorithm |
|-----------|----------|-------------------|
| T(n) = 2T(n/2) + n | Θ(n log n) | Merge Sort |
| T(n) = 4T(n/2) + n | Θ(n²) | Naive Matrix Mult |
| T(n) = T(n-1) + n | Θ(n²) | Insertion Sort |
| T(n) = T(n/2) + 1 | Θ(log n) | Binary Search |
| T(n) = 2T(n/2) + n² | Θ(n²) | Slow Divide-and-Conquer |
| T(n) = 3T(n/3) + n | Θ(n log n) | Divide-and-Conquer |

---

## Answer Key Summary

All solutions with detailed proofs are provided above in each section. Key concepts:

✅ **Big-O/Omega/Theta:** Used to classify algorithm complexity  
✅ **Master Theorem:** Fast way to solve divide-and-conquer recurrences  
✅ **Substitution:** Guess and prove by induction  
✅ **Recursion Tree:** Visualize and sum level costs  

---

## Tips for Exam

1. **Identify the pattern first** (linear? divide-and-conquer? exponential?)
2. **For divide-and-conquer → Try Master Theorem first**
3. **For linear recurrences → Substitution often easiest**
4. **For complex cases → Recursion tree provides good intuition**
5. **Always verify** your answer using a different method if time permits

---

*Last Updated: November 2026*  
*For BITS Pilani M.Tech CSI ZG525 — Data Structures & Algorithms*
