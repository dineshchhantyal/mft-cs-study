# MFT CS — Programming Fundamentals & Programming Languages

> **Exam:** ETS Major Field Test in Computer Science
> **Date:** May 1, 2026
> **Format:** 66 MCQs, 2 hours, closed book
> **This topic's weight:** ~21–27% (Programming Fundamentals + Programming Languages combined; the largest single cluster on the test)
> **Language of the exam:** ETS uses a neutral Algol/Pascal-flavored **pseudocode**. You will NOT see Java/Python/C syntax — but the _semantics_ questions assume you know how real languages differ.

---

## Table of Contents

1. [ETS Content Outline for this Topic](#1-ets-content-outline-for-this-topic)
2. [How ETS Writes These Questions (Meta-strategy)](#2-how-ets-writes-these-questions-meta-strategy)
3. [Parameter Passing](#3-parameter-passing)
4. [Scope: Static vs Dynamic](#4-scope-static-vs-dynamic)
5. [Binding Times](#5-binding-times)
6. [Recursion](#6-recursion)
7. [Object-Oriented Programming](#7-object-oriented-programming)
8. [Functional Programming Concepts](#8-functional-programming-concepts)
9. [Type Systems](#9-type-systems)
10. [Language Translation (Compilers/Interpreters/JIT)](#10-language-translation)
11. [Memory: Stack, Heap, Activation Records, GC](#11-memory-management)
12. [Exception Handling](#12-exception-handling)
13. [Expressions, Operators, Side Effects (Classic Trick Questions)](#13-expressions-operators-side-effects)
14. [Control Flow Gotchas](#14-control-flow-gotchas)
15. [Concurrency Basics (spillover from Systems)](#15-concurrency-basics)
16. [Mnemonics & Memory Tricks](#16-mnemonics--memory-tricks)
17. [10 Sample MCQs with Worked Solutions](#17-sample-mcqs-with-worked-solutions)
18. [Quick-Reference Cheat Sheet](#18-quick-reference-cheat-sheet)

---

## 1. ETS Content Outline for this Topic

Per the official ETS MFT CS Test Description (4CMF), **Programming** (21–27%) is broken into:

### Programming Fundamentals

- Fundamental data structures (arrays, strings, records, lists, stacks, queues, trees, hash tables) — mostly tested in the Data Structures section, but **representation** shows up here.
- Iteration and recursion under program control and structure.
- Event-driven programming (rare, ~1 question).
- Object-oriented programming: classes, objects, inheritance, polymorphism, encapsulation.

### Programming Languages

- **Overview of programming languages** (paradigms: imperative, OO, functional, logic, scripting).
- **Virtual machines** (JVM, CLR) — interpretation vs compilation vs JIT.
- **Introduction to language translation** — lexical analysis, parsing, semantic analysis, code generation, optimization phases.
- **Declarations, modularity, storage management** — activation records, lifetime, allocation.
- **Scope, binding, and parameter passing** — _the highest-frequency subtopic in this section._
- **Type systems** — strong/weak, static/dynamic, inference, polymorphism kinds.
- **Abstraction mechanisms** — procedures, ADTs, generics, modules.
- **Functional programming** — higher-order functions, closures, immutability, lazy evaluation.
- **Language semantics** — operational, denotational intuition (rarely formal).

> **Budgeting:** Roughly 14–18 of 66 questions come from this cluster. Expect ~3–5 on parameter passing/scope, ~2–3 on OOP, ~2 on functional, ~2 on type systems, ~2 on compilers/interpreters, plus recursion traces.

---

## 2. How ETS Writes These Questions (Meta-strategy)

Patterns seen across released ETS samples and candidate write-ups (Ben Leskey, scratchrobotics, Urch forum, TAMU quals repo):

1. **Output-prediction traces.** You get a ~10-line pseudocode snippet and must pick the printed value under a specified evaluation rule (by value, by reference, static scope, etc.). Same code, 4 answers covering the 4 passing modes — read the stem carefully for which rule applies.
2. **"Which statement is true?" style.** Four claims about OOP / types / compilers; three are subtly wrong. Almost always the trick is a **swapped definition** (e.g., "polymorphism = ability to bind a name at runtime" — no, that's late binding).
3. **Tiny recursion traces.** f(4) calls where you must compute an arithmetic total. Classic: Ackermann-like, Fibonacci count, mutual recursion.
4. **Grouped questions.** A single pseudocode block with 2–3 questions attached. If you struggle with the code, skip the whole group and return.
5. **Precedence/associativity.** One question per test usually — evaluate an expression with mixed `&&`, `||`, `!`, `+`, unary minus, and maybe assignment-as-expression.
6. **Short-circuit and side effects.** `if (p != null && p.next == x)` style — know that `&&` short-circuits left-to-right.

**Golden rule:** when the question says "assume call-by-X" or "under dynamic scoping," the default answer under the _other_ discipline is almost always one of the distractors. Compute both, then pick.

---

## 3. Parameter Passing

This is the single most tested PL topic on the MFT.

### 3.1 The Four Disciplines

| Mode                | Also called          | Argument evaluated                                          | What callee sees                                 | Writes visible to caller?                        |
| ------------------- | -------------------- | ----------------------------------------------------------- | ------------------------------------------------ | ------------------------------------------------ |
| **By value**        | copy-in              | Once, at call                                               | A **copy**                                       | No                                               |
| **By reference**    | by address           | Once, at call (must be lvalue)                              | A **pointer/alias** to caller's variable         | **Yes, immediately**                             |
| **By value-result** | copy-in/copy-out     | Once, at call (copy in); copy back at return                | A copy, written back on return                   | **Yes, at return only**                          |
| **By name**         | textual substitution | **Every use** inside the callee re-evaluates the expression | A thunk (delayed expression with caller's scope) | Yes, each read/write re-runs the expression      |
| **By need**         | lazy / memoized name | First use evaluates; subsequent uses reuse the result       | Thunk + cache                                    | Effectively like name but evaluated at most once |

### 3.2 The Classic Swap Problem

```
procedure swap(x, y):
    t := x
    x := y
    y := t

a := 1
b := 2
swap(a, b)
print(a, b)
```

| Mode         | Output                          | Reason                                    |
| ------------ | ------------------------------- | ----------------------------------------- |
| Value        | `1 2`                           | Callee swaps its copies; caller unchanged |
| Reference    | `2 1`                           | x,y are aliases of a,b                    |
| Value-result | `2 1`                           | Copies swap, then written back            |
| Name         | `2 1` _if args are simple vars_ | Every use rewrites `a`, `b`               |

### 3.3 The Canonical Call-by-Name Trap: `swap(i, a[i])`

```
i := 1
a := [10, 20, 30]   // 1-indexed
swap(i, a[i])
```

- **By value:** `i=1, a=[10,20,30]` (no change).
- **By reference:** aliases bound once. `x` aliases `i`, `y` aliases `a[1]`. After swap: `i=10, a[1]=1` → `a=[1,20,30]`.
- **By value-result:** same as reference here (one address per arg).
- **By name:** Every mention of `y` re-evaluates `a[i]` using the current `i`.
  - `t := x` → `t := i = 1`
  - `x := y` → `i := a[i] = a[1] = 10` _(now i=10!)_
  - `y := t` → `a[i] := 1`, but now `i=10`, so `a[10] := 1`. If array bounds are only 1..3, this is a bounds error (or silently writes somewhere else in pseudocode). **Commonly the "right" MFT answer is "array index out of bounds" or "a is unchanged, i=10".**

> **Mnemonic:** **VRaN** — Value, Reference, and Name differ when the argument is a _compound expression_ or _subscripted variable_.

### 3.4 Jensen's Device (call-by-name showpiece)

```
real procedure sum(i, lo, hi, expr):
    s := 0
    for i := lo to hi do s := s + expr
    return s

// Call:
total := sum(k, 1, 10, k*k)   // computes 1^2 + 2^2 + ... + 10^2 = 385
```

Works only under **call-by-name** because each iteration re-evaluates `k*k` with the current `k`. Under value, `k*k` is evaluated once (with whatever `k` was before the call) and summed 10 times.

### 3.5 Common ETS Trick Variants

- **"Which of these languages uses call-by-value only?"** → C, Java (for primitives AND object refs — Java passes the _reference_ by value; often phrased "Java is call-by-value").
- **"Which yields different results under value vs reference?"** → Any procedure that _writes_ to its parameter.
- **Python/Ruby** — "call-by-object" or "call-by-sharing": reference to object is passed by value. Reassignment in callee doesn't affect caller; mutation does.
- **Algol 60 default** = **call-by-name**. Algol 68, Pascal, Ada, C, C++, Java = **call-by-value** (with pointers/refs to simulate reference).

---

## 4. Scope: Static vs Dynamic

### 4.1 Definitions

<details>
<summary><strong>Q1.</strong> In <strong>call-by-value</strong>, what does the callee receive?</summary>

A. The address of the argument
B. A copy of the argument's value
C. A reference alias to the caller's variable
D. The unevaluated expression text

**Answer: (B) A copy of the argument's value.**
Call-by-value copies the argument's value into the parameter, so changes inside the callee do not affect the caller.

</details>

<details>
<summary><strong>Q2.</strong> Which compiler phase rejects <code>int x = "hello";</code> in a statically typed language?</summary>

A. Lexical analysis
B. Syntax analysis (parsing)
C. Semantic analysis (type checking)
D. Code generation

**Answer: (C) Semantic analysis (type checking).**
Lexing accepts the tokens, parsing accepts the grammar, and the type mismatch is caught during semantic analysis.

</details>

<details>
<summary><strong>Q3.</strong> Which memory region typically holds objects allocated with <code>new</code> in Java?</summary>

A. Stack
B. Heap
C. Code segment
D. CPU registers

**Answer: (B) Heap.**
In Java, the object itself is heap-allocated and garbage-collected; the reference variable may live elsewhere.

</details>

<details>
<summary><strong>Q4.</strong> Which best distinguishes an <strong>abstract class</strong> from a classical (pre-Java 8) <strong>interface</strong>?</summary>

A. Interfaces can have constructors; abstract classes cannot
B. Abstract classes can hold state (fields) and partial implementations; interfaces only declared method signatures
C. A class can extend multiple abstract classes but implement only one interface
D. Interfaces support private fields; abstract classes do not

**Answer: (B).**
Abstract classes can carry fields and concrete methods; classical interfaces only declare signatures and constants. A class may implement many interfaces but extend only one class.

</details>

<details>
<summary><strong>Q5.</strong> What does the <strong>Liskov Substitution Principle</strong> require?</summary>

A. Subtypes must override every method of the supertype
B. Subtypes must be usable anywhere the supertype is expected without breaking correctness
C. Supertypes must not have abstract methods
D. Subtypes should always throw new checked exceptions

**Answer: (B).**
LSP requires behavioral substitutability: a subtype should be a drop-in replacement for its supertype without surprising failures.

</details>

<details>
<summary><strong>Q6.</strong> Mark-and-sweep, reference counting, and generational collection are variants of what?</summary>

A. Stack allocation strategies
B. Garbage collection algorithms
C. Virtual memory paging
D. Type inference techniques

**Answer: (B) Garbage collection algorithms.**
They are different ways of reclaiming unreachable heap memory.

</details>

<details>
<summary><strong>Q7.</strong> The <strong>dangling-else</strong> ambiguity is resolved in most C-family languages by binding <code>else</code> to the…</summary>

A. Outermost unmatched <code>if</code>
B. Nearest unmatched <code>if</code>
C. First <code>if</code> in the block
D. It is a compile error

**Answer: (B) Nearest unmatched <code>if</code>.**
That is the standard rule used to resolve the ambiguity.

</details>

<details>
<summary><strong>Q8.</strong> Which is a property of a <strong>pure function</strong>?</summary>

A. It may write to a global log
B. Given the same inputs, it returns the same output and has no side effects
C. It must be recursive
D. It must return <code>void</code>

**Answer: (B).**
Purity means deterministic output for the same inputs and no side effects.

</details>
| Needs       | **Static link** or display in activation record | **Dynamic link** (access link via call stack) |
| Readability | Good — see declarations in source               | Poor — behavior depends on caller             |
| Speed       | Faster (offsets known)                          | Slower (search)                               |

> **Mnemonic:** **"Static follows the page, Dynamic follows the stage."** The page = source code; the stage = active actors (call stack).

<details>
<summary><strong>Q9.</strong> Static vs dynamic scope. Given the code below, what is printed under <strong>static (lexical)</strong> vs <strong>dynamic</strong> scope?</summary>

```
x = 1
function f():  print(x)
function g():  x = 2; f()
g()
```

A. 1 and 1
B. 2 and 2
C. 1 (static) and 2 (dynamic)
D. 2 (static) and 1 (dynamic)

**Answer: (C).**
Under static scope, `f` resolves `x` in its lexical environment and prints 1. Under dynamic scope, `f` sees `g`'s local `x = 2` on the call stack and prints 2.

</details>

<details>
<summary><strong>Q10.</strong> Recursion trace — what does <code>mystery(4)</code> return?</summary>

```
function mystery(n):
  if n <= 1: return 1
  return n * mystery(n - 2)
```

A. 24
B. 8
C. 6
D. 4

**Answer: (B) 8.**
Trace it bottom-up: `mystery(4) = 4 * mystery(2) = 4 * (2 * mystery(0)) = 4 * 2 * 1 = 8`.

</details>

<details>
<summary><strong>Q11.</strong> Java-like overload/override:</summary>

```
class A { void p(int x) { print("A-int"); } }
class B extends A {
  void p(int x) { print("B-int"); }
  void p(double x) { print("B-double"); }
}
A a = new B();
a.p(3);
```

What prints?
A. A-int
B. B-int
C. B-double
D. Compile error

**Answer: (B) B-int.**
The call resolves to `p(int)` at compile time from the static type `A`, then dispatches dynamically to B's override.

</details>

<details>
<summary><strong>Q12.</strong> Short-circuit + precedence. With <code>a = 0</code>, <code>b = 5</code>, evaluate <code>a != 0 && b / a > 1</code>:</summary>

A. Runtime division-by-zero
B. <code>false</code> (no division performed)
C. <code>true</code>
D. Compile error

**Answer: (B) false.**
The left operand is false, so `&&` short-circuits and the division is never evaluated.

</details>

<details>
<summary><strong>Q13.</strong> Pre/post-increment (assume strict left-to-right evaluation with sequence points):</summary>

```
int i = 2;
int r = (i++) + (++i) + i;
```

What is `r`?
A. 8
B. 9
C. 10
D. 11

**Answer: (C) 10.**
`i++` yields 2 then increments `i` to 3; `++i` increments to 4 and yields 4; final `i` is 4, so the sum is 2 + 4 + 4 = 10.

</details>

<details>
<summary><strong>Q14.</strong> The classic JavaScript 3-3-3 problem:</summary>

```
var fns = [];
for (var i = 0; i < 3; i++) {
  fns.push(function() { return i; });
}
print(fns[0](), fns[1](), fns[2]());
```

What prints?
A. 0 1 2
B. 1 2 3
C. 3 3 3
D. undefined undefined undefined

**Answer: (C) 3 3 3.**
`var` is function-scoped, so all closures capture the same `i`, which ends at 3.

</details>

<details>
<summary><strong>Q15.</strong> Exception propagation (Java-like):</summary>

```
try {
  try { throw new IOException(); }
  finally { print("A"); }
} catch (IOException e) { print("B"); }
finally { print("C"); }
```

What prints?
A. A B C
B. B A C
C. A C
D. B C

**Answer: (A) A B C.**
The inner `finally` runs first, then the exception is caught, then the outer `finally` runs.

</details>

<details>
<summary><strong>Q16.</strong> Higher-order + immutability:</summary>

```
xs = [1, 2, 3]
ys = map(xs, x -> x * 2)
print(xs, ys)
```

Assuming `map` is pure and non-mutating:
A. <code>[2,4,6] [2,4,6]</code>
B. <code>[1,2,3] [2,4,6]</code>
C. <code>[1,2,3] [1,2,3]</code>
D. <code>[2,4,6] [1,2,3]</code>

**Answer: (B).**
`map` returns a new list and leaves the input unchanged.

</details>

| Kind           | Also called        | Example                                       |
| -------------- | ------------------ | --------------------------------------------- |
| **Subtype**    | Inclusion, dynamic | Shape → Circle, Square; `s.area()` dispatches |
| **Parametric** | Generics           | `List<T>`, ML's `'a list`, Java generics      |

<details>
<summary><strong>Q17.</strong> <strong>Call-by-name with swap</strong> (Jensen's device). Parameters passed by name — every use of a parameter re-evaluates its actual expression:</summary>

```
procedure swap(a, b):     // pass by NAME
  temp = a
  a = b
  b = temp

i = 1
A = [10, 20, 30]          // 1-indexed: A[1]=10, A[2]=20, A[3]=30
swap(i, A[i])
```

After the call, what are `i` and `A`?
A. i = 10, A = [1, 20, 30]
B. i = 10, A = [10, 1, 30]
C. i = 1, A = [1, 20, 30]
D. i = 10, A = [10, 20, 30]

**Answer: (A) i = 10, A = [1, 20, 30].**
Trace with call-by-name by substituting the actual expressions textually. `temp = a` reads `i = 1`; `a = b` becomes `i = A[i]`, so `i` becomes 10; `b = temp` then re-evaluates `A[i]` with the new `i`, which is the classic call-by-name trap. The key takeaway is that `swap(i, A[i])` breaks under name semantics.

</details>

<details>
<summary><strong>Q18.</strong> <strong>Overload (static) vs override (dynamic):</strong></summary>

```
class Animal { void speak(Animal a) { print("animal+animal"); } }
class Dog extends Animal {
  void speak(Animal a) { print("dog+animal"); }   // override
  void speak(Dog d)    { print("dog+dog"); }      // overload (Dog-only)
}
Animal x = new Dog();
Dog    y = new Dog();
x.speak(y);
```

What prints? (Java resolves overloads at compile time using **static** types.)
A. dog+dog
B. dog+animal
C. animal+animal
D. Compile error

**Answer: (B) dog+animal.**
`x`'s static type is `Animal`, so overload resolution sees only `speak(Animal)`. At runtime, dynamic dispatch invokes `Dog`'s override of that method.

</details>

<details>
<summary><strong>Q19.</strong> <strong>Closures capture variables, not values</strong> — the <code>let</code> twist:</summary>

```
let fns = [];
for (let i = 0; i < 3; i++) {
  fns.push(() => i);
}
console.log(fns[0](), fns[1](), fns[2]());
```

What prints?
A. 0 1 2
B. 3 3 3
C. 2 2 2
D. undefined undefined undefined

**Answer: (A) 0 1 2.**
`let` creates a fresh binding per iteration, so each closure captures its own `i`.

</details>

<details>
<summary><strong>Q20.</strong> <strong>Call-by-value-result (copy-in/copy-out) vs call-by-reference.</strong></summary>

```
procedure p(x, y):
  x = x + 1
  y = y + 1

i = 5
p(i, i)                   // same variable passed twice
```

What is `i` after the call under (a) call-by-reference, and (b) call-by-value-result (copy back left-to-right on return)?
A. (a) 7, (b) 7
B. (a) 7, (b) 6
C. (a) 6, (b) 7
D. (a) 6, (b) 6

**Answer: (B) (a) 7, (b) 6.**
Under reference, both parameters alias `i`, so `i` is incremented twice. Under value-result, both locals start at 5, both become 6, and the second copy-back overwrites the first.

</details>
### 9.3 Subtyping

`S <: T` means anywhere a `T` is expected, an `S` can be used (**Liskov Substitution Principle**). Consequences:

- **Covariant** return types (override can return a more-specific type) — safe.
- **Contravariant** parameter types — safe in theory; Java/C++ don't allow it (they use invariant params).
- Arrays in Java are covariant but unsound (`ArrayStoreException`).
- Generics in Java are **invariant** (`List<Cat>` is not a `List<Animal>`). Use `? extends` / `? super` (PECS) for variance.

### 9.4 Parametric Polymorphism Implementations

| Strategy             | Languages           | Cost                                            |
| -------------------- | ------------------- | ----------------------------------------------- |
| **Type erasure**     | Java generics       | No runtime type info; one copy of code          |
| **Monomorphization** | C++ templates, Rust | Code bloat; one copy per instantiation; fastest |
| **Boxed/uniform**    | ML, Haskell         | One copy; boxes primitives                      |

---

## 10. Language Translation

### 10.1 Compiler Phases

```
Source → [Lexer] → tokens → [Parser] → AST → [Semantic analyzer] → annotated AST
       → [IR gen] → IR → [Optimizer] → IR' → [Code gen] → target code
```

| Phase                           | Input → Output             | Typical errors caught                         |
| ------------------------------- | -------------------------- | --------------------------------------------- |
| **Lexical analysis** (scanning) | Chars → tokens             | Invalid characters, bad numeric literals      |
| **Syntax analysis** (parsing)   | Tokens → parse tree / AST  | Missing semicolons, mismatched braces         |
| **Semantic analysis**           | AST → annotated AST        | Undeclared vars, type mismatches, wrong #args |
| **Intermediate code gen**       | AST → IR (e.g., 3-address) | —                                             |
| **Optimization**                | IR → IR                    | —                                             |
| **Code generation**             | IR → assembly/machine      | Register allocation issues                    |

### 10.2 Compiler vs Interpreter vs JIT

|                  | Compiler             | Interpreter                                                | JIT                              |
| ---------------- | -------------------- | ---------------------------------------------------------- | -------------------------------- |
| Translates when? | Ahead of time, once  | Line by line during execution                              | At runtime, hot paths            |
| Output           | Native binary        | None (runs AST/bytecode)                                   | Native code cached               |
| Startup          | Slow build, fast run | Fast start, slow run                                       | Medium start, fast steady-state  |
| Examples         | C, C++, Rust, Go     | Early BASIC, classic Python (CPython bytecode interpreter) | Java HotSpot, V8, PyPy, .NET CLR |

> **Trick:** "Java is compiled AND interpreted." It's compiled to **bytecode** ahead of time, then interpreted (or JIT-compiled) by the JVM.

### 10.3 Grammar Classes (peripheral but can appear)

| Class                  | Parsable by                 |
| ---------------------- | --------------------------- |
| Regular                | Finite automaton (lexer)    |
| Context-free           | Pushdown automaton (parser) |
| Context-sensitive      | Linear-bounded automaton    |
| Recursively enumerable | Turing machine              |

LL(k) = top-down, k lookahead. LR(k) = bottom-up. LALR(1) = Yacc/Bison. Typical programming languages are LALR(1) or designed for LL(1) (recursive-descent friendly).

---

## 11. Memory Management

### 11.1 Stack vs Heap

|               | Stack                                   | Heap                                     |
| ------------- | --------------------------------------- | ---------------------------------------- |
| Allocation    | LIFO, automatic on call                 | Explicit (`new`, `malloc`) or GC-managed |
| Speed         | Very fast (pointer bump)                | Slower (free-list, GC)                   |
| Lifetime      | Until function returns                  | Until freed / unreachable                |
| Size          | Limited (typically 1–8 MB)              | Large (GBs)                              |
| Fragmentation | None                                    | Yes                                      |
| Holds         | Locals, params, return addr, saved regs | Objects with indeterminate lifetime      |

### 11.2 Activation Record (Stack Frame)

Contents (typical layout, growing downward):

```
+-------------------+
| Arguments         |  <- caller pushes
+-------------------+
| Return address    |  <- from call instruction
+-------------------+
| Saved frame ptr   |
+-------------------+  <- FP points here
| Saved registers   |
+-------------------+
| Local variables   |
+-------------------+
| Temporaries       |
+-------------------+  <- SP points here
```

In languages with **nested procedures** (Pascal, Ada, Algol), the frame also has:

- **Static link** — pointer to lexical parent frame (for static scope).
- **Dynamic link** — pointer to caller frame (for dynamic scope or to pop).

### 11.3 Garbage Collection

| Algorithm                | Idea                                  | Pros                            | Cons                             |
| ------------------------ | ------------------------------------- | ------------------------------- | -------------------------------- |
| **Reference counting**   | Each object has count; free when 0    | Incremental, predictable        | **Cycles leak**; per-op overhead |
| **Mark-and-sweep**       | Mark reachable, free rest             | Handles cycles                  | Pauses; fragmentation            |
| **Copying (semi-space)** | Copy live to other half; free old     | No fragmentation; fast alloc    | Uses 2x memory                   |
| **Generational**         | Most objects die young; separate gens | Very fast for typical workloads | Complex                          |

> **ETS trick:** "Which GC algorithm fails on cyclic data?" → **Reference counting**. "Which provides bounded pause time?" → reference counting (amortized) or incremental GCs; **not** stop-the-world mark-sweep.

### 11.4 Memory Errors

- **Dangling pointer:** pointer to freed memory.
- **Memory leak:** allocated but unreachable without `free`.
- **Double free:** `free` called twice.
- **Use-after-free:** access through dangling pointer.
- **Buffer overflow:** writing past array bounds (classic C).
- **Stack overflow:** recursion too deep / huge local array.

---

## 12. Exception Handling

### 12.1 Models

| Model           | Behavior                                                                                                                          |
| --------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| **Termination** | After handler runs, control does NOT return to the raising point; enclosing block terminates. Used by C++, Java, Python, C#.      |
| **Resumption**  | Handler may fix the problem and resume at the raise point. Used by Common Lisp's condition system, historically PL/I. Rare today. |

### 12.2 Checked vs Unchecked (Java)

- **Checked** (extends `Exception`, not `RuntimeException`): compiler forces you to declare or catch. E.g., `IOException`.
- **Unchecked** (`RuntimeException`, `Error`): no compile-time requirement. E.g., `NullPointerException`.

### 12.3 `try / catch / finally`

- `finally` runs **always** (normal exit, exception, return in try/catch). _Exception_: `System.exit`, JVM crash.
- If a `finally` block has `return` or throws, it **overrides** any pending exception or return from try/catch.
- `try-with-resources` (Java 7+) auto-closes in reverse order of declaration.

### 12.4 Stack Unwinding

When an exception is thrown, the runtime walks up the stack popping frames and running `finally`/destructors until a matching `catch` is found. Uncaught → program terminates.

---

## 13. Expressions, Operators, Side Effects

### 13.1 Precedence (C/Java, high to low)

| Level       | Operators                                                 |
| ----------- | --------------------------------------------------------- |
| 1 (highest) | `()` `[]` `.` `->`                                        |
| 2           | unary `!` `~` `++` `--` `+` `-` `*`(deref) `&`(addr) cast |
| 3           | `*` `/` `%`                                               |
| 4           | `+` `-`                                                   |
| 5           | `<<` `>>`                                                 |
| 6           | `<` `<=` `>` `>=`                                         |
| 7           | `==` `!=`                                                 |
| 8           | `&` (bitwise AND)                                         |
| 9           | `^`                                                       |
| 10          | `\|`                                                      |
| 11          | `&&`                                                      |
| 12          | `\|\|`                                                    |
| 13          | `? :`                                                     |
| 14          | `=` `+=` etc. (right-assoc)                               |
| 15 (lowest) | `,`                                                       |

**Common trap:** `a & b == c` parses as `a & (b == c)` because `==` binds tighter than `&`. Always parenthesize bitwise ops.

### 13.2 Short-Circuit Evaluation

- `a && b` — if `a` false, `b` **not** evaluated.
- `a || b` — if `a` true, `b` **not** evaluated.

**Safety idiom:** `if (p != null && p.value > 0)` — prevents NPE.
**Trap:** using `&` or `|` (bitwise/non-short-circuit) where `&&` or `||` is intended. On bools in Java both work logically but bitwise always evaluates both sides → side effects fire either way.

### 13.3 Pre- vs Post-Increment

```
x = 5; y = x++;  // y=5, x=6   (post: use, then inc)
x = 5; y = ++x;  // y=6, x=6   (pre: inc, then use)
```

ETS may ask: What does `a[i++] = i;` store? **Undefined behavior in C** (sequence points — two unsequenced modifications of `i`). In Java it's defined left-to-right: evaluate `a[i++]` (uses current i, then increments) → if initial i=0: write to `a[0]`, then RHS evaluates `i` = 1, so `a[0] = 1`.

### 13.4 Assignment Operators

- In C/Java, `=` is an **expression** returning the assigned value. So `a = b = c = 5;` works (right-assoc).
- In Python, `=` is a **statement** (but `:=` walrus is an expression).

### 13.5 Evaluation Order of Arguments

- **C/C++:** unspecified. `f(g(), h())` may call `g` or `h` first.
- **Java:** strictly left-to-right.
- **Scheme:** unspecified.
- **OCaml:** right-to-left (historically; depends on impl).

---

## 14. Control Flow Gotchas

### 14.1 `switch` Fall-through

In C/C++/Java (without `->` arrow syntax), `case` falls through to the next without `break`. Forgetting `break` is a classic bug.

### 14.2 Dangling Else

```
if (a)
  if (b) x();
else y();
```

Indentation lies: `else` binds to the **nearest** `if`. That's the inner `if`. If `a` is false, nothing runs.

### 14.3 `for` Loop Semantics

- C-style: `for (init; cond; update) body` — cond tested before each iteration.
- `do ... while` tests **after** body — always runs at least once.
- Python `for x in iterable: ... else:` — `else` runs if loop completes without `break`.

### 14.4 `goto` and Structured Programming

Dijkstra's "Go To Statement Considered Harmful" (1968). Modern languages permit limited non-local exits: `break`, `continue`, `return`, exceptions, labeled break (Java). Pure structured programming = single entry/exit, sequence/selection/iteration only.

---

## 15. Concurrency Basics

(A bit spills into PL; more lives in Systems/OS.)

- **Process** = own address space; **Thread** = shares address space, own stack.
- **Race condition** = outcome depends on timing; fix with synchronization.
- **Deadlock** = 4 conditions (mutual exclusion, hold-and-wait, no preemption, circular wait).
- **Mutex / semaphore / monitor** — mutual exclusion primitives.
- **Atomic / volatile** — memory model guarantees. In Java, `volatile` ensures visibility but **not** atomicity for compound ops.

---

## 16. Mnemonics & Memory Tricks

| Mnemonic                                                                   | For                                                                      |
| -------------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| **"Value copies, Reference shares, Result reconciles, Name recomputes"**   | The four parameter modes                                                 |
| **"Static = page, Dynamic = stage"**                                       | Scoping rules                                                            |
| **"PIE"** — Polymorphism, Inheritance, Encapsulation                       | OOP pillars (add A for Abstraction)                                      |
| **"PECS"** — Producer Extends, Consumer Super                              | Java generic wildcards                                                   |
| **"LSP"** — Liskov: subtypes must be substitutable                         | Subtyping rule                                                           |
| **"VRaN"** — Value, Reference, **a**nd Name                                | 3 passing modes that can differ                                          |
| **"Parse before Semantics, Semantics before Codegen"**                     | Compiler phase order                                                     |
| **"Overload = compile-time (Ot=Ct)"** vs **"Override = run-time (Or=Rt)"** | Binding times                                                            |
| **"Strong/Weak is about casting; Static/Dynamic is about timing"**         | Type system axes                                                         |
| **"Mark finds; Sweep frees; Copy compacts; RefCount cheats (cycles)"**     | GC algorithms                                                            |
| **"LIFO stack, random heap"**                                              | Memory regions                                                           |
| **"Interface = contract; Abstract = partial code"**                        | OOP hierarchy choice                                                     |
| **"Closures close over variables, not values"**                            | Closure semantics — captures the _binding_, so later changes are visible |

---

## 17. Sample MCQs with Worked Solutions

These are MFT-style practice questions (original; calibrated to ETS difficulty and style).

---

### Q1. Parameter Passing

```
procedure p(a, b):
    a := a + 1
    b := b + a

x := 5
p(x, x)
print(x)
```

What does the program print under **call-by-reference**?

**(A)** 5 **(B)** 6 **(C)** 11 **(D)** 12 **(E)** Undefined

**Answer: (D) 12.**
Under reference, `a` and `b` both alias `x`. Trace:

- `a := a+1` → `x := 5+1 = 6` (now a=b=x=6).
- `b := b+a` → `x := 6+6 = 12`.

Under call-by-value, the answer would be (A) 5. Under value-result, trace locals: a=5→6, b=5→11; copy-back writes 6 then 11, final is 11 (order-dependent; often (C)). This illustrates why ETS presents all four answers.

---

### Q2. Static vs Dynamic Scope

```
x := 1

procedure f():
    print(x)

procedure g():
    x := 2
    f()

g()
```

Under **dynamic scoping**, the output is:

**(A)** 1 **(B)** 2 **(C)** Error: x undeclared in f **(D)** Nothing

**Answer: (B) 2.**
Dynamic scope: at the time `f` prints `x`, the call stack has `g`'s local `x=2` above the global. Most-recent binding wins.

Under static scope: answer would be (A) 1 — `f` is declared at top level, so its `x` refers to the global.

---

### Q3. Recursion Trace

```
f(n):
    if n <= 1: return 1
    return f(n-1) + f(n-2) + 1
```

What is `f(5)`?

**(A)** 9 **(B)** 13 **(C)** 15 **(D)** 17 **(E)** 25

**Answer: (C) 15.**
Compute bottom-up: f(0)=1, f(1)=1, f(2)=1+1+1=3, f(3)=3+1+1=5, f(4)=5+3+1=9, f(5)=9+5+1=**15**.

---

### Q4. OOP — Dynamic Dispatch

```
class A:
    method f(): print("A.f")
    method g(): self.f()

class B extends A:
    override method f(): print("B.f")

x := new B()
x.g()
```

Assuming virtual method dispatch (like Java), the output is:

**(A)** A.f **(B)** B.f **(C)** A.f then B.f **(D)** Compile error

**Answer: (B) B.f.**
`x.g()` calls the inherited `g` from A. Inside `g`, `self.f()` dispatches on the **runtime type** of `self`, which is B. So B's `f` runs. This is dynamic dispatch / late binding.

If the language used static dispatch (like C++ without `virtual`), `self.f()` in A's body would call A.f → answer (A). ETS tests this exact distinction.

---

### Q5. Overloading vs Overriding

Which statement is TRUE?

**(A)** Overloading is resolved at runtime based on the object's actual type.
**(B)** Overriding is resolved at compile time based on the declared type.
**(C)** Overloading uses the declared (static) types of arguments and is resolved at compile time.
**(D)** Overriding requires methods to have different parameter lists.

**Answer: (C).**
(A) and (B) swap the definitions. (D) describes overloading, not overriding.

---

### Q6. Short-Circuit and Side Effects

```
int i = 0;
boolean b = (i++ > 0) && (i++ > 0);
print(i);
```

What is printed (Java semantics)?

**(A)** 0 **(B)** 1 **(C)** 2 **(D)** Undefined

**Answer: (B) 1.**
First operand: `i++ > 0` → uses i=0 (false since 0>0 is false), then i becomes 1. Because `&&` short-circuits on false, the second operand is **not** evaluated. Final `i = 1`.

If `&&` were replaced by `&` (non-short-circuit), both increments fire → i=2.

---

### Q7. Compiler Phases

During which phase of compilation is the error `"variable x used but not declared"` typically reported?

**(A)** Lexical analysis
**(B)** Syntax analysis
**(C)** Semantic analysis
**(D)** Code generation
**(E)** Linking

**Answer: (C).**
Lexing only forms tokens. Parsing only checks grammar. Use-before-declaration is a **name-resolution / symbol-table** check — part of semantic analysis. Type errors also show up here.

---

### Q8. Garbage Collection

Which property is UNIQUE to reference counting compared to mark-and-sweep?

**(A)** Can reclaim memory in cycles of mutually referencing objects.
**(B)** Incurs per-assignment overhead.
**(C)** Requires a root set of pointers.
**(D)** Must pause all program threads periodically.

**Answer: (B).**
Reference counting increments/decrements on every pointer assignment → per-op overhead. It **cannot** reclaim cycles (so A is false; cycles are the refcount weakness). Mark-and-sweep uses a root set, so (C) is shared. (D) describes stop-the-world mark-sweep, not refcount.

---

### Q9. Closures

```
counters := []
for i := 1 to 3:
    counters.append( function() { return i } )

print(counters[0](), counters[1](), counters[2]())
```

Under typical **static scoping with shared loop variable** (Python 2/3, JavaScript `var`), the output is:

**(A)** 1 2 3 **(B)** 3 3 3 **(C)** 1 1 1 **(D)** 4 4 4 **(E)** Undefined

**Answer: (B) 3 3 3.**
All three closures capture the **same** variable `i`, not its value at creation. After the loop ends, `i` is 3 (or 4 depending on semantics — "4 4 4" is also a common distractor for languages where the loop body runs once with i then increments). If the language block-scopes the loop variable (JavaScript `let`, Java) the answer becomes (A).

**Closures close over variables, not values.**

---

### Q10. Type Systems

Which language is **statically typed AND weakly typed**?

**(A)** Python **(B)** Haskell **(C)** C **(D)** JavaScript **(E)** Ruby

**Answer: (C) C.**
C checks types at compile time (static), but allows casts between pointers, `void*` conversions, and union aliasing (weak). Haskell is static-strong. Python/Ruby are dynamic-strong. JavaScript is dynamic-weak.

---

### Q11. Bonus — Call-by-Name Classic

```
procedure p(x, y):
    y := y + 1
    x := y

i := 1
a := [10, 20, 30]
p(a[i], i)
print(a[1], a[2], i)
```

Under **call-by-name**, what is printed?

**(A)** 10 20 2 **(B)** 10 2 2 **(C)** 2 20 2 **(D)** 10 20 1

**Answer: (B) 10 2 2.**
Bind `x` ↔ `a[i]`, `y` ↔ `i` (re-evaluated each time).

- `y := y+1` → `i := i+1 = 2`.
- `x := y` → `a[i] := i`. `i` is now 2, so `a[2] := 2`. Array becomes `[10, 2, 30]`.
- Print a[1]=10, a[2]=2, i=2 → **10 2 2**.

Under reference: x bound once to `a[1]`, y to `i`. `i := 2`, then `a[1] := 2`. Print: 2 20 2 → (C). Different answer! This is the archetypal MFT question.

---

## 18. Quick-Reference Cheat Sheet

### 18.1 Parameter passing one-liner

| Mode         | Mental model                              |
| ------------ | ----------------------------------------- |
| Value        | Copy in                                   |
| Reference    | Alias                                     |
| Value-result | Copy in, copy out                         |
| Name         | Paste the argument expression at each use |
| Need         | Name + memoize                            |

### 18.2 Scoping one-liner

- **Static**: lookup by source text (compiler's job).
- **Dynamic**: lookup by call stack (runtime's job).

### 18.3 Binding time hierarchy

`design < implementation < compile < link < load < runtime-early < runtime-late`

### 18.4 OOP trio

- **Overload** = compile-time, same name / different sig.
- **Override** = runtime, same sig, subclass.
- **Overshadow/hide** = subclass redeclares field/static method; resolved by static type.

### 18.5 Polymorphism kinds

- Subtype (Shape → Circle)
- Parametric (`List<T>`)
- Ad-hoc (overloading)
- Coercion (int → double)

### 18.6 Compiler phases (order matters!)

`Lex → Parse → Semantic → IR → Optimize → Codegen`

### 18.7 GC one-liners

- **Refcount**: cheap, can't handle cycles.
- **Mark-sweep**: handles cycles, pauses, fragments.
- **Copying**: fast alloc, uses 2x memory, no fragmentation.
- **Generational**: young gen collected often; based on "most objects die young".

### 18.8 Language examples (memorize 3 per paradigm)

| Paradigm   | Languages                              |
| ---------- | -------------------------------------- |
| Imperative | C, Pascal, Fortran                     |
| OO         | Java, C++, Python, Smalltalk           |
| Functional | Haskell, ML/OCaml, Lisp/Scheme, Erlang |
| Logic      | Prolog                                 |
| Scripting  | Python, Ruby, Perl, JavaScript         |

### 18.9 Must-memorize equivalences

| Java | is call-by-value of references (NOT call-by-reference!) |
| Python | is call-by-object-sharing (same practical effect as Java) |
| C | is call-by-value; use pointers for "reference" semantics |
| C++ | supports both: `int x` (value) and `int& x` (reference) |
| Algol 60 | call-by-name (default) + call-by-value |
| Fortran | call-by-reference (classically) |
| Haskell | call-by-need (lazy) |

### 18.10 Output patterns to recognize instantly

| Swap `a` and `b` | Value: no change; Ref: swapped |
| Nested function prints outer x | Static: outer declaration's x; Dynamic: caller's most-recent x |
| Loop creates closures over `i` | Shared var: all same final value; per-iteration var (let/Java): each unique |
| Virtual call on subclass through parent ref | Virtual: subclass method; Non-virtual: parent method |
| `i++` in array index | Post: uses old value, then increments |

### 18.11 Error-catching phase by example

| Error                          | Phase             |
| ------------------------------ | ----------------- |
| `1.2.3`                        | Lexer (bad token) |
| `if (x)` missing body          | Parser            |
| `x + "hello"` type mismatch    | Semantic          |
| Array out-of-bounds            | Runtime           |
| `undefined symbol foo` at link | Linker            |
| Stack overflow from recursion  | Runtime           |

### 18.12 Time budget on test day

- 66 questions in 120 min = **~1 min 49 sec per question**. Expect ~30 secs on simple facts, 3+ min on trace questions. **Skip and flag** any question you can't answer in 60s; come back.
- For trace questions, **write** the variable state on scratch paper — don't trust mental arithmetic under pressure.
- If you see a group of 3 questions on one code block, read the block once carefully; all three questions share setup cost.

---

## Appendix: Official Sources Used

- ETS MFT CS Test Description (4CMF) — https://www.ets.org/pdfs/mft/comp-sci-test-description.pdf
- ETS MFT CS Sample Questions — https://www.ets.org/pdfs/mft/comp-sci-sample-questions.pdf
- ETS MFT Content page — https://www.ets.org/mft/about/test-content.html
- Ben Leskey's experience report — https://benleskey.com/blog/cs_mft
- scratchrobotics preparation guide — https://scratchrobotics.com/2018/07/08/how-i-prepare-for-ets-major-field-test-computer-science/
- TAMU MFT CS quals archive — https://people.engr.tamu.edu/d-walker/Quals/gre_cs_questions_compsci.pdf
- Urch forum MFT thread — https://www.urch.com/forums/gre-computer-science/123223-practice-problems-ets-major-field-test-computer-science.html

---

_End of file — Programming Fundamentals & Programming Languages. Next files in this series should cover: Data Structures & Algorithms, Systems (Arch/OS/Net), Discrete Math, Software Engineering + Information Management._

---

## 🧪 Try Yourself — Practice Questions

<details>
<summary><strong>Q1.</strong> In call-by-value, what does the callee receive?</summary>

A. The address of the argument  
B. A copy of the argument's value  
C. A reference alias to the caller's variable  
D. The unevaluated expression text

**Answer: B.** Call-by-value copies the argument value into the parameter.

</details>

<details>
<summary><strong>Q2.</strong> Which compiler phase rejects <code>int x = "hello";</code> in a statically typed language?</summary>

A. Lexical analysis  
B. Syntax analysis (parsing)  
C. Semantic analysis (type checking)  
D. Code generation

**Answer: C.** Type mismatch is caught during semantic analysis.

</details>

<details>
<summary><strong>Q3.</strong> Which memory region typically holds objects allocated with <code>new</code> in Java?</summary>

A. Stack  
B. Heap  
C. Code segment  
D. CPU registers

**Answer: B.** Java objects are heap-allocated.

</details>

<details>
<summary><strong>Q4.</strong> Which best distinguishes an abstract class from a classical (pre-Java 8) interface?</summary>

A. Interfaces can have constructors; abstract classes cannot  
B. Abstract classes can hold state and partial implementations; interfaces only declared method signatures  
C. A class can extend multiple abstract classes but implement only one interface  
D. Interfaces support private fields; abstract classes do not

**Answer: B.** Abstract classes can include fields and concrete methods.

</details>

<details>
<summary><strong>Q5.</strong> What does the Liskov Substitution Principle require?</summary>

A. Subtypes must override every method of the supertype  
B. Subtypes must be usable anywhere the supertype is expected without breaking correctness  
C. Supertypes must not have abstract methods  
D. Subtypes should always throw new checked exceptions

**Answer: B.** Subtypes must be behaviorally substitutable.

</details>

<details>
<summary><strong>Q6.</strong> Mark-and-sweep, reference counting, and generational collection are variants of what?</summary>

A. Stack allocation strategies  
B. Garbage collection algorithms  
C. Virtual memory paging  
D. Type inference techniques

**Answer: B.** These are all GC strategies.

</details>

<details>
<summary><strong>Q7.</strong> The dangling-else ambiguity is resolved in most C-family languages by binding <code>else</code> to the...</summary>

A. Outermost unmatched if  
B. Nearest unmatched if  
C. First if in the block  
D. It is a compile error

**Answer: B.** `else` binds to the nearest unmatched `if`.

</details>

<details>
<summary><strong>Q8.</strong> Which is a property of a pure function?</summary>

A. It may write to a global log  
B. Given the same inputs, it returns the same output and has no side effects  
C. It must be recursive  
D. It must return void

**Answer: B.** Pure functions are deterministic and side-effect free.

</details>

<details>
<summary><strong>Q9.</strong> Static vs dynamic scope. Given the code shown, what prints under static vs dynamic scope?</summary>

`x = 1; f() prints x; g() sets x=2 then calls f()`

A. 1 and 1  
B. 2 and 2  
C. 1 (static) and 2 (dynamic)  
D. 2 (static) and 1 (dynamic)

**Answer: C.** Static lookup uses lexical declaration, dynamic uses call stack.

</details>

<details>
<summary><strong>Q10.</strong> Recursion trace: <code>mystery(4)</code> where <code>mystery(n)=n*mystery(n-2)</code> and base is 1.</summary>

A. 24  
B. 8  
C. 6  
D. 4

**Answer: B.** `4 * 2 * 1 = 8`.

</details>

<details>
<summary><strong>Q11.</strong> Java-like overload/override: <code>A a = new B(); a.p(3);</code> where B overrides <code>p(int)</code>. What prints?</summary>

A. A-int  
B. B-int  
C. B-double  
D. Compile error

**Answer: B.** Overload selection is static; override dispatch is dynamic.

</details>

<details>
<summary><strong>Q12.</strong> With <code>a=0</code>, <code>b=5</code>, evaluate <code>a != 0 && b / a > 1</code>.</summary>

A. Runtime division-by-zero  
B. false (no division performed)  
C. true  
D. Compile error

**Answer: B.** Short-circuit prevents division by zero.

</details>

<details>
<summary><strong>Q13.</strong> With strict left-to-right evaluation, what is <code>r</code> for <code>int i=2; int r=(i++) + (++i) + i;</code>?</summary>

A. 8  
B. 9  
C. 10  
D. 11

**Answer: C.** Terms are 2, 4, 4 so total is 10.

</details>

<details>
<summary><strong>Q14.</strong> JavaScript closure loop with <code>var i</code> from 0 to 2; each function returns <code>i</code>. What prints?</summary>

A. 0 1 2  
B. 1 2 3  
C. 3 3 3  
D. undefined undefined undefined

**Answer: C.** `var` is function-scoped; all closures see final `i=3`.

</details>

<details>
<summary><strong>Q15.</strong> Exception flow with inner <code>finally("A")</code>, outer <code>catch("B")</code>, outer <code>finally("C")</code>. Output?</summary>

A. A B C  
B. B A C  
C. A C  
D. B C

**Answer: A.** Inner finally runs, then catch, then outer finally.

</details>

<details>
<summary><strong>Q16.</strong> For <code>xs=[1,2,3]</code>, <code>ys=map(xs,x->x*2)</code> with pure non-mutating map, what prints?</summary>

A. [2,4,6] [2,4,6]  
B. [1,2,3] [2,4,6]  
C. [1,2,3] [1,2,3]  
D. [2,4,6] [1,2,3]

**Answer: B.** `map` returns a new list and keeps `xs` unchanged.

</details>

<details>
<summary><strong>Q17.</strong> Call-by-name swap trap: <code>swap(i, A[i])</code>. What is the accepted exam outcome?</summary>

A. i = 10, A = [1, 20, 30]  
B. i = 10, A = [10, 1, 30]  
C. i = 1, A = [1, 20, 30]  
D. i = 10, A = [10, 20, 30]

**Answer: A.** This classic example shows call-by-name breaks swap.

</details>

<details>
<summary><strong>Q18.</strong> Overload vs override: <code>Animal x = new Dog(); Dog y = new Dog(); x.speak(y);</code> prints?</summary>

A. dog+dog  
B. dog+animal  
C. animal+animal  
D. Compile error

**Answer: B.** Overload resolution picks `speak(Animal)` statically; Dog override runs dynamically.

</details>

<details>
<summary><strong>Q19.</strong> JavaScript closure loop using <code>let i</code> from 0 to 2; what prints?</summary>

A. 0 1 2  
B. 3 3 3  
C. 2 2 2  
D. undefined undefined undefined

**Answer: A.** `let` creates a fresh binding per iteration.

</details>

<details>
<summary><strong>Q20.</strong> Same variable passed twice to <code>p(i,i)</code>: result for (a) call-by-reference and (b) call-by-value-result?</summary>

A. (a) 7, (b) 7  
B. (a) 7, (b) 6  
C. (a) 6, (b) 7  
D. (a) 6, (b) 6

**Answer: B.** Reference aliases both params (7); value-result copy-back ends at 6.

</details>

---

## 🔧 Supplement: Bit Manipulation & C Pointer Arithmetic

Low-level bit tricks and pointer semantics are staple MFT CS questions. They test whether you truly understand how data is laid out in memory.

### Bit Manipulation Tricks (Must-Know)

| Trick                  | Formula                                         | Use                                                     |
| ---------------------- | ----------------------------------------------- | ------------------------------------------------------- |
| Clear lowest set bit   | `x & (x-1)`                                     | Power-of-2 check: `x & (x-1) == 0` and `x > 0`          |
| Isolate lowest set bit | `x & -x`                                        | Lowest 1-bit as a mask (Fenwick tree)                   |
| Set lowest unset bit   | `x \| (x+1)`                                    | Fill next 0                                             |
| Identity / toggle      | `x ^ 0 == x`, `x ^ x == 0`                      | XOR swap, XOR linked lists, find the odd one out        |
| Count set bits         | Brian Kernighan: `while(x){ x &= x-1; cnt++; }` | Runs in O(popcount), not O(word size)                   |
| Test bit k             | `(x >> k) & 1`                                  | Read k-th bit                                           |
| Set bit k              | `x \| (1 << k)`                                 |                                                         |
| Clear bit k            | `x & ~(1 << k)`                                 |                                                         |
| Toggle bit k           | `x ^ (1 << k)`                                  |                                                         |
| Multiply / divide by 2 | `x << 1`, `x >> 1`                              | Careful with signed right shift (arithmetic vs logical) |

**XOR swap (no temp):**

```c
a ^= b;   // a = a^b
b ^= a;   // b = b^(a^b) = a
a ^= b;   // a = (a^b)^a = b
```

⚠️ Breaks if `a` and `b` alias the same memory (both become 0).

**Sign extension:** when widening a signed value (e.g., `int8_t → int32_t`), the sign bit is replicated into the upper bits. Unsigned values are zero-extended.

**Endianness:** little-endian stores LSB at lowest address (x86/ARM typical); big-endian stores MSB first (network byte order). Detect:

```c
union { int i; char c[4]; } u = { 1 };
u.c[0] == 1 → little-endian
```

### C Pointer Arithmetic

- `ptr + n` advances by `n * sizeof(*ptr)` **bytes**, not `n` bytes.
- `ptr1 - ptr2` yields a `ptrdiff_t` — the number of **elements** between them (only defined within the same array).
- `arr[i]` is defined as `*(arr + i)`, so `i[arr]` also compiles and works (`*(i + arr)`). A classic trap.
- **Pointer to array vs array of pointers:**
  - `int (*p)[10];` — a single pointer to an array of 10 ints.
  - `int *p[10];` — an array of 10 pointers to int.
- **Function pointer:** `int (*fp)(int, int);` — pointer to function taking two ints, returning int.
- **const qualifiers** (read right-to-left):
  - `const int *p` / `int const *p` — pointer to const int; `*p = x;` illegal, `p++` legal.
  - `int * const p` — const pointer to int; `*p = x;` legal, `p = q;` illegal.
  - `const int * const p` — both locked.
- **NULL pointer:** dereferencing is UB. **Dangling pointer:** points to freed memory. **Wild pointer:** uninitialized.
- `malloc`/`free`: every `malloc` needs exactly one `free`. Double-free and use-after-free are classic UB sources.
- **`sizeof` trap:**
  ```c
  int a[10];          // sizeof(a) == 40 (on 4-byte int)
  int *p = a;         // sizeof(p) == 8 on 64-bit, regardless of what it points to
  void f(int a[10]){ sizeof(a); } // also pointer size — array decays on function param!
  ```

### Practice MCQs

<details>
<summary><strong>Q1.</strong> What does <code>x & (x-1) == 0</code> test for (assuming <code>x &gt; 0</code>)?</summary>

A) x is even B) x is odd C) x is a power of 2 D) x has exactly 2 set bits

**Answer: (C) x is a power of 2.**
Clearing the lowest set bit leaves 0 only if exactly one bit was set.

</details>

<details>
<summary><strong>Q2.</strong> After <code>int x = 12; x &amp;= x - 1;</code> what is <code>x</code>?</summary>

A) 11 B) 8 C) 4 D) 0

**Answer: (B) 8.**
12 is <code>1100</code>; <code>12 &amp; 11</code> is <code>1000</code>, which is 8.

</details>

<details>
<summary><strong>Q3.</strong> In C, <code>int a[5]; int *p = a;</code> what does <code>sizeof(a) / sizeof(p)</code> evaluate to on a 64-bit system (int=4 bytes)?</summary>

A) 5 B) 2 C) 2.5 D) 1

**Answer: (B) 2.**
`sizeof(a)` is 20 and `sizeof(p)` is 8, so the integer division is 2. The trap is treating the array like a pointer.

</details>

<details>
<summary><strong>Q4.</strong> Which declaration describes a pointer to a constant integer?</summary>

A) <code>int * const p;</code> B) <code>const int *p;</code> C) <code>const int _ const p;</code> D) <code>int const _ const p;</code>

**Answer: (B) <code>const int \*p;</code>.**
The pointed-to integer is const; the pointer itself can still be reassigned.

</details>

<details>
<summary><strong>Q5.</strong> Expression <code>3[arr]</code> where <code>int arr[] = {10,20,30,40,50};</code> evaluates to:</summary>

A) 10 B) 30 C) 40 D) compile error

**Answer: (C) 40.**
`3[arr]` is the same as `arr[3]`, which is 40.

</details>

<details>
<summary><strong>Q6.</strong> XOR swap fails when:</summary>

A) values are equal B) one value is 0 C) both args alias the same memory D) values are negative

**Answer: (C) both args alias the same memory.**
If both references point at the same location, the first XOR zeros the memory and the swap corrupts the value.

</details>

<details>
<summary><strong>Q7.</strong> <code>int (*fp)(int);</code> declares:</summary>

A) array of function pointers B) function returning pointer to int C) pointer to function taking int and returning int D) function pointer array of size int

**Answer: (C) pointer to function taking int and returning int.**
The parentheses bind <code>\*fp</code> before the parameter list.

</details>

<details>
<summary><strong>Q8.</strong> Counting set bits of <code>0b10110100</code> (180) via Brian Kernighan takes how many loop iterations?</summary>

A) 8 B) 4 C) 7 D) 1

**Answer: (B) 4.**
Kernighan's loop runs once per set bit, and <code>10110100</code> has four 1-bits.

</details>

---
