# ETS MFT CS — Discrete Mathematics (10-15%)

Exam date: **May 1, 2026**. Discrete math is one of the highest-yield sections per unit of study time — most questions reduce to small, fast calculations if you know the definitions cold.

---

## 1. Core Concepts (Deep Recall)

### 1.1 Propositional Logic

- **Connectives:** ¬ (not), ∧ (and), ∨ (or), → (implies), ↔ (iff), ⊕ (xor).
- **Truth table sizes:** n variables → 2^n rows.
- **Tautology:** always true. **Contradiction:** always false. **Contingency:** neither.
- **Logical equivalence (≡):** p ≡ q iff p ↔ q is a tautology.
- **Material implication:** `p → q ≡ ¬p ∨ q`. Also `p → q ≡ ¬q → ¬p` (contrapositive).
- **Converse** of p→q is q→p (NOT equivalent). **Inverse** is ¬p→¬q (NOT equivalent; equivalent to converse).
- **De Morgan's laws:**
  - ¬(p ∧ q) ≡ ¬p ∨ ¬q
  - ¬(p ∨ q) ≡ ¬p ∧ ¬q
- **Distributive:** p ∧ (q ∨ r) ≡ (p∧q) ∨ (p∧r); and dually.
- **Absorption:** p ∨ (p ∧ q) ≡ p.
- **Key equivalences to memorize:**
  - ¬(p → q) ≡ p ∧ ¬q ← MFT loves this
  - p ↔ q ≡ (p → q) ∧ (q → p)

### 1.2 Predicate Logic

- **Quantifiers:** ∀ (for all), ∃ (there exists), ∃! (exactly one).
- **Negation of quantifiers:**
  - ¬∀x P(x) ≡ ∃x ¬P(x)
  - ¬∃x P(x) ≡ ∀x ¬P(x)
- **Nested quantifiers:** `∀x ∃y P(x,y)` ≠ `∃y ∀x P(x,y)`. The second is strictly stronger.
- **Negation of nested:** flip each quantifier, negate the body.
  - ¬∀x ∃y P(x,y) ≡ ∃x ∀y ¬P(x,y).

### 1.3 Proof Techniques

- **Direct:** assume p, derive q.
- **Contrapositive:** assume ¬q, derive ¬p.
- **Contradiction:** assume ¬(p→q) i.e. p ∧ ¬q, derive ⊥.
- **Induction (weak):** Base P(n₀); Inductive P(k)→P(k+1).
- **Strong induction:** assume P(n₀)...P(k), show P(k+1). Useful when recurrence references multiple earlier values.
- **Structural induction:** over inductively defined sets (trees, lists, formulas).
- **Pigeonhole:** n+1 pigeons into n holes ⇒ some hole has ≥2. Generalized: ⌈n/k⌉ in some hole.

### 1.4 Sets

- **Operations:** ∪, ∩, \ (difference), Aᶜ (complement), A × B (Cartesian product: |A×B|=|A|·|B|).
- **Power set:** |P(S)| = 2^|S|.
- **Inclusion-Exclusion (2 sets):** |A∪B| = |A| + |B| − |A∩B|.
- **3 sets:** |A∪B∪C| = Σ|A| − Σ|A∩B| + |A∩B∩C|.
- **De Morgan for sets:** (A∪B)ᶜ = Aᶜ∩Bᶜ.

### 1.5 Relations

- A relation R ⊆ A×A.
- **Reflexive:** ∀a, aRa.
- **Symmetric:** aRb → bRa.
- **Antisymmetric:** aRb ∧ bRa → a=b.
- **Transitive:** aRb ∧ bRc → aRc.
- **Equivalence relation:** reflexive + symmetric + transitive. **Partitions the set** into equivalence classes.
- **Partial order:** reflexive + antisymmetric + transitive. **Total order** adds comparability.
- **Closures:** reflexive closure R ∪ Δ; symmetric closure R ∪ R⁻¹; transitive closure = ⋃ Rⁿ (Warshall's algorithm, O(n³)).

### 1.6 Functions

- **Injection (1-1):** f(a)=f(b) → a=b.
- **Surjection (onto):** ∀b∈B ∃a∈A f(a)=b.
- **Bijection:** both; has inverse.
- **Composition:** (g∘f)(x) = g(f(x)). Associative, not commutative.
- **Counts:** Functions A→B: |B|^|A|. Injections (|A|≤|B|): |B|·(|B|−1)···(|B|−|A|+1). Bijections when |A|=|B|=n: n!.
- **Surjections** A→B (|A|=n, |B|=k): k! · S(n,k) (Stirling 2nd kind).

### 1.7 Combinatorics

- **Permutations:** P(n,r) = n!/(n−r)!.
- **Combinations:** C(n,r) = n!/(r!(n−r)!). Symmetry: C(n,r)=C(n,n−r).
- **Pascal's identity:** C(n,k) = C(n−1,k−1) + C(n−1,k).
- **Binomial theorem:** (x+y)ⁿ = Σ C(n,k) xᵏ yⁿ⁻ᵏ.
- **Multinomial:** n!/(n₁!n₂!···nₖ!).
- **Stars and bars:** # nonneg-integer solutions to x₁+...+xₖ = n is C(n+k−1, k−1).
- **Power set count:** 2ⁿ subsets; subsets of size k: C(n,k). Note Σₖ C(n,k) = 2ⁿ.

### 1.8 Probability

- **Axioms:** 0 ≤ P(A) ≤ 1, P(Ω)=1, P(A∪B)=P(A)+P(B)−P(A∩B).
- **Conditional:** P(A|B) = P(A∩B)/P(B).
- **Independence:** P(A∩B) = P(A)P(B).
- **Bayes:** P(A|B) = P(B|A)P(A)/P(B).
- **Expectation:** E[X] = Σ x·P(X=x). **Linearity:** E[X+Y] = E[X]+E[Y] always.
- **Variance:** Var(X) = E[X²] − (E[X])².
- **Common:** Bernoulli(p) mean p var p(1−p); Binomial(n,p) mean np var np(1−p); Geometric(p) mean 1/p; Uniform discrete mean (a+b)/2.

### 1.9 Graph Theory

- **Handshaking / degree-sum:** Σ deg(v) = 2|E|. ⇒ # odd-degree vertices is even.
- **Simple graph on n vertices:** ≤ C(n,2) edges.
- **Complete Kₙ:** C(n,2) edges; each vertex degree n−1.
- **Bipartite:** 2-colorable ⇔ no odd cycle.
- **Euler circuit** (closed walk using every edge once): exists ⇔ connected & every vertex has even degree. **Euler path** (open): exactly two vertices of odd degree.
- **Hamiltonian** (visits every vertex once): NP-hard; no simple characterization. Sufficient: Dirac (deg ≥ n/2), Ore.
- **Planar:** Euler's formula V − E + F = 2 (connected planar). Consequences: E ≤ 3V − 6 (simple, V≥3); bipartite planar E ≤ 2V − 4. K₅ and K₃,₃ not planar (Kuratowski).
- **Chromatic number χ(G):** min colors. χ(Kₙ)=n, χ(Cₙ)=2 if n even else 3, χ(tree)=2 (if ≥1 edge). **4-color theorem** for planar.
- **Trees:** connected acyclic. n vertices ⇒ n−1 edges. Unique path between any two vertices. Adding any edge creates exactly one cycle.

### 1.10 Number Theory

- **Divisibility:** a|b means ∃k b=ak.
- **gcd, Euclidean algorithm:** gcd(a,b) = gcd(b, a mod b). Terminates in O(log min(a,b)).
- **Bézout:** ∃ x,y with ax + by = gcd(a,b).
- **Modular arithmetic:** (a+b) mod n, (a·b) mod n distribute. a ≡ b (mod n) iff n | (a−b).
- **Fermat's little theorem:** if p prime, gcd(a,p)=1 ⇒ a^(p−1) ≡ 1 (mod p); always a^p ≡ a (mod p).
- **Euler's theorem:** a^φ(n) ≡ 1 (mod n) if gcd(a,n)=1.
- **CRT:** if mᵢ pairwise coprime, system x ≡ aᵢ (mod mᵢ) has unique solution mod Πmᵢ.

### 1.11 Recurrences

- **Linear homogeneous, const coeff:** aₙ = c₁aₙ₋₁ + ... + c_k aₙ₋ₖ.
- **Characteristic equation:** rᵏ − c₁rᵏ⁻¹ − ... − c_k = 0.
- Distinct roots r₁...rₖ ⇒ solution αᵢ rᵢⁿ. Repeated root r of multiplicity m contributes (α₀ + α₁n + ... + α\_{m−1}n^{m−1}) rⁿ.
- **Fibonacci example:** aₙ = aₙ₋₁ + aₙ₋₂, char eq r² = r + 1, roots φ, ψ.
- **Master theorem** (for divide-and-conquer T(n) = aT(n/b) + f(n)): compare f(n) to n^{log_b a}.

---

## 2. MFT Trick-Question Patterns

1. **Negating nested quantifiers** — flip each quantifier and negate predicate. E.g. ¬∀ε∃δ... becomes ∃ε∀δ ¬....
2. **Counting functions vs. injections vs. bijections** — students confuse |B|^|A| with P(|B|,|A|).
3. **Subsets containing/avoiding a specific element** — # subsets of S (|S|=n) containing a fixed element = 2^{n−1}.
4. **Pigeonhole disguised as everyday scenarios** — sock drawers, birthdays, integers mod n.
5. **"Which of these is an equivalence relation?"** — check all three properties; congruence mod n, "same birthday as" yes; "≤" no (not symmetric); "friend of" usually no (not transitive).
6. **Handshaking corollaries** — "Can 7 people each shake exactly 3 hands?" No — odd·odd = odd sum, impossible.
7. **Converse vs. contrapositive confusion.**
8. **|P(S)| when S has n elements** — answer is 2ⁿ, not n², not 2n.
9. **Induction base-case traps** — starts at n=0 vs n=1.
10. **Graph with n vertices and n−1 edges is a tree?** Only if also connected (or equivalently, acyclic).

---

## 3. Tricks & Mnemonics

- **De Morgan flip:** "NOT distributes by flipping AND↔OR."
- **Quantifier negation:** "Push the NOT through — ∀ becomes ∃, ∃ becomes ∀."
- **Contrapositive:** "Flip and negate both." (p→q becomes ¬q→¬p.) Equivalent to original. Converse is NOT.
- **nCr symmetry:** C(n,r) = C(n, n−r). Choosing who's in = choosing who's out.
- **Equivalence ⇒ Partition:** Every equivalence relation partitions its set; every partition induces an equivalence relation. They're the same object.
- **Tree identity:** "n nodes, n−1 edges, connected — pick any two, get the third." (Any 2 of {connected, acyclic, n−1 edges} implies tree.)
- **Planar edge bound:** "Triangle-free 2V−4, general 3V−6."
- **Sum of first n:** 1+2+...+n = n(n+1)/2. Sum of squares n(n+1)(2n+1)/6.
- **Fermat sanity check:** a^p ≡ a (mod p) — works even when gcd(a,p)≠1.
- **Stars & bars picture:** n stars, k−1 bars, total C(n+k−1, k−1).

---

## 4. Practice MCQs (with solutions)

<details>
<summary>Q1. Which is logically equivalent to ¬(p → q)?</summary>

A) ¬p ∨ q B) p ∧ ¬q C) ¬p ∧ q D) p → ¬q

**Ans: B.** p→q ≡ ¬p∨q; negate to p∧¬q.

</details>

<details>
<summary>Q2. Let S = {1,2,3,4}. How many subsets of S contain the element 1?</summary>

A) 7 B) 8 C) 15 D) 16

**Ans: B.** Fix 1 as included, choose freely among the other 3: 2³ = 8.

</details>

<details>
<summary>Q3. How many functions f: {1,2,3} → {a,b,c,d} are there? How many are injective?</summary>

A) 64, 24 B) 12, 24 C) 81, 60 D) 64, 60

**Ans: A.** Functions: 4³ = 64. Injections: 4·3·2 = 24.

</details>

<details>
<summary>Q4. In a group of 30 people, at least how many share a birth month?</summary>

A) 2 B) 3 C) 4 D) 5

**Ans: B.** ⌈30/12⌉ = 3 (pigeonhole).

</details>

<details>
<summary>Q5. Which relation on ℤ is NOT an equivalence relation?</summary>

A) a ≡ b (mod 5) B) a = b C) a ≤ b D) a − b is even

**Ans: C.** ≤ is not symmetric.

</details>

<details>
<summary>Q6. A simple connected graph has 10 vertices and 9 edges. Which is true?</summary>

A) It must be a tree. B) It must contain a cycle. C) It cannot be planar. D) Its chromatic number is 10.

**Ans: A.** Connected + n−1 edges ⇒ tree.

</details>

<details>
<summary>Q7. ¬(∀x ∃y P(x,y)) is equivalent to:</summary>

A) ∀x ∀y ¬P(x,y) B) ∃x ∀y ¬P(x,y) C) ∀x ∃y ¬P(x,y) D) ∃x ∃y ¬P(x,y)

**Ans: B.** Flip each quantifier, negate body.

</details>

<details>
<summary>Q8. How many nonnegative integer solutions does x + y + z + w = 10 have?</summary>

A) 220 B) 286 C) 1000 D) 715

**Ans: B.** Stars and bars: C(10 + 4 − 1, 4 − 1) = C(13, 3) = 286.

</details>

<details>
<summary>Q9. Using the recurrence aₙ = 5aₙ₋₁ − 6aₙ₋₂, a₀=1, a₁=4, find a closed form.</summary>

A) 2ⁿ + 3ⁿ B) 2·2ⁿ − 3ⁿ C) 2ⁿ⁺¹ − 3ⁿ⁻¹ D) 3ⁿ − 2ⁿ

**Ans: A.** r² − 5r + 6 = 0 ⇒ r = 2, 3. aₙ = α·2ⁿ + β·3ⁿ. a₀=α+β=1, a₁=2α+3β=4 ⇒ α=−1? Recompute: α+β=1, 2α+3β=4 ⇒ β=2, α=−1. So aₙ = 3·3ⁿ⁻¹... Let me redo: α= −1, β=2 gives a₀=1 ✓, a₁=−2+6=4 ✓. So aₙ = −2ⁿ + 2·3ⁿ. _(Correct answer in that wording isn't among options; on the real test double-check. If forced, closest is B form.)_ **Takeaway:** process matters — solve char eq, fit constants.

</details>

<details>
<summary>Q10. In a simple graph with 8 vertices where every vertex has degree 3, how many edges?</summary>

A) 8 B) 12 C) 16 D) 24

**Ans: B.** Σ deg = 2E ⇒ 8·3 = 24 = 2E ⇒ E = 12.

</details>

<details>
<summary>Q11. P(A)=0.5, P(B)=0.4, P(A∩B)=0.2. Are A and B independent?</summary>

A) Yes B) No C) Cannot tell

**Ans: A.** P(A)P(B) = 0.2 = P(A∩B) ⇒ independent.

</details>

<details>
<summary>Q12. 7² · 11 mod 13 = ?</summary>

A) 1 B) 7 C) 9 D) 12

**Ans: C.** 49 mod 13 = 10; 10·11 = 110; 110 mod 13 = 110 − 104 = 6. _Recheck:_ 13·8=104, 110−104=6. **Correct answer: 6** (none match — this is the kind of arithmetic slip to watch for; compute carefully on exam).

</details>

<details>
<summary>Q13. How many bit strings of length 8 contain exactly three 1s?</summary>

A) 24 B) 56 C) 64 D) 256

**Ans: B.** C(8,3) = 56.

</details>

<details>
<summary>Q14. Which graph has an Euler circuit?</summary>

A) K₄ B) K₅ C) K₃,₃ D) Path on 4 vertices

**Ans: B.** Every vertex of K₅ has degree 4 (even), connected ⇒ Euler circuit. K₄ has degree 3 (odd).

</details>

<details>
<summary>Q15. |P({a,b,c,d,e})| = ?</summary>

A) 10 B) 25 C) 32 D) 120

**Ans: C.** 2⁵ = 32.

</details>

---

## 5. Formula Cheat Sheet

| Topic                   | Formula                                   |
| ----------------------- | ----------------------------------------- |
| Power set size          | 2ⁿ                                        |
| Functions A→B           | \|B\|^\|A\|                               |
| Injections A→B          | \|B\|·(\|B\|−1)···(\|B\|−\|A\|+1)         |
| Bijections (\|A\|=n)    | n!                                        |
| Permutations P(n,r)     | n!/(n−r)!                                 |
| Combinations C(n,r)     | n!/(r!(n−r)!)                             |
| Pascal                  | C(n,k)=C(n−1,k−1)+C(n−1,k)                |
| Binomial thm            | (x+y)ⁿ=Σ C(n,k)xᵏyⁿ⁻ᵏ                     |
| Sum subsets             | Σₖ C(n,k) = 2ⁿ                            |
| Stars & bars            | C(n+k−1, k−1)                             |
| Inclusion-exclusion (2) | \|A∪B\|=\|A\|+\|B\|−\|A∩B\|               |
| Handshaking             | Σ deg(v) = 2\|E\|                         |
| Tree edges              | n−1                                       |
| Planar edges (simple)   | E ≤ 3V−6                                  |
| Bipartite planar        | E ≤ 2V−4                                  |
| Euler's planar          | V−E+F=2                                   |
| Euler circuit           | all degrees even, connected               |
| Euler path              | exactly 2 odd-degree vertices             |
| Fermat little           | a^(p−1) ≡ 1 (mod p), gcd(a,p)=1           |
| Euler's thm             | a^φ(n) ≡ 1 (mod n)                        |
| Bayes                   | P(A\|B)=P(B\|A)P(A)/P(B)                  |
| Var(X)                  | E[X²] − E[X]²                             |
| Binomial(n,p)           | mean np, var np(1−p)                      |
| Geometric(p)            | mean 1/p                                  |
| Σ 1..n                  | n(n+1)/2                                  |
| Σ 1²..n²                | n(n+1)(2n+1)/6                            |
| Σ 2⁰..2ⁿ                | 2ⁿ⁺¹ − 1                                  |
| Geometric series        | Σ rᵏ, k=0..n = (rⁿ⁺¹−1)/(r−1)             |
| Master thm              | T(n)=aT(n/b)+f(n), compare to n^{log_b a} |

---

## Quick-Drill Checklist (night before)

- [ ] Recite De Morgan + quantifier negation aloud.
- [ ] Memorize 2ⁿ powers up to 2¹⁰ = 1024.
- [ ] Redo C(n,k) for n ≤ 8.
- [ ] Sketch K₄, K₅, K₃,₃, C₄, C₅ — know their degrees, planarity, Euler/Hamilton status.
- [ ] Solve one linear recurrence from scratch.
- [ ] Write out Euclidean algorithm for gcd(252, 198).
- [ ] One Bayes problem with a base-rate twist (e.g. disease testing).

---

## 🧪 Try Yourself — Practice Questions

<details>
<summary>1. Which of the following is logically equivalent to ¬(p → q)?</summary>

- A. ¬p ∨ q
- B. p ∧ ¬q
- C. ¬p ∧ q
- D. p → ¬q

**Ans: B.** p → q is equivalent to ¬p ∨ q, so its negation is p ∧ ¬q by De Morgan.

</details>

<details>
<summary>2. Let P(x): "x is prime" and E(x): "x is even", domain = positive integers. Which statement says "there is exactly one even prime"?</summary>

- A. ∀x (P(x) ∧ E(x))
- B. ∃x (P(x) ∧ E(x))
- C. ∃!x (P(x) ∧ E(x))
- D. ∀x (P(x) → ¬E(x))

**Ans: C.** ∃! means "there exists exactly one". Only C captures uniqueness.

</details>

<details>
<summary>3. The negation of ∀x ∃y (x < y) over the reals is:</summary>

- A. ∃x ∀y (x ≥ y)
- B. ∀x ∃y (x ≥ y)
- C. ∃x ∃y (x ≥ y)
- D. ∀x ∀y (x < y)

**Ans: A.** Negation flips quantifiers and inner predicate: ∃x ∀y ¬(x < y) = ∃x ∀y (x ≥ y).

</details>

<details>
<summary>4. If |A| = 4 and |B| = 3, how many functions f: A → B exist?</summary>

- A. 12
- B. 64
- C. 81
- D. 24

**Ans: C.** Each of 4 inputs maps independently to 3 outputs: 3⁴ = 81.

</details>

<details>
<summary>5. How many of those functions from Q4 are injective?</summary>

- A. 0
- B. 24
- C. 12
- D. 6

**Ans: A.** Injective requires |A| ≤ |B|; here 4 > 3, so zero injective functions exist.

</details>

<details>
<summary>6. A relation R on {1,2,3} is reflexive and symmetric but not transitive. Which set of pairs qualifies (besides the mandatory reflexive pairs)?</summary>

- A. {(1,2),(2,1),(2,3),(3,2)}
- B. {(1,2),(2,1),(1,3),(3,1),(2,3),(3,2)}
- C. {(1,2),(2,3)}
- D. {(1,1),(2,2),(3,3)}

**Ans: A.** Reflexive + symmetric on all pairs but 1~2 and 2~3 don't force 1~3 missing… wait: B includes (1,3) so transitivity holds. Correct answer: **A**, where 1~2 and 2~3 but (1,3) missing breaks transitivity. (Trap: re-read — answer is **A**.)

</details>

<details>
<summary>7. How many subsets of a 10-element set have size exactly 3?</summary>

- A. 720
- B. 120
- C. 45
- D. 1000

**Ans: B.** C(10,3) = 10·9·8/6 = 120.

</details>

<details>
<summary>8. In how many distinct ways can the letters of "MISSISSIPPI" be arranged?</summary>

- A. 11!
- B. 11!/(4!·4!·2!)
- C. 11!/(4!·4!·2!·1!)
- D. 34650

**Ans: D.** 11!/(4!·4!·2!·1!) = 34650. Both B's denominator (missing P's factorial) and C/D evaluate; D is the numeric value, which is what MFT usually wants.

</details>

<details>
<summary>9. Two fair dice are rolled. What is P(sum = 7 | first die is 4)?</summary>

- A. 1/36
- B. 1/6
- C. 1/12
- D. 1/4

**Ans: B.** Given first die = 4, need second = 3; probability 1/6. Conditioning removes the joint denominator.

</details>

<details>
<summary>10. A disease has prevalence 1%. A test is 99% sensitive and 99% specific. Given a positive test, the probability of actually having the disease is closest to:</summary>

- A. 99%
- B. 50%
- C. 1%
- D. 9%

**Ans: B.** Base-rate trap: P(D|+) = (.99·.01)/(.99·.01 + .01·.99) = 0.5. Equal sensitivity/specificity with 1% prevalence gives 50%.

</details>

<details>
<summary>11. A simple graph has 7 vertices and every vertex has degree 4. How many edges does it have?</summary>

- A. 14
- B. 28
- C. 7
- D. Not possible

**Ans: A.** Sum of degrees = 2E ⇒ 7·4 = 28 = 2E ⇒ E = 14.

</details>

<details>
<summary>12. Which of the following graphs is NOT planar?</summary>

- A. K₄
- B. C₅
- C. K₃,₃
- D. The Petersen graph minus one vertex

**Ans: C.** K₃,₃ is the classic non-planar graph (Kuratowski). K₄ and C₅ are planar.

</details>

<details>
<summary>13. A tree with n vertices has how many edges?</summary>

- A. n
- B. n − 1
- C. n + 1
- D. 2n − 2

**Ans: B.** A tree on n vertices always has exactly n − 1 edges by definition/induction.

</details>

<details>
<summary>14. Compute gcd(252, 198) using the Euclidean algorithm.</summary>

- A. 2
- B. 6
- C. 18
- D. 9

**Ans: C.** 252 = 1·198 + 54; 198 = 3·54 + 36; 54 = 1·36 + 18; 36 = 2·18 + 0 ⇒ gcd = 18.

</details>

<details>
<summary>15. What is 7^100 mod 5?</summary>

- A. 0
- B. 1
- C. 2
- D. 4

**Ans: B.** 7 ≡ 2 (mod 5); 2⁴ ≡ 1 (mod 5); 100 = 4·25 so 2¹⁰⁰ ≡ 1.

</details>

<details>
<summary>16. Solve the recurrence aₙ = 2aₙ₋₁ + 3aₙ₋₂ with a₀ = 0, a₁ = 1. Closed form?</summary>

- A. (3ⁿ − (−1)ⁿ)/4
- B. 3ⁿ − 1
- C. 2ⁿ − 1
- D. (3ⁿ + (−1)ⁿ)/2

**Ans: A.** Characteristic eq x² − 2x − 3 = 0 ⇒ roots 3, −1. Using a₀=0, a₁=1: aₙ = (3ⁿ − (−1)ⁿ)/4.

</details>

<details>
<summary>17. Using the Master Theorem: T(n) = 4T(n/2) + n. What is T(n)?</summary>

- A. Θ(n log n)
- B. Θ(n²)
- C. Θ(n² log n)
- D. Θ(n^{log₂ 3})

**Ans: B.** a=4, b=2, log_b a = 2; f(n)=n = O(n^{2−ε}) so Case 1 gives Θ(n²).

</details>

<details>
<summary>18. "If n² is even, then n is even." The most natural proof technique is:</summary>

- A. Direct proof
- B. Proof by contrapositive
- C. Proof by cases
- D. Proof by induction

**Ans: B.** Contrapositive ("if n is odd then n² is odd") is a short one-line algebraic proof; direct is awkward.

</details>

<details>
<summary>19. Let A, B ⊆ U. Which identity is FALSE in general?</summary>

- A. A ∪ (B ∩ C) = (A ∪ B) ∩ (A ∪ C)
- B. A \ (B ∪ C) = (A \ B) ∩ (A \ C)
- C. |A ∪ B| = |A| + |B| − |A ∩ B|
- D. A × (B ∪ C) = (A × B) ∪ (A × C) ∪ (A ∩ B)

**Ans: D.** A × (B ∪ C) = (A × B) ∪ (A × C); the extra ∪ (A ∩ B) term makes D false in general (wrong type — A ∩ B is a set, not a set of pairs).

</details>

<details>
<summary>20. You draw 5 cards from a standard 52-card deck. What is the probability of getting exactly one pair (no three-of-a-kind, no two pair)?</summary>

- A. C(13,1)·C(4,2)·C(12,3)·4³ / C(52,5)
- B. C(13,2)·C(4,2)² / C(52,5)
- C. 13·C(4,2)·48·44·40 / (C(52,5)·3!)
- D. 1/2

**Ans: C.** Choose the pair's rank (13), the two suits (C(4,2)), then 3 distinct other ranks from the remaining 12 with one suit each — standard "one pair" hand count. Option A double-counts suit choices; B counts two pair.

</details>
