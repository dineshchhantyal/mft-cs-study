# MFT CS — Operating Systems (~8–10%)

Exam: ETS Major Field Test in Computer Science, May 1, 2026.

### Process

- A program in execution. Has its own address space (code, data, heap, stack), open files, PID, registers.
- Created by `fork()` (UNIX) — child gets copy of parent's address space (copy-on-write in practice).

- `exec()` replaces the process image; `wait()` blocks parent until child finishes; `exit()` terminates.

### Thread

- Lightweight unit of execution _within_ a process. Shares address space (code/data/heap/open files) with other threads in the process.
- Each thread has its own: **stack, registers, program counter, thread ID**.

- Context switch between threads of the same process is cheaper than between processes (no TLB/page-table flush).

### User-level vs Kernel-level threads

- **User-level (green threads):** fast context switch, but one blocking syscall blocks all threads; no true multi-core parallelism.

- **Kernel-level:** OS aware, can run on multiple cores, but more expensive context switch.
- **Hybrid (M:N):** many user threads mapped to N kernel threads.

### Process States & Transitions

```
   [new] --admit--> [ready] --dispatch--> [running] --exit--> [terminated]

                      ^                      | |
                      |--I/O completes-- [waiting]
                      |                      |

                      |--timer interrupt (preempt)--
```

Five canonical states: **new, ready, running, waiting (blocked), terminated**.

Transitions:

- new → ready: admitted
- ready → running: scheduler dispatch

- running → ready: preempted (timer)
- running → waiting: I/O or event wait

- waiting → ready: I/O complete / event
- running → terminated: exit

### PCB (Process Control Block) contents

- PID, process state
- q too large → becomes FCFS. q too small → excessive context-switch overhead.
- Rule of thumb: q should be large compared to context-switch time, ~10–100 ms. Typically want 80% of CPU bursts < q.

#### Multilevel Queue

- Multiple ready queues (e.g., foreground RR, background FCFS) with fixed priorities.

#### Multilevel Feedback Queue (MLFQ)

- Processes move between queues based on behavior (CPU-bound demoted, I/O-bound promoted).
- Approximates SJF without prior knowledge. Most real OSes use MLFQ variants.

### Computing waiting & turnaround time

Example (all arrive at t=0): P1 burst 24, P2 burst 3, P3 burst 3.

- **FCFS order P1,P2,P3:** waits = 0, 24, 27 → avg = 17
- **SJF order P2,P3,P1:** waits = 0, 3, 6 → avg = 3
- **RR q=4:** P1:0–4, P2:4–7, P3:7–10, P1:10–30. Waits: P1=6, P2=4, P3=7 → avg = 5.66

**Gantt chart method:** draw execution timeline, read completion times, subtract arrival/burst.

---

## 3. Synchronization

### Race Condition

Outcome depends on interleaving. Classic: `count++` = load, inc, store — two threads can both read old value.

### Critical Section Problem

Need mutual exclusion, progress (no indefinite postponement when no one is in CS), bounded waiting.

### Primitives

- **Mutex lock:** binary, owned — only the locker can unlock. `acquire()/release()`.
- **Semaphore:** integer `S`, atomic `wait(S)` (P: decrement, block if <0) and `signal(S)` (V: increment, wake one).
  - **Binary semaphore** (0/1) ~ mutex (but not owned).
  - **Counting semaphore** — limit N resources.
- **Monitor:** high-level construct; at most one thread inside. **Condition variables** with `wait()`/`signal()` for blocking inside a monitor. Hoare vs Mesa semantics (Mesa is usual — use `while` around `wait()`).
- **Spinlock:** busy wait; OK for very short CS on multiprocessors.

### Classic Problems

- **Producer–Consumer (bounded buffer):** sema `empty=N`, `full=0`, `mutex=1`. Producer: `wait(empty); wait(mutex); put; signal(mutex); signal(full);`
- **Readers–Writers:** multiple readers OR one writer. Reader preference can starve writers; writer preference can starve readers.
- **Dining Philosophers:** 5 philosophers, 5 forks; prevent deadlock by: pick up lower-numbered first; or allow only 4 to sit at once; or monitor-based.

### Deadlock in synchronization

Wrong ordering of `wait()`s causes deadlock: T1 `wait(A); wait(B)`; T2 `wait(B); wait(A)`. Always lock in a global order.

---

## 4. Deadlock

### Four Necessary Conditions (ALL must hold)

1. **Mutual exclusion** — at least one non-shareable resource
2. **Hold and wait** — process holds one and requests another
3. **No preemption** — resources released only voluntarily
4. **Circular wait** — cycle in the wait-for graph

### Strategies

- **Prevention** — negate one of the 4 conditions (e.g., impose resource ordering → no circular wait; or require all resources up front → no hold-and-wait).
- **Avoidance** — require advance info on max needs; grant only if system stays in **safe state**. **Banker's algorithm**.
- **Detection & Recovery** — let deadlock happen, detect via resource-allocation graph cycle + pending (for multi-instance, use matrix algorithm). Recovery: kill processes or preempt resources.
- **Ignore** (ostrich algorithm) — what most OSes do in practice.

### Resource-Allocation Graph

- Single instance per resource type: cycle ⇔ deadlock.
- Multiple instances: cycle is _necessary_ but not sufficient.

### Banker's Algorithm (avoidance)

Data structures for n processes, m resource types:

- `Available[m]`
- `Max[n][m]`
- `Allocation[n][m]`
- `Need[n][m] = Max − Allocation`

**Safety check:** find a sequence such that each process's `Need ≤ Work` (current available + released). If one exists, state is safe.

**Worked example:**

- Available = (3, 3, 2); Max and Allocation:

| P   | Alloc | Max   | Need  |
| --- | ----- | ----- | ----- |
| P0  | 0,1,0 | 7,5,3 | 7,4,3 |
| P1  | 2,0,0 | 3,2,2 | 1,2,2 |
| P2  | 3,0,2 | 9,0,2 | 6,0,0 |
| P3  | 2,1,1 | 2,2,2 | 0,1,1 |
| P4  | 0,0,2 | 4,3,3 | 4,3,1 |

Work = (3,3,2). P1 Need=(1,2,2) ≤ Work → run, Work=(5,3,2). P3 Need=(0,1,1) ≤ Work → Work=(7,4,3). P0 Need=(7,4,3) ≤ Work → Work=(7,5,3). P2 → Work=(10,5,5). P4 → Work=(10,5,7). Safe sequence: **P1, P3, P0, P2, P4**. → SAFE.

---

## 5. Memory Management

### Contiguous Allocation

- Each process gets one contiguous block. Uses base + limit registers.
- **Fragmentation:**
  - **External:** free holes scattered, total free enough but no single block fits. Compaction fixes (expensive).
  - **Internal:** allocated block larger than requested (e.g., fixed partitions, page frames).

### Paging

- Logical address = (page #, offset). Physical address = (frame #, offset).
- Page table per process maps page → frame.
- **Page size tradeoff:** small pages ⇒ less internal frag but bigger page table & more TLB misses. Large ⇒ more internal frag but smaller table.
- Eliminates **external** fragmentation; still has **internal** (last page).
- **TLB** — cache for page table entries; effective access time = h·(TLB+mem) + (1−h)·(TLB+2·mem).

### Segmentation

- Logical addr = (segment #, offset). Variable-size segments reflect program structure (code, stack, heap).
- Has **external** fragmentation; no internal.

### Segmented Paging (x86, Multics)

- Segment table → each segment is paged. Combines protection of segmentation with frag-free paging.

---

## 6. Virtual Memory

### Demand Paging

- Pages loaded only on access. Page fault → trap → OS loads page from disk → restart instruction.
- Effective access time = (1−p)·mem_time + p·page_fault_service. Even tiny p devastates performance.

### Page Replacement Algorithms

- **FIFO** — evict oldest loaded. Suffers **Belady's anomaly** (more frames → more faults possible).
- **Optimal (OPT / MIN / Belady's)** — evict page not used for the longest future time. **Unimplementable** but lower-bound benchmark.
- **LRU** — evict least-recently used. Good approximation of OPT; expensive to implement exactly (stack or counters).
- **Clock / Second-Chance** — circular buffer + reference bit; cheap LRU approximation. Widely used.
- **LFU** — evict least frequently used. Bad for pages used heavily then never again.
- **MFU** — argues page with small count probably just arrived. Rare.

### Belady's Anomaly

Reference string `1,2,3,4,1,2,5,1,2,3,4,5`:

- 3 frames FIFO → 9 faults
- 4 frames FIFO → 10 faults (!). Only FIFO exhibits this; LRU and OPT are **stack algorithms** and cannot.

### Working Set Model

- W(t, Δ) = set of pages referenced in last Δ accesses.
- Sum of working-set sizes over processes > frames → **thrashing** (CPU spends all time paging).
- OS can suspend processes when Σ|W| > frames.

### Thrashing

- Symptom: CPU utilization plummets, paging I/O saturated. Naïve OS responds by adding more processes → worse. Fix: working-set or page-fault-frequency control.

### Page Replacement Example

Reference: 7,0,1,2,0,3,0,4,2,3,0,3,2,1,2,0,1,7,0,1 with 3 frames:

- FIFO faults = 15
- LRU faults = 12
- OPT faults = 9

---

## 7. File Systems

### Directory Structures

- Single-level, two-level, tree, acyclic-graph (hard links), general graph (soft/symbolic links — cycles possible, use reference counts carefully).

### File Allocation

- **Contiguous:** fast sequential & random; suffers external frag; hard to grow.
- **Linked (FAT):** no external frag; poor random access; FAT caches link table.
- **Indexed (inode):** each file has an index block listing its data blocks. UNIX inode has direct + single/double/triple indirect pointers → supports large files with small overhead for small files.

### Inode contents

Mode, owner, size, timestamps, link count, pointers to data blocks. File _name_ is in the directory, **not** in the inode.

### FAT

File Allocation Table indexed by block #; each entry points to next block or EOF marker. Simple, used by FAT12/16/32.

---

## 8. Disk Scheduling

Given a queue of cylinder requests and a current head position, compute total head movement.

Example: queue = 98, 183, 37, 122, 14, 124, 65, 67; head at 53; disk has cylinders 0–199.

- **FCFS:** serve in order. Movement = 640.
- **SSTF (Shortest Seek Time First):** always nearest. May starve far requests. Movement = 236.
- **SCAN (elevator):** go in one direction to end, reverse. Movement (moving up then down) = 208.
- **C-SCAN:** like SCAN but on reaching end, jump to other end without servicing; uniform wait.
- **LOOK / C-LOOK:** like SCAN/C-SCAN but reverse at last _request_, not at the end of disk.

Total movement for LOOK (head 53, up first): 53→65→67→98→122→124→183→37→14. Moves = (183−53) + (183−14) = 130 + 169 = 299.

---

## 9. I/O

- **Blocking I/O:** caller waits until complete. Simple but can idle.
- **Non-blocking:** returns immediately with whatever data is available.
- **Asynchronous:** returns immediately; notification on completion (signal/callback).
- **Buffering:** smooths speed mismatch between producer/consumer; enables copy-semantics.
- **Spooling:** buffer output destined for slow device (printer).
- **DMA:** device transfers to memory without CPU; CPU interrupted only on completion.

---

## 10. Key Tricks / Quick Rules

- **SJF optimal for avg waiting time.** Proof sketch: swap any adjacent pair where shorter is later; avg wait only decreases.
- **Optimal page replacement is optimal but unimplementable** (requires future reference knowledge).
- **LRU ≈ OPT in practice** because programs exhibit temporal locality.
- **Paging: internal frag, no external. Segmentation: external frag, no internal.**
- **Only FIFO suffers Belady's anomaly.** LRU/OPT are stack algorithms.
- **Mutex is owned, binary semaphore is not.**
- **Deadlock requires all 4 conditions simultaneously.**
- **Cycle in RAG:** single instance → deadlock; multi-instance → only necessary.
- **Thread ≪ process** for switching cost (shared address space, shared TLB).
- **Page fault is a trap, not an interrupt.**
- **TLB miss vs page fault:** TLB miss → walk page table (fast); page fault → page not in memory → disk I/O (slow).

---

## 11. MCQs (10+)

**Q1.** Which is NOT in the PCB?
(a) PID (b) program counter (c) list of open files (d) source code of the program.

<details>
<summary>Reveal answer</summary>

**Ans:** (d). PCB references memory, not the program's source.

</details>

<details>
<summary>Reveal answer</summary>

**A1.** **(b) running → waiting.** A blocking I/O causes the running process to wait for I/O completion. Ready → waiting is not a valid transition (must run first).

## </details>

## 1. Processes & Threads

<details>
<summary>Reveal answer</summary>

**A2.** **(d) stack.** Each thread has its own stack and registers; heap/globals/file descriptors are shared.

</details>

**Q2.** Processes P1, P2, P3 arrive at t=0 with bursts 8, 4, 9. Under SJF (non-preemptive), average waiting time?

<details>
<summary>Reveal answer</summary>

Order: P2 (0–4), P1 (4–12), P3 (12–21). Waits = 4, 0, 12 → avg = **16/3 ≈ 5.33**.

</details>

**Q3.** Reference string 1,2,3,4,1,2,5,1,2,3,4,5 with 3 frames, FIFO:

<details>
<summary>Reveal answer</summary>

Faults: 1(F),2(F),3(F),4(F evict 1),1(F evict 2),2(F evict 3),5(F evict 4),1(hit),2(hit),3(F evict 1),4(F evict 2),5(hit) → **9 faults**. With 4 frames → **10**. Belady's anomaly.

</details>

<details>
<summary>Reveal answer</summary>

**A3.** **(c) TLB entries.** The TLB is a CPU-hardware cache, flushed/refilled by hardware — not stored per-process in the PCB. Page-table base pointer is in PCB, but not TLB contents.

</details>

**Q4.** Which of the four deadlock conditions does "requesting all resources upfront" negate?

<details>
<summary>Reveal answer</summary>

**Ans:** Hold and wait.

</details>

<details>
<summary>Reveal answer</summary>

**A4.** FCFS order A,B,C,D. Waits: A=0, B=6, C=14, D=21. Avg = (0+6+14+21)/4 = 41/4 = **10.25**.

</details>

**Q5.** A counting semaphore initialized to 3 allows up to \_ threads in the critical section.

<details>
<summary>Reveal answer</summary>

**Ans:** **3**.

</details>

<details>
<summary>Reveal answer</summary>

**A5.** SJF order D(3), A(6), C(7), B(8). Waits: D=0, A=3, C=9, B=16. Avg = 28/4 = **7.0**.

</details>

**Q6.** Disk head at 50; queue = 82,170,43,140,24,16,190. Compute SSTF total movement.

<details>
<summary>Reveal answer</summary>

Nearest to 50: 43 (7). Next nearest to 43: 24 (19). Next: 16 (8). Next: 82 (66). Next: 140 (58). Next: 170 (30). Next: 190 (20). Total = 7+19+8+66+58+30+20 = **208**.

</details>

<details>
<summary>Reveal answer</summary>

**A6.** SRTF timeline: t=0 P1 runs (rem 7). t=2 P2 arrives (4 < 5), preempt → P2 runs. t=4 P3 arrives (1 < 2), preempt → P3 runs 4–5. t=5 P4 arrives (4); remaining: P1=5, P2=2, P4=4 → P2 runs 5–7. t=7: P1=5, P4=4 → P4 runs 7–11. t=11: P1=5 runs 11–16. **P1 completes at t=16.**

</details>

**Q7.** Which page replacement is an OS likely to approximate in hardware?

<details>
<summary>Reveal answer</summary>

**Ans:** **Clock / second-chance** (LRU approximation using reference bits).

</details>

<details>
<summary>Reveal answer</summary>

**A7.** Queue order arrival P1,P2,P3,P4. RR q=3: P1 0–3(rem 2), P2 3–6(done), P3 6–9(rem 3), P4 9–11(done), P1 11–13(done), P3 13–16(done). Completion order: **P2, P4, P1, P3**.

</details>

**Q8.** Which of the following causes internal fragmentation?
(a) paging (b) pure segmentation (c) variable partitions (d) compaction.

<details>
<summary>Reveal answer</summary>

**Ans:** (a) paging (last page of a process may be partially full).

</details>

<details>
<summary>Reveal answer</summary>

**A8.** S=2 lets 2 in, the other **3 block**.

</details>

**Q9.** A system has 12 tape drives, 3 processes each needing max 5. Is it deadlock-free?

<details>
<summary>Reveal answer</summary>

If each holds 4, requests 1 more → 12 allocated, 0 free → deadlock. But if each holds ≤ 3 (sum 9), at least 3 free and one can complete. Need 3·5 − n + 1 ≤ 12 ⇒ n ≥ 4, so with 12 ≥ 3·(5−1)+1 = 13? Actually rule: Σmax − n + 1 ≤ resources ⇒ 15 − 3 + 1 = 13 > 12 → **not guaranteed deadlock-free**. Answer: may deadlock.

</details>

<details>
<summary>Reveal answer</summary>

**A9.** **(b) readers–writers.** The writer-starvation variant prioritizes writers to prevent them being perpetually blocked by readers.

</details>

**Q10.** RR with q = 4, processes (arrival, burst): P1(0,5), P2(1,4), P3(2,2). Completion times?

<details>
<summary>Reveal answer</summary>

Timeline: P1 0–4, P2 4–8, P3 8–10, P1 10–11. P1 done at 11, P2 at 8, P3 at 10.

</details>

<details>
<summary>Reveal answer</summary>

**A10.** **(b) ownership.** Only the thread that locked a mutex may unlock it; a binary semaphore can be signaled by any thread. (a) is false — mutexes can be shared; (c) is false.

</details>

**Q11.** Which scheduling may cause starvation?
(a) FCFS (b) RR (c) SJF (d) priority without aging.

<details>
<summary>Reveal answer</summary>

**Ans:** (c) and (d). Best single answer: **priority without aging** (or SJF).

</details>

<details>
<summary>Reveal answer</summary>

**A11.** **Banker's.** Available = Total − Σalloc = (10,5,7) − (7,2,5) = **(3,3,2)**. Need = Max − Alloc: P0(7,4,3), P1(1,2,2), P2(6,0,0), P3(0,1,1), P4(4,3,1). Check: P1 need(1,2,2) ≤ (3,3,2) ✓ → Avail (5,3,2). P3 need(0,1,1) ≤ ✓ → (7,4,3). P0(7,4,3) ≤ ✓ → (7,5,3). P2(6,0,0) ≤ ✓ → (10,5,5). P4(4,3,1) ≤ ✓. **Safe sequence: P1, P3, P0, P2, P4.**

</details>

**Q12.** Belady's anomaly can occur in which algorithm?

<details>
<summary>Reveal answer</summary>

**Ans:** **FIFO only** (among the standard ones).

</details>

<details>
<summary>Reveal answer</summary>

**A12.** **(b) definite deadlock** — only in single-instance RAG. With multiple instances a cycle is necessary but not sufficient.

</details>

**Q13.** In UNIX, which is stored in the directory, not the inode?

<details>
<summary>Reveal answer</summary>

**Ans:** **File name**.

</details>

<details>
<summary>Reveal answer</summary>

**A13.** Pages = 2^32 / 2^12 = 2^20 entries × 4 B = **4 MB per process**. (This motivates multi-level page tables.)

</details>

**Q14.** Effective access time with TLB hit ratio 80%, TLB 20 ns, memory 100 ns:

<details>
<summary>Reveal answer</summary>

EAT = 0.8·(20+100) + 0.2·(20+200) = 0.8·120 + 0.2·220 = 96 + 44 = **140 ns**.

</details>

<details>
<summary>Reveal answer</summary>

**A14.** **(b) pure segmentation.** Variable-size segments → external fragmentation; no fixed page frames → no internal. Paging has internal only; fixed partitions have internal.

</details>

**Q15.** Which cannot be used to avoid deadlock?
(a) Banker's (b) resource ordering (c) pre-emption of resources (d) increasing time quantum.

<details>
<summary>Reveal answer</summary>

**Ans:** (d). Quantum is scheduling, not deadlock.

</details>

---

## 12. Cheat Sheet

```
PROCESS vs THREAD
  Process: own addr space, own PCB, expensive switch
  Thread:  shared addr space, own stack+regs, cheap switch

STATES: new → ready ↔ running → terminated
                     ↕
                   waiting

SCHEDULING
  FCFS   — convoy effect
  SJF    — optimal avg wait; needs burst prediction
  SRTF   — preemptive SJF
  RR(q)  — q too big ⇒ FCFS; q too small ⇒ overhead
  Prio   — starvation → aging
  MLFQ   — approximates SJF adaptively

SYNC
  Mutex (owned, binary)
  Semaphore wait/signal (P/V)
  Monitor + condition vars (Mesa: while-wait)
  Producer/Consumer: empty=N, full=0, mutex=1

DEADLOCK — need all 4:
  mut-ex, hold-wait, no-preempt, circular-wait
  Prevent | Avoid (Banker) | Detect | Ignore

MEMORY
  Paging       → internal frag, no external
  Segmentation → external frag, no internal
  Seg-paging   → best of both

PAGE REPLACEMENT
  OPT  — optimal, unimplementable
  LRU  — stack alg, no Belady's
  FIFO — simple, has Belady's
  Clock/second-chance — practical LRU approx

DISK (head movement)
  FCFS, SSTF (starvation), SCAN, C-SCAN, LOOK, C-LOOK

FILE ALLOC
  Contig (ext frag), Linked/FAT (slow random),
  Indexed/inode (UNIX; direct + indirect pointers)

BIG RULES
  SJF optimal avg wait.
  OPT optimal page replacement.
  LRU ≈ OPT in practice.
  Only FIFO has Belady's anomaly.
  Page fault: trap → disk I/O (ms); TLB miss: walk table (ns).
  Deadlock ⇔ all 4 conditions.
  Cycle in single-instance RAG ⇔ deadlock.
```

---

## 🧪 Try Yourself — Practice Questions

<details>
<summary>Reveal answer</summary>

**A15.** LRU, 3 frames, string 7,0,1,2,0,3,0,4,2,3,0,3,2:
7(F)[7], 0(F)[7,0], 1(F)[7,0,1], 2(F evict 7)[0,1,2], 0(H)[1,2,0], 3(F evict 1)[2,0,3], 0(H)[2,3,0], 4(F evict 2)[3,0,4], 2(F evict 3)[0,4,2], 3(F evict 0)[4,2,3], 0(F evict 4)[2,3,0], 3(H)[2,0,3], 2(H). Faults = **9**.

</details>

**Q1.** A process executing a blocking `read()` transitions from which state to which?
(a) running → ready (b) running → waiting (c) ready → waiting (d) waiting → running

**Q2.** Two threads of the same process share all of the following EXCEPT:
(a) heap (b) global variables (c) open file descriptors (d) stack

**Q3.** Which field would you NOT expect to find in a PCB?
(a) CPU register snapshot (b) scheduling priority (c) cached translations from the TLB (d) list of open files

**Q4.** Jobs arrive at t=0 in order A, B, C, D with bursts 6, 8, 7, 3. Under **FCFS**, what is the average waiting time?

**Q5.** Same jobs as Q4 with bursts A=6, B=8, C=7, D=3 (all arriving t=0). Under **non-preemptive SJF**, average waiting time?

**Q6.** Processes with (arrival, burst): P1(0,7), P2(2,4), P3(4,1), P4(5,4). Under **SRTF (preemptive SJF)**, what is P1's completion time?

**Q7.** Round Robin with quantum = 3. Arrival order at t=0: P1(5), P2(3), P3(6), P4(2). In what order do processes **first complete**?

**Q8.** A counting semaphore S is initialized to 2. Threads execute `wait(S); CS; signal(S);`. If 5 threads call `wait(S)` simultaneously, how many block?

**Q9.** Which classic synchronization problem requires avoiding writer starvation?
(a) dining philosophers (b) readers–writers (c) producer–consumer (d) sleeping barber

**Q10.** A mutex differs from a binary semaphore primarily because:
(a) mutex cannot be used between processes (b) a mutex has ownership — only the locker can unlock (c) binary semaphores cannot reach 0 (d) mutex operations are non-atomic

**Q11.** A system has 3 resource types with totals (A=10, B=5, C=7). Current allocation:
P0: (0,1,0); P1: (2,0,0); P2: (3,0,2); P3: (2,1,1); P4: (0,0,2). Max needs: P0(7,5,3), P1(3,2,2), P2(9,0,2), P3(2,2,2), P4(4,3,3).
Is the state safe? If yes, give one safe sequence.

**Q12.** In a **single-instance** resource allocation graph, a cycle implies:
(a) possible deadlock only (b) definite deadlock (c) starvation (d) nothing

**Q13.** Logical address space = 32 bits, page size = 4 KB, each PTE = 4 B. Size of a single-level page table per process?

**Q14.** Which suffers **external** fragmentation but not internal?
(a) paging (b) pure segmentation (c) fixed partitions (d) paged segmentation

**Q15.** Reference string: 7,0,1,2,0,3,0,4,2,3,0,3,2 with **3 frames** under **LRU**. How many page faults?

**Q16.** Same reference string as Q15 with **3 frames** under **OPT**. Page faults?

<details>
<summary>Reveal answer</summary>

**A16.** OPT, 3 frames, same string:
7(F),0(F),1(F),2(F evict 7 — next use: 7 never, 0 soon, 1 farther → evict 7)[0,1,2]. Actually evict farthest-future: at step 4 future = 0,3,0,4,2,3,0,3,2. 7 not used → evict 7. [0,1,2]. 0(H). 3(F): future after = 0,4,2,3,0,3,2; among {0,1,2}: 1 never used again → evict 1. [0,2,3]. 0(H). 4(F): future = 2,3,0,3,2; among {0,2,3}: 0 used at idx, 2 next, 3 next; farthest = 0 → evict 0. [2,3,4]. 2(H). 3(H). 0(F): future = 3,2; among {2,3,4}: 4 never → evict 4. [0,2,3]. 3(H). 2(H). Faults = 7(F),0(F),1(F),2(F),3(F),4(F),0(F) = **7**.

</details>

**Q17.** TLB access = 10 ns, memory = 100 ns, TLB hit ratio = 90%, page table is single-level in memory. **EAT** (no page faults)?

<details>
<summary>Reveal answer</summary>

**A17.** Single-level: on TLB hit → TLB + mem = 10+100 = 110. On miss → TLB + mem (PT) + mem (data) = 10+100+100 = 210. EAT = 0.9·110 + 0.1·210 = 99 + 21 = **120 ns**.

</details>

**Q18.** A UNIX inode uses 12 direct, 1 single-indirect, 1 double-indirect pointer. Block = 4 KB, pointer = 4 B. Max file size?

<details>
<summary>Reveal answer</summary>

**A18.** Pointers per block = 4096/4 = 1024. Direct = 12 blocks. Single-indirect = 1024 blocks. Double-indirect = 1024·1024 = 2^20 blocks. Total blocks = 12 + 1024 + 1048576 ≈ 1,049,612 blocks × 4 KB ≈ **~4.0 GB** (exactly 1,049,612·4 KB = 4,198,448 KB ≈ 4.00 GiB).

</details>

**Q19.** Disk head at cylinder 50. Request queue: 82, 170, 43, 140, 24, 16, 190. Under **SCAN** moving toward higher cylinders first (end = 199), total head movement?

<details>
<summary>Reveal answer</summary>

**A19.** SCAN toward higher from 50 to 199, then reverse. Sorted: 16,24,43,50,82,140,170,190. Go up: 82,140,170,190,199 → movement 199−50 = 149. Then down to 43: 199−43 = 156. Continue to 24,16: already past. Total = 149 + (199−16) = 149 + 183 = **332**. (Alt: 50→199→16 = 149+183=332.)

</details>

**Q20.** Same disk setup as Q19 (head at 50, queue 82,170,43,140,24,16,190). Under **LOOK** moving toward higher first, total head movement?

---

<details>
<summary>Reveal answer</summary>

**A20.** LOOK toward higher first: 50→82→140→170→190 (movement 190−50=140), then reverse to 43→24→16 (190−16=174). Total = 140 + 174 = **314**.

</details>
