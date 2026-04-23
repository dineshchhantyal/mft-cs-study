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

| Key | Definition |
|---|---|
| Superkey | Any set of attrs that uniquely identifies a tuple |
| Candidate key | Minimal superkey (no proper subset is a superkey) |
| Primary key | Chosen candidate key (NOT NULL, unique) |
| Alternate key | Candidate key not chosen as PK |
| Foreign key | Attr(s) referencing a PK in another (or same) relation |
| Composite key | Key made of >1 attribute |

- **Entity integrity**: PK ≠ NULL.
- **Referential integrity**: FK must equal some PK value or be NULL.
- On delete/update of referenced row: `CASCADE`, `SET NULL`, `SET DEFAULT`, `RESTRICT`, `NO ACTION`.

---

## 2. Relational Algebra

| Op | Symbol | Meaning |
|---|---|---|
| Select | σ_cond(R) | Rows matching condition |
| Project | π_attrs(R) | Columns (dedup — set semantics) |
| Union | R ∪ S | Both (union-compatible) |
| Intersect | R ∩ S | In both |
| Difference | R − S | In R but not S |
| Cartesian | R × S | All pairs |
| Rename | ρ | Rename relation/attrs |
| Natural join | R ⋈ S | Equi-join on common attrs, drop dupes |
| Theta join | R ⋈_θ S | σ_θ(R × S) |
| Equi-join | special theta with "=" |
| Outer joins | ⟕ ⟖ ⟗ | Left / Right / Full |
| Division | R ÷ S | "For all" queries |

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

| NF | Condition |
|---|---|
| 1NF | All attributes atomic (no repeating groups / multi-valued cells) |
| 2NF | 1NF + no **partial dependency** (no non-prime attr depends on part of a composite candidate key) |
| 3NF | 2NF + no **transitive dependency**: for every FD X→A (A non-prime), X is a superkey, **or** A is prime |
| BCNF | For every non-trivial FD X→Y, X is a superkey |
| 4NF | BCNF + no non-trivial MVD unless LHS is superkey |
| 5NF | No non-trivial join dependencies except from candidate keys |

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
|---|---|---|---|
| READ UNCOMMITTED | Possible | Possible | Possible |
| READ COMMITTED | **No** | Possible | Possible |
| REPEATABLE READ | **No** | **No** | Possible |
| SERIALIZABLE | **No** | **No** | **No** |

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
- (a) A  (b) AB  (c) AC  (d) ABCD
**A: (a) A.** A⁺ = ABCD. B,C,D each appear on RHS, A never does → A must be in every key. A alone suffices → only candidate key.

**Q2.** Table T(id, val) has rows (1,10), (2,NULL), (3,20). What's `SELECT COUNT(*), COUNT(val), SUM(val), AVG(val) FROM T;`?
- **A:** 3, 2, 30, 15. (AVG = 30/2, not 30/3.)

**Q3.** R(A,B,C) with FDs A→B, B→C. Highest normal form?
- (a) 1NF (b) 2NF (c) 3NF (d) BCNF
**A: (b) 2NF.** Key is A. B→C is transitive (B not superkey, C not prime) → violates 3NF.

**Q4.** Schedule: T1:R(A); T2:W(A); T1:W(A); T2:Commit; T1:Commit. Conflict serializable?
- Conflicts: T1.R(A)–T2.W(A) ⇒ T1→T2. T2.W(A)–T1.W(A) ⇒ T2→T1. **Cycle ⇒ NOT conflict serializable.**

**Q5.** Which isolation level allows phantoms but not non-repeatable reads?
- **A: REPEATABLE READ.**

**Q6.** `SELECT * FROM A LEFT JOIN B ON A.x = B.x WHERE B.y = 5;` — Issue?
- **A:** The WHERE on B effectively turns it into an inner join (NULLs from unmatched A rows fail `B.y = 5`). Move predicate to `ON` clause to preserve.

**Q7.** R(A,B,C,D), FDs: AB→C, C→D, D→A. Is R in BCNF?
- Candidate keys: AB, BC, BD (check closures). C→D violates BCNF (C not a superkey). **Not BCNF; it is 3NF** (D is prime since BD is a key, A is prime since AB is a key).

**Q8.** Which operator expresses "customers who bought **every** product"?
- **A: Division (÷).**

**Q9.** 100 rows in A, 50 in B, `A CROSS JOIN B`?
- **A: 5000.**

**Q10.** `SELECT dept, COUNT(*) FROM emp GROUP BY dept HAVING COUNT(*) > 5 ORDER BY dept;` — if `emp` is empty?
- **A:** Zero rows (no groups formed). Note: a query with no GROUP BY and aggregates only would return one row even on empty input.

**Q11.** Which property does strict 2PL guarantee beyond plain 2PL?
- **A:** Avoids **cascading aborts** and ensures recoverability/strictness by holding X-locks until commit.

**Q12.** `WHERE col NOT IN (SELECT x FROM T)` returns nothing. Why?
- **A:** Subquery returned a NULL; `col <> NULL` is UNKNOWN for every row → all filtered out. Use `NOT EXISTS`.

**Q13.** R1 ⋈ R2 where R1 has schema (A,B), R2 has (B,C). Lossless guarantee when decomposing R(A,B,C)?
- **A:** Lossless iff B → A or B → C (common attr B is a key in one side).

**Q14.** B+ tree vs hash index — which supports `WHERE salary BETWEEN 1000 AND 2000`?
- **A: B+ tree** (ordered leaves). Hash cannot do range.

---

## 13. Cheat Sheets

### Normal Forms (one-liner)

| NF | Rule of Thumb |
|---|---|
| 1NF | Atomic cells |
| 2NF | No partial key dependencies |
| 3NF | Non-key attrs depend on **key, whole key, nothing but the key** |
| BCNF | Every non-trivial FD's LHS is a superkey |
| 4NF | Same, extended to MVDs |

### Isolation Levels

| Level | Dirty | Non-rep | Phantom |
|---|---|---|---|
| READ UNCOMMITTED | Y | Y | Y |
| READ COMMITTED | N | Y | Y |
| REPEATABLE READ | N | N | Y |
| SERIALIZABLE | N | N | N |

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

1. **(Easy)** Which of the following is true about a *superkey* of a relation?
   A. It must be minimal.
   B. It uniquely identifies every tuple but need not be minimal.
   C. It can contain NULL values in any attribute.
   D. Every relation has exactly one superkey.

2. **(Easy)** The relational-algebra operator that returns tuples appearing in R but not in S (both union-compatible) is:
   A. σ
   B. π
   C. R − S
   D. R ⋈ S

3. **(Easy)** In SQL, which clause is evaluated *before* `SELECT`?
   A. ORDER BY
   B. DISTINCT
   C. HAVING
   D. LIMIT

4. **(Easy)** Given FDs {A → B, B → C}, by Armstrong's axioms we can derive:
   A. C → A
   B. A → C (transitivity)
   C. B → A
   D. AC → B only

5. **(Easy)** A relation is in **1NF** if:
   A. It has no partial dependencies.
   B. It has no transitive dependencies.
   C. All attribute values are atomic (no repeating groups).
   D. Every determinant is a candidate key.

6. **(Easy)** In an ER diagram, a *weak entity* is characterized by:
   A. Having no attributes.
   B. Being identified only in combination with its identifying (owner) entity via a partial key.
   C. Participating only in unary relationships.
   D. Always having a total primary key of its own.

7. **(Easy)** The "D" in ACID guarantees:
   A. Transactions appear to execute serially.
   B. Committed changes survive crashes.
   C. Concurrent transactions don't see each other's writes.
   D. All-or-nothing execution.

8. **(Easy)** Write-Ahead Logging (WAL) requires:
   A. Data pages be flushed to disk before log records.
   B. Log records describing a change be flushed to disk *before* the corresponding data page.
   C. Logs be written only at commit time.
   D. Checkpoints replace the need for logs.

9. **(Medium)** Consider `SELECT COUNT(*) - COUNT(salary) FROM emp;` over a table of 10 rows where 3 rows have `salary IS NULL`. The result is:
   A. 0
   B. 3
   C. 7
   D. 10

10. **(Medium)** Schedule S has edges T1→T2 and T2→T1 in its precedence graph. S is:
    A. Conflict-serializable.
    B. View-serializable only.
    C. Not conflict-serializable (cycle).
    D. Always recoverable.

11. **(Medium)** Strict 2PL guarantees:
    A. Serializability only.
    B. Serializability + recoverability, and avoids cascading aborts by holding X-locks till commit/abort.
    C. No deadlocks.
    D. Phantom-free reads without index locking.

12. **(Medium)** `R(A,B,C,D)` with FDs {AB → C, C → D, D → A}. A candidate key is:
    A. {A,B}
    B. {B,C}
    C. {A,D}
    D. Both A and B (AB and BC are both candidate keys)

13. **(Medium)** Which index is *best* for the query `WHERE salary BETWEEN 50000 AND 70000`?
    A. Hash index on salary.
    B. B+-tree index on salary.
    C. Bitmap index on department.
    D. No index (full scan always wins).

14. **(Medium)** In SQL, `SELECT dept, AVG(salary) FROM emp GROUP BY dept HAVING COUNT(*) > 5;` returns:
    A. Each employee's salary if their dept has > 5 people.
    B. Average salary per department, only for departments with more than 5 employees.
    C. Departments with more than 5 distinct salaries.
    D. Error: HAVING cannot use COUNT(*).

15. **(Medium)** A left outer join `R LEFT JOIN S ON R.x = S.x` on 10 R-rows where only 4 match S will produce:
    A. 4 rows.
    B. 10 rows, with NULLs in S's columns for the 6 non-matching R-rows.
    C. 14 rows.
    D. An error if S has NULLs in x.

16. **(Medium)** Under isolation level **READ COMMITTED**, which anomaly is *still possible*?
    A. Dirty read.
    B. Non-repeatable read.
    C. Lost update on the same row within one statement.
    D. Reading uncommitted data.

17. **(Trap)** `SELECT * FROM emp WHERE commission <> 1000;` on a table where some rows have `commission = NULL`. Those NULL rows are:
    A. Returned (NULL ≠ 1000).
    B. Not returned — the predicate evaluates to UNKNOWN, which `WHERE` filters out.
    C. Returned only if `ANSI_NULLS` is OFF.
    D. Cause a runtime error.

18. **(Trap)** Relation R(A,B,C) with FDs {A → B, B → C, C → A}. The highest normal form R satisfies is:
    A. 2NF only.
    B. 3NF but not BCNF.
    C. BCNF (every determinant A, B, C is a superkey since all are candidate keys).
    D. Not even 1NF.

19. **(Trap)** A transaction T1 runs `SELECT COUNT(*) FROM orders WHERE total > 100;` twice. Between the reads, T2 *inserts* a new qualifying row and commits. T1 sees different counts. This is:
    A. A dirty read.
    B. A non-repeatable read (same row changed).
    C. A phantom read — prevented only by SERIALIZABLE (or predicate/range locks).
    D. A lost update.

20. **(Trap)** R(A,B,C,D) with FDs {A → B, C → D}. R is:
    A. In BCNF.
    B. In 3NF but not BCNF (neither A nor C is a superkey).
    C. Not in 2NF (and thus not in 3NF/BCNF) — partial dependencies on the only candidate key {A,C}.
    D. In 1NF only because of transitive dependencies.

### ✅ Answer Key & Explanations

<details>
<summary>Reveal full answer key</summary>

1. **B** — A superkey uniquely identifies tuples; a *candidate* key adds the minimality requirement.
2. **C** — Set difference `R − S` returns tuples in R but not S.
3. **C** — Order: FROM → WHERE → GROUP BY → HAVING → **SELECT** → DISTINCT → ORDER BY → LIMIT. HAVING runs before SELECT.
4. **B** — Transitivity: A → B and B → C ⇒ A → C.
5. **C** — 1NF = atomic values / no repeating groups. No partial (2NF) or transitive (3NF) dependency constraints yet.
6. **B** — Weak entities lack a full key of their own and depend on an identifying relationship + partial (discriminator) key.
7. **B** — Durability = committed changes persist through crashes.
8. **B** — WAL's core rule: log record on stable storage *before* the dirty data page.
9. **B** — `COUNT(*)` = 10 counts all rows; `COUNT(salary)` = 7 ignores NULLs; difference = 3 (the NULL count).
10. **C** — A cycle in the precedence graph means the schedule is **not** conflict-serializable.
11. **B** — Strict 2PL holds exclusive locks until commit/abort, giving serializability + recoverability + no cascading aborts. It does *not* prevent deadlocks.
12. **D** — AB → C → D → A, so AB determines all; also BC → (via C→D→A) → all, so BC is also a candidate key.
13. **B** — Range queries need ordered traversal; B+-trees support ranges, hash indexes don't.
14. **B** — Standard GROUP BY + HAVING semantics: aggregate per group, filter groups by aggregate predicate.
15. **B** — LEFT OUTER JOIN preserves all left rows; unmatched right side becomes NULL.
16. **B** — READ COMMITTED blocks dirty reads but *allows* non-repeatable reads and phantoms.
17. **B** — NULL <> 1000 is UNKNOWN, not TRUE; `WHERE` drops UNKNOWN rows. Classic NULL trap.
18. **C** — When every attribute is a candidate key (cyclic FDs A→B→C→A make {A},{B},{C} all keys), every determinant is a superkey ⇒ BCNF.
19. **C** — New *inserted* rows matching a predicate between two reads = phantom. Requires SERIALIZABLE or predicate/range locks (standard 2PL on existing rows won't stop it).
20. **C** — The only candidate key is {A,C}. A → B and C → D are *partial* dependencies on the key ⇒ violates 2NF. So R is only in 1NF. (Trap: looks like a 3NF-vs-BCNF question but fails earlier at 2NF.)

</details>
