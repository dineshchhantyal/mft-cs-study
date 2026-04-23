# MFT CS Study — Gap Audit

**Date:** 2026-04-22  |  **Exam:** ETS MFT CS, 2026-05-01  |  **Days left:** 9

Purpose: identify topics on the ETS MFT CS outline that our 10 study files cover poorly or not at all, rank them by exam value, and give the student something immediately usable.

---

## 1. Coverage Scorecard (vs ETS 4CMF outline + student reports)

Legend: ✅ thorough · ⚠️ light · ❌ missing

### Programming Fundamentals / Languages (21–27%)
| Topic | Status | Where |
|---|---|---|
| Parameter passing (value/ref/name/value-result/need) | ✅ | 01 §3 |
| Scope static vs dynamic | ✅ | 01 §4 |
| Binding times; static vs dynamic dispatch | ✅ | 01 §5, §7.4 |
| Recursion, tail calls, mutual recursion | ✅ | 01 §6 |
| OOP pillars, inheritance, polymorphism kinds | ✅ | 01 §7 |
| Functional programming, closures, HOFs, laziness | ✅ | 01 §8 |
| Type systems (static/dynamic, variance, inference) | ✅ | 01 §9 |
| Compilers overview (phases, VMs, JIT) | ✅ | 01 §10 |
| **Parsing detail: LL(1), LR, FIRST/FOLLOW, shift-reduce conflicts** | ⚠️ | 01 §10 one paragraph only |
| Memory model: stack/heap/activation records/GC | ✅ | 01 §11 |
| Exception handling | ✅ | 01 §12 |
| Expressions, precedence, side effects | ✅ | 01 §13 |
| **Lisp/Scheme evaluation (cons/car/cdr, quote, lambda)** | ❌ | only named in paradigm table |
| **Prolog basics (facts/rules/unification/backtracking)** | ❌ | only named |
| **C pointer arithmetic (p+i semantics, p[i]≡*(p+i), array decay)** | ⚠️ | implied, not laid out |
| **Formal semantics / Hoare logic / loop invariants / program verification** | ❌ | absent |

### Algorithms & Complexity (16–22%)
| Topic | Status | Where |
|---|---|---|
| Asymptotic notation, growth ladder | ✅ | 02 §1.1 |
| Master theorem + recursion trees | ✅ | 02 §1.2–1.3 |
| Sorting (table, stability, in-place, lower bound) | ✅ | 02 §1.4 |
| Searching (binary/interp/hash) | ✅ | 02 §1.5 |
| Graph algos (BFS/DFS/Dijkstra/BF/FW/MST/TS/SCC) | ✅ | 02 §1.6 |
| DP (classic problems list) | ✅ | 02 §1.7 |
| Greedy, D&C, amortized | ✅ | 02 §1.8–1.11 |
| NP / NP-complete / reductions | ✅ | 02 §1.10 |
| **Binary search variants (lower_bound/upper_bound, first/last occurrence)** | ⚠️ | only canonical form |
| **Randomized algorithms (Las Vegas vs Monte Carlo, randomized quicksort)** | ⚠️ | mentioned once |
| Segment/Fenwick trees | n/a | out of MFT scope — correctly skipped |

### Data Structures (~10–15%)
| Topic | Status | Where |
|---|---|---|
| Arrays, linked lists, stacks/queues/deques | ✅ | 03 §1.1–1.4 |
| BST/AVL/RB/B-tree/B+ | ✅ | 03 §1.5 |
| Traversals + reconstruction rules | ✅ | 03 §1.6 |
| Heap, hash table (all collision schemes) | ✅ | 03 §1.7–1.8 |
| Graph reps, tries, union-find, skip list | ✅ | 03 §1.9–1.12 |

### Discrete Math (15–21%)
| Topic | Status | Where |
|---|---|---|
| Propositional & predicate logic | ✅ | 04 §1.1–1.2 |
| Proof techniques, induction, pigeonhole | ✅ | 04 §1.3 |
| Sets, relations, functions | ✅ | 04 §1.4–1.6 |
| Combinatorics + stars-and-bars | ✅ | 04 §1.7 |
| Probability + expectation/variance | ✅ | 04 §1.8 |
| Graph theory (Euler, planar, coloring) | ✅ | 04 §1.9 |
| Number theory (gcd, Bezout, Fermat, Euler, CRT) | ✅ | 04 §1.10 |
| Recurrences (characteristic roots) | ✅ | 04 §1.11 |
| **Boolean algebra / circuit minimization as "discrete math"** | ✅ (in 05) | covered under architecture |
| **Lattices / boolean posets beyond relations** | ⚠️ | brief |

### Architecture (inside Systems 16–24%)
| Topic | Status | Where |
|---|---|---|
| Number rep (2's comp, IEEE 754) | ✅ | 05 §1.1 |
| Boolean algebra, K-maps | ✅ | 05 §1.2 |
| Combinational + sequential logic | ✅ | 05 §1.3 |
| CPU datapath, pipelining, hazards | ✅ | 05 (rest) |
| Cache (3Cs, mapping, write policy) | ✅ | 05 §1 |
| **Cache coherence (MESI)** | ⚠️ | named only in 00 |
| **Bit manipulation tricks (XOR swap, isolate lowest bit, popcount)** | ❌ | absent |
| **Amdahl's law / speedup formulas** | ⚠️ verify in 05 cheat sheet |

### Operating Systems
| Topic | Status | Where |
|---|---|---|
| Processes/threads, PCB, states | ✅ | 06 §1 |
| Scheduling (FCFS/SJF/SRTF/RR/MLFQ, Gantt) | ✅ | 06 §2 |
| Synchronization, semaphores, monitors | ✅ | 06 §3 |
| Deadlock (4 conditions, banker's) | ✅ | 06 §4 |
| Paging, virtual memory, TLB, page replacement | ✅ | 06 §5–6 |
| File systems, disk scheduling, I/O | ✅ | 06 §7–9 |
| **fork/exec trace questions** | ⚠️ | light examples |

### Databases (3–8%)
Relational model, algebra, SQL, FDs/normalization, ER, ACID, isolation levels, indexing, recovery — ✅ all present in 07.

### Networks (part of Systems)
OSI/TCPIP, TCP vs UDP, handshake, CIDR, congestion control, DNS/DHCP/ARP, routing — ✅ in 09.

### Theory of Computation (5–10%)
Regex/DFA/NFA, CFG/PDA, pumping lemmas, Chomsky hierarchy, TM, decidability, P/NP — ✅ in 08.

### Software Engineering (3–9%)
SDLC, coupling/cohesion, SOLID, GoF patterns, testing, metrics, UML, VCS — ✅ in 09.

### "Other" Bucket (3–8%) — **this is where most gaps live**
| Topic | Status |
|---|---|
| HCI / Nielsen heuristics / usability | ❌ named once, never defined |
| Professional ethics / licensing (GPL, MIT, copyright vs patent) | ❌ named once |
| Graphics pipeline (transformations, rasterization, scanline, Bezier, clipping) | ❌ one word in 00 |
| AI: A*, minimax, alpha-beta pruning, admissible heuristic | ❌ named only |
| AI/ML basics: supervised vs unsupervised, overfitting, bias-variance | ❌ absent |
| Cryptography: symmetric vs asymmetric, hash functions, public-key, digital signatures | ❌ absent |
| Information theory: entropy, Huffman optimality, source coding | ⚠️ Huffman in 02, no entropy |
| Numerical methods: Newton's method, fixed-point iteration, rounding/truncation error, condition number | ❌ absent |
| Regex practical use (grep-style character classes, anchors) | ⚠️ only formal in 08 |

---

## 2. Critical Gaps (must-fix before May 1)

Ordered by expected exam yield (weight × likelihood of appearance × cheapness to learn).

1. **Graphics basics** — ETS reports consistently show 1–2 questions on 2D transformations, homogeneous coordinates, scanline fill, or clipping. Zero coverage currently.
2. **AI basics (A*, minimax, alpha-beta, admissible heuristic)** — recurring 1–2 questions; easy to memorize.
3. **Cryptography basics (symmetric vs asymmetric, hash functions, RSA intuition, digital signature flow)** — 1 question is common; current coverage is zero.
4. **HCI / Nielsen's 10 heuristics** — reliable 1 question in the "Other" bucket; pure memorization.
5. **Ethics / licensing (copyright vs patent vs trademark, GPL copyleft vs MIT/BSD, ACM code)** — near-guaranteed 1 question; zero coverage.
6. **Hoare logic / loop invariants / partial vs total correctness** — ETS has revived program-verification items on recent forms. Completely absent.
7. **Parsing depth: LL(1) vs LR(1), FIRST/FOLLOW, shift-reduce vs reduce-reduce conflict** — only one line today. Mid-value (compilers questions are sparse but appear).
8. **Bit manipulation tricks (XOR swap, `x & (x-1)` kills lowest set bit, `x & -x` isolates it, detect power of 2)** — code-trace questions often hide these.
9. **C pointer arithmetic semantics** — `p + i` adds `i * sizeof(*p)`, `&a[i] == a + i`, arrays decay to pointers. Classic MFT snippet trap; currently implicit only.
10. **Numerical methods lite** — Newton's method fixed-point, rounding vs truncation error, catastrophic cancellation, relative vs absolute error. Appears on ~1/3 of forms.

## 3. Minor Gaps (nice-to-have)

- **Information-theory basics** — entropy formula, why Huffman is optimal among prefix codes.
- **Cache coherence (MESI states)** — 1 question risk in architecture cluster.
- **Binary-search variants** — lower_bound/upper_bound, exact count of occurrences.
- **Randomized algorithms** — Las Vegas vs Monte Carlo, expected runtime of randomized quicksort.
- **Lisp/Scheme basic semantics** — `(cons a b)`, `car`, `cdr`, `(lambda ...)` reading.
- **Prolog basic semantics** — `parent(tom, bob).` facts, `?- parent(tom,X).` query, unification.
- **fork() trace questions** — "how many processes after N forks?" = 2^N.
- **Amdahl's law** — speedup = 1 / ((1-p) + p/s).
- **Practical regex (`^`, `$`, `[a-z]`, `\d`, `*`, `+`, `?`, greedy vs lazy).**
- **Parallel/concurrent algorithm basics** — Flynn's taxonomy (SISD/SIMD/MISD/MIMD) in arch file? currently missing.

## 4. Overcovered Topics (relative to exam weight)

Trim these on re-review; do not delete, but don't re-study:

- **01 §3.4 Jensen's device** + deep call-by-name array traps — one canonical trap suffices; current file has three variants.
- **02 DP table with 10 problems** — MFT tests *recognition* of DP, rarely the specific recurrence. Knapsack, LCS, edit distance, coin change is enough.
- **03 §1.12 Skip lists** — near-zero MFT appearance. Skim only.
- **03 §1.5 B+ tree internals** — know "shallow, used in DBs"; deep mechanics are DB-class material, not MFT.
- **07 §8 Isolation levels full phenomena matrix** — one-line summary (RU→RC→RR→SER blocks dirty/non-rep/phantom progressively) is enough.
- **07 §10 Recovery (ARIES/WAL detail)** — very unlikely to be tested beyond "WAL = log before data".
- **09 GoF 23-pattern list** — ETS normally asks only the top ~6 (Singleton, Factory, Adapter, Decorator, Observer, Strategy).
- **08 Myhill–Nerode formal side** — recognize the name; don't memorize proof.

## 5. Recommendations — Rank-Ordered Fixes

| Rank | Fix | Time to learn | Placement |
|---|---|---|---|
| 1 | Add "Other-bucket survival sheet" (Graphics + AI + HCI + Ethics + Crypto) | 60 min | **new file `10-other-topics.md`** |
| 2 | Add parsing depth (LL(1) tree, FIRST/FOLLOW example, shift-reduce) | 30 min | append to `01-programming-fundamentals.md` §10 |
| 3 | Add bit-manipulation tricks + C pointer arithmetic | 20 min | append to `01-programming-fundamentals.md` new §13a, or `05-architecture.md` |
| 4 | Add Hoare triples / loop invariants + partial/total correctness | 20 min | new section `01-programming-fundamentals.md` §19 |
| 5 | Add numerical-methods + information-theory cheat box | 20 min | append to `02-algorithms.md` or `10-other-topics.md` |
| 6 | Fill Amdahl, MESI, Flynn's taxonomy in architecture | 15 min | `05-architecture.md` |
| 7 | Add Lisp/Scheme + Prolog 10-line primers | 15 min | `01-programming-fundamentals.md` §8 appendix |
| 8 | Add fork() practice + binary-search variants | 15 min | `06-operating-systems.md` and `02-algorithms.md` |

Total new study time: ~3 hours — fits Day 8 or 9 easily.

---

## 6. Emergency Appendix — Most Important Facts from Critical Gaps

*(Usable immediately even if no new files get written.)*

### Graphics
1. **2D transforms use 3×3 matrices on homogeneous coordinates** `(x, y, 1)`. Translation is *not* linear in 2D; that's why we go to 3D homogeneous form. Composition order: `M_total = M_last · … · M_first` (right-to-left application).
2. **Rotation matrix (CCW θ):** `[[cosθ, -sinθ, 0],[sinθ, cosθ, 0],[0,0,1]]`. Scale: diagonal `s_x, s_y, 1`. Translation: `[[1,0,t_x],[0,1,t_y],[0,0,1]]`.
3. **Pipeline order:** model → view → projection → clip → viewport → rasterize → shade. **Painter's algorithm** = back-to-front draw; **z-buffer** = per-pixel depth test (standard). **Scanline fill:** parity rule (odd = inside) using active edge table.

### AI
4. **A\*** uses `f(n) = g(n) + h(n)`. Optimal iff `h` is **admissible** (never overestimates) and, for graph search, **consistent/monotonic** (`h(n) ≤ c(n,n') + h(n')`). BFS = A\* with h=0 and unit costs; Dijkstra = A\* with h=0.
5. **Minimax + alpha-beta:** MAX maximizes, MIN minimizes. Alpha-beta can prune a branch when `α ≥ β`; best-case reduces tree from `b^d` to `b^(d/2)` (double the search depth).

### Cryptography
6. **Symmetric** (AES, DES, 3DES) — one shared key, fast, key-distribution problem. **Asymmetric/public-key** (RSA, ECC, Diffie-Hellman) — pair (pub, priv); encrypt with pub, decrypt with priv (confidentiality). Sign with priv, verify with pub (authenticity).
7. **Hash functions** (SHA-256, MD5-broken) — one-way, fixed-length, collision-resistant (ideally). Used in digital signatures (sign `hash(m)`, not `m`) and integrity checks.
8. **Digital signature:** `sig = Enc_privSender(hash(m))`. Receiver computes `hash(m)` and `Dec_pubSender(sig)`, compares.

### HCI — Nielsen's 10 Heuristics (recognize, don't memorize order)
9. Visibility of system status; match system & real world; user control & freedom; consistency & standards; error prevention; recognition rather than recall; flexibility & efficiency of use; aesthetic & minimalist design; help users recognize/diagnose/recover from errors; help & documentation. **Fitts' law:** movement time ∝ log(distance/target-size).

### Ethics / Licensing
10. **Copyright** protects expression; **patent** protects invention; **trademark** protects brand identity; **trade secret** protects confidential info.  **Copyleft (GPL)** requires derivatives to be GPL; **permissive (MIT, BSD, Apache)** allows proprietary derivatives. Apache adds explicit patent grant. **ACM Code** emphasizes public good, avoiding harm, honesty, fairness, respecting privacy.

### Hoare Logic
11. **Hoare triple** `{P} S {Q}`: if P holds before S and S terminates, Q holds after. **Partial correctness** = no termination guarantee; **total correctness** = P implies termination in Q. Assignment axiom: `{Q[e/x]} x := e {Q}` (substitute e for x in postcondition). **Loop rule:** need an **invariant** I with `{I ∧ B} S {I}` and terminate with `{I ∧ ¬B}` → Q. Classic factorial invariant: `i ≤ n ∧ f = i!`.

### Parsing detail
12. **LL(1):** top-down, 1 token lookahead; can parse any grammar whose parse table has no duplicate entries. Built from FIRST/FOLLOW. **FIRST(α)** = terminals that can begin a string derived from α (include ε if α ⇒* ε). **FOLLOW(A)** = terminals that can immediately follow A in some derivation; always includes `$` for the start symbol. Left-recursive grammars are **not** LL(1); must eliminate left recursion. **LR(1)** is strictly stronger (bottom-up; handles left recursion). **Shift-reduce conflict** = parser can both shift and reduce in same state (e.g., dangling-else). **Reduce-reduce** = two valid reductions in same state — worse.

### Bit manipulation
13. `x & (x - 1)` clears the lowest set bit. `x & -x` isolates the lowest set bit. `x` is a power of 2 iff `x > 0 && (x & (x-1)) == 0`. XOR swap: `a ^= b; b ^= a; a ^= b;`. **Popcount** = Brian Kernighan's loop: `while(x){ x &= x-1; count++; }` runs in `O(# set bits)`.

### C pointers
14. For `int *p`, `p + 1` advances by `sizeof(int)` bytes. `p[i]` is literally defined as `*(p + i)`, so `a[i] == *(a+i) == *(i+a) == i[a]` (yes, `i[a]` compiles). Array name decays to `&a[0]` in expressions (not in `sizeof` or unary `&`). Passing an array to a function passes a pointer — `sizeof(arr)` inside callee gives pointer size, not array size.

### Numerical methods
15. **Newton's method:** `x_{n+1} = x_n - f(x_n)/f'(x_n)`; quadratic convergence near a simple root. **Fixed-point iteration** `x_{n+1} = g(x_n)` converges if `|g'(x*)| < 1`. **Absolute error** = `|approx - true|`; **relative error** = `|approx - true| / |true|`. **Catastrophic cancellation:** subtracting near-equal floats loses precision — rewrite formulas (e.g., quadratic: use `-b - sign(b)·√disc` form). **Condition number** measures sensitivity of output to input perturbation; large = ill-conditioned.

### Bonus (fork + Amdahl)
- After `n` sequential `fork()` calls with no `exit()`, there are `2^n` processes total.
- **Amdahl's law:** speedup `= 1 / ((1 - p) + p/s)` with `p` = parallelizable fraction, `s` = speedup on that fraction. Limit as `s → ∞` is `1/(1-p)` — serial fraction caps gain.

---

## Audit Summary

Existing 10 files cover the **core 85%** of the exam thoroughly: Programming, Algorithms, Data Structures, Discrete Math, OS, Architecture, Databases, Theory, SE, Networking are all in strong shape. The **weak belt is the "Other" 3–8% bucket** (HCI, Graphics, AI, Ethics, Crypto) plus a few specific concept holes (Hoare logic, bit tricks, pointer arithmetic, numerical methods, parsing depth).

With 9 days left, the highest-leverage move is a single new `10-other-topics.md` containing items 1–10 of the critical-gap list. That file plus Appendix §6 of this audit should lift expected score by roughly 3–5 raw questions (≈5–10 scaled points).
