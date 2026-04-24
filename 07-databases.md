# MFT CS — Databases (~5–8%)

Exam: May 1, 2026. Focus: relational model, SQL, FDs/normalization, transactions/concurrency, indexing.

---

## 1. Relational Model

- **Relation**: a set of tuples over a schema `R(A1:D1, …, An:Dn)`.
- **Tuple** = row; **Attribute** = column; **Domain** = set of allowed values; **Degree** = # attributes; **Cardinality** = # tuples.
- **Schema** (intension, the structure) vs **Instance** (extension, current data).
- Relations are **sets** (no duplicates, no ordering) in pure theory; SQL tables are **bags/multisets** (duplicates allowed unless `DISTINCT`).
- **Closed under operations**: every relational-algebra op returns a relation.

### Keys

| Key           | Definition                                             |
| ------------- | ------------------------------------------------------ |
| Superkey      | Any set of attrs that uniquely identifies a tuple      |
| Candidate key | Minimal superkey (no proper subset is a superkey)      |
| Primary key   | Chosen candidate key (NOT NULL, unique)                |
| Alternate key | Candidate key not chosen as PK                         |
| Foreign key   | Attr(s) referencing a PK in another (or same) relation |
| Composite key | Key made of >1 attribute                               |

- **Entity integrity**: PK ≠ NULL.
- **Referential integrity**: FK must equal some PK value or be NULL.
- On delete/update of referenced row: `CASCADE`, `SET NULL`, `SET DEFAULT`, `RESTRICT`, `NO ACTION`.

---

## 2. Relational Algebra

| Op           | Symbol                 | Meaning                               |
| ------------ | ---------------------- | ------------------------------------- |
| Select       | σ_cond(R)              | Rows matching condition               |
| Project      | π_attrs(R)             | Columns (dedup — set semantics)       |
| Union        | R ∪ S                  | Both (union-compatible)               |
| Intersect    | R ∩ S                  | In both                               |
| Difference   | R − S                  | In R but not S                        |
| Cartesian    | R × S                  | All pairs                             |
| Rename       | ρ                      | Rename relation/attrs                 |
| Natural join | R ⋈ S                  | Equi-join on common attrs, drop dupes |
| Theta join   | R ⋈_θ S                | σ_θ(R × S)                            |
| Equi-join    | special theta with "=" |
| Outer joins  | ⟕ ⟖ ⟗                  | Left / Right / Full                   |
| Division     | R ÷ S                  | "For all" queries                     |

- **Division example**: "students who took all courses." `Took(sid,cid) ÷ Courses(cid)` → sids in Took paired with every cid in Courses.
- σ and π commute only if projection keeps all attrs in σ's condition.
- Selection pushdown is a major query-optimization rule.

---

## 3. SQL

### Logical Evaluation Order

```
FROM → ON → JOIN → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT
```

(Writing order differs: `SELECT … FROM … WHERE … GROUP BY … HAVING … ORDER BY … LIMIT`.)

Key consequence: column **aliases defined in SELECT** cannot be used in `WHERE`/`GROUP BY`/`HAVING` (but can in `ORDER BY`).

### Joins

- **INNER JOIN**: matching rows only.
- **LEFT/RIGHT OUTER**: keep all left/right, fill NULLs.
- **FULL OUTER**: keep all, fill NULLs on both sides.
- **CROSS JOIN**: Cartesian product.
- **SELF JOIN**: table joined with itself (aliases required).
- **Semi-join / anti-join**: via `EXISTS` / `NOT EXISTS`.

### Subqueries

- **Non-correlated**: inner executes once.
- **Correlated**: inner depends on outer row, re-evaluated per outer row.
- `EXISTS` returns T on ≥1 row; ignores NULL weirdness (great replacement for `NOT IN` with NULLs).
- **`NOT IN` pitfall**: if the subquery yields any NULL, the whole predicate is UNKNOWN → no rows returned.

### Aggregates and NULLs

- `COUNT(*)` counts all rows including NULLs.
- `COUNT(col)` skips NULLs.
- `SUM`, `AVG`, `MIN`, `MAX` all **ignore NULLs**.
- `AVG(col)` = `SUM(col) / COUNT(col)`, **not** `/ COUNT(*)`.
- Empty input: `SUM → NULL`, `COUNT → 0`.
- `GROUP BY` treats all NULLs as one group.

### NULL Semantics (3-valued logic)

- `NULL = NULL` → UNKNOWN (never TRUE). Use `IS NULL` / `IS NOT NULL`.
- `TRUE OR UNKNOWN = TRUE`; `FALSE AND UNKNOWN = FALSE`; else UNKNOWN.
- `WHERE` keeps rows only if predicate is TRUE (UNKNOWN is dropped).
- `CHECK` constraints pass on UNKNOWN (opposite of WHERE).
- `UNIQUE`: most DBMS allow multiple NULLs (SQL standard: yes; SQL Server: one).

---

## 4. Functional Dependencies

- **FD** `X → Y`: any two tuples agreeing on X agree on Y.
- **Armstrong's axioms** (sound & complete):
  1. **Reflexivity**: Y ⊆ X ⇒ X → Y
  2. **Augmentation**: X → Y ⇒ XZ → YZ
  3. **Transitivity**: X → Y, Y → Z ⇒ X → Z
- Derived:
  - **Union**: X→Y, X→Z ⇒ X→YZ
  - **Decomposition**: X→YZ ⇒ X→Y, X→Z
  - **Pseudo-transitivity**: X→Y, WY→Z ⇒ WX→Z

### Finding Candidate Keys

1. Compute **attribute closure** X⁺ under FDs.
2. X is a superkey iff X⁺ = all attrs.
3. Candidate key = minimal superkey.
4. **Attributes never on RHS of any FD must be in every candidate key.**

---

## 5. Normalization

| NF   | Condition                                                                                              |
| ---- | ------------------------------------------------------------------------------------------------------ |
| 1NF  | All attributes atomic (no repeating groups / multi-valued cells)                                       |
| 2NF  | 1NF + no **partial dependency** (no non-prime attr depends on part of a composite candidate key)       |
| 3NF  | 2NF + no **transitive dependency**: for every FD X→A (A non-prime), X is a superkey, **or** A is prime |
| BCNF | For every non-trivial FD X→Y, X is a superkey                                                          |
| 4NF  | BCNF + no non-trivial MVD unless LHS is superkey                                                       |
| 5NF  | No non-trivial join dependencies except from candidate keys                                            |

- **BCNF ⊂ 3NF**: BCNF stricter. 3NF tolerates `prime → prime` via non-superkey LHS.
- **Decomposition properties**:
  - **Lossless-join**: decomposing R into R1, R2 is lossless iff R1 ∩ R2 → R1 or R1 ∩ R2 → R2 (common attrs form key of at least one piece).
  - **Dependency-preserving**: every FD can be checked on a single decomposed relation.
- **BCNF decomposition may lose FDs; 3NF synthesis always preserves them.**

---

## 6. ER Model

- **Entity**: object; **Entity set**: collection.
- **Attributes**: simple, composite, multi-valued (double ellipse), derived (dashed).
- **Relationship**: association among entity sets; **degree** = # participants (binary, ternary).
- **Cardinality**: 1:1, 1:N, M:N.
- **Participation**: total (double line) vs partial.
- **Weak entity**: no PK of its own, identified by owner + discriminator (double rectangle, double diamond).
- **ISA (generalization/specialization)**: disjoint vs overlapping; total vs partial coverage.
- **To relational**:
  - M:N relationship → new table with FKs of both sides as composite PK.
  - 1:N → put FK on the many side.
  - Multi-valued attr → new table.
  - Weak entity → include owner's PK in weak's PK.

---

## 7. Transactions — ACID

- **Atomicity**: all-or-nothing (log-based undo).
- **Consistency**: takes DB from valid state to valid state (constraints hold at commit).
- **Isolation**: concurrent txns appear serial.
- **Durability**: committed changes survive crashes (WAL + fsync).

### Schedules & Serializability

- **Serial schedule**: txns run one after another.
- **Serializable**: equivalent to some serial schedule.
- **Conflict serializable**: can be transformed to serial via swaps of non-conflicting ops. Test: **precedence graph** — nodes = txns, edge Ti→Tj if Ti has a conflicting op before Tj's. **Acyclic ⇔ conflict serializable.**
- Conflicting ops: operate on same item and at least one is a **write** (W-R, R-W, W-W).
- **View serializable** ⊇ conflict serializable (rare extra cases via blind writes).
- **Recoverable**: Tj commits only after all Ti it read from have committed.
- **Cascadeless (ACA)**: reads only committed data.
- **Strict**: no read/write of uncommitted data; enables undo via before-images.

### Locking

- **2PL**: growing phase (acquire), shrinking phase (release) — ensures conflict serializability.
- **Strict 2PL**: hold all X-locks until commit/abort — prevents cascading aborts.
- **Rigorous 2PL**: hold all locks (S and X) until commit.
- **Deadlock**: cycle in wait-for graph. Handle via detection (abort victim), prevention (wait-die, wound-wait), timeouts, or lock ordering.

### Timestamp Ordering

- Each txn gets TS at start. For each item: `readTS`, `writeTS`.
- On read/write, if it would violate timestamp order → abort. No deadlocks, but cascading aborts possible.

---

## 8. Isolation Levels (SQL Standard)

| Level | Dirty Read | Non-repeatable Read | Phantom Read |
| ----- | ---------- | ------------------- | ------------ |

| READ UNCOMMITTED | Possible   | Possible            | Possible     |
| READ COMMITTED   | **No**     | Possible            | Possible     |
| REPEATABLE READ  | **No**     | **No**              | Possible     |
| SERIALIZABLE     | **No**     | **No**              | **No**       |

- **Dirty read**: read uncommitted data.

- **Non-repeatable read**: re-read a row → different value.
- **Phantom read**: re-run a range query → different set of rows. Prevented only by **predicate/range locks** or serializable isolation.
- Snapshot isolation (MVCC) prevents dirty/non-repeatable/phantom but allows **write skew** (not fully serializable).

---

## 9. Indexing

- **B+ tree**: balanced, all data in leaves, leaves linked → great for range queries and equality. O(log n).
- **Hash index**: O(1) equality, **no range support**.
- **Clustered index**: table rows physically ordered by key (one per table).
- **Non-clustered (secondary)**: separate structure pointing to rows; can have many.

- **Dense** (entry per record) vs **sparse** (entry per block, must be clustered).
- **Covering index**: includes all needed columns → avoids table lookup.
- Indexes speed up reads, slow down writes (maintenance cost).
- Bad for very small tables or low-selectivity columns (e.g., boolean).

---

## 10. Recovery

- **WAL (write-ahead logging)**: log record flushed to stable storage **before** corresponding data page.
- **Log record**: `<Ti, X, old, new>`.
- **Undo**: revert uncommitted txns (use old value).

- **Redo**: reapply committed txns that didn't make it to disk (use new value).
- **Checkpoint**: flush dirty pages + write checkpoint record → limits recovery scan.
- **ARIES**: analysis → redo (from earliest dirty page LSN) → undo (losers). Uses LSN, PageLSN, DPT, TT.

---

## 11. MFT Trick Patterns

1. **Candidate key**: attribute that's **never on RHS** of any FD must be in every candidate key.
2. **`COUNT(*)` vs `COUNT(col)`**: only `COUNT(*)` includes NULL rows.
3. **`NOT IN` + NULL** in subquery → returns nothing. Prefer `NOT EXISTS`.
4. **BCNF decomposition**: might lose dependency preservation. 3NF always preserves.

5. **Phantoms** need range/predicate locks; row locks alone don't prevent them.
6. **Conflict-serializable test**: precedence graph acyclicity.
7. **Lossless decomposition test**: R1 ∩ R2 is a superkey of R1 or R2.
8. **`HAVING` vs `WHERE`**: WHERE filters rows; HAVING filters groups (can use aggregates).
9. **`GROUP BY`**: every non-aggregated SELECT column must appear in GROUP BY (standard SQL).
10. **Outer join cardinality**: `|LEFT JOIN|` ≥ `|INNER JOIN|`.

---

## 12. Practice MCQs

**Q1.** R(A,B,C,D) with FDs: A→B, B→C, C→D. Candidate keys?
- (a) A (b) AB (c) AC (d) ABCD

<details>
<summary>Reveal answer</summary>

**Answer:** (a) A. A+ = ABCD. B, C, D appear on RHS; A does not, so A must be in every key and alone determines all attributes.

</details>

**Q2.** Table T(id, val) has rows (1,10), (2,NULL), (3,20). What does `SELECT COUNT(*), COUNT(val), SUM(val), AVG(val) FROM T;` return?

<details>
<summary>Reveal answer</summary>

**Answer:** 3, 2, 30, 15. `COUNT(*)` counts all rows, `COUNT(val)` ignores NULL, and `AVG` uses non-NULL count.

</details>

**Q3.** R(A,B,C) with FDs A→B, B→C. Highest normal form?
- (a) 1NF (b) 2NF (c) 3NF (d) BCNF

<details>
<summary>Reveal answer</summary>

**Answer:** (b) 2NF. Key is A; B→C creates transitive dependency, violating 3NF.

</details>

**Q4.** Schedule: T1:R(A); T2:W(A); T1:W(A); T2:Commit; T1:Commit. Conflict-serializable?

<details>
<summary>Reveal answer</summary>

**Answer:** No. Conflicts create T1→T2 and T2→T1 in the precedence graph, so there is a cycle.

</details>

**Q5.** Which isolation level allows phantoms but not non-repeatable reads?

<details>
<summary>Reveal answer</summary>

**Answer:** REPEATABLE READ.

</details>

**Q6.** Which SQL clause filters groups after aggregation?
- (a) WHERE (b) HAVING (c) ORDER BY (d) DISTINCT

<details>
<summary>Reveal answer</summary>

**Answer:** (b) HAVING.

</details>

**Q7.** Which join keeps all rows from the left table and fills unmatched right columns with NULL?
- (a) INNER (b) LEFT OUTER (c) RIGHT OUTER (d) CROSS

<details>
<summary>Reveal answer</summary>

**Answer:** (b) LEFT OUTER JOIN.

</details>

**Q8.** READ COMMITTED prevents which anomaly?
- (a) Phantom read (b) Dirty read (c) Non-repeatable read (d) Write skew

<details>
<summary>Reveal answer</summary>

**Answer:** (b) Dirty read.

</details>

**Q9.** Why can `WHERE commission <> 1000` exclude rows with `commission = NULL`?

<details>
<summary>Reveal answer</summary>

**Answer:** `NULL <> 1000` is UNKNOWN, and WHERE keeps only TRUE.

</details>

**Q10.** `SELECT dept, COUNT(*) FROM emp GROUP BY dept HAVING COUNT(*) > 5 ORDER BY dept;` on an empty `emp` table returns?

<details>
<summary>Reveal answer</summary>

**Answer:** Zero rows, because no groups are formed.

</details>

**Q11.** Which property does strict 2PL guarantee beyond plain 2PL?

<details>
<summary>Reveal answer</summary>

**Answer:** No cascading aborts and recoverability/strictness by holding X-locks to commit/abort.

</details>

**Q12.** `WHERE col NOT IN (SELECT x FROM T)` returns nothing. Most likely reason?

<details>
<summary>Reveal answer</summary>

**Answer:** Subquery produced a NULL, making comparisons UNKNOWN. `NOT EXISTS` is safer.

</details>

**Q13.** R1(A,B) and R2(B,C). For decomposition of R(A,B,C), when is join lossless?

<details>
<summary>Reveal answer</summary>

**Answer:** Lossless iff B→A or B→C (common attributes form a key of one side).

</details>

**Q14.** Best index for `WHERE salary BETWEEN 1000 AND 2000`?
- (a) Hash index on salary (b) B+ tree on salary (c) Bitmap on dept (d) No index

<details>
<summary>Reveal answer</summary>

**Answer:** (b) B+ tree, because ordered leaves support range scans.

</details>

---

## 13. Cheat Sheets

### Normal Forms (one-liner)

| NF   | Rule of Thumb                                                   |
| ---- | --------------------------------------------------------------- |
| 1NF  | Atomic cells                                                    |
| 2NF  | No partial key dependencies                                     |
| 3NF  | Non-key attrs depend on **key, whole key, nothing but the key** |
| BCNF | Every non-trivial FD's LHS is a superkey                        |
| 4NF  | Same, extended to MVDs                                          |

### Isolation Levels

| Level            | Dirty | Non-rep | Phantom |
| ---------------- | ----- | ------- | ------- |
| READ UNCOMMITTED | Y     | Y       | Y       |
| READ COMMITTED   | N     | Y       | Y       |
| REPEATABLE READ  | N     | N       | Y       |
| SERIALIZABLE     | N     | N       | N       |

### Conflict Rules (for Serializability)

Two ops conflict iff: same item **AND** different transactions **AND** at least one is a write.

### Armstrong's Axioms

Reflexivity · Augmentation · Transitivity (→ derive Union, Decomposition, Pseudo-transitivity).

### NULL Rules

- `x = NULL` → UNKNOWN. Use `IS NULL`.
- Aggregates skip NULL (except `COUNT(*)`).
- `WHERE` drops UNKNOWN; `CHECK` keeps UNKNOWN.

### SELECT Evaluation Order

FROM → JOIN/ON → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT.

### Finding Candidate Keys — Procedure

1. Attrs never on RHS → must be in every key.
2. Attrs never on LHS or RHS → must be in every key.
3. Compute closure of minimal candidate sets; minimal ones = candidate keys.

### Decomposition Lossless Test

R → R1, R2 lossless iff (R1 ∩ R2) → R1 or (R1 ∩ R2) → R2.

---

**Study tip:** On the MFT, database questions usually hit (1) SQL output with NULL/aggregate tricks, (2) "highest normal form" given FDs, (3) isolation-level effect table, (4) precedence-graph/serializability. Memorize the four tables above.

---

## 🧪 Try Yourself — Practice Questions

**Q1. Superkey definition**

A. It must be minimal.  
B. It uniquely identifies every tuple but need not be minimal.  
C. It can contain NULL values in any attribute.  
D. Every relation has exactly one superkey.

<details>
<summary>Reveal answer</summary>

**Answer: B**

</details>

**Q2. Relational algebra difference operator**

A. σ  
B. π  
C. R − S  
D. R ⋈ S

<details>
<summary>Reveal answer</summary>

**Answer: C**

</details>

**Q3. Clause evaluated before SELECT**

A. ORDER BY  
B. DISTINCT  
C. HAVING  
D. LIMIT

<details>
<summary>Reveal answer</summary>

**Answer: C**

</details>

**Q4. From A→B and B→C, derive**

A. C → A  
B. A → C  
C. B → A  
D. AC → B only

<details>
<summary>Reveal answer</summary>

**Answer: B**

</details>

**Q5. 1NF means**

A. No partial dependencies.  
B. No transitive dependencies.  
C. Atomic values only.  
D. Every determinant is a candidate key.

<details>
<summary>Reveal answer</summary>

**Answer: C**

</details>

**Q6. Weak entity characteristic**

A. No attributes.  
B. Identified with owner + partial key.  
C. Unary only.  
D. Full key of its own.

<details>
<summary>Reveal answer</summary>

**Answer: B**

</details>

**Q7. ACID D stands for**

A. Serializable behavior.  
B. Committed data survives crashes.  
C. No visibility across txns.  
D. All-or-nothing.

<details>
<summary>Reveal answer</summary>

**Answer: B**

</details>

**Q8. WAL requires**

A. Flush data page first.  
B. Flush log record first.  
C. Log only at commit.  
D. Checkpoint replaces logs.

<details>
<summary>Reveal answer</summary>

**Answer: B**

</details>

**Q9. COUNT(*) - COUNT(salary) with 10 rows, 3 NULL salary**

A. 0  
B. 3  
C. 7  
D. 10

<details>
<summary>Reveal answer</summary>

**Answer: B**

</details>

**Q10. Precedence graph has T1→T2 and T2→T1**

A. Conflict-serializable  
B. View-serializable only  
C. Not conflict-serializable  
D. Always recoverable

<details>
<summary>Reveal answer</summary>

**Answer: C**

</details>

**Q11. Strict 2PL guarantees**

A. Serializability only  
B. Serializability + recoverability + no cascading aborts  
C. No deadlocks  
D. Phantom-free without range locks

<details>
<summary>Reveal answer</summary>

**Answer: B**

</details>

**Q12. Candidate key for R(A,B,C,D), FDs {AB→C, C→D, D→A}**

A. {A,B}  
B. {B,C}  
C. {A,D}  
D. Both AB and BC

<details>
<summary>Reveal answer</summary>

**Answer: D**

</details>

**Q13. Best index for salary BETWEEN query**

A. Hash on salary  
B. B+-tree on salary  
C. Bitmap on department  
D. No index

<details>
<summary>Reveal answer</summary>

**Answer: B**

</details>

**Q14. GROUP BY + HAVING query returns**

A. Employee rows  
B. Avg salary per dept with count > 5  
C. Depts with >5 distinct salaries  
D. HAVING error

<details>
<summary>Reveal answer</summary>

**Answer: B**

</details>

**Q15. LEFT JOIN with 10 left rows and 4 matches produces**

A. 4 rows  
B. 10 rows with NULLs on unmatched right side  
C. 14 rows  
D. Error

<details>
<summary>Reveal answer</summary>

**Answer: B**

</details>

**Q16. READ COMMITTED still allows**

A. Dirty read  
B. Non-repeatable read  
C. Lost update in one statement  
D. Reading uncommitted data

<details>
<summary>Reveal answer</summary>

**Answer: B**

</details>

**Q17. commission <> 1000 when commission is NULL**

A. Returned  
B. Not returned (UNKNOWN)  
C. Returned only with ANSI_NULLS OFF  
D. Runtime error

<details>
<summary>Reveal answer</summary>

**Answer: B**

</details>

**Q18. Highest NF for R(A,B,C), FDs {A→B, B→C, C→A}**

A. 2NF only  
B. 3NF not BCNF  
C. BCNF  
D. Not 1NF

<details>
<summary>Reveal answer</summary>

**Answer: C**

</details>

**Q19. Count query changes after insert between reads**

A. Dirty read  
B. Non-repeatable read  
C. Phantom read  
D. Lost update

<details>
<summary>Reveal answer</summary>

**Answer: C**

</details>

**Q20. R(A,B,C,D) with FDs {A→B, C→D} is**

A. BCNF  
B. 3NF not BCNF  
C. Not in 2NF (thus not 3NF/BCNF)  
D. 1NF due to transitive dependencies

<details>
<summary>Reveal answer</summary>

**Answer: C**

</details>
