# 10 — Other Topics (Graphics, AI, Crypto, HCI, Ethics)

> **ETS MFT CS — Exam: May 1, 2026**
> Coverage target: the "Other" 3–8% bucket. Historically underweighted in study plans — expect **4–12 questions** across these five topics. High-yield, shallow-depth material. Memorize definitions, acronyms, and canonical tables.

---

## Topic 1 — Computer Graphics Basics

### 1.1 Raster vs Vector

| Aspect         | Raster (bitmap)                      | Vector                                  |
| -------------- | ------------------------------------ | --------------------------------------- |
| Representation | Grid of pixels (color per pixel)     | Mathematical primitives (lines, curves) |
| Scaling        | Loses quality (pixelation/aliasing)  | Scales losslessly                       |
| File size      | Grows with resolution (W·H·bpp)      | Grows with number of primitives         |
| Good for       | Photos, textures                     | Logos, fonts, CAD, diagrams             |
| Examples       | PNG, JPEG, BMP, GIF, TIFF            | SVG, PDF (partly), EPS, AI              |
| Rendering cost | Fast display (direct to framebuffer) | Must be rasterized for display          |

**MFT trap:** JPEG is **raster**, not vector. PDF can embed both. Fonts today (TrueType/OpenType) are **vector** outlines rasterized at display time (hinting).

### 1.2 Color Models

| Model     | Type        | Channels                   | Use                              |
| --------- | ----------- | -------------------------- | -------------------------------- |
| RGB       | Additive    | Red, Green, Blue           | Screens, cameras, light-emitting |
| CMYK      | Subtractive | Cyan, Magenta, Yellow, K   | Printing (K = black)             |
| HSV       | Perceptual  | Hue, Saturation, Value     | Color pickers, intuitive editing |
| HSL       | Perceptual  | Hue, Saturation, Lightness | CSS, design tools                |
| YUV/YCbCr | Luma/chroma | Y + 2 color diff           | Video compression (JPEG, MPEG)   |

- **Additive** (RGB): start black, add light → white.
- **Subtractive** (CMYK): start white paper, subtract reflected light → black.
- **Hue** = angle on color wheel (0–360°). **Saturation** = colorfulness. **Value/Lightness** = brightness.
- 24-bit RGB = 8 bits/channel = **16,777,216** colors; +8-bit alpha = 32-bit RGBA.

**Mnemonic:** "**A**dd light for **R**GB screens; **S**ubtract ink for **C**MYK print."

### 1.3 Coordinate Systems & Transformations

**2D transforms (3×3 matrices with homogeneous coords):**

```
Translate(tx,ty):   Scale(sx,sy):       Rotate(θ):
[1 0 tx]            [sx 0  0]           [cosθ -sinθ 0]
[0 1 ty]            [0  sy 0]           [sinθ  cosθ 0]
[0 0 1 ]            [0  0  1]           [0     0    1]
```

**3D transforms use 4×4 matrices.** Homogeneous coordinates (x, y, z, **w**) with w=1 for points, w=0 for vectors/directions.

**Why 4×4 / homogeneous?**

1. Translation is **not linear** in 3D — can't represent as 3×3 matrix. Adding a 4th row/col lets you encode translation as matrix multiplication.
2. Lets you **compose** translation + rotation + scale as a single matrix product.
3. Perspective projection naturally uses w ≠ 1 (divide x,y,z by w at end — "perspective divide").

### 1.4 Composition Order MATTERS

Matrix multiplication is **non-commutative**. Transform applied to point p:

```
p' = M_total · p    where M_total = T · R · S  (right-to-left reading)
```

- Reading **right-to-left** (column vectors): first S (scale), then R (rotate), then T (translate).
- **Rotate-then-translate ≠ translate-then-rotate.** Translating first moves the pivot; rotating around the origin after translation produces a large arc.
- Standard convention: **Scale → Rotate → Translate** (SRT) so that scale and rotation happen around the object's local origin before placement.

**MFT trap:** A question will swap the order. Answer: rotating after translation rotates around **world origin**, not the object's center.

### 1.5 Rasterization — Bresenham's Line Algorithm

- Draws lines using **only integer arithmetic** (no floats, no division).
- Decision variable chooses between the "east" pixel and "northeast" pixel based on error accumulation.
- O(max(Δx, Δy)) time — linear in longer axis.
- Extends to circles (Bresenham's circle / midpoint circle).

**Why it matters on exam:** "Which algorithm draws lines using only integer ops?" → **Bresenham**. (DDA uses floats.)

### 1.6 Clipping & Projection

- **Clipping:** discard geometry outside view frustum. Algorithms: Cohen–Sutherland (lines, region codes), Sutherland–Hodgman (polygons), Liang–Barsky.
- **Projection:**
  - **Orthographic:** parallel projection, no foreshortening. Used in CAD, architectural. Parallel lines stay parallel.
  - **Perspective:** objects farther away appear smaller; parallel lines converge at vanishing points. Realistic. Requires perspective divide (x/w, y/w, z/w).

### 1.7 Z-Buffer (Depth Buffer)

- Per-pixel depth value stored alongside color.
- On drawing a fragment: if its z < stored z, write pixel + update z; else discard.
- Solves **hidden surface removal** in O(n) per primitive.
- Alternative: painter's algorithm (sort back-to-front) — fails on interpenetrating polygons.

### 1.8 Rendering Pipeline (High Level)

```
1. Vertex processing     — vertex shader, apply model/view/projection matrices
2. Primitive assembly    — group vertices into triangles/lines
3. Clipping              — cull outside-frustum primitives
4. Rasterization         — triangles → fragments (pixels)
5. Fragment processing   — fragment/pixel shader, texturing, lighting
6. Per-fragment ops      — depth test (z-buffer), blending, stencil
7. Framebuffer           — final image displayed
```

**Mnemonic:** "**V**ery **P**retty **C**ats **R**un **F**ast **P**ast **F**ences" (Vertex, Primitive, Clip, Raster, Fragment, PerFragment, Framebuffer).

### Topic 1 MFT Trap Patterns

- Confusing RGB/CMYK direction (additive vs subtractive).
- Forgetting why 4×4 matrices (answer: to encode translation as multiplication).
- Composition order questions — remember right-to-left.
- "Which is NOT a raster format?" SVG is vector; rest (PNG/JPG/GIF) are raster.

---

## Topic 2 — Artificial Intelligence Basics

### 2.1 Uninformed Search

Let **b** = branching factor, **d** = depth of shallowest goal, **m** = max tree depth, **C\*** = optimal cost, **ε** = min step cost.

| Algorithm | Complete?      | Optimal?        | Time             | Space  |
| --------- | -------------- | --------------- | ---------------- | ------ |
| BFS       | Yes (finite b) | Yes (if step=1) | O(b^d)           | O(b^d) |
| DFS       | No (infinite)  | No              | O(b^m)           | O(bm)  |
| UCS       | Yes (ε>0)      | Yes             | O(b^(1+⌊C\*/ε⌋)) | Same   |
| IDS       | Yes            | Yes (if step=1) | O(b^d)           | O(bd)  |
| DLS       | No             | No              | O(b^l)           | O(bl)  |

- **BFS** uses a queue (FIFO). Finds shallowest goal. Massive memory.
- **DFS** uses a stack (LIFO). Low memory. Can get stuck in infinite branches.
- **UCS** (Uniform-Cost / Dijkstra): expands lowest-g(n) node. Priority queue.
- **IDS** (Iterative Deepening): run DLS with depth 0, 1, 2, …. Best of BFS (optimality, completeness) + DFS (memory). Re-expansion overhead is negligible (geometric series).

**MFT trap:** "Which has linear space?" → DFS / IDS. "Which is BFS but cost-aware?" → UCS.

### 2.2 Informed (Heuristic) Search

- **Greedy best-first:** f(n) = h(n). Fast but **not optimal**, not complete (can loop).
- **A\*:** f(n) = g(n) + h(n). Combines actual cost-so-far and estimated cost-to-goal.

**Heuristic properties:**

- **Admissible:** h(n) ≤ h\*(n) (never overestimates true cost). Guarantees **A\* optimal** on tree search.
- **Consistent (monotone):** h(n) ≤ c(n, n') + h(n') for every successor n'. Stronger: implies admissible. Guarantees A\* optimal on **graph search**.
- **Consistent ⇒ Admissible** (not vice versa).

**Why A\* is optimal (tree search, admissible h):** if A\* expands a suboptimal goal G₂, then f(G₂) = g(G₂) > C*. But any node n on the optimal path has f(n) = g(n) + h(n) ≤ C* (admissibility) ≤ f(G₂), so A\* would have expanded n first. Contradiction.

### 2.3 Adversarial Search

- **Minimax:** MAX and MIN alternate; MAX maximizes, MIN minimizes. Time O(b^d), space O(bd).
- **Alpha-beta pruning:** prune branches that can't affect decision.
  - α = best so far for MAX, β = best so far for MIN. Prune when α ≥ β.
  - **Best case (perfect ordering):** O(b^(d/2)) — effectively doubles search depth.
  - **Worst case:** same as minimax O(b^d).
- **Move ordering matters hugely.** Good ordering (best moves first) approaches best-case pruning.
- **Evaluation function** used at cutoff depth when game can't be searched to terminal.

### 2.4 Game Trees

- **MAX nodes** (our turn): pick max child utility.
- **MIN nodes** (opponent): pick min child utility.
- **Utility** at terminal: +1 win, 0 draw, −1 loss (or any bounded scale).

### 2.5 CSP (Constraint Satisfaction Problems)

- Variables + domains + constraints. E.g., map coloring, N-queens, Sudoku.
- **Backtracking:** DFS assigning variables one at a time; backtrack on constraint violation.
- **Forward checking:** after assigning X, remove inconsistent values from neighbors' domains.
- **Arc consistency (AC-3):** ensure for every value in X's domain, some consistent value exists in Y's domain, for each arc. Propagates until fixed point.
- **Heuristics:** MRV (minimum remaining values — pick most constrained var), degree heuristic (tie-break), LCV (least constraining value — try value ruling out fewest).

### 2.6 Machine Learning Basics

- **Supervised:** labeled data. Classification (discrete labels) / Regression (continuous output).
- **Unsupervised:** no labels. Clustering (k-means, hierarchical), dimensionality reduction (PCA).
- **Reinforcement:** agent + environment + reward. Policy, value function, Q-learning.
- **Semi-supervised / self-supervised:** some/derived labels.

**Overfitting:** model memorizes training data, fails on new data. Signs: low train error, high test error.
**Underfitting:** model too simple; high error on both.

**Bias–variance tradeoff:**

- **Bias:** error from simplifying assumptions (underfitting).
- **Variance:** error from sensitivity to training data (overfitting).
- Total error = Bias² + Variance + Irreducible noise.

**Train/Validation/Test split:**

- **Train:** fit parameters.
- **Validation:** tune hyperparameters, early stopping.
- **Test:** final unbiased estimate. **Never tune on test.**
- Typical split: 60/20/20 or 70/15/15. Use **k-fold cross-validation** when data is scarce.

**Confusion Matrix (binary):**

|              | Predicted + | Predicted − |
| ------------ | ----------- | ----------- |
| **Actual +** | TP          | FN          |
| **Actual −** | FP          | TN          |

- **Accuracy** = (TP+TN) / total. Misleading on imbalanced data.
- **Precision** = TP / (TP + FP) — "of predicted positives, how many were right?"
- **Recall** (sensitivity, TPR) = TP / (TP + FN) — "of actual positives, how many did we catch?"
- **F1** = 2·P·R / (P+R) — harmonic mean.
- **Specificity** = TN / (TN + FP).

**Mnemonic:** **P**recision watches **P**redictions; **R**ecall watches **R**eality.

### 2.7 Neural Networks

- **Perceptron:** y = step(Σwᵢxᵢ + b). Only separates **linearly separable** data (can't do XOR).
- **MLP** (multi-layer perceptron): adding hidden layers + nonlinearity → universal approximator.
- **Activation functions:** sigmoid (0–1, vanishing gradients), tanh (−1 to 1), ReLU (max(0,x) — fast, sparse, popular), softmax (output probabilities).
- **Backpropagation:** computes ∂Loss/∂w via chain rule, layer-by-layer from output backwards. Combined with gradient descent to update weights.

### 2.8 Logic-Based AI

- **Propositional resolution:** refutation proof — add negation of goal, derive empty clause. Requires CNF.
- **Horn clauses:** clause with **at most one positive literal**: ¬p₁ ∨ ¬p₂ ∨ … ∨ q. Rewritable as `p₁ ∧ p₂ → q`. Inference in Horn-KB is **polynomial** (linear with forward/backward chaining). This is why **Prolog** is fast.
- **Definite clause:** exactly one positive literal.

### Topic 2 MFT Traps

- BFS optimal **only if step costs equal**. UCS is always optimal (ε>0).
- A\* admissibility ≠ consistency. Consistency is stronger.
- Alpha-beta **does not change answer** — same result as minimax, just faster.
- Precision vs Recall direction — easy to swap.

---

## Topic 3 — Cryptography Basics

### 3.1 Symmetric Ciphers

**Block vs Stream:**

- **Block cipher:** encrypts fixed-size blocks (AES: 128-bit block). Uses modes of operation.
- **Stream cipher:** encrypts bit/byte at a time with keystream XOR. Faster for streaming data. E.g., RC4 (broken), ChaCha20, Salsa20.

**DES:** 56-bit key — **broken** (brute-forceable since 1999). 3DES (168-bit effective ~112) — deprecated.
**AES:** 128/192/256-bit keys. Block size 128 bits. Standard today. SubBytes/ShiftRows/MixColumns/AddRoundKey.

**Modes of operation:**

| Mode | Properties                                                                        |
| ---- | --------------------------------------------------------------------------------- |
| ECB  | Same plaintext block → same ciphertext. **BAD** (patterns visible — penguin!).    |
| CBC  | XOR prev ciphertext before encrypt. Needs IV. Sequential. No tamper detect.       |
| CTR  | Encrypt counter, XOR with plaintext. Parallelizable. Acts like stream cipher.     |
| GCM  | CTR + authentication (GMAC). **Authenticated encryption (AEAD)**. Modern default. |
| CFB  | Like CBC but operates as stream.                                                  |
| OFB  | Feedback without plaintext dependency — stream-like.                              |

**MFT trap:** ECB the classic "don't use" mode — images reveal structure.

### 3.2 Asymmetric (Public-Key)

**RSA:**

1. Pick primes p, q. n = p·q. φ(n) = (p−1)(q−1).
2. Pick e coprime with φ(n). Compute d = e⁻¹ mod φ(n).
3. Public key: (n, e). Private: (n, d).
4. Encrypt: c = m^e mod n. Decrypt: m = c^d mod n.
5. Sign: s = H(m)^d mod n. Verify: s^e mod n =? H(m).

- Security: factoring n is hard.

**Diffie–Hellman (DH):** key agreement, not encryption.

- Public: prime p, generator g. Alice picks a, sends g^a mod p. Bob picks b, sends g^b mod p. Shared = g^(ab) mod p.
- Security: discrete log problem.
- **Vulnerable to MITM** unless authenticated.

**ECC (Elliptic Curve Cryptography):**

- Based on elliptic curve discrete log.
- Same security as RSA with **much smaller keys** (256-bit ECC ≈ 3072-bit RSA).
- Used in TLS (ECDHE), Bitcoin (secp256k1), SSH.

### 3.3 Hash Functions

**Required properties:**

1. **Preimage resistance:** given h, hard to find m where H(m)=h.
2. **Second preimage resistance:** given m₁, hard to find m₂ ≠ m₁ with H(m₁)=H(m₂).
3. **Collision resistance:** hard to find any m₁ ≠ m₂ with H(m₁)=H(m₂).

Collision resistance implies second preimage (but not vice versa).

**Birthday paradox:** collision expected after ~2^(n/2) tries for n-bit hash. So 128-bit hash only gives 64-bit collision security.

| Hash     | Status                                          |
| -------- | ----------------------------------------------- |
| MD5      | **Broken** (collisions found 2004). Do not use. |
| SHA-1    | **Broken** (SHAttered 2017).                    |
| SHA-2    | SHA-256, SHA-512. **Secure**.                   |
| SHA-3    | Keccak. **Secure**, different design (sponge).  |
| BLAKE2/3 | Modern, fast, secure.                           |

### 3.4 MAC vs Digital Signature

| Aspect              | MAC (e.g., HMAC)               | Digital Signature (e.g., RSA-PSS)           |
| ------------------- | ------------------------------ | ------------------------------------------- |
| Key type            | Symmetric (shared secret)      | Asymmetric (private signs, public verifies) |
| Authentication      | Yes                            | Yes                                         |
| Integrity           | Yes                            | Yes                                         |
| **Non-repudiation** | **No** (both parties have key) | **Yes** (only signer has priv key)          |
| Speed               | Fast                           | Slow                                        |

### 3.5 PKI & Certificates

- **PKI** (Public Key Infrastructure): CAs, RAs, certificates, CRLs/OCSP for revocation.
- **X.509 certificate** binds identity (CN, SAN) to public key, signed by CA.
- **Chain of trust:** end-entity cert → intermediate CA → root CA (self-signed, pre-trusted in browser/OS store).

### 3.6 TLS Handshake (High Level, TLS 1.2 classic)

1. **ClientHello:** versions, cipher suites, random nonce.
2. **ServerHello:** chosen cipher, random, **certificate**.
3. Client verifies cert chain, extracts public key.
4. **Key exchange:** client generates premaster secret, encrypts with server pubkey (RSA) OR ECDHE (forward secrecy).
5. Both derive session keys from premaster + nonces.
6. **Finished** messages (HMAC of handshake) sent encrypted → verified.
7. Application data flows under symmetric cipher (AES-GCM).

**TLS 1.3:** 1-RTT handshake, only AEAD ciphers, mandatory forward secrecy (ECDHE).

### 3.7 Password Storage

- **Never store plaintext.** Never store plain hash (rainbow tables).
- **Salt:** random per-user value concatenated with password before hashing. Defeats precomputed rainbow tables and prevents identical passwords hashing identically.
- **Pepper:** site-wide secret (in env var, not DB) added additionally.
- Use **slow hash** functions: **bcrypt**, **scrypt** (memory-hard), **Argon2** (winner of PHC, memory+time hard). Configurable work factor.

### 3.8 Kerckhoffs's Principle

> "A cryptosystem should be secure even if everything about the system, except the key, is public knowledge."

Security must live in the key, **not** in secrecy of algorithm ("security through obscurity" is an anti-pattern). Shannon's maxim: "The enemy knows the system."

### Topic 3 MFT Traps

- Hash is **one-way** — not encryption.
- MAC does **NOT** provide non-repudiation.
- DH alone is vulnerable to MITM (need cert/signature).
- ECB is the "wrong answer" mode on every exam.
- Salt ≠ pepper.

---

## Topic 4 — HCI (Human–Computer Interaction)

### 4.1 Nielsen's 10 Usability Heuristics

1. **Visibility of system status** — feedback, loading indicators.
2. **Match between system and real world** — user's language, familiar metaphors.
3. **User control and freedom** — undo, emergency exits.
4. **Consistency and standards** — platform conventions.
5. **Error prevention** — constraints, confirmations.
6. **Recognition rather than recall** — menus, visible options.
7. **Flexibility and efficiency of use** — shortcuts for experts.
8. **Aesthetic and minimalist design** — no irrelevant info.
9. **Help users recognize, diagnose, recover from errors** — plain-language error msgs.
10. **Help and documentation** — searchable, contextual.

**Mnemonic:** "**V**ery **M**uch **U**ser **C**entered, **E**rror-**R**esistant **F**riendly **A**pps **H**elp **H**umans" (Visibility, Match, User-control, Consistency, Error-prev, Recognition, Flexibility, Aesthetic, Help-recover, Help-docs).

### 4.2 Fitts's Law

> **T = a + b · log₂(D/W + 1)**

- T = time to acquire target, D = distance to target, W = width (size) of target.
- Implication: **bigger, closer targets are faster.** Edges/corners are infinitely wide (infinite edge) → Mac menu bar at screen top, Windows Start in corner.

### 4.3 Hick's Law

> **T = b · log₂(n + 1)**

- Decision time grows logarithmically with number of equally probable choices.
- Implication: segment long menus, use progressive disclosure, group options.

### 4.4 Norman's 7 Stages of Action

Execution side:

1. Form goal.
2. Form intention.
3. Specify action sequence.
4. Execute action.

Evaluation side: 5. Perceive state of world. 6. Interpret perception. 7. Evaluate outcome against goal.

Two **gulfs**:

- **Gulf of Execution:** gap between user's goal and means to achieve it (hard to figure out how).
- **Gulf of Evaluation:** gap between system state and user's understanding (hard to tell what happened).

### 4.5 Accessibility

- **WCAG** (Web Content Accessibility Guidelines). 4 principles (**POUR**): **P**erceivable, **O**perable, **U**nderstandable, **R**obust. Levels A / AA / AAA.
- **ARIA** (Accessible Rich Internet Applications): roles, states, properties for screen readers. `role="button"`, `aria-label`, `aria-live`.
- Key practices: alt text, color contrast (4.5:1 normal, 3:1 large), keyboard navigation, focus indicators, don't rely on color alone.

### 4.6 User-Centered Design (UCD)

- Iterative: understand users → design → prototype → evaluate → repeat.
- **Personas:** fictional archetypes representing user segments.
- **Usability testing:** observe real users doing tasks.
- **Think-aloud protocol:** users verbalize thoughts while using system; reveals mental models.
- **Heuristic evaluation:** experts check against heuristics (cheap, less thorough than user tests).
- **Cognitive walkthrough:** step through tasks asking "will user know what to do?"

### 4.7 Error Types (Norman / Reason)

- **Slip:** correct intention, wrong action (typo, clicked wrong button). Skill-based.
- **Mistake:** wrong intention (misunderstood the task). Knowledge/rule-based.
- Design addresses each differently: slips → constraints, confirmations. Mistakes → better feedback, mental model support.

### 4.8 Design Principles (Norman)

- **Affordance:** perceived properties suggesting use (button looks pressable, handle looks pullable).
- **Signifier:** the communicative cue (label, icon) that reveals the affordance.
- **Feedback:** system communicates result of action.
- **Constraints:** limit possible actions (grey out, disable, physical shape).
- **Mapping:** relationship between controls and effects (stove knob layout matches burners).
- **Conceptual model:** user's mental model of how system works.

### Topic 4 MFT Traps

- Fitts's law: target **closer & bigger** is faster — both variables matter.
- Heuristic evaluation uses **experts**, not end users.
- Slip vs Mistake direction is often reversed in distractors.

---

## Topic 5 — Ethics, Legal & Licensing in Computing

### 5.1 ACM Code of Ethics (2018) — Key Principles

**General Ethical Principles (Section 1):**
1.1 Contribute to society and human well-being.
1.2 Avoid harm.
1.3 Be honest and trustworthy.
1.4 Be fair and take action not to discriminate.
1.5 Respect work required to produce ideas (attribution, copyright).
1.6 Respect privacy.
1.7 Honor confidentiality.

**Professional Responsibilities (Section 2):** strive for high quality, maintain competence, know laws, accept/provide review, comprehensive evaluations, honor contracts, improve public understanding, access only when authorized.

**Leadership (Section 3)** and **Compliance (Section 4)** sections follow.

### 5.2 IP Forms

| Right            | Covers                                | Term (US)                                    | Needs registration?                     |
| ---------------- | ------------------------------------- | -------------------------------------------- | --------------------------------------- |
| **Copyright**    | Expression of ideas (code, text, art) | Life + 70 (individual); 95 yrs work-for-hire | Auto on fixation                        |
| **Patent**       | Novel inventions / processes          | Utility 20 yrs from filing                   | Yes, USPTO                              |
| **Trademark**    | Brand identifiers (names, logos)      | Indefinite w/ use + renew                    | Optional (™ common-law; ® registered)   |
| **Trade secret** | Confidential business info            | As long as kept secret                       | No (but requires reasonable protection) |

- **Copyright protects expression, NOT ideas** (idea–expression dichotomy). Algorithms as abstract ideas not copyrightable (but source code text is).
- Coca-Cola formula = trade secret, not patent (patent expires).

### 5.3 Software Licenses

| License              | Type             | Must share source?               | Can mix with proprietary?         | Notes                                      |
| -------------------- | ---------------- | -------------------------------- | --------------------------------- | ------------------------------------------ |
| **GPL** v2/v3        | Strong copyleft  | Yes, if distribute               | No (viral: your code becomes GPL) | GPL v3 adds patent grant, anti-Tivoization |
| **LGPL**             | Weak copyleft    | Only LGPL-covered parts          | Yes, via dynamic linking          | Library-friendly                           |
| **AGPL**             | Network copyleft | Yes, even for network-served use | No                                | Closes "SaaS loophole"                     |
| **MIT**              | Permissive       | No                               | Yes                               | Must preserve copyright notice             |
| **BSD** (2/3-clause) | Permissive       | No                               | Yes                               | 3-clause adds no-endorsement               |
| **Apache 2.0**       | Permissive       | No                               | Yes                               | Explicit patent grant, NOTICE file         |
| **Proprietary/EULA** | Closed           | N/A                              | N/A                               | Per vendor terms                           |

**Key question pattern:** "Can you use GPL code in a commercial closed-source product?"

- If you **distribute** a binary linked with GPL code → you **must** release your combined source under GPL (viral).
- If you **use it internally** (no distribution) → generally OK (GPL isn't triggered without distribution). But AGPL triggers on network-served use.
- LGPL via dynamic linking → keep your own code proprietary, but must allow replacing LGPL lib.

### 5.4 DMCA (Digital Millennium Copyright Act, 1998)

- Implements WIPO treaties.
- **Anti-circumvention** (§1201): illegal to bypass technical protection measures (DRM).
- **Safe harbor** (§512): ISPs/platforms not liable for user content if they respond to **takedown notices** and have a designated agent. Counter-notice process exists.

### 5.5 Privacy / GDPR

**GDPR (EU, 2018) key principles (Article 5):**

1. **Lawfulness, fairness, transparency.**
2. **Purpose limitation** — collected for specified purposes.
3. **Data minimization** — only what's necessary.
4. **Accuracy.**
5. **Storage limitation** — keep only as long as needed.
6. **Integrity & confidentiality** (security).
7. **Accountability.**

**Rights:** access, rectification, **erasure ("right to be forgotten")**, restriction, portability, objection.
**Consent:** must be freely given, specific, informed, unambiguous, withdrawable.
**Fines:** up to €20M or 4% global annual turnover, whichever higher.

Other privacy laws: **CCPA/CPRA** (California), **HIPAA** (US health), **FERPA** (US education), **COPPA** (US under 13).

### 5.6 Fair Use (US Copyright Act §107)

Four factors:

1. **Purpose and character** of use (transformative? commercial vs educational/nonprofit).
2. **Nature of copyrighted work** (factual more freely usable than creative fiction).
3. **Amount and substantiality** of portion used (and whether "heart of work").
4. **Effect on the market** for original work.

No single factor decides; courts weigh all four. EU has narrower "fair dealing" instead.

### 5.7 Whistleblowing & Professional Responsibility

- Engineers have a duty to warn about serious risks to public safety/welfare, even against employer wishes.
- Internal escalation first (raise to management / ethics board / ombudsperson); external (regulators, press) as last resort.
- **Sarbanes-Oxley** (US) protects corporate whistleblowers on financial fraud.
- Conflicts of interest must be disclosed.

### 5.8 Common Scenario Judgments

| Scenario                                                                | Answer                                                           |
| ----------------------------------------------------------------------- | ---------------------------------------------------------------- |
| Found GPL library on GitHub, want to link into commercial app for sale  | Must release your source under GPL (or pick LGPL/MIT/Apache lib) |
| Using MIT-licensed code in proprietary product                          | OK; preserve copyright notice                                    |
| Running modified GPL code on your own server, never distributing binary | GPL not triggered (but AGPL would be)                            |
| Storing user passwords as SHA-256 only                                  | Fails best practice — need salted slow hash (bcrypt/Argon2)      |
| Collecting EU user emails without consent banner                        | GDPR violation                                                   |
| Quoting 2 sentences of a book in a research paper                       | Likely fair use (small amount, educational, transformative)      |

### Topic 5 MFT Traps

- **Copyright ≠ Patent.** Copyright auto, patent must be filed.
- GPL is **viral on distribution**, not on mere use.
- Trade secrets last forever **if kept secret** (no fixed term).
- Fair use has **4 factors**, not a single rule.
- ACM code emphasizes **public good first**, employer second.

---

## 🧪 Try Yourself — Practice Questions

**Topic 1 — Graphics**

**Q1.** Why do 3D graphics use 4×4 matrices instead of 3×3?
A. To allow 16-bit color
B. Because rotation requires 4 parameters
C. To represent translation as matrix multiplication using homogeneous coordinates
D. To speed up GPU memory access

<details>
<summary>Reveal answer</summary>

**Answer:** C

Translation is affine, not linear in 3D. Homogeneous coords add a 4th component so translation can be encoded in matrix multiplication, enabling composition.

</details>

**Q2.** You first translate a 2D object by (5, 0), then rotate 90° about the origin. Where does a point initially at (0, 0) end up?
A. (0, 0)
B. (5, 0)
C. (0, 5)
D. (−5, 0)

<details>
<summary>Reveal answer</summary>

**Answer:** C

Starting at (0,0), translate +5,0 → (5,0). Rotating (5,0) by 90° about origin gives (0,5). The correct answer is **C. (0, 5)**.

</details>

**Q3.** Which of the following is NOT a raster format?
A. JPEG
B. PNG
C. SVG
D. GIF

<details>
<summary>Reveal answer</summary>

**Answer:** C

SVG is vector (XML-based). The others are pixel-grid raster formats.

</details>

**Q4.** What does the Z-buffer algorithm solve?
A. Anti-aliasing
B. Hidden surface removal
C. Texture mapping
D. Color quantization

<details>
<summary>Reveal answer</summary>

**Answer:** B

Z-buffer stores per-pixel depth; fragments closer to the camera overwrite farther ones, achieving hidden surface removal.

</details>

**Topic 2 — AI**

**Q5.** Which search algorithm has O(bd) space complexity while still being complete and optimal (unit costs)?
A. BFS
B. DFS
C. IDS
D. Greedy best-first

<details>
<summary>Reveal answer</summary>

**Answer:** C

Iterative Deepening Search combines DFS's O(bd) space with BFS's completeness and optimality under unit costs. BFS is O(b^d) space.

</details>

**Q6.** A heuristic h is admissible if:
A. h(n) = h*(n) always
B. h(n) ≥ h*(n) for all n
C. h(n) ≤ h\*(n) for all n
D. h satisfies the triangle inequality

<details>
<summary>Reveal answer</summary>

**Answer:** C

Admissible means never overestimates true cost: h(n) ≤ h\*(n).

</details>

**Q7.** Alpha-beta pruning's best-case time complexity is:
A. O(b^d)
B. O(b^(d/2))
C. O(d^b)
D. O(bd)

<details>
<summary>Reveal answer</summary>

**Answer:** B

With perfect move ordering, alpha-beta effectively halves the depth, giving O(b^(d/2)).

</details>

**Q8.** A classifier predicts 100 positives, 80 of which are correct. There are 200 actual positives in the dataset. What are precision and recall?
A. P=0.80, R=0.40
B. P=0.40, R=0.80
C. P=0.80, R=0.80
D. P=0.50, R=0.50

<details>
<summary>Reveal answer</summary>

**Answer:** A

Precision = TP/(TP+FP) = 80/100 = 0.80. Recall = TP/(TP+FN) = 80/200 = 0.40.

</details>

**Topic 3 — Cryptography**

**Q9.** Why is ECB mode discouraged?
A. It's too slow
B. It requires a long IV
C. Identical plaintext blocks produce identical ciphertext blocks, revealing patterns
D. It cannot be parallelized

<details>
<summary>Reveal answer</summary>

**Answer:** C

ECB encrypts each block independently, so identical plaintext blocks yield identical ciphertext blocks and leak structure.

</details>

**Q10.** Which service does a MAC provide that a plain hash does not, and which does a digital signature provide that a MAC does not?
A. Confidentiality; Integrity
B. Authentication; Non-repudiation
C. Non-repudiation; Authentication
D. Integrity; Confidentiality

<details>
<summary>Reveal answer</summary>

**Answer:** B

Plain hash provides integrity only. MAC adds authentication. Digital signature additionally provides non-repudiation because only the private-key holder could have signed.

</details>

**Q11.** Diffie–Hellman key exchange, used alone, is vulnerable to:
A. Brute force on 128-bit keys
B. Man-in-the-middle attack
C. Collision attack
D. Timing attack

<details>
<summary>Reveal answer</summary>

**Answer:** B

Unauthenticated DH allows an attacker to perform separate exchanges with each party and relay traffic. Fix with certificate-based authentication.

</details>

**Q12.** For a 256-bit hash function, approximately how many hashes must be computed to find a collision with 50% probability (birthday bound)?
A. 2^32
B. 2^64
C. 2^128
D. 2^256

<details>
<summary>Reveal answer</summary>

**Answer:** C

Birthday bound is approximately 2^(n/2). For n = 256, that is 2^128 operations.

</details>

**Topic 4 — HCI**

**Q13.** Fitts's law predicts that pointing time is:
A. Constant regardless of target size
B. Proportional to target width
C. Increases logarithmically with distance/width ratio
D. Decreases linearly with distance

<details>
<summary>Reveal answer</summary>

**Answer:** C

T = a + b·log₂(D/W + 1), so pointing time grows logarithmically with the distance-to-width ratio.

</details>

**Q14.** A user means to click "Save" but clicks "Save As" by accident. This error is best classified as a:
A. Mistake
B. Slip
C. Lapse
D. Violation

<details>
<summary>Reveal answer</summary>

**Answer:** B

Slip means correct intention, wrong action. Mistake means wrong intention altogether.

</details>

**Q15.** Which is NOT one of Nielsen's 10 heuristics?
A. Visibility of system status
B. Aesthetic and minimalist design
C. Gestalt principle of similarity
D. Match between system and real world

<details>
<summary>Reveal answer</summary>

**Answer:** C

Gestalt principles are perceptual grouping laws, not part of Nielsen's 10 usability heuristics.

</details>

**Q16.** WCAG's four principles are summarized by the acronym:
A. RACI
B. POUR
C. SOLID
D. CRUD

<details>
<summary>Reveal answer</summary>

**Answer:** B

Perceivable, Operable, Understandable, Robust -> POUR.

</details>

**Topic 5 — Ethics & Licensing**

**Q17.** You want to statically link a GPLv3 library into a proprietary application you sell. What must you do?
A. Nothing — GPL allows this
B. Pay the FSF a license fee
C. Release your combined application's source under GPL when you distribute
D. Only attribute the authors in a NOTICE file

<details>
<summary>Reveal answer</summary>

**Answer:** C

GPL is viral on distribution: combining and distributing with GPL code obliges you to license the combined work under GPL and provide source.

</details>

**Q18.** Which of the following is covered by **copyright**, not patent?
A. A new hardware chip design
B. The expression of a program's source code
C. A secret manufacturing process kept internal
D. A company logo

<details>
<summary>Reveal answer</summary>

**Answer:** B

Source code expression is covered by copyright. Chip design is patent, secret process is trade secret, logo is trademark.

</details>

**Q19.** A GDPR principle requiring collection of only the data needed for a stated purpose is called:
A. Purpose limitation
B. Data minimization
C. Storage limitation
D. Accountability

<details>
<summary>Reveal answer</summary>

**Answer:** B

Data minimization means only collecting what's necessary for the stated purpose.

</details>

**Q20.** Which license permits use in closed-source commercial software with minimal obligations (essentially preserve copyright notice)?
A. GPL v3
B. AGPL
C. MIT
D. LGPL

<details>
<summary>Reveal answer</summary>

**Answer:** C

MIT is a permissive license with minimal obligations, essentially preserving the copyright/license notice.

</details>

---

### 🎯 Topic 10 Exam-Day Priorities

1. Graphics: **why 4×4**, composition order, raster vs vector, Z-buffer.
2. AI: search table (complete/optimal/time/space), A\* admissibility, precision vs recall formula, alpha-beta O(b^(d/2)).
3. Crypto: ECB-bad, MAC-no-nonrepudiation, birthday bound 2^(n/2), salt+bcrypt, Kerckhoffs.
4. HCI: Fitts's law formula, POUR, slip vs mistake, Nielsen heuristics (recognize list).
5. Ethics: GPL viral-on-distribution, copyright-vs-patent, GDPR principles, fair use 4 factors.

**Score target:** 4/4 or 3/4 per topic band. Coverage in this file + baseline reading should lock in the Other 3–8% bucket.
