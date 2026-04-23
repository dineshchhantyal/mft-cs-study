# MFT CS: Software Engineering + Networking

**Exam date:** May 1, 2026 | **Weight:** ~5-10% combined (lighter)
**Strategy:** Don't over-invest. Lock in high-yield items: coupling/cohesion, cyclomatic complexity, SOLID, design patterns, OSI layers, TCP vs UDP, 3-way handshake, CIDR basics.

---

## Interactive Study Mode (Use This First)

Make this file active instead of passive reading.

### 20-Minute Sprint

- [ ] 5 min: Read one section fast (no notes)
- [ ] 8 min: Close notes, explain from memory out loud
- [ ] 5 min: Solve 5 MCQs from this file
- [ ] 2 min: Write 3 weak points to revise

### Score Game

- +2 points: Correct answer without peeking
- +1 point: Correct after one hint/review
- 0 points: Incorrect
- Target: 14+ points per sprint

### Active Recall Prompts

1. Explain LOW coupling and HIGH cohesion in one sentence each.
2. Say the OSI layers bottom-to-top without looking.
3. Compare TCP vs UDP in 20 seconds.
4. Solve hosts in /26 and /30 mentally.

---

## PART A — SOFTWARE ENGINEERING

### 1. SDLC Models

| Model | Key Idea | When Used |
|---|---|---|
| **Waterfall** | Sequential, each phase complete before next. No backtracking. | Stable, well-understood requirements |
| **Iterative** | Build in repeated cycles, refine each time | Requirements evolving |
| **Spiral** | Risk-driven iterative; each loop = planning, risk analysis, engineering, evaluation | Large, high-risk projects |
| **V-Model** | Waterfall + testing paired to each dev phase (verification & validation) | Safety-critical |
| **Agile — Scrum** | Sprints (2-4 wk), backlog, daily standups, roles: PO, SM, team | Changing requirements |
| **Agile — Kanban** | Continuous flow, WIP limits, no fixed iterations | Ops/support work |
| **Prototyping** | Throwaway or evolutionary prototype to clarify requirements | Unclear UI/UX |

**Trick:** V-model is NOT agile; it's waterfall-extended. Spiral is NOT linear.

### 2. Requirements

- **Functional** — what the system does (e.g., "user can log in")
- **Non-functional (NFR / quality attributes)** — how it does it: performance, security, usability, reliability, scalability, maintainability
- **Use case** — actor + system interaction sequence; part of UML

### 3. Coupling and Cohesion (HIGH-YIELD)

**Goal: LOW coupling, HIGH cohesion.**

**Coupling (worst → best):**
1. **Content** — module modifies another's internals (goto, shared memory) — WORST
2. **Common** — modules share global data
3. **External** — shared external format/device
4. **Control** — one module passes a flag controlling another's logic
5. **Stamp (data-structured)** — pass whole record/struct when only part is needed
6. **Data** — pass only needed parameters — BEST
7. **Message (OO)** — even looser, via message passing — BEST-ish

**Cohesion (worst → best):**
1. **Coincidental** — unrelated activities grouped — WORST
2. **Logical** — similar category (e.g., "all I/O") but chosen by flag
3. **Temporal** — executed at same time (e.g., initialization)
4. **Procedural** — steps follow a sequence but on unrelated data
5. **Communicational** — operate on same data
6. **Sequential** — output of one is input of next
7. **Functional** — all parts contribute to one task — BEST

**Trick mnemonics:** Coupling "CCECSD(M)" worst→best. Cohesion "CLTPCSF" worst→best.

### 4. SOLID Principles

- **S — Single Responsibility:** a class has one reason to change
- **O — Open/Closed:** open for extension, closed for modification
- **L — Liskov Substitution:** subtypes must be substitutable for base types without breaking
- **I — Interface Segregation:** many small client-specific interfaces > one fat interface
- **D — Dependency Inversion:** depend on abstractions, not concretions

### 5. Design Patterns (GoF)

**Creational:**
- **Singleton** — exactly one instance, global access
- **Factory Method** — subclasses decide what to instantiate
- **Abstract Factory** — families of related objects
- **Builder** — step-by-step construction of complex object
- **Prototype** — clone existing instances

**Structural:**
- **Adapter** — converts one interface to another (wrap incompatible class)
- **Decorator** — adds behavior dynamically without subclassing
- **Facade** — simplified interface over a subsystem
- **Proxy** — placeholder/surrogate controlling access (lazy, remote, protection)
- **Composite** — tree of uniform objects (leaf + container)
- **Bridge** — decouple abstraction from implementation
- **Flyweight** — share fine-grained objects for memory

**Behavioral:**
- **Observer** — publish/subscribe; notify on state change
- **Strategy** — interchangeable algorithms at runtime
- **Command** — encapsulate request as object (undo, queueing)
- **Iterator** — traverse a collection without exposing structure
- **State** — behavior changes with state (looks like class change)
- **Template Method** — skeleton in base class, steps in subclasses
- **Chain of Responsibility** — pass request along handlers
- **Mediator** — central object coordinates peers
- **Visitor** — add operations without modifying classes
- **Memento** — capture/restore state (undo)

**Common scenario cues:**
- "Add logging without modifying class" → **Decorator**
- "Notify multiple views" → **Observer**
- "Swap sort algorithm at runtime" → **Strategy**
- "One DB connection pool" → **Singleton**
- "Plug old API into new interface" → **Adapter**
- "Simplify complex subsystem" → **Facade**
- "Undo" → **Command** or **Memento**

### 6. Testing

- **Unit** — smallest testable piece (function/class), usually mocked deps
- **Integration** — modules together
- **System** — full end-to-end
- **Acceptance (UAT)** — customer validates
- **Regression** — ensure old features still work
- **Smoke** — quick sanity check
- **Black-box** — no knowledge of internals; tests spec/behavior
- **White-box (glass)** — knows code; targets paths/branches
- **Gray-box** — mix
- **Coverage hierarchy (weakest → strongest):** Statement ⊂ Branch (decision) ⊂ Path
- **TDD:** Red (failing test) → Green (minimal code) → Refactor
- **Mutation testing** — inject bugs; good tests should catch them
- **Boundary value analysis** — test at/just past edges of input ranges
- **Equivalence partitioning** — one rep from each equivalence class

**Alpha = internal/dev test site; Beta = external users.**

### 7. Metrics

- **LOC** — lines of code (crude)
- **Cyclomatic Complexity V(G)**:
  - **V(G) = E − N + 2P** (P = connected components, usually 1)
  - Or: V(G) = number of decision points + 1
  - Or: V(G) = number of independent regions in flow graph
  - Interpretation: ≤10 simple; 11-20 moderate; 21-50 complex; >50 untestable
- **Function Points** — size based on inputs, outputs, inquiries, files, interfaces
- **Halstead metrics** — operators & operands
- **COCOMO (basic):** Effort = a·(KLOC)^b
  - Organic: 2.4·KLOC^1.05
  - Semi-detached: 3.0·KLOC^1.12
  - Embedded: 3.6·KLOC^1.20

### 8. UML Class Diagram Relationships

| Relationship | Symbol | Meaning |
|---|---|---|
| **Association** | plain line | generic "uses/knows" |
| **Aggregation** | hollow diamond on whole side | whole-part, parts can exist independently ("has-a", weak) |
| **Composition** | filled diamond | strong whole-part; parts die with whole |
| **Inheritance/Generalization** | hollow triangle | "is-a" |
| **Realization** | dashed + hollow triangle | implements interface |
| **Dependency** | dashed arrow | uses temporarily |

**Trick:** Composition = filled diamond = strong (car & engine lifetime-bound). Aggregation = hollow = weak (team & players).

### 9. Version Control

- **Centralized (SVN)** vs **Distributed (Git, Mercurial)**
- Key ops: clone, branch, merge, rebase, pull, push, commit
- Merge vs rebase: rebase rewrites history, merge preserves it

### 10. Risk & Estimation

- Risk: identify → analyze (probability × impact) → mitigate → monitor
- Estimation: expert judgment, analogous, COCOMO, function points, PERT (optimistic + 4·likely + pessimistic)/6

---

## PART B — NETWORKING

### 1. OSI 7-Layer Model

| # | Layer | Role | Units | Examples |
|---|---|---|---|---|
| 7 | **Application** | user-facing protocols | data | HTTP, FTP, SMTP, DNS |
| 6 | **Presentation** | format, encrypt, compress | data | TLS, JPEG, ASCII |
| 5 | **Session** | establish/manage sessions | data | NetBIOS, RPC |
| 4 | **Transport** | end-to-end, reliability | segments | TCP, UDP |
| 3 | **Network** | routing, addressing | packets | IP, ICMP, routers |
| 2 | **Data Link** | frame, MAC, local delivery | frames | Ethernet, switches, ARP |
| 1 | **Physical** | bits on wire | bits | cables, hubs, NIC |

**Mnemonic (top→bottom):** "All People Seem To Need Data Processing"
**Bottom→top:** "Please Do Not Throw Sausage Pizza Away"

### 2. TCP/IP 4-Layer Model

| TCP/IP | ≈ OSI |
|---|---|
| Application | 5+6+7 |
| Transport | 4 |
| Internet | 3 |
| Network Access (Link) | 1+2 |

### 3. TCP vs UDP

| Feature | TCP | UDP |
|---|---|---|
| Connection | connection-oriented (handshake) | connectionless |
| Reliability | reliable (ack, retransmit) | best-effort |
| Ordering | in-order | no ordering |
| Flow/Congestion control | yes | no |
| Header | 20 bytes | 8 bytes |
| Use cases | HTTP, SSH, email, file transfer | DNS, VoIP, video, DHCP, gaming |

### 4. TCP 3-Way Handshake

1. Client → Server: **SYN** (seq=x)
2. Server → Client: **SYN-ACK** (seq=y, ack=x+1)
3. Client → Server: **ACK** (ack=y+1)

**Teardown (4-way):** FIN → ACK → FIN → ACK (each side closes independently).

### 5. Congestion Control

- **Slow start:** cwnd doubles each RTT until ssthresh
- **AIMD (Additive Increase, Multiplicative Decrease):** +1 per RTT in congestion avoidance; cwnd ÷ 2 on loss
- Variants: Tahoe, Reno, CUBIC

### 6. IP Addressing

**IPv4 classes (legacy):**
- A: 0.0.0.0 – 127.255.255.255 (/8)
- B: 128.0.0.0 – 191.255.255.255 (/16)
- C: 192.0.0.0 – 223.255.255.255 (/24)
- D: 224–239 multicast
- E: 240–255 reserved

**Private ranges (RFC 1918):**
- 10.0.0.0/8
- 172.16.0.0/12
- 192.168.0.0/16

**Loopback:** 127.0.0.0/8 (usually 127.0.0.1)

**CIDR:** /n means n prefix bits. Hosts per subnet = 2^(32−n) − 2 (subtract network + broadcast).
- /24 → 256 addrs, 254 usable
- /16 → 65,536 addrs
- /30 → 4 addrs, 2 usable (point-to-point)

**IPv6:** 128 bits, 8 groups of 4 hex, `::` collapses zeros. Loopback = `::1`.

### 7. Protocol → Layer Cheat

| Protocol | Layer | Port |
|---|---|---|
| HTTP | App (7) | 80 |
| HTTPS | App (7) / TLS at 6 | 443 |
| FTP | App (7) | 20/21 |
| SMTP | App (7) | 25 |
| DNS | App (7) | 53 (UDP mostly) |
| DHCP | App (7) | 67/68 (UDP) |
| SSH | App (7) | 22 |
| TCP/UDP | Transport (4) | — |
| IP, ICMP | Network (3) | — |
| ARP | Data Link (2) — debated, between 2 and 3 | — |
| Ethernet | Data Link (2) | — |

### 8. Routing

- **Distance vector (RIP):** share full table with neighbors; Bellman-Ford; slow convergence; count-to-infinity
- **Link state (OSPF):** share link info with all; each builds full map; Dijkstra; faster convergence
- **Path vector (BGP):** inter-domain

### 9. Devices

| Device | Layer | Forwards on |
|---|---|---|
| **Hub** | 1 | bits to all ports (obsolete) |
| **Switch** | 2 | frames via MAC table |
| **Router** | 3 | packets via IP routing table |
| **Gateway** | varies | protocol translator |
| **Firewall** | 3–7 | filters |

### 10. DNS / DHCP / ARP

- **DNS:** name → IP; recursive + iterative queries; root, TLD, authoritative servers; record types A, AAAA, CNAME, MX, NS, TXT
- **DHCP:** assigns IP (DORA: Discover, Offer, Request, Ack)
- **ARP:** IP → MAC on local LAN

---

## PART C — 12 PRACTICE MCQs (INTERACTIVE)

Try to answer first. Open each answer only after you commit.

**Q1.** A module passes a flag to another module that changes its control flow. This is:
A) Data coupling  B) Stamp coupling  C) Control coupling  D) Common coupling
<details>
<summary>Reveal answer</summary>

**C.** Flag controlling behavior = control coupling.
</details>

**Q2.** Which cohesion is BEST?
A) Logical  B) Temporal  C) Sequential  D) Functional
<details>
<summary>Reveal answer</summary>

**D.** Functional = all parts contribute to one task.
</details>

**Q3.** A function has 4 `if` statements (no else), and 1 `while` loop. Cyclomatic complexity?
A) 4  B) 5  C) 6  D) 7
<details>
<summary>Reveal answer</summary>

**C.** V(G) = decision points + 1 = 5 + 1 = 6.
</details>

**Q4.** "Add scrolling to a window at runtime without subclassing" - which pattern?
A) Adapter  B) Decorator  C) Proxy  D) Strategy
<details>
<summary>Reveal answer</summary>

**B.** Decorator adds behavior dynamically.
</details>

**Q5.** Liskov Substitution Principle means:
A) Classes should have one responsibility
B) Subtypes must be usable wherever base type is expected
C) Depend on abstractions
D) Many small interfaces
<details>
<summary>Reveal answer</summary>

**B.** Subtypes must work anywhere the base type is expected.
</details>

**Q6.** Which layer does a router operate at?
A) 1  B) 2  C) 3  D) 4
<details>
<summary>Reveal answer</summary>

**C.** Network layer (IP routing).
</details>

**Q7.** TCP 3-way handshake order:
A) SYN, ACK, SYN-ACK  B) SYN, SYN-ACK, ACK  C) ACK, SYN, SYN-ACK  D) SYN-ACK, SYN, ACK
<details>
<summary>Reveal answer</summary>

**B.** SYN -> SYN-ACK -> ACK.
</details>

**Q8.** How many usable hosts in a /26 subnet?
A) 62  B) 64  C) 30  D) 126
<details>
<summary>Reveal answer</summary>

**A.** $2^{(32-26)} - 2 = 64 - 2 = 62$.
</details>

**Q9.** Which protocol is connectionless and has 8-byte header?
A) TCP  B) UDP  C) IP  D) ICMP
<details>
<summary>Reveal answer</summary>

**B.** UDP.
</details>

**Q10.** DNS primarily uses which transport protocol and port?
A) TCP 53  B) UDP 53  C) TCP 80  D) UDP 80
<details>
<summary>Reveal answer</summary>

**B.** UDP 53 (TCP 53 for zone transfers / large responses).
</details>

**Q11.** White-box testing that ensures every edge of the control flow graph is executed:
A) Statement coverage  B) Branch coverage  C) Path coverage  D) Loop coverage
<details>
<summary>Reveal answer</summary>

**B.** Branch (decision) coverage.
</details>

**Q12.** In UML, a filled diamond from Car to Engine means:
A) Aggregation  B) Composition  C) Association  D) Dependency
<details>
<summary>Reveal answer</summary>

**B.** Composition - Engine lifetime bound to Car.
</details>

---

## QUICK CHEAT SHEET

**Software Engineering:**
- LOW coupling, HIGH cohesion. Worst coupling = content; best = data/message. Worst cohesion = coincidental; best = functional.
- SOLID: Single resp, Open/closed, Liskov, Interface seg, Dependency inversion.
- V(G) = E − N + 2 = decisions + 1.
- Decorator = add behavior. Observer = pub/sub. Strategy = swap algo. Singleton = one instance. Adapter = interface convert. Facade = simplify.
- Coverage: Statement ⊂ Branch ⊂ Path.
- BVA = boundary tests; EP = equivalence classes.
- UML: filled diamond = composition (strong); hollow = aggregation (weak).

**Networking:**
- OSI (L1→L7): Physical, Data Link, Network, Transport, Session, Presentation, Application — "Please Do Not Throw Sausage Pizza Away".
- Hub=L1, Switch=L2, Router=L3.
- TCP = reliable, connection, 20B header. UDP = unreliable, connectionless, 8B header.
- Handshake: SYN → SYN-ACK → ACK. Teardown: 4-way FIN/ACK.
- Private: 10/8, 172.16/12, 192.168/16. Loopback 127.0.0.1.
- Hosts in /n = 2^(32−n) − 2.
- DNS=53, HTTP=80, HTTPS=443, SSH=22, SMTP=25, FTP=20/21, DHCP=67/68.
- DHCP = DORA. ARP: IP→MAC. Distance vector (RIP, Bellman-Ford) vs Link state (OSPF, Dijkstra).
- Congestion: slow start (exponential) → AIMD (linear up, halve on loss).

## 🧪 Try Yourself — Practice Questions

1. Which SDLC model is best suited when requirements are unclear and user feedback is needed early and often?
   - A) Waterfall  B) V-Model  C) Spiral  D) Big Bang

2. "The system shall encrypt all passwords using bcrypt" is an example of:
   - A) Functional requirement  B) Non-functional requirement  C) Business requirement  D) User requirement

3. Which coupling is the WORST (tightest)?
   - A) Data coupling  B) Stamp coupling  C) Control coupling  D) Content coupling

4. Which cohesion is the BEST (highest)?
   - A) Logical  B) Temporal  C) Functional  D) Procedural

5. A class that handles both database persistence and email notifications violates which SOLID principle?
   - A) OCP  B) SRP  C) LSP  D) DIP

6. A payment system allows plugging in Stripe, PayPal, or Razorpay without modifying existing code. Which pattern?
   - A) Singleton  B) Strategy  C) Observer  D) Factory

7. A logger class must have exactly one instance across the app. Which pattern?
   - A) Prototype  B) Builder  C) Singleton  D) Adapter

8. When a subject's state changes, all dependents are notified automatically. Which pattern?
   - A) Mediator  B) Observer  C) Visitor  D) Command

9. TDD cycle is:
   - A) Code → Test → Refactor  B) Red → Green → Refactor  C) Design → Code → Test  D) Test → Debug → Deploy

10. For input range 1–100, Boundary Value Analysis test values are:
    - A) 1, 50, 100  B) 0, 1, 100, 101  C) 0, 1, 2, 99, 100, 101  D) -1, 0, 101, 102

11. A flowgraph has 10 edges, 8 nodes, and 1 connected component. Cyclomatic complexity =
    - A) 2  B) 3  C) 4  D) 5

12. Statement coverage = 100% guarantees:
    - A) All branches executed  B) All paths executed  C) Every line executed at least once  D) No bugs exist

13. A "Car HAS-A Engine" relationship where the Engine cannot exist without the Car is:
    - A) Aggregation  B) Composition  C) Association  D) Dependency

14. Encryption and decryption occur at which OSI layer?
    - A) Transport  B) Session  C) Presentation  D) Application

15. Which is TRUE about UDP?
    - A) Connection-oriented  B) Guarantees delivery  C) Has 3-way handshake  D) Lower overhead than TCP

16. The correct order of TCP 3-way handshake is:
    - A) SYN → ACK → SYN-ACK  B) SYN → SYN-ACK → ACK  C) ACK → SYN → SYN-ACK  D) SYN-ACK → SYN → ACK

17. IP address 192.168.5.10 belongs to which class and is it private?
    - A) Class B, public  B) Class C, private  C) Class A, private  D) Class C, public

18. A /26 subnet provides how many usable host addresses?
    - A) 64  B) 62  C) 32  D) 30

19. Which protocol operates at the Network layer?
    - A) TCP  B) HTTP  C) ICMP  D) FTP

20. A Layer-3 device that forwards packets between different networks using IP addresses is a:
    - A) Hub  B) Switch  C) Bridge  D) Router

### ✅ Answer Key & Explanations

<details>
<summary>Reveal full answer key</summary>

1. **C) Spiral** — Iterative with risk analysis; ideal for evolving requirements. Waterfall needs fixed specs upfront.

2. **B) Non-functional requirement** — Describes a quality/constraint (security), not a behavior the system performs for users.

3. **D) Content coupling** — One module directly modifies/accesses another's internals. Hierarchy (worst→best): Content > Common > External > Control > Stamp > Data > Message.

4. **C) Functional cohesion** — All elements contribute to a single well-defined task. Hierarchy (best→worst): Functional > Sequential > Communicational > Procedural > Temporal > Logical > Coincidental.

5. **B) SRP** — Single Responsibility Principle: a class should have only one reason to change. Persistence and notifications are two responsibilities.

6. **B) Strategy** — Encapsulates interchangeable algorithms/behaviors selected at runtime without altering clients. (Factory creates objects; Strategy chooses behavior.)

7. **C) Singleton** — Ensures a single instance with global access.

8. **B) Observer** — One-to-many dependency; subject notifies observers on state change.

9. **B) Red → Green → Refactor** — Write failing test (red), make it pass (green), then clean up (refactor).

10. **C) 0, 1, 2, 99, 100, 101** — BVA tests just-below, boundary, and just-above on both ends (min−1, min, min+1, max−1, max, max+1).

11. **C) 4** — V(G) = E − N + 2P = 10 − 8 + 2(1) = 4.

12. **C) Every line executed at least once** — Statement coverage does NOT guarantee branch/path coverage or bug-free code (trap).

13. **B) Composition** — Strong "part-of" with lifecycle dependency (part dies with whole). Aggregation is weaker — part can exist independently.

14. **C) Presentation** — Layer 6 handles encryption, compression, and translation/formatting. (Common trap: people guess Session or Application.)

15. **D) Lower overhead than TCP** — UDP is connectionless, no handshake, no delivery guarantee; hence smaller header and less overhead.

16. **B) SYN → SYN-ACK → ACK** — Client sends SYN, server replies SYN-ACK, client confirms with ACK.

17. **B) Class C, private** — 192.x is Class C (192–223). Private ranges: 10.0.0.0/8, 172.16–31.0.0/12, 192.168.0.0/16.

18. **B) 62** — /26 → 6 host bits → 2^6 − 2 = 62 usable (subtract network + broadcast). Trap: 64 forgets the −2.

19. **C) ICMP** — ICMP (ping, traceroute) runs at Layer 3 (Network). TCP=L4, HTTP/FTP=L7.

20. **D) Router** — Layer 3 device using IP for inter-network forwarding. Switch=L2 (MAC), Hub=L1, Bridge=L2.

</details>

---

## 🔧 Supplement: Information Theory & Practical Regex

### Information Theory

**Entropy** of a discrete random variable X with outcomes {x₁,…,xₙ} and probabilities p(xᵢ):

```
H(X) = − Σᵢ p(xᵢ) · log₂ p(xᵢ)     (bits)
```

Interpretation: the **minimum average number of bits** required to encode one outcome of X, in the limit of long sequences.

**Key facts:**
- H(X) ≥ 0, with equality iff X is deterministic (one outcome has probability 1).
- H(X) is **maximized** when X is uniform: H_max = log₂ n for n equally likely outcomes.
- **Fair coin:** H = −(0.5·log₂ 0.5 + 0.5·log₂ 0.5) = 1 bit.
- **Biased coin** (p=0.9, 0.1): H ≈ 0.469 bits — less than 1, so you *can* compress a stream of biased flips below 1 bit/symbol on average.
- **Fair 8-sided die:** H = log₂ 8 = 3 bits.

**Shannon's source coding theorem (informal):** for any lossless code, the expected code length L satisfies L ≥ H(X). Huffman and arithmetic coding approach this bound.

**Huffman coding:** greedy bottom-up algorithm producing an optimal **prefix code** (no codeword is a prefix of another). Build a min-heap of symbols by frequency; repeatedly merge the two lightest. Produces codewords of length ⌈−log₂ p⌉ roughly, achieving L within 1 bit of H.

**Lossless** (ZIP, PNG, FLAC) vs **lossy** (JPEG, MP3, MPEG) — lossy trades fidelity for much higher compression and is not reversible.

**Channel capacity** (Shannon–Hartley, concept): C = B · log₂(1 + S/N) bits/sec — maximum error-free rate over a noisy channel of bandwidth B and signal-to-noise ratio S/N.

### Practical Regex (complements theoretical regex in file 08)

**Character classes:**
| Pattern | Matches |
|---------|---------|
| `[abc]` | a, b, or c |
| `[^abc]` | anything except a, b, c |
| `[a-z]` | any lowercase letter |
| `\d` / `\D` | digit / non-digit |
| `\w` / `\W` | word char `[A-Za-z0-9_]` / non-word |
| `\s` / `\S` | whitespace / non-whitespace |
| `.` | any char except newline (usually) |

**Quantifiers:**
| Pattern | Matches |
|---------|---------|
| `*` | 0 or more (greedy) |
| `+` | 1 or more |
| `?` | 0 or 1 |
| `{n}` / `{n,}` / `{n,m}` | exactly n / at least n / between n and m |
| `*?`, `+?`, `??` | lazy (non-greedy) variants |

**Greedy vs lazy:** on input `<a><b>`, `<.*>` matches the whole string; `<.*?>` matches just `<a>`.

**Anchors:** `^` start of line, `$` end of line, `\b` word boundary, `\B` non-boundary.

**Groups:**
- `(abc)` — capturing group, referenceable as `\1` (backref) or `$1` (replacement).
- `(?:abc)` — non-capturing (no group number allocated).
- `(?P<name>abc)` — named group (Python/PCRE).

**Lookarounds** (zero-width; don't consume input):
- `(?=X)` positive lookahead: "followed by X"
- `(?!X)` negative lookahead
- `(?<=X)` positive lookbehind (fixed width in many engines)
- `(?<!X)` negative lookbehind

**Common patterns (exam-ready):**
- **Email (simplified):** `^[\w.+-]+@[\w-]+\.[\w.-]+$`
- **IPv4 octet:** `(25[0-5]|2[0-4]\d|1\d\d|[1-9]?\d)`
- **URL (rough):** `^https?://[\w.-]+(:\d+)?(/[^\s]*)?$`
- **US phone:** `^\(?\d{3}\)?[-.\s]?\d{3}[-.\s]?\d{4}$`

**Engine types:**
- **NFA-based** (Perl, Python, Java, JavaScript): backtracking engines. Support backreferences, lookarounds. Worst-case exponential time on pathological patterns ("catastrophic backtracking").
- **DFA-based** (grep, RE2, lex): linear-time guaranteed. Do **not** support backreferences or most lookarounds. Use when input is untrusted or large.

### Practice MCQs

1. Entropy of a fair 4-sided die is:
   A) 1 bit   B) 2 bits   C) 4 bits   D) log₂ 3 bits

2. Huffman coding produces an optimal:
   A) block code   B) fixed-length code   C) prefix code   D) arithmetic code

3. On input `<b>hi</b>`, the regex `<.*>` (greedy) matches:
   A) `<b>`   B) `</b>`   C) `<b>hi</b>`   D) nothing

4. Which regex feature is typically **unsupported** in pure DFA engines like RE2?
   A) character classes   B) alternation `a|b`   C) backreferences `\1`   D) quantifier `*`

5. The entropy of a biased coin with p(Heads)=0.9 is:
   A) exactly 1 bit   B) 0 bits   C) ≈ 0.47 bits   D) ≈ 1.47 bits

6. `(?=\d)` in a regex is:
   A) a capturing group matching a digit   B) a non-capturing group   C) a positive lookahead (zero-width)   D) a backreference

**Answer Key:**
<details>
<summary>Reveal Practice MCQ answers</summary>

1. **B** — log₂ 4 = 2 bits; uniform distribution achieves max entropy.
2. **C** — Huffman is optimal among **prefix** codes; arithmetic coding can do slightly better but is not a prefix code.
3. **C** — `.*` greedily extends to the last `>`; use `<.*?>` for minimal match.
4. **C** — backrefs make regex equivalence NP-hard; DFA engines deliberately omit them to keep O(n) matching.
5. **C** — H = −(0.9·log₂ 0.9 + 0.1·log₂ 0.1) ≈ 0.469 bits.
6. **C** — `(?=...)` is a positive lookahead; matches zero characters but asserts the following text matches the inner pattern.

</details>

---

