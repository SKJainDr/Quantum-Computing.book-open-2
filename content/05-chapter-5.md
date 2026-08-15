# CHAPTER 5

# Quantum Error Correction: Principles, Codes & Stabilisers

*No-Cloning  |  3-Qubit Codes  |  Shor Code  |  Stabiliser Formalism  |  CSS Codes  |  Steane [7,1,3]*

<div class="box box-anecdote">
<p class="box-title"><strong>📜  Opening Reflection — John Preskill, Caltech, 1997</strong></p>
<p>"Error correction is the hidden secret that makes quantum computing possible.</p>
<p>Without it, every quantum computer is merely an expensive way to produce noise."</p>
<p>— John Preskill, Caltech, 1997</p>
<p>The history of computing is, in large part, the history of error correction. Classical computers achieve</p>
<p>near-perfect reliability through error-correcting codes embedded in every storage medium, every communication</p>
<p>channel, every memory chip. Quantum computers face a far harder challenge: errors are continuous, measurement</p>
<p>destroys information, and copying qubits is forbidden. The miracle is that quantum error correction is possible at all.</p>
</div>

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  Chapter 5 Learning Objectives</strong></p>
<p>After studying this chapter, you will be able to:</p>
<p>•  Explain why classical repetition codes fail for quantum information (no-cloning, continuous errors)</p>
<p>•  Describe the 3-qubit bit-flip code: encoding, syndrome measurement, and correction</p>
<p>•  Describe the 3-qubit phase-flip code and the Shor 9-qubit code</p>
<p>•  Explain the QEC miracle: discrete error correction covers continuous error spaces</p>
<p>•  Define the Pauli group, stabiliser groups, and stabiliser states</p>
<p>•  Describe the [[n,k,d]] stabiliser code formalism</p>
<p>•  Analyse syndrome measurement without disturbing the encoded logical qubit</p>
<p>•  Construct and analyse CSS codes, particularly the [[7,1,3]] Steane code</p>
<p>•  Identify logical operators X_L and Z_L on a stabiliser code</p>
</div>

## 5.1 Why Quantum Error Correction Appears Impossible

Before diving into how quantum error correction works, it is worth understanding why, at first glance, it seems completely impossible. Three fundamental obstacles conspire to make QEC appear hopeless:

### 5.1.1 Obstacle 1: The No-Cloning Theorem

<div class="box box-generic">
<p class="box-title"><strong>Theorem 5.1  No-Cloning Theorem (Wootters &amp; Zurek 1982)</strong></p>
<p>There is no quantum operation U that clones an arbitrary unknown quantum state.</p>
<p>That is, there is no unitary U such that for all |ψ⟩:</p>
<p>U(|ψ⟩ ⊗ |0⟩)  =  |ψ⟩ ⊗ |ψ⟩</p>
<p>Proof: Suppose such U existed. Then for |ψ⟩ = |0⟩, |1⟩, |+⟩:</p>
<p>U(|0⟩|0⟩) = |00⟩       U(|1⟩|0⟩) = |11⟩</p>
<p>U(|+⟩|0⟩) = U( (|0⟩+|1⟩)/√2 ⊗ |0⟩ )</p>
<p>= (U|0⟩|0⟩ + U|1⟩|0⟩) / √2     [by linearity of U]</p>
<p>= (|00⟩ + |11⟩) / √2  ≠  |+⟩|+⟩</p>
<p>Contradiction. Therefore no such U exists.  □</p>
<p>Consequence: Classical error correction copies data to detect/correct errors.</p>
<p>This strategy is FORBIDDEN for quantum states — we need a fundamentally different approach.</p>
</div>

<div class="box box-anecdote">
<p class="box-title"><strong>📜  No-Cloning and the Einstein-Podolsky-Rosen Ghost</strong></p>
<p>The no-cloning theorem was independently discovered by Wootters &amp; Zurek and by Dieks in 1982, motivated by a</p>
<p>proposal from Nick Herbert to use EPR pairs and cloning to send information faster than light. Herbert's paper</p>
<p>was actually published in Foundations of Physics (1982), and the refereeing process — where physicists tried and</p>
<p>FAILED to find the flaw — led directly to the discovery of the no-cloning theorem.</p>
<p>Rarely has a wrong paper had such a productive scientific impact. The no-cloning theorem is now recognised as a</p>
<p>fundamental feature of quantum mechanics with far-reaching implications for cryptography, teleportation, and QEC.</p>
</div>

### 5.1.2 Obstacle 2: Continuous Errors

A qubit suffers errors from its interaction with the environment. Unlike classical bits (which only flip 0↔1), a qubit can rotate by any angle. The general single-qubit error is parameterised by a continuous set of operators. How can we correct infinitely many possible errors?

**Error: |ψ⟩ → (a·I + b·X + c·Y + d·Z)|ψ⟩   for any a,b,c,d ∈ ℂ**

This looks like we need to correct an uncountable infinity of errors — one for each point on the Bloch sphere surface. Classical error correction handles only discrete (bit-flip) errors. The solution comes from a remarkable mathematical miracle, explained below.

### 5.1.3 Obstacle 3: Measurement Destroys Information

In classical error correction, we read the stored bits, check for errors, and rewrite the corrected value. In quantum computing, measuring the state collapses it — we destroy the quantum information we are trying to protect. Any naive attempt to 'check' the state will destroy the superposition.

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  The QEC Miracle: Why It Works Anyway</strong></p>
<p>Despite all three obstacles, quantum error correction is possible. The key insights are:</p>
<p>1.  SYNDROME MEASUREMENT: We measure WHICH ERROR occurred, not WHAT the logical state is.</p>
<p>A syndrome tells us 'error on qubit 2' without revealing the encoded information (α, β).</p>
<p>2.  DISCRETISATION: The Knill-Laflamme conditions ensure that correcting a discrete set {I, X, Y, Z}</p>
<p>automatically corrects ALL errors on those qubits. Continuous errors become discrete after syndrome!</p>
<p>3.  ENTANGLED SUBSPACE ENCODING: The logical qubit is encoded in a subspace of many physical qubits.</p>
<p>Local errors on individual qubits remain within this subspace — detectable and correctable.</p>
</div>

## 5.2 The 3-Qubit Bit-Flip Code

Despite the no-cloning theorem, we can protect quantum information by encoding it redundantly in ENTANGLED states, not by copying. The 3-qubit bit-flip code is the simplest example, and studying it in detail reveals all the essential features of quantum error correction.

### 5.2.1 Encoding

The logical qubit |ψ\_L⟩ = α|0\_L⟩ + β|1\_L⟩ is encoded into three physical qubits as:

**|0\_L⟩ = |000⟩       |1\_L⟩ = |111⟩**

The encoding circuit uses two CNOT gates: |ψ⟩|00⟩ → CNOT₁₂ → CNOT₁₃ → α|000⟩ + β|111⟩. This is NOT copying — the three-qubit state is an entangled superposition that encodes the amplitude ratio α:β without revealing those amplitudes.

<img class="fig-img" src="content/images/image16.png" alt="figure">

**Figure 1: 3-Qubit Bit-Flip Code: Encoding, Error & Syndrome Circuit** *— Encoding uses 2 CNOT gates; syndrome uses ancilla measurements to identify the erroneous qubit without disturbing the data*

### 5.2.2 Syndrome Measurement and Error Table

The syndrome measurements Z₁Z₂ and Z₂Z₃ identify WHICH qubit was flipped without revealing α or β. The eigenvalue +1 means no error at that location; −1 means an error:

| Error | Post-error State | Syndrome (s₁,s₂) | Correction |
|---|---|---|---|
| None | α\|000⟩+β\|111⟩ | (0,0) | I (no action) |
| X₁ (flip q1) | α\|100⟩+β\|011⟩ | (1,0) | Apply X₁ |
| X₂ (flip q2) | α\|010⟩+β\|101⟩ | (1,1) | Apply X₂ |
| X₃ (flip q3) | α\|001⟩+β\|110⟩ | (0,1) | Apply X₃ |

### 5.2.3 The Discretisation Miracle

Suppose the error is not a perfect bit-flip X but a partial rotation: E = cos(θ)I + i sin(θ)X. After syndrome measurement, the state collapses to either |ψ\_L⟩ (no error) or X₁|ψ\_L⟩ (bit flip on qubit 1). The continuous parameter θ vanishes! This is the fundamental magic: measurement collapses continuous errors into discrete, correctable syndromes.

**cos(θ)|ψ\_L⟩ + i sin(θ)X₁|ψ\_L⟩  →  syndrome  →  either |ψ\_L⟩  or  X₁|ψ\_L⟩**

## 5.3 The 3-Qubit Phase-Flip Code and Shor's 9-Qubit Code

### 5.3.1 Phase-Flip Errors and the Phase-Flip Code

A phase-flip (Z error) maps |+⟩ → |−⟩. To protect against Z errors, we encode in the Hadamard-rotated basis: |0\_L⟩ = |+++⟩ and |1\_L⟩ = |−−−⟩. The stabilisers become X₁X₂ and X₂X₃ — the exact Hadamard conjugate of the bit-flip code stabilisers.

### 5.3.2 Shor's 9-Qubit Code: Protecting Against Both X and Z

Shor's code (Peter Shor, 1995) was the first quantum error-correcting code protecting against BOTH bit-flip and phase-flip errors on any single qubit — and hence against arbitrary single-qubit errors — using 9 physical qubits. It uses a two-level concatenation:

**|0\_L⟩ = (1/2√2)(|000⟩+|111⟩)(|000⟩+|111⟩)(|000⟩+|111⟩)**

**|1\_L⟩ = (1/2√2)(|000⟩−|111⟩)(|000⟩−|111⟩)(|000⟩−|111⟩)**

<div class="box box-anecdote">
<p class="box-title"><strong>📜  Shor's 1995 QEC Breakthrough — When Physics Said 'It's Possible'</strong></p>
<p>In 1994, Peter Shor published his factoring algorithm — but quantum computers were still theoretical,</p>
<p>devastatingly sensitive to noise. Many physicists, including Rolf Landauer and Bill Unruh, argued that</p>
<p>decoherence made quantum computing physically impossible. Landauer famously insisted 'information is physical'</p>
<p>— and physical systems are noisy.</p>
<p>Then in 1995, Shor published his 9-qubit error-correcting code. Overnight, the question changed from</p>
<p>'Can quantum computers work?' to 'How much overhead does error correction require?'</p>
<p>Andrew Steane independently discovered a 7-qubit code in the same year.</p>
<p>John Preskill later wrote: 'I consider Shor's 1995 paper on quantum error correction to be one of the</p>
<p>most important papers in the history of physics.' It transformed quantum computing from a theoretical</p>
<p>curiosity into a physically realisable (if challenging) engineering goal.</p>
</div>

<img class="fig-img" src="content/images/image17.png" alt="figure">

**Figure 2: Shor 9-Qubit Code: Two-Level Concatenated Structure** *— Inner 3-qubit bit-flip codes protect X errors; outer 3-qubit phase-flip code protects Z errors; together they correct any single-qubit error*

## 5.4 The Knill-Laflamme Quantum Error Correction Conditions

The Knill-Laflamme conditions (1997) provide the precise mathematical criterion for a quantum code to correct a set of errors. They are both necessary and sufficient — the definitive characterisation of quantum correctability.

<div class="box box-generic">
<p class="box-title"><strong>Theorem 5.2  Knill-Laflamme QEC Conditions (1997)</strong></p>
<p>A quantum code with codeword subspace C (spanned by |0_L⟩, |1_L⟩) can correct a set of errors</p>
<p>{E_k} if and only if for all i, j ∈ {0_L, 1_L} and all error pairs (E_k, E_l):</p>
<p>⟨i_L| E_k† E_l |j_L⟩  =  C_{kl} δ_{ij}</p>
<p>where C_{kl} is a Hermitian matrix INDEPENDENT of the codeword indices i, j.</p>
<p>Physical interpretation:</p>
<p>• δ_{ij} condition: Different codewords remain distinguishable after ANY correctable error.</p>
<p>(Errors do not mix codewords — necessary for correctability.)</p>
<p>• C_{kl} condition: All error information is encoded in the syndrome, not the codeword amplitudes.</p>
<p>(We can diagnose the error from the syndrome alone — without learning α or β.)</p>
<p>Powerful consequence: A code corrects ALL errors on t qubits iff it corrects {I, X, Y, Z} on those</p>
<p>qubits — continuous errors map to discrete, correctable ones (the discretisation theorem).</p>
</div>

## 5.5 The Stabiliser Formalism

The stabiliser formalism (Gottesman 1997) provides a powerful and efficient framework for describing quantum error-correcting codes. It represents the code subspace IMPLICITLY — without writing down exponentially large state vectors — using the structure of the Pauli group.

### 5.5.1 The Pauli Group

The n-qubit Pauli group P\_n consists of all n-fold tensor products of Pauli operators {I, X, Y, Z} with overall phases {±1, ±i}. Key properties: every element squares to ±I; any two elements either commute or anticommute; |P\_n| = 4^(n+1).

**P\_n = { i^k σ₁ ⊗ σ₂ ⊗ ··· ⊗ σ\_n  :  k ∈ {0,1,2,3},  σⱼ ∈ {I,X,Y,Z} }**

### 5.5.2 Stabiliser Groups and Codes

<div class="box box-generic">
<p class="box-title"><strong>Definition 5.3  Stabiliser Group and [[n,k,d]] Code</strong></p>
<p>A stabiliser group S is an Abelian subgroup of P_n that does not contain −I.</p>
<p>The +1 eigenspace of all elements of S is the stabiliser code subspace:</p>
<p>C(S) = { |ψ⟩ : g|ψ⟩ = |ψ⟩  for all  g ∈ S }</p>
<p>The code C(S) encodes k = n − |generators(S)| logical qubits.</p>
<p>The code is called an [[n, k, d]] code where:</p>
<p>n = number of physical qubits</p>
<p>k = number of logical qubits (= n − # independent generators)</p>
<p>d = code distance = minimum weight of any logical operator not in S</p>
<p>Example: 3-qubit bit-flip code stabilisers: S = ⟨Z₁Z₂, Z₂Z₃⟩</p>
<p>Both generators have eigenvalue +1 on |000⟩ and |111⟩.</p>
<p>C(S) = span{|000⟩, |111⟩} — exactly the codespace.  ✓</p>
</div>

### 5.5.3 Syndrome Measurement in the Stabiliser Picture

In the stabiliser formalism, syndrome measurement is elegant: we measure each stabiliser generator gᵢ. An error-free codeword gives all +1 eigenvalues. After an error E, measuring gᵢ gives −1 if E anticommutes with gᵢ (error detected), and +1 if E commutes:

**syndrome bit sᵢ = 0  iff  [E, gᵢ] = 0   (E commutes with generator gᵢ)**

**syndrome bit sᵢ = 1  iff  {E, gᵢ} = 0   (E anticommutes with generator gᵢ)**

| Code | Parameters [[n,k,d]] | Corrects | Stabilisers | Comment |
|---|---|---|---|---|
| 3-qubit bit-flip | [[3,1,1]] | Detects 1 X-error | Z₁Z₂, Z₂Z₃ | Phase errors not corrected |
| 3-qubit phase-flip | [[3,1,1]] | Detects 1 Z-error | X₁X₂, X₂X₃ | Bit-flip errors not corrected |
| Shor code | [[9,1,3]] | Corrects 1 any-error | 8 generators | First universal QEC code |
| Steane code | [[7,1,3]] | Corrects 1 any-error | 6 generators | CSS; transversal Clifford |
| Perfect 5-qubit code | [[5,1,3]] | Corrects 1 any-error | 4 generators | Smallest possible code |
| Surface code (d=3) | [[9,1,3]] | Corrects 1 any-error | 8 generators | Topological; threshold ~1% |

## 5.6 CSS Codes and the [[7,1,3]] Steane Code

Calderbank-Shor-Steane (CSS) codes are a particularly important family of stabiliser codes constructed from two classical linear codes. Their structure enables transversal implementation of Clifford gates — a critical feature for fault tolerance.

<div class="box box-generic">
<p class="box-title"><strong>Construction 5.4  CSS Code Construction</strong></p>
<p>Let C₁ and C₂ be classical [n, k₁] and [n, k₂] binary linear codes with C₂ ⊆ C₁.</p>
<p>The CSS code CSS(C₁, C₂) is a quantum [[n, k₁−k₂, d]] code with:</p>
<p>X-stabilisers: one X^⊗H(C₂) generator for each parity-check row of C₂</p>
<p>Z-stabilisers: one Z^⊗H(C₁⊥) generator for each parity-check row of C₁⊥</p>
<p>Key property: X and Z stabilisers are built from different classical codes,</p>
<p>so X and Z errors can be detected and corrected INDEPENDENTLY.</p>
<p>Logical operators:</p>
<p>X_L = coset representative of C₁ / C₂</p>
<p>Z_L = coset representative of C₁⊥ / C₂⊥</p>
</div>

### 5.6.1 The [[7,1,3]] Steane Code

The Steane code uses the classical [7,4,3] Hamming code. Both C₁ and C₂ are taken from the Hamming family with C₂ = C₁⊥. The result is a [[7,1,3]] code — 7 physical qubits, 1 logical qubit, distance 3.

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  Steane Code: All 6 Stabiliser Generators</strong></p>
<p>X-stabilisers (from Hamming code parity checks):</p>
<p>g₁ = X₁X₂X₃X₄     g₂ = X₁X₂X₅X₆     g₃ = X₁X₃X₅X₇</p>
<p>Z-stabilisers (same parity checks applied to Z):</p>
<p>g₄ = Z₁Z₂Z₃Z₄     g₅ = Z₁Z₂Z₅Z₆     g₆ = Z₁Z₃Z₅Z₇</p>
<p>6 generators on 7 qubits  →  7 − 6 = 1 logical qubit  ✓</p>
<p>Logical operators:</p>
<p>X_L = X₁X₂X₃X₄X₅X₆X₇   (weight 7 — detects all single-qubit X errors)</p>
<p>Z_L = Z₁Z₂Z₃Z₄Z₅Z₆Z₇   (weight 7)</p>
<p>Code distance d = 3  (minimum weight of any logical operator mod stabilisers = 3)</p>
</div>

<img class="fig-img" src="content/images/image18.png" alt="figure">

**Figure 3: Steane [[7,1,3]] Code: Parity-Check Matrix & Tanner Graph** *— Left: the H matrix of the [7,4,3] Hamming code used for both X and Z stabilisers. Right: Tanner graph connecting qubit nodes to stabiliser check nodes*

### 5.6.2 Transversal Gates on the Steane Code

| Logical Gate | Implementation on Steane Code | Fault-Tolerant? |
|---|---|---|
| X_L (Pauli X) | X on all 7 physical qubits | YES — transversal |
| Z_L (Pauli Z) | Z on all 7 physical qubits | YES — transversal |
| H_L (Hadamard) | H on all 7 physical qubits | YES — transversal |
| S_L (Phase gate) | S on all 7 physical qubits | YES — transversal |
| CNOT_L | CNOT between corresponding qubits of two code blocks | YES — transversal |
| T gate (π/8 rotation) | NOT transversal — requires magic state distillation | NOT directly FT |

<div class="box box-warning">
<p class="box-title"><strong>⚠  The Clifford Group, T Gate, and the Eastin-Knill Theorem</strong></p>
<p>The Clifford group (generated by H, S, CNOT) maps Pauli operators to Pauli operators under conjugation.</p>
<p>Stabiliser codes have transversal Clifford gates by design — this is their great advantage.</p>
<p>Unfortunately, Clifford circuits alone are NOT universal — they can be efficiently simulated classically</p>
<p>(Gottesman-Knill theorem). To achieve universality we need at least one non-Clifford gate: T = diag(1, e^{iπ/4}).</p>
<p>The Eastin-Knill theorem (2009) proves: NO stabiliser code can implement ALL gates transversally.</p>
<p>This is why fault-tolerant T gates require magic state distillation (Chapter 6) — a costly overhead</p>
<p>that dominates the resource cost of fault-tolerant quantum computing.</p>
</div>

### 5.6.3 The [[5,1,3]] Perfect Code

The 5-qubit code is the smallest possible quantum code correcting arbitrary single-qubit errors. It is called 'perfect' because it saturates the quantum Hamming bound — there is no smaller code with the same error-correcting power.

<div class="box box-generic">
<p class="box-title"><strong>Theorem 5.5  Quantum Hamming Bound</strong></p>
<p>An [[n,k,d]] code with d = 2t+1 (corrects t errors) must satisfy:</p>
<p>2^(n-k)  ≥  Σ_{j=0}^{t}  C(n,j) × 3^j</p>
<p>For t=1 (correct 1 error), k=1 (1 logical qubit):</p>
<p>2^(n-1) ≥ 1 + 3n</p>
<p>For n=5:  2^4 = 16 ≥ 1 + 15 = 16  ✓  (exactly saturated — 'perfect')</p>
<p>For n=4:  2^3 = 8  &lt; 1 + 12 = 13  ✗  (impossible!)</p>
<p>The 5-qubit code is the unique perfect code correcting all single-qubit errors.</p>
<p>Stabilisers (cyclic structure): g₁ = XZZXI,  g₂ = IXZZX,  g₃ = XIXZZ,  g₄ = ZXIXZ</p>
<p>Logical operators: X_L = XXXXX,  Z_L = ZZZZZ</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>📋  Chapter 5 Summary</strong></p>
<p>No-Cloning Theorem:      Quantum states cannot be copied; classical repetition codes fail</p>
<p>Continuous errors:       Discretisation miracle: syndrome measurement collapses errors to discrete</p>
<p>3-qubit bit-flip code:   Syndromes = Z₁Z₂, Z₂Z₃; corrects any single X error; [[3,1,1]]</p>
<p>Shor 9-qubit code:       Concatenated; [[9,1,3]]; corrects any single-qubit error (1995)</p>
<p>Knill-Laflamme:          ⟨i_L|E_k†E_l|j_L⟩ = C_{kl}δ_{ij}; necessary and sufficient</p>
<p>Pauli group:             P_n: n-qubit Paulis with phases; elements commute or anticommute</p>
<p>Stabiliser codes [[n,k,d]]: n physical, k logical qubits; k = n − (#generators)</p>
<p>Syndrome measurement:    Measure stabiliser generators; −1 eigenvalue flags error location</p>
<p>CSS codes:               Built from C₂ ⊆ C₁; X and Z errors corrected independently</p>
<p>Steane [[7,1,3]]:        7 qubits, 1 logical, distance 3; transversal Clifford gates; 6 stabilisers</p>
<p>Perfect 5-qubit code:    Smallest code correcting all single-qubit errors; saturates Hamming bound</p>
<p>Eastin-Knill theorem:    No stabiliser code has a transversal universal gate set</p>
</div>

## RECAP — SHORT ANSWER QUESTIONS & MODEL ANSWERS

Chapter 5: Quantum Error Correction Principles

Instructions: Answer each question in 3–6 lines. Each question carries equal marks.

**PART A — QUESTIONS**

**Q1.  Why does the no-cloning theorem appear to make quantum error correction impossible, and how is this obstacle overcome?**

**Q2.  How does 'the discretisation miracle' allow correction of continuous rotation errors using only discrete syndrome outcomes?**

**Q3.  Describe the encoding and syndrome measurement of the 3-qubit bit-flip code.**

**Q4.  How does the phase-flip code relate to the bit-flip code, and why is Shor's 9-qubit code needed to protect against both error types?**

**Q5.  State the Knill-Laflamme conditions and explain their two physical implications.**

**Q6.  What is the stabiliser formalism, and why is it more efficient than describing codes via explicit state vectors?**

**Q7.  What are CSS codes, and what is special about the [[7,1,3]] Steane code?**

**Q8.  What do the three numbers in the notation [[n,k,d]] represent for a stabiliser code?**

**Q9.  Explain what logical operators X\_L and Z\_L are and why they must commute with all stabiliser generators.**

**Q10.  How does the Steane code's transversality property simplify fault-tolerant computation?**

**Q11.  Why is quantum error correction sometimes described as a 'miracle', historically?**

**PART B — MODEL ANSWERS**

**Answer 1:**

Classical error correction relies on copying data redundantly, but the no-cloning theorem forbids copying an arbitrary unknown quantum state. Quantum error correction sidesteps this by encoding the logical qubit in an entangled superposition across several physical qubits (e.g. α|000⟩+β|111⟩) rather than literally copying it — the redundancy exists in correlations between qubits, not in duplicated amplitudes.

**Answer 2:**

Although a physical qubit can undergo a continuous rotation error such as E = cos(θ)I + i sin(θ)X, syndrome measurement projects the corrupted state onto one of a discrete set of outcomes (e.g. 'no error' or 'X₁ occurred') without revealing θ or the encoded amplitudes α, β. This measurement collapses the continuous error into one of a finite set of discrete, correctable Pauli errors — the Knill-Laflamme conditions guarantee that correcting this discrete set suffices to correct the full continuum of possible errors.

**Answer 3:**

The logical states are encoded as |0\_L⟩=|000⟩ and |1\_L⟩=|111⟩ using two CNOT gates. Syndrome measurement of the stabiliser generators Z₁Z₂ and Z₂Z₃ (without measuring the individual qubits) yields a two-bit syndrome (s₁,s₂) that uniquely identifies which single qubit (if any) suffered a bit-flip, without disturbing or revealing the encoded amplitudes α and β, allowing the correct Pauli-X correction to be applied.

**Answer 4:**

The phase-flip code is the Hadamard-conjugate of the bit-flip code: encoding in |0\_L⟩=|+++⟩, |1\_L⟩=|−−−⟩ with stabilisers X₁X₂, X₂X₃ protects against Z errors just as the bit-flip code protects against X errors, but neither code alone protects against both. Shor's 9-qubit code concatenates the bit-flip code (inner, 3 qubits) inside the phase-flip code (outer, 3 blocks of 3) to correct an arbitrary single-qubit error (any combination of X, Y, Z) using 9 physical qubits per logical qubit.

**Answer 5:**

The Knill-Laflamme conditions require ⟨i\_L|E\_k†E\_l|j\_L⟩ = C\_kl·δ\_ij for all codewords i,j and correctable errors E\_k,E\_l, where C\_kl is independent of i,j. The δ\_ij part ensures different logical codewords remain perfectly distinguishable after any correctable error (errors don't mix logical states), while the C\_kl-independence-of-codeword part ensures all information about which error occurred is encoded purely in the syndrome, not in the amplitudes α, β — so diagnosing the error never reveals (and hence never disturbs) the encoded quantum information.

**Answer 6:**

The stabiliser formalism (Gottesman, 1997) describes a quantum code implicitly as the joint +1 eigenspace of a set of commuting Pauli group operators (the stabiliser generators), rather than writing out the exponentially large state vector of the code explicitly. This allows an n-qubit stabiliser code to be fully specified and manipulated using only O(n) generators and O(n²) classical bookkeeping (per the Gottesman-Knill theorem), rather than the O(2^n) resources a general n-qubit state would require.

**Answer 7:**

CSS (Calderbank-Shor-Steane) codes are stabiliser codes constructed from a pair of nested classical error-correcting codes, with X-type stabilisers derived from one classical code and Z-type stabilisers from its dual — allowing bit-flip and phase-flip protection to be designed somewhat independently. The [[7,1,3]] Steane code encodes 1 logical qubit into 7 physical qubits with code distance 3 (correcting any single-qubit error), and crucially supports transversal implementation of the full Clifford gate set, simplifying fault-tolerant gate design.

**Answer 8:**

n is the number of physical qubits used by the code, k is the number of logical qubits encoded, and d is the code distance — the minimum weight of an undetectable logical error, which determines how many physical-qubit errors the code can correct (⌊(d−1)/2⌋ errors). A larger distance d gives stronger error protection but generally requires more physical qubits n for fixed k.

**Answer 9:**

Logical operators X\_L and Z\_L are Pauli operators (acting on the physical qubits) that implement the logical X and Z gates on the encoded qubit while preserving the code space — that is, they must commute with every stabiliser generator so that applying them keeps the state within the +1 eigenspace defining the code, but they must not themselves be stabiliser elements (otherwise they would act trivially on all codewords).

**Answer 10:**

A transversal gate acts independently and identically on corresponding qubits across code blocks (e.g. applying the same single-qubit gate to each of the 7 physical qubits), so that a single physical error on one qubit cannot propagate to corrupt multiple qubits within the same code block. The Steane code supports transversal implementation of the entire Clifford group, meaning these logical gates can be implemented fault-tolerantly without special error-propagation-control circuitry, greatly simplifying the design of a fault-tolerant processor (though non-Clifford gates like T still require magic state distillation).

**Answer 11:**

Prior to Shor's 1995 paper, prominent physicists (including Rolf Landauer and Bill Unruh) argued that decoherence made large-scale quantum computation physically impossible, since continuous, uncontrollable errors seemed unstoppable and no-cloning ruled out the classical remedy of redundant copying. Shor's demonstration that a 9-qubit code could correct arbitrary single-qubit errors — despite these apparent obstacles — is widely regarded (per John Preskill) as one of the most important results in the history of physics, transforming quantum computing from a theoretical curiosity into an engineering-feasible goal.

## A. Solved Problems

<div class="box box-generic">
<p class="box-title"><strong>Solved Example 1  Encoding and Error Detection with the 3-Qubit Bit-Flip Code</strong></p>
<p>Problem: The state |ψ⟩ = (√3/2)|0⟩ + (1/2)|1⟩ is encoded in the 3-qubit bit-flip code.</p>
<p>A bit-flip error occurs on qubit 2. Find the syndrome and perform the correction.</p>
<p>Step 1 — Encoding:  |ψ_L⟩ = (√3/2)|000⟩ + (1/2)|111⟩</p>
<p>Step 2 — Error on qubit 2 (X₂):</p>
<p>X₂|ψ_L⟩ = (√3/2)|010⟩ + (1/2)|101⟩</p>
<p>Step 3 — Syndrome measurement:</p>
<p>Measure Z₁Z₂:  Z|0⟩·Z|1⟩ = (+1)(−1) → eigenvalue −1 → s₁ = 1</p>
<p>Measure Z₂Z₃:  Z|1⟩·Z|0⟩ = (−1)(+1) → eigenvalue −1 → s₂ = 1</p>
<p>Syndrome = (1,1) → error on qubit 2  (from syndrome table)</p>
<p>Step 4 — Correction: Apply X₂:</p>
<p>X₂[(√3/2)|010⟩ + (1/2)|101⟩] = (√3/2)|000⟩ + (1/2)|111⟩ = |ψ_L⟩  ✓</p>
<p>Key: The syndrome (1,1) uniquely identifies qubit 2. The amplitudes √3/2 and 1/2 are NEVER revealed.</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Solved Example 2  Verifying the Knill-Laflamme Conditions for the 3-Qubit Code</strong></p>
<p>Problem: Verify that the 3-qubit bit-flip code satisfies KL conditions for X₁ and X₂ errors.</p>
<p>Codewords: |0_L⟩ = |000⟩,  |1_L⟩ = |111⟩</p>
<p>KL condition: ⟨i_L|E_k†E_l|j_L⟩ = C_{kl}δ_{ij}</p>
<p>⟨0_L|X₁†X₁|0_L⟩ = ⟨000|000⟩ = 1 = C₁₁  (after X₁†X₁ = I)</p>
<p>⟨1_L|X₁†X₁|1_L⟩ = ⟨111|111⟩ = 1 = C₁₁  ✓  (same — no codeword index dependence!)</p>
<p>⟨0_L|X₁†X₁|1_L⟩ = ⟨000|111⟩ = 0  ✓  (orthogonality preserved under X₁)</p>
<p>⟨0_L|X₁†X₂|0_L⟩ = ⟨100|010⟩ = 0 = C₁₂  (X₁†X₂ = X₁X₂ on |000⟩ gives |110⟩)</p>
<p>⟨1_L|X₁†X₂|1_L⟩ = ⟨011|101⟩ = 0 = C₁₂  ✓  (consistent)</p>
<p>All KL conditions satisfied: C matrix is the 2×2 identity; code correctly corrects X₁, X₂.</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Solved Example 3  Stabiliser Group for the 3-Qubit Phase-Flip Code</strong></p>
<p>Problem: Write the stabiliser group for the 3-qubit phase-flip code.</p>
<p>Codewords: |0_L⟩ = |+++⟩,  |1_L⟩ = |−−−⟩</p>
<p>Try g₁ = X₁X₂:</p>
<p>X₁X₂|+++⟩ = X|+⟩·X|+⟩·|+⟩ = |+⟩|+⟩|+⟩ = |+++⟩  ✓</p>
<p>X₁X₂|−−−⟩ = X|−⟩·X|−⟩·|−⟩ = (−|−⟩)(−|−⟩)|−⟩ = |−−−⟩  ✓</p>
<p>Try g₂ = X₂X₃: similarly stabilises both codewords  ✓</p>
<p>Stabiliser group: S = ⟨X₁X₂, X₂X₃⟩</p>
<p>Elements: {I, X₁X₂, X₂X₃, X₁X₃}  (4 elements = 2² ✓ for 2 generators)</p>
<p>This is exactly the Hadamard conjugate of bit-flip stabilisers {Z₁Z₂, Z₂Z₃},</p>
<p>confirming the X/Z symmetry: H⊗³ {Z₁Z₂,Z₂Z₃} H⊗³ = {X₁X₂,X₂X₃}.  ✓</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Solved Example 4  Quantum Hamming Bound: Minimum n for [[n,2,3]] Code</strong></p>
<p>Problem: Using the quantum Hamming bound, find minimum n to encode k=2 logical qubits</p>
<p>in a code that corrects t=1 error.</p>
<p>Quantum Hamming bound: 2^(n−k) ≥ Σ_{j=0}^{t} C(n,j) · 3^j</p>
<p>For t=1, k=2:  2^(n−2) ≥ 1 + 3n</p>
<p>Test n=6:  2^4 = 16   vs   1+18 = 19   →  16 &lt; 19  ✗  (impossible!)</p>
<p>Test n=7:  2^5 = 32   vs   1+21 = 22   →  32 ≥ 22  ✓  (possible)</p>
<p>Test n=8:  2^6 = 64   vs   1+24 = 25   →  64 ≥ 25  ✓</p>
<p>Minimum n = 7 qubits to encode k=2 logical qubits correcting 1 error.</p>
<p>Such a code exists: the [[7,2,3]] CSS code (a generalisation of the Steane code).</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Solved Example 5  Steane Code Syndrome Lookup for X Error on Qubit 3</strong></p>
<p>Problem: Use the Steane code parity-check matrix H to find the syndrome for X₃.</p>
<p>Steane code parity-check matrix H (Hamming [7,4,3]):</p>
<p>Row 1: [1 1 1 1 0 0 0]  ← positions of first check</p>
<p>Row 2: [0 1 1 0 1 1 0]</p>
<p>Row 3: [0 0 1 1 0 1 1]</p>
<p>Error X₃ → error vector e = [0 0 1 0 0 0 0]</p>
<p>Syndrome s = H · e^T  (mod 2):</p>
<p>s₁ = 1·0+1·0+1·1+1·0+0+0+0 = 1  (mod 2)</p>
<p>s₂ = 0·0+1·0+1·1+0+1·0+1·0+0 = 1  (mod 2)</p>
<p>s₃ = 0+0+1·1+1·0+0+1·0+1·0 = 1  (mod 2)</p>
<p>Syndrome = (1,1,1) = binary 7 in decimal → Column 3 of H = [1,1,1]^T = syndrome.  ✓</p>
<p>Correction: Apply X on qubit 3.</p>
</div>

## B. Unsolved Problems

## Chapter 5: Problems

**1.** The state (|0⟩+|1⟩)/√2 is encoded in the 3-qubit bit-flip code. Show that a Z₁ error cannot be detected (syndrome = (0,0)), demonstrating the code only protects against X errors.  *[Ans: Z₁(α|000⟩+β|111⟩)=α|000⟩−β|111⟩; Z₁Z₂ eigenvalue=(+1)(+1)=+1; Z₂Z₃=(+1)(+1)=+1; syndrome=(0,0) — Z error undetected]*

**2.** For the Shor 9-qubit code, identify which stabiliser generators detect a Y error on qubit 5.  *[Ans: Y₅=iX₅Z₅; X-part detected by Z-generators of block 2 (g₅=Z₄Z₅Z₆); Z-part detected by X-type outer generator; two syndromes triggered]*

**3.** Verify X₁X₂X₃X₄X₅X₆X₇ is the logical X operator of the Steane code by checking it commutes with all 6 stabilisers.  *[Ans: Commutes with X-stabilisers trivially (X·X=I); commutes with Z-stabilisers by even overlap count (4 in common → commute); weight 7 > max stab weight → not in S]*

**4.** Compute the number of stabiliser generators for a [[15,7,3]] Reed-Muller code. How many logical qubits does it encode?  *[Ans: n−k = 15−7 = 8 generators; encodes k=7 logical qubits]*

**5.** The 5-qubit code has generators g₁=XZZXI, g₂=IXZZX, g₃=XIXZZ, g₄=ZXIXZ. Verify g₁ and g₂ commute.  *[Ans: q2(Z,X)→anti, q3(Z,Z)→comm, q4(X,Z)→anti, q5(I,X)→comm; 2 anticommuting positions → even → [g₁,g₂]=0 ✓]*

**6.** Show that the encoding circuit for the 3-qubit phase-flip code is H⊗³ · (bit-flip encoding) · H⊗³.  *[Ans: H conjugates Z→X, X→Z; bit-flip code in Hadamard basis = phase-flip code by conjugation symmetry]*

**7.** For CSS(C₁,C₂) with C₁=[7,4,3] Hamming and C₂=C₁⊥=[7,3,4], verify [[n,k,d]].  *[Ans: n=7, k=4−3=1, d=min(3,4)=3 → [[7,1,3]] Steane code ✓]*

**8.** Prove that for any stabiliser code, X\_L and Z\_L must anticommute.  *[Ans: X\_L Z\_L = iY\_L, Z\_L X\_L = −iY\_L; sum = iY\_L − iY\_L = 0 → anticommute ✓]*

**9.** A stabiliser code has n=10 physical, k=2 logical, d=4. (a) generators? (b) errors corrected? (c) Hamming bound check?  *[Ans: (a) n−k=8; (b) t=⌊(d−1)/2⌋=1; (c) 2^8=256 ≥ 1+30=31 ✓]*

**10.** Explain the connection between the Gottesman-Knill theorem and the Eastin-Knill theorem.  *[Ans: G-K: Clifford circuits classically simulable; E-K: no transversal universal gate set; if all FT gates were transversal Clifford, QC would be classically simulable → useless; T gate essential]*

## C. Multiple Choice Questions

## Chapter 5 MCQs

<div class="box box-generic">
<p class="box-title"><strong>Q1.  The no-cloning theorem is proved by exploiting which property of quantum mechanics?</strong></p>
<p>(A)  Heisenberg uncertainty principle</p>
<p>(B)  Linearity (unitarity) of quantum operations</p>
<p>(C)  Pauli exclusion principle</p>
<p>(D)  Decoherence</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q2.  In the 3-qubit bit-flip code, syndrome (1,1) indicates an error on:</strong></p>
<p>(A)  Qubit 1</p>
<p>(B)  Qubit 2</p>
<p>(C)  Qubit 3</p>
<p>(D)  No error</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q3.  The 'discretisation miracle' in QEC refers to:</strong></p>
<p>(A)  Digital computers can simulate quantum errors</p>
<p>(B)  Syndrome measurement collapses continuous errors to a discrete set</p>
<p>(C)  Quantum errors occur at discrete time intervals</p>
<p>(D)  Error rates are naturally small in quantum hardware</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q4.  The Knill-Laflamme condition ⟨i_L|E_k†E_l|j_L⟩ = C_{kl}δ_{ij} requires:</strong></p>
<p>(A)  All errors must be unitary</p>
<p>(B)  Codewords remain orthogonal under all correctable errors</p>
<p>(C)  Code must use ≥9 physical qubits</p>
<p>(D)  Syndrome measurement is O(n²)</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q5.  The Shor 9-qubit code has parameters:</strong></p>
<p>(A)  [[9,1,1]]</p>
<p>(B)  [[9,3,3]]</p>
<p>(C)  [[9,1,3]]</p>
<p>(D)  [[9,1,9]]</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q6.  A stabiliser group S must satisfy which property?</strong></p>
<p>(A)  All elements are Hermitian</p>
<p>(B)  S is Abelian and does not contain −I</p>
<p>(C)  S acts on at most 3 qubits</p>
<p>(D)  S contains only X-type operators</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q7.  For an [[n,k,d]] stabiliser code, the number of independent generators is:</strong></p>
<p>(A)  k</p>
<p>(B)  d</p>
<p>(C)  n−k</p>
<p>(D)  n+k</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q8.  The Steane [[7,1,3]] code is based on which classical code?</strong></p>
<p>(A)  [7,4,3] Hamming code</p>
<p>(B)  [7,3,4] dual Hamming</p>
<p>(C)  [6,3,4] Hexacode</p>
<p>(D)  [15,7,5] BCH</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q9.  Transversal gates on the Steane code include all EXCEPT:</strong></p>
<p>(A)  Hadamard (H)</p>
<p>(B)  Phase gate (S)</p>
<p>(C)  CNOT</p>
<p>(D)  T gate (π/8 rotation)</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q10.  The quantum Hamming bound gives minimum n for [[n,1,3]] code as:</strong></p>
<p>(A)  n=4</p>
<p>(B)  n=5</p>
<p>(C)  n=6</p>
<p>(D)  n=7</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q11.  The Eastin-Knill theorem states that:</strong></p>
<p>(A)  Gottesman-Knill theorem is wrong</p>
<p>(B)  No stabiliser code has a transversal universal gate set</p>
<p>(C)  All Clifford gates are non-transversal</p>
<p>(D)  T gate has lower fidelity than H</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q12.  Syndrome measurement of Z₁Z₂ reveals:</strong></p>
<p>(A)  Whether qubit 1 is |0⟩ or |1⟩</p>
<p>(B)  Whether an X error occurred on qubit 1 or 2 (relative parity)</p>
<p>(C)  Value of both qubits simultaneously</p>
<p>(D)  Entanglement entropy between qubits 1 and 2</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q13.  The 5-qubit perfect code is 'perfect' because it:</strong></p>
<p>(A)  Has zero error rate</p>
<p>(B)  Uses minimum qubits to correct all single-qubit errors (saturates Hamming bound)</p>
<p>(C)  Has all transversal gates</p>
<p>(D)  Can be decoded in O(1) time</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q14.  In CSS code CSS(C₁,C₂), which condition on the classical codes is required?</strong></p>
<p>(A)  C₁ = C₂</p>
<p>(B)  C₂ ⊆ C₁</p>
<p>(C)  C₁ ⊥ C₂</p>
<p>(D)  dim(C₁) = dim(C₂)</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q15.  Peter Shor's quantum error correction paper was published in:</strong></p>
<p>(A)  1993</p>
<p>(B)  1994</p>
<p>(C)  1995</p>
<p>(D)  1997</p>
</div>

## D. Theory Questions

## Chapter 5: Theory Questions

**Q1.** State and prove the no-cloning theorem. Why does this make classical error correction strategies directly inapplicable to quantum computing? What encoding strategy does QEC use instead?

**Q2.** Explain the 'discretisation miracle' of quantum error correction. Why does syndrome measurement collapse a continuous error (e.g., rotation by angle θ) into a discrete, correctable error?

**Q3.** Describe the 3-qubit bit-flip code in full detail: encoding circuit, error model, syndrome operators, syndrome table, and correction circuit. Identify the two stabiliser generators.

**Q4.** State the Knill-Laflamme quantum error correction conditions. Explain the physical meaning of BOTH conditions (the δ\_{ij} condition and the C\_{kl} condition). Why are they necessary AND sufficient?

**Q5.** Define the Pauli group P\_n and a stabiliser code. Why must the stabiliser group be Abelian, and why is −I excluded? How does the number of generators determine the number of logical qubits?

**Q6.** Describe the Shor 9-qubit code. How does the concatenated structure protect against both X and Z errors? Express the logical codewords explicitly and identify the 8 stabiliser generators.

**Q7.** Explain the CSS code construction. What are the requirements on classical codes C₁ and C₂? Show that the X and Z stabilisers of a CSS code automatically commute.

**Q8.** Describe the [[7,1,3]] Steane code: stabilisers, logical operators, code distance, and transversal gates. Why does the Steane code admit transversal Clifford gates?

**Q9.** State the quantum Hamming bound. Prove the 5-qubit code is the smallest code correcting all single-qubit errors. What makes it 'perfect'?

**Q10.** State the Eastin-Knill theorem. Why is the T gate non-transversal for stabiliser codes? What does this imply about fault-tolerant universal computation overhead?

## E. Programming / Research Assignments

<div class="box box-real-world">
<p class="box-title"><strong>🌐  Assignment 1: Simulate the 3-Qubit Bit-Flip Code in Qiskit</strong></p>
<p>Implement a complete simulation of the 3-qubit bit-flip code in Qiskit:</p>
<p>(a) Build the encoding circuit for state (α|0⟩+β|1⟩)|00⟩</p>
<p>(b) Insert X errors on each qubit separately using barrier + gate</p>
<p>(c) Implement syndrome measurement using 2 ancilla qubits (measuring Z₁Z₂ and Z₂Z₃)</p>
<p>(d) Build classical correction logic based on syndrome outcome</p>
<p>(e) Verify recovery by computing state fidelity with the original state</p>
<p>(f) Run 1000 shots with p_error = 0.1 on each qubit; plot logical vs physical error rate</p>
<p>(g) Compare to theoretical bound: P(logical error) ≈ 3p² for the 3-qubit code</p>
<p>Bonus: Implement the 3-qubit phase-flip code and test on Z errors.</p>
<p>Deliverable: Jupyter notebook with all circuits, plots, and 2-page analysis.</p>
</div>

<div class="box box-real-world">
<p class="box-title"><strong>🌐  Assignment 2: Steane Code Deep Dive</strong></p>
<p>Perform a complete analysis of the [[7,1,3]] Steane code:</p>
<p>(a) Write all 6 stabiliser generators explicitly as tensor products of Pauli operators</p>
<p>(b) Construct the full syndrome lookup table for all single-qubit X and Z errors (14 cases)</p>
<p>(c) Verify logical operators X_L=X⊗7 and Z_L=Z⊗7 commute with all stabilisers</p>
<p>(d) Show that X_L and Z_L anticommute: X_L Z_L = −Z_L X_L</p>
<p>(e) Implement the Steane code encoding circuit in Qiskit (7 qubits)</p>
<p>(f) Insert X₃ and Z₅ errors and run syndrome measurement; verify correct identification</p>
<p>(g) Verify the Knill-Laflamme conditions numerically for 3 random error pairs</p>
<p>Deliverable: Mathematical derivation report (3 pages) + Qiskit implementation.</p>
</div>

## References and Further Reading — Chapter 5

## Chapter 5 References

- Shor, P.W. (1995). Scheme for reducing decoherence in quantum computer memory. Physical Review A, 52, R2493.

- Steane, A.M. (1996). Error Correcting Codes in Quantum Theory. Physical Review Letters, 77(5), 793–797.

- Knill, E. & Laflamme, R. (1997). Theory of quantum error-correcting codes. Physical Review A, 55(2), 900–911.

- Gottesman, D. (1997). Stabilizer Codes and Quantum Error Correction. PhD Thesis, Caltech.

- Calderbank, A.R. & Shor, P.W. (1996). Good quantum error-correcting codes exist. Physical Review A, 54(2), 1098–1105.

- Wootters, W.K. & Zurek, W.H. (1982). A single quantum cannot be cloned. Nature, 299, 802–803.

- Eastin, B. & Knill, E. (2009). Restrictions on Transversal Encoded Quantum Gate Sets. Physical Review Letters, 102, 110502.

- Nielsen, M.A. & Chuang, I.L. (2010). Quantum Computation and Quantum Information. Cambridge University Press. Chapter 10.
