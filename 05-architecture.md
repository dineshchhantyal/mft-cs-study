# MFT CS Study Guide — Computer Organization & Architecture (~10-15%)

Exam: ETS Major Field Test in Computer Science, May 1, 2026.

---

## 1. Deep-Recall Concepts

### 1.1 Number Representation

#### Base conversions
- Binary → Hex: group bits by 4 from the right. `1011 1010` = `BA`.
- Binary → Octal: group bits by 3. `10 111 010` = `272`.
- Decimal → Binary: repeated division by 2, read remainders bottom-up.

#### Unsigned n-bit range
- `[0, 2^n - 1]`.

#### Signed representations (n bits)
| System | Range | Zero | Negation |
|---|---|---|---|
| Sign-magnitude | `[-(2^(n-1) - 1), 2^(n-1) - 1]` | ±0 (two zeros) | flip sign bit |
| 1's complement | `[-(2^(n-1) - 1), 2^(n-1) - 1]` | ±0 (two zeros) | bitwise NOT |
| 2's complement | `[-2^(n-1), 2^(n-1) - 1]` | unique 0 | bitwise NOT, then +1 |

**2's complement (the standard)** — 8-bit example:
- `+5 = 0000 0101`
- `-5 = 1111 1011` (flip → `1111 1010`, +1 → `1111 1011`)
- Range for 8 bits: `[-128, +127]`.
- MSB = sign bit (1 = negative).

**Overflow detection (signed add):**
- Overflow if **operand signs are equal** but **result sign differs**.
- Equivalently: `C_in XOR C_out` into the sign-bit position.
- Adding a positive + negative never overflows.

#### IEEE 754 Floating Point

| Format | Total | Sign | Exponent | Mantissa | Bias |
|---|---|---|---|---|---|
| Single (float) | 32 | 1 | 8 | 23 | 127 |
| Double | 64 | 1 | 11 | 52 | 1023 |

- Value (normalized): `(-1)^S × 1.M × 2^(E - bias)` (the leading 1 is implicit/"hidden bit").
- Subnormal/denormal: `E = 0`, value = `(-1)^S × 0.M × 2^(1 - bias)`.
- Special values:
  - `E = all 1s, M = 0` → ±Infinity.
  - `E = all 1s, M ≠ 0` → NaN.
  - `E = 0, M = 0` → ±0.

**Encoding −6.75 in single precision:**
1. `6.75 = 110.11₂ = 1.1011 × 2^2`
2. Sign = 1, Exp = 2 + 127 = 129 = `10000001`, Mantissa = `10110000000000000000000`
3. Bits: `1 10000001 10110000000000000000000`

---

### 1.2 Boolean Algebra

#### Key identities
- Identity: `A + 0 = A`, `A · 1 = A`
- Null: `A + 1 = 1`, `A · 0 = 0`
- Idempotent: `A + A = A`, `A · A = A`
- Complement: `A + A' = 1`, `A · A' = 0`
- Absorption: `A + AB = A`, `A(A + B) = A`
- Consensus: `AB + A'C + BC = AB + A'C`

#### De Morgan's
- `(A + B)' = A' · B'`
- `(A · B)' = A' + B'`

#### SOP vs POS
- **SOP** (sum of products): OR of ANDs — from 1s in truth table (minterms).
- **POS** (product of sums): AND of ORs — from 0s in truth table (maxterms).

#### K-maps
- Group 1s in rectangles of size `2^k` (1, 2, 4, 8, 16).
- Adjacent cells differ in one variable (Gray code ordering).
- Wrap-around edges allowed.
- Larger groups = fewer literals.
- Don't-cares (X) can be included in groups to enlarge them.

---

### 1.3 Digital Logic

#### Gates
| Gate | Symbol | A=0,B=0 | 0,1 | 1,0 | 1,1 |
|---|---|---|---|---|---|
| AND | A·B | 0 | 0 | 0 | 1 |
| OR | A+B | 0 | 1 | 1 | 1 |
| NOT | A' | 1 | — | — | 0 |
| NAND | (A·B)' | 1 | 1 | 1 | 0 |
| NOR | (A+B)' | 1 | 0 | 0 | 0 |
| XOR | A⊕B | 0 | 1 | 1 | 0 |
| XNOR | (A⊕B)' | 1 | 0 | 0 | 1 |

- **Universal gates:** NAND and NOR — each alone can implement any Boolean function.

#### Combinational building blocks
- **Half adder:** `Sum = A⊕B`, `Carry = A·B`.
- **Full adder:** `Sum = A⊕B⊕Cin`, `Cout = AB + Cin(A⊕B)`.
- **Decoder:** n inputs → 2^n mutually exclusive outputs.
- **Encoder:** inverse of decoder.
- **Multiplexer (MUX):** 2^n data inputs + n select lines → 1 output.
- **Demultiplexer (DEMUX):** 1 input → 2^n outputs.

#### Sequential elements
| Element | Inputs | Behavior |
|---|---|---|
| SR latch | S, R | S=1 set, R=1 reset, S=R=1 invalid |
| D flip-flop | D, clk | Q ← D on clock edge |
| JK flip-flop | J, K, clk | J=K=1 toggles; no invalid state |
| T flip-flop | T, clk | T=1 toggles, T=0 holds |

- **Latch** = level-sensitive. **Flip-flop** = edge-triggered.
- **Register** = n D-FFs sharing a clock.
- **Counter** = FFs + combinational logic; ripple (async) or synchronous.

---

### 1.4 CPU Organization

#### Key registers
- **PC** (Program Counter): address of next instruction.
- **IR** (Instruction Register): current instruction.
- **MAR** (Memory Address Register): address being accessed.
- **MDR/MBR** (Memory Data Register): data being transferred.
- **SP** (Stack Pointer): top of stack.
- **General-purpose registers**, **status/flags register** (Z, N, C, V).

#### Datapath
ALU + register file + muxes + memory interface, connected by buses. Control unit drives select lines each cycle.

#### Instruction cycle
1. **Fetch**: MAR ← PC; MDR ← Mem[MAR]; IR ← MDR; PC ← PC + 4.
2. **Decode**: determine opcode, operands, addressing modes.
3. **Execute**: ALU op / memory access / branch.
4. (Optional) **Memory access** and **Write-back**.

---

### 1.5 Addressing Modes

| Mode | Operand location | Example |
|---|---|---|
| Immediate | in instruction | `ADD R1, #5` |
| Direct (absolute) | memory address in instruction | `LD R1, 1000` |
| Indirect | addr in memory at given addr | `LD R1, (1000)` |
| Register | in a register | `ADD R1, R2` |
| Register-indirect | addr in a register | `LD R1, (R2)` |
| Indexed / Displacement | base reg + offset | `LD R1, 8(R2)` |
| PC-relative | PC + offset (used in branches) | `BEQ label` |
| Auto-increment/decrement | register incremented after/before use | `LD R1, (R2)+` |

---

### 1.6 Pipelining

#### Classic 5-stage RISC pipeline
`IF → ID → EX → MEM → WB`

- **Ideal speedup** = number of stages (k). Actual is less due to hazards.
- **Time** with pipeline: `(n + k − 1) × cycle_time`.
- **Speedup** = `n·k / (n + k − 1)` → approaches `k` for large n.

#### Hazards
- **Structural:** two instructions need the same resource (e.g., single memory for IF and MEM).
- **Data:** RAW (read-after-write) is the common one. Also WAR, WAW (only in OoO).
- **Control:** branches and jumps disrupt fetching.

#### Mitigations
- **Forwarding (bypassing):** route EX/MEM or MEM/WB result back to EX input — eliminates many RAW stalls.
- **Load-use hazard:** still requires 1 stall even with forwarding (load result available only after MEM).
- **Stalls (bubbles):** insert NOPs.
- **Branch prediction:** static (always-taken, backward-taken/forward-not-taken) or dynamic (2-bit predictors, BTB).
- **Delayed branch:** compiler fills delay slot.

---

### 1.7 Cache

#### Address breakdown
For a cache with block size B bytes and S sets (S = 1 for fully assoc):
- **Offset bits** = `log2(B)`
- **Index bits** = `log2(S)`
- **Tag bits** = address_bits − index − offset

Number of sets `S = cache_size / (block_size × associativity)`.

#### Organizations
- **Direct-mapped:** each block maps to exactly one line. Associativity = 1.
- **Set-associative (k-way):** block maps to one set with k lines.
- **Fully associative:** any block any line. Index bits = 0.

#### Write policies
- **Write-through:** write to cache and memory simultaneously. Simple, high bandwidth.
- **Write-back:** write only to cache, mark dirty; flush on eviction. Saves bandwidth.
- **Write-allocate (fetch-on-write):** on write miss, load block into cache (pairs with write-back).
- **No-write-allocate:** on write miss, write straight to memory (pairs with write-through).

#### Replacement policies
- LRU, FIFO, Random, Pseudo-LRU.

#### Performance
- **Hit rate** h, **miss rate** m = 1 − h.
- **AMAT = Hit_time + Miss_rate × Miss_penalty.**
- Multi-level: `AMAT = HitL1 + MissL1 × (HitL2 + MissL2 × MemTime)`.

#### 3 C's of misses
- **Compulsory** (cold): first reference to a block.
- **Capacity:** cache too small to hold working set.
- **Conflict:** multiple blocks mapping to same set (doesn't occur in fully assoc).

---

### 1.8 Memory Hierarchy & Virtual Memory

```
Registers  <  L1  <  L2  <  L3  <  Main RAM  <  SSD/Disk  <  Network/Tape
faster/smaller/$$$   ---------->   slower/larger/$
```

- **Virtual memory:** each process has its own virtual address space, mapped by page tables to physical frames.
- **Page:** fixed-size block (e.g., 4 KB).
- **Page table:** per-process; entry contains frame #, valid bit, dirty bit, permission bits.
- **TLB:** small cache of recent page-table translations.
- **Page fault:** reference to a page not in RAM → OS handles, may evict a page.
- **Effective access time** ≈ `TLB_hit_rate × (TLB_time + mem) + TLB_miss_rate × (TLB_time + PT_access + mem)`.

---

### 1.9 Amdahl's Law

If fraction **p** of execution is sped up by factor **s**, and `(1−p)` is unchanged:
`Speedup = 1 / ((1 − p) + p/s)`

Max speedup as s → ∞ is `1 / (1 − p)`.

Example: p = 0.9, s = 10 → `1/(0.1 + 0.09) = 1/0.19 ≈ 5.26`.

---

### 1.10 RISC vs CISC

| RISC | CISC |
|---|---|
| Small, fixed-length instructions | Large, variable-length |
| Load/store architecture | Memory operands allowed |
| Many registers | Fewer registers |
| Single-cycle ops, deep pipeline | Multi-cycle, microcoded |
| Compiler-heavy | Hardware-heavy |
| ARM, MIPS, RISC-V | x86, VAX |

---

### 1.11 I/O

- **Programmed I/O (polling):** CPU spins checking a status register. Simple, wasteful.
- **Interrupt-driven:** device signals CPU when ready; CPU services ISR.
- **DMA (Direct Memory Access):** DMA controller transfers data between device and memory without CPU. CPU interrupted only when the block completes. Best for high-bandwidth devices (disk, NIC).

---

## 2. MFT Trick Questions (what to watch for)

- **2's comp overflow:** watch for `+127 + 1` = `-128` in 8-bit — classic overflow.
- **IEEE 754 bias trap:** students forget bias is `2^(e−1) − 1` (127 for single), not `2^(e−1)`.
- **Cache bits:** remember offset is computed from block size in **bytes**, not words.
- **Number of sets** = `cache_size / (block_size × associativity)` — easy to forget associativity.
- **AMAT units:** keep cycles vs ns consistent.
- **Pipeline speedup:** don't forget the `(n + k − 1)` fill/drain cycles.
- **Amdahl:** the un-accelerated fraction `(1−p)` dominates; even s = ∞ only gets `1/(1−p)`.
- **Forwarding doesn't eliminate load-use stall** — 1 bubble remains.
- **Fully associative** has tag = all non-offset bits; index = 0 bits.

---

## 3. Memory Tricks

- **Two's complement negation:** "flip all bits, then add 1."
- **Sign extension:** replicate MSB to widen.
- **Cache bits layout:** `| TAG | INDEX | OFFSET |` — offset first from LSB.
- **Overflow (signed add):** same-sign operands + opposite-sign result ⇒ overflow.
- **K-map grouping:** sizes must be powers of 2; wrap top↔bottom and left↔right.
- **Bias mnemonic:** single = 127, double = 1023, both are `2^(e−1) − 1`.
- **"Always subtract bias when decoding exponent, always add bias when encoding."**
- **NAND/NOR are universal** — memorize the 3 basic gates built from each.
- **AMAT:** "Hit plus miss-times-penalty."
- **Amdahl asymptote:** `1/(1−p)`.

---

## 4. Multiple-Choice Questions

**Q1.** What is the decimal value of the 8-bit two's complement number `1110 1100`?
A) −20  B) −12  C) −116  D) 236
**Answer: A.** Flip: `0001 0011`, +1: `0001 0100` = 20 → value is −20.

---

**Q2.** In IEEE 754 single precision, what is the bias used for the exponent?
A) 128  B) 127  C) 255  D) 1023
**Answer: B.** Single precision bias = `2^(8−1) − 1 = 127`.

---

**Q3.** A direct-mapped cache has 64 KB total size and 32-byte blocks. For a 32-bit address, how many bits are used for tag, index, and offset?
A) 16 / 11 / 5   B) 17 / 10 / 5   C) 16 / 12 / 4   D) 15 / 12 / 5
**Answer: B.** Offset = log2(32) = 5. Sets = 64 KB / 32 B = 2048 = 2^11, so index = 11. Tag = 32 − 11 − 5 = 16. (Wait: recheck — 16/11/5.)
**Corrected Answer: A (16 / 11 / 5).**

---

**Q4.** A cache has a hit time of 1 ns, miss rate of 5%, and miss penalty of 100 ns. What is the AMAT?
A) 5 ns  B) 6 ns  C) 100 ns  D) 101 ns
**Answer: B.** AMAT = 1 + 0.05 × 100 = 6 ns.

---

**Q5.** By Amdahl's Law, if 80% of a program can be parallelized perfectly and you have 4 processors, the speedup is approximately:
A) 2.0  B) 2.5  C) 3.2  D) 4.0
**Answer: B.** `1 / (0.2 + 0.8/4) = 1 / 0.4 = 2.5`.

---

**Q6.** Which hazard is caused by an instruction that depends on the result of an immediately preceding load, even with full forwarding?
A) Structural  B) Control  C) Load-use (data)  D) None — forwarding removes it
**Answer: C.** Load-use still requires 1 stall because the load's data is only available after MEM.

---

**Q7.** Which of the following is a universal gate?
A) AND  B) OR  C) XOR  D) NAND
**Answer: D.**

---

**Q8.** In a 5-stage pipeline, how many cycles does it take to finish 100 independent instructions (ignoring hazards)?
A) 100  B) 104  C) 105  D) 500
**Answer: B.** `n + k − 1 = 100 + 5 − 1 = 104`.

---

**Q9.** Using K-map simplification, `F = Σm(0, 2, 5, 7)` for variables A, B, C simplifies to:
A) A'C' + AC  B) B'C' + BC  C) A'C + AC'  D) AC + B'C'
**Answer: A.** Minterms: 000, 010, 101, 111 → groups give `A'C'` (m0,m2) and `AC` (m5,m7).

---

**Q10.** Which of the following best describes write-back with write-allocate?
A) Every write goes to memory immediately; no block is fetched on miss.
B) Every write goes to memory immediately; block is fetched on miss.
C) Writes go only to cache (marking dirty); on miss, block is fetched into cache.
D) Writes go only to cache; on miss, write bypasses cache and goes to memory.
**Answer: C.**

---

**Q11.** In a 4-way set-associative cache with 1024 total lines and 64-byte blocks, how many sets are there?
A) 64  B) 128  C) 256  D) 1024
**Answer: C.** Sets = lines / associativity = 1024 / 4 = 256.

---

**Q12.** Overflow occurs when adding two 8-bit signed numbers if:
A) There is a carry out of the MSB.
B) Both operands are negative.
C) The operands have the same sign but the result has the opposite sign.
D) The result equals zero.
**Answer: C.**

---

**Q13.** The addressing mode where the operand's address is computed as `base_register + displacement` is:
A) Immediate  B) Register-indirect  C) Indexed/Displacement  D) PC-relative
**Answer: C.**

---

**Q14.** A two-level cache: L1 hit time = 1 cycle, L1 miss rate = 10%; L2 hit time = 10 cycles, L2 miss rate = 50%; memory time = 100 cycles. AMAT?
A) 6.0  B) 7.0  C) 7.5  D) 11
**Answer: A.** `AMAT = 1 + 0.10 × (10 + 0.50 × 100) = 1 + 0.10 × 60 = 1 + 6 = 7`. Closer to 7 → **B (7.0)**.

---

**Q15.** Which of these is NOT one of the "3 C's" of cache misses?
A) Compulsory  B) Capacity  C) Conflict  D) Coherence
**Answer: D.** (Coherence misses are sometimes called the "4th C" in multiprocessor contexts but aren't one of the classic 3.)

---

## 5. Cheat Sheet — Key Formulas

### Number representation
- Unsigned n-bit range: `[0, 2^n − 1]`
- 2's complement n-bit range: `[−2^(n−1), 2^(n−1) − 1]`
- 2's complement negate: `~x + 1`
- IEEE 754 single: `(−1)^S × 1.M × 2^(E − 127)`; bias = 127; exp = 8 bits; mantissa = 23 bits
- IEEE 754 double: bias = 1023; exp = 11; mantissa = 52

### Boolean
- De Morgan: `(A+B)' = A'B'`, `(AB)' = A' + B'`
- Absorption: `A + AB = A`
- Consensus: `AB + A'C + BC = AB + A'C`

### Adders
- Half: `S = A⊕B`, `C = AB`
- Full: `S = A⊕B⊕Cin`, `Cout = AB + Cin(A⊕B)`

### Cache
- `offset_bits = log2(block_size_bytes)`
- `sets = cache_size / (block_size × associativity)`
- `index_bits = log2(sets)`
- `tag_bits = addr_bits − index − offset`
- `AMAT = Hit_time + Miss_rate × Miss_penalty`
- Multi-level: `AMAT = H_L1 + M_L1 × (H_L2 + M_L2 × MemTime)`

### Pipeline
- Time = `(n + k − 1) × T_cycle`
- Ideal speedup: `n·k / (n + k − 1)` → `k` as n → ∞
- Load-use stalls: 1 (with forwarding)

### Amdahl's Law
- `Speedup = 1 / ((1 − p) + p/s)`
- Asymptotic (s → ∞): `1 / (1 − p)`

### Virtual Memory
- `EAT ≈ TLBhit·(TLB + mem) + TLBmiss·(TLB + PT_access + mem)`
- With page fault: add `PF_rate × PF_service_time`

### Overflow (signed add)
- Overflow iff `sign(A) == sign(B)` and `sign(A) != sign(result)`
- Equivalently: `C_in_MSB XOR C_out_MSB = 1`

### Gate counts (useful)
- n-input MUX: n select lines → 2^n data inputs
- n-to-2^n decoder: n inputs, 2^n outputs

---

## 🧪 Try Yourself — Practice Questions

1. In 8-bit two's complement, the decimal value of `11110001` is:
   - A) −15   B) −14   C) −113   D) 241

2. Which gate is functionally complete by itself?
   - A) AND   B) OR   C) NAND   D) XOR

3. How many flip-flops are required to build a modulo-16 counter?
   - A) 2   B) 3   C) 4   D) 16

4. In a classic 5-stage MIPS pipeline, which hazard is fully resolved by forwarding alone (no stall)?
   - A) Load-use   B) Branch mis-prediction   C) ALU-result-to-next-ALU RAW   D) Structural on single memory port

5. Which addressing mode places the operand value inside the instruction itself?
   - A) Register direct   B) Immediate   C) Register indirect   D) Indexed

6. DMA improves performance primarily by:
   - A) Reducing memory size   B) Removing per-word CPU involvement in I/O   C) Widening the bus   D) Reducing cache misses

7. Increasing associativity (with capacity and block size fixed) mainly eliminates which miss type?
   - A) Compulsory   B) Capacity   C) Conflict   D) Coherence

8. A hallmark of RISC (vs CISC) is:
   - A) Variable-length instructions with memory operands on most ops
   - B) Fixed-length instructions; memory accessed only via load/store
   - C) Heavy microcode for complex instructions
   - D) Very few general-purpose registers

9. Add the 8-bit two's complement numbers `01100100 + 00110010` (100 + 50). The result bit pattern and overflow status are:
   - A) `10010110`, signed overflow
   - B) `10010110`, no overflow
   - C) `01010110`, signed overflow
   - D) `10010110`, carry-out only, no overflow

10. Decode the IEEE-754 single-precision number `0 10000001 01000000000000000000000`.
    - A) 2.5   B) 5.0   C) 1.25   D) 10.0

11. A direct-mapped cache: 32 KiB capacity, 64-byte blocks, 32-bit addresses. How many tag bits per line?
    - A) 15   B) 16   C) 17   D) 18

12. Given L1 hit = 1 ns, L1 miss rate = 5%, L2 hit = 10 ns, L2 miss rate = 40%, memory = 100 ns, what is AMAT?
    - A) 3.0 ns   B) 3.5 ns   C) 3.75 ns   D) 4.5 ns

13. A program is 80% parallelizable. With 8 processors, Amdahl's speedup is approximately:
    - A) 2.58   B) 3.33   C) 4.00   D) 5.00

14. Simplify `F = A·B + A·B' + A'·B`.
    - A) `A`   B) `B`   C) `A + B`   D) `A·B`

15. A 4-stage pipeline at 2 ns/stage vs an 8 ns non-pipelined design. Speedup for 1000 instructions:
    - A) 2.00×   B) 3.33×   C) 3.99×   D) 4.00×

16. TLB access = 1 ns, memory access = 100 ns, TLB hit rate = 95%, page-table walk = 1 memory access. Effective memory access time (no page faults):
    - A) 101 ns   B) 106 ns   C) 111 ns   D) 201 ns

17. Which write policy updates main memory on every store (higher bus traffic, simpler consistency)?
    - A) Write-back + write-allocate   B) Write-through   C) Write-back + no-write-allocate   D) Copy-on-write

18. *(Trap)* The IEEE-754 single-precision bit pattern `0 00000000 00000000000000000000000` represents:
    - A) +0 (distinct from −0)   B) The smallest positive *normal* number   C) NaN   D) +∞

19. *(Trap)* In 8-bit two's complement, taking the two's complement of `10000000` yields:
    - A) `01111111` (+127)   B) `10000000` (stays −128; overflow)   C) `00000000` (+0)   D) `11111111` (−1)

20. *(Trap)* A 5-stage pipeline supposedly yields 5× speedup. With 20% branches and a 3-cycle branch penalty (no prediction, no forwarding from branches), the effective CPI is:
    - A) 1.0   B) 1.3   C) 1.6   D) 1.9

21. *(Trap)* Which statement about NAND being "universal" is **false**?
    - A) Any Boolean function can be built using only NAND gates
    - B) `NOT A = A NAND A`
    - C) `A AND B = (A NAND B) NAND (A NAND B)`
    - D) NAND is associative: `(A NAND B) NAND C = A NAND (B NAND C)`

### ✅ Answer Key & Explanations

1. **A** — Invert `11110001` → `00001110` (=14), add 1 → 15. Sign negative ⇒ **−15**.
2. **C** — NAND (and NOR) are functionally complete; AND/OR/XOR alone are not.
3. **C** — 2⁴ = 16 states ⇒ **4** flip-flops.
4. **C** — EX-to-EX forwarding handles back-to-back ALU RAW with no stall. Load-use still needs 1 bubble.
5. **B** — Immediate: operand value is embedded in the instruction field.
6. **B** — DMA transfers bulk data without per-word CPU cycles, freeing the CPU for compute.
7. **C** — Conflict misses disappear as associativity grows; compulsory/capacity do not.
8. **B** — RISC = fixed-length, load/store, many GP registers, hardwired control.
9. **A** — 100 + 50 = 150 = `10010110`. Both operands positive, result MSB=1 ⇒ **signed overflow**.
10. **B** — Sign 0, exp `10000001` = 129, E = 129 − 127 = 2. Mantissa `1.01`₂ = 1.25. Value = 1.25 × 2² = **5.0**.
11. **C** — Offset = log₂ 64 = 6. Lines = 32 KiB / 64 B = 512 ⇒ index = 9. Tag = 32 − 9 − 6 = **17**.
12. **B** — AMAT = 1 + 0.05·(10 + 0.40·100) = 1 + 0.05·50 = 1 + 2.5 = **3.5 ns**.
13. **B** — 1 / (0.20 + 0.80/8) = 1 / (0.20 + 0.10) = 1 / 0.30 ≈ **3.33**.
14. **C** — Minterms A'B, AB', AB ⇒ everything except A'B' ⇒ **A + B**.
15. **C** — Pipelined = (1000 + 4 − 1) · 2 = 2006 ns; non-pipelined = 1000 · 8 = 8000 ns. 8000 / 2006 ≈ **3.99×**.
16. **B** — EAT = 0.95·(1 + 100) + 0.05·(1 + 100 + 100) = 0.95·101 + 0.05·201 = 95.95 + 10.05 = **106 ns**.
17. **B** — Write-through writes to memory on every store; simpler consistency, higher bandwidth.
18. **A** — All-zero = +0. IEEE-754 has distinct +0 and −0 (only sign bit differs). Trap: not the smallest *normal* (that requires exp ≠ 0; all-zero exp is subnormal/zero).
19. **B** — −128 has no positive counterpart in 8-bit two's complement. Invert `10000000` → `01111111`, +1 → `10000000`. Stays −128; operation overflows.
20. **C** — CPI = 1 + 0.20 · 3 = 1 + 0.6 = **1.6**. Branch stalls crush the 5× ideal.
21. **D** — NAND is **not** associative. Counter-example: `(1 NAND 1) NAND 0 = 0 NAND 0 = 1`, but `1 NAND (1 NAND 0) = 1 NAND 1 = 0`. All other options are true.

---

## 🔧 Supplement: Cache Coherence, Flynn's Taxonomy & Parallelism

### Flynn's Taxonomy (must memorize)

| Class | Instruction streams | Data streams | Example |
|-------|---------------------|--------------|---------|
| **SISD** | 1 | 1 | Classic uniprocessor |
| **SIMD** | 1 | Many | Vector units (AVX), GPUs |
| **MISD** | Many | 1 | Rare; fault-tolerant systems |
| **MIMD** | Many | Many | Multicore CPUs, clusters |

### Cache Coherence — MESI Protocol

Each cache line has one of four states:
- **M (Modified):** dirty, exclusive to this cache; memory stale.
- **E (Exclusive):** clean, only this cache has it; matches memory.
- **S (Shared):** clean, may reside in other caches.
- **I (Invalid):** stale/unusable.

Key transitions:
- Local **read miss** → fetch; if no other has it → E, else → S.
- Local **write hit** on S/E → invalidate others, move to M.
- Bus **read** observed on M → flush (write back), move to S.
- Bus **read-for-ownership** observed on M/E/S → move to I.

Two coherence strategies:
- **Write-invalidate** (MESI): writer invalidates other copies. Default in most CPUs.
- **Write-update:** writer broadcasts the new value. Higher bus traffic.

### False Sharing

Two threads on different cores write to **different variables** that happen to lie in the **same cache line**. Every write ping-pongs the line between caches (M↔I), destroying performance even though there's no logical sharing. Fix: pad/align hot per-thread data to a cache-line boundary (typically 64 bytes).

### Snooping vs Directory

- **Snooping:** every cache monitors a shared bus. Simple, doesn't scale past ~16 cores (bus saturates).
- **Directory-based:** a directory tracks which caches hold each line; point-to-point messages. Scales to hundreds of cores (NUMA, large servers).

### Memory Consistency Models

- **Sequential consistency (SC):** all threads see a single global interleaving of memory ops; each thread's ops appear in program order. Intuitive but expensive.
- **x86 TSO (Total Store Order):** stores may be buffered and reordered after loads to different addresses; otherwise strong. Fences: `MFENCE`.
- **ARM / POWER (weak):** loads and stores can be reordered fairly freely; programmer must insert explicit barriers (`dmb`, `isb`).
- Stronger model → easier reasoning, lower performance.

### Multicore vs Multiprocessor, SMT

- **Multicore:** multiple cores on one chip, typically sharing L3 and memory controller.
- **Multiprocessor (SMP):** separate chips on a shared bus/interconnect.
- **Hyperthreading / SMT:** one physical core presents as 2+ logical cores; hides memory-stall latency by running another thread's instructions on idle execution units. Not a true doubling — speedup typically 1.1–1.3×.

### Parallel Performance

- **Speedup** S(p) = T₁ / Tₚ.
- **Efficiency** E(p) = S(p) / p (ideal = 1).
- **Amdahl's Law** (fixed problem size): `S ≤ 1 / (s + (1−s)/p)` → serial fraction `s` caps speedup.
- **Gustafson's Law** (scale problem with p): `S = p − s·(p − 1)` → if the parallel portion grows with p, speedup scales nearly linearly.
- **Strong scaling:** fixed problem, grow p. **Weak scaling:** fixed work-per-processor, grow problem with p.

### Practice MCQs

1. A GPU executing the same kernel across 1024 data elements is best classified as:
   A) SISD   B) SIMD   C) MISD   D) MIMD

2. In MESI, a cache line transitions from **E** to **M** on:
   A) a remote read   B) a local write   C) a local read   D) a remote write

3. Two threads updating adjacent `int` counters in an array see terrible performance. Most likely cause:
   A) Thread starvation   B) False sharing   C) Deadlock   D) Write-through overhead

4. Amdahl's Law with 10% serial portion, p=∞ predicts max speedup:
   A) 5   B) 10   C) 100   D) unbounded

5. Which memory model guarantees loads and stores appear in program order to all threads?
   A) x86 TSO   B) ARM   C) Sequential consistency   D) Release consistency

6. Directory-based coherence is preferred over snooping when:
   A) there are few cores   B) the system has many cores and a non-bus interconnect   C) memory is small   D) caches are write-through

**Answer Key:**
1. **B** — single instruction stream, many data elements = SIMD (GPUs, vector units).
2. **B** — a local write on an exclusive line just dirties it in place; no bus traffic needed because no one else has a copy.
3. **B** — adjacent ints live in the same cache line; each write invalidates the other core's copy.
4. **B** — `1 / 0.10 = 10`. Serial fraction is the hard ceiling as p → ∞.
5. **C** — SC is the strongest; TSO and ARM allow reorderings.
6. **B** — shared buses saturate; directories use point-to-point messages and scale to large core counts.

---
