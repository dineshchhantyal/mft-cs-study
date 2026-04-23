# 11 — Formal Methods, Parsing & Numerical Methods

> **Exam:** ETS MFT CS — May 1, 2026
> **Purpose:** Close three remaining gaps from `AUDIT-gaps.md`: (1) Hoare logic & program verification, (2) Parsing / compiler front-end (deeper than file 01), (3) Numerical methods.
> **How to read:** Each topic is self-contained. Tables summarize; examples illustrate. Do the 18 MCQs at the end cold, then check the key.

---

## Topic 1 — Hoare Logic & Program Verification

Formal method for proving programs correct with respect to a specification. Written by **C.A.R. Hoare (1969)**.

### 1.1 Hoare Triples

A **Hoare triple** asserts that if a precondition holds before a statement executes, a postcondition holds after.

| Notation | Name | Meaning |
|----------|------|---------|
| `{P} S {Q}` | **Partial correctness** | If `P` holds before `S`, and `S` **terminates**, then `Q` holds after. (Says nothing about non-termination.) |
| `[P] S [Q]` | **Total correctness** | If `P` holds before `S`, then `S` terminates **and** `Q` holds after. |

- `P` = **precondition** (assertion on state *before* `S`).
- `Q` = **postcondition** (assertion on state *after* `S`).
- `S` = program / statement.

Total correctness = partial correctness + termination proof.

### 1.2 The Rules (Inference System)

Each rule is a schema: premises above the line, conclusion below.

#### (a) Assignment Axiom (backward substitution)

```
──────────────────────────
  { Q[E/x] }  x := E  { Q }
```

To make `Q` true *after* `x := E`, you must make `Q` with `x` replaced by `E` true *before*. This is the **only** axiom; everything else is a rule.

**Example.** To show `{ ? } x := x+1 { x > 0 }`: substitute `x+1` for `x` in the postcondition → precondition is `x+1 > 0`, i.e., `x > -1`.

#### (b) Sequence (composition)

```
  {P} S1 {R}     {R} S2 {Q}
  ──────────────────────────
        {P} S1; S2 {Q}
```

Chain statements through an **intermediate assertion** `R`.

#### (c) Conditional (if–then–else)

```
  {P ∧ B} S1 {Q}     {P ∧ ¬B} S2 {Q}
  ─────────────────────────────────────
     {P} if B then S1 else S2 {Q}
```

Both branches must establish `Q`; each gets to assume `B` (or its negation).

#### (d) While (loop rule) — the key rule

```
           {I ∧ B} S {I}
  ───────────────────────────────
   {I} while B do S {I ∧ ¬B}
```

- `I` is the **loop invariant** — true before the loop, preserved by each iteration, and true after.
- On exit, guard `B` is false, so we get `I ∧ ¬B`.
- For **total correctness**, add a **variant** (see §1.6).

#### (e) Rule of Consequence (strengthen/weaken)

```
  P ⇒ P'     {P'} S {Q'}     Q' ⇒ Q
  ───────────────────────────────────
               {P} S {Q}
```

- You may **strengthen** the precondition (demand more).
- You may **weaken** the postcondition (promise less).

### 1.3 Loop Invariants — What Makes a Good One

A loop invariant `I` for `while B do S` must satisfy **three conditions**:

1. **Initialization** — `I` holds when the loop is first reached (`P ⇒ I`).
2. **Preservation** — one iteration keeps `I` true: `{I ∧ B} S {I}`.
3. **Usefulness** — on exit, `I ∧ ¬B ⇒ Q` (the invariant plus loop-exit implies postcondition).

**Tip:** an invariant that's too weak fails #3; one that's too strong fails #2. The invariant usually generalizes the postcondition by introducing the loop index.

### 1.4 Worked Examples

#### Example A: Sum 1..n

```c
// Precondition: n ≥ 0
s := 0;
i := 0;
while (i < n) {
    i := i + 1;
    s := s + i;
}
// Postcondition: s = n*(n+1)/2
```

**Invariant:** `I ≡ s = i*(i+1)/2  ∧  0 ≤ i ≤ n`.

- *Init:* after `s:=0; i:=0`, we have `s=0, i=0`, and `0 = 0*1/2`. ✓
- *Preservation:* assume `I ∧ i<n`. After `i:=i+1; s:=s+i`, new `i' = i+1`, new `s' = s + (i+1) = i(i+1)/2 + (i+1) = (i+1)(i+2)/2 = i'(i'+1)/2`. ✓
- *Exit:* `¬(i<n)` and `i ≤ n` give `i = n`, so `s = n(n+1)/2`. ✓

#### Example B: Factorial

```c
// Pre: n ≥ 0
f := 1; k := 0;
while (k < n) { k := k+1; f := f * k; }
// Post: f = n!
```

**Invariant:** `f = k!  ∧  0 ≤ k ≤ n`.

#### Example C: Linear Search

```c
// Pre: A is array[0..n-1]
i := 0;
while (i < n ∧ A[i] ≠ x) i := i+1;
// Post: (i < n ∧ A[i]=x) ∨ (i = n ∧ x ∉ A)
```

**Invariant:** `0 ≤ i ≤ n  ∧  ∀ j. 0 ≤ j < i ⇒ A[j] ≠ x`.

#### Example D: Binary Search (sketch)

```c
// Pre: A sorted ascending, 0 ≤ n
lo := 0; hi := n;
while (lo < hi) {
    m := (lo + hi) / 2;
    if (A[m] < x) lo := m+1;
    else          hi := m;
}
// Post: lo is least index with A[lo] ≥ x (insertion point)
```

**Invariant:** `0 ≤ lo ≤ hi ≤ n  ∧  (∀ j < lo. A[j] < x)  ∧  (∀ j ≥ hi. A[j] ≥ x)`.
**Variant:** `hi − lo` (decreases each iteration).

### 1.5 Weakest Precondition (`wp`)

Dijkstra's **predicate transformer**. `wp(S, Q)` is the weakest (most permissive) precondition ensuring `S` terminates with `Q`.

| Statement `S` | `wp(S, Q)` |
|---------------|------------|
| `skip` | `Q` |
| `x := E` | `Q[E/x]` |
| `S1; S2` | `wp(S1, wp(S2, Q))` |
| `if B then S1 else S2` | `(B ∧ wp(S1,Q)) ∨ (¬B ∧ wp(S2,Q))` |
| `while B do S` | least fixpoint; in practice, supply invariant + variant |

`{P} S {Q}` is valid iff `P ⇒ wp(S, Q)`. Weakest precondition is **backwards**: start from `Q`, push through `S` in reverse.

### 1.6 Termination via Variants

To prove a loop terminates, exhibit a **variant (ranking) function** `V` such that:

1. `V` maps states to a **well-founded set** (typically ℕ; no infinite descending chains).
2. Each iteration strictly **decreases** `V`: `{I ∧ B} V₀ := V; S {V < V₀}`.
3. `V ≥ 0` (or in general, bounded below) while loop runs.

A well-founded order on ℕ means the sequence of `V` values must eventually hit the floor → exit.

**Examples of variants:**
- Sum / factorial loops: `V = n − i`.
- Binary search: `V = hi − lo`.
- Euclid's algorithm: `V = min(a,b)`.

### 1.7 Design by Contract

Introduced by Bertrand Meyer (Eiffel). Each routine has:

- **Precondition** — obligation of caller.
- **Postcondition** — obligation of callee.
- **Class invariant** — must hold before/after every public method.

Languages: Eiffel (native), Java (JML), C# (Code Contracts), Ada/SPARK, Python (`icontract`), Racket (`contract-out`). Assertions (`assert`) are a lightweight runtime form.

### 1.8 Cheat-Sheet

| Concept | One-liner |
|---------|-----------|
| `{P}S{Q}` | partial correctness |
| `[P]S[Q]` | total correctness = partial + termination |
| Assignment | `{Q[E/x]} x:=E {Q}` — substitute into post |
| Loop proof obligations | init, preservation, `I ∧ ¬B ⇒ Q`, variant for termination |
| `wp(x:=E, Q)` | `Q[E/x]` |
| Consequence | strengthen pre, weaken post |
| Variant | decreasing well-founded measure |

---

## Topic 2 — Parsing & Compiler Front-End (Deeper)

Compilation pipeline:

```
Source ─► Lexer ─► Parser ─► AST ─► Semantic Analysis ─► IR ─► Optimizer ─► Code Gen ─► Target
```

### 2.1 Grammar Hierarchy (for parsing)

```
Regular  ⊊  LL(k)  ⊊  LR(k)  ⊊  Deterministic CFG  ⊊  CFG  ⊊  CSG  ⊊  Rec. Enum.
```

| Class | Recognizer | Typical use |
|-------|-----------|-------------|
| Regular | DFA/NFA | tokens (lexer) |
| LL(1) | predictive parser, 1-token lookahead | hand-written recursive descent |
| LR(1), LALR(1) | shift-reduce with states | yacc/bison |
| CFG | CYK, Earley (O(n³)) | natural language, ambiguous DSLs |

Every LL(k) is LR(k), but **not vice-versa**. LR is strictly more powerful.

### 2.2 LL(1) Parsing — Top-Down

- Builds **leftmost derivation**.
- **1-token lookahead**; at each step, decide which production to expand.
- Implemented as **recursive descent** (one function per non-terminal) or table-driven **predictive parser**.
- Cannot handle **left recursion** or **ambiguity**; needs **left factoring** for common prefixes.

### 2.3 LR(k) Parsing — Bottom-Up

- Builds **rightmost derivation in reverse** (handle reduction).
- **Shift-reduce** automaton: push tokens until a production's RHS is on top, then reduce.
- Variants, weakest to strongest:

| Variant | Lookahead | Power | Table size |
|---------|-----------|-------|------------|
| LR(0) | none | weakest | small |
| SLR(1) | FOLLOW sets | weak | small |
| **LALR(1)** | merged LR(1) states | medium | small — **yacc/bison** |
| LR(1) | full | strongest practical | large |

Every LALR(1) grammar is LR(1); merging states can introduce **reduce-reduce conflicts** not present in LR(1).

### 2.4 FIRST and FOLLOW Sets

Given a grammar `G` with non-terminals `N`, terminals `T`, start `S`, and empty string `ε`.

#### FIRST(α) — terminals that can begin a string derived from α

**Algorithm (for non-terminals):** iterate until no change.
```
FIRST(a)     = {a}  for terminal a
FIRST(ε)     = {ε}
For X → Y₁ Y₂ … Yₙ:
    add FIRST(Y₁) − {ε} to FIRST(X)
    if ε ∈ FIRST(Y₁), add FIRST(Y₂) − {ε}; continue …
    if ε ∈ FIRST(Yᵢ) for all i, add ε to FIRST(X)
```

#### FOLLOW(A) — terminals that can appear immediately after `A` in some derivation

```
Put $ (end marker) in FOLLOW(S)
For every production B → α A β:
    add FIRST(β) − {ε} to FOLLOW(A)
    if ε ∈ FIRST(β) or β = ε:
        add FOLLOW(B) to FOLLOW(A)
Iterate to fixpoint.
```

#### Worked example

```
E → T E'
E' → + T E' | ε
T → F T'
T' → * F T' | ε
F → ( E ) | id
```

| X | FIRST(X) | FOLLOW(X) |
|---|----------|-----------|
| E | `(`, `id` | `)`, `$` |
| E' | `+`, `ε` | `)`, `$` |
| T | `(`, `id` | `+`, `)`, `$` |
| T' | `*`, `ε` | `+`, `)`, `$` |
| F | `(`, `id` | `*`, `+`, `)`, `$` |

### 2.5 LL(1) Parse Table Construction

For each production `A → α`:
1. For each `a ∈ FIRST(α)` with `a ≠ ε`: put `A → α` in `M[A, a]`.
2. If `ε ∈ FIRST(α)`: for each `b ∈ FOLLOW(A)`, put `A → α` in `M[A, b]`.

A grammar is **LL(1)** iff no cell gets two productions.

#### What breaks LL(1)

- **Left recursion** `A → A α | β`: predictive parsers loop forever.
  *Fix:* `A → β A'`, `A' → α A' | ε`.
- **Common prefixes** `A → α β | α γ`: can't choose with 1-token lookahead.
  *Fix (left factoring):* `A → α A'`, `A' → β | γ`.
- **Ambiguity** (e.g., dangling else): multiple parses for one string.

### 2.6 Shift-Reduce & Reduce-Reduce Conflicts

In an LR parser's action table:

- **Shift-reduce conflict:** cell has both "shift t" and "reduce A → α". Classic: dangling-else (`if-then` vs `if-then-else`). Usual fix: prefer shift (matches `else` to nearest `if`), or use precedence declarations.
- **Reduce-reduce conflict:** cell has two different reductions. Grammar is genuinely ambiguous at that point (for this parser class) — rewrite grammar.

### 2.7 Transformations

**Eliminate immediate left recursion** `A → A α₁ | … | A αₘ | β₁ | … | βₙ`:
```
A  → β₁ A' | … | βₙ A'
A' → α₁ A' | … | αₘ A' | ε
```

**Left factoring** `A → α β | α γ`:
```
A  → α A'
A' → β | γ
```

### 2.8 Precedence & Associativity in Grammars

Encoded by **stratifying** non-terminals. Higher-precedence operators go in lower levels (closer to leaves). Left-associative ⇒ left-recursive production; right-associative ⇒ right-recursive.

```
E  → E + T  | T            -- + left-associative, low precedence
T  → T * F  | F            -- * left-associative, higher
F  → ( E ) | num
```

In yacc/bison: `%left`, `%right`, `%nonassoc` with order giving precedence (later = higher).

### 2.9 Parse Tree vs Abstract Syntax Tree

| Parse tree (concrete) | AST (abstract) |
|------------------------|----------------|
| Every grammar rule firing is a node | Only semantically meaningful nodes |
| Contains punctuation, parentheses, `ε` | Strips punctuation; structure implicit |
| Large; reflects grammar | Compact; reflects meaning |

`(a + b) * c`: the parse tree contains `(`, `)` nodes; the AST is just `Mul(Add(a,b), c)`.

### 2.10 Semantic Analysis

After parsing, check things context-free grammars can't:

- **Symbol table:** scope-stack, mapping names → types / locations. Opened/closed on block entry/exit.
- **Type checking:** unify declared vs inferred types; coercions; generics.
- **Name resolution:** link uses to declarations, handling shadowing.
- **Other checks:** return in every path, definite assignment, constancy.

### 2.11 Intermediate Representations

| IR | Shape | Use |
|----|-------|-----|
| **Three-Address Code (TAC)** | `t := a op b` | easy to optimize and translate |
| **Quadruples / Triples** | records of op + operands | linear, close to machine |
| **Control-Flow Graph (CFG)** | basic blocks + edges | dataflow analyses |
| **SSA (Static Single Assignment)** | each variable assigned once; φ-nodes at merges | modern optimizers (LLVM, GCC) |

**SSA idea:** rename variables so each has exactly one definition. At CFG join points, insert `x₃ := φ(x₁, x₂)` picking the right version. Enables fast constant propagation, DCE, GVN.

### 2.12 Classical Optimizations

| Optimization | Idea | Example |
|--------------|------|---------|
| **Constant folding** | evaluate const exprs at compile time | `2*3` → `6` |
| **Constant propagation** | replace uses of known-constant vars | `x=3; y=x+1;` → `y=4` |
| **Dead code elimination (DCE)** | remove assignments whose value is never used | `x=5; x=7;` kills first |
| **Common subexpression elimination (CSE)** | reuse prior compute | `(a+b)*c + (a+b)` → `t=a+b; t*c + t` |
| **Loop-invariant code motion (LICM)** | hoist expressions out of loops | compute `m = x+y` once before loop |
| **Strength reduction** | cheaper ops | `i*4` → `i<<2`; mult in loop → add |
| **Inlining** | replace call with body | enables further opts |
| **Tail-call optimization** | reuse stack frame | turns tail recursion into loop |

### 2.13 Register Allocation (Graph Coloring)

Chaitin's approach:

1. Build **interference graph:** node = variable (live range); edge = two vars live simultaneously.
2. **k-color** the graph where `k` = number of registers. Colors = registers.
3. If graph is not k-colorable, **spill** a node (store/load from memory) and retry.

Heuristic: repeatedly remove a node of degree `< k` (push on stack); if none exists, pick a spill candidate.

Graph k-coloring is NP-hard in general; compilers use heuristics.

### 2.14 Code Generation

- Traverse IR, emit target instructions.
- **Instruction selection** (often by tree-pattern matching / BURS).
- **Instruction scheduling** (reorder to fill pipeline slots, avoid stalls).
- **Peephole optimization** on final assembly (window-based local rewrites).

### 2.15 Cheat-Sheet

| Tool | Parser class | Direction |
|------|--------------|-----------|
| Hand-written recursive descent | LL(1) | top-down, leftmost |
| ANTLR | LL(*) | top-down |
| yacc / bison / byacc | LALR(1) | bottom-up, rightmost-reversed |
| GLR parsers | all CFGs | handles ambiguity, merges parses |

---

## Topic 3 — Numerical Methods

Finite-precision arithmetic + iterative algorithms. Concerns: **accuracy**, **stability**, **convergence**, **cost**.

### 3.1 Floating-Point Error

IEEE 754 `double` = 1 sign + 11 exp + 52 mantissa bits; ~15–17 significant decimals.

| Quantity | Meaning |
|----------|---------|
| **Absolute error** | `|x̂ − x|` |
| **Relative error** | `|x̂ − x| / |x|` |
| **Machine epsilon `ε_m`** | smallest `ε` with `1 + ε > 1` in FP. Double: `ε_m ≈ 2⁻⁵² ≈ 2.22·10⁻¹⁶`. |
| **Unit roundoff `u`** | typically `ε_m / 2`. Every real rounds to FP with rel error ≤ `u`. |

Rounding modes: round-to-nearest-even (default), toward 0, +∞, −∞.

### 3.2 Catastrophic Cancellation

Subtracting nearly equal numbers kills significant digits.

**Example.** `x₁ = 1.2345678`, `x₂ = 1.2345670` (7-digit precision). `x₁ − x₂ = 0.0000008`: only **one** significant digit survives.

#### Fix: quadratic formula

Standard `x = (−b ± √(b²−4ac)) / 2a` loses digits in one root when `b² ≫ 4ac`.

**Stable form:** compute the larger-magnitude root first, then use Vieta:
```
q = −½ (b + sign(b)·√(b²−4ac))
x₁ = q / a
x₂ = c / q
```
This avoids the cancellation by using addition (same signs) in `q`, then recovering the other root by product `x₁·x₂ = c/a`.

### 3.3 Error Propagation in Sums — Kahan Summation

Naive `Σxᵢ` accumulates `O(n·u)` relative error.

**Kahan compensated summation** carries a running correction `c`:
```
s = 0; c = 0
for x in xs:
    y = x − c
    t = s + y        # high bits of y may be lost
    c = (t − s) − y  # recover what was lost
    s = t
```
Error becomes `O(u)` (independent of `n`, to leading order). Cost: 4× the work.

### 3.4 Root Finding

Find `x*` with `f(x*) = 0`.

#### Bisection

Needs `f(a)·f(b) < 0` and `f` continuous on `[a,b]`. Halve interval each step.

- **Always converges** (IVT guarantee).
- **Linear convergence:** error halves each step (rate ½).
- Cost: `⌈log₂((b−a)/tol)⌉` iterations.

#### Newton–Raphson

`x_{n+1} = x_n − f(x_n)/f'(x_n)`. Tangent-line intersection with x-axis.

- **Quadratic convergence** (errors square, `|eₙ₊₁| ≈ C|eₙ|²`) near a simple root with `f'(x*) ≠ 0`.
- Needs derivative and a **good initial guess**.
- Can **diverge** or **cycle** (e.g., `f'` near 0, or poor start).
- At a multiple root (multiplicity m ≥ 2), degrades to linear.

#### Secant Method

`x_{n+1} = x_n − f(x_n) · (x_n − x_{n−1}) / (f(x_n) − f(x_{n−1}))`.

- Replaces `f'` by finite difference (derivative-free).
- **Superlinear** convergence, order `φ = (1+√5)/2 ≈ 1.618`.
- Two starting values; no bracketing guarantee.

#### Fixed-Point Iteration

Rewrite `f(x)=0` as `x = g(x)`; iterate `x_{n+1} = g(x_n)`.

- Converges to fixed point `x* = g(x*)` if `|g'(x*)| < 1` (**contraction**).
- **Linear** convergence with rate `|g'(x*)|`.

#### Summary

| Method | Order | Requires | Guarantee |
|--------|-------|----------|-----------|
| Bisection | linear (1) | sign change, continuity | always converges |
| Fixed-point | linear | `|g'|<1` | local |
| Secant | ≈1.618 | two starts | local |
| Newton | 2 (quadratic) | `f'`, good start | local |

### 3.5 Convergence Rates — Definitions

`eₙ = xₙ − x*`. A method has order `p` with rate `C` if `|eₙ₊₁| ≈ C |eₙ|^p` (for large `n`).

- `p = 1, C < 1`: **linear** (error shrinks by constant factor).
- `1 < p < 2`: **superlinear** (e.g., secant p ≈ 1.618).
- `p = 2`: **quadratic** (digits double per step).

### 3.6 Numerical Integration

Estimate `∫_a^b f(x) dx` via sampling.

#### Trapezoidal rule

With `n` subintervals of width `h = (b−a)/n`:
```
T = h · [½f(x₀) + f(x₁) + … + f(x_{n−1}) + ½f(x_n)]
```
Error: `O(h²)`. Exact for linear `f`.

#### Simpson's 1/3 rule

Requires `n` even. Fits parabolas on pairs of intervals:
```
S = (h/3) · [f(x₀) + 4·(f₁+f₃+…) + 2·(f₂+f₄+…) + f(x_n)]
```
Error: `O(h⁴)`. Exact for cubics (one order of "free" accuracy).

| Rule | Poly. degree exact | Error |
|------|--------------------|-------|
| Midpoint | 1 | O(h²) |
| Trapezoidal | 1 | O(h²) |
| Simpson's 1/3 | 3 | O(h⁴) |
| Simpson's 3/8 | 3 | O(h⁴) |
| Gauss–Legendre (n pts) | 2n−1 | very high |

### 3.7 Linear Systems

Solve `A x = b`.

#### Gaussian elimination

Forward-eliminate to upper-triangular `U`, then back-substitute. Cost: `O(n³)` (≈ `2n³/3` flops). Back-substitution: `O(n²)`.

#### Pivoting

Without pivoting, a small pivot (or zero) causes huge rounding errors or failure.

- **Partial pivoting:** at column `k`, swap in the row with largest `|a_{ik}|` (i ≥ k). Standard; gives stability for almost all `A`.
- **Complete pivoting:** also swap columns (largest entry in the remaining submatrix). Even more stable but rarely needed.

#### LU Decomposition

`A = L·U` with `L` unit lower-triangular, `U` upper. Solve `Ax=b` via `Ly=b` then `Ux=y`. With partial pivoting: `P A = L U`.

**Why LU:** you can reuse the factorization for many right-hand sides at `O(n²)` each.

### 3.8 Conditioning vs Stability

| Notion | About | Formal |
|--------|-------|--------|
| **Condition number** `κ(A)` | the *problem* | `κ(A) = ‖A‖ · ‖A⁻¹‖` (in chosen norm) |
| **Stability** | the *algorithm* | small perturbations → small forward error |

- `κ(A)` large ⇒ **ill-conditioned:** tiny changes in `b` cause big changes in `x`. Even a perfect algorithm can't recover lost information.
- An algorithm is **backward stable** if computed answer is the exact answer to a slightly perturbed input.
- Forward error `≲ κ · backward error`. So: `ill-conditioning + stable algorithm = still bad answer`.

Classic ill-conditioned matrix: the **Hilbert matrix** `H_{ij}=1/(i+j−1)`; `κ(H_{10}) ≈ 10¹³`.

### 3.9 Interpolation

Given `(xᵢ, yᵢ)` for `i=0..n`, find polynomial `p` of degree ≤ `n` with `p(xᵢ) = yᵢ`. Unique.

#### Lagrange form

```
p(x) = Σᵢ yᵢ · Lᵢ(x),    Lᵢ(x) = Π_{j≠i} (x − xⱼ)/(xᵢ − xⱼ)
```
Clean, but adding a new point forces recompute: `O(n²)` each time. Evaluation: `O(n²)`.

#### Newton form (divided differences)

```
p(x) = f[x₀] + f[x₀,x₁](x−x₀) + f[x₀,x₁,x₂](x−x₀)(x−x₁) + …
```
Adding a new `(x_{n+1},y_{n+1})` appends **one term**: `O(n)` update (with table of divided diffs).

| Form | Construct | Add a point | Evaluate |
|------|-----------|-------------|----------|
| Lagrange | O(n²) | O(n²) (rebuild) | O(n²) or O(n) via barycentric |
| Newton | O(n²) | O(n) | O(n) (Horner-like nesting) |

#### Runge Phenomenon

Equispaced interpolation at high degree **oscillates wildly** near the endpoints, even for smooth functions. Classic example: `f(x) = 1/(1+25x²)` on `[−1,1]` — error grows as `n→∞`.

**Mitigations:**
- Use **Chebyshev nodes** `xₖ = cos((2k+1)π / (2n+2))` (clustered near endpoints).
- Use **piecewise** interpolation: cubic splines, piecewise linear.
- Limit degree.

### 3.10 Cheat-Sheet

| Rule of thumb | |
|---------------|-|
| Machine epsilon (double) | `≈ 2.22 × 10⁻¹⁶` |
| Avoid subtracting near-equal FP | reformulate |
| Bisection always works, Newton is fast | pair them |
| Simpson beats trapezoid by two orders | `O(h⁴)` vs `O(h²)` |
| Partial pivoting is standard | |
| Ill-conditioned ≠ unstable algorithm | different concepts |
| High-degree equispaced = Runge | use Chebyshev/splines |

---

## 🧪 Try Yourself — Practice Questions

### Hoare Logic (Q1–Q6)

**Q1.** What is the weakest precondition `wp(x := x + 3, x > 10)` ?
 A. `x > 10`   B. `x > 7`   C. `x > 13`   D. `x ≥ 10`

**Q2.** A triple `{P} S {Q}` (partial correctness) is vacuously true when:
 A. `P` is false initially
 B. `S` does not terminate
 C. `Q` is true
 D. Both A and B

**Q3.** For `while B do S`, the three proof obligations for a candidate invariant `I` are:
 A. `P⇒I`; `{I ∧ B} S {I}`; `I ∧ ¬B ⇒ Q`
 B. `I⇒P`; `{I} S {I ∧ B}`; `I ⇒ Q`
 C. `P⇒Q`; `{I} S {Q}`; `¬B ⇒ Q`
 D. `I ∧ B ⇒ Q`; termination; `P⇒Q`

**Q4.** Which is needed for **total** but not partial correctness?
 A. An invariant
 B. A variant (well-founded decreasing measure)
 C. A postcondition
 D. The rule of consequence

**Q5.** Applying the assignment axiom, `{?} y := 2*x + 1 {y > 5}` gives:
 A. `x > 2`   B. `x > 3`   C. `x ≥ 2`   D. `x > 5`

**Q6.** The rule of consequence allows you to:
 A. Strengthen the post and weaken the pre
 B. Weaken the post and strengthen the pre
 C. Weaken both pre and post
 D. Replace an invariant by the postcondition

### Parsing / Compilers (Q7–Q12)

**Q7.** Which parser family does yacc/bison use?
 A. LL(1)   B. LALR(1)   C. SLR(0)   D. Earley

**Q8.** `A → A α | β` is problematic for LL(1) because of:
 A. Left factoring failure
 B. Left recursion (infinite descent)
 C. FOLLOW set collision only
 D. Ambiguity

**Q9.** FIRST(`A B C`) given `ε ∈ FIRST(A)` and `ε ∉ FIRST(B)` equals:
 A. FIRST(A)
 B. FIRST(A) ∪ FIRST(B)
 C. (FIRST(A) − {ε}) ∪ FIRST(B)
 D. FIRST(A) ∪ FIRST(B) ∪ FIRST(C)

**Q10.** A reduce-reduce conflict in an LALR(1) table means:
 A. Two productions' right-hand sides are candidates to reduce on the same lookahead
 B. The grammar has left recursion
 C. Two tokens can be shifted at once
 D. The lexer returned two tokens

**Q11.** Which optimization moves a computation that doesn't depend on the loop index out of the loop?
 A. Dead code elimination
 B. Common subexpression elimination
 C. Loop-invariant code motion
 D. Strength reduction

**Q12.** SSA form is characterized by:
 A. Every variable is assigned at most once (with φ-nodes at merges)
 B. All variables become registers
 C. Elimination of all control flow
 D. Removing the symbol table

### Numerical Methods (Q13–Q18)

**Q13.** Newton's method converges at what order near a simple root?
 A. Linear   B. Superlinear (≈1.618)   C. Quadratic   D. Cubic

**Q14.** Simpson's 1/3 rule is exact for polynomials up to what degree?
 A. 1   B. 2   C. 3   D. 4

**Q15.** "Catastrophic cancellation" occurs when:
 A. Multiplying two huge numbers
 B. Subtracting two nearly equal numbers
 C. Dividing by machine epsilon
 D. Overflow in exponent

**Q16.** The condition number `κ(A)` large means:
 A. The algorithm is unstable
 B. The problem itself amplifies input errors
 C. `A` is singular
 D. Pivoting is impossible

**Q17.** To avoid the Runge phenomenon when interpolating with a high-degree polynomial, use:
 A. More equispaced nodes
 B. Chebyshev nodes (or splines)
 C. Lagrange instead of Newton form
 D. Higher precision arithmetic

**Q18.** Bisection's convergence is:
 A. Quadratic   B. Superlinear   C. Linear (halves each step)   D. Depends on `f`

---

### ✅ Answer Key & Explanations

**Q1 — B.** By the axiom, `wp(x:=x+3, x>10) = (x+3 > 10) = x > 7`.

**Q2 — D.** Partial correctness `{P}S{Q}` is an implication: *if* `P` holds initially *and* `S` terminates, *then* `Q`. It is vacuously true when `P` is false (antecedent false) or when `S` diverges (termination condition fails).

**Q3 — A.** Init `P⇒I`, preservation `{I∧B} S {I}`, usefulness `I∧¬B⇒Q`. (Termination-via-variant is extra, for total correctness.)

**Q4 — B.** Partial correctness doesn't require termination; total correctness adds a well-founded variant.

**Q5 — A.** Substitute: `(2x+1) > 5 ⇔ 2x > 4 ⇔ x > 2`.

**Q6 — B.** You can **strengthen the precondition** (demand more of caller) and **weaken the postcondition** (promise less). Think: `P ⇒ P'`, `Q' ⇒ Q`.

**Q7 — B.** Yacc and bison generate LALR(1) parsers — LR-class tables with merged states, giving compact tables while handling almost all practical grammars.

**Q8 — B.** `A → A α | β` is immediate left recursion; a predictive top-down parser trying `A` would call `A` again with no input consumed and loop forever. Must be rewritten.

**Q9 — C.** Since `ε ∈ FIRST(A)`, you look past `A` to `B`. Because `ε ∉ FIRST(B)`, you stop there. Result: `(FIRST(A) − {ε}) ∪ FIRST(B)`.

**Q10 — A.** Reduce-reduce: on the same lookahead in the same state, two different productions are candidates for reduction. Usually signals grammar ambiguity for the parser class.

**Q11 — C.** LICM hoists loop-invariant expressions. DCE removes unused; CSE reuses repeats; strength reduction replaces costly ops with cheaper ones.

**Q12 — A.** SSA = each variable assigned exactly once; at control-flow joins, φ-functions pick the version. Enables precise dataflow.

**Q13 — C.** Newton has quadratic convergence near a simple root with non-zero derivative. (Secant is superlinear ≈ 1.618; bisection is linear.)

**Q14 — C.** Simpson's 1/3 integrates cubics exactly (one degree higher than its derivation suggests, due to symmetry). Error `O(h⁴)`.

**Q15 — B.** Catastrophic cancellation: `x − y` when `x ≈ y` loses the leading significant digits, leaving only noise.

**Q16 — B.** `κ(A)` is a property of the **problem**: it bounds how much relative input error can be amplified in the solution. An unstable algorithm is separate; a stable algorithm on an ill-conditioned problem still gives a poor answer.

**Q17 — B.** Runge phenomenon is tied to equispaced nodes. Chebyshev nodes cluster near endpoints and avoid the oscillation; piecewise (splines) also fixes it.

**Q18 — C.** Each bisection step halves the bracket: `|eₙ₊₁| = ½ |eₙ|`. Linear with rate ½, always converges under sign-change + continuity.

---

## Final Quick-Recall Box

```
Hoare:       {Q[E/x]} x:=E {Q};   loop = init + preserve + useful (+variant)
WP:          wp(x:=E, Q) = Q[E/x];  backwards from post
Parsers:     LL(1) top-down, LALR(1) bottom-up (yacc);  FIRST/FOLLOW drive tables
Breaks LL1:  left recursion, common prefix, ambiguity
SSA:         one assignment per var + φ at joins
Opts:        CF, CP, DCE, CSE, LICM, strength reduction, inlining
Newton:      quadratic, needs f' and good start;  Secant ≈1.618;  Bisection linear, safe
Simpson:     O(h⁴), exact on cubics;  Trapezoidal O(h²)
κ(A):        problem's sensitivity  ≠  algorithm stability
Runge:       high-degree equispaced oscillates → use Chebyshev / splines
```
