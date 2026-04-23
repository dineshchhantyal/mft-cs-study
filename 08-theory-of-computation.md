# Theory of Computation & Formal Languages — MFT CS Study Guide

**Weight on exam:** ~5–10% of ETS MFT CS (May 1, 2026)
**Why it matters:** Easy points if you memorize closure tables + Chomsky hierarchy. MFT loves trick questions on "is this regular / CFL / decidable?"

---

## 1. Foundations

### Alphabets, Strings, Languages

- **Alphabet (Σ):** finite non-empty set of symbols (e.g., {0,1}).
- **String:** finite sequence over Σ. Empty string = **ε** (|ε| = 0).
- **Σ\*** = set of all finite strings over Σ (including ε). Always **countably infinite**.
- **Σ⁺** = Σ\* \ {ε}.
- **Language:** any subset L ⊆ Σ\*. Number of possible languages over Σ is **uncountable** (2^ℵ₀) — so most languages are not describable by any finite machine.
- **Kleene star:** A\* = ⋃\_{i≥0} Aⁱ. Note A⁰ = {ε} always, so ε ∈ A\* for every A (even A = ∅; ∅\* = {ε}).

### String operations

- Concatenation: |xy| = |x|+|y|; associative, not commutative.
- Reverse: (xy)ᴿ = yᴿxᴿ.

---

## 2. Regular Languages

### Regular Expressions

**Operators** (precedence high→low): **\* (Kleene)** > **concatenation** > **| (union)**.

**Identities:**

- R | R = R (idempotent)
- (R\*)\* = R\*
- ε | RR\* = R\*
- (R|S)\* = (R\*S\*)\* = (R\* | S\*)\*
- ∅R = R∅ = ∅; εR = Rε = R
- ∅\* = ε\* = ε (denotes {ε})

### DFA — Deterministic Finite Automaton

**5-tuple:** M = (Q, Σ, δ, q₀, F)

- Q: finite states
- Σ: input alphabet
- δ: Q × Σ → Q (**total** function — exactly one transition per symbol)
- q₀ ∈ Q: start state
- F ⊆ Q: accept states

Accepts w iff δ̂(q₀, w) ∈ F.

### NFA (with ε-transitions)

- δ: Q × (Σ ∪ {ε}) → 2^Q (power set).
- Accepts if **some** computation ends in F.
- **Equivalence:** every NFA has an equivalent DFA (subset construction).
  - DFA states = subsets of NFA states → worst-case **2ⁿ** blowup.
  - Some languages genuinely need 2ⁿ DFA states (e.g., "n-th from end is 1").

### Equivalences (all equally powerful)

**Regex ⇔ NFA ⇔ DFA ⇔ Regular Grammar (right-linear)**

- **Thompson's construction:** regex → ε-NFA (linear size).
- **Subset construction:** NFA → DFA (exponential).
- **State elimination** / Arden's lemma: DFA → regex.

### Closure Properties (Regular Languages — all CLOSED)

| Operation            | Regular? |
| -------------------- | -------- |
| Union                | Yes      |
| Intersection         | Yes      |
| Complement           | Yes      |
| Concatenation        | Yes      |
| Kleene star          | Yes      |
| Reverse              | Yes      |
| Homomorphism         | Yes      |
| Inverse homomorphism | Yes      |
| Difference (L1 \ L2) | Yes      |

### Pumping Lemma (Regular)

If L is regular, ∃ pumping length **p** such that every w ∈ L with |w| ≥ p can be split w = xyz with:

1. |xy| ≤ p
2. |y| ≥ 1
3. ∀ i ≥ 0, xyⁱz ∈ L

**Use:** Prove non-regular by contradiction. Adversary picks p; you pick w; adversary picks split; you pick i.

**Classic non-regular examples:**

- {aⁿbⁿ : n ≥ 0}
- {aⁿbⁿcⁿ}
- {wwᴿ : w ∈ {a,b}\*} (palindromes)
- {aᵖ : p prime}
- {aⁿ² : n ≥ 0}
- {w : #a(w) = #b(w)}
- {aⁿbᵐ : n ≠ m} (careful — its complement w.r.t. a\*b\* isn't regular either)

### Myhill–Nerode Theorem

L is regular **iff** the relation "x ≡_L y iff ∀z: xz ∈ L ⇔ yz ∈ L" has **finitely many equivalence classes**. The number of classes = size of the minimum DFA. Gives:

- Lower bounds on DFA size.
- A non-regularity proof technique (exhibit infinitely many distinguishable prefixes).

### DFA Minimization

- Remove unreachable states.
- Merge equivalent states via **table-filling (partition refinement)**: start with {F, Q\F}; split if any symbol leads to different classes. Result is **unique** up to renaming.

---

## 3. Context-Free Languages

### CFG — Context-Free Grammar

G = (V, Σ, R, S) where rules look like A → α with A ∈ V, α ∈ (V ∪ Σ)\*.

- **Leftmost / rightmost derivation:** always expand leftmost / rightmost nonterminal.
- **Parse tree:** yields equivalent derivation up to order.
- **Ambiguous:** some string has ≥2 parse trees. Some CFLs are **inherently ambiguous** (e.g., {aⁱbʲcᵏ : i=j or j=k}).

### PDA — Pushdown Automaton

CFG ≡ **nondeterministic** PDA. **DPDA < NPDA** (deterministic is strictly weaker).

- Example CFL not accepted by any DPDA: {wwᴿ : w ∈ {a,b}\*} (even-length palindromes).
- DCFLs are closed under complement; CFLs are NOT.

### Normal Forms

- **Chomsky Normal Form (CNF):** A → BC or A → a (plus S → ε if ε ∈ L). Enables **CYK parsing** in O(n³).
- **Greibach Normal Form (GNF):** A → aα where α ∈ V\*. Derivations have length = |string|.

### Pumping Lemma for CFLs

If L is CFL, ∃ p such that every w with |w| ≥ p can be split w = uvxyz with:

1. |vxy| ≤ p
2. |vy| ≥ 1
3. ∀ i ≥ 0, uvⁱxyⁱz ∈ L

**Classic non-CFLs:**

- {aⁿbⁿcⁿ}
- {ww : w ∈ {a,b}\*} (note: wwᴿ IS CFL, but ww is not)
- {aⁱbʲcᵏ : i ≤ j ≤ k}
- {aᵖ : p prime}

### Closure Properties (CFLs)

| Operation        | CFL closed?                        |
| ---------------- | ---------------------------------- |
| Union            | **Yes**                            |
| Concatenation    | **Yes**                            |
| Kleene star      | **Yes**                            |
| Reverse          | **Yes**                            |
| Homomorphism     | **Yes**                            |
| **Intersection** | **NO**                             |
| **Complement**   | **NO**                             |
| CFL ∩ Regular    | **YES** (special case — memorize!) |
| Difference       | NO                                 |

**Proof sketch that CFLs not closed under ∩:** {aⁿbⁿcᵐ} and {aᵐbⁿcⁿ} are both CFL; their intersection is {aⁿbⁿcⁿ}, which is not.

---

## 4. Chomsky Hierarchy

| Type  | Name                        | Grammar form        | Accepting machine              | Example         |
| ----- | --------------------------- | ------------------- | ------------------------------ | --------------- |
| **3** | Regular                     | A → aB, A → a       | DFA / NFA                      | a\*b\*          |
| **2** | Context-Free                | A → α (α ∈ (V∪Σ)\*) | NPDA                           | aⁿbⁿ            |
| **1** | Context-Sensitive           | αAβ → αγβ, \|γ\|≥1  | Linear-Bounded Automaton (LBA) | aⁿbⁿcⁿ          |
| **0** | Recursively Enumerable (RE) | any α → β           | Turing Machine                 | halting problem |

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
**Answer: (b).** CFLs are notoriously not closed under intersection.

**Q2.** The language L = {aⁿbⁿcⁿ : n ≥ 0} is:
(a) Regular (b) CFL but not regular (c) Decidable but not CFL (d) Undecidable
**Answer: (c).** Pumping lemma for CFL shows it's not CFL; a TM can easily decide it.

**Q3.** Converting an NFA with n states to a DFA, the DFA has at most how many states?
(a) n (b) n² (c) 2ⁿ (d) n!
**Answer: (c).** Subset construction.

**Q4.** Which problem is decidable?
(a) Does TM M halt on input w? (b) Is L(M) = ∅? (c) Is L(M) regular? (d) Does DFA D accept any string?
**Answer: (d).** DFA emptiness = reachability of an accept state (BFS). The others are undecidable (a: halting; b,c: by Rice).

**Q5.** If A ≤ₘ B and B is decidable, then:
(a) A is decidable (b) A is RE only (c) A is undecidable (d) nothing can be said
**Answer: (a).** Fundamental property of many-one reductions.

**Q6.** Which is NOT known to be NP-complete?
(a) 3-SAT (b) CLIQUE (c) 2-SAT (d) VERTEX-COVER
**Answer: (c).** 2-SAT ∈ P (Aspvall–Plass–Tarjan, linear time via SCC).

**Q7.** Regular expression (a|b)\*a(a|b) denotes strings over {a,b} such that:
(a) end in 'a' (b) contain 'ab' (c) have 'a' as second-to-last symbol (d) start with 'a'
**Answer: (c).** Matches ...a? where ?∈{a,b}, i.e., 'a' in position |w|−1.

**Q8.** Complement of the halting problem is:
(a) Decidable (b) RE but not decidable (c) co-RE but not RE (d) Both RE and co-RE
**Answer: (c).** HALT is RE; if HALTᶜ were also RE, HALT would be decidable — contradiction.

**Q9.** Let L1 be CFL and L2 be regular. Which is guaranteed to be CFL?
(a) L1 ∩ L2 (b) L1 ∪ L2 (c) Complement of L1 (d) Both (a) and (b)
**Answer: (d).** CFL ∩ Regular = CFL; CFL ∪ CFL = CFL. Complement of CFL might not be CFL.

**Q10.** Rice's theorem implies that:
(a) All TM problems are undecidable (b) All nontrivial semantic properties of RE languages are undecidable (c) Syntactic properties are undecidable (d) Halting is decidable
**Answer: (b).**

**Q11.** Myhill–Nerode: minimum DFA for L has k states, where k =
(a) size of any DFA accepting L (b) number of equivalence classes of ≡_L (c) 2^|Σ| (d) always 1
**Answer: (b).**

**Q12.** Pumping lemma for regular languages states there exists p such that every string w ∈ L with |w| ≥ p:
(a) is not in L (b) can be split w=xyz with |xy|≤p, |y|≥1, and xyⁱz ∈ L ∀i≥0 (c) has length exactly p (d) is unique
**Answer: (b).**

**Q13.** Which inclusion is PROVEN strict?
(a) P ⊊ NP (b) NP ⊊ PSPACE (c) P ⊊ EXPTIME (d) PSPACE ⊊ EXPTIME
**Answer: (c).** Time hierarchy theorem. The others are open.

**Q14.** Which language is NOT context-free?
(a) {aⁿbⁿ} (b) {wwᴿ} (c) {ww} (d) {aⁿbᵐ : n≠m}
**Answer: (c).** {ww} requires matching arbitrary string to itself — needs more than a stack.

**Q15.** The set of all languages over Σ = {0,1} is:
(a) Finite (b) Countably infinite (c) Uncountable (d) Empty
**Answer: (c).** 2^Σ\* is uncountable; hence most languages are not recognizable.

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

**Q1.** The minimum number of states in a DFA accepting strings over {a,b} where the third-last symbol is 'a' is:
(a) 3 (b) 4 (c) 8 (d) 16

**Q2.** Which of the following regular expressions is equivalent to (a+b)*?
(a) a*b* (b) (a*b*)* (c) (a+b)(a+b)_ (d) a_+b\*

**Q3.** An NFA with n states is converted to an equivalent DFA. The maximum possible number of states in the DFA is:
(a) n (b) n² (c) 2ⁿ (d) n!

**Q4.** Which of the following is NOT closed under intersection?
(a) Regular languages (b) Context-free languages (c) Decidable languages (d) Recursively enumerable languages

**Q5.** The language L = {aⁿbⁿcⁿ : n ≥ 0} is:
(a) Regular (b) CFL but not regular (c) Context-sensitive but not CFL (d) Not recursively enumerable

**Q6.** A pushdown automaton is more powerful than a finite automaton because it has:
(a) More states (b) A stack (c) Two tapes (d) Nondeterminism only

**Q7.** Which of the following is TRUE about deterministic pushdown automata (DPDA) and nondeterministic pushdown automata (NPDA)?
(a) DPDA = NPDA in power (b) DPDA is strictly less powerful than NPDA (c) DPDA is more powerful than NPDA (d) Both accept all CFLs

**Q8.** Which of the following languages is context-free?
(a) {ww : w ∈ {a,b}_} (b) {wwᴿ : w ∈ {a,b}_} (c) {aⁿbⁿcⁿ} (d) {aᵖ : p is prime}

**Q9.** The intersection of a CFL and a regular language is always:
(a) Regular (b) Context-free (c) Context-sensitive but not CFL (d) Undecidable

**Q10.** Which of the following is decidable?
(a) Whether L(M) = ∅ for a given TM M (b) Whether a given CFG generates ε (c) Whether two TMs accept the same language (d) Whether L(M) is regular

**Q11.** According to Rice's theorem, which property of L(M) is decidable?
(a) Whether L(M) is empty (b) Whether L(M) is regular (c) Whether M has exactly 10 states (d) Whether L(M) is finite

**Q12.** The complement of a recursively enumerable language is:
(a) Always RE (b) Always decidable (c) RE iff the original is decidable (d) Never RE

**Q13.** Which is known to be in P?
(a) 3-SAT (b) 2-SAT (c) Hamiltonian Cycle (d) Subset Sum

**Q14.** If problem A reduces in polynomial time to problem B, and B ∈ P, then:
(a) A ∈ NP-complete (b) A ∈ P (c) A is undecidable (d) B ∈ NP-complete

**Q15.** Which of the following is NP-complete?
(a) 2-SAT (b) Sorting (c) 3-SAT (d) Matrix multiplication

**Q16.** The pumping lemma for CFLs guarantees a splitting w = uvxyz such that:
(a) |vxy| ≤ p, |vy| ≥ 1, and uvⁱxyⁱz ∈ L ∀i≥0 (b) |uv| ≤ p and uvⁱz ∈ L (c) v = y (d) |v| = |y|

**Q17.** Which statement is TRUE?
(a) Every RE language is decidable (b) Every decidable language is context-free (c) Every context-free language is decidable (d) Every regular language is finite

**Q18.** In the Chomsky hierarchy, Type-1 grammars correspond to:
(a) Regular languages (b) Context-free languages (c) Context-sensitive languages (d) Recursively enumerable languages

**Q19.** The language {⟨M⟩ : M is a TM and L(M) is empty} is:
(a) Decidable (b) RE but not decidable (c) co-RE but not RE (d) Neither RE nor co-RE

**Q20.** Which of the following is TRUE about the halting problem?
(a) It is decidable (b) It is RE but not decidable (c) Its complement is RE (d) It is in P

### ✅ Answer Key & Explanations

<details>
<summary>Reveal full answer key</summary>

**Q1. (c) 8.** For the k-th last symbol condition, a DFA needs 2ᵏ states; here k=3 → 2³ = 8.

**Q2. (b) (a*b*)\*.** Generates all strings over {a,b}. (a) misses 'ba'; (c) misses ε; (d) misses mixed strings.

**Q3. (c) 2ⁿ.** Subset construction — each DFA state is a subset of NFA states.

**Q4. (b) CFLs.** Classic trap: CFLs are NOT closed under intersection or complement. All others are closed under intersection.

**Q5. (c).** {aⁿbⁿcⁿ} is the canonical non-CFL but is context-sensitive (and decidable).

**Q6. (b) A stack.** The defining feature of a PDA.

**Q7. (b) DPDA strictly < NPDA.** Unlike DFAs/NFAs, determinism reduces power for PDAs (e.g., {wwᴿ} is CFL but not DCFL).

**Q8. (b) {wwᴿ}.** Classic palindrome-like CFL accepted by NPDA. {ww} is NOT a CFL — the stack can't match a string to itself in order.

**Q9. (b) Context-free.** CFL ∩ Regular = CFL — standard closure property and common MFT trap.

**Q10. (b).** Checking if a CFG generates ε is decidable. All others are undecidable by Rice's theorem or reduction from halting.

**Q11. (c).** Number of states is a syntactic property of M, not a semantic property of L(M), so Rice doesn't apply. All others are nontrivial semantic properties → undecidable.

**Q12. (c).** L is decidable iff both L and Lᶜ are RE (by the RE ∩ co-RE = decidable theorem).

**Q13. (b) 2-SAT.** 2-SAT is in P (via implication graph / SCCs); 3-SAT is NP-complete — key trap.

**Q14. (b) A ∈ P.** Polynomial-time reductions preserve P membership downward.

**Q15. (c) 3-SAT.** Cook–Levin theorem; 2-SAT is in P; sorting and matrix multiplication are in P.

**Q16. (a).** Standard CFL pumping lemma statement — |vxy| ≤ p, |vy| ≥ 1.

**Q17. (c).** Every CFL is decidable (CYK algorithm, O(n³)). (a) false (halting is RE not decidable), (b) false, (d) false.

**Q18. (c) Context-sensitive.** Chomsky hierarchy: Type-0 RE, Type-1 CSL, Type-2 CFL, Type-3 Regular.

**Q19. (c) co-RE but not RE.** E_TM is co-RE (can enumerate non-empty witnesses) but not RE — reduction from HALT complement.

**Q20. (b).** HALT is the canonical RE-but-undecidable language. Its complement is NOT RE (it's co-RE only).

</details>
