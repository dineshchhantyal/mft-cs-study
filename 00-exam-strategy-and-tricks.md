# MFT Computer Science — Exam Strategy & Tricks

**Exam date:** Friday, May 1, 2026
**Today:** April 22, 2026 (9 days out)
**Goal of this file:** everything about HOW to take the exam. Content lives in the topic files.

---

## 1. Exam Format & Logistics (confirmed)

| Item | Value |
|---|---|
| Questions | **66 multiple-choice** (4 options each, A–D; some have 5) |
| Time | **2 hours (120 min)** — may be split into two 1-hour sessions at some schools; confirm with your testing center |
| Grouping | Some questions come in **sets** tied to a shared diagram, graph, code fragment, or table |
| Calculator | **NOT allowed** — no calculators of any kind. Math is designed to be doable by hand |
| Scratch paper | **Provided** by the testing center. You cannot bring your own |
| Electronic devices | Phones, watches, earbuds — all prohibited |
| Scoring scale | **Total score: 120–200** (statistically scaled across forms) |
| Subscores | Reported on 20–100 scale (not always given to students; depends on school) |
| Guessing penalty | **NONE.** Raw score = number correct. **Never leave a blank** |
| Pass/fail | There is no "passing" score from ETS — institutions set their own threshold (if any). 150 ≈ median; 160+ is strong; 170+ is top quartile; 180+ ≈ 95th+ percentile |

### Per-question time budget
- 120 min / 66 Q = **~109 seconds (1 min 49 sec) per question**
- Practical target: **aim for ~90 sec per question** on first pass → leaves ~20 min buffer for harder items and review
- **Pace checkpoints (write these on scratch paper at start):**
  - Q 22 done by minute **40**
  - Q 44 done by minute **80**
  - Q 66 done by minute **115** → last 5 min for flagged questions and filling blanks

### Non-negotiables
- **Bubble an answer for every question before time expires.** If the clock hits 118:00 and you have blanks, fill them all with the same letter (typically C — see guessing heuristics below).
- First pass: **never spend more than 2 minutes** on any single question. Flag and move on.

---

## 2. What's Actually Tested (weights + reality from student reports)

Official ETS content distribution:

| # | Area | ETS % | Approx # questions (of 66) | Rustiness risk (for CS grad) |
|---|---|---|---|---|
| 1 | **Programming Fundamentals** | 21–27% | **14–18** | Medium — pseudocode tracing, recursion, pointers |
| 2 | **Systems** (OS, architecture, networking, concurrency) | 16–24% | **11–16** | High if you haven't touched OS/arch in years |
| 3 | **Algorithms & Complexity** | 16–22% | **11–15** | Medium — Big-O, sorting, graph algos |
| 4 | **Discrete Structures** (logic, sets, graphs, combinatorics, proofs, number theory, discrete prob) | 15–21% | **10–14** | High — proofs + counting get rusty fast |
| 5 | **Software Engineering** | 3–9% | **2–6** | Low — conceptual, common sense mostly |
| 6 | **Information Management** (DB, SQL, normalization, relational algebra) | 3–8% | **2–5** | Medium |
| 7 | **Other** (HCI, graphics, AI, web, professional/ethics) | 3–8% | **2–5** | Low — mostly recognition |

### What students actually report seeing (heavy → rare)
- **Heavy:** code tracing (especially C-style with pointers, arrays, recursion), Big-O of classic algorithms, truth tables / propositional logic, binary trees / BST operations, process scheduling (FCFS, SJF, Round Robin), virtual memory / paging, basic combinatorics, graph traversal (BFS/DFS)
- **Medium:** sorting algorithm properties (stable? in-place? worst case?), regular expressions & DFA/NFA, SQL joins, normalization (1NF/2NF/3NF), TCP vs UDP, OSI layers, concurrency (semaphores, deadlock conditions)
- **Sparse but guaranteed:** Turing machines / computability (halting problem), context-free grammars, HCI principles, graphics (transformations, scanline), ethics / licensing, basic software lifecycle (waterfall vs agile)
- **Rare but possible:** compiler phases, cache coherence, floating point representation, specific network protocols (ARP, DNS), AI (A*, minimax)

### Programming languages on the exam
- Pseudocode is most common. **C / C-like syntax** appears often (pointers, arrays, structs). Occasional Java/Python-flavored snippets. You do not need to write code — only trace it.

---

## 3. 9-Day Pre-Exam Plan (Apr 22 → May 1)

Prioritization formula: **weight × difficulty × your rustiness**

### Day-by-day

**Day 1 — Tue Apr 22 (today)** — Baseline + Discrete
- Take the 16 ETS sample questions cold, no prep. Score yourself. Identify weak areas. (45 min)
- Discrete structures refresh: logic (and/or/implies/iff), truth tables, De Morgan, sets, basic proofs (direct, contradiction, induction). (2–3 hr)

**Day 2 — Wed Apr 23** — Discrete + Combinatorics
- Counting: permutations vs combinations, pigeonhole, inclusion-exclusion. (1.5 hr)
- Graph theory basics: definitions, Euler/Hamilton, trees, adjacency matrix/list. (1.5 hr)
- Discrete probability (expected value, conditional). (1 hr)

**Day 3 — Thu Apr 24** — Algorithms & Complexity
- Big-O/Theta/Omega, master theorem quick form, recurrence → complexity. (1.5 hr)
- Sorting: merge, quick, heap, insertion, bubble, radix — know worst/avg/best, stable, in-place. (1.5 hr)
- Graph algos: BFS, DFS, Dijkstra, Bellman-Ford, MST (Prim/Kruskal), topological sort. (1.5 hr)

**Day 4 — Fri Apr 25** — Programming Fundamentals (HEAVIEST topic)
- C-style pointers, arrays, pass-by-value vs reference. Trace 10 code snippets. (2 hr)
- Recursion: factorial, Fibonacci, Ackermann, tree recursion, tail call. (1.5 hr)
- Data structures: stack, queue, linked list, BST, heap, hash table — operations and complexity. (1.5 hr)

**Day 5 — Sat Apr 26** — Systems Part 1: OS
- Processes vs threads, context switch. (1 hr)
- Scheduling: FCFS, SJF, SRTF, RR, priority, multilevel feedback. Work 2 Gantt chart problems. (1.5 hr)
- Synchronization: race conditions, semaphores, mutexes, monitors, deadlock (4 conditions, prevention vs avoidance, banker's algorithm). (2 hr)

**Day 6 — Sun Apr 27** — Systems Part 2: Memory + Arch + Networking
- Paging, segmentation, TLB, virtual memory, page replacement (FIFO, LRU, optimal). (1.5 hr)
- Cache: direct-mapped vs set-associative, hit/miss, write-through vs write-back. (1 hr)
- Number systems: binary, hex, two's complement, IEEE 754 float basics. (1 hr)
- Networking: OSI/TCP-IP layers, TCP vs UDP, IP subnetting basics, ARP, DNS. (1.5 hr)

**Day 7 — Mon Apr 28** — Theory + Databases + SE
- Regex → DFA/NFA, CFG, pumping lemma (conceptually), Turing machines, halting problem, P vs NP. (2 hr)
- Databases: relational model, SQL (SELECT/JOIN/GROUP BY/HAVING), normalization (1NF/2NF/3NF/BCNF), ER diagrams. (2 hr)
- Software engineering: SDLC models, testing (unit/integration/black-box/white-box), UML basics, design patterns (top 5). (1 hr)

**Day 8 — Tue Apr 29** — Full practice + Other
- Do the ETS sample questions AGAIN + any old GRE CS subject test problems you can find. Timed, 90 sec/question. (2 hr)
- Review every wrong answer and understand WHY. (1.5 hr)
- Skim "Other": HCI principles (Nielsen heuristics), graphics pipeline basics, AI (A*, minimax, alpha-beta). (1 hr)

**Day 9 — Wed Apr 30 (day before)** — Light review only
- Re-read your quick-recall card (section 7). (30 min)
- Redo any 5 problems you got wrong on day 8. (45 min)
- **Stop studying by 6 PM.** Dinner, relax, pack bag, sleep 8 hours.

**Exam Day — Thu May 1** — see section 6.

### What to CUT if time runs short
If you fall behind, drop these first: software engineering minutiae, HCI, ethics, graphics, compiler internals. They are <10% combined. Do NOT cut programming fundamentals or systems — highest EV.

---

## 4. In-Exam Test-Taking Tricks

### 4.1 Elimination for 4-option MCQs
- **Two similar options + two different:** the answer is usually one of the two similar ones (ETS often tests fine distinctions).
- **Opposite options:** one of them is almost always correct. Compare carefully.
- **Extreme wording** ("always," "never," "all," "none") — usually wrong in CS questions, except when stating a well-known definition or a proven theorem.
- **"None of the above" / "All of the above":** ETS uses NOTA sparingly as the correct answer. If you're 50/50 between a concrete option and NOTA, **lean toward the concrete option**. AOTA is more often correct than NOTA when present.
- **Numeric answers:** throw out the largest and smallest first; the right answer is usually one of the middle two (ETS brackets the correct value with close distractors).

### 4.2 When to skip and return
- **Skip if:** you haven't started making progress in 45 seconds, or you realize the problem requires a deep trace of a 30-line code block when you're behind on pace.
- **Mark the question** (circle number on scratch paper AND flag it on the answer sheet if digital).
- **Never leave the bubble blank** — put your best guess even when flagging, so if you run out of time you're covered.

### 4.3 Time management
- **Every 20 questions, check the clock:** Q20 ≈ 36 min, Q40 ≈ 73 min, Q60 ≈ 109 min.
- If you're >5 min behind pace, **force yourself to skip harder questions**.
- **Last 3 minutes:** stop solving. Fill every blank. Review only the ones you flagged as "almost had it."

### 4.4 ETS question-style pattern recognition
Common distractor archetypes — learn to spot and avoid:
- **Off-by-one:** answer is n, distractors include n-1 and n+1. For array/loop problems, trace the boundary carefully.
- **Wrong complexity:** if the correct answer is O(n log n), distractors will be O(n), O(n²), O(log n). Know classic algorithm complexities cold.
- **Base-case confusion:** recursive functions — distractors often come from wrong base case or missing base case.
- **Inverted logic:** distractors swap "if" and "only if," or negate one clause.
- **Right formula, wrong variable:** e.g., number of edges in complete graph is n(n-1)/2; distractor uses n(n+1)/2.
- **Unit swap:** bits vs bytes, milliseconds vs microseconds, Hz vs cycles.
- **Good-sounding but subtly wrong definitions:** especially in OS/networking — e.g., swapping "paging" with "segmentation" effects.

### 4.5 Guessing heuristics (MFT / ETS specific)
- **No penalty — answer every question.**
- If pure random: on a 4-option MCQ, any letter gives 25% EV. Pick one letter and stick with it for blind guesses — reduces decision fatigue. **Default: C.** (On 5-option questions, default C or D.)
- If you eliminated 1 of 4: guessing is worth 33% EV — always guess.
- If you eliminated 2 of 4: 50% EV — definitely guess.
- **Avoid the "changed my mind" trap:** your first instinct is right more often than not unless you spot a concrete error. Only change on a clear reason.

### 4.6 Common traps ETS uses
- **Code with side effects:** watch for `++i` vs `i++`, short-circuit evaluation in `&&`/`||`, integer division truncation.
- **Tree/graph questions with a diagram:** count nodes/edges TWICE — the diagram is often deceptively drawn.
- **"Which is NOT true":** circle the word NOT on your scratch paper. Easy to miss.
- **Asymptotic vs exact:** a question asking for "number of operations" wants exact count; "running time" wants Big-O.
- **Big-O of best case vs worst case vs average:** always verify which the question asks.
- **Stable vs in-place sort mixups.**
- **2's complement sign:** the top bit being 1 means negative — watch for this in bit-manipulation questions.
- **Short-circuit in boolean:** `f() && g()` does not call g if f is false.

---

## 5. Universal Tricks & Reference Tables

### 5.1 Powers of 2 — memorize cold
| n | 2ⁿ |
|---|---|
| 1 | 2 |
| 2 | 4 |
| 3 | 8 |
| 4 | 16 |
| 5 | 32 |
| 6 | 64 |
| 7 | 128 |
| 8 | 256 |
| 9 | 512 |
| 10 | 1,024 (≈10³) |
| 11 | 2,048 |
| 12 | 4,096 |
| 13 | 8,192 |
| 14 | 16,384 |
| 15 | 32,768 |
| 16 | 65,536 (2-byte max+1) |
| 20 | ≈1,048,576 (≈10⁶, 1 Mi) |
| 30 | ≈10⁹ (1 Gi) |
| 32 | ≈4.29 × 10⁹ (unsigned int max+1) |
| 40 | ≈10¹² (1 Ti) |

### 5.2 Log₂ of common numbers
| x | log₂(x) |
|---|---|
| 2 | 1 |
| 4 | 2 |
| 8 | 3 |
| 16 | 4 |
| 32 | 5 |
| 64 | 6 |
| 100 | ≈6.64 |
| 128 | 7 |
| 256 | 8 |
| 1000 | ≈9.97 (~10) |
| 1024 | 10 |
| 10⁶ | ≈20 |
| 10⁹ | ≈30 |

**Rule of thumb:** log₂(n) ≈ 3.32 × log₁₀(n). So log₂(1M) ≈ 20, log₂(1B) ≈ 30.

### 5.3 Complexity cheat sheet (memorize)

| Algorithm | Best | Avg | Worst | Space | Stable? | In-place? |
|---|---|---|---|---|---|---|
| Bubble sort | n | n² | n² | 1 | Yes | Yes |
| Insertion sort | n | n² | n² | 1 | Yes | Yes |
| Selection sort | n² | n² | n² | 1 | No | Yes |
| Merge sort | n log n | n log n | n log n | n | Yes | No |
| Quicksort | n log n | n log n | n² | log n | No | Yes |
| Heapsort | n log n | n log n | n log n | 1 | No | Yes |
| Counting/radix | n+k | n+k | n+k | n+k | Yes | No |

| Data structure | Access | Search | Insert | Delete |
|---|---|---|---|---|
| Array | O(1) | O(n) | O(n) | O(n) |
| Sorted array | O(1) | O(log n) | O(n) | O(n) |
| Linked list | O(n) | O(n) | O(1)* | O(1)* |
| Stack/Queue | — | — | O(1) | O(1) |
| Hash table | — | O(1) avg / O(n) worst | O(1) avg | O(1) avg |
| BST (balanced) | O(log n) | O(log n) | O(log n) | O(log n) |
| BST (unbalanced) | O(n) | O(n) | O(n) | O(n) |
| Heap | O(1) min/max | O(n) | O(log n) | O(log n) |

| Graph algorithm | Complexity |
|---|---|
| BFS / DFS | O(V + E) |
| Dijkstra (binary heap) | O((V+E) log V) |
| Bellman-Ford | O(VE) |
| Floyd-Warshall | O(V³) |
| Prim (binary heap) | O(E log V) |
| Kruskal | O(E log E) |
| Topological sort | O(V + E) |

### 5.4 Key formulas
- Sum 1..n = n(n+1)/2
- Sum 1²..n² = n(n+1)(2n+1)/6
- Geometric series: 1+2+4+…+2ⁿ = 2ⁿ⁺¹ − 1
- Complete graph K_n: n(n−1)/2 edges
- Tree with n nodes: exactly n−1 edges
- Binary tree of height h: at most 2^(h+1) − 1 nodes
- Permutations of n: n!
- Combinations C(n,k) = n! / (k!(n−k)!)
- Master theorem: T(n)=aT(n/b)+f(n). Compare f(n) to n^(log_b a).

### 5.5 Logic identities to have in muscle memory
- ¬(P ∧ Q) ≡ ¬P ∨ ¬Q (De Morgan)
- ¬(P ∨ Q) ≡ ¬P ∧ ¬Q
- P → Q ≡ ¬P ∨ Q ≡ ¬Q → ¬P (contrapositive)
- P ↔ Q ≡ (P → Q) ∧ (Q → P)
- Contrapositive is equivalent; converse and inverse are NOT.

---

## 6. Day-Before & Day-Of Checklist

### Night before (Wed Apr 30)
- [ ] Stop studying by 6 PM. Cramming past this is negative EV.
- [ ] Read the quick-recall card (section 7) once, slowly.
- [ ] Lay out: photo ID, admission ticket/confirmation, 2 sharpened #2 pencils + erasers, water bottle, light snack, sweater/layer.
- [ ] Confirm test location, parking, start time. Plan to arrive **30 min early**.
- [ ] Set two alarms.
- [ ] Protein + carb dinner. No alcohol. Hydrate.
- [ ] In bed by 10 PM. Target 8 hours of sleep. **Sleep > one more hour of review.**

### Morning of (Thu May 1)
- [ ] Wake 2+ hours before exam start.
- [ ] Real breakfast: protein + complex carbs (eggs + oatmeal, or similar). Avoid sugar crash foods.
- [ ] Coffee/tea only if you normally drink it. Don't introduce new caffeine today.
- [ ] Bathroom before check-in (no breaks available once testing begins).
- [ ] Re-read quick-recall card once in the car / waiting room. Then close it and breathe.
- [ ] 2-min box breathing before walking in: inhale 4, hold 4, exhale 4, hold 4.

### First 60 seconds of the exam
- [ ] Write on scratch paper: pace checkpoints (22 / 40 / 80 / 115 min marks).
- [ ] Write powers of 2 (through 2¹⁶) in a corner for reference.
- [ ] Write complexity cheat row for sorting (Merge n log n, Quick n² worst, Heap n log n).
- [ ] Take one deep breath. Begin.

---

## 7. Quick-Recall Card — 30 things you MUST know cold

1. **2¹⁰ = 1024**, 2²⁰ ≈ 1M, 2³⁰ ≈ 1B, 2³² ≈ 4.29B
2. log₂(n) ≈ 3.32 · log₁₀(n)
3. Merge/Heap = O(n log n) worst; Quicksort = O(n²) worst, O(n log n) avg
4. BFS/DFS = O(V+E); Dijkstra = O((V+E) log V); Floyd-Warshall = O(V³)
5. Hash table lookup: **O(1) avg, O(n) worst**
6. Balanced BST: all ops O(log n); unbalanced BST degenerates to O(n)
7. Complete graph K_n has **n(n−1)/2** edges; tree on n nodes has **n−1** edges
8. Sum 1..n = n(n+1)/2; 1+2+4+…+2ⁿ = 2ⁿ⁺¹ − 1
9. Deadlock: **mutual exclusion, hold-and-wait, no preemption, circular wait** (all 4 needed)
10. Page replacement: FIFO, LRU, Optimal (Belady's). FIFO can suffer Belady's anomaly; LRU cannot.
11. TCP = reliable, ordered, connection-oriented; UDP = unreliable, unordered, connectionless
12. OSI layers (bottom→top): **Physical, Data Link, Network, Transport, Session, Presentation, Application** ("Please Do Not Throw Sausage Pizza Away")
13. Normalization: 1NF (atomic), 2NF (no partial dep), 3NF (no transitive dep), BCNF (every determinant is a key)
14. SQL JOINs: INNER, LEFT/RIGHT OUTER, FULL OUTER, CROSS. GROUP BY before HAVING.
15. Halting problem = **undecidable**. P ⊆ NP. NP-complete problems reduce to each other.
16. Regular ⊂ Context-free ⊂ Context-sensitive ⊂ Recursive ⊂ RE
17. Regex/DFA/NFA all recognize regular languages; CFGs recognize CFLs (need stack = PDA)
18. Two's complement: invert bits + 1. MSB = sign. n-bit range: −2ⁿ⁻¹ to 2ⁿ⁻¹−1.
19. IEEE 754 single-precision: 1 sign + 8 exponent + 23 mantissa = 32 bits
20. Short-circuit: `&&` skips right if left false; `||` skips right if left true
21. C: pass-by-value default; arrays decay to pointers when passed
22. Recursion needs **base case**; every recursive call must make progress toward it
23. Stack = LIFO; Queue = FIFO; Priority queue usually implemented with heap
24. BFS uses queue; DFS uses stack (or recursion)
25. MST: Prim (grow from vertex, good for dense), Kruskal (add min edge, good for sparse, uses union-find)
26. Dynamic programming = overlapping subproblems + optimal substructure
27. Divide-and-conquer Master Theorem: T(n)=aT(n/b)+f(n); compare f(n) to n^(log_b a)
28. Pigeonhole: n+1 items into n boxes → some box has ≥2
29. Permutations P(n,k) = n!/(n−k)!; Combinations C(n,k) = n!/(k!(n−k)!)
30. Contrapositive ≡ original; converse and inverse are NOT equivalent to original

---

## 8. Mental State & Pacing Advice

### Mindset
- **This test is scaled and designed for broad CS majors.** You only need ~70% correct to score very well. You can miss ~20 questions and still land around the 80th percentile.
- **You do not need to answer every question correctly. You need to answer every question.**
- Most institutions use the MFT as a program assessment, not an individual gate. If this is not high-stakes for you, treat it as a best-effort exam, not a crisis.
- **Confidence compounds.** Spend the first 10 questions being decisive — they're usually easier and build momentum. Don't get derailed by an early hard question.

### Handling panic / the "stuck brain" moment
- If you blank on a question: skip it, don't dwell. Return later.
- If you blank for >30 seconds on two consecutive questions: **pause for 15 seconds**, close your eyes, one deep breath. Reset is cheaper than 2 minutes of frozen staring.
- If you feel panic rising: physically release your shoulders, unclench your jaw, breathe out longer than you breathe in (exhale twice as long).

### Pacing mantras
- "Ninety seconds, then move."
- "Best guess beats blank."
- "First instinct unless I see a reason."
- "One question doesn't sink the test."

### What great test-takers do differently
- They **commit to an answer and move on**, even with uncertainty — they trust Future-Me to come back if time permits.
- They **read the question twice** before looking at options (prevents distractor anchoring).
- They **cover the answer choices** while working out numeric/code problems, then match — avoids being pulled toward a plausible distractor.
- They **eliminate out loud in their head**: "A wrong because… B wrong because…" — forces rigor.

### Final reminder
Show up rested. Pace yourself. Never leave blanks. Trust your prep.
Good luck.

---

## Sources

- [ETS MFT Computer Science Test Description (PDF)](https://www.ets.org/pdfs/mft/comp-sci-test-description.pdf)
- [ETS MFT CS Sample Questions (PDF)](https://www.ets.org/pdfs/mft/comp-sci-sample-questions.pdf)
- [ETS: How the Tests are Scored](https://www.ets.org/mft/scores-reports/how-the-tests-are-scored.html)
- [ETS Major Field Tests: Computer Science content](https://origin-www.ets.org/mft/about/content/computer_science)
- [ETS Guide to Score Interpretation (PDF)](https://www.ets.org/pdfs/mft/guide_to_scores.pdf)
- [Scratch Robotics: How I Prepare for MFT CS — Part 1](https://scratchrobotics.com/2018/07/08/how-i-prepare-for-ets-major-field-test-computer-science/)
- [Scratch Robotics: Prep MFT CS — Part 2](https://scratchrobotics.com/2019/02/26/prep-mft-cs-story-part-2/)
- [Jin Park's Blog — Qual Exam (Texas A&M)](https://www.jinhyunpark.com/home/qual-exam)
- [Ben Leskey — I took the ETS MFT for CS](https://benleskey.com/blog/cs_mft)
- [UNCW MFT Computer Science FAQ](https://people.uncw.edu/narayans/courses/csc434/MFT%20FAQ.htm)
- [Radford MFT FAQ](https://sites.radford.edu/~nokie/mft.faq.html)
- [Missouri S&T Testing Center — MFT](https://testcenter.mst.edu/testingprograms/majorfieldtest/)
- [Northern Kentucky University — MFT](https://inside.nku.edu/testing/tests/nku-exams/MFT.html)
- [Wartburg MFT CS Test Description (PDF)](https://mcsp.wartburg.edu/letsche/assess/mft_testdesc_compsci_4cmf.pdf)
- [TAMU — MFT CS Sample Questions (PDF)](https://people.engr.tamu.edu/d-walker/Quals/gre_cs_questions_compsci.pdf)
