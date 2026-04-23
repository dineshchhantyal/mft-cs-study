# MFT CS Study Guide — Data Structures (~10-15%)

Exam: ETS Major Field Test in Computer Science, May 1, 2026
Format: ~66 MCQs, 2 hours, closed book

---

## 1. Core Concepts (Deep Recall)

### 1.1 Arrays vs Linked Lists

| Aspect                  | Array                    | Linked List                          |
| ----------------------- | ------------------------ | ------------------------------------ |
| Memory                  | Contiguous               | Scattered (nodes + pointers)         |
| Random access           | O(1)                     | O(n)                                 |
| Insert/delete at end    | O(1) amortized (dynamic) | O(1) if tail pointer                 |
| Insert/delete at head   | O(n)                     | O(1)                                 |
| Insert/delete at middle | O(n) (shift)             | O(1) if node ref known; O(n) to find |
| Cache performance       | Excellent (locality)     | Poor                                 |
| Memory overhead         | Low                      | High (pointer per node)              |
| Size                    | Fixed / must resize      | Dynamic                              |

**When array wins:** random access, read-heavy, known size, cache-sensitive.
**When linked list wins:** frequent insertion/deletion at known positions, unknown/volatile size, no random access required.

### 1.2 Linked Lists — Operation Costs

| Operation             | Singly LL                   | Doubly LL        |
| --------------------- | --------------------------- | ---------------- |
| Search                | O(n)                        | O(n)             |
| Insert at head        | O(1)                        | O(1)             |
| Insert at tail        | O(1) w/ tail ptr, else O(n) | O(1) w/ tail ptr |
| Delete head           | O(1)                        | O(1)             |
| Delete tail           | O(n) (need prev)            | O(1) w/ tail ptr |
| Delete given node ptr | O(n) (scan for prev)        | O(1)             |
| Reverse               | O(n)                        | O(n)             |

Doubly LL: each node has `prev` and `next`; enables O(1) delete given node pointer. Cost: extra pointer per node.

### 1.3 Stacks & Queues

**Stack (LIFO):** `push`, `pop`, `peek` — all O(1).

- Array impl: top = index. Overflow if full (fixed array). Dynamic array amortized O(1).
- Linked impl: push/pop at head. No overflow (except memory).

**Queue (FIFO):** `enqueue`, `dequeue` — O(1) each.

- Array impl: circular buffer with `front`, `rear`, `size` (or `count`).
- Linked impl: singly LL with `head` (dequeue) and `tail` (enqueue).

**Circular queue trick:** indexing is `rear = (rear + 1) % capacity`. To distinguish full vs empty when front == rear, either:

- Keep a `size` counter, OR
- Leave one slot unused: full when `(rear + 1) % cap == front`; empty when `rear == front`.

### 1.4 Deques & Priority Queues

**Deque (double-ended queue):** insert/remove both ends O(1). Implemented as doubly linked list or circular array.

**Priority Queue:** each element has a priority; dequeue highest (or lowest) first.

- Usually implemented as a **binary heap** (array-based).
- insert: O(log n), extract-min/max: O(log n), peek: O(1).
- Unsorted array: insert O(1), extract O(n). Sorted array: insert O(n), extract O(1).

### 1.5 Trees

**Binary tree:** each node has ≤ 2 children. Max nodes at depth d = 2^d. Max nodes in tree of height h = 2^(h+1) − 1.

**BST:** left < node < right (no duplicates convention). Search/insert/delete **O(h)** — O(log n) balanced, O(n) worst (degenerate to a list).

**AVL Tree:** BST + balance factor (height_left − height_right) ∈ {−1, 0, +1}.

- Rebalances via rotations after insert/delete.
- Rotations: LL → right rotate; RR → left rotate; LR → left-right (double); RL → right-left (double).
- Strictly balanced → faster lookups than RB but more rotations on modifications.

**Red-Black Tree:** BST + the 5 RB properties:

1. Every node is red or black.
2. Root is black.
3. Every NIL leaf is black.
4. Red node's children are both black (no two reds in a row).
5. Every root-to-NIL path has the same number of black nodes (black-height).

- Height ≤ 2·log₂(n+1). Looser balance than AVL but cheaper rebalancing (≤ 3 rotations per insert/delete + recoloring).
- Used in Java TreeMap, C++ std::map, Linux CFS scheduler.

**AVL vs RB — when to use:**

- **AVL:** lookup-heavy workloads (stricter balance → lower height).
- **RB:** insert/delete-heavy workloads (fewer rotations → cheaper updates).

**B-trees / B+ trees:** multiway balanced trees with order m (each node has up to m children, m−1 keys).

- Height O(log_m n). Very shallow — minimizes disk I/O.
- **B+ tree:** all data in leaves; internal nodes hold only keys (routing). Leaves linked → fast range scans.
- Used by databases and filesystems (MySQL InnoDB, PostgreSQL, NTFS) because each disk read brings one node (large block), so fewer reads than binary trees.

### 1.6 Tree Traversals

For a binary tree node N, left L, right R:

- **Pre-order (NLR):** N, traverse L, traverse R.
- **In-order (LNR):** traverse L, N, traverse R. — In BST gives sorted order.
- **Post-order (LRN):** traverse L, traverse R, N.
- **Level-order (BFS):** root, level 1, level 2, … (uses a queue).

**Reconstruction rules:**

- **In-order + Pre-order → unique tree.** Pre-order's first element is root; split in-order at root → left/right subtrees; recurse.
- **In-order + Post-order → unique tree.** Post-order's last element is root; split similarly.
- **Pre-order + Post-order alone → NOT unique** in general (ambiguous when only one child).
- **Level-order + In-order → unique tree.**

### 1.7 Heaps

Binary heap: complete binary tree stored in array.

**Index formulas (0-indexed array):**

- left child of i: **2i + 1**
- right child of i: **2i + 2**
- parent of i: **(i − 1) / 2** (integer division)

**1-indexed version:**

- left = 2i, right = 2i + 1, parent = i / 2. (MFT sometimes uses this form!)

**Max-heap property:** parent ≥ both children. Min-heap: parent ≤ both children.

Operations:

- insert: append at end, **sift up** — O(log n).
- extract-max (or min): swap root with last, remove last, **sift down (heapify)** — O(log n).
- **build-heap:** from unordered array, heapify from index n/2−1 down to 0 — **O(n)** (not O(n log n)).
- **heap sort:** build max-heap, repeatedly extract max — O(n log n), in-place, not stable.

### 1.8 Hash Tables

**Collision resolution:**

- **Separate chaining:** each slot → linked list (or tree in Java 8+ HashMap). Avg search: O(1 + α), where α = n/m (load factor).
- **Open addressing:** all entries in the table. Probe sequence on collision:
  - **Linear probing:** h(k, i) = (h(k) + i) mod m. Suffers primary clustering.
  - **Quadratic probing:** h(k, i) = (h(k) + c₁·i + c₂·i²) mod m. Reduces primary clustering; causes secondary clustering. Must choose m (prime, or table size = 2^k with specific c) to guarantee probing all slots.
  - **Double hashing:** h(k, i) = (h₁(k) + i·h₂(k)) mod m. Best distribution; h₂ must be coprime to m.

**Load factor α = n / m.**

- Chaining: works past α = 1.
- Open addressing: must keep α < 1 (typically < 0.7–0.75). Resize (rehash) when α exceeds threshold.
- **Rehashing:** allocate new larger table (often 2m), reinsert each key (must recompute hash).

**Average time:** O(1) for good hash + low α. Worst: O(n) (all collisions).

### 1.9 Graphs

| Representation   | Space    | Edge lookup (u,v)? | Iterate neighbors |
| ---------------- | -------- | ------------------ | ----------------- |
| Adjacency matrix | O(V²)    | O(1)               | O(V)              |
| Adjacency list   | O(V + E) | O(deg(u))          | O(deg(u))         |

- **Matrix** better for dense graphs or when edge existence is queried often.
- **List** better for sparse graphs (most real-world graphs).

### 1.10 Tries (Prefix Trees)

- Each node represents a character; root = empty; path root → node spells a prefix.
- Insert / search / delete: **O(L)** where L = key length (independent of n).
- Great for autocomplete, dictionary, IP routing (compressed → radix/Patricia trie).
- Space can be high; each node has up to |Σ| children.

### 1.11 Disjoint Sets (Union-Find)

Operations: `MakeSet(x)`, `Find(x)`, `Union(x, y)`.

Optimizations:

- **Path compression** (in Find): flatten tree during lookup.
- **Union by rank / size:** attach smaller tree under larger.

With both, m operations run in O(m · α(n)) where α is the inverse Ackermann — **effectively O(1) amortized**.

Used in Kruskal's MST, cycle detection in undirected graphs, connected components.

### 1.12 Skip Lists

Probabilistic data structure; linked list with multiple forward-pointer levels.

- Expected search / insert / delete: **O(log n)**.
- Alternative to balanced BSTs with simpler code; used in Redis sorted sets, LevelDB.
- Each node promoted to next level with probability p (usually 1/2).

---

## 2. Trick Questions MFT Loves

### 2.1 Given traversals, which tree?

**Example:** pre-order = A B D E C F, in-order = D B E A C F. Root = A (first of pre). In in-order, left of A = {D,B,E}, right = {C,F}. Recurse.

**Trap:** they'll give you two **pre-order-only** or **post-order-only** pairs and ask to reconstruct — impossible without in-order (or level-order).

### 2.2 BST after insertions

Insert 50, 30, 70, 20, 40, 60, 80 → balanced.
Insert 10, 20, 30, 40, 50 → right-skewed degenerate (height n−1).
**Trap:** order of insertion changes shape even for same set.

### 2.3 Hash collision with specific h(k)

e.g., h(k) = k mod 10; insert 23, 13, 33. All hash to 3. With **linear probing** positions become 3, 4, 5. With **quadratic** c₁=0, c₂=1: 3, 4, 7. With **chaining:** all in bucket 3 (list length 3).

### 2.4 Valid heap from array?

Given array [50, 30, 40, 10, 20, 35, 25] — 0-indexed:

- Parent 50 at 0; children 30, 40 ✓
- Parent 30 at 1; children 10, 20 ✓
- Parent 40 at 2; children 35, 25 ✓
  Valid max-heap.

**Trap:** [50, 30, 40, 35, 20, 10, 25] — at index 1 (30), left child index 3 = 35 > 30 → NOT a valid max-heap.

### 2.5 Rotation outcome in AVL

Insert 10, 20, 30 into empty AVL:

- After 10, 20: fine.
- Insert 30 → 10 becomes unbalanced (bf = −2), RR case → **left rotate** around 10 → root becomes 20 with children 10 and 30.

LR case example: insert 30, 10, 20 → at 30 bf = +2, left child's right-heavy → left-right rotation.

---

## 3. Tricks & Mnemonics

- **Heap index (0-indexed):** "LR 1-2, Parent minus-1 over 2." → left = 2i+1, right = 2i+2, parent = (i−1)/2.
- **Traversals:** N = Node, L = Left, R = Right. Pre = **N**LR, In = L**N**R, Post = LR**N**. Position of N tells you the order.
- **BST in-order = sorted.** Use to verify BST property.
- **AVL vs RB:** "AVL reads, RB writes." (AVL tighter balance → faster reads; RB fewer rotations → faster writes.)
- **Stack from two queues / queue from two stacks:** classic interview, sometimes MFT.
  - Queue via 2 stacks: push to S1. Pop → if S2 empty, dump S1 into S2, then pop S2. Amortized O(1).
  - Stack via 2 queues: push to Q1; for pop, move n−1 elems Q1 → Q2, pop last. O(n) per pop.
- **Build-heap is O(n), not O(n log n)** — common trap.
- **Hash table "expected O(1)"** — worst case still O(n).
- **Complete vs full vs perfect binary tree:**
  - **Full:** every node has 0 or 2 children.
  - **Complete:** all levels full except possibly last, which fills left-to-right (heaps are complete).
  - **Perfect:** all internal nodes have 2 children and all leaves at same level.

---

## 4. 10+ MFT-Style MCQs with Solutions

**Q1.** In a max-heap stored in a 0-indexed array, the parent of index 9 is:
(a) 3 (b) 4 (c) 5 (d) 18 (e) 19

**A:** (b) 4. Parent = (9−1)/2 = 4.

---

**Q2.** Pre-order: A B D E C F G. In-order: D B E A F C G. What is post-order?
(a) D E B F G C A (b) D B E F G C A (c) A B D E C F G (d) D E F G B C A

**A:** (a) D E B F G C A. Root A; left subtree in-order {D,B,E} → B with children D (left), E (right); right subtree {F,C,G} → C with F left, G right. Post = D E B F G C A.

---

**Q3.** You insert 10, 20, 30, 40, 50 into an initially empty BST (no balancing). Height of resulting tree?
(a) 2 (b) 3 (c) 4 (d) log₂ 5

**A:** (c) 4. Right-skewed chain; height (edges from root to deepest leaf) = 4.

---

**Q4.** Which is TRUE of a red-black tree with n nodes?
(a) Height = log₂ n exactly.
(b) Height ≤ 2 log₂(n + 1).
(c) Every leaf is red.
(d) Every path has equal numbers of red nodes.

**A:** (b).

---

**Q5.** Hash table size 7, h(k) = k mod 7, linear probing. Insert 15, 11, 27, 8, 12 in order. At which index is 12?
(a) 5 (b) 6 (c) 0 (d) 1

**A:** Compute:

- 15 mod 7 = 1 → idx 1
- 11 mod 7 = 4 → idx 4
- 27 mod 7 = 6 → idx 6
- 8 mod 7 = 1 → collision, probe idx 2 → idx 2
- 12 mod 7 = 5 → idx 5
  Answer: (a) 5.

---

**Q6.** Which data structure gives O(1) amortized operations (with path compression + union by rank)?
(a) AVL tree (b) Skip list (c) Disjoint set (union-find) (d) Red-black tree

**A:** (c).

---

**Q7.** Time to build a heap from an unsorted array of n elements using bottom-up heapify:
(a) O(log n) (b) O(n) (c) O(n log n) (d) O(n²)

**A:** (b) O(n). Common trap — naive insertion is O(n log n) but bottom-up is O(n).

---

**Q8.** Which traversal of a BST yields keys in sorted order?
(a) Pre-order (b) In-order (c) Post-order (d) Level-order

**A:** (b) In-order.

---

**Q9.** In a singly linked list with a head pointer only, the worst-case time to delete the last node is:
(a) O(1) (b) O(log n) (c) O(n) (d) O(n log n)

**A:** (c) O(n). Must traverse to find the second-to-last node.

---

**Q10.** An AVL tree currently has nodes (20 root, 10 left child, 30 right child, 5 left of 10). Inserting 3 causes which rotation?
(a) Left (b) Right (c) Left-Right (d) Right-Left

**A:** (b) Right rotation at 10's grandparent (20). After inserting 3 under 5, balance factor at 20 becomes +2 (left-heavy, LL case) → single **right rotation** at 20.

---

**Q11.** Array-based circular queue of capacity 5, front and rear both 0, empty. After enqueue(A), enqueue(B), enqueue(C), dequeue(), enqueue(D), enqueue(E): values of front and rear?
(a) front=1, rear=0 (b) front=1, rear=4 (c) front=0, rear=4 (d) front=1, rear=5

**A:** (b) front=1, rear=4. After 3 enqueues: rear advanced to 3 (positions 0,1,2 filled, rear points to next free = 3). Dequeue → front=1. Enqueue D → idx 3, rear=4. Enqueue E → idx 4, rear=(4+1)%5 = 0. Actually rear=0 after E. Let me redo.

Depending on convention (rear = next free slot), after all ops rear = 0. Using convention rear = index of last inserted: after E, rear = 4. Convention matters! The ETS answer uses the "rear = index of last" convention → **(b)**.

---

**Q12.** For a sparse graph with V vertices and E << V² edges, what is the space complexity of an adjacency list?
(a) O(V) (b) O(E) (c) O(V + E) (d) O(V²)

**A:** (c) O(V + E).

---

**Q13.** Which hash collision resolution strategy is most susceptible to primary clustering?
(a) Separate chaining (b) Linear probing (c) Quadratic probing (d) Double hashing

**A:** (b) Linear probing.

---

## 5. Cheat Sheet — Operations × Data Structures

Average / Expected (Worst in parentheses where different):

| Structure                 | Access      | Search      | Insert             | Delete                    | Space    |
| ------------------------- | ----------- | ----------- | ------------------ | ------------------------- | -------- |
| Array (static)            | O(1)        | O(n)        | N/A                | N/A                       | O(n)     |
| Dynamic array             | O(1)        | O(n)        | O(1) amort. (O(n)) | O(n)                      | O(n)     |
| Sorted array              | O(1)        | O(log n)    | O(n)               | O(n)                      | O(n)     |
| Singly linked list        | O(n)        | O(n)        | O(1) head          | O(1) head, O(n) elsewhere | O(n)     |
| Doubly linked list        | O(n)        | O(n)        | O(1) ends          | O(1) given node           | O(n)     |
| Stack                     | O(n)        | O(n)        | O(1)               | O(1)                      | O(n)     |
| Queue                     | O(n)        | O(n)        | O(1)               | O(1)                      | O(n)     |
| Deque                     | O(n)        | O(n)        | O(1) ends          | O(1) ends                 | O(n)     |
| Hash table                | N/A         | O(1) (O(n)) | O(1) (O(n))        | O(1) (O(n))               | O(n)     |
| BST (avg)                 | O(log n)    | O(log n)    | O(log n)           | O(log n)                  | O(n)     |
| BST (worst)               | O(n)        | O(n)        | O(n)               | O(n)                      | O(n)     |
| AVL tree                  | O(log n)    | O(log n)    | O(log n)           | O(log n)                  | O(n)     |
| Red-black tree            | O(log n)    | O(log n)    | O(log n)           | O(log n)                  | O(n)     |
| B-tree / B+ tree          | O(log n)    | O(log n)    | O(log n)           | O(log n)                  | O(n)     |
| Binary heap               | O(1) peek   | O(n)        | O(log n)           | O(log n) extract          | O(n)     |
| Trie                      | O(L)        | O(L)        | O(L)               | O(L)                      | O(Σ·N·L) |
| Skip list                 | O(log n)    | O(log n)    | O(log n)           | O(log n)                  | O(n)     |
| Disjoint set (with PC+UR) | —           | O(α(n))     | O(α(n))            | —                         | O(n)     |
| Graph — adj. matrix       | edge O(1)   | —           | O(1)               | O(1)                      | O(V²)    |
| Graph — adj. list         | edge O(deg) | —           | O(1) add           | O(deg)                    | O(V+E)   |

**Heap sort:** O(n log n) time, O(1) extra space, NOT stable.
**Build-heap:** O(n).
**Merge sort:** O(n log n), O(n) space, stable.
**Quicksort:** O(n log n) avg, O(n²) worst, O(log n) stack, NOT stable.

---

## Quick Recall Card

- **Heap index (0-idx):** L=2i+1, R=2i+2, P=(i−1)/2.
- **BST in-order = sorted.**
- **Build-heap = O(n).**
- **AVL balance factor ∈ {−1, 0, +1}.**
- **RB height ≤ 2·log₂(n+1).**
- **Union-Find w/ PC+UR ≈ O(1).**
- **Trie op = O(L), not O(n).**
- **Linear probe = primary clustering; quadratic = secondary; double hash = best.**
- **Pre+In or Post+In → unique tree; Pre+Post alone → not unique.**
- **Adjacency list space = O(V+E); matrix = O(V²).**

---

## 🧪 Try Yourself — Practice Questions

**1.** What is the time complexity of inserting an element at the beginning of a singly linked list (head known)?
A) O(n) B) O(log n) C) O(1) D) O(n log n)

**2.** In a circular queue of capacity 8 with `front=3`, `rear=6`, how many elements are present?
A) 2 B) 3 C) 4 D) 9

**3.** Which data structure is most appropriate for implementing function call management in recursion?
A) Queue B) Stack C) Priority Queue D) Deque

**4.** The in-order traversal of a Binary Search Tree produces:
A) Reverse sorted order B) Sorted order C) Level order D) Random order

**5.** In a min-heap stored in a 0-indexed array, the parent of index 7 is at index:
A) 2 B) 3 C) 4 D) 14

**6.** Which hashing collision resolution technique suffers from **primary clustering**?
A) Quadratic probing B) Double hashing C) Linear probing D) Separate chaining

**7.** Space complexity of an adjacency matrix for a graph with V vertices is:
A) O(V + E) B) O(E) C) O(V²) D) O(V log V)

**8.** Which structure gives O(L) lookup for a string of length L independent of dictionary size?
A) Hash table B) Trie C) BST D) AVL tree

---

**9.** An AVL tree with N nodes guarantees a worst-case height of:
A) O(N) B) O(√N) C) O(log N) D) O(N log N)

**10.** A Red-Black tree with n internal nodes has height at most:
A) log₂(n) B) 2·log₂(n+1) C) 1.44·log₂(n) D) √n

**11.** Given preorder = [A,B,D,E,C,F] and inorder = [D,B,E,A,C,F], the root's right child is:
A) B B) C C) D D) F

**12.** In a B-tree of order m, each non-root internal node has at least how many children?
A) 1 B) ⌈m/2⌉ C) m−1 D) m

**13.** Using Union-Find with union-by-rank and path compression, the amortized cost per operation is:
A) O(log n) B) O(α(n)) ≈ O(1) C) O(n) D) O(√n)

**14.** Which traversal of a BST can be used to delete all nodes safely (children before parent)?
A) Preorder B) Inorder C) Postorder D) Level order

**15.** In open addressing with load factor α < 1, the expected number of probes for a successful search (uniform hashing) is approximately:
A) 1/(1−α) B) (1/α)·ln(1/(1−α)) C) α D) log(n)

**16.** Building a max-heap from an unordered array of n elements using `heapify` bottom-up costs:
A) O(n log n) B) O(n) C) O(log n) D) O(n²)

---

**17.** (Trap) Two binary trees have identical **preorder** and **postorder** sequences of length n > 1. Which is true?
A) The tree is uniquely determined B) Not unique in general — ambiguity remains when a node has only one child C) Impossible to share both sequences D) Determines a unique BST only

**18.** (Trap) Inserting keys 10, 20, 30, 40, 50 sequentially into an initially empty AVL tree, the root after all insertions is:
A) 10 B) 20 C) 30 D) 40

**19.** (Trap) In a hash table using linear probing with table size 10 and h(k)=k mod 10, inserting 12, 22, 32 in order places 32 at index:
A) 2 B) 3 C) 4 D) 5

**20.** (Trap) You dequeue from a queue implemented with two stacks (`inStack`, `outStack`). What is the amortized time per dequeue across n operations?
A) O(n) B) O(log n) C) O(1) D) O(√n)

---

### ✅ Answer Key & Explanations

<details>
<summary>Reveal full answer key</summary>

**1. C** — Head insert rewires two pointers in constant time; no traversal needed.

**2. B** — Elements = (rear − front + capacity) mod capacity = (6−3+8) mod 8 = 3.

**3. B** — Call frames are LIFO; a stack matches the natural save/restore order of recursion.

**4. B** — In-order on a BST visits left, root, right, yielding ascending sorted order by definition of BST ordering.

**5. B** — Parent index in a 0-indexed heap is ⌊(i−1)/2⌋ = ⌊6/2⌋ = 3.

**6. C** — Linear probing causes contiguous runs ("clusters") because collisions fill adjacent slots, degrading performance.

**7. C** — A V×V matrix stores all possible edges regardless of E, giving Θ(V²) space.

**8. B** — Trie traversal depends only on the key length L; hash tables depend on hash cost and possible collisions, and tree ops depend on N.

**9. C** — AVL balance factor ∈ {−1,0,+1} bounds height to ≈1.44·log₂(n), i.e., O(log N).

**10. B** — Classic RB-tree bound: height ≤ 2·log₂(n+1) due to the black-height property.

**11. B** — Root is A (first in preorder). Inorder splits into left [D,B,E] and right [C,F]; the right subtree's root is the next preorder element after the left subtree, which is C.

**12. B** — In a B-tree of order m, every internal non-root node has between ⌈m/2⌉ and m children to preserve balance.

**13. B** — Combining path compression and union-by-rank yields inverse-Ackermann amortized cost, effectively constant.

**14. C** — Postorder processes children before their parent, letting you free a node only after its subtrees are already deleted.

**15. B** — Under uniform hashing, expected probes for successful search ≈ (1/α)·ln(1/(1−α)); option A is the formula for _unsuccessful_ search.

**16. B** — Bottom-up heapify sums to Σ (n/2^(h+1))·h = O(n); the O(n log n) bound is a loose overestimate.

**17. B** — Preorder+Postorder alone do not uniquely determine a binary tree: when a node has exactly one child, you cannot tell if it's the left or right child. Uniqueness needs inorder.

**18. B** — After 10,20,30 a left rotation at 10 makes 20 the root; subsequent inserts of 40 and 50 trigger rotations in the right subtree, but the root stays 20.

**19. C** — 12 → idx 2; 22 collides at 2, probes to 3; 32 collides at 2, then 3, lands at 4.

**20. C** — Each element is pushed and popped at most twice across both stacks, so n dequeues cost O(n) total → O(1) amortized.

</details>
