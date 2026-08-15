# CHAPTER 3

# Quantum Complexity Theory

*Complexity Classes  |  Query Complexity  |  The Polynomial Method  |  Quantum Lower Bounds*

<div class="box box-anecdote">
<p class="box-title"><strong>📜  Opening Reflection — Scott Aaronson, 2013</strong></p>
<p>"Complexity theory is the study of what is possible and what is not possible."</p>
<p>— Scott Aaronson, 2013</p>
<p>The deepest questions in computer science are not about faster hardware or better algorithms —</p>
<p>they are about the fundamental limits of computation itself. What can be computed efficiently?</p>
<p>What cannot? Does quantum mechanics change these limits, and by how much?</p>
<p>These are the questions that complexity theory answers — or tries to.</p>
</div>

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  Chapter 3 Learning Objectives</strong></p>
<p>After studying this chapter, you will be able to:</p>
<p>•  Define and distinguish the complexity classes P, NP, BPP, BQP, QMA, PP, and PSPACE</p>
<p>•  Prove or state the known containment relations: P ⊆ BPP ⊆ BQP ⊆ PP ⊆ PSPACE</p>
<p>•  Explain the decision tree and quantum query complexity models</p>
<p>•  State and apply the polynomial method to derive quantum query lower bounds</p>
<p>•  Prove the BBBV theorem: Grover's O(√N) search is optimal for unstructured search</p>
<p>•  Describe the quantum adversary method and its application to query lower bounds</p>
<p>•  Classify problems as BQP-complete, QMA-complete, or belonging to other classes</p>
<p>•  Explain the quantum speedup taxonomy: exponential, quadratic, polynomial, none</p>
</div>

## 3.1 Foundations of Classical Complexity Theory

Computational complexity theory asks: given a computational problem, what resources (time, space, randomness) are necessary and sufficient to solve it? The answers depend critically on what we mean by 'efficient' — and whether we are using a classical or quantum computer.

### 3.1.1 The Turing Machine Model

The Church-Turing thesis asserts that any effectively computable function can be computed by a Turing machine. The complexity of an algorithm is measured in terms of: time complexity (steps as function of input length n), space complexity (tape cells used), and circuit complexity (gates in a Boolean circuit).

<div class="box box-anecdote">
<p class="box-title"><strong>📜  The Birth of Complexity Theory — Cook, Karp, and the P vs NP Problem</strong></p>
<p>Computational complexity theory was born in the 1960s with Hartmanis and Stearns, who won the 1993 Turing</p>
<p>Award for their foundational work. But the field came alive in 1971 when Stephen Cook published</p>
<p>'The Complexity of Theorem Proving Procedures' — introducing NP-completeness and proving that SAT</p>
<p>is the 'hardest' problem in NP. A year later, Richard Karp listed 21 NP-complete problems.</p>
<p>P vs NP — whether every problem verifiable in polynomial time is also solvable in polynomial time —</p>
<p>remains the most famous unsolved problem in mathematics and computer science.</p>
<p>The Clay Mathematics Institute offers $1,000,000 for a solution. Most experts believe P ≠ NP, but no proof exists.</p>
</div>

### 3.1.2 The Class P: Polynomial Time

The class P (polynomial time) contains all decision problems solvable by a deterministic Turing machine in time O(n^k) for some constant k. P is our formal definition of 'efficiently solvable'.

| Problem | Input | Best Classical Algorithm | In P? |
|---|---|---|---|
| Primality testing | n-bit integer | O(n⁶) AKS algorithm (2002) | YES |
| Sorting | n numbers | O(n log n) merge sort | YES |
| Matrix multiplication | n×n matrices | O(n^2.37) Strassen/Williams | YES |
| Shortest path | Graph, n vertices | O(n² log n) Dijkstra | YES |
| Integer factoring | n-bit integer | O(exp((log N)^(1/3))) GNFS | UNKNOWN |
| SAT (3-CNF) | Boolean formula | O(2^n) brute force (NP-hard) | UNKNOWN |
| Graph isomorphism | Two n-vertex graphs | Quasi-polynomial (Babai 2016) | UNKNOWN |

### 3.1.3 The Class NP: Non-deterministic Polynomial Time

NP contains all decision problems where YES instances have a polynomial-length proof (certificate) verifiable in polynomial time.

<div class="box box-generic">
<p class="box-title"><strong>Definition 3.1  The Class NP</strong></p>
<p>A language L ⊆ {0,1}* is in NP if there exists a polynomial-time verifier V such that:</p>
<p>x ∈ L  ⟺  ∃ certificate c with |c| ≤ poly(|x|) such that V(x, c) = 1</p>
<p>Examples of NP problems:</p>
<p>• SAT: Given a CNF formula, does it have a satisfying assignment? (certificate = assignment)</p>
<p>• 3-COLOURING: Can a graph be 3-coloured? (certificate = the colouring)</p>
<p>• SUBSET-SUM: Do any elements sum to target T? (certificate = the subset)</p>
<p>• TSP-DECISION: Does a tour of cost ≤ k exist? (certificate = the tour)</p>
</div>

### 3.1.4 The Class BPP: Bounded-Error Probabilistic Polynomial Time

<div class="box box-generic">
<p class="box-title"><strong>Definition 3.2  The Class BPP</strong></p>
<p>A language L is in BPP if there exists a probabilistic polynomial-time Turing machine M such that:</p>
<p>x ∈ L  ⟹  Pr[M(x) = 1] ≥ 2/3</p>
<p>x ∉ L  ⟹  Pr[M(x) = 1] ≤ 1/3</p>
<p>The constants 2/3 and 1/3 are not special: any constants bounded away from 1/2 suffice,</p>
<p>because the error can be reduced to exp(−k) by running k times and taking majority vote.</p>
<p>Key facts: P ⊆ BPP; most experts believe P = BPP (Nisan-Wigderson conjecture).</p>
</div>

## 3.2 BQP: Bounded-Error Quantum Polynomial Time

BQP (bounded-error quantum polynomial time) is the quantum analogue of BPP — the class of problems solvable by a quantum computer with bounded error in polynomial time. BQP is the central complexity class of quantum computing.

<div class="box box-generic">
<p class="box-title"><strong>Definition 3.3  The Class BQP</strong></p>
<p>A language L is in BQP if there exists a uniform family of quantum circuits {Q_n} of polynomial size such that:</p>
<p>x ∈ L  ⟹  Pr[ Q_{|x|}(|x⟩) accepts ] ≥ 2/3</p>
<p>x ∉ L  ⟹  Pr[ Q_{|x|}(|x⟩) accepts ] ≤ 1/3</p>
<p>'Uniform' means the circuit Q_n can be generated by a classical polynomial-time algorithm,</p>
<p>and Q_n uses O(poly(n)) qubits and O(poly(n)) two-qubit gates.</p>
</div>

### 3.2.1 Problems Known to Be in BQP

| Problem | Classical Best | Quantum Complexity | Speedup Type |
|---|---|---|---|
| Integer factoring (Shor) | Sub-exponential GNFS | O(n³) polynomial | Exponential |
| Discrete logarithm (Shor) | Sub-exponential | O(n³) polynomial | Exponential |
| Unstructured search (Grover) | O(N) classical | O(√N) | Quadratic |
| Element distinctness | O(N) | O(N^(2/3)) | Super-quadratic |
| Parity N bits (Deutsch-Jozsa) | O(N) deterministic | O(1) | Exponential |
| NAND tree evaluation | Θ(N) | O(N^0.793) | Polynomial |
| Simon's problem | Ω(2^(n/2)) average | O(n) | Exponential |

### 3.2.2 The BQP vs BPP Question and Known Containments

The most important unresolved question about BQP is whether it strictly contains BPP. Shor's algorithm provides strong evidence that BQP ⊋ BPP, but no unconditional proof exists.

<img class="fig-img" src="content/images/image12.png" alt="figure">

**Figure 1: Quantum Complexity Class Hierarchy** *— Conjectured containment structure: P ⊆ BPP ⊆ BQP ⊆ PP ⊆ PSPACE; QMA = quantum NP; key unknown: NP ⊆ BQP?*

<div class="box box-generic">
<p class="box-title"><strong>Theorem 3.4  BQP Containment (all proven)</strong></p>
<p>The following inclusions are proven:</p>
<p>P ⊆ BPP ⊆ BQP ⊆ PP ⊆ PSPACE</p>
<p>Proof sketches:</p>
<p>• P ⊆ BPP: Any deterministic algorithm is trivially a zero-error probabilistic one.</p>
<p>• BPP ⊆ BQP: Classical randomness can be simulated by quantum circuits (Hadamard coins).</p>
<p>• BQP ⊆ PP: Quantum computation probabilities are polynomials in amplitudes (Bernstein-Vazirani 1997).</p>
<p>• PP ⊆ PSPACE: PP machines can be simulated in polynomial space by counting accepting paths.</p>
</div>

## 3.3 QMA: Quantum Merlin-Arthur and the Quantum NP

QMA (Quantum Merlin-Arthur) is the quantum analogue of NP. In QMA, a quantum verifier checks a quantum proof — a quantum state sent by the all-powerful prover 'Merlin'.

<div class="box box-generic">
<p class="box-title"><strong>Definition 3.5  The Class QMA</strong></p>
<p>A language L is in QMA if there exists a polynomial-time quantum verifier V such that:</p>
<p>x ∈ L  ⟹  ∃ quantum state |ψ⟩ (poly(n) qubits) s.t. Pr[V(x,|ψ⟩) accepts] ≥ 2/3</p>
<p>x ∉ L  ⟹  for ALL states |ψ⟩: Pr[V(x,|ψ⟩) accepts] ≤ 1/3</p>
<p>The quantum proof |ψ⟩ is called the 'witness'. Unlike NP, we cannot copy quantum witnesses</p>
<p>(no-cloning theorem), so QMA amplification is more delicate than in NP.</p>
</div>

### 3.3.1 The k-Local Hamiltonian Problem: QMA-Complete

The most important QMA-complete problem is the k-Local Hamiltonian problem — the quantum analogue of SAT. This result, proved by Kitaev in 1999, establishes QMA's 'hardness'.

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  k-Local Hamiltonian Problem (k-LH)</strong></p>
<p>INPUT: A k-local Hamiltonian H = Σᵢ Hᵢ on n qubits (each Hᵢ acts on at most k qubits),</p>
<p>and two real numbers a &lt; b with b − a ≥ 1/poly(n).</p>
<p>PROMISE: Either the ground state energy λ₀(H) ≤ a (YES instance),</p>
<p>or λ₀(H) ≥ b (NO instance).</p>
<p>GOAL: Decide which case holds.</p>
<p>Theorem (Kitaev 1999): 5-LH is QMA-complete.</p>
<p>Improved: 2-LH is QMA-complete (Kempe, Kitaev, Regev 2006).</p>
<p>Physical interpretation: Determining if a quantum system has low ground state energy is as</p>
<p>hard as any quantum verification problem — QMA-hardness is physically motivated.</p>
</div>

<div class="box box-anecdote">
<p class="box-title"><strong>📜  Kitaev and the Quantum Cook-Levin Theorem (1999)</strong></p>
<p>Alexei Kitaev presented what is now called the 'Quantum Cook-Levin Theorem' at a workshop in 1999.</p>
<p>The result was so elegant and profound that it circulated as an unpublished preprint for years before</p>
<p>appearing in print. Kitaev proved it by constructing a 'clock Hamiltonian' — a quantum system whose</p>
<p>ground state encodes the ENTIRE HISTORY of a quantum computation. The k-Local Hamiltonian problem</p>
<p>thus captures the quantum analogue of NP-hardness with beautiful physical intuition.</p>
<p>Kitaev later won the 2023 Breakthrough Prize in Fundamental Physics for this and his work on</p>
<p>topological quantum computing. He is famously reclusive and rarely gives public talks.</p>
</div>

### 3.3.2 Other QMA-Complete Problems

| Problem | Description | Reference |
|---|---|---|
| k-Local Hamiltonian | Ground energy of k-body quantum system | Kitaev 1999 |
| Quantum k-SAT | Frustration-free quantum satisfiability | Bravyi 2006 |
| Density matrix consistency | Local density matrices mutually consistent? | Liu et al. 2006 |
| N-representability | Valid many-body density matrix? | Liu et al. 2007 |
| Separability | Is a given mixed state entangled? | Gharibian 2010 |

## 3.4 QCMA, PP, and the Polynomial Hierarchy

### 3.4.1 QCMA: Classical Witnesses for Quantum Verifiers

QCMA (Quantum Classical Merlin-Arthur) is like QMA, but the proof (witness) is classical rather than quantum. This models the situation where a quantum verifier checks a classically-described solution. It is unknown whether QCMA = QMA — separating them would require showing quantum proofs are strictly more powerful than classical proofs for quantum verification.

**QCMA: quantum verifier + classical witness        QMA: quantum verifier + quantum witness**

### 3.4.2 PP: Unbounded Error Probabilistic Polynomial Time

PP contains all problems solvable by a probabilistic polynomial-time machine where acceptance probability > 1/2 for YES and ≤ 1/2 for NO. Unlike BPP, the gap can be exponentially small. The key fact BQP ⊆ PP is proved by showing any quantum amplitude is computable by a PP machine.

### 3.4.3 The Polynomial Hierarchy and Boson Sampling

The polynomial hierarchy PH = ∪\_k Σ\_k^P generalises P and NP. PH is believed to be infinite — if it collapses to any level, it implies NP = coNP (considered extremely unlikely). Quantum computing is believed NOT to collapse PH: Aaronson and Arkhipov (2011) showed that if quantum computers can be efficiently classically simulated, PH collapses to the third level.

| Class | Informal Description | Relation to BQP |
|---|---|---|
| P | Efficiently decidable | P ⊆ BQP (proven) |
| NP | Efficiently verifiable (classical) | NP ⊆ BQP? Unknown — major open problem |
| BPP | Efficient randomised computation | BPP ⊆ BQP (proven) |
| BQP | Efficient quantum computation | The main class of quantum computing |
| QMA | Quantum NP (quantum witness) | BQP ⊆ QMA (trivially; BQP has trivial proof) |
| QCMA | Classical witness, quantum verifier | BQP ⊆ QCMA ⊆ QMA |
| PP | Unbounded error probabilistic | BQP ⊆ PP (proven) |
| PSPACE | Polynomial space | BQP ⊆ PSPACE (proven) |

## 3.5 Quantum Circuit Complexity and Black Holes

Quantum circuit complexity is the study of the minimum number of gates required to prepare a given quantum state or implement a given unitary transformation. It connects to quantum chaos, cryptography, and — surprisingly — black hole physics.

- polyQC (polynomial quantum circuit class): Unitaries implementable by poly-size quantum circuits — the 'efficient' set.

- Most quantum states are computationally complex: only 2^poly(n) poly-size circuits exist, but 2^(2^n) quantum states.

<div class="box box-anecdote">
<p class="box-title"><strong>📜  Complexity Theory and Black Holes — Susskind &amp; Preskill (2014)</strong></p>
<p>In 2014, Leonard Susskind and John Preskill proposed a deep connection between quantum circuit complexity</p>
<p>and the physics of black holes. Their 'Complexity = Action' and 'Complexity = Volume' conjectures</p>
<p>relate the quantum computational complexity of the boundary state in AdS/CFT holography to the volume</p>
<p>(or action) of the black hole interior.</p>
<p>This suggests that complexity theory is not just about computers — it may be fundamental to spacetime</p>
<p>geometry itself. The conjecture implies that a black hole interior grows in volume at precisely the rate</p>
<p>predicted by increasing computational complexity of the quantum state on the boundary.</p>
<p>This is speculative but has driven enormous interest in quantum complexity from string theorists,</p>
<p>and represents one of the deepest connections between computation and fundamental physics.</p>
</div>

## 3.6 Quantum Query Complexity

Query complexity counts the number of accesses to an oracle (a black-box function f: {0,1}^n → {0,1}) needed to compute some property of f. This model is central to proving quantum speedups because lower bounds ARE provable, unlike circuit complexity lower bounds which remain largely elusive.

### 3.6.1 The Quantum Query Model

In the quantum query model, the algorithm accesses the oracle O\_f via:

**O\_f |i⟩|b⟩ = |i⟩|b ⊕ f(i)⟩   (bit oracle)**

**O\_f^phase |i⟩ = (−1)^f(i) |i⟩   (phase oracle)**

A quantum query algorithm alternates k applications of O\_f with fixed unitaries U₀, U₁, ..., U\_k, then measures. The quantum query complexity Q(f) is the minimum k achieving error ≤ 1/3.

<img class="fig-img" src="content/images/image13.png" alt="figure">

**Figure 2: Quantum Query Algorithm Structure** *— Fixed unitaries U₀,...,U\_k interleaved with k oracle queries O\_f; the minimum k is the quantum query complexity Q(f)*

### 3.6.2 Key Query Complexity Separations

| Problem | D(f) Classical | Q(f) Quantum | Separation Type |
|---|---|---|---|
| Deutsch (1-bit parity) | 2 queries | 1 query | 2× speedup |
| Deutsch-Jozsa (promise parity) | N deterministic / O(1) randomised | 1 query | Exponential over D; 0 over BPP |
| Simon's problem | Ω(2^(n/2)) average | O(n) | Exponential |
| Bernstein-Vazirani (dot product) | N queries | 1 query | N× speedup |
| Grover (unstructured search) | Θ(N) | Θ(√N) | Quadratic (proven optimal) |
| Element distinctness | Θ(N) | Θ(N^(2/3)) | Sub-quadratic (cubic root advantage) |
| OR function (any bit 1?) | N queries | Θ(√N) | Quadratic |

## 3.7 The Polynomial Method

The polynomial method (Beals et al. 1998) is the main tool for proving quantum query lower bounds. Its key insight: any bounded quantum query algorithm computing f(x) can be modelled as a low-degree multivariate polynomial.

<div class="box box-generic">
<p class="box-title"><strong>Theorem 3.6  The Polynomial Method (Beals-Buhrman-Cleve-Mosca-de Wolf 1998)</strong></p>
<p>Let f: {0,1}^N → {0,1}. If a quantum algorithm computes f with error ≤ 1/3 using T queries,</p>
<p>then there exists a real multivariate polynomial p(x₁,...,x_N) of degree ≤ 2T such that:</p>
<p>|p(x) − f(x)| ≤ 1/3   for all x ∈ {0,1}^N</p>
<p>Proof idea: Pr[accept] after T queries is a degree-2T multivariate polynomial in x₁,...,x_N.</p>
<p>Each query contributes at most degree 2 (one from the query, one from the complex conjugate).</p>
<p>Consequence:   Q(f)  ≥  deg̃(f) / 2</p>
<p>where deg̃(f) = minimum degree of any (1/3)-approximating polynomial for f.</p>
</div>

| Function f | Approximate Degree deg̃(f) | Quantum Query Lower Bound |
|---|---|---|
| OR_N (any bit 1?) | Θ(√N) | Ω(√N) → proves Grover is OPTIMAL! |
| AND_N (all bits 1?) | Θ(√N) | Ω(√N) |
| PARITY (XOR of N bits) | N (exact) | Ω(N) — NO quantum speedup possible! |
| MAJORITY (more 1s than 0s?) | Θ(√N log N) to Θ(N) | Ω(N^(1/2)) |
| Collision (f injective?) | Θ(N^(2/3)) | Ω(N^(1/3)) |

<div class="box box-warning">
<p class="box-title"><strong>⚠  Why PARITY Has No Quantum Speedup</strong></p>
<p>The PARITY function (XOR of N bits) has approximate degree exactly N — it requires a polynomial</p>
<p>of degree N to approximate it on {0,1}^N. By the polynomial method: Q(PARITY) = Ω(N).</p>
<p>Classically, D(PARITY) = N as well. So quantum and classical have the SAME query complexity!</p>
<p>This is a remarkable result: some functions offer ZERO quantum speedup.</p>
<p>The key property: PARITY is sensitive to ALL input bits — flipping any single bit changes the output.</p>
<p>This forces a high-degree polynomial approximation, preventing any quantum advantage.</p>
<p>Intuition: Quantum speedup requires exploiting STRUCTURE (periodicity, amplitude interference).</p>
<p>Problems without structure — like PARITY — offer no quantum advantage whatsoever.</p>
</div>

## 3.8 The BBBV Theorem and the Adversary Method

The Bennett-Bernstein-Brassard-Vazirani (BBBV) theorem of 1994 was the first rigorous quantum query lower bound, proving that Grover's O(√N) algorithm is OPTIMAL for unstructured search.

<div class="box box-generic">
<p class="box-title"><strong>Theorem 3.7  BBBV Lower Bound (Bennett-Bernstein-Brassard-Vazirani 1994)</strong></p>
<p>Any quantum algorithm solving the unstructured search problem (OR_N) with success probability ≥ 2/3</p>
<p>must make at least Ω(√N) queries to the oracle.</p>
<p>Proof sketch (quantum progress argument):</p>
<p>Define 'progress' W_t = Σᵢ wᵢ |αᵢᵗ|² where αᵢᵗ is the amplitude on oracle input i.</p>
<p>Key lemma: W_{t+1} − W_t ≤ O(√(W_t)) per query.</p>
<p>Since W₀ = O(1/N) and we need W_T = Ω(1) for success: T = Ω(√N).</p>
<p>Consequence: No clever quantum algorithm can search N items in fewer than Ω(√N) queries.</p>
<p>Grover's algorithm is EXACTLY optimal — not just asymptotically, but tight.</p>
</div>

### 3.8.1 The Quantum Adversary Method

<div class="box box-generic">
<p class="box-title"><strong>Theorem 3.8  Adversary Bound (Ambainis 2002)</strong></p>
<p>Let f: {0,1}^N → {0,1}. Choose X ⊆ f⁻¹(0), Y ⊆ f⁻¹(1), and relation R ⊆ X × Y.</p>
<p>Let mx = |{y : (x,y) ∈ R}|, my = |{x : (x,y) ∈ R}|,</p>
<p>and m_{x,i} = |{y : (x,y) ∈ R, xᵢ ≠ yᵢ}|.</p>
<p>Q(f)  ≥  Ω( √(|R| × mx × my) / max_i √(m_{x,i} × m_{y,i}) )</p>
<p>Applied to OR_N with X = {0^N}, Y = all weight-1 strings, |R| = N:</p>
<p>Q(OR_N) = Ω(√N)  ✓  (matching Grover's bound — adversary is tight!)</p>
<p>The adversary bound often gives tighter bounds than the polynomial method for</p>
<p>functions with 'intermediate' structure (e.g. element distinctness, triangle finding).</p>
</div>

<img class="fig-img" src="content/images/image14.png" alt="figure">

**Figure 3: Quantum Speedup Taxonomy** *— Classification of known quantum speedups from exponential (Shor, Simon) to provably none (PARITY)*

## 3.9 Simon's Problem: The First Exponential Separation

Simon's problem (1994) was the first problem to demonstrate an exponential quantum speedup — predating Shor's algorithm. It directly inspired Shor: when Shor saw Simon's use of the Hadamard transform to detect hidden periodicity, he realised the same idea could break RSA.

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  Simon's Problem Statement</strong></p>
<p>INPUT: Oracle access to f: {0,1}^n → {0,1}^n</p>
<p>PROMISE: Either f is one-to-one (injective), OR ∃ secret s ∈ {0,1}^n, s ≠ 0^n,</p>
<p>such that f(x) = f(y) iff x ⊕ y ∈ {0^n, s}</p>
<p>GOAL: Find s (or determine f is injective)</p>
<p>Classical complexity: Ω(2^(n/2)) queries expected (birthday paradox collision)</p>
<p>Quantum complexity: O(n) queries (Simon's algorithm)</p>
<p>Exponential separation: quantum is exponentially faster than any classical algorithm!</p>
</div>

Simon's algorithm: (1) Prepare |0^n⟩|0^n⟩, apply H^⊗n; (2) Query f; (3) Measure second register → collapse to (|x₀⟩+|x₀⊕s⟩)/√2; (4) Apply H^⊗n → get y with y·s = 0 (mod 2); (5) Repeat O(n) times; (6) Solve linear system by Gaussian elimination to find s.

<div class="box box-anecdote">
<p class="box-title"><strong>📜  Simon Inspires Shor — Two Papers That Changed the World</strong></p>
<p>Peter Shor has publicly stated that Simon's 1994 paper was the direct inspiration for his factoring algorithm.</p>
<p>When Shor saw Simon's use of the Hadamard transform to detect hidden periodicity in {0,1}^n,</p>
<p>he realised the same idea could be applied to Z_N to find the order of an element — which is</p>
<p>exactly what the Quantum Fourier Transform accomplishes in Shor's algorithm.</p>
<p>Simon presented his result at FOCS 1994. Shor presented the factoring algorithm at STOC 1994,</p>
<p>just months later. Two papers that changed the world, separated by weeks.</p>
<p>The hidden subgroup problem (HSP) — generalising both Deutsch-Jozsa, Simon, and Shor — became</p>
<p>one of the central organising frameworks of quantum algorithms research throughout the 1990s and 2000s.</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>📋  Chapter 3 Summary</strong></p>
<p>P:                Polynomial-time decidable; our formal notion of 'efficient computation'</p>
<p>NP:               Polynomial-time verifiable; certificate-based; P vs NP is the $1M question</p>
<p>BPP:              Randomised poly-time; P ⊆ BPP; most experts believe P = BPP</p>
<p>BQP:              Quantum poly-time; P ⊆ BPP ⊆ BQP ⊆ PP ⊆ PSPACE (all proven)</p>
<p>QMA:              Quantum NP; 2-Local Hamiltonian is QMA-complete (Kitaev 1999)</p>
<p>QCMA:             Classical witnesses + quantum verifier; QCMA ⊆ QMA (separation unknown)</p>
<p>Query complexity: Oracle model; Q(f) = min queries; provable lower bounds possible</p>
<p>Polynomial method:Q(f) ≥ deg̃(f)/2; used to prove Grover is optimal</p>
<p>BBBV theorem:     Q(OR_N) = Ω(√N); unstructured search lower bound (1994)</p>
<p>Adversary method: Tighter lower bounds via relation argument (Ambainis 2002)</p>
<p>Simon's problem:  First exponential separation: O(n) quantum vs Ω(2^(n/2)) classical</p>
<p>PARITY:           Q = D = N — no quantum speedup possible (polynomial method proof)</p>
</div>

## RECAP — SHORT ANSWER QUESTIONS & MODEL ANSWERS

Chapter 3: Complexity Classes, Query Complexity & the Polynomial Method

Instructions: Answer each question in 3–6 lines. Each question carries equal marks.

**PART A — QUESTIONS**

**Q1.  Define the classes P, BPP, and BQP, and state their known containment relation.**

**Q2.  What is BQP formally, and why is it the quantum analogue of BPP rather than of P?**

**Q3.  What is QMA, and give an example of a QMA-complete problem.**

**Q4.  How does quantum query complexity differ from classical decision-tree complexity, and what does the polynomial method provide?**

**Q5.  State the BBBV theorem and its significance for Grover's algorithm.**

**Q6.  What is the quantum adversary method, and how does it complement the polynomial method for proving lower bounds?**

**Q7.  Describe Simon's problem and why it represents the first exponential separation between quantum and classical query complexity.**

**Q8.  What is QCMA, and how does it differ from QMA?**

**Q9.  Summarise the quantum speedup taxonomy discussed in this chapter.**

**Q10.  What does 'PSPACE' mean, and where does BQP sit relative to it?**

**Q11.  Explain what it means to say P vs NP is 'the most famous unsolved problem in computer science', and how BQP relates to it.**

**Q12.  What role did Cook's 1971 paper play in the history of complexity theory, referenced as background to this chapter?**

**PART B — MODEL ANSWERS**

**Answer 1:**

P is the class of decision problems solvable in deterministic polynomial time; BPP allows a classical probabilistic (randomised) polynomial-time algorithm with bounded two-sided error; BQP allows a polynomial-time quantum algorithm with bounded error. The known containment is P ⊆ BPP ⊆ BQP ⊆ PSPACE, with BPP ⊆ BQP believed (but not proven) to be strict, reflecting the conjecture that quantum computers are strictly more powerful than classical randomised ones for some problems.

**Answer 2:**

BQP (Bounded-error Quantum Polynomial time) is the class of decision problems solvable by a polynomial-size quantum circuit that outputs the correct answer with probability at least 2/3. Because quantum measurement is inherently probabilistic, BQP algorithms are error-bounded rather than exact, exactly paralleling BPP's bounded-error classical randomised model — hence BQP is considered the natural quantum counterpart of BPP, not of the exact/deterministic class P.

**Answer 3:**

QMA (Quantum Merlin-Arthur) is the quantum analogue of NP: a language is in QMA if a quantum 'proof' (witness) state, when verified by a polynomial-time quantum circuit, is accepted with high probability for yes-instances and rejected with high probability for all witnesses on no-instances. The k-local Hamiltonian problem — determining whether the ground-state energy of a Hamiltonian expressed as a sum of k-local terms is below or above a threshold — is QMA-complete, playing a role analogous to SAT's NP-completeness.

**Answer 4:**

Classical decision-tree (query) complexity counts the number of oracle queries a classical algorithm needs to determine a Boolean function's value; quantum query complexity counts queries to a quantum oracle, allowing superposition queries. The polynomial method shows that any bounded-error quantum query algorithm making T queries corresponds to a real polynomial of degree O(T) approximating the target function, so proving a lower bound on the approximate polynomial degree of a function yields a matching lower bound on its quantum query complexity.

**Answer 5:**

The BBBV theorem (Bennett, Bernstein, Brassard, Vazirani) proves that any quantum algorithm solving unstructured search over N items requires Ω(√N) oracle queries, establishing that Grover's O(√N) algorithm is asymptotically optimal — no quantum algorithm can search faster than quadratically better than the classical O(N). It is proved via a 'hybrid argument' bounding how much a small number of queries can change the amplitude on any single marked item.

**Answer 6:**

The adversary method proves query lower bounds by constructing a weighted relation between 'hard-to-distinguish' input pairs and tracking how slowly a quantum algorithm's amplitude can distinguish them per query, giving a bound based on this combinatorial/spectral quantity rather than polynomial degree. It often gives tight bounds where the polynomial method is looser (and vice versa), and the two methods together cover essentially all known quantum query lower bound techniques taught in this course.

**Answer 7:**

Simon's problem asks to find a hidden 'period' string s such that f(x) = f(x⊕s) for a given 2-to-1 function f. A quantum algorithm solves it with O(n) queries using QFT-based period extraction, whereas any classical (even randomised) algorithm requires Ω(2^{n/2}) queries due to the birthday-paradox-like structure of the collision search. This was the first known example of an exponential quantum-classical query complexity gap, and it directly inspired Shor's algorithm.

**Answer 8:**

QCMA (Quantum Classical Merlin Arthur) requires the untrusted witness/proof to be a classical string, while the verification circuit is quantum; QMA allows the witness itself to be an arbitrary quantum state. QCMA ⊆ QMA is known, but whether QCMA = QMA is an open problem — intuitively, quantum witnesses could in principle encode more 'proof power' per qubit than classical witnesses, though no problem is currently known to separate the two classes.

**Answer 9:**

Quantum algorithms are classified by the type of speedup they provide over the best known classical algorithm: exponential speedups (e.g. Shor's factoring, discrete log), quadratic speedups (e.g. Grover's search, amplitude estimation), polynomial (sub-quadratic but not quadratic) speedups in specialised query models, and heuristic speedups observed empirically for certain variational or optimisation problems without a proven asymptotic advantage.

**Answer 10:**

PSPACE is the class of problems solvable using polynomial space (memory) regardless of the time taken. It is known that BQP ⊆ PSPACE — quantum computations can be simulated (albeit exponentially slowly in time) using only polynomial memory, because a quantum state on n qubits can be represented and manipulated using space polynomial in n even though the state vector itself has exponentially many amplitudes, via careful recursive simulation techniques.

**Answer 11:**

P vs NP asks whether every problem whose solution can be efficiently verified (NP) can also be efficiently solved (P); it remains unproven despite being a Clay Millennium Prize problem. BQP's relationship to NP is a separate open question from P vs NP: quantum computers are believed to give speedups for certain structured problems (factoring) but are not believed to solve NP-complete problems (like 3-SAT) in polynomial time, i.e. NP ⊄ BQP is conjectured, so quantum computing is not expected to resolve P vs NP.

**Answer 12:**

Stephen Cook's 1971 paper 'The Complexity of Theorem Proving Procedures' introduced the concept of NP-completeness and proved that Boolean satisfiability (SAT) is NP-complete, i.e. every problem in NP can be reduced to it in polynomial time. This founded the field of NP-completeness theory (extended by Karp's 1972 list of 21 NP-complete problems) and provides the classical backdrop against which quantum complexity classes like BQP and QMA are defined and compared.

## A. Solved Problems

<div class="box box-generic">
<p class="box-title"><strong>Solved Example 1  Verifying BPP ⊆ BQP</strong></p>
<p>Problem: Show that any BPP algorithm can be simulated by a BQP algorithm.</p>
<p>Proof: A BPP algorithm uses random bits r to compute f(x, r). A quantum algorithm can:</p>
<p>1. Prepare |0^n⟩ and apply H^⊗n to create uniform superposition over all random strings r:</p>
<p>(1/√(2^n)) Σ_r |r⟩</p>
<p>2. Coherently compute the BPP algorithm: (1/√(2^n)) Σ_r |r⟩|f(x,r)⟩</p>
<p>3. Measure the second register.</p>
<p>The measurement outcome f(x,r) is distributed exactly as the BPP algorithm's output.</p>
<p>Therefore the quantum circuit simulates the BPP algorithm with the same error probability.</p>
<p>The circuit uses O(n) qubits for the random bits and poly(n) gates.  ∴ BPP ⊆ BQP.  □</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Solved Example 2  Polynomial Method: Lower Bound for AND₄</strong></p>
<p>Problem: Use the polynomial method to prove Q(AND₄) = Ω(2).</p>
<p>AND₄(x₁,x₂,x₃,x₄) = x₁ · x₂ · x₃ · x₄  (1 only if all bits are 1)</p>
<p>Step 1: Find the minimum degree for the univariate symmetric approximation.</p>
<p>The symmetric version AND_N(k) where k = Σxᵢ:</p>
<p>AND_N(k) = 1 iff k = N. For N=4 this is 1 only when k=4.</p>
<p>Step 2: The univariate polynomial approximating AND_N must have degree Ω(√N).</p>
<p>For N=4: deg̃(AND₄) ≥ 2.</p>
<p>Step 3: By the polynomial method: Q(AND₄) ≥ deg̃(AND₄)/2 ≥ 1.</p>
<p>Tighter analysis gives Q(AND₄) ≥ 2, matching the OR₄ lower bound.</p>
<p>Note: Q(AND₄) = Q(OR₄) = Θ(√4) = 2 — AND and OR have the same complexity by De Morgan's law.</p>
<p>This exemplifies the power of the polynomial method for proving exact quantum lower bounds.</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Solved Example 3  Classifying Integer Factoring into Complexity Classes</strong></p>
<p>Problem: Classify integer factoring into its appropriate complexity classes.</p>
<p>Factoring Decision Problem: Given N and k, does N have a factor ≤ k?</p>
<p>(a) Is factoring in NP?</p>
<p>YES: Certificate = the factor p ≤ k. Verify: p divides N (O(log N) time). ✓</p>
<p>(b) Is factoring in co-NP?</p>
<p>YES: For NO instances, certificate = primality proofs for all prime factors &gt; k.</p>
<p>(Pratt certificates exist in polynomial length). ∴ Factoring ∈ NP ∩ co-NP.</p>
<p>(c) Is factoring in P?</p>
<p>UNKNOWN. No polynomial classical algorithm known. Generally believed NOT in P.</p>
<p>(d) Is factoring in BQP?</p>
<p>YES: Shor's algorithm runs in O((log N)³) quantum gates. ∴ Factoring ∈ BQP. ✓</p>
<p>(e) Is factoring NP-complete?</p>
<p>Believed NO: If factoring were NP-complete, then NP = co-NP (since factoring ∈ co-NP),</p>
<p>which would collapse the polynomial hierarchy — considered extremely unlikely by experts.</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Solved Example 4  Adversary Bound for PARITY_N</strong></p>
<p>Problem: Apply the adversary method to verify Q(PARITY_N) = Ω(N).</p>
<p>Adversary construction:</p>
<p>• X = all even-weight strings (PARITY = 0), starting with x = 0^N</p>
<p>• Y = all weight-1 strings eᵢ (PARITY = 1)</p>
<p>• R: (x, y) ∈ R iff y = eᵢ (the unique bit where they differ from 0^N)</p>
<p>For x = 0^N: mx = N (related to all N weight-1 strings)</p>
<p>For y = eᵢ: my = 1 (only 0^N is related to eᵢ)</p>
<p>Adversary bound: Q ≥ √(|R| × mx × my) / max √(m_{x,i} × m_{y,i})</p>
<p>= √(N × N × 1) / √(1 × 1) = N</p>
<p>Therefore Q(PARITY_N) = Ω(N). Since D(PARITY_N) = N ≥ Q(PARITY_N) ≥ N:</p>
<p>Q(PARITY_N) = Θ(N) — no quantum speedup for parity. ✓</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Solved Example 5  Simon's Algorithm Step-by-Step (n=2, s=10)</strong></p>
<p>Problem: Trace Simon's algorithm for n=2, secret s=10.</p>
<p>Oracle: f(00)=f(10)=a,  f(01)=f(11)=b  (for some a≠b)</p>
<p>Step 1: |00⟩|00⟩  →  H⊗H⊗I⊗I  →  (1/2)(|00⟩+|01⟩+|10⟩+|11⟩)|00⟩</p>
<p>Step 2: Apply oracle:</p>
<p>= (1/2)(|00⟩|a⟩ + |01⟩|b⟩ + |10⟩|a⟩ + |11⟩|b⟩)</p>
<p>Step 3: Measure second register. Suppose outcome = |a⟩.</p>
<p>First register collapses to: (1/√2)(|00⟩ + |10⟩)  [the two inputs mapping to a]</p>
<p>Step 4: Apply H⊗H to first register:</p>
<p>H⊗H applied to (1/√2)(|00⟩+|10⟩):</p>
<p>= (1/2√2)[(|0⟩+|1⟩)⊗(|0⟩+|1⟩) + (|0⟩−|1⟩)⊗(|0⟩+|1⟩)]</p>
<p>= (1/√2)(|00⟩ + |01⟩)  → peaks at y with y·s=0 (mod 2)</p>
<p>Step 5: Measure → get y ∈ {00, 01}. Check: 00·10=0 ✓, 01·10=0 ✓</p>
<p>Run multiple times; solve system of equations; find s=10. ✓</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Solved Example 7  QMA Witness Verification for 2-Local Hamiltonian</strong></p>
<p>Problem: Describe how a quantum verifier checks a QMA witness for the 2-Local Hamiltonian problem.</p>
<p>H = Σᵢ Hᵢ (each Hᵢ on 2 qubits). Claim: λ₀(H) ≤ a (ground state energy ≤ a).</p>
<p>The QMA witness is a quantum state |ψ⟩ purporting to be the ground state.</p>
<p>Verification protocol:</p>
<p>1. Receive quantum state |ψ⟩ from Merlin (the prover)</p>
<p>2. Choose a random term Hᵢ from the Hamiltonian</p>
<p>3. Measure ⟨ψ|Hᵢ|ψ⟩ by applying Hᵢ on the relevant qubits and measuring in eigenbasis</p>
<p>4. ACCEPT if sampled energy ≤ (a+b)/2; REJECT otherwise</p>
<p>Analysis:</p>
<p>• If λ₀ ≤ a: Merlin sends the true ground state; ⟨H⟩ ≤ a; verifier accepts w.p. ≥ 2/3</p>
<p>• If λ₀ ≥ b: For any |ψ⟩, ⟨ψ|H|ψ⟩ ≥ b; verifier rejects w.p. ≥ 2/3</p>
<p>• The gap b−a ≥ 1/poly(n) ensures verifier can distinguish the two cases.</p>
<p>• This is the quantum analogue of NP verification — proof is |ψ⟩, not a bit string.</p>
</div>

## B. Unsolved Problems

## Chapter 3: Problems

**1.** Show that f(x) = x₁ AND x₂ AND x₃ AND x₄ is in NP. Write the certificate and verifier.  *[Ans: Certificate = assignment with all bits 1; Verifier checks all 4 bits in O(1) time ✓]*

**2.** Verify that H = (Z₁Z₂+Z₂Z₃+Z₁Z₃)/3 has minimum energy −1/3 for n=3 qubits. What is the ground state?  *[Ans: Try |010⟩: ⟨Z₁Z₂⟩=−1, ⟨Z₂Z₃⟩=−1, ⟨Z₁Z₃⟩=+1 → E=(−1−1+1)/3=−1/3; ground state is any odd-weight antiferromagnetic assignment]*

**3.** Apply the polynomial method to prove MAJORITY₃ requires Q ≥ 2.  *[Ans: p(x₁,x₂,x₃) = x₁x₂+x₂x₃+x₁x₃−2x₁x₂x₃; degree=3; Q ≥ 3/2 → Q ≥ 2]*

**4.** Prove Deutsch-Jozsa is in BQP but not efficiently separable from BPP. What does this say about BQP vs BPP?  *[Ans: Quantum: 1 query; Classical randomised: O(1) (with high prob.); Deterministic: N queries. Separation is over D, not BPP — no separation over BPP demonstrated]*

**5.** Show Q(OR₂) ≥ √2 ≈ 1.41 by the adversary method, then verify with a direct 1-query impossibility argument.  *[Ans: Adversary: X={00}, Y={01,10}, |R|=2, mx=2, my=1, m\_{x,i}=1; Q≥√2; Direct: 1-query circuit cannot distinguish 3 inputs {00,01,10} with success >2/3 by inner-product argument]*

**6.** Classify each into P, NP, BQP, QMA: (a) primality, (b) graph isomorphism, (c) 3-SAT, (d) factoring, (e) k-LH.  *[Ans: (a) P (AKS); (b) NP∩co-AM, not known complete; (c) NP-complete; (d) NP∩co-NP∩BQP; (e) QMA-complete]*

**7.** For Simon's problem with n=3 and s=011, how many independent linear equations y·s=0 are needed?  *[Ans: n−1=2 independent equations needed; the non-trivial solution space is 2-dimensional]*

**8.** Prove BQP is closed under complement (if L ∈ BQP then L̄ ∈ BQP).  *[Ans: Complement: swap ACCEPT and REJECT in the circuit; the error probability and runtime are unchanged]*

**9.** State the quantum query complexity of the NAND tree on N=2^k leaves. Why is this significant?  *[Ans: Q(NAND\_N) = O(N^(log\_4 3)) = O(N^0.793); significant because it provides a super-quadratic speedup not from Grover-type search]*

**10.** Show Q(f) ≤ D(f) for any total Boolean function f.  *[Ans: Any deterministic algorithm is a valid (degenerate) quantum algorithm — classical D(f) queries are a valid quantum strategy using computational basis states]*

## C. Multiple Choice Questions

## Chapter 3 MCQs

<div class="box box-generic">
<p class="box-title"><strong>Q1.  The class BQP is known to satisfy which containment?</strong></p>
<p>(A)  BPP ⊆ BQP ⊆ PSPACE</p>
<p>(B)  NP ⊆ BQP ⊆ EXP</p>
<p>(C)  P = BQP</p>
<p>(D)  BQP ⊆ NP</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q2.  Which problem is QMA-complete?</strong></p>
<p>(A)  3-SAT</p>
<p>(B)  Graph Colouring</p>
<p>(C)  k-Local Hamiltonian</p>
<p>(D)  Integer Factoring</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q3.  The polynomial method proves Q(OR_N) = Ω(√N) because:</strong></p>
<p>(A)  OR_N is NP-complete</p>
<p>(B)  Any approximating polynomial for OR_N has degree Ω(√N)</p>
<p>(C)  OR reduces to factoring</p>
<p>(D)  The oracle requires Ω(N) classical queries</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q4.  The BBBV theorem proves that:</strong></p>
<p>(A)  Grover's algorithm is suboptimal</p>
<p>(B)  Any quantum search algorithm needs Ω(√N) queries</p>
<p>(C)  BQP = BPP</p>
<p>(D)  PARITY has no quantum speedup</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q5.  The approximate degree deg̃(PARITY_N) equals:</strong></p>
<p>(A)  1</p>
<p>(B)  √N</p>
<p>(C)  N/2</p>
<p>(D)  N</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q6.  Simon's problem demonstrates a quantum speedup over classical of:</strong></p>
<p>(A)  Quadratic</p>
<p>(B)  Polynomial</p>
<p>(C)  Exponential</p>
<p>(D)  No speedup</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q7.  QMA is to NP as BQP is to:</strong></p>
<p>(A)  P</p>
<p>(B)  BPP</p>
<p>(C)  PP</p>
<p>(D)  NP</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q8.  The adversary bound Q(f) ≥ Adv(f) applied to OR_N gives:</strong></p>
<p>(A)  O(1)</p>
<p>(B)  O(log N)</p>
<p>(C)  Ω(√N)</p>
<p>(D)  Ω(N)</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q9.  The Deutsch-Jozsa problem gives exponential speedup over which complexity measure?</strong></p>
<p>(A)  BPP (randomised)</p>
<p>(B)  BQP itself</p>
<p>(C)  Deterministic classical D(f)</p>
<p>(D)  NP</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q10.  Which of these containments remains UNPROVEN?</strong></p>
<p>(A)  P ⊆ BPP</p>
<p>(B)  BPP ⊆ BQP</p>
<p>(C)  BQP ⊆ PSPACE</p>
<p>(D)  NP ⊆ BQP</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q11.  The k-Local Hamiltonian QMA-completeness was proved by:</strong></p>
<p>(A)  Peter Shor (1994)</p>
<p>(B)  Alexei Kitaev (1999)</p>
<p>(C)  Scott Aaronson (2004)</p>
<p>(D)  Umesh Vazirani (2001)</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q12.  In the quantum query model, a query algorithm alternates O_f with:</strong></p>
<p>(A)  Classical computations</p>
<p>(B)  Fixed unitary transformations</p>
<p>(C)  Measurements</p>
<p>(D)  Phase kickback circuits</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q13.  Why can't Grover's algorithm solve NP in polynomial quantum time?</strong></p>
<p>(A)  Grover requires the solution to be marked beforehand</p>
<p>(B)  Grover's O(√(2^n)) = O(2^(n/2)) is still exponential in n</p>
<p>(C)  Grover cannot handle Boolean functions</p>
<p>(D)  SAT is not in NP</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q14.  Bernstein-Vazirani problem achieves exactly:</strong></p>
<p>(A)  Quadratic speedup</p>
<p>(B)  1 quantum query vs N classical queries (N× speedup)</p>
<p>(C)  Exponential speedup</p>
<p>(D)  No speedup</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q15.  PH collapsing to the third level would be implied by:</strong></p>
<p>(A)  Proving P = NP</p>
<p>(B)  Factoring having a classical poly algorithm</p>
<p>(C)  An efficient classical simulation of boson sampling</p>
<p>(D)  BQP ⊆ BPP</p>
</div>

## D. Theory Questions

## Chapter 3: Theory Questions

**Q1.** State and prove the inclusion P ⊆ BPP ⊆ BQP ⊆ PSPACE. For the BQP ⊆ PSPACE step, outline how quantum computations can be simulated with polynomial space.

**Q2.** Define QMA and explain the k-Local Hamiltonian problem in detail. Why is the quantum proof (witness) state |ψ⟩ more powerful than a classical certificate?

**Q3.** Explain the quantum query model (oracle model) and why it is used to prove quantum lower bounds when circuit complexity lower bounds are out of reach.

**Q4.** State the polynomial method theorem (Beals et al. 1998). Explain why the acceptance probability of a T-query quantum algorithm is a degree-2T polynomial in the input bits.

**Q5.** Prove that Q(PARITY\_N) = Ω(N) using either the polynomial method or the adversary bound. What does this imply about the class of problems that quantum computers cannot speed up?

**Q6.** Describe Simon's algorithm. Why does it achieve an exponential speedup over classical algorithms, and how does it differ structurally from Shor's algorithm?

**Q7.** What is the polynomial hierarchy (PH)? Why would PH collapsing be considered catastrophic, and how does this connect to the hardness of boson sampling and RCS?

**Q8.** Explain the difference between QMA and QCMA. Give an example of a problem in QMA and discuss whether quantum proofs are provably more useful than classical proofs.

**Q9.** Explain the 'Complexity = Volume' conjecture and its connection to black hole physics. What does it say about the relevance of quantum complexity theory beyond computing?

**Q10.** Describe the quantum speedup taxonomy: exponential, quadratic, polynomial, and no speedup. Give one example of each and explain what structural feature enables (or prevents) the speedup.

## E. Programming / Research Assignments

<div class="box box-real-world">
<p class="box-title"><strong>🌐  Assignment 1: Complexity Classification Project</strong></p>
<p>For each of the following 10 problems, classify into ALL applicable complexity classes</p>
<p>(P, NP, co-NP, BPP, BQP, QMA, QCMA, PSPACE, NP-complete, QMA-complete):</p>
<p>1. Graph isomorphism              2. Primality testing (AKS)</p>
<p>3. 5-Local Hamiltonian            4. Discrete logarithm</p>
<p>5. Linear programming             6. Matrix rank computation</p>
<p>7. Simon's problem                8. N-representability</p>
<p>9. Circuit SAT                    10. Boson sampling (decision version)</p>
<p>For each: (a) state the complexity classes, (b) justify in one line each,</p>
<p>(c) note any open questions (e.g. 'Unknown: is X in P?')</p>
<p>Submit as a structured table. Due: one week from session.</p>
</div>

## F. Project Suggestions

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  Project 1: Quantum Complexity Class Explorer (Interactive Tool)</strong></p>
<p>Build an interactive web tool or Python application that:</p>
<p>• Visualises the quantum complexity class hierarchy (P, BPP, BQP, NP, QMA, PP, PSPACE)</p>
<p>• For each class: shows definition, key examples, known containments, open questions</p>
<p>• Implements an 'Oracle Construction' module: given a function f (n ≤ 5 bits),</p>
<p>compute D(f), Q(f) numerically by exhaustive search and adversary method</p>
<p>• Computes the approximate degree of f and verifies the polynomial method bound</p>
<p>Deliverable: Working Python application + 3-page technical report.</p>
</div>

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  Project 3: The Polynomial Method — Hands-On Investigation</strong></p>
<p>Deep dive into the polynomial method for quantum lower bounds:</p>
<p>• Implement the polynomial method computationally: for small Boolean functions f on n ≤ 4 bits,</p>
<p>use scipy.optimize to find the minimum-degree (1/3)-approximating polynomial</p>
<p>• Verify deg̃(OR_N) = Θ(√N) for N = 1, 2, 4, 8, 16</p>
<p>• Verify deg̃(PARITY_N) = N for N = 2, 4, 8</p>
<p>• Explore AND-OR trees: verify deg̃(AND₂(OR₂)) matches theory</p>
<p>• Computationally verify (or disprove) that deg̃(MAJORITY₅) = 3</p>
<p>• Write a 5-page mathematical report summarising findings and proofs</p>
<p>Extension: Implement the adversary method for OR, AND, PARITY and verify bounds match.</p>
</div>

## References and Further Reading — Chapter 3

## Chapter 3 References

- Beals, R., Buhrman, H., Cleve, R., Mosca, M. & de Wolf, R. (1998). Quantum lower bounds by polynomials. Proceedings 39th FOCS, pp. 352–361.

- Bennett, C.H., Bernstein, E., Brassard, G. & Vazirani, U. (1997). Strengths and Weaknesses of Quantum Computing. SIAM Journal on Computing, 26(5), 1510–1523.

- Ambainis, A. (2002). Quantum lower bounds by quantum arguments. Journal of CSS, 64(4), 750–767.

- Kitaev, A.Yu. (1999). Quantum NP. Talk at AQIP. Formal version: Kitaev, Shen & Vyalyi, Classical and Quantum Computation. AMS, 2002.

- Kempe, J., Kitaev, A. & Regev, O. (2006). The complexity of the Local Hamiltonian Problem. SIAM Journal on Computing, 35(5), 1070–1097.

- Simon, D.R. (1997). On the Power of Quantum Computation. SIAM Journal on Computing, 26(5), 1474–1483.

- Watrous, J. (2009). Quantum Computational Complexity. Encyclopedia of Complexity and Systems Science. Springer.

- Aaronson, S. (2013). Quantum Computing since Democritus. Cambridge University Press. Chapters 11–15.
