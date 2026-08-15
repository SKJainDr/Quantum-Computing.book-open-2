# CHAPTER 6

# Surface Codes, Threshold Theorem & Fault-Tolerant Architecture

*Topological Codes  |  Threshold Theorem  |  Magic State Distillation  |  Resource Estimates  |  IBM 2023*

<div class="box box-anecdote">
<p class="box-title"><strong>📜  Chapter Epigraph — Austin Fowler, UCSB, 2012</strong></p>
<p>"The surface code is the most promising path to fault-tolerant quantum computing.</p>
<p>It requires only nearest-neighbour interactions on a 2D lattice, has a threshold of about 1%,</p>
<p>and its logical error rate can be made exponentially small by increasing the code distance."</p>
<p>— Austin Fowler, Martinis Group, UCSB, 2012</p>
</div>

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  Chapter 6 Learning Objectives</strong></p>
<p>After studying this chapter, you will be able to:</p>
<p>•  Describe the surface code on a 2D qubit lattice: data qubits, X-checks, Z-checks</p>
<p>•  Explain the topological interpretation: logical qubit as a non-contractible loop</p>
<p>•  State and interpret the threshold theorem for fault-tolerant quantum computing</p>
<p>•  Calculate the logical error rate p_L ≈ A(p/p_th)^⌈(d+1)/2⌉</p>
<p>•  Explain magic state distillation and why it is needed for universal fault-tolerant computation</p>
<p>•  Estimate the physical qubit overhead for factoring RSA-2048 with fault tolerance</p>
<p>•  Describe IBM's 2023 surface code experiment demonstrating error correction benefit</p>
</div>

## 6.1 From Stabiliser Codes to Topological Codes

The codes in Chapter 5 (Shor, Steane) require all-to-all connectivity — qubits in the code interact with arbitrary other qubits. This is physically unrealistic for hardware, where qubits can only interact with nearest neighbours. Topological codes solve this by placing qubits on a 2D lattice, requiring only local interactions.

<div class="box box-anecdote">
<p class="box-title"><strong>📜  Kitaev's Toric Code and the Discovery of Topological Order (1997)</strong></p>
<p>In 1997, Alexei Kitaev introduced the toric code — a quantum error-correcting code defined on a torus</p>
<p>(a square lattice with periodic boundary conditions). His profound insight: the code's logical information</p>
<p>was TOPOLOGICAL — encoded in the non-contractible loops of the torus, making it immune to any local perturbation.</p>
<p>This connected quantum error correction to topology and condensed matter physics — specifically to topological</p>
<p>order and anyons. The toric code is mathematically equivalent to Z₂ gauge theory on the lattice.</p>
<p>Physical errors create pairs of anyonic excitations; correcting them means fusing the anyons back together</p>
<p>before they wander far enough apart to corrupt the logical information.</p>
<p>Kitaev's toric code became the foundational model for ALL modern surface codes. He later won the 2023</p>
<p>Breakthrough Prize in Fundamental Physics for this work and his topological quantum computing proposals.</p>
</div>

## 6.2 The Surface Code

The surface code is the leading candidate for fault-tolerant quantum computing in the near term. It is a topological stabiliser code defined on a 2D square lattice with OPEN boundary conditions (unlike Kitaev's toric code on a torus), making it physically realisable without requiring periodic boundaries.

### 6.2.1 Lattice Structure and Qubit Count

A distance-d surface code is defined on a (2d−1) × (2d−1) effective lattice containing:

- d² data qubits (on a d × d sub-lattice)

- X-type check qubits: plaquette operators measuring X⊗X⊗X⊗X on 4 neighbours

- Z-type check qubits: vertex operators measuring Z⊗Z⊗Z⊗Z on 4 neighbours

- Total physical qubits: 2d² − 1  (for 1 logical qubit)

<img class="fig-img" src="content/images/image19.png" alt="figure">

**Figure 4: Surface Code d=3: 2D Qubit Lattice** *— 9 data qubits (blue circles), 4 X-plaquette checks (blue diamonds), 4 Z-vertex checks (orange diamonds); 17 physical qubits total encode 1 logical qubit with distance d=3*

### 6.2.2 Stabiliser Generators

- Plaquette (X-type) stabiliser: A\_p = ⊗\_{q ∈ ∂p} X\_q — product of X on all qubits around a plaquette.

- Vertex (Z-type) stabiliser: B\_v = ⊗\_{q ∈ ∂v} Z\_q — product of Z on all qubits around a vertex.

### 6.2.3 Logical Operators — Topological Strings

The logical operators X\_L and Z\_L of the surface code are STRING operators — connected chains of qubits spanning the lattice from one boundary to the opposite:

- X\_L: A horizontal chain of X operators from left boundary to right boundary

- Z\_L: A vertical chain of Z operators from top boundary to bottom boundary

The code distance d is the minimum length of such a string — for a d × d lattice, exactly d, confirming the code distance.

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  Topological Interpretation: Anyons and Error Correction</strong></p>
<p>The surface code stores logical information in the global topology of error strings.</p>
<p>A physical error creates a pair of syndrome defects ('anyons') at its endpoints.</p>
<p>As errors accumulate, defect pairs may separate further and further apart.</p>
<p>Decoding = Minimum Weight Perfect Matching (MWPM): pair up detected defects in the</p>
<p>most likely way and apply corrections. A logical error occurs only if the matching</p>
<p>incorrectly assigns defects and a non-contractible string (length ≥ d) forms.</p>
<p>Key intuition: A chain of d errors is needed to create a logical error.</p>
<p>The probability of d errors scales as p^d — exponentially small for p &lt; p_threshold.</p>
</div>

## 6.3 Syndrome Extraction and Decoding

In practice, syndrome measurement is itself noisy. The surface code syndrome extraction circuit uses ancilla qubits that interact with 4 neighbouring data qubits via CNOT gates, then measures the ancilla to obtain the syndrome bit.

<img class="fig-img" src="content/images/image20.png" alt="figure">

**Figure 5: X-Plaquette Syndrome Extraction Circuit** *— 4 CNOT gates + 1 ancilla qubit measure the X-plaquette operator; +1 outcome = no error, −1 = syndrome detected*

### 6.3.1 Minimum Weight Perfect Matching (MWPM) Decoder

- Collect all syndrome defects (check qubits where stabiliser measurement = −1)

- Construct a complete graph with defects as nodes; edge weight = distance between defects

- Find the minimum-weight perfect matching using Edmonds' blossom algorithm

- For each matched pair, apply correction along the shortest path between them

MWPM runs in time O(n³) classically and must operate faster than the physical gate time (~1 μs for superconducting qubits). Modern decoders use neural networks, Union-Find algorithms (nearly linear time), and hardware-accelerated FPGA implementations.

## 6.4 The Threshold Theorem: The Bedrock of Fault-Tolerant Computing

The threshold theorem is arguably the most important theoretical result in quantum computing. It proves that quantum error correction works: if the physical error rate is below a critical threshold, increasing the code distance exponentially suppresses the logical error rate.

<div class="box box-generic">
<p class="box-title"><strong>Theorem 6.1  The Threshold Theorem (Aharonov-Ben-Or; Kitaev; Knill-Laflamme-Zurek — 1996-1997)</strong></p>
<p>There exists a constant threshold error rate p_th &gt; 0 such that if the physical gate error</p>
<p>rate p &lt; p_th, then a quantum circuit of size L can be executed to arbitrary precision</p>
<p>using only poly-log(L) overhead in qubits and gates.</p>
<p>For the surface code specifically:</p>
<p>p_L  ≈  A × (p / p_th)^⌈(d+1)/2⌉</p>
<p>where A ≈ 0.1,  p_th ≈ 1%  (depolarising noise + MWPM decoding),</p>
<p>and d is the code distance.</p>
<p>Physical interpretation:</p>
<p>• For p &lt; p_th: increasing d EXPONENTIALLY suppresses p_L</p>
<p>• For p &gt; p_th: increasing d makes p_L WORSE (larger codes accumulate more errors)</p>
<p>• The threshold is the dividing line between hopeless and achievable fault tolerance.</p>
</div>

<div class="box box-anecdote">
<p class="box-title"><strong>📜  The Threshold Debate of 1996 — Landauer, Unruh, and the Sceptics</strong></p>
<p>The threshold theorem was proved almost simultaneously by three groups in 1996:</p>
<p>Aharonov and Ben-Or; Kitaev; and Knill, Laflamme, and Zurek.</p>
<p>Before these proofs, many physicists — including Rolf Landauer and Bill Unruh — had argued that the</p>
<p>continuous nature of quantum errors made error correction physically impossible. Unruh wrote a widely-cited</p>
<p>paper titled 'Maintaining coherence in quantum computers' concluding that decoherence would always win.</p>
<p>The threshold theorem silenced these objections: it proved constructively that for sufficiently low physical</p>
<p>error rates, quantum computation can proceed indefinitely with only polynomial overhead.</p>
<p>For the surface code, that threshold is p_th ≈ 1%. Current superconducting processors (IBM Heron 2024,</p>
<p>Google Willow 2024) achieve two-qubit error rates of 0.1–0.3% — potentially below threshold.</p>
<p>IBM's 2023 Nature paper was the first experimental evidence of the threshold theorem in action.</p>
</div>

| Code | Noise Model | Threshold p_th | Notes |
|---|---|---|---|
| Concatenated [[7,1,3]] Steane | Depolarising | ~0.01% (10⁻⁴) | Requires long-range connectivity |
| Surface code | Depolarising + MWPM | ~1.0% | Nearest-neighbour; leading candidate |
| Surface code | Circuit-level noise | ~0.5–0.75% | Realistic incl. syndrome extraction errors |
| Toric code | Independent X/Z | ~11% (theoretical) | Optimal but unachievable in practice |
| Color code (2D) | Depolarising | ~0.1% | Has transversal T gate; lower threshold |

<img class="fig-img" src="content/images/image21.png" alt="figure">

**Figure 6: Surface Code: Logical vs Physical Error Rate** *— Below threshold p\_th≈1%, increasing distance d exponentially suppresses p\_L; above threshold, larger codes make things worse*

## 6.5 Magic State Distillation: The T Gate Problem

As established in Chapter 5, the Clifford group (H, S, CNOT) is not computationally universal. Universal quantum computation requires a non-Clifford gate — typically the T gate — which cannot be implemented transversally on any stabiliser code (Eastin-Knill theorem).

**T  =  |0⟩⟨0| + e^(iπ/4)|1⟩⟨1|  =  diag(1, e^(iπ/4))**

### 6.5.1 Magic States and Gate Teleportation

A magic state |T⟩ = T|+⟩ = (|0⟩ + e^(iπ/4)|1⟩)/√2 enables fault-tolerant T gates via gate teleportation using only Clifford operations:

- Prepare |T⟩ ⊗ |ψ⟩ (magic state ⊗ target qubit)

- Apply CNOT from |ψ⟩ to |T⟩

- Measure |T⟩ in the X basis

- Apply classically-controlled S correction if measurement = −1

The magic state is CONSUMED in the process. To perform M T gates, we need M magic states. Since physical T gates are noisy, we must PURIFY noisy magic states — this is magic state distillation.

### 6.5.2 The Bravyi-Kitaev 15-to-1 Distillation Protocol

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  Bravyi-Kitaev 15-to-1 Distillation Protocol (2005)</strong></p>
<p>Input:   15 noisy magic states with fidelity F = 1 − ε</p>
<p>Output:  1 high-fidelity magic state with fidelity 1 − 35ε³</p>
<p>The protocol uses ONLY Clifford operations (which are fault-tolerant on stabiliser codes),</p>
<p>and outputs a magic state with error rate ∝ ε³ from inputs with error rate ε.</p>
<p>Error reduction per round:  ε → 35ε³   (cubic suppression)</p>
<p>Resource cost per round:    15 noisy T states → 1 high-fidelity T state</p>
<p>After k rounds of distillation:  ε_k ≈ (35ε₀)^(3^k) / 35</p>
<p>Example: ε₀ = 10⁻³, 2 rounds → ε₂ ≈ 10⁻²¹  (far below any required precision)</p>
<p>Total raw T states for 2-round distillation: 15² = 225 raw T gates → 1 logical T gate</p>
</div>

## 6.6 Resource Estimates: Factoring RSA-2048

The most commonly cited target for fault-tolerant quantum computing is factoring RSA-2048 — the 2048-bit public key used in most current internet security. Physical resource requirements have been carefully studied.

| Reference | Year | Logical Qubits | Physical Qubits | Time Estimate |
|---|---|---|---|---|
| Beauregard | 2003 | ~4,099 | ~100K (rough) | ~1 year |
| Fowler, Martinis et al. | 2012 | ~4,000 | ~1 billion | ~27 hours |
| Babbush et al. | 2019 | ~4,000 | ~20 million | ~8 hours |
| Webber et al. (Quantinuum) | 2022 | ~13,000 | ~13.6 million | ~104 days |
| Sanders et al. (optimised) | 2023 | ~4,500 | ~4 million | ~1 week |

<div class="box box-real-world">
<p class="box-title"><strong>🌐  Why ~20 Million Physical Qubits? The Key Arithmetic</strong></p>
<p>Requirements for RSA-2048 factoring via Shor's algorithm:</p>
<p>1.  Logical qubits: ~4,000 (for 2048-bit modular arithmetic in Shor's circuit)</p>
<p>2.  T-gate count: ~3 × 10⁹ T gates (from optimised Shor's circuit analysis)</p>
<p>3.  Physical qubits per logical qubit (surface code, d=27):</p>
<p>2d² − 1 = 2(27)² − 1 = 1457 physical qubits per logical qubit</p>
<p>Including ancilla overhead (~1.5×): ~2200 per logical qubit</p>
<p>4.  Magic state factory overhead: ~50 T factories × ~1000 qubits each = 50,000 qubits</p>
<p>5.  Total: ~4000 × 2200 + 50,000 ≈ 8.9 million (higher estimates add routing: ~20 million)</p>
<p>At 1 MHz surface code cycle rate: runtime ≈ 3×10⁹ / 10⁶ = 3000 seconds ≈ 50 minutes</p>
<p>CURRENT STATUS (2024): Best hardware has ~1000 qubits, no fault tolerance → completely infeasible</p>
</div>

## 6.7 IBM 2023: First Experimental Evidence of the Threshold Theorem

In 2023, IBM Research published a landmark paper in Nature demonstrating, for the first time, that a larger surface code outperforms a smaller one — providing the first direct experimental evidence that the threshold theorem works in practice.

| Code Distance d | Physical Qubits | Logical Error Rate per Round | Improvement vs d=3 |
|---|---|---|---|
| d = 3 | 17 qubits | 3.0 × 10⁻³ | Baseline |
| d = 5 | 49 qubits | 2.1 × 10⁻³ | 30% improvement |
| d = 7 | 97 qubits | 1.7 × 10⁻³ | 43% improvement |

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  IBM 2023: What Was and Was NOT Demonstrated</strong></p>
<p>DOES demonstrate:</p>
<p>✓  Larger surface codes have lower logical error rates (threshold behaviour observed)</p>
<p>✓  Physical gate fidelity (Eagle processor, ~0.1% error) is sub-threshold</p>
<p>✓  Syndrome extraction and MWPM decoding working experimentally</p>
<p>DOES NOT demonstrate:</p>
<p>✗  Fault-tolerant logical GATE operations (memory experiment only)</p>
<p>✗  Scalability to millions of qubits</p>
<p>✗  Logical error rates below 10⁻¹⁰ (needed for Shor; current: ~10⁻³)</p>
<p>The 2023 result is the FIRST RUNG of the fault-tolerant ladder.</p>
<p>Many more rungs must be climbed before Shor's algorithm is physically realisable.</p>
</div>

## 6.8 Full Fault-Tolerant Quantum Computer Architecture

A complete fault-tolerant quantum computer requires multiple layers working together. Understanding this architecture is essential for appreciating both the engineering challenges and the long-term prospects.

<img class="fig-img" src="content/images/image22.png" alt="figure">

**Figure 7: Fault-Tolerant Quantum Computer: Full Architecture Stack** *— Six layers from physical qubits to user algorithm; QEC and magic state distillation dominate layers 2 and 3*

### 6.8.1 Concatenated Codes vs Surface Codes

| Property | Concatenated Codes | Surface Codes |
|---|---|---|
| Threshold | ~10⁻⁴ (0.01%) | ~1% |
| Connectivity required | All-to-all or hierarchical | 2D nearest-neighbour only |
| Physical qubit overhead | ~10,000× per logical qubit | ~1,000× per logical qubit (d=25) |
| Logical gate set | Transversal Clifford (Steane) | Clifford + magic state T |
| Decoding complexity | Hierarchical, efficient | MWPM: O(n³) but parallelisable |
| Current experimental status | Demonstrated in small systems | IBM 2023: threshold demonstrated |

<div class="box box-generic">
<p class="box-title"><strong>📋  Chapter 6 Summary</strong></p>
<p>Surface code:           2D nearest-neighbour; [[2d²−1, 1, d]]; X-plaquette + Z-vertex stabilisers</p>
<p>Logical operators:      X_L = horizontal string (length d); Z_L = vertical string (length d)</p>
<p>Syndrome extraction:    4-qubit plaquette measurements via ancilla CNOT circuits</p>
<p>MWPM decoding:          Pairs syndrome defects by minimum total distance; O(n³) classical</p>
<p>Threshold theorem:      p &lt; p_th ≈ 1%: larger d gives exponentially lower p_L</p>
<p>Logical error rate:     p_L ≈ A(p/p_th)^⌈(d+1)/2⌉; exponential suppression with d</p>
<p>Magic state:            |T⟩ = T|+⟩; consumed in gate teleportation to perform T gate</p>
<p>15-to-1 distillation:   ε → 35ε³; enables fault-tolerant T gate from noisy T gates</p>
<p>Eastin-Knill theorem:   No stabiliser code has a transversal universal gate set</p>
<p>RSA-2048 estimate:      ~4–20 million physical qubits; ~8 hours; d=27 surface code</p>
<p>IBM 2023 experiment:    d=3,5,7 surface codes: larger d → lower p_L (first threshold evidence)</p>
</div>

## RECAP — SHORT ANSWER QUESTIONS & MODEL ANSWERS

Chapter 6: Surface Codes and Fault-Tolerant Computing

Instructions: Answer each question in 3–6 lines. Each question carries equal marks.

**PART A — QUESTIONS**

**Q1.  What is a topological quantum code, and how does the surface code implement one?**

**Q2.  Describe how syndrome extraction and decoding work in the surface code.**

**Q3.  State the threshold theorem and explain its practical significance for scaling quantum computers.**

**Q4.  Why is the T gate especially costly in a fault-tolerant architecture, and how is it implemented?**

**Q5.  Why does factoring RSA-2048 with Shor's algorithm require on the order of 20 million physical qubits despite needing only a few thousand logical qubits?**

**Q6.  What experimental milestone did IBM report in 2023 regarding the threshold theorem?**

**Q7.  What is the difference between X-type and Z-type stabilisers in the surface code, and what errors does each detect?**

**Q8.  Explain, at a conceptual level, why a full fault-tolerant quantum computer architecture needs more than just an error-correcting code.**

**Q9.  Why do resource estimates for fault-tolerant algorithms depend so heavily on magic state distillation overhead?**

**Q10.  How does concatenation (as in Shor's original 9-qubit code) differ from the 2D topological structure of the surface code?**

**Q11.  What does it mean for the surface code threshold to be 'about 1%', and why is this number important for hardware roadmaps?**

**PART B — MODEL ANSWERS**

**Answer 1:**

A topological code encodes logical information in global, non-local properties of a 2D lattice of qubits (such as boundary conditions or loop parities) that are robust to local physical errors. The surface code arranges physical qubits on a 2D lattice with alternating X-type and Z-type stabiliser plaquettes; the logical qubit is encoded in the global topology of the lattice (e.g. the parity of a chain of operators connecting boundaries), making local errors detectable via nearest-neighbour stabiliser measurements alone.

**Answer 2:**

Ancilla qubits are used to repeatedly measure the X-type and Z-type stabiliser generators around each plaquette without collapsing the encoded logical information, producing a time series of syndrome outcomes. A classical decoding algorithm (commonly minimum-weight perfect matching, MWPM) then infers the most likely pattern of physical errors consistent with the observed syndromes and applies the corresponding Pauli correction, all without ever directly measuring the logical qubit.

**Answer 3:**

The threshold theorem states that if the physical error rate p is below a threshold p\_th (roughly 1% for the surface code), then increasing the code distance d suppresses the logical error rate exponentially: p\_L ≈ A(p/p\_th)^⌈(d+1)/2⌉. Practically, this means that a large-scale, arbitrarily long, fault-tolerant computation is possible in principle — as long as hardware achieves error rates below threshold, adding more physical qubits (larger d) can make logical errors as rare as desired.

**Answer 4:**

The T gate is a non-Clifford gate, so unlike Clifford gates it cannot be implemented transversally (fault-tolerantly) on codes like the surface code or Steane code without risking error propagation. Instead, T gates are implemented via magic state distillation: many noisy copies of a special 'magic state' are purified through repeated rounds of a distillation protocol (e.g. the [[15,1,3]] Reed-Muller code) to produce one high-fidelity magic state, which is then consumed via gate teleportation to apply a fault-tolerant T gate — an expensive process dominating the resource cost of fault-tolerant algorithms.

**Answer 5:**

Shor's algorithm on RSA-2048 requires roughly 4,000-plus logical qubits, but each logical qubit under the surface code requires on the order of 1,000 physical qubits (at realistic near-threshold error rates and target logical error rates) for reliable error correction, plus substantial additional overhead for magic state distillation factories needed to supply T gates at the rate the algorithm consumes them. Multiplying logical qubit count by per-qubit physical overhead and factory overhead yields the widely cited estimate of about 20 million physical qubits.

**Answer 6:**

IBM's 2023 Nature paper presented experimental evidence that increasing the code distance of an implemented error-correcting code reduced the logical error rate, consistent with predictions of the threshold theorem — an important practical demonstration that the theoretical benefit of larger codes is being realised on real superconducting hardware, rather than being purely a theoretical construct.

**Answer 7:**

X-type stabilisers are products of Pauli-X operators around a 'star' or vertex configuration of the lattice and are sensitive to (anticommute with) Z errors (phase flips) on the qubits they act on; Z-type stabilisers are products of Pauli-Z operators around a 'plaquette' configuration and are sensitive to X errors (bit flips). Measuring both types of stabilisers therefore allows the surface code to detect and localise both bit-flip and phase-flip errors independently, similar in spirit to how the Shor code separately handles X and Z errors but using a scalable 2D topological structure instead of concatenation.

**Answer 8:**

An error-correcting code alone only defines how information is protected while idle; a full fault-tolerant architecture must also ensure that the logical gates, syndrome extraction circuits, and state preparation/measurement procedures themselves do not introduce or propagate errors uncontrollably (e.g. via transversal gates, fault-tolerant syndrome extraction, and magic state distillation for non-Clifford gates). All these pieces — code, fault-tolerant gates, decoding, and resource management — must work together below threshold for a computation of arbitrary length to be reliably executed.

**Answer 9:**

Because Clifford gates are 'free' in the sense of being directly transversal and fault-tolerant, but T gates require costly distillation (often needing many noisy input magic states to produce one high-fidelity output, repeated across multiple distillation rounds), the total physical resource cost of a fault-tolerant algorithm is frequently dominated by its T-count and the associated distillation factories rather than by the 'logical' gate count alone — this is why circuit designers work hard to minimise T-count even at the expense of adding more Clifford gates.

**Answer 10:**

Concatenated codes nest smaller codes recursively inside each other (e.g. bit-flip code within phase-flip code) to build up protection level by level, with overhead and complexity growing multiplicatively with each level of concatenation. The surface code instead uses a single flat 2D lattice with only nearest-neighbour stabiliser interactions, which is more compatible with realistic hardware connectivity constraints and achieves a favourable threshold, making it the leading practical candidate architecture for near-term fault-tolerant hardware despite generally requiring more physical qubits per logical qubit than deeply concatenated schemes at equivalent logical error rates.

**Answer 11:**

A threshold of about 1% means that if the physical two-qubit gate error rate can be engineered below roughly 1%, then scaling up the code distance d will make the logical error rate arbitrarily small; above this threshold, adding more physical qubits actually makes things worse, not better. Because leading superconducting platforms have pushed two-qubit gate fidelities to the 99.5%+ range (error rates below 0.5%), current hardware roadmaps are explicitly built around demonstrating and then scaling surface-code-based fault tolerance now that this threshold condition is achievable.

## A. Solved Problems

<div class="box box-generic">
<p class="box-title"><strong>Solved Example 6  Surface Code Logical Error Rate Calculation</strong></p>
<p>Problem: For a d=7 surface code with p = 0.3%, p_th = 1%, A = 0.1,</p>
<p>calculate the logical error rate and compare to the physical rate.</p>
<p>Formula: p_L ≈ A × (p / p_th)^⌈(d+1)/2⌉</p>
<p>For d = 7:  ⌈(7+1)/2⌉ = ⌈4⌉ = 4</p>
<p>p / p_th = 0.003 / 0.01 = 0.3</p>
<p>p_L = 0.1 × (0.3)^4 = 0.1 × 0.0081 = 8.1 × 10⁻⁴  ≈  0.081%</p>
<p>Physical error rate: p = 0.3%</p>
<p>Logical error rate:  p_L = 0.081%</p>
<p>Improvement factor: p / p_L = 0.3 / 0.081 ≈ 3.7×</p>
<p>For d=15: p_L = 0.1×(0.3)^8 = 6.56×10⁻⁶  (far better — exponential improvement!)</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Solved Example 7  Magic State Distillation Resource Calculation</strong></p>
<p>Problem: An algorithm needs M = 10⁶ T gates with target precision ε_T = 10⁻¹².</p>
<p>Starting from ε₀ = 10⁻³, how many rounds of 15-to-1 distillation are needed?</p>
<p>15-to-1 protocol: ε_out ≈ 35 × ε_in³  (per round)</p>
<p>Round 0: ε₀ = 10⁻³</p>
<p>Round 1: ε₁ ≈ 35 × (10⁻³)³ = 35 × 10⁻⁹ = 3.5 × 10⁻⁸</p>
<p>Round 2: ε₂ ≈ 35 × (3.5×10⁻⁸)³ = 35 × 4.3×10⁻²³ ≈ 1.5 × 10⁻²¹</p>
<p>2 rounds give ε₂ ≈ 10⁻²¹ &lt;&lt; 10⁻¹² required  →  2 rounds suffice  ✓</p>
<p>T state consumption per output T state: 15² = 225 raw T gates</p>
<p>Total raw T gates needed: 225 × 10⁶ = 2.25 × 10⁸ raw T operations</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Solved Example 8  Physical Qubit Count for RSA-1024</strong></p>
<p>Problem: Estimate physical qubit count to factor RSA-1024 with surface code d=23.</p>
<p>Assume: 2,000 logical qubits needed, 1.5×10⁹ T gates.</p>
<p>Physical qubits per logical qubit (surface code, d=23):</p>
<p>2d² − 1 = 2×(23)² − 1 = 2×529 − 1 = 1057 data+check per logical qubit</p>
<p>With ancilla overhead (~1.5×): ~1600 per logical qubit</p>
<p>Computation qubits: 2000 × 1600 = 3.2 × 10⁶</p>
<p>Magic state factories (20 factories × 1000 qubits each): 20,000 additional</p>
<p>Total: ~3.22 million physical qubits</p>
<p>Runtime: 1.5×10⁹ T gates × 1μs per surface code cycle ≈ 1500 seconds ≈ 25 minutes</p>
<p>Note: RSA-1024 keys are already considered insecure classically — only RSA-2048+ is relevant today.</p>
</div>

## B. Unsolved Problems

## Chapter 6: Problems

**11.** For a distance-5 surface code, count: (a) total physical qubits, (b) data qubits, (c) X-stabilisers, (d) Z-stabilisers.  *[Ans: (a) 2d²−1=49; (b) d²=25; (c) ~12 X-ancilla; (d) ~12 Z-ancilla (total=49)]*

**12.** Calculate p\_L for a d=9 surface code with p=0.2% and p\_th=1%.  *[Ans: ⌈(9+1)/2⌉=5; p\_L=0.1×(0.002/0.01)^5≈3.2×10⁻⁵; improvement factor ≈62.5×]*

**13.** In MWPM, syndrome defects are at (1,1) and (4,4) on a d=7 grid. What is the minimum weight matching?  *[Ans: Manhattan distance = |4−1|+|4−1| = 6; apply chain of X corrections along 6-step path]*

**14.** Estimate raw T gates needed for 10⁸ logical T gates with 15-to-1 distillation, ε₀=5×10⁻³, ε\_T=10⁻¹⁵.  *[Ans: 2 rounds needed (ε₂≈3×10⁻¹⁶); 225 raw per output; total 225×10⁸=2.25×10¹⁰]*

**15.** For surface code d=11, p=0.5%, how many error correction rounds per logical gate and cycles per second at 1μs cycle time?  *[Ans: d=11 rounds per logical gate = 11μs per gate; cycles/sec = 10⁶ per second (1 MHz)]*

**16.** Why is only the S gate (not X or Z) needed as the T gate teleportation post-measurement correction?  *[Ans: S=TXT† (up to global phase) accounts for the T-gate phase difference; X and Z are insufficient]*

**17.** IBM Eagle processor: 133 qubits, ~0.1% error. How many d=3, d=5, d=7 surface codes simultaneously?  *[Ans: d=3: 133/17=7 codes; d=5: 133/49=2 codes; d=7: 133/97=1 code]*

**18.** Calculate P(logical error chain of length d=7) per round for p=0.1%.  *[Ans: P ≈ p^d = (10⁻³)^7 = 10⁻²¹ per round — negligibly small]*

**19.** Concatenated Steane [[7,1,3]] at level k=3, estimate p\_L starting from p=10⁻⁵, p\_th=10⁻⁴.  *[Ans: p\_L=10⁻⁴×(10⁻¹)^8=10⁻¹² (excellent sub-threshold suppression)]*

**20.** FT computer uses d=27 surface code, 4000 logical qubits, 20 magic factories (5000 qubits each). Estimate total physical qubits.  *[Ans: Logical: 4000×1457≈5.83M; Factories: 100K; Routing: 1.2M; Total: ~7.1 million]*

## C. Multiple Choice Questions

## Chapter 6 MCQs

<div class="box box-generic">
<p class="box-title"><strong>Q16.  A distance-d surface code requires how many total physical qubits?</strong></p>
<p>(A)  d²</p>
<p>(B)  2d−1</p>
<p>(C)  2d²−1</p>
<p>(D)  4d²</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q17.  In the surface code, the logical X_L operator corresponds to:</strong></p>
<p>(A)  A vertical Z-string from top to bottom</p>
<p>(B)  A horizontal X-string from left to right boundary</p>
<p>(C)  X operator on all qubits</p>
<p>(D)  A plaquette operator</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q18.  The threshold error rate for the surface code with MWPM decoding is approximately:</strong></p>
<p>(A)  0.01%</p>
<p>(B)  0.1%</p>
<p>(C)  1%</p>
<p>(D)  5%</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q19.  The logical error rate formula for the surface code is:</strong></p>
<p>(A)  p_L ≈ p/d</p>
<p>(B)  p_L ≈ A(p/p_th)^⌈(d+1)/2⌉</p>
<p>(C)  p_L ≈ A·exp(−d·p)</p>
<p>(D)  p_L ≈ p² for any d</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q20.  Magic state distillation is needed because:</strong></p>
<p>(A)  T gate has lower fidelity</p>
<p>(B)  No stabiliser code implements T transversally (Eastin-Knill); magic states provide an indirect route</p>
<p>(C)  T gates need more physical qubits</p>
<p>(D)  Classical computers cannot simulate T gates</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q21.  The 15-to-1 distillation protocol reduces magic state error as:</strong></p>
<p>(A)  ε → ε²</p>
<p>(B)  ε → 35ε³</p>
<p>(C)  ε → ε/15</p>
<p>(D)  ε → ε^15</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q22.  The T gate magic state is:</strong></p>
<p>(A)  |0⟩</p>
<p>(B)  T|0⟩</p>
<p>(C)  T|+⟩ = (|0⟩+e^(iπ/4)|1⟩)/√2</p>
<p>(D)  H|T⟩</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q23.  Kitaev's toric code stores logical information in:</strong></p>
<p>(A)  The energy of the ground state</p>
<p>(B)  Non-contractible loops on the torus (topological invariants)</p>
<p>(C)  The total parity of measurements</p>
<p>(D)  Local stabiliser eigenvalues</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q24.  The MWPM decoder for the surface code:</strong></p>
<p>(A)  Measures all qubits</p>
<p>(B)  Pairs syndrome defects by minimum total distance</p>
<p>(C)  Uses quantum phase estimation</p>
<p>(D)  Applies random corrections</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q25.  IBM's 2023 experiment showed:</strong></p>
<p>(A)  Logical error rate below 10⁻¹⁰</p>
<p>(B)  Fault-tolerant logical gate operations</p>
<p>(C)  Larger surface codes (d=5,7) outperform d=3 — threshold behaviour</p>
<p>(D)  RSA-1024 factoring on 133 qubits</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q26.  The threshold theorem was proved (1996-97) by:</strong></p>
<p>(A)  Shor and Steane</p>
<p>(B)  Aharonov-Ben-Or, Kitaev, and Knill-Laflamme-Zurek</p>
<p>(C)  Google and IBM research teams</p>
<p>(D)  Bravyi and Kitaev</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q27.  The Gottesman-Knill theorem implies that:</strong></p>
<p>(A)  Clifford circuits are more powerful than classical</p>
<p>(B)  Clifford circuits can be efficiently simulated classically</p>
<p>(C)  Any quantum circuit can be Cliffordised</p>
<p>(D)  Error rates below 1% guarantee fault tolerance</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q28.  For d=7, p=0.1%, p_th=1%, A=0.1, the logical error rate is approximately:</strong></p>
<p>(A)  ≈ 10⁻⁷</p>
<p>(B)  ≈ 10⁻⁸</p>
<p>(C)  ≈ 10⁻⁵</p>
<p>(D)  ≈ 10⁻⁴</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q29.  Surface code syndrome extraction for an X-plaquette uses:</strong></p>
<p>(A)  3 H + 2 CNOT per check</p>
<p>(B)  1 ancilla + 4 CNOT + H before/after measurement</p>
<p>(C)  Direct measurement of 4 data qubits</p>
<p>(D)  QFT on check qubits</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q30.  Approximate physical qubits to factor RSA-2048 with a surface code:</strong></p>
<p>(A)  ~1,000</p>
<p>(B)  ~100,000</p>
<p>(C)  ~4–20 million</p>
<p>(D)  ~10 billion</p>
</div>

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  MCQ Answer Key — Chapter 5 (Q1–Q15)</strong></p>
<p>Q1:B   Q2:B   Q3:B   Q4:B   Q5:C   Q6:B   Q7:C   Q8:A   Q9:D   Q10:B</p>
<p>Q11:B   Q12:B   Q13:B   Q14:B   Q15:C</p>
</div>

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  MCQ Answer Key — Chapter 6 (Q16–Q30)</strong></p>
<p>Q16:C   Q17:B   Q18:C   Q19:B   Q20:B   Q21:B   Q22:B   Q23:B   Q24:B   Q25:C</p>
<p>Q26:B   Q27:B   Q28:B   Q29:B   Q30:C</p>
</div>

## D. Theory Questions

## Chapter 6: Theory Questions

**Q11.** Describe the surface code lattice for distance d: qubit layout, X-plaquette and Z-vertex stabilisers, boundary conditions, and total physical qubit count. How is the code distance related to the lattice size?

**Q12.** Explain the topological interpretation of the surface code. What is an 'anyon', and how does MWPM decide which correction to apply? What constitutes a 'logical error' in topological terms?

**Q13.** State the threshold theorem. What is the physical interpretation of p\_th ≈ 1% for the surface code? Derive or motivate the logical error rate formula p\_L ≈ A(p/p\_th)^⌈(d+1)/2⌉.

**Q14.** Describe the syndrome extraction circuit for an X-plaquette stabiliser. Why are ancilla qubits needed? What types of errors in syndrome extraction are dangerous, and how are they handled?

**Q15.** Explain magic state distillation: what is the magic state |T⟩, how is it used for T gate teleportation, and why does the 15-to-1 Bravyi-Kitaev protocol suppress errors cubically?

**Q16.** Why does magic state distillation dominate the physical qubit overhead of fault-tolerant computing? Estimate the distillation overhead for 10⁶ T gates at target precision ε = 10⁻¹⁵.

**Q17.** Compare concatenated codes and surface codes on: threshold, connectivity, qubit overhead, decoding complexity, and current experimental status.

**Q18.** Describe the MWPM decoder. What is its algorithmic complexity? What alternative decoders have been proposed and why?

**Q19.** Estimate physical qubit and time requirements for Shor's algorithm on RSA-2048 using surface code d=27 at 1 GHz clock rate. What are the main sources of overhead?

**Q20.** Describe IBM's 2023 surface code experiment. What was demonstrated, and why is it significant? What further milestones must be reached before fault-tolerant computation is practical?

## F. Project Suggestions

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  Project 1: Surface Code Simulator with MWPM Decoder</strong></p>
<p>Build a complete distance-d surface code simulator from scratch:</p>
<p>• Implement the d × d data qubit lattice with X-plaquette and Z-vertex stabilisers</p>
<p>• Generate random X and Z errors under depolarising noise model p</p>
<p>• Implement syndrome extraction (identify which plaquettes have syndrome = −1)</p>
<p>• Implement MWPM decoder using NetworkX or PyMatching (Blossom V algorithm)</p>
<p>• Simulate for d = 3, 5, 7, 9 and p = 0.1%, 0.5%, 1%, 2%, 5%</p>
<p>• Plot logical error rate vs physical error rate for each d; identify threshold experimentally</p>
<p>• Compare to theoretical p_L = A(p/p_th)^⌈(d+1)/2⌉</p>
<p>Extension: Implement the Union-Find decoder and compare speed vs MWPM.</p>
<p>Deliverable: Python codebase + 6-page report with simulation results.</p>
</div>

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  Project 2: Magic State Distillation Protocol Implementation</strong></p>
<p>Implement and analyse magic state distillation:</p>
<p>• Implement the Bravyi-Kitaev 15-to-1 distillation circuit in Qiskit</p>
<p>• Model noisy T gate: ρ → (1−ε)T ρ T† + ε/3(XρX+YρY+ZρZ)</p>
<p>• Simulate 1 round for ε = 0.01, 0.05, 0.1; verify ε_out ≈ 35ε³</p>
<p>• Extend to 2 rounds; verify cascaded error suppression</p>
<p>• Calculate total raw T gate consumption for M=10⁴ T gates at ε_T = 10⁻⁹</p>
<p>• Compare to directly using noisy T gate at hardware error rate</p>
<p>Deliverable: Jupyter notebook + technical report on distillation overhead.</p>
</div>
