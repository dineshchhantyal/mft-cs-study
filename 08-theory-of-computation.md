# Theory of Computation & Formal Languages — MFT CS Study Guide

**Weight on exam:** ~5–10% of ETS MFT CS (May 1, 2026)
**Why it matters:** Easy points if you memorize closure tables + Chomsky hierarchy. MFT loves trick questions on "is this regular / CFL / decidable?"

---

## 1. Foundations

### Alphabets, Strings, Languages

- **Alphabet (Σ):** finite non-empty set of symbols (e.g., {0,1}).
- **String:** finite sequence over Σ. Empty string = **ε** (|ε| = 0).

1. **(Easy)** Which of the following is true about a _superkey_ of a relation?
   A. It must be minimal.
   B. It uniquely identifies every tuple but need not be minimal.
   C. It can contain NULL values in any attribute.
   D. Every relation has exactly one superkey.

   <details>
   <summary>Reveal answer</summary>

   **Answer:** B. A superkey uniquely identifies tuples, but a candidate key adds the minimality requirement.

   </details>

2. **(Easy)** The relational-algebra operator that returns tuples appearing in R but not in S (both union-compatible) is:
   A. σ
   B. π
   C. R − S
   D. R ⋈ S

   <details>
   <summary>Reveal answer</summary>

   **Answer:** C. Set difference `R − S` returns tuples in R but not in S.

   </details>

3. **(Easy)** In SQL, which clause is evaluated _before_ `SELECT`?
   A. ORDER BY
   B. DISTINCT
   C. HAVING
   D. LIMIT

   <details>
   <summary>Reveal answer</summary>

   **Answer:** C. Order: FROM → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT. HAVING runs before SELECT.

   </details>

4. **(Easy)** Given FDs {A → B, B → C}, by Armstrong's axioms we can derive:
   A. C → A
   B. A → C (transitivity)
   C. B → A
   D. AC → B only

   <details>
   <summary>Reveal answer</summary>

   **Answer:** B. Transitivity: A → B and B → C imply A → C.

   </details>

5. **(Easy)** A relation is in **1NF** if:
   A. It has no partial dependencies.
   B. It has no transitive dependencies.
   C. All attribute values are atomic (no repeating groups).
   D. Every determinant is a candidate key.

   <details>
   <summary>Reveal answer</summary>

   **Answer:** C. 1NF means atomic values and no repeating groups. Partial and transitive dependency rules belong to higher normal forms.

   </details>

6. **(Easy)** In an ER diagram, a _weak entity_ is characterized by:
   A. Having no attributes.
   B. Being identified only in combination with its identifying (owner) entity via a partial key.
   C. Participating only in unary relationships.
   D. Always having a total primary key of its own.

   <details>
   <summary>Reveal answer</summary>

   **Answer:** B. Weak entities lack a full key of their own and depend on an identifying relationship plus a partial key.

   </details>

7. **(Easy)** The "D" in ACID guarantees:
   A. Transactions appear to execute serially.
   B. Committed changes survive crashes.
   C. Concurrent transactions don't see each other's writes.
   D. All-or-nothing execution.

   <details>
   <summary>Reveal answer</summary>

   **Answer:** B. Durability means committed changes persist through crashes.

   </details>

8. **(Easy)** Write-Ahead Logging (WAL) requires:
   A. Data pages be flushed to disk before log records.
   B. Log records describing a change be flushed to disk _before_ the corresponding data page.
   C. Logs be written only at commit time.
   D. Checkpoints replace the need for logs.

   <details>
   <summary>Reveal answer</summary>

   **Answer:** B. WAL requires the log record to be on stable storage before the dirty data page.

   </details>

9. **(Medium)** Consider `SELECT COUNT(*) - COUNT(salary) FROM emp;` over a table of 10 rows where 3 rows have `salary IS NULL`. The result is:
   A. 0
   B. 3
   C. 7
   D. 10

   <details>
   <summary>Reveal answer</summary>

   **Answer:** B. `COUNT(*)` = 10 counts all rows; `COUNT(salary)` = 7 ignores NULLs; the difference is 3.

   </details>

10. **(Medium)** Schedule S has edges T1→T2 and T2→T1 in its precedence graph. S is:
    A. Conflict-serializable.
    B. View-serializable only.
    C. Not conflict-serializable (cycle).
    D. Always recoverable.

  <details>
  <summary>Reveal answer</summary>

**Answer:** C. A cycle in the precedence graph means the schedule is not conflict-serializable.

  </details>

11. **(Medium)** Strict 2PL guarantees:
    A. Serializability only.
    B. Serializability + recoverability, and avoids cascading aborts by holding X-locks till commit/abort.
    C. No deadlocks.
    D. Phantom-free reads without index locking.

  <details>
  <summary>Reveal answer</summary>

**Answer:** B. Strict 2PL holds exclusive locks until commit/abort, giving serializability plus recoverability and no cascading aborts. It does not prevent deadlocks.

  </details>

12. **(Medium)** `R(A,B,C,D)` with FDs {AB → C, C → D, D → A}. A candidate key is:
    A. {A,B}
    B. {B,C}
    C. {A,D}
    D. Both A and B (AB and BC are both candidate keys)

  <details>
  <summary>Reveal answer</summary>

**Answer:** D. AB → C → D → A, so AB determines all. Also BC → D → A, so BC is also a candidate key.

  </details>

13. **(Medium)** Which index is _best_ for the query `WHERE salary BETWEEN 50000 AND 70000`?
    A. Hash index on salary.
    B. B+-tree index on salary.
    C. Bitmap index on department.
    D. No index (full scan always wins).

  <details>
  <summary>Reveal answer</summary>

**Answer:** B. Range queries need ordered traversal; B+-trees support ranges, hash indexes do not.

  </details>

14. **(Medium)** In SQL, `SELECT dept, AVG(salary) FROM emp GROUP BY dept HAVING COUNT(*) > 5;` returns:
    A. Each employee's salary if their dept has > 5 people.
    B. Average salary per department, only for departments with more than 5 employees.
    C. Departments with more than 5 distinct salaries.
    D. Error: HAVING cannot use COUNT(\*).

  <details>
  <summary>Reveal answer</summary>

**Answer:** B. GROUP BY forms one row per department, and HAVING filters those groups by aggregate condition.

  </details>

15. **(Medium)** A left outer join `R LEFT JOIN S ON R.x = S.x` on 10 R-rows where only 4 match S will produce:
    A. 4 rows.
    B. 10 rows, with NULLs in S's columns for the 6 non-matching R-rows.
    C. 14 rows.
    D. An error if S has NULLs in x.

  <details>
  <summary>Reveal answer</summary>

**Answer:** B. LEFT OUTER JOIN preserves all left rows and fills unmatched right-side columns with NULL.

  </details>

16. **(Medium)** Under isolation level **READ COMMITTED**, which anomaly is _still possible_?
    A. Dirty read.
    B. Non-repeatable read.
    C. Lost update on the same row within one statement.
    D. Reading uncommitted data.

  <details>
  <summary>Reveal answer</summary>

**Answer:** B. READ COMMITTED blocks dirty reads but still allows non-repeatable reads and phantoms.

  </details>

17. **(Trap)** `SELECT * FROM emp WHERE commission <> 1000;` on a table where some rows have `commission = NULL`. Those NULL rows are:
    A. Returned (NULL ≠ 1000).
    B. Not returned — the predicate evaluates to UNKNOWN, which `WHERE` filters out.
    C. Returned only if `ANSI_NULLS` is OFF.
    D. Cause a runtime error.

  <details>
  <summary>Reveal answer</summary>

**Answer:** B. `NULL <> 1000` is UNKNOWN, not TRUE, so WHERE drops those rows.

  </details>

18. **(Trap)** Relation R(A,B,C) with FDs {A → B, B → C, C → A}. The highest normal form R satisfies is:
    A. 2NF only.
    B. 3NF but not BCNF.
    C. BCNF (every determinant A, B, C is a superkey since all are candidate keys).
    D. Not even 1NF.

  <details>
  <summary>Reveal answer</summary>

**Answer:** C. Each attribute is a candidate key in the cyclic FDs, so every determinant is a superkey and the relation is in BCNF.

  </details>

19. **(Trap)** A transaction T1 runs `SELECT COUNT(*) FROM orders WHERE total > 100;` twice. Between the reads, T2 _inserts_ a new qualifying row and commits. T1 sees different counts. This is:
    A. A dirty read.
    B. A non-repeatable read (same row changed).
    C. A phantom read — prevented only by SERIALIZABLE (or predicate/range locks).
    D. A lost update.

  <details>
  <summary>Reveal answer</summary>

**Answer:** C. An inserted matching row between two predicate reads is a phantom. It requires SERIALIZABLE or predicate/range locks.

  </details>

20. **(Trap)** R(A,B,C,D) with FDs {A → B, C → D}. R is:
    A. In BCNF.
    B. In 3NF but not BCNF (neither A nor C is a superkey).
    C. Not in 2NF (and thus not in 3NF/BCNF) — partial dependencies on the only candidate key {A,C}.
    D. In 1NF only because of transitive dependencies.

  <details>
  <summary>Reveal answer</summary>

**Answer:** C. The only candidate key is {A,C}. A → B and C → D are partial dependencies on that key, so the relation violates 2NF and is only in 1NF.

  </details>

Strict inclusions: **Type 3 ⊊ Type 2 ⊊ Type 1 ⊊ Type 0**.
Also: **Decidable (recursive)** sits strictly between Type 1 and Type 0.

---

## 5. Turing Machines & Computability

### Turing Machine

M = (Q, Σ, Γ, δ, q₀, q_accept, q_reject) where Γ ⊇ Σ ∪ {⊔} is tape alphabet.

- **All variants equivalent in power:** multi-tape, nondeterministic, 2-way infinite tape, RAM — all accept the same class (RE).
- Nondeterministic TM = Deterministic TM in power (but may differ in time).

### Recognizable vs Decidable

- **Turing-recognizable (RE):** some TM accepts exactly L (may loop on non-members).
- **Turing-decidable (recursive):** some TM always halts, accepting L, rejecting ~L.
- **Decidable = RE ∩ co-RE.** If both L and Lᶜ are RE, then L is decidable.

### Halting Problem

HALT = {⟨M, w⟩ : M halts on w}.

- **HALT is RE** (simulate; accept if halts).
- **HALT is undecidable** (Turing, 1936 — diagonalization).
- **HALTᶜ is not even RE.**

**Diagonalization sketch:** Suppose decider H exists. Define D(⟨M⟩) = "if H says M halts on ⟨M⟩, loop; else halt." Then D(⟨D⟩) contradicts.

### Rice's Theorem

Every **non-trivial semantic property** of RE languages is **undecidable**.

- "Non-trivial" = some TM has it, some doesn't.
- "Semantic" = depends only on L(M), not the description of M.
- Examples undecidable by Rice: L(M)=∅, L(M) regular, L(M)=Σ\*, M accepts 42, L(M₁)=L(M₂).
- **Syntactic** properties (e.g., "M has ≥5 states") are NOT covered and may be decidable.

### Reductions

**Mapping (many-one) reduction** A ≤ₘ B: computable f with x ∈ A ⇔ f(x) ∈ B.

- If B decidable ⇒ A decidable.
- Contrapositive: if A undecidable ⇒ B undecidable.
- If B ∈ RE ⇒ A ∈ RE.

---

## 6. Complexity

### Classes

- **P:** decidable in polynomial time by deterministic TM.
- **NP:** verifiable in poly time (equivalently, poly-time nondeterministic TM).
- **co-NP:** complement in NP.
- **PSPACE:** poly space.
- **EXPTIME:** 2^poly(n) time.

### Known inclusions

**P ⊆ NP ⊆ PSPACE ⊆ EXPTIME**
**P ⊆ co-NP ⊆ PSPACE**
**Proven strict:** P ⊊ EXPTIME (time hierarchy theorem). All other inclusions are open (P vs NP is the famous one).

### NP-completeness

L is **NP-hard** if every A ∈ NP reduces (poly-time) to L.
L is **NP-complete** if NP-hard AND L ∈ NP.

**Cook–Levin (1971):** SAT is NP-complete.

**Classic NP-complete problems (memorize):**

- SAT, 3-SAT, CIRCUIT-SAT
- CLIQUE, INDEPENDENT-SET, VERTEX-COVER (all three trivially inter-reducible)
- HAM-PATH, HAM-CYCLE
- TSP (decision version)
- SUBSET-SUM, PARTITION, KNAPSACK (decision)
- 3-COLORING
- SET-COVER

**Not NP-complete (in P):** 2-SAT, matching, Eulerian path, minimum spanning tree, shortest path, linear programming, primality (AKS 2002).

---

## 7. Exam-Trap Cheat Sheet

### Critical "is it regular / CFL / decidable?" reference

| Language               | Regular? | CFL?   | Decidable?      |
| ---------------------- | -------- | ------ | --------------- |
| a\*b\*                 | Yes      | Yes    | Yes             |
| {aⁿbⁿ}                 | **No**   | Yes    | Yes             |
| {aⁿbⁿcⁿ}               | No       | **No** | Yes             |
| {wwᴿ} palindromes      | No       | Yes    | Yes             |
| {ww}                   | No       | **No** | Yes             |
| {aᵖ : p prime}         | No       | No     | Yes             |
| {⟨M,w⟩ : M halts on w} | No       | No     | **No (RE)**     |
| Complement of HALT     | No       | No     | **Not even RE** |

### Closure summary

| Op             | Regular | DCFL | CFL   | Decidable | RE    |
| -------------- | ------- | ---- | ----- | --------- | ----- |
| ∪              | Y       | N    | Y     | Y         | Y     |
| ∩              | Y       | N    | **N** | Y         | Y     |
| complement     | Y       | Y    | **N** | Y         | **N** |
| concat         | Y       | N    | Y     | Y         | Y     |
| \*             | Y       | N    | Y     | Y         | Y     |
| reverse        | Y       | N    | Y     | Y         | Y     |
| hom            | Y       | N    | Y     | N         | Y     |
| ∩ with regular | Y       | Y    | **Y** | Y         | Y     |

---

## 8. Practice MCQs with Solutions

**Q1.** Which of the following is NOT closed under intersection?
(a) Regular languages (b) CFLs (c) Decidable languages (d) RE languages

<details>
<summary>Reveal answer</summary>

**Answer:** (b). CFLs are notoriously not closed under intersection.

</details>

**Q2.** The language L = {aⁿbⁿcⁿ : n ≥ 0} is:
(a) Regular (b) CFL but not regular (c) Decidable but not CFL (d) Undecidable

<details>
<summary>Reveal answer</summary>

**Answer:** (c). Pumping lemma for CFLs shows it's not CFL, and a TM can easily decide it.

</details>

**Q3.** Converting an NFA with n states to a DFA, the DFA has at most how many states?
(a) n (b) n² (c) 2ⁿ (d) n!

<details>
<summary>Reveal answer</summary>

**Answer:** (c). Subset construction.

</details>

**Q4.** Which problem is decidable?
(a) Does TM M halt on input w? (b) Is L(M) = ∅? (c) Is L(M) regular? (d) Does DFA D accept any string?

<details>
<summary>Reveal answer</summary>

**Answer:** (d). DFA emptiness is reachability of an accept state (BFS). The others are undecidable (a: halting; b,c: by Rice).

</details>

**Q5.** If A ≤ₘ B and B is decidable, then:
(a) A is decidable (b) A is RE only (c) A is undecidable (d) nothing can be said

<details>
<summary>Reveal answer</summary>

**Answer:** (a). Fundamental property of many-one reductions.

</details>

**Q6.** Which is NOT known to be NP-complete?
(a) 3-SAT (b) CLIQUE (c) 2-SAT (d) VERTEX-COVER

<details>
<summary>Reveal answer</summary>

**Answer:** (c). 2-SAT ∈ P (Aspvall–Plass–Tarjan, linear time via SCC).

</details>

**Q7.** Regular expression (a|b)\*a(a|b) denotes strings over {a,b} such that:
(a) end in 'a' (b) contain 'ab' (c) have 'a' as second-to-last symbol (d) start with 'a'

<details>
<summary>Reveal answer</summary>

**Answer:** (c). Matches ...a? where ?∈{a,b}, i.e., 'a' in position |w|−1.

</details>

**Q8.** Complement of the halting problem is:
(a) Decidable (b) RE but not decidable (c) co-RE but not RE (d) Both RE and co-RE

<details>
<summary>Reveal answer</summary>

**Answer:** (c). HALT is RE; if HALTᶜ were also RE, HALT would be decidable, which is impossible.

</details>

**Q9.** Let L1 be CFL and L2 be regular. Which is guaranteed to be CFL?
(a) L1 ∩ L2 (b) L1 ∪ L2 (c) Complement of L1 (d) Both (a) and (b)

<details>
<summary>Reveal answer</summary>

**Answer:** (d). CFL ∩ Regular = CFL; CFL ∪ CFL = CFL. Complement of a CFL might not be CFL.

</details>

**Q10.** Rice's theorem implies that:
(a) All TM problems are undecidable (b) All nontrivial semantic properties of RE languages are undecidable (c) Syntactic properties are undecidable (d) Halting is decidable

<details>
<summary>Reveal answer</summary>

**Answer:** (b).

</details>

**Q11.** Myhill–Nerode: minimum DFA for L has k states, where k =
(a) size of any DFA accepting L (b) number of equivalence classes of ≡_L (c) 2^|Σ| (d) always 1

<details>
<summary>Reveal answer</summary>

**Answer:** (b).

</details>

**Q12.** Pumping lemma for regular languages states there exists p such that every string w ∈ L with |w| ≥ p:
(a) is not in L (b) can be split w=xyz with |xy|≤p, |y|≥1, and xyⁱz ∈ L ∀i≥0 (c) has length exactly p (d) is unique

<details>
<summary>Reveal answer</summary>

**Answer:** (b).

</details>

**Q13.** Which inclusion is PROVEN strict?
(a) P ⊊ NP (b) NP ⊊ PSPACE (c) P ⊊ EXPTIME (d) PSPACE ⊊ EXPTIME

<details>
<summary>Reveal answer</summary>

**Answer:** (c). Time hierarchy theorem. The others are open.

</details>

**Q14.** Which language is NOT context-free?
(a) {aⁿbⁿ} (b) {wwᴿ} (c) {ww} (d) {aⁿbᵐ : n≠m}

<details>
<summary>Reveal answer</summary>

**Answer:** (c). {ww} requires matching an arbitrary string to itself, which needs more than a stack.

</details>

**Q15.** The set of all languages over Σ = {0,1} is:
(a) Finite (b) Countably infinite (c) Uncountable (d) Empty

<details>
<summary>Reveal answer</summary>

**Answer:** (c). 2^Σ\* is uncountable; hence most languages are not recognizable.

</details>

---

## 9. Final Memorization Tips

- **"Regular closed under everything; CFL closed under union/concat/star/reverse but NOT intersection or complement."**
- **"CFL ∩ regular = CFL"** — super common MFT trap.
- **"aⁿbⁿ is the canonical non-regular; aⁿbⁿcⁿ is the canonical non-CFL."**
- **"Halting problem: RE, not decidable. Its complement: not even RE."**
- **"Rice: every nontrivial semantic property of L(M) is undecidable."**
- **"P ⊊ EXPTIME is the only proven strict inclusion in the main chain."**
- **"Subset construction: 2ⁿ worst case. Thompson: regex → NFA linearly."**
- **"DPDA < NPDA (unlike DFA = NFA)."**
- **"2-SAT ∈ P; 3-SAT NP-complete" — don't mix up.**

---

## 🧪 Try Yourself — Practice Questions

<details>
<summary>Q1. Minimum DFA states where the third-last symbol is 'a'</summary>

(a) 3 (b) 4 (c) 8 (d) 16

**Answer: (c)**

</details>

<details>
<summary>Q2. Regex equivalent to (a+b)*</summary>

(a) a*b* (b) (a*b*)_ (c) (a+b)(a+b)_ (d) a*+b*

**Answer: (b)**

</details>

<details>
<summary>Q3. Max DFA states from NFA with n states</summary>

(a) n (b) n² (c) 2ⁿ (d) n!

**Answer: (c)**

</details>

<details>
<summary>Q4. Not closed under intersection</summary>

(a) Regular languages (b) Context-free languages (c) Decidable languages (d) Recursively enumerable languages

**Answer: (b)**

</details>

<details>
<summary>Q5. L = {aⁿbⁿcⁿ : n ≥ 0}</summary>

(a) Regular (b) CFL but not regular (c) Context-sensitive but not CFL (d) Not recursively enumerable

**Answer: (c)**

</details>

<details>
<summary>Q6. PDA is more powerful than FA because it has</summary>

(a) More states (b) A stack (c) Two tapes (d) Nondeterminism only

**Answer: (b)**

</details>

<details>
<summary>Q7. DPDA vs NPDA</summary>

(a) DPDA = NPDA (b) DPDA is strictly less powerful (c) DPDA is more powerful (d) Both accept all CFLs

**Answer: (b)**

</details>

<details>
<summary>Q8. Which language is context-free</summary>

(a) {ww} (b) {wwᴿ} (c) {aⁿbⁿcⁿ} (d) {aᵖ}

**Answer: (b)**

</details>

<details>
<summary>Q9. CFL ∩ regular is always</summary>

(a) Regular (b) Context-free (c) Context-sensitive but not CFL (d) Undecidable

**Answer: (b)**

</details>

<details>
<summary>Q10. Which is decidable</summary>

(a) L(M)=∅ (TM) (b) CFG generates ε (c) Two TMs equivalent (d) L(M) regular

**Answer: (b)**

</details>

<details>
<summary>Q11. Rice theorem: decidable property</summary>

(a) L(M) empty (b) L(M) regular (c) M has exactly 10 states (d) L(M) finite

**Answer: (c)**

</details>

<details>
<summary>Q12. Complement of an RE language is</summary>

(a) Always RE (b) Always decidable (c) RE iff original is decidable (d) Never RE

**Answer: (c)**

</details>

<details>
<summary>Q13. Known in P</summary>

(a) 3-SAT (b) 2-SAT (c) Hamiltonian Cycle (d) Subset Sum

**Answer: (b)**

</details>

<details>
<summary>Q14. If A ≤p B and B ∈ P, then</summary>

(a) A is NP-complete (b) A ∈ P (c) A undecidable (d) B NP-complete

**Answer: (b)**

</details>

<details>
<summary>Q15. NP-complete problem</summary>

(a) 2-SAT (b) Sorting (c) 3-SAT (d) Matrix multiplication

**Answer: (c)**

</details>

<details>
<summary>Q16. Pumping lemma for CFLs condition</summary>

(a) |vxy| ≤ p, |vy| ≥ 1, and uvⁱxyⁱz ∈ L ∀i≥0  
(b) |uv| ≤ p and uvⁱz ∈ L  
(c) v = y  
(d) |v| = |y|

**Answer: (a)**

</details>

<details>
<summary>Q17. True statement</summary>

(a) Every RE language is decidable (b) Every decidable language is context-free (c) Every context-free language is decidable (d) Every regular language is finite

**Answer: (c)**

</details>

<details>
<summary>Q18. Type-1 grammars correspond to</summary>

(a) Regular (b) Context-free (c) Context-sensitive (d) Recursively enumerable

**Answer: (c)**

</details>

<details>
<summary>Q19. {⟨M⟩ : L(M) is empty} is</summary>

(a) Decidable (b) RE not decidable (c) co-RE not RE (d) Neither RE nor co-RE

**Answer: (c)**

</details>

<details>
<summary>Q20. True about Halting problem</summary>

(a) Decidable (b) RE but not decidable (c) Complement is RE (d) In P

**Answer: (b)**

</details>
