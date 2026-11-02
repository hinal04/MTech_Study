# Data Structures & Algorithms - Post-Semester Questions & Answers
## CSI ZG528 - Chapter 4 (AVL Trees & Advanced Topics)

---

## Q1. AVL Tree Rotations in Detail [3.5 Marks]

### Question:
Provide detailed explanations of all four AVL rotation cases. For each case (LL, RR, LR, RL): (i) describe when it occurs, (ii) draw before/after diagrams, (iii) provide pseudocode for rotation, (iv) show practical example with node values.

### Answer:

**LL Rotation (Left-Left Case) [0.875 marks]:**

**When:** Inserted in left subtree of left child; balance factor becomes -2 with left child having -1
```
Before LL Rotation:          After LL Rotation:
      z (BF=-2)                   y (BF=0)
     /                           / \
    y (BF=-1)        →          x   z
   /
  x
```

**Pseudocode:**
```
rotateRight(z):
    y = z.left
    z.left = y.right
    y.right = z
    z.height = max(height(z.left), height(z.right)) + 1
    y.height = max(height(y.left), height(y.right)) + 1
    return y
```

**Example:**
```
Insert 1, 2, 3:
Step 1: Insert 1        1 (BF=0)

Step 2: Insert 2        1 (BF=-1)
                         \
                          2

Step 3: Insert 3        1 (BF=-2) ← UNBALANCED
                         \
                          2
                           \
                            3
Apply LL Rotation (right rotate at 1):
                        2 (BF=0)
                       / \
                      1   3
```

**RR Rotation (Right-Right Case) [0.875 marks]:**

**When:** Inserted in right subtree of right child; balance factor becomes +2 with right child having +1
```
Before RR Rotation:          After RR Rotation:
    x (BF=+2)                   y (BF=0)
     \                         / \
      y (BF=+1)     →         x   z
       \
        z
```

**Pseudocode:**
```
rotateLeft(x):
    y = x.right
    x.right = y.left
    y.left = x
    x.height = max(height(x.left), height(x.right)) + 1
    y.height = max(height(y.left), height(y.right)) + 1
    return y
```

**Example:**
```
Insert 3, 2, 1:
Step 1: Insert 3        3 (BF=0)

Step 2: Insert 2        3 (BF=+1)
                       /
                      2

Step 3: Insert 1        3 (BF=+2) ← UNBALANCED
                       /
                      2
                     /
                    1
Apply RR Rotation (left rotate at 3):
                        2 (BF=0)
                       / \
                      1   3
```

**LR Rotation (Left-Right Case) [0.875 marks]:**

**When:** Inserted in right subtree of left child; balance factor becomes -2 with left child having +1
```
Before LR:              After Left Rotation:    After Right Rotation:
    z (BF=-2)              z (BF=-2)                y (BF=0)
   /                       /                       / \
  x (BF=+1)      →        y            →          x   z
   \                     /
    y                   x
```

**Process:**
1. Left rotate on left child: x becomes left child with y as its right child
2. Right rotate on parent: y becomes new root

**Example:**
```
Insert 10, 5, 7:
Step 1: Insert 10       10 (BF=0)

Step 2: Insert 5        10 (BF=+1)
                       /
                      5

Step 3: Insert 7        10 (BF=-2) ← UNBALANCED (LR case)
                       /
                      5 (BF=+1)
                       \
                        7

Apply Left Rotation on 5:
                    10 (BF=-2)
                   /
                  7 (BF=+1)
                 /
                5

Apply Right Rotation on 10:
                    7 (BF=0)
                   / \
                  5   10
```

**RL Rotation (Right-Left Case) [0.875 marks]:**

**When:** Inserted in left subtree of right child; balance factor becomes +2 with right child having -1
```
Before RL:              After Right Rotation:   After Left Rotation:
    x (BF=+2)              x (BF=+2)                y (BF=0)
     \                       \                      / \
      z (BF=-1)     →         y            →       x   z
     /                         \
    y                           z
```

**Process:**
1. Right rotate on right child
2. Left rotate on parent

**Example:**
```
Insert 5, 10, 7:
Step 1: Insert 5        5 (BF=0)

Step 2: Insert 10       5 (BF=-1)
                         \
                         10

Step 3: Insert 7        5 (BF=+2) ← UNBALANCED (RL case)
                         \
                         10 (BF=-1)
                        /
                       7

Apply Right Rotation on 10:
                    5 (BF=+2)
                     \
                      7 (BF=+1)
                       \
                       10

Apply Left Rotation on 5:
                    7 (BF=0)
                   / \
                  5   10
```

---

## Q2. AVL Insertion Algorithm [3.5 Marks]

### Question:
Provide complete AVL insertion algorithm. Include: (i) basic BST insertion pseudocode, (ii) balance factor calculation, (iii) balance checking and rotation triggering logic, (iv) how to determine which rotation type to apply, (v) trace through example: insert 50, 25, 75, 10, 30, 60, 80, 5 into empty AVL tree.

### Answer:

**AVL Insertion Algorithm [1.5 marks]:**

```
insert(node, value):
    if (node == NULL)
        return new Node(value)
    
    // Standard BST insertion
    if (value < node.data)
        node.left = insert(node.left, value)
    else if (value > node.data)
        node.right = insert(node.right, value)
    else
        return node  // Duplicate; no insertion
    
    // Update height
    node.height = 1 + max(height(node.left), height(node.right))
    
    // Calculate balance factor
    bf = balanceFactor(node)
    
    // Perform rotations if unbalanced
    // LL Case
    if (bf < -1 AND balanceFactor(node.left) <= 0)
        return rotateRight(node)
    
    // RR Case
    if (bf > 1 AND balanceFactor(node.right) >= 0)
        return rotateLeft(node)
    
    // LR Case
    if (bf < -1 AND balanceFactor(node.left) > 0):
        node.left = rotateLeft(node.left)
        return rotateRight(node)
    
    // RL Case
    if (bf > 1 AND balanceFactor(node.right) < 0):
        node.right = rotateRight(node.right)
        return rotateLeft(node)
    
    return node

balanceFactor(node):
    if (node == NULL)
        return 0
    return height(node.left) - height(node.right)

height(node):
    if (node == NULL)
        return -1
    return node.height
```

**Balance Factor Calculation and Rotation Triggering [1 mark]:**

After insertion:
1. Update node's height: height = 1 + max(left_height, right_height)
2. Calculate balance factor: BF = height(left) - height(right)
3. If BF ∉ [-1, 0, +1], tree is unbalanced
4. Determine rotation type based on BF and child's BF
5. Perform rotation and continue up tree

**Determine Rotation Type [0.5 marks]:**

| Parent BF | Child BF | Case | Rotation |
|-----------|----------|------|----------|
| < -1 (left-heavy) | ≤ 0 (child left-heavy) | LL | Right |
| < -1 | > 0 (child right-heavy) | LR | Left, then Right |
| > 1 (right-heavy) | ≥ 0 (child right-heavy) | RR | Left |
| > 1 | < 0 (child left-heavy) | RL | Right, then Left |

**Trace Insertion [0.5 marks]:**

Insert: 50, 25, 75, 10, 30, 60, 80, 5

```
Insert 50:
        50 (H=0, BF=0)

Insert 25:
        50 (H=1, BF=1)
       /
      25 (H=0, BF=0)

Insert 75:
        50 (H=1, BF=0)
       /  \
      25   75

Insert 10:
        50 (H=2, BF=1)
       /  \
      25   75
     /
    10

Insert 30:
        50 (H=2, BF=1)
       /  \
      25   75
     / \
    10  30

Insert 60:
        50 (H=2, BF=0)
       /  \
      25   75
     / \  /
    10 30 60

Insert 80:
        50 (H=2, BF=-1)
       /  \
      25   75 (H=1, BF=-1)
     / \  / \
    10 30 60 80

Insert 5:
        50 (H=3, BF=2) ← UNBALANCED
       /  \
      25   75 (H=1, BF=-1)
     / \  / \
    10 30 60 80
   /
  5

Apply RR Rotation (left rotate at 50):
        75 (H=2, BF=0)
       /  \
      50   80
     / \
    25  60
   / \
  10  30
 /
5
```

---

## Q3. AVL Tree Deletion [3 Marks]

### Question:
Explain AVL tree deletion. Discuss: (i) how deletion differs from insertion in terms of balancing requirements, (ii) whether deletion can cause multiple rotations, (iii) when rebalancing is needed, (iv) provide example showing deletion causing rotation, (v) complexity analysis.

### Answer:

**AVL Deletion Overview [0.75 marks]:**

Deletion similar to insertion but with key differences:
- Delete node like in BST (three cases: no children, one child, two children)
- Recalculate height going up
- Check balance factor at each node
- Perform rotations if needed (can be multiple rotations on path to root)

**Key Difference from Insertion:**
- Insertion may cause rebalancing at single node on path to root
- Deletion can cause multiple rebalancing operations as we traverse up
- After rotation at node, may need further rotation at parent

**When Rebalancing Needed [0.75 marks]:**

After deletion, check balance factor of each ancestor:
- If BF > 1 (right-heavy): perform LL or LR rotation
- If BF < -1 (left-heavy): perform RR or RL rotation
- Continue checking ancestors; multiple rotations may occur

**Example: Deletion Causing Rotation [1 mark]:**

```
Initial balanced AVL tree:
        50
       /  \
      25   75
     / \   / \
    10 30 60  80

Delete 10:
        50 (H=2, BF=-1)
       /  \
      25   75 (H=1, BF=-1)
     / \  / \
    X 30 60  80 ← NOT UNBALANCED YET

Rotate up: 25 (H=0, BF=0) - OK
          50 (H=2, BF=-1) - Check parent

Rotate up: 50 (H=2, BF=-1) - OK still balanced

After Deletion (if we delete 30 instead):
        50 (H=2, BF=2) ← UNBALANCED!
       /  \
      25   75 (H=1, BF=-1)
     /    / \
    10   60  80
    
Apply LR Rotation (Right-Left):
        60
       /  \
      25   75
     / \    \
    10  50   80
```

**Complexity Analysis [0.5 marks]:**

- **Time:** O(log n) for deletion (like BST) + O(log n) for rebalancing
  - Visit O(log n) nodes on path to leaf
  - At each node, rotation is O(1)
  - Total: O(log n)

- **Space:** O(log n) for recursion stack

---

## Q4. Red-Black Trees vs. AVL Trees [3 Marks]

### Question:
Compare AVL trees and Red-Black trees. Discuss: (i) structure (balance property), (ii) insertion/deletion complexity, (iii) number of rotations, (iv) when to use each, (v) provide simple example showing difference in rotations needed.

### Answer:

**AVL Tree Properties [0.75 marks]:**
- Balance condition: |height(left) - height(right)| ≤ 1
- Stricter balance; more balanced trees
- Height-balanced

**Red-Black Tree Properties [0.75 marks]:**
- Color property: each node is red or black
- Root must be black
- Red nodes cannot have red children
- All paths from node to NULL must have same number of black nodes
- Less strict balance; longer paths possible

**Comparison Table [1 mark]:**

| Aspect | AVL Tree | Red-Black Tree |
|--------|----------|---------------|
| **Balance** | Stricter (height-balanced) | Looser (color-balanced) |
| **Insertion Rotations** | At most 1 | At most 2 |
| **Deletion Rotations** | At most O(log n) | At most 3 |
| **Height** | O(log n) guaranteed | O(log n) guaranteed |
| **Search Time** | O(log n) guaranteed | O(log n) guaranteed |
| **Insert Time** | O(log n) + 1 rotation | O(log n) + 2 rotations |
| **Delete Time** | O(log n) + O(log n) rotations | O(log n) + 3 rotations |
| **When to Use** | Search-heavy; insertion-rare | Balanced insertions/deletions |
| **Real-world Use** | Database indexes; more rotations | Linux kernel; maps/sets |

**When to Use Each [0.5 marks]:**

**Use AVL Trees:**
- Read-heavy workloads; search operations more frequent than insertions
- When strict balance and minimal height is critical
- Database indexes, file systems

**Use Red-Black Trees:**
- Balanced insertion and deletion operations
- Dynamic dictionaries, maps, hash tables
- Lower constant factors; faster in practice for frequent modifications
- Used in C++ STL map, Java TreeMap, Linux kernel

---

## Q5. Hashing and Hash Functions [3 Marks]

### Question:
Explain hashing. Discuss: (i) what is a hash function and desirable properties, (ii) hash table with separate chaining vs. open addressing, (iii) collision resolution techniques, (iv) load factor and when to resize, (v) average time complexity for search/insert/delete.

### Answer:

**Hash Function Properties [0.75 marks]:**
- Maps key to index in hash table: h(key) = index (0 to table_size-1)
- **Deterministic:** same key always hashes to same index
- **Uniform distribution:** minimize collisions; spread keys evenly
- **Efficient computation:** O(1) to compute
- **Example:** h(key) = key mod table_size for integer keys

**Separate Chaining [1 mark]:**
Each index stores linked list of colliding elements
```
Hash Table with size 5:
Index 0: [key1, key6, key11] → all hash to 0
Index 1: [key2] → hashes to 1
Index 2: [key3, key8]
Index 3: []
Index 4: [key4, key9]

Search for key8:
- Compute h(key8) = 2
- Traverse list at index 2: find key8 in O(1) average
```

- **Advantages:** Simple; good for hash functions; delete easy
- **Disadvantages:** Extra memory for pointers; cache-unfriendly
- **Collision resolution:** Elements at same index stored in chain

**Open Addressing [1 mark]:**
All elements stored in table; no separate chains. On collision, find empty slot using probe sequence
```
Insert with linear probing (probe sequence: i, i+1, i+2, ...):
h(key) = key mod table_size
If occupied, try h(key)+1, h(key)+2, ... until empty slot found

Search:
- Compute h(key)
- Probe until find key or empty slot
- If empty slot, key not found
```

- **Variants:** Linear probing, quadratic probing, double hashing
- **Advantages:** Better cache locality; no pointer overhead
- **Disadvantages:** More complex; deletion harder; clustering problem

**Load Factor and Resizing [0.5 marks]:**
- Load factor α = n/m (n items in table of size m)
- When α exceeds threshold (typically 0.75):
  - Create new table (usually double size)
  - Rehash all items: h'(key) = key mod new_size
  - Copy to new table
- Cost: O(n) for one resize; amortized O(1) per insertion

**Time Complexity [1 mark]:**

Average case (good hash function, load factor α):
- **Search:** O(1) with separate chaining or open addressing
- **Insert:** O(1) + O(n) for occasional resizing = O(1) amortized
- **Delete:** O(1) with separate chaining

Worst case (poor hash function; all collisions):
- **Search/Insert/Delete:** O(n) if all keys collide

---

## Q6. Trie (Prefix Tree) Data Structure [3.5 Marks]

### Question:
Explain the Trie (prefix tree) data structure. Discuss: (i) structure and nodes, (ii) insertion/search/delete operations, (iii) advantages over hash tables for string operations, (iv) space complexity, (v) practical applications, (vi) example showing advantages for prefix queries.

### Answer:

**Trie Structure [0.75 marks]:**
- Tree of nodes where each node has children for possible next characters
- Root node has no character
- Path from root to node spells prefix
- Node marked as "end of word" if it completes a word

```
Trie of words: "cat", "car", "dog"
        root
       /    \
      c      d
      |      |
      a      o
     / \     |
    t   r    g
   (*)  (*)  (*)
   
* indicates end of word
```

**Core Operations [1.25 marks]:**

```
insert(word):
    node = root
    for each character c in word:
        if (node.children[c] == NULL)
            node.children[c] = new Node()
        node = node.children[c]
    node.isEndOfWord = TRUE

search(word):
    node = root
    for each character c in word:
        if (node.children[c] == NULL)
            return FALSE
        node = node.children[c]
    return node.isEndOfWord

delete(word):
    // Helper recursive function
    helper(node, word, index):
        if (index == word.length):
            if (NOT node.isEndOfWord)
                return FALSE  // Word doesn't exist
            node.isEndOfWord = FALSE
            return node.children.isEmpty()  // Delete if no children
        
        c = word[index]
        if (node.children[c] == NULL)
            return FALSE  // Word doesn't exist
        
        shouldDeleteChild = helper(node.children[c], word, index+1)
        if (shouldDeleteChild):
            node.children.remove(c)
            return node.children.isEmpty() AND NOT node.isEndOfWord
        
        return FALSE
```

**Advantages over Hash Tables [0.75 marks]:**

1. **Prefix queries:** Find all words with prefix "cat" in O(m) where m is prefix length (hash needs to traverse all entries)

2. **Sorted iteration:** Traverse trie in alphabetical order (hash needs sorting)

3. **No collisions:** No hash function needed; inherent structure

4. **Memory efficient for related strings:** "cat", "car", "card" share "ca" prefix

**Disadvantage:** Space overhead for alphabet (26 pointers per node for English)

**Space Complexity [0.5 marks]:**
- Average: O(ALPHABET_SIZE × total_characters_in_words) = O(m) where m total chars
- Worst: O(26 × n) for n words if all different (26 letters)
- Better than hash table when words share prefixes

**Practical Applications [0.5 marks]:**
- **Autocomplete:** Type "app" → suggest "apple", "application"
- **IP routing:** Route table uses trie for IP prefixes
- **Spell checker:** Check if word exists; suggest corrections
- **Dictionary:** Word lookup, prefix search
- **Genome sequencing:** DNA sequence searches

**Example: Autocomplete Advantage [0.5 marks]:**

Words: "apple", "app", "application", "apply"

Autocomplete for "app":
```
Trie approach:
- Search "app" to node N
- Return all words in subtree rooted at N
- Result: "app", "apple", "application", "apply" → O(m + k) where m=3, k=4

Hash table approach:
- Must check every word
- "apple".startswith("app") ✓
- "dog".startswith("app") ✗
- ...
- Result: O(n) where n = total words
```

---

## Q7. B-Trees and Multi-way Search Trees [3.5 Marks]

### Question:
Explain B-Trees. Discuss: (i) B-tree properties and order, (ii) why B-trees are used for databases, (iii) insertion/deletion operations (complex; provide high-level description and one example), (iv) time complexity, (v) comparison with binary search trees.

### Answer:

**B-Tree Properties [1 mark]:**

A B-tree of order m has:
- Every node has at most m children
- Every internal node has at least ⌈m/2⌉ children (except root)
- Root has at least 2 children
- All leaves at same level
- Nodes contain keys and pointers to children; keys sorted

Example B-tree of order 3 (each node up to 2 keys, up to 3 children):
```
        [30]
       /  |  \
    [10] [40] [60, 70]
```

**Why B-Trees for Databases [0.75 marks]:**

- **Disk optimization:** Minimize disk I/O; node size matches disk block size
- **Large datasets:** Branching factor m >> 2 (binary tree); much shallower
- **Height:** O(log_m n) is very small even for millions of records
- **Example:** m=100, n=1,000,000 → height = log₁₀₀(10⁶) ≈ 3 disk reads

Compare with binary tree: log₂(10⁶) ≈ 20 disk reads

**Insertion Overview [1 mark]:**

1. Find leaf where key should go
2. Insert key into leaf
3. If leaf full (has m keys):
   - Split into two nodes
   - Move median key up to parent
   - If parent full, split recursively up to root
   
**Deletion Overview [0.5 marks]:**

More complex; rebalancing needed to maintain minimum keys per node. High-level:
1. Find key and remove
2. If node below minimum keys:
   - Try borrow from sibling
   - If sibling can't spare, merge with sibling

**Time Complexity [0.5 marks]:**

- **Search:** O(log_m n) for tree traversal + O(m) for key search per node = O(m·log_m n)
- **Insert:** O(m·log_m n) (traversal + split operations)
- **Delete:** O(m·log_m n)

**B-Tree vs. Binary Search Tree [0.5 marks]:**

| Aspect | Binary BST | B-Tree (m=100) |
|--------|-----------|----------------|
| **Height (n=10⁶)** | 20 | 3 |
| **Children per node** | 2 | 100 |
| **Disk I/O for search** | 20 | 3 |
| **Use case** | In-memory | Disk-based (database) |
| **Implementation** | Simple | Complex |

---

## Q8. Suffix Arrays and Advanced String Matching [3 Marks]

### Question:
Explain suffix arrays. Discuss: (i) what a suffix array is and its structure, (ii) how suffix arrays improve string matching compared to naive algorithm, (iii) construction using sorting, (iv) search in suffix array (binary search), (v) advantages and disadvantages, (vi) practical applications.

### Answer:

**Suffix Array Definition [0.75 marks]:**

Suffix array is array of starting positions of all suffixes of string, sorted lexicographically.

Example: Text "banana"
```
Suffixes:        Sorted Suffixes:     Suffix Array (positions):
0: banana        5: a                 [5, 3, 1, 0, 4, 2]
1: anana         3: ana
2: nana          1: anana
3: ana           0: banana
4: na            4: na
5: a             2: nana
```

**How Suffix Arrays Improve String Matching [0.75 marks]:**

Naive algorithm: Search pattern "ana" in "banana"
- Compare at each position: O(n×m) worst case

Suffix array approach:
- Binary search for pattern in sorted suffixes: O(m·log n)
- All occurrences form contiguous block in suffix array

```
Pattern: "ana"
Binary search in suffix array [5, 3, 1, 0, 4, 2]:
- "a" at position 5: "a" < "ana" 
- "ana" at position 3: "ana" = "ana" ✓
- "anana" at position 1: "anana" > "ana"

Range found: positions 1, 3 both start with "ana"
Occurrences: position 1 and position 3 in original text
Time: O(m·log n)
```

**Construction [0.5 marks]:**

Simple approach: Extract all suffixes, sort them, store starting indices
```
Suffixes with indices:
(banana, 0), (anana, 1), (nana, 2), (ana, 3), (na, 4), (a, 5)

Sort lexicographically:
(a, 5), (ana, 3), (anana, 1), (banana, 0), (na, 4), (nana, 2)

Suffix Array: [5, 3, 1, 0, 4, 2]
```

Time: O(n² log n) for naive sorting; O(n log² n) with optimized algorithms

**Advantages [0.5 marks]:**
- Pattern matching in O(m·log n)
- Find all occurrences efficiently
- Lexicographically sorted enables range queries
- Space-efficient: only store indices, not full strings

**Disadvantages:**
- Slow construction: O(n log² n) or better algorithms complex
- More memory than single string (but less than suffix tree)
- Less flexible than suffix trees for some operations

**Practical Applications [0.5 marks]:**
- Genome sequence analysis: search for patterns in DNA
- Data compression: find repeated patterns
- Full-text search: substring matching in large texts
- DNA database searches

---

## Q9. Graph Algorithms - Shortest Path (Dijkstra) [3 Marks]

### Question:
Explain Dijkstra's shortest path algorithm. Provide: (i) algorithm pseudocode, (ii) data structures used, (iii) time complexity, (iv) why it doesn't work for negative edges, (v) worked example showing distances computed at each step, (vi) practical applications.

### Answer:

**Dijkstra's Algorithm Pseudocode [1 mark]:**

```
dijkstra(graph, source):
    distance = array of size V, initialized to ∞
    distance[source] = 0
    visited = set of unvisited vertices
    
    while (visited not empty):
        u = vertex in visited with minimum distance
        remove u from visited
        
        for each neighbor v of u:
            if (distance[u] + weight(u,v) < distance[v]):
                distance[v] = distance[u] + weight(u,v)
    
    return distance
```

**Data Structures [0.5 marks]:**
- **Distance array:** Track shortest distance to each vertex
- **Visited set:** Unvisited vertices; use priority queue for efficiency
- **Priority queue:** Extract minimum distance vertex efficiently

**Time Complexity [0.5 marks]:**
- With binary heap: O((V + E) log V)
  - Each vertex extracted once: O(V log V)
  - Each edge relaxed once: O(E log V)
- With Fibonacci heap: O(E + V log V)

**Why Not for Negative Edges [0.5 marks]:**

Once vertex marked as visited, distance assumed final. With negative edges, later paths could be shorter:

```
Graph: 1 --(1)-- 2 --(−3)-- 3

Dijkstra from 1:
Step 1: Visit 1, distance[1] = 0
Step 2: Visit 2, distance[2] = 1
Step 3: Visit 3, distance[3] = 1 + (−3) = −2

But if path through 2 → 3 exists with negative edge,
later updates could reduce distance[3] further
Algorithm marks as visited too early, missing better paths
```

Solution: Use Bellman-Ford algorithm for negative edges; allows relaxation of all edges n-1 times

**Worked Example [1 mark]:**

Graph:
```
      1 --- 4 --- 3
      |           |
      2 --- 2 --- 3
      
Start: 1
```

```
Initial:
distance = [0, ∞, ∞, ∞]
visited = {1, 2, 3}

Step 1: Visit 1 (min distance = 0)
        Relax neighbors:
        distance[2] = 0 + 1 = 1
        distance[3] = 0 + 4 = 4
        distance = [0, 1, 4, ∞]
        visited = {2, 3}

Step 2: Visit 2 (min distance = 1)
        Relax neighbors:
        distance[3] = min(4, 1 + 2) = 3
        distance = [0, 1, 3, ∞]
        visited = {3}

Step 3: Visit 3 (min distance = 3)
        Relax neighbors: (none unvisited)
        distance = [0, 1, 3, ∞]
        visited = {}

Result:
Shortest distances from 1: [0, 1, 3]
```

**Practical Applications [0.5 marks]:**
- GPS navigation: shortest route between locations
- Network routing: find minimum-cost path
- Game pathfinding: AI movement
- Social networks: degrees of separation

---

## Q10. Complexity Analysis Deep Dive [2.5 Marks]

### Question:
Analyze why certain algorithms are efficient/inefficient. Compare: (i) bubble sort vs. merge sort for n=10⁶, (ii) linear search vs. binary search on sorted data, (iii) hash table vs. sorted array for lookups, (iv) explain practical implications of theoretical complexity.

### Answer:

**Bubble Sort vs. Merge Sort [0.75 marks]:**

For n = 1,000,000:
```
Bubble Sort: O(n²) = (10⁶)² = 10¹² operations
At 10⁹ ops/sec: 10¹²/10⁹ = 1000 seconds = 16.7 minutes

Merge Sort: O(n log n) = 10⁶ × 20 = 2×10⁷ operations
At 10⁹ ops/sec: 2×10⁷/10⁹ = 0.02 seconds = 20 milliseconds
```

**Practical implication:** Merge sort is ~50,000× faster! Big-O matters at scale.

**Linear Search vs. Binary Search [0.75 marks]:**

For n = 1,000,000 items (worst case - item at end or not found):
```
Linear Search: O(n) = 10⁶ comparisons = 1 millisecond

Binary Search: O(log n) = log₂(10⁶) ≈ 20 comparisons = 20 microseconds
```

**Practical:** Binary search 50× faster on large datasets.

**Hash Table vs. Sorted Array [0.75 marks]:**

100 million items:
```
Hash Table lookup:
- O(1) average: ~1 operation = 1 nanosecond
- O(n) worst case: 10⁸ operations = 100 milliseconds (collision nightmare)

Sorted Array + Binary Search:
- O(log n) = ~27 comparisons = 27 nanoseconds
- Guaranteed; no worst case
```

**Trade-off:** Hash table faster average; binary search guaranteed worst-case

**Practical Implications [0.5 marks]:**

1. **Algorithm choice matters:** O(n²) vs. O(n log n) difference compounds with data size
2. **Constants matter at scale:** O(n) algorithm with higher constant might beat O(log n) for small n
3. **Real-world factors:** Cache locality, memory access patterns affect actual performance
4. **Hybrid approaches:** Use fast algorithm for most cases, specialized for edge cases (e.g., introsort: quicksort → heapsort fallback)

---

## Marks Summary
- Q1: 3.5 marks
- Q2: 3.5 marks
- Q3: 3 marks
- Q4: 3 marks
- Q5: 3 marks
- Q6: 3.5 marks
- Q7: 3.5 marks
- Q8: 3 marks
- Q9: 3 marks
- Q10: 2.5 marks

**Total: 31 marks**

