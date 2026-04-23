# MFT CS — Algorithms & Complexity Analysis (~15–22% of exam)

**Exam:** ETS Major Field Test in Computer Science — 66 MCQs, 2 hours, closed book.
**Target date:** May 1, 2026.
**This file covers the single highest-yield trick-heavy section.** Study this until the cheat sheet at the bottom is automatic.

---

## 1. Core Concepts (Deep Recall)

### 1.1 Asymptotic Notation — Formal Definitions

Let f, g: N → R≥0.

- **Big-O (upper bound):** f(n) = O(g(n)) iff ∃ c > 0, n₀ ≥ 0 such that ∀ n ≥ n₀, `0 ≤ f(n) ≤ c·g(n)`.
- **Big-Omega (lower bound):** f(n) = Ω(g(n)) iff ∃ c > 0, n₀ such that ∀ n ≥ n₀, `0 ≤ c·g(n) ≤ f(n)`.
- **Big-Theta (tight):** f(n) = Θ(g(n)) iff f = O(g) AND f = Ω(g). Equivalently, ∃ c₁, c₂ > 0 with c₁·g(n) ≤ f(n) ≤ c₂·g(n) for large n.
- **little-o (strict upper):** f = o(g) iff lim_{n→∞} f(n)/g(n) = 0. (g grows strictly faster.)
- **little-ω (strict lower):** f = ω(g) iff lim f(n)/g(n) = ∞.

**Proving f = O(g):** pick explicit c and n₀. Example: 3n² + 5n + 2 ≤ 10n² for n ≥ 1, so c=10, n₀=1.
**Disproving f = O(g):** show f(n)/g(n) → ∞. E.g., n² ≠ O(n) because n²/n = n → ∞.

**Exam traps:**
- O(g) is an *upper* bound, not tight. `n = O(n²)` is TRUE.
- `2ⁿ ≠ O(nᵏ)` for any constant k (exponential dominates any polynomial).
- `log_a(n) = Θ(log_b(n))` — log bases don't matter in Θ (differ by constant factor).
- `n^ε` grows faster than `(log n)ᵏ` for any ε > 0, k.
- `n!` grows faster than `2ⁿ`. Stirling: n! ≈ √(2πn)(n/e)ⁿ.

**Growth ordering (memorize):**
```
1 ≺ log log n ≺ log n ≺ √n ≺ n ≺ n log n ≺ n² ≺ n³ ≺ 2ⁿ ≺ 3ⁿ ≺ n! ≺ nⁿ
```

---

### 1.2 Master Theorem

For T(n) = a·T(n/b) + f(n), where a ≥ 1, b > 1, f(n) ≥ 0.
Let **critical exponent** = log_b(a).

- **Case 1 (work dominated by leaves):** if f(n) = O(n^(log_b a − ε)) for some ε > 0, then `T(n) = Θ(n^(log_b a))`.
- **Case 2 (balanced):** if f(n) = Θ(n^(log_b a) · log^k n) for k ≥ 0, then `T(n) = Θ(n^(log_b a) · log^(k+1) n)`.
- **Case 3 (work dominated by root):** if f(n) = Ω(n^(log_b a + ε)) AND regularity a·f(n/b) ≤ c·f(n) for some c < 1, then `T(n) = Θ(f(n))`.

**Worked examples:**
| Recurrence | log_b(a) | f(n) | Case | Answer |
|---|---|---|---|---|
| T(n) = 2T(n/2) + n | 1 | n | 2 | Θ(n log n) — **merge sort** |
| T(n) = 4T(n/2) + n | 2 | n | 1 | Θ(n²) |
| T(n) = T(n/2) + 1 | 0 | 1 | 2 | Θ(log n) — **binary search** |
| T(n) = 2T(n/2) + n² | 1 | n² | 3 | Θ(n²) |
| T(n) = 7T(n/2) + n² | log₂7 ≈ 2.807 | n² | 1 | Θ(n^log₂7) — **Strassen** |
| T(n) = 3T(n/4) + n log n | log₄3 ≈ 0.79 | n log n | 3 | Θ(n log n) |
| T(n) = T(n/2) + n | 0 | n | 3 | Θ(n) — **median-of-medians partition** |
| T(n) = 2T(n/2) + n log n | 1 | n log n | 2 (k=1) | Θ(n log² n) |

**When Master fails:**
- Non-polynomial gap: T(n) = 2T(n/2) + n/log n — gap isn't polynomial. Use recursion tree → Θ(n log log n).
- Variable split: T(n) = T(n/3) + T(2n/3) + n — use recursion tree → Θ(n log n).

---

### 1.3 Recurrence-Solving Techniques

**(a) Substitution (guess and verify by induction).**
Guess T(n) ≤ c·n log n. Prove by induction for T(n) = 2T(n/2) + n.

**(b) Recursion tree.** Sum work per level × number of levels.
- T(n) = 2T(n/2) + n: each level has total work n, depth log₂n → n log n.
- T(n) = T(n/3) + T(2n/3) + n: longest path has depth log_{3/2}n, work per level ≤ n → O(n log n).

**(c) Master theorem** (see above).

**Non-divide-conquer recurrences:**
- T(n) = T(n−1) + 1 → Θ(n)
- T(n) = T(n−1) + n → Θ(n²)
- T(n) = 2T(n−1) + 1 → Θ(2ⁿ) — Tower of Hanoi
- T(n) = T(n−1) + T(n−2) → Θ(φⁿ) — Fibonacci (φ ≈ 1.618)
- T(n) = T(√n) + 1 → Θ(log log n)

---

### 1.4 Sorting Algorithms (MEMORIZE THIS TABLE)

| Algorithm | Best | Avg | Worst | Space | Stable? | In-place? | Notes |
|---|---|---|---|---|---|---|---|
| **Bubble** | Θ(n) | Θ(n²) | Θ(n²) | O(1) | Yes | Yes | Best when adjacent-swap-check detects sorted |
| **Insertion** | Θ(n) | Θ(n²) | Θ(n²) | O(1) | Yes | Yes | **Best for nearly-sorted / small n**; O(nk) when every element ≤ k away |
| **Selection** | Θ(n²) | Θ(n²) | Θ(n²) | O(1) | **No** | Yes | Always n² even on sorted input; minimizes writes |
| **Merge** | Θ(n log n) | Θ(n log n) | Θ(n log n) | O(n) | **Yes** | No | External sorting; stable; guaranteed n log n |
| **Quick** | Θ(n log n) | Θ(n log n) | **Θ(n²)** | O(log n) avg | **No** | Yes | Worst: sorted input with first/last pivot; fastest in practice avg |
| **Heap** | Θ(n log n) | Θ(n log n) | Θ(n log n) | O(1) | **No** | Yes | build-heap is O(n); sort is n log n |
| **Counting** | Θ(n+k) | Θ(n+k) | Θ(n+k) | O(n+k) | Yes | No | k = range; only integers |
| **Radix** | Θ(d(n+k)) | Θ(d(n+k)) | Θ(d(n+k)) | O(n+k) | Yes | No | d = digits; LSD stable |
| **Bucket** | Θ(n+k) | Θ(n+k) | Θ(n²) | O(n+k) | Yes | No | Uniform distribution assumption |

**Lower bound:** comparison-based sort is Ω(n log n) — decision tree argument.
Counting/radix/bucket beat this because they're not comparison-based.

**Stable sort mnemonic:** "**Merge Insertion Bubble Counting Radix** stay stable — **Select Quick Heap** don't." (MIBCR vs SQH)

---

### 1.5 Searching

| Method | Time | Notes |
|---|---|---|
| Linear | O(n) | unsorted |
| Binary | **Θ(log n)** exactly ⌊log₂ n⌋+1 comparisons worst | sorted array, random access |
| Interpolation | Θ(log log n) avg on uniform data, O(n) worst | sorted numeric + uniform |
| Hashing | O(1) avg, O(n) worst | collisions degrade |
| BST (balanced) | O(log n) | AVL, Red-Black |
| BST (unbalanced) | O(n) worst | degenerate tree |

**Binary search recurrence:** T(n) = T(n/2) + O(1) → Θ(log n).

---

### 1.6 Graph Algorithms

| Algorithm | Purpose | Complexity | Key facts |
|---|---|---|---|
| **BFS** | shortest path unweighted | O(V+E) | uses queue; shortest-hop tree |
| **DFS** | reachability, topological, SCC | O(V+E) | uses stack/recursion; yields DAG classification edges |
| **Dijkstra** | single-src shortest path, non-neg weights | O((V+E) log V) with binary heap, O(E + V log V) with Fibonacci heap | **FAILS on negative edges** (greedy locks in wrong distance) |
| **Bellman-Ford** | single-src SP, allows negative edges | O(V·E) | detects negative cycles |
| **Floyd-Warshall** | all-pairs SP | O(V³) | DP; works with negative edges (no neg cycles) |
| **Johnson's** | all-pairs SP, sparse | O(V² log V + V·E) | reweight via Bellman-Ford then Dijkstra |
| **Prim** | MST | O(E log V) binary heap | grows one tree |
| **Kruskal** | MST | O(E log E) = O(E log V) | union-find; picks global min edge |
| **Topological sort** | DAG linearization | O(V+E) | Kahn's (in-degree) or DFS post-order reversed |
| **Kosaraju** | SCC | O(V+E) | 2 DFS passes (one on reverse graph) |
| **Tarjan** | SCC | O(V+E) | 1 DFS with low-link |

**Why Dijkstra fails on negative edges:** once a node is "settled," Dijkstra never revisits. A negative edge discovered later could produce a shorter path — but the node is already locked.

**MST properties:**
- **Cut property:** lightest edge crossing any cut is in *some* MST.
- **Cycle property:** heaviest edge on any cycle is NOT in *any* MST.
- If all edge weights distinct → MST is unique.

---

### 1.7 Dynamic Programming

**Two conditions required:**
1. **Optimal substructure:** optimal solution built from optimal solutions to subproblems.
2. **Overlapping subproblems:** same subproblems recur (separates DP from divide-and-conquer).

| Problem | Recurrence | Time | Space |
|---|---|---|---|
| Fibonacci | F(n)=F(n-1)+F(n-2) | O(n) | O(1) |
| LCS | if x[i]=y[j]: L[i,j]=L[i-1,j-1]+1 else max | O(mn) | O(mn) → O(min(m,n)) |
| 0/1 Knapsack | K[i,w]=max(K[i-1,w], K[i-1,w-wᵢ]+vᵢ) | O(nW) **pseudo-poly** | O(nW) |
| Unbounded Knapsack | K[w]=max(K[w], K[w-wᵢ]+vᵢ) | O(nW) | O(W) |
| Edit Distance (Levenshtein) | min(insert, delete, replace) | O(mn) | O(mn) |
| Matrix chain mult | M[i,j]=min_k{M[i,k]+M[k+1,j]+pᵢpₖ₊₁pⱼ₊₁} | O(n³) | O(n²) |
| Coin change (min coins) | C[v]=1+min over coins C[v-c] | O(nV) | O(V) |
| Longest Increasing Subseq | L[i]=1+max(L[j]) for j<i, a[j]<a[i] | O(n²) or O(n log n) w/ patience | O(n) |
| Bellman-Ford | d[v]=min(d[v], d[u]+w) | O(VE) | O(V) |
| Floyd-Warshall | d[i,j,k]=min(d[i,j,k-1], d[i,k,k-1]+d[k,j,k-1]) | O(V³) | O(V²) |

**Knapsack trap:** 0/1 vs unbounded differ by iteration order on weight axis. 0/1 iterates weight in reverse (each item once); unbounded iterates forward (reuse).

---

### 1.8 Greedy

**Works when:** greedy-choice property (locally optimal → globally optimal) + optimal substructure.

- **Activity selection:** sort by finish time, pick earliest-finishing compatible. O(n log n).
- **Huffman coding:** repeatedly merge two smallest-frequency nodes. O(n log n) with heap. Prefix-free optimal.
- **Fractional knapsack:** sort by value/weight, take greedily. O(n log n). (0/1 knapsack: NOT greedy, needs DP.)
- **MST (Prim, Kruskal):** greedy cut/cycle property.
- **Dijkstra:** greedy on tentative distance.

**Greedy FAILS on:** 0/1 knapsack, coin change for arbitrary denominations (works for canonical sets like US coins).

---

### 1.9 Divide and Conquer Patterns

- **Merge sort:** split, recurse, merge. T(n) = 2T(n/2) + n → n log n.
- **Quick sort:** partition, recurse on halves. Avg n log n; worst n².
- **Binary search:** T(n) = T(n/2) + 1 → log n.
- **Strassen matrix mult:** 7 multiplications on n/2 blocks. T(n) = 7T(n/2) + n² → n^(log₂7) ≈ n^2.807.
- **Karatsuba:** T(n) = 3T(n/2) + n → n^(log₂3) ≈ n^1.585.
- **Closest pair (2D):** T(n) = 2T(n/2) + n → n log n.
- **Median-of-medians (linear select):** T(n) = T(n/5) + T(7n/10) + n → O(n).

---

### 1.10 NP-Completeness

**Definitions:**
- **P:** decision problems solvable by deterministic TM in polynomial time.
- **NP:** decision problems whose YES-instances have a certificate verifiable in poly time (equivalently, nondeterministic TM poly).
- **NP-hard:** at least as hard as every problem in NP (every NP problem reduces to it in poly time). **NP-hard problems need not be in NP** (e.g., halting problem).
- **NP-complete:** NP-hard AND in NP.

**Relationships:** P ⊆ NP ⊆ PSPACE ⊆ EXP. Whether P = NP is open.

**Reductions:** A ≤_p B means "if I can solve B in poly time, I can solve A in poly time." To prove B is NP-hard, reduce a known NP-hard problem A to B.

**Cook-Levin theorem (1971):** SAT is NP-complete (the first).

**Famous NP-complete problems:**
- **SAT, 3-SAT** (not 2-SAT — 2-SAT is in P!)
- **Vertex Cover, Independent Set, Clique** (all equivalent by complement)
- **Hamiltonian Path / Cycle** (contrast Eulerian — P!)
- **Traveling Salesman (decision version)**
- **Subset Sum, Partition, Knapsack (decision)**
- **Graph Coloring (k ≥ 3)** — 2-coloring is P (bipartite check)
- **Set Cover**
- **Graph Isomorphism** — unknown (likely not NP-complete; quasi-poly algorithm exists)

**Classic P problems (don't confuse!):**
- 2-SAT (SCC-based), Eulerian path, bipartite matching, primality (AKS 2002), linear programming, max-flow, shortest paths.

---

### 1.11 Amortized Analysis

Three methods, all equivalent:

**(1) Aggregate:** total cost of n ops / n.
**(2) Accounting:** assign "credits" to ops; expensive ops borrow from saved credit.
**(3) Potential:** define Φ(state) ≥ 0, amortized cost = actual + ΔΦ.

**Classic examples:**
- **Dynamic array (doubling):** n pushes cost O(n) total → **O(1) amortized** per push.
- **Binary counter increment:** n increments flip at most 2n bits total → O(1) amortized.
- **Splay tree / union-find:** O(log n) / O(α(n)) amortized.

---

## 2. Trick Questions (These Appear Often)

1. **"Which sort is best for nearly-sorted data?"** → **Insertion sort.** O(n) when each element is within constant distance of its final position; generally O(n·d) where d = max displacement.

2. **"Quicksort worst case?"** → **O(n²)** when input is already sorted (or reverse-sorted) AND pivot is always first or last element. Randomized pivot or median-of-three makes worst case vanishingly rare.

3. **"Building a heap from unsorted array takes?"** → **O(n)**, not O(n log n). Sift-down from bottom: Σ_{h=0}^{log n} (n/2^(h+1)) · h = O(n). Heapsort is still O(n log n) because of n extract-max operations.

4. **"Which of these is NOT stable: merge, quick, insertion, counting?"** → **Quick** (and heap, selection).

5. **"Dijkstra on a graph with one negative edge — will it work?"** → **No**, even a single negative edge can break it. Use Bellman-Ford.

6. **"What does this recurrence solve to: T(n) = 2T(n/2) + n log n?"** → **Θ(n log² n)** (Master case 2 with k=1). Trap: students guess n log n.

7. **"Mid-run identification":** given a partial sorted array, determine which algorithm produced it.
   - Selection sort: leftmost prefix is fully sorted AND contains the k smallest.
   - Insertion sort: leftmost prefix is sorted but elements may not be the overall smallest.
   - Bubble: after pass i, rightmost i elements are in final position (largest).
   - Merge: partial runs of length 2^k are sorted.
   - Quick: pivot in its final position with < on left, > on right (partitioned around pivots).

8. **"BFS gives shortest path in a weighted graph?"** → **Only if all weights equal.** Otherwise use Dijkstra.

9. **"log(n!) = ?"** → **Θ(n log n)** by Stirling. Appears in decision-tree lower bound for sorting.

10. **"Which problem is in P: 2-SAT, 3-SAT, TSP, Clique?"** → **2-SAT** (the others are NP-complete).

11. **"Fibonacci recursive naive complexity?"** → **Θ(φⁿ)** exponential; memoized is O(n).

12. **"Prim vs Kruskal on dense graph?"** → Prim with adjacency matrix O(V²) beats Kruskal O(E log V) = O(V² log V) when graph is dense.

13. **"Hash table with chaining, load factor α, expected search?"** → **Θ(1 + α)**.

14. **"Tightest bound on 3n² + 100n log n?"** → Θ(n²). Big-O of n³ is technically TRUE but not tight.

15. **"Which traversal visits DAG in valid linear order?"** → Reverse post-order DFS, or Kahn's BFS on in-degree.

---

## 3. Tricks & Mnemonics

### Sort complexity mnemonic
**"SIBS are square, MHQ are n log n, CRB are linear."**
- Square (Θ(n²)): **S**election, **I**nsertion, **B**ubble, **S**hell worst.
- n log n: **M**erge, **H**eap, **Q**uick avg (Q worst n²).
- Linear-ish: **C**ounting, **R**adix, **B**ucket avg.

### Master theorem shortcut
Compare f(n) with n^(log_b a):
- f smaller (by poly factor) → **Case 1**: answer is n^(log_b a).
- f equal (within log^k) → **Case 2**: multiply by one more log.
- f larger (by poly factor) → **Case 3**: answer is f(n) (check regularity).

### "Heap build is O(n)" — proof sketch
At height h there are ≤ ⌈n/2^(h+1)⌉ nodes; sift-down from height h costs O(h).
Total = Σ_{h=0}^{log n} (n/2^(h+1)) · h = n · Σ (h/2^(h+1)) = n · O(1) = **O(n)**.
(Geometric series Σ h/2^h converges to 2.)

### Stable sort mnemonic
**"Most Items Can Be Rated"** — **M**erge, **I**nsertion, **C**ounting, **B**ubble, **R**adix are stable.
Everything else (Quick, Heap, Selection, Shell) is unstable in standard form.

### In-place mnemonic
**"Heap, Quick, and the n² crew are in-place; Merge, Counting, Radix need extra room."**

### NP-hard vs NP-complete
**"Complete = Hard ∩ NP."** If you can verify in poly time AND reduce SAT to it, it's complete. If you can't even verify (e.g., halting), it's only hard.

### Graph algorithm pick-chart
- Unweighted shortest path → **BFS**.
- Non-negative weights → **Dijkstra**.
- Negative weights (no neg cycle) → **Bellman-Ford**.
- All pairs, dense graph → **Floyd-Warshall**.
- MST sparse → **Kruskal**. Dense → **Prim**.
- DAG ordering → **Topological sort**.
- Cycle detection undirected → **Union-Find** or DFS.
- Cycle detection directed → DFS with back-edge / gray-node check.

---

## 4. Worked MFT-Style MCQs

**Q1.** Which of the following has the smallest asymptotic order?
(A) n log n  (B) n^1.1  (C) (log n)^10  (D) 2^(log n)  (E) n / log n

**Answer: (C).** (log n)^10 = polylog; polylog < any polynomial. Note 2^(log₂ n) = n, so (D) = n. Order: (log n)^10 < n/log n < n < n log n < n^1.1.

---

**Q2.** T(n) = 3T(n/2) + n². Which holds?
(A) Θ(n²)  (B) Θ(n² log n)  (C) Θ(n^log₂3)  (D) Θ(n³)  (E) Θ(n log n)

**Answer: (A).** log_2 3 ≈ 1.585; f(n) = n² = Ω(n^(1.585+ε)). Case 3, regularity holds (3·(n/2)² = 3n²/4 ≤ c·n² for c=3/4). So T(n) = Θ(n²).

---

**Q3.** You build a max-heap from an array of n distinct integers and then perform k calls to EXTRACT-MAX. Total time?
(A) O(n log n)  (B) O(n + k log n)  (C) O(k log n)  (D) O(n log k)  (E) O(n·k)

**Answer: (B).** Build is O(n); each extract is O(log n).

---

**Q4.** Which algorithm produces this intermediate state on input [5, 1, 4, 2, 8] after 2 passes: `[1, 4, 2, 5, 8]`?
(A) Selection  (B) Insertion  (C) Bubble  (D) Quick  (E) Merge

**Answer: (C) Bubble.** After pass 1 (largest bubbles right): [1,4,2,5,8]. After pass 2: [1,2,4,5,8]. Actually the shown state matches end of pass 1 — but selection after pass 2 would place 1 and 2 in front: [1,2,...]. This is bubble after pass 1. (In practice read carefully — MFT tests exact trace recognition.)

---

**Q5.** In a connected weighted undirected graph with all distinct weights, how many distinct minimum spanning trees exist?
(A) 0  (B) 1  (C) V−1  (D) E−V+1  (E) depends on the graph

**Answer: (B) exactly 1.** Uniqueness theorem: distinct weights ⇒ unique MST.

---

**Q6.** Which problem is in P?
(A) Hamiltonian Cycle  (B) 3-SAT  (C) 2-SAT  (D) Vertex Cover  (E) Subset Sum

**Answer: (C) 2-SAT** (solved in linear time via implication graph SCCs). Others are NP-complete.

---

**Q7.** Dijkstra's algorithm on a graph with edge weights ∈ {−1, 0, 1, 2}:
(A) Always correct  (B) Correct if no neg cycle  (C) May fail on some inputs  (D) Runs in O(V+E)  (E) Same as BFS

**Answer: (C).** Dijkstra may fail on any negative edge, not just negative cycles.

---

**Q8.** Tight bound for T(n) = T(n−1) + n is:
(A) Θ(n)  (B) Θ(n log n)  (C) Θ(n²)  (D) Θ(2ⁿ)  (E) Θ(n!)

**Answer: (C).** Sum 1+2+…+n = n(n+1)/2.

---

**Q9.** A hash table with chaining has load factor α = 3. Expected time for unsuccessful search (simple uniform hashing)?
(A) O(1)  (B) O(log n)  (C) O(α) = O(1)  (D) O(n)  (E) O(n²)

**Answer: (C).** Unsuccessful search = Θ(1 + α). With α=3, this is Θ(1).

---

**Q10.** The number of comparisons binary search performs on a sorted array of n elements in the worst case is:
(A) n/2  (B) log₂ n  (C) ⌊log₂ n⌋ + 1  (D) n log n  (E) √n

**Answer: (C).** Worst-case comparisons = ⌊log₂ n⌋ + 1.

---

**Q11.** Which of the following recurrences does NOT apply to the Master Theorem directly?
(A) T(n) = 4T(n/2) + n²  (B) T(n) = 2T(n/2) + n/log n  (C) T(n) = T(n/2) + 1  (D) T(n) = 8T(n/2) + n³  (E) T(n) = 2T(n/2) + n

**Answer: (B).** The gap between f(n) = n/log n and n^(log_b a) = n is not polynomial, so the Master Theorem does not apply. (Actual answer via recursion tree: Θ(n log log n).)

---

**Q12.** Which sorting algorithm is OPTIMAL (minimum total work) when the input is known to be sorted in reverse?
(A) Quicksort with first-element pivot  (B) Insertion sort  (C) Heap sort  (D) Merge sort  (E) Bubble sort

**Answer: (D) Merge sort** — still Θ(n log n).
- (A) quicksort degrades to Θ(n²)
- (B) insertion sort is Θ(n²) on reverse input
- (C) heap sort works but not faster
- (E) bubble sort is Θ(n²)
Both (C) and (D) are Θ(n log n); merge sort makes exactly n⌈log n⌉ comparisons worst case. In an MFT MCQ, pick merge sort as the "safe n log n."

---

**Q13.** Which traversal of a binary search tree yields elements in sorted order?
(A) Pre-order  (B) In-order  (C) Post-order  (D) Level-order  (E) Reverse pre-order

**Answer: (B) In-order.**

---

**Q14.** For which problem is a greedy algorithm guaranteed to produce an optimal solution?
(A) 0/1 Knapsack  (B) Traveling Salesman  (C) Fractional Knapsack  (D) Longest Common Subsequence  (E) Matrix Chain Multiplication

**Answer: (C) Fractional Knapsack** (sort by value/weight ratio).

---

## 5. Master Cheat Sheet

### Asymptotic growth ladder
`1 < log log n < log n < √n < n < n log n < n² < n³ < 2ⁿ < n!`

### Sort summary
| Algo | Best | Avg | Worst | Space | Stable | In-place |
|---|---|---|---|---|---|---|
| Insertion | n | n² | n² | 1 | Y | Y |
| Selection | n² | n² | n² | 1 | N | Y |
| Bubble | n | n² | n² | 1 | Y | Y |
| Merge | n log n | n log n | n log n | n | Y | N |
| Quick | n log n | n log n | n² | log n | N | Y |
| Heap | n log n | n log n | n log n | 1 | N | Y |
| Counting | n+k | n+k | n+k | n+k | Y | N |
| Radix | d(n+k) | d(n+k) | d(n+k) | n+k | Y | N |
| Bucket | n+k | n+k | n² | n+k | Y | N |

### Data-structure operation table
| DS | Access | Search | Insert | Delete |
|---|---|---|---|---|
| Array | O(1) | O(n) | O(n) | O(n) |
| Sorted array | O(1) | O(log n) | O(n) | O(n) |
| Linked list | O(n) | O(n) | O(1)* | O(1)* |
| Stack / Queue | — | O(n) | O(1) | O(1) |
| Hash table | — | O(1) avg / O(n) worst | O(1)/O(n) | O(1)/O(n) |
| BST (balanced) | O(log n) | O(log n) | O(log n) | O(log n) |
| BST (worst) | O(n) | O(n) | O(n) | O(n) |
| Binary Heap | O(1) top | O(n) | O(log n) | O(log n) |
| B-Tree | O(log n) | O(log n) | O(log n) | O(log n) |
| Trie | — | O(m) | O(m) | O(m) |
| Union-Find | — | — | O(α(n)) | O(α(n)) |

\* Given node pointer.

### Graph algorithm table
| Algorithm | Time | Handles neg weights? | Output |
|---|---|---|---|
| BFS | O(V+E) | — | unweighted SP tree |
| DFS | O(V+E) | — | discovery/finish times |
| Dijkstra | O((V+E) log V) | **No** | SSSP |
| Bellman-Ford | O(VE) | Yes (detects neg cycle) | SSSP |
| Floyd-Warshall | O(V³) | Yes (no neg cycle) | APSP |
| Johnson | O(V² log V + VE) | Yes | APSP, sparse |
| Prim | O(E log V) | — | MST |
| Kruskal | O(E log V) | — | MST |
| Topological sort | O(V+E) | — | DAG ordering |
| Kosaraju / Tarjan | O(V+E) | — | SCCs |

### DP problem table
| Problem | Time | Space |
|---|---|---|
| Fibonacci | O(n) | O(1) |
| LCS | O(mn) | O(min(m,n)) |
| LIS | O(n log n) | O(n) |
| Edit distance | O(mn) | O(mn) |
| 0/1 Knapsack | O(nW) | O(W) |
| Unbounded Knapsack | O(nW) | O(W) |
| Matrix chain | O(n³) | O(n²) |
| TSP (Held-Karp) | O(n²·2ⁿ) | O(n·2ⁿ) |

### NP-complete quick list
SAT, 3-SAT, Vertex Cover, Independent Set, Clique, Hamiltonian Path/Cycle, TSP (decision), Subset Sum, Partition, Knapsack (decision), 3-Coloring, Set Cover.

### In P (don't confuse):
2-SAT, Eulerian path/circuit, bipartite matching, max flow, shortest paths (non-neg or poly), primality, linear programming, 2-coloring.

---

## Sources

- [ETS Major Field Test Computer Science Sample Questions (official PDF)](https://www.ets.org/pdfs/mft/comp-sci-sample-questions.pdf)
- [ETS MFT Computer Science Test Description](https://www.ets.org/pdfs/mft/comp-sci-test-description.pdf)
- [ETS MFT Test Content page](https://www.ets.org/mft/about/test-content.html)
- [How I prepare for ETS Major Field Test CS — Part 1](https://scratchrobotics.com/2018/07/08/how-i-prepare-for-ets-major-field-test-computer-science/)
- [TAMU Sample Questions mirror](https://people.engr.tamu.edu/d-walker/Quals/gre_cs_questions_compsci.pdf)
- [Quizlet MFT CS Study Guide](https://quizlet.com/study-guides/major-field-test-in-computer-science-a35fe395-557a-4dd0-818d-f72e9ea8a5bc)
- [Urch MFT CS practice forum](https://www.urch.com/forums/gre-computer-science/123223-practice-problems-ets-major-field-test-computer-science.html)
- [MIT 6.046 Master Theorem Worksheet Solutions](https://courses.csail.mit.edu/6.046/spring02/handouts/mastersol.pdf)
- [Stanford CS 161 Recurrences lecture](https://web.stanford.edu/class/archive/cs/cs161/cs161.1168/lecture3.pdf)
- [UWO Master Theorem Practice Problems](https://www.csd.uwo.ca/~mmorenom/CS433-CS9624/Resources/master.pdf)
- [Berkeley Vazirani Chapter 8 NP-completeness](https://people.eecs.berkeley.edu/~vazirani/algorithms/chap8.pdf)
- [Wikipedia: List of NP-complete problems](https://en.wikipedia.org/wiki/List_of_NP-complete_problems)
- [GeeksforGeeks Master Theorem](https://www.geeksforgeeks.org/dsa/advanced-master-theorem-for-divide-and-conquer-recurrences/)
- [Baeldung: Quicksort Worst Case](https://www.baeldung.com/cs/quicksort-time-complexity-worst-case)

---

## 🧪 Try Yourself — Practice Questions

1. Which of the following is TRUE about the function f(n) = 3n² + 5n log n + 100?
   - A. f(n) = O(n log n)
   - B. f(n) = Θ(n²)
   - C. f(n) = Ω(n³)
   - D. f(n) = o(n²)

2. What is the time complexity of building a binary heap from an unsorted array of n elements using the bottom-up (heapify) method?
   - A. O(log n)
   - B. O(n)
   - C. O(n log n)
   - D. O(n²)

3. An array is nearly sorted (each element is at most k = O(1) positions from its sorted place). Which sorting algorithm runs in O(n) time on such input?
   - A. Merge sort
   - B. Quicksort (random pivot)
   - C. Insertion sort
   - D. Heapsort

4. Which algorithm FAILS to compute correct single-source shortest paths when the graph has negative edge weights (but no negative cycles)?
   - A. Bellman-Ford
   - B. Floyd-Warshall
   - C. Dijkstra's algorithm
   - D. Topological-order relaxation on a DAG

5. Which equality is correct?
   - A. log(n!) = Θ(n)
   - B. log(n!) = Θ(log n)
   - C. log(n!) = Θ(n log n)
   - D. log(n!) = Θ(n²)

6. Which statement about 2-SAT is TRUE?
   - A. 2-SAT is NP-complete.
   - B. 2-SAT is solvable in polynomial time.
   - C. 2-SAT is undecidable.
   - D. 2-SAT is harder than 3-SAT.

7. BFS from a source s in an unweighted graph computes:
   - A. Shortest paths in any weighted graph.
   - B. Shortest paths only when all edge weights are equal (e.g., unweighted).
   - C. A minimum spanning tree.
   - D. Strongly connected components.

8. Solve T(n) = 2T(n/2) + n using the Master Theorem.
   - A. Θ(n)
   - B. Θ(n log n)
   - C. Θ(n²)
   - D. Θ(log n)

9. Solve T(n) = 4T(n/2) + n.
   - A. Θ(n)
   - B. Θ(n log n)
   - C. Θ(n²)
   - D. Θ(n² log n)

10. Solve T(n) = T(n/2) + 1.
    - A. Θ(1)
    - B. Θ(log n)
    - C. Θ(n)
    - D. Θ(n log n)

11. Solve T(n) = 3T(n/2) + n² using the Master Theorem.
    - A. Θ(n^log₂ 3)
    - B. Θ(n²)
    - C. Θ(n² log n)
    - D. Θ(n³)

12. Which problem is solved optimally by a greedy algorithm (not requiring full DP)?
    - A. 0/1 Knapsack
    - B. Fractional Knapsack
    - C. Matrix Chain Multiplication
    - D. Longest Common Subsequence

13. In a dynamic array (vector) that doubles capacity on overflow, the amortized cost per push_back is:
    - A. Θ(1)
    - B. Θ(log n)
    - C. Θ(n)
    - D. Θ(n log n)

14. The running time of Floyd-Warshall all-pairs shortest paths is:
    - A. O(V²)
    - B. O(V² log V)
    - C. O(V³)
    - D. O(VE log V)

15. Kruskal's MST with union-find (path compression + union by rank) on a graph with |V|=V, |E|=E runs in:
    - A. O(V + E)
    - B. O(E log V)
    - C. O(V²)
    - D. O(VE)

16. If problem A is NP-complete and A ≤ₚ B (A polynomial-time reduces to B) with B ∈ NP, then:
    - A. B is in P.
    - B. B is NP-complete.
    - C. B is undecidable.
    - D. P = NP.

17. A topological sort exists for a directed graph G if and only if:
    - A. G is connected.
    - B. G is a DAG (has no directed cycle).
    - C. G is bipartite.
    - D. G is strongly connected.

18. Consider the recurrence T(n) = T(n-1) + n. Solving via substitution gives:
    - A. Θ(n)
    - B. Θ(n log n)
    - C. Θ(n²)
    - D. Θ(2ⁿ)

19. The edit distance (Levenshtein) between strings of length m and n can be computed in:
    - A. O(m + n)
    - B. O(mn)
    - C. O(mn log(mn))
    - D. O((m+n)²·log(m+n))

20. In a DFS of a directed graph, classifying edges: the presence of which edge type indicates a cycle?
    - A. Tree edge
    - B. Forward edge
    - C. Cross edge
    - D. Back edge

### ✅ Answer Key & Explanations

1. **B.** Dominant term is 3n²; lower-order terms (n log n, constant) are absorbed. So f(n) = Θ(n²). Not O(n log n) (too small) and not Ω(n³) (too big).

2. **B.** Bottom-up heapify costs Σ_{h=0..log n} (n/2^(h+1))·h which sums to O(n). *Trap:* inserting n items one-by-one into a heap is O(n log n), but building from an existing array is linear.

3. **C.** Insertion sort runs in O(nk) on input where each element is at most k positions from its sorted place; with k = O(1) that is O(n). Merge/heap/quicksort still run in Θ(n log n).

4. **C.** Dijkstra's "finalize the min-distance vertex" greedy step is invalid with negative edges — a later negative edge can reduce an already-finalized distance. Bellman-Ford and Floyd-Warshall handle negative edges (absent a negative cycle for SSSP).

5. **C.** log(n!) = Σ_{k=1..n} log k. Upper bound ≤ n log n. Lower bound ≥ (n/2)·log(n/2) = Θ(n log n). So log(n!) = Θ(n log n) (Stirling).

6. **B.** 2-SAT is in P — solvable in linear time via the implication graph and strongly connected components (Aspvall-Plass-Tarjan). 3-SAT and higher are NP-complete.

7. **B.** BFS layers give shortest paths by edge count — equivalent to shortest paths only when edge weights are all equal (unweighted). General weights require Dijkstra/Bellman-Ford.

8. **B.** a=2, b=2, f(n)=n; n^log_b a = n^1 = n = Θ(f(n)) → Case 2 → Θ(n log n). (Merge sort recurrence.)

9. **C.** a=4, b=2, f(n)=n; n^log_b a = n² dominates f(n) → Case 1 → Θ(n²).

10. **B.** a=1, b=2, f(n)=1; n^log_b a = n⁰ = 1 = Θ(f(n)) → Case 2 → Θ(log n). (Binary search recurrence.)

11. **B.** a=3, b=2, f(n)=n²; n^log_b a = n^1.585; f(n)=n² grows faster and regularity holds (3·(n/2)² = 3n²/4 ≤ c·n² with c=3/4<1) → Case 3 → Θ(n²).

12. **B.** Fractional knapsack: sort by value/weight ratio and take greedily (splitting the last item) — provably optimal. 0/1 Knapsack needs DP; MCM and LCS are classic DP problems.

13. **A.** Doubling bounds total copying cost by a geometric series 1+2+4+…+n ≤ 2n over n pushes, so the amortized cost per push is Θ(1).

14. **C.** Three nested loops over V vertices → Θ(V³); handles negative weights (no negative cycle).

15. **B.** Sorting edges is O(E log E) = O(E log V) (since E ≤ V²); union-find operations contribute O(E·α(V)) ≈ O(E). Total O(E log V).

16. **B.** If A is NP-complete and A ≤ₚ B, then B is NP-hard. Combined with B ∈ NP, B is NP-complete by definition.

17. **B.** A directed graph admits a topological ordering iff it is a DAG. Connectivity is not required; strong connectivity would forbid any topo order.

18. **C.** T(n) = n + (n-1) + … + 1 = n(n+1)/2 = Θ(n²).

19. **B.** Standard Wagner-Fischer DP uses an (m+1)×(n+1) table; each cell is O(1) → O(mn) time (space reducible to O(min(m,n))).

20. **D.** In DFS of a directed graph, a back edge (to an ancestor in the DFS tree) exists iff the graph has a cycle. Forward/cross edges alone do not imply cycles.

---

## 🔧 Supplement: Binary Search Variants & Randomized Algorithms

### Binary Search Variants

Standard binary search is just the tip. Know these patterns:

**1. Exact match (classic):**
```
lo = 0, hi = n - 1
while lo <= hi:
    mid = lo + (hi - lo) / 2      # avoid overflow; NOT (lo+hi)/2
    if a[mid] == target: return mid
    elif a[mid] < target: lo = mid + 1
    else: hi = mid - 1
return -1
```

**2. Lower bound** — first index where `a[i] >= target` (C++ `lower_bound`):
```
lo = 0, hi = n
while lo < hi:
    mid = lo + (hi - lo) / 2
    if a[mid] < target: lo = mid + 1
    else: hi = mid
return lo       # in [0, n]
```

**3. Upper bound** — first index where `a[i] > target`. Same as above with `a[mid] <= target: lo = mid + 1`.

**4. Floor / ceiling:** floor(x) = largest a[i] ≤ x = `upper_bound(x) - 1`. Ceiling(x) = smallest a[i] ≥ x = `lower_bound(x)`.

**5. Rotated sorted array:** at each step one half is guaranteed sorted. Check which half contains target:
```
if a[lo] <= a[mid]:           # left half sorted
    if a[lo] <= target < a[mid]: hi = mid - 1
    else: lo = mid + 1
else:                          # right half sorted
    if a[mid] < target <= a[hi]: lo = mid + 1
    else: hi = mid - 1
```

**6. Binary search on answer (parametric search):** when the answer space is monotone (e.g., "minimum capacity to ship in D days"). Search the **answer** instead of an array:
- Define `feasible(x)` that returns True iff answer x works.
- Find smallest/largest x where feasible transitions.
- Runtime: O(log(range) · cost_of_feasible).

**Off-by-one traps:**
- `while lo < hi` with `hi = n` (exclusive) → lower/upper bound style.
- `while lo <= hi` with `hi = n-1` (inclusive) → exact-match style.
- **Never** do `(lo + hi) / 2` on signed ints — use `lo + (hi - lo) / 2`.
- Exact iteration count on array of length n: ⌊log₂ n⌋ + 1 comparisons in the worst case.

### Randomized Algorithms

**Two flavors:**
| | Correctness | Runtime |
|-|-|-|
| **Las Vegas** | Always correct | Random (expected bound) |
| **Monte Carlo** | Possibly wrong, bounded error prob. | Deterministic (worst-case bound) |

- **Randomized quicksort:** pick a random pivot. **Expected** time O(n log n); worst case still O(n²) but with negligible probability. Las Vegas — always sorts correctly.
- **Median-of-three:** choose pivot = median of `a[lo], a[mid], a[hi]`. Mitigates the adversarial already-sorted input.
- **Miller–Rabin primality:** Monte Carlo with one-sided error. "Composite" is always correct; "probably prime" has error ≤ (1/4)^k after k rounds.
- **Skip lists:** randomized level assignment gives O(log n) expected search/insert — simpler than balanced BSTs.
- **Reservoir sampling** (pick k uniformly from a stream of unknown length n):
  ```
  reservoir = first k elements
  for i = k+1 .. n:
      j = random(1..i)
      if j <= k: reservoir[j] = a[i]
  ```
  After processing, every element has probability k/n of being in the reservoir.
- **Fisher–Yates shuffle** (uniform random permutation, O(n)):
  ```
  for i = n-1 down to 1:
      j = random(0..i)
      swap(a[i], a[j])
  ```
  ⚠️ Naïve "swap each a[i] with a[random(0..n-1)]" is **not** uniform.

### Practice MCQs

1. Given sorted array `[1,3,5,7,9,11]`, `lower_bound(7)` returns index:
   A) 2   B) 3   C) 4   D) −1

2. In iterative binary search over n elements, the worst-case number of comparisons is:
   A) n/2   B) n   C) ⌊log₂ n⌋ + 1   D) √n

3. Why is `mid = lo + (hi - lo) / 2` preferred over `(lo + hi) / 2`?
   A) Faster   B) Avoids integer overflow   C) Gives better rounding   D) Works on floats

4. Expected running time of randomized quicksort on n distinct elements:
   A) O(n)   B) O(n log n)   C) O(n²)   D) O(n log² n)

5. A Monte Carlo algorithm is one that:
   A) Always correct, random runtime   B) Possibly wrong, bounded runtime   C) Always runs in polynomial time   D) Uses no randomness

6. Reservoir sampling with k=1 on a stream of length n gives each element probability:
   A) 1   B) 1/k   C) 1/n   D) k/n² of being selected

7. Miller–Rabin declaring "composite" means:
   A) Definitely composite   B) Probably composite   C) Probably prime   D) Inconclusive

8. To binary-search the "minimum weight capacity to ship all packages within D days", you search over:
   A) the package array   B) the answer space (capacities)   C) days   D) package indices

**Answer Key:**
1. **B** — index 3 is the first position with a value ≥ 7 (value 7 itself).
2. **C** — halving n takes ⌊log₂ n⌋ + 1 comparisons worst case.
3. **B** — `lo + hi` may overflow for large indices; subtraction form is safe.
4. **B** — expected O(n log n); worst case O(n²) with vanishing probability.
5. **B** — Monte Carlo: bounded time, may be wrong with small probability.
6. **C** — by symmetry, each of n elements has equal probability 1/n.
7. **A** — "composite" is one-sided certain; only "prime" verdicts are probabilistic.
8. **B** — parametric / binary-search-on-answer: search the monotone feasibility space.

---

