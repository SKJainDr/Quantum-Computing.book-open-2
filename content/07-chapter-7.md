# CHAPTER 7

# QAOA, VQE for Molecular Systems & Optimiser Strategies

*Cost & Mixer Hamiltonians  |  MaxCut  |  UCCSD  |  Active Space  |  CCSD(T)  |  COBYLA / SPSA / ADAM*

<div class="box box-anecdote">
<p class="box-title"><strong>📜  Opening Reflection — Edward Farhi, MIT, 2014</strong></p>
<p>"The variational principle is perhaps the most powerful single idea in physics.</p>
<p>The best approximate answer is the one that minimises the energy.</p>
<p>Quantum computers let us explore a vastly richer set of trial states than classical computers ever could."</p>
<p>— Edward Farhi, shortly before introducing QAOA</p>
</div>

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  Chapter 7 Learning Objectives</strong></p>
<p>After studying this chapter, you will be able to:</p>
<p>•  Formulate the QAOA circuit for general combinatorial optimisation problems</p>
<p>•  Define cost and mixer Hamiltonians and construct p-layer QAOA circuits</p>
<p>•  Apply QAOA to MaxCut, portfolio optimisation, and TSP</p>
<p>•  Explain the theoretical guarantees and known limits of QAOA at p = 1</p>
<p>•  Describe VQE with UCCSD ansatz for molecular systems in Qiskit Nature</p>
<p>•  Understand active space approximation, CCSD(T) benchmarking, and convergence</p>
<p>•  Compare classical optimiser strategies: COBYLA, SPSA, ADAM, gradient-free vs gradient-based</p>
<p>•  Analyse the shot-noise budget and its impact on optimiser convergence</p>
</div>

## 7.1 The NISQ Era and the Variational Approach

We are currently in the Noisy Intermediate-Scale Quantum (NISQ) era — quantum processors with 50–1000 qubits but without full quantum error correction. In this regime, the deep coherent circuits needed for Shor's algorithm are unavailable. Variational Quantum Algorithms (VQAs) are designed specifically for NISQ hardware: they use shallow, parametrised circuits and offload optimisation to a classical co-processor.

<div class="box box-anecdote">
<p class="box-title"><strong>📜  The NISQ Term — John Preskill, 2018</strong></p>
<p>The term 'NISQ' (Noisy Intermediate-Scale Quantum) was coined by John Preskill in his landmark 2018 paper 'Quantum</p>
<p>Computing in the NISQ Era and Beyond'. Rather than waiting decades for fault-tolerant quantum computers, Preskill</p>
<p>encouraged the community to ask: what useful computations might be achievable on noisy near-term hardware?</p>
<p>The paper immediately reoriented the entire field. Within a year, NISQ had become the dominant framing for quantum</p>
<p>computing research and investment worldwide — spawning hundreds of papers on variational algorithms.</p>
<p>In 2023, Preskill revised his optimism cautiously, noting that many NISQ quantum advantage claims had not held up</p>
<p>under classical scrutiny, but maintained that the NISQ era had been enormously valuable for building hardware understanding.</p>
</div>

The core idea of a Variational Quantum Algorithm is the hybrid quantum-classical loop shown below. Every VQA — from VQE to QAOA to quantum neural networks — follows this same four-step structure:

- A parametrised quantum circuit (the ansatz) prepares a trial state |ψ(θ)⟩.

- The quantum processor measures the cost function: C(θ) = ⟨ψ(θ)|H|ψ(θ)⟩.

- A classical optimiser updates the parameters θ to minimise (or maximise) C(θ).

- Repeat until convergence. Output the optimal parameters θ\* and the solution.

<img class="fig-img" src="content/images/image23.png" alt="figure">

**Figure 1: VQA Hybrid Classical-Quantum Loop** *— The quantum processor evaluates cost values; the classical optimiser drives parameter updates toward the minimum*

## 7.2 The Quantum Approximate Optimisation Algorithm (QAOA)

QAOA (Farhi, Goldstone & Gutmann, 2014) is a variational algorithm specifically designed for combinatorial optimisation problems. It is inspired by adiabatic quantum computing: a system slowly evolved from an easy initial state to the ground state of a hard problem Hamiltonian. QAOA truncates this adiabatic path into p discrete layers.

### 7.2.1 General QAOA Framework

Given a combinatorial optimisation problem with n binary variables, we encode it as a diagonal cost Hamiltonian H\_C acting on n qubits. We also define a mixer Hamiltonian H\_B that generates transitions between computational basis states. The QAOA ansatz is:

**|ψ(γ,β)⟩ = e^(−iβ\_p H\_B) e^(−iγ\_p H\_C) ··· e^(−iβ₁ H\_B) e^(−iγ₁ H\_C) |+⟩^⊗n**

where |+⟩^⊗n = H^⊗n|0^n⟩ is the uniform superposition (ground state of the standard mixer H\_B = Σⱼ Xⱼ), and (γ,β) = (γ₁,...,γ\_p, β₁,...,β\_p) are the 2p variational parameters.

<div class="box box-generic">
<p class="box-title"><strong>Definition 7.1  QAOA Circuit Structure</strong></p>
<p>Parameters:  p layers,  angles (γ₁,...,γ_p) for phase separation,  (β₁,...,β_p) for mixing</p>
<p>Initialisation:  |ψ₀⟩ = H^⊗n |0^n⟩  =  (1/√2^n) Σ_z |z⟩   (uniform superposition)</p>
<p>Phase separation layer k:   U_C(γ_k) = e^(−iγ_k H_C)  —  applies cost as a quantum phase</p>
<p>Mixing layer k:              U_B(β_k)  = e^(−iβ_k H_B)  —  generates transitions between states</p>
<p>Output state:  |ψ_p(γ,β)⟩ = U_B(β_p)U_C(γ_p) ··· U_B(β₁)U_C(γ₁)|ψ₀⟩</p>
<p>Objective:  maximise  ⟨ψ_p(γ,β)|H_C|ψ_p(γ,β)⟩  by optimising  (γ,β)  classically</p>
<p>Key property:  as p → ∞, QAOA converges to the exact optimal solution</p>
<p>(follows from the quantum adiabatic theorem applied to the discrete schedule).</p>
</div>

### 7.2.2 QAOA Circuit for MaxCut — K₃ Example

For MaxCut on a graph G = (V,E), the cost Hamiltonian is H\_C = Σ\_{(i,j)∈E} w\_{ij}(I − Z\_i Z\_j)/2. Each ZZ interaction is implemented as a CNOT–Rz(2γ)–CNOT sequence, and the mixer e^(−iβ Xⱼ) = Rx(2β) is a simple single-qubit rotation. The full circuit for the 3-node triangle K₃ at p = 1 is shown below:

<img class="fig-img" src="content/images/image24.png" alt="figure">

**Figure 2: QAOA p=1 Circuit for MaxCut on K₃ (Triangle Graph)** *— Hadamard initialisation, ZZ phase separation on each of 3 edges, X-rotation mixing per vertex; 6 CNOT gates total*

<div class="box box-warning">
<p class="box-title"><strong>⚠  QAOA at p=1 vs Classical Goemans-Williamson</strong></p>
<p>QAOA at p = 1 achieves an approximation ratio of 11/16 ≈ 0.6875 for MaxCut on 3-regular unweighted graphs.</p>
<p>This is a PROVEN result (Farhi, Goldstone &amp; Gutmann 2014).</p>
<p>However, the classical Goemans-Williamson SDP algorithm achieves ≥ 0.8786 × OPT — significantly better.</p>
<p>⚠  This means QAOA at p=1 is provably WORSE than the best classical algorithm for MaxCut.</p>
<p>Larger p values improve QAOA's approximation ratio, but no finite p is proven to beat GW.</p>
<p>This is one of the most important limitations of near-term QAOA to understand.</p>
</div>

## 7.3 QAOA Applications: MaxCut, Portfolio Optimisation, and TSP

### 7.3.1 MaxCut Problem Formulation

MaxCut is the canonical NP-hard problem for QAOA. Given a graph G = (V,E) with edge weights w\_{ij}, find a partition of vertices into two sets S and S̄ that maximises the total cut weight.

**MaxCut: maximise  C(z) = Σ\_{(i,j)∈E} w\_{ij} (1 − z\_i z\_j)/2,   z\_i ∈ {±1}**

This maps to the quantum Hamiltonian H\_C = Σ\_{(i,j)∈E} w\_{ij}(I − Z\_i Z\_j)/2. QAOA maximises the expected value of H\_C over the variational state.

### 7.3.2 Portfolio Optimisation

Portfolio optimisation maps directly to QUBO (Quadratic Unconstrained Binary Optimisation), which QAOA handles naturally. Given n assets with expected returns μ and covariance matrix Σ, the discrete problem is: minimise w^TΣw − λμ^Tw subject to w ∈ {0,1}^n. This requires n qubits — feasible for NISQ up to n ≈ 50 assets.

### 7.3.3 Travelling Salesman Problem

The TSP requires n² binary variables (x\_{i,p} = 1 if city i is visited at position p), encoded as: H\_TSP = A·(constraint violations) + B·(total distance). For n = 10 cities this needs 100 qubits — at the edge of current NISQ hardware.

| Problem | Qubits | Best Classical | QAOA p=1 Status |
|---|---|---|---|
| MaxCut (n vertices) | n | GW SDP: ≥ 0.878×OPT | 11/16 ≈ 0.69×OPT (proven) |
| Portfolio (n assets) | n | Exact via Gurobi MILP | NISQ feasible n≤50; no proven speedup |
| TSP (n cities) | n² | Christofides: 1.5×OPT | n≤10 feasible; quality poor |
| Max-k-SAT (n vars) | n | Best SDP-based classical | Natural formulation; no proven advantage |

## 7.4 VQE for Molecular Systems: UCCSD, Active Space & Convergence

The Variational Quantum Eigensolver (VQE) targets the electronic structure problem — finding the lowest-energy configuration of electrons in a molecule. This is classically intractable for large systems (FCI scales exponentially) but VQE can, in principle, solve it using a quantum processor.

| Molecule | Electrons | STO-3G Orbitals | FCI Determinants | Classical FCI? |
|---|---|---|---|---|
| H₂ | 2 | 4 | 6 | Trivial |
| LiH | 4 | 12 | ~225 | Easy |
| H₂O | 10 | 24 | ~10⁶ | Manageable |
| N₂ | 14 | 28 | ~10⁸ | Challenging |
| FeMoco (N₂ fixation) | ~54 | ~54 | ~10²⁰ | Impossible classically |

### 7.4.1 The Active Space Approximation CAS(m,n)

For large molecules, the full orbital space cannot fit on a quantum computer. The Complete Active Space (CAS) approximation selects a chemically relevant subset: m electrons in n orbitals. Core orbitals (always occupied) and high-energy virtual orbitals are frozen, dramatically reducing qubit count.

<div class="box box-real-world">
<p class="box-title"><strong>🌐  Why Active Space Selection Is the Key Design Decision in VQE</strong></p>
<p>The active space CAS(m,n) requires 2n qubits (Jordan-Wigner mapping, factor 2 for spin).</p>
<p>For FeMoco (the nitrogen-fixation enzyme active site): CAS(54,54) → 108 qubits.</p>
<p>Rules of thumb for active space selection:</p>
<p>• Include all orbitals within ~1 eV of the HOMO-LUMO gap</p>
<p>• For bond-breaking: include the bonding/antibonding pair of the breaking bond</p>
<p>• For transition metals: include d-orbitals and strongly coupled ligand orbitals</p>
<p>Consequences of wrong choice:</p>
<p>Too small → miss important electron correlation → inaccurate energy</p>
<p>Too large → too many qubits → circuit too deep for NISQ hardware</p>
</div>

### 7.4.2 The UCCSD Ansatz

The Unitary Coupled Cluster Singles and Doubles (UCCSD) ansatz is the gold standard for quantum chemistry VQE. The unitary operator is:

**U\_UCCSD(θ) = exp( T̂₁(θ) + T̂₂(θ) − T̂₁†(θ) − T̂₂†(θ) )**

where T̂₁ = Σ\_{ia} θ\_i^a a†\_a a\_i (single excitations from occupied orbital i to virtual a) and T̂₂ = Σ\_{ijab} θ\_{ij}^{ab} a†\_a a†\_b a\_j a\_i (double excitations). The Jordan-Wigner mapping converts these fermionic operators to qubit Pauli strings.

<div class="box box-anecdote">
<p class="box-title"><strong>📜  The Birth of VQE — A Photonic Chip and the First Molecule</strong></p>
<p>VQE was invented by Alberto Peruzzo, a photonics experimentalist in Jeremy O'Brien's group at Bristol,</p>
<p>collaborating with Alán Aspuru-Guzik's theoretical chemistry group at Harvard.</p>
<p>Their 2014 Nature Communications paper demonstrated VQE on a 2-qubit photonic chip for the helium</p>
<p>hydride ion (HeH⁺). The result was modest in scale but profound in concept: for the first time, a</p>
<p>variational quantum algorithm had computed a molecular ground state energy experimentally.</p>
<p>Aspuru-Guzik later described it as 'the most important paper I've ever been involved in, bar none.'</p>
<p>The HeH⁺ molecule has a beautiful historical significance: it is believed to be the FIRST molecule</p>
<p>that ever formed in the universe — about 100,000 years after the Big Bang, during recombination,</p>
<p>when hydrogen and helium nuclei first combined. A quantum computer simulated the universe's</p>
<p>first molecule. Poetry in physics.</p>
</div>

### 7.4.3 Classical Benchmarks: CCSD(T)

| Method | Scaling | Accuracy | Applicability |
|---|---|---|---|
| Hartree-Fock (HF) | O(N³–N⁴) | Poor for correlated systems | Initial guess; reference state |
| MP2 | O(N⁵) | Good for weakly correlated | Small-medium molecules |
| CCSD | O(N⁶) | Chemical accuracy (~1 kcal/mol) | Up to ~100 atoms |
| CCSD(T)  ← gold standard | O(N⁷) | Sub-chemical accuracy | Up to ~50 atoms |
| FCI | Exponential | Exact | ≤18 electrons classically |
| VQE + UCCSD (quantum) | O(N⁴) gates | FCI quality (in principle) | Strongly correlated; large active spaces |

## 7.5 Classical Optimiser Strategies for Variational Algorithms

The choice of classical optimiser is critical for VQA performance. The optimiser must find the minimum of C(θ) using only noisy, finite-sample measurements from quantum hardware. The key challenges are: shot noise, local minima in rugged landscapes, and barren plateaus (the subject of Chapter 8).

### 7.5.1 The Parameter Shift Rule: Exact Quantum Gradients

<div class="box box-generic">
<p class="box-title"><strong>Theorem 7.2  Parameter Shift Rule (Mitarai et al. 2018; Schuld et al. 2019)</strong></p>
<p>For a parametrised gate U(θ_k) = e^(−iθ_k G/2) where G has eigenvalues ±1</p>
<p>(satisfied by all Pauli rotation gates Rx, Ry, Rz), the exact gradient is:</p>
<p>∂C/∂θ_k  =  [ C(θ_k + π/2) − C(θ_k − π/2) ] / 2</p>
<p>This is an EXACT derivative — not a finite-difference approximation.</p>
<p>It requires only 2 circuit evaluations per parameter.</p>
<p>Proof sketch: C(θ_k) is a sinusoidal function of θ_k (C = A + B sin(θ_k + φ)).</p>
<p>Therefore ∂C/∂θ_k = B cos(θ_k + φ) = [C(θ_k+π/2) − C(θ_k−π/2)] / 2.  □</p>
<p>Total gradient cost: 2|θ| circuit evaluations per gradient step.</p>
<p>Advantage over finite differences: exact, not limited by numerical step-size.</p>
</div>

### 7.5.2 Gradient-Free Optimisers

| Optimiser | Type | Key Idea | Best Use Case |
|---|---|---|---|
| COBYLA | Gradient-free, trust-region | Linear approx. in trust region | Small params; robust to noise |
| Nelder-Mead | Simplex | Evolves n+1-point simplex | Very few parameters (<20) |
| SPSA | Stochastic approx. | 2 evaluations → full gradient estimate | NISQ hardware; noise-tolerant |
| Bayesian Opt. | Surrogate model (GPR) | Acquisition function guides search | Data-efficient; global |

### 7.5.3 Gradient-Based Optimisers

| Optimiser | Type | Update Rule | Use Case |
|---|---|---|---|
| Gradient Descent | 1st order | θ ← θ − η∇C(θ) | Simplest; often too slow |
| ADAM | Adaptive moments | θ ← θ − η m̂_t / (√v̂_t + ε) | Best general-purpose; momentum + adaptive lr |
| SPSA | Stochastic approx. | Simultaneous perturbation; 2 evals | Best on noisy hardware; O(1) per step |
| QNG (Quantum Natural Grad.) | 2nd order geometry | θ ← θ − ηF⁻¹∇C; F = Fubini-Study metric | Faster convergence; F computation costly |

<div class="box box-warning">
<p class="box-title"><strong>⚠  Practical Optimiser Recommendations for NISQ VQE</strong></p>
<p>• &lt;50 parameters:  COBYLA — robust, few shots per evaluation, no gradient needed</p>
<p>• 50–500 parameters:  SPSA — only 2 evaluations per step regardless of parameter count</p>
<p>• &gt;500 parameters:  ADAM with parameter shift rule — adaptive learning rate</p>
<p>• Chemistry VQE:  warm-start from classical CCSD solution → dramatically faster convergence</p>
<p>• Always:  apply readout error mitigation (measurement error correction) — it is cheap</p>
<p>• Watch out:  if gradient variance is tiny (&lt;10⁻⁶), you have hit a barren plateau (see Ch.8)</p>
</div>

## 7.6 Hardware-Efficient Ansatz: When UCCSD Is Too Deep

The UCCSD ansatz generates circuits of depth O(N⁴) — too deep for NISQ hardware on large systems. Hardware-efficient ansatze use shallower circuits with gates native to the quantum processor, sacrificing chemical motivation for circuit depth reduction.

| Ansatz | CNOT Count | Parameters | Expressibility | Best For |
|---|---|---|---|---|
| UCCSD | O(N⁴) | O(N²) | Chemically motivated | Small molecules; chemical accuracy |
| SU(2) hardware-efficient | O(L·N) | O(L·N) | Universal for large L | NISQ; hardware-native |
| ADAPT-VQE | Grows iteratively | Grows iteratively | Problem-adapted | Optimal tradeoff |
| k-UpCCGSD | O(kN²) | O(kN²) | Approximates UCCSD | Medium molecules |

<div class="box box-warning">
<p class="box-title"><strong>⚠  Expressibility Does NOT Equal Accuracy</strong></p>
<p>A highly expressible ansatz (one that can represent any quantum state) is NOT necessarily better</p>
<p>for VQE than a chemically motivated but less general ansatz like UCCSD.</p>
<p>Reason 1: High expressibility correlates with BARREN PLATEAUS (Chapter 8 explains this in detail).</p>
<p>Random, expressive circuits have exponentially small gradients — making them untrainable.</p>
<p>Reason 2: The variational principle guarantees a lower bound on energy ONLY IF the true ground</p>
<p>state is representable by the ansatz. UCCSD represents the right chemistry; SU(2) may not.</p>
<p>Best practice: Start with UCCSD for chemical accuracy; use SU(2) only when circuit depth is the</p>
<p>hard bottleneck. Never expect SU(2) to match CCSD(T) accuracy.</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>📋  Chapter 7 Summary</strong></p>
<p>VQA paradigm:          Hybrid loop — quantum circuit evaluates cost; classical optimiser updates θ</p>
<p>QAOA circuit:          p layers of U_C(γ) U_B(β); 2p parameters; H_C cost + H_B mixer</p>
<p>QAOA for MaxCut:       p=1 achieves 11/16 × OPT for 3-regular graphs; GW wins at 0.878</p>
<p>QAOA applications:     Portfolio, TSP, MaxSAT: all as QUBO → diagonal H_C</p>
<p>VQE active space:      CAS(m,n) selects m electrons in n orbitals; 2n qubits (JW)</p>
<p>UCCSD ansatz:          Singles + doubles excitations; O(N⁴) CNOTs; CCSD(T) is benchmark</p>
<p>Parameter shift rule:  ∂C/∂θ = [C(θ+π/2)−C(θ−π/2)]/2  exact gradient; 2 circuits per param</p>
<p>SPSA optimiser:        O(1) gradient estimate; 2 evaluations per step; best for noisy hardware</p>
<p>ADAM:                  Adaptive moments; best general-purpose gradient-based optimiser</p>
<p>Hardware-efficient:    Shallow circuits; expressible but may suffer barren plateaus (Ch.8)</p>
</div>

## RECAP — SHORT ANSWER QUESTIONS & MODEL ANSWERS

Chapter 7: Advanced Variational and Hybrid Algorithms

Instructions: Answer each question in 3–6 lines. Each question carries equal marks.

**PART A — QUESTIONS**

**Q1.  What defines the 'NISQ era', and why does it motivate variational (hybrid) quantum algorithms?**

**Q2.  Describe the structure of the Quantum Approximate Optimisation Algorithm (QAOA).**

**Q3.  Give three application domains for QAOA discussed in this chapter.**

**Q4.  What is 'active space' selection in VQE for molecular systems, and why is it necessary?**

**Q5.  How is CCSD(T) used as a benchmark for VQE molecular energy calculations?**

**Q6.  What is a barren plateau and why does it threaten the scalability of variational algorithms?**

**Q7.  What classical optimiser considerations matter when training variational quantum circuits, and why is COBYLA often used?**

**Q8.  What is a hardware-efficient ansatz, and when is it preferred over UCCSD?**

**Q9.  Explain the parameter-shift rule and why it gives exact (not approximate) gradients for quantum circuits.**

**Q10.  What is the adiabatic theorem's connection to QAOA, and under what conditions does QAOA approach adiabatic optimality?**

**Q11.  Why might a p-layer QAOA circuit for a large graph nonetheless fail to find a good MaxCut solution on NISQ hardware?**

**Q12.  Summarise the trade-offs between UCCSD and hardware-efficient ansätze for near-term quantum chemistry.**

**PART B — MODEL ANSWERS**

**Answer 1:**

The NISQ (Noisy Intermediate-Scale Quantum) era refers to current devices with tens to a few thousand qubits, no error correction, and non-negligible gate errors and limited coherence times, which together limit achievable circuit depth. Variational algorithms are designed for this regime because they use short, parametrised quantum circuits combined with a classical optimiser, keeping circuit depth low enough to be run reliably on noisy hardware while still exploring a rich space of quantum states.

**Answer 2:**

QAOA prepares a state by alternately applying a problem-specific cost unitary e^{−iγH\_C} (encoding the objective function to be optimised, e.g. MaxCut) and a mixer unitary e^{−iβH\_M} (typically transverse-field X rotations) for p layers, with 2p classical parameters (γ, β) tuned by a classical optimiser to minimise the expected cost. As p increases, QAOA can in principle approach the adiabatic-theorem limit and find better approximate solutions, at the cost of deeper circuits.

**Answer 3:**

QAOA formulations are given for MaxCut (graph partitioning), portfolio optimisation (selecting an asset allocation subject to risk/return trade-offs, formulated as a QUBO), and the travelling salesman problem (route optimisation) — all combinatorial optimisation problems that can be encoded as Ising-type cost Hamiltonians suitable for the QAOA cost/mixer structure.

**Answer 4:**

Active space selection restricts the VQE simulation to a chosen subset of molecular orbitals believed to be most chemically relevant (e.g. valence orbitals near the Fermi level), freezing core orbitals and truncating high-energy virtual orbitals. This is necessary because the number of qubits required scales with the number of spin-orbitals included, and current NISQ hardware cannot support the full orbital space of all but the smallest molecules, so active space selection is essential for keeping problems tractable while retaining chemical accuracy for the properties of interest.

**Answer 5:**

CCSD(T) (Coupled Cluster with Singles, Doubles, and perturbative Triples) is considered the classical 'gold standard' method for high-accuracy molecular energy calculations for small to medium molecules. VQE results (e.g. using UCCSD ansätze via Qiskit Nature) are benchmarked against CCSD(T) energies to assess whether the quantum calculation achieves 'chemical accuracy' (typically within about 1 milli-Hartree), providing a rigorous classical yardstick for judging near-term quantum chemistry results.

**Answer 6:**

A barren plateau is a region of the parameter landscape where the cost function's gradient becomes exponentially small in the number of qubits, meaning the number of measurement shots required to resolve a useful gradient direction grows exponentially with system size. This makes gradient-based (and even many gradient-free) training of variational circuits intractable at scale, since classical optimisers effectively see a flat, uninformative landscape almost everywhere.

**Answer 7:**

Variational circuit optimisation must contend with noisy cost function evaluations (finite shot noise), potentially non-smooth landscapes, and expensive function evaluations (each requiring a full quantum circuit execution), favouring optimisers that are robust to noise and sample-efficient. COBYLA (Constrained Optimisation BY Linear Approximation), a gradient-free optimiser, is often preferred in NISQ settings because it does not require potentially noisy or expensive gradient estimates and tends to be robust to the stochastic noise inherent in quantum measurement outcomes.

**Answer 8:**

A hardware-efficient ansatz is built from native hardware gates (single-qubit rotations and the device's native two-qubit entangling gate) arranged in repeated layers matching the qubit connectivity, rather than being derived from a physically motivated operator like UCCSD. It is preferred when UCCSD's circuit is too deep for available coherence times or when the specific chemical/physical structure UCCSD exploits is not critical to the task, trading some physical interpretability and convergence guarantees for significantly shallower, more hardware-friendly circuits.

**Answer 9:**

For a gate parameter θ appearing as a rotation generated by an operator with eigenvalues ±1/2, the parameter-shift rule gives the exact analytic gradient as ∂E/∂θ = [E(θ+π/2) − E(θ−π/2)]/2 — a finite difference at a specific, non-infinitesimal shift, rather than an approximation. This is possible because the expectation value of a Pauli rotation is exactly sinusoidal in θ, so the derivative of a sinusoid can be obtained exactly from two well-chosen evaluation points, unlike classical finite-difference gradients which are only approximate.

**Answer 10:**

The adiabatic theorem states that a quantum system initialised in the ground state of an easy-to-prepare Hamiltonian will remain in the instantaneous ground state if the Hamiltonian is varied sufficiently slowly toward a target (problem) Hamiltonian. As the number of QAOA layers p → ∞ with appropriately scheduled angles, QAOA's alternating cost/mixer structure can be shown to approximate a discretised adiabatic path, in principle allowing it to reach the optimal solution — though in practice only finite (often shallow) p is feasible, and the discrete-p QAOA does not generally inherit the same optimality guarantees as the continuous adiabatic limit.

**Answer 11:**

Increasing p to improve the theoretical approximation ratio also increases circuit depth, increasing the accumulated effect of gate errors and decoherence, and can push the optimisation landscape toward barren-plateau-like behaviour, making the classical optimisation step harder even if the ideal (noiseless) circuit would perform better. There is thus a practical trade-off between the theoretical benefit of more layers and the practical cost of deeper, noisier, and harder-to-train circuits on real NISQ devices.

**Answer 12:**

UCCSD is physically motivated, respects known symmetries (e.g. particle number), and offers systematic convergence toward chemical accuracy but produces circuits that are often too deep for current coherence times, especially for larger active spaces. Hardware-efficient ansätze are shallower and better matched to device connectivity, improving trainability and reducing noise accumulation, but they can lack physical structure, sometimes converge to unphysical states, and are more prone to barren plateaus without additional symmetry-preserving constraints — so the choice depends on the specific molecule size and target hardware.

## A. Solved Problems

<div class="box box-generic">
<p class="box-title"><strong>Solved Example 1  QAOA Circuit for MaxCut on K₃ at p=1</strong></p>
<p>Problem: Write the explicit QAOA circuit for MaxCut on K₃ (3-node triangle, unit weights)</p>
<p>at p=1. Identify cost and mixer Hamiltonians and the gate sequence.</p>
<p>Cost Hamiltonian:  H_C = (I−Z₀Z₁)/2 + (I−Z₁Z₂)/2 + (I−Z₀Z₂)/2  =  (3/2)I − (Z₀Z₁+Z₁Z₂+Z₀Z₂)/2</p>
<p>Mixer Hamiltonian:  H_B = X₀ + X₁ + X₂</p>
<p>Circuit (p=1):</p>
<p>Step 1: Initialise |+⟩^⊗3 = H₀ H₁ H₂ |000⟩</p>
<p>Step 2: Phase separation — apply Rzz(γ) for each edge:</p>
<p>Edge (0,1): CNOT(0→1) · Rz(2γ, q1) · CNOT(0→1)</p>
<p>Edge (1,2): CNOT(1→2) · Rz(2γ, q2) · CNOT(1→2)</p>
<p>Edge (0,2): CNOT(0→2) · Rz(2γ, q2) · CNOT(0→2)</p>
<p>Step 3: Mixing — apply Rx(2β) to q0, q1, q2</p>
<p>Gate count: 3×H + 3×(2 CNOT + 1 Rz) + 3×Rx = 15 gates total; 6 CNOT gates</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Solved Example 2  QAOA Cost Evaluation at Specific Parameters</strong></p>
<p>Problem: For K₃ MaxCut with γ = π/4, β = π/8 at p=1, calculate ⟨C⟩.</p>
<p>Using the analytic result for K₃ MaxCut at p=1 (Farhi et al. 2014):</p>
<p>⟨C(γ,β)⟩ = 3/2 − (3/2) × (1/2) sin(4γ) sin(2β) cos(4γ)</p>
<p>At γ = π/4:  sin(4·π/4) = sin(π) = 0</p>
<p>So ⟨C(π/4, π/8)⟩ = 3/2 − 0 = 3/2</p>
<p>K₃ has MaxCut = 3 (cut all 3 edges) as optimal.  Expected value 3/2 = 1.5.</p>
<p>Approximation ratio = 1.5/3 = 0.50 — not the best for this parameter choice.</p>
<p>Optimal: γ* ≈ π/8, β* ≈ π/8 gives ⟨C⟩ ≈ 2.25 / 3 ≈ 0.75 approximation ratio.</p>
<p>This matches the 11/16 ≈ 0.6875 lower bound guarantee for 3-regular graphs.</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Solved Example 3  Parameter Shift Rule: Exact Gradient Calculation</strong></p>
<p>Problem: For ansatz U(θ) = Rx(θ), compute ∂C/∂θ at θ = π/3 where C(θ) = ⟨Z⟩.</p>
<p>State: Rx(θ)|0⟩ = cos(θ/2)|0⟩ − i sin(θ/2)|1⟩</p>
<p>Cost: C(θ) = ⟨Z⟩ = cos²(θ/2) − sin²(θ/2) = cos(θ)</p>
<p>Parameter shift rule: ∂C/∂θ = [C(θ+π/2) − C(θ−π/2)] / 2</p>
<p>At θ = π/3:</p>
<p>C(π/3 + π/2) = cos(5π/6) = −√3/2 ≈ −0.866</p>
<p>C(π/3 − π/2) = cos(−π/6) = +√3/2 ≈ +0.866</p>
<p>∂C/∂θ = (−0.866 − 0.866)/2 = −0.866</p>
<p>Direct differentiation: d(cos θ)/dθ|_{θ=π/3} = −sin(π/3) = −√3/2 ≈ −0.866  ✓</p>
<p>The parameter shift rule gives the exact gradient — no approximation involved!</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Solved Example 5  Active Space Selection for Water (H₂O)</strong></p>
<p>Problem: H₂O has 10 electrons and 7 spatial orbitals (STO-3G basis).</p>
<p>Design a VQE-friendly active space targeting O-H bond dissociation.</p>
<p>Full orbital space: CAS(10,14 spin-orbitals) → 14 qubits → feasible but large</p>
<p>Frozen core: O 1s orbital (2 electrons) → 8 electrons remaining</p>
<p>Remaining: CAS(8,12) → 12 qubits → manageable</p>
<p>For O-H bond: most important orbitals are O-H σ bonding and σ* antibonding pair</p>
<p>Minimal active space: CAS(2,2) → 4 qubits → very small, tractable</p>
<p>Better balance: CAS(4,4) including O lone pairs → 8 qubits</p>
<p>UCCSD parameter count for CAS(4,4):</p>
<p>Singles: 2 occupied × 4 virtual = 8 (→ 4 after spin symmetry)</p>
<p>Doubles: C(2,2)×C(4,2) = 1×6 = 6</p>
<p>Total: ~10 parameters → highly feasible on NISQ hardware</p>
<p>Chemical accuracy target: ±0.001 Hartree (≈ 0.6 kcal/mol) vs CCSD(T) reference.</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Solved Example 7  SPSA Optimiser: One Update Step</strong></p>
<p>Problem: Apply one SPSA step to VQE with θ = (0.5, 0.3), η = 0.1, δ = 0.05.</p>
<p>Random perturbation Δ = (+1, −1). Cost: C(θ) = (θ₁−0.8)² + (θ₂+0.5)².</p>
<p>θ+ = θ + δΔ = (0.5+0.05, 0.3−0.05) = (0.55, 0.25)</p>
<p>θ− = θ − δΔ = (0.5−0.05, 0.3+0.05) = (0.45, 0.35)</p>
<p>C(θ+) = (0.55−0.8)² + (0.25+0.5)² = 0.0625 + 0.5625 = 0.625</p>
<p>C(θ−) = (0.45−0.8)² + (0.35+0.5)² = 0.1225 + 0.7225 = 0.845</p>
<p>SPSA gradient: g̃ = [C(θ+) − C(θ−)] / (2δΔ)  =  (0.625−0.845)/(0.1) × (1/Δ)</p>
<p>= −2.2 × (1/+1, 1/−1) = (−2.2, +2.2)</p>
<p>Update: θ_new = θ − η × g̃ = (0.5+0.22, 0.3−0.22) = (0.72, 0.08)</p>
<p>True gradient at θ: ∇C = (2(0.5−0.8), 2(0.3+0.5)) = (−0.6, 1.6)</p>
<p>SPSA estimate (−2.2, 2.2) has correct sign for θ₁ — noisy but informative.</p>
</div>

## B. Unsolved Problems

## Chapter 7: Problems

**1.** Write H\_C for MaxCut on the 4-node cycle graph C₄ (edges: 01, 12, 23, 30).  *[Ans: H\_C = (I−Z₀Z₁)/2 + (I−Z₁Z₂)/2 + (I−Z₂Z₃)/2 + (I−Z₃Z₀)/2]*

**2.** For QAOA on C₄ at p=1, how many parameters and CNOT gates?  *[Ans: 2 parameters (γ₁,β₁); 4 edges × 2 CNOTs = 8 CNOTs]*

**3.** Show the maximum MaxCut of C₄ is 4 and state the p=1 QAOA approximation ratio.  *[Ans: Max cut = 4 (alternate partition); QAOA p=1 achieves ≈ 0.75 approx. ratio]*

**4.** Count UCCSD parameters for CAS(4,5) with 4 electrons in 5 orbitals, 10 spin-orbitals.  *[Ans: Singles: 4 occ × 6 virt ≈ 8 spatial → 4 params; Doubles: ~28; Total ≈ 32]*

**5.** Using parameter shift, compute ∂C/∂θ for C(θ) = ⟨Ry(θ)|Z|Ry(θ)⟩ at θ = π/3.  *[Ans: C(θ)=cos θ; shift: [cos(π/3+π/2)−cos(π/3−π/2)]/2 = [−√3/2−√3/2]/2 = −√3/2 ≈ −0.866]*

**6.** An ADAM step has η=0.01, m=0.05, v=0.01 at iteration t=5. Compute the update size.  *[Ans: m̂=0.05/0.41≈0.122; v̂=0.01/0.005=2.0; update=0.01×0.122/√2≈0.00086]*

**7.** Estimate total shot budget for one gradient step: n\_params=100, Var(H)=0.25, target ε=0.01.  *[Ans: 2 × 100 × Var/(2ε²) = 200 × 0.25/0.0002 = 250,000 shots per gradient]*

**8.** For CCSD(T) scaling O(N⁷), if orbitals double from N=10 to N=20, by what factor does cost grow?  *[Ans: (20/10)^7 = 2^7 = 128 times more expensive]*

**9.** Warm-start QAOA uses GW SDP achieving 87.8 out of OPT=100. What p would QAOA need to improve on GW?  *[Ans: p ≈ 12 (conjectured to match GW for 3-regular); no finite p proven to beat GW in general]*

**10.** For portfolio optimisation with n=8 binary assets, how many ZZ interaction terms in H\_C?  *[Ans: C(8,2) = 28 ZZ covariance terms + 8 Z return terms + budget constraint terms ≈ 44 Pauli terms]*

## C. Multiple Choice Questions

<div class="box box-generic">
<p class="box-title"><strong>Q1.  QAOA stands for:</strong></p>
<p>(A)  Quantum Algorithm for Optimisation and Approximation</p>
<p>(B)  Quantum Approximate Optimisation Algorithm</p>
<p>(C)  Quantum Adiabatic Optimal Algorithm</p>
<p>(D)  Quasi-Approximate Quantum Optimisation</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q2.  In QAOA with p layers, the total number of variational parameters is:</strong></p>
<p>(A)  p</p>
<p>(B)  2p</p>
<p>(C)  p²</p>
<p>(D)  n+p</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q3.  The standard QAOA mixer Hamiltonian H_B is:</strong></p>
<p>(A)  Z₁+Z₂+...+Z_n</p>
<p>(B)  X₁+X₂+...+X_n</p>
<p>(C)  Z₁Z₂+Z₂Z₃+...</p>
<p>(D)  H_C itself</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q4.  QAOA at p=1 achieves what approximation ratio for MaxCut on 3-regular graphs?</strong></p>
<p>(A)  1/2 = 0.500</p>
<p>(B)  2/3 = 0.667</p>
<p>(C)  11/16 = 0.688</p>
<p>(D)  7/8 = 0.875</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q5.  The Goemans-Williamson SDP for MaxCut achieves approximation ratio:</strong></p>
<p>(A)  3/4 = 0.75</p>
<p>(B)  11/16 = 0.688</p>
<p>(C)  0.8786</p>
<p>(D)  exactly 1.0</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q6.  The parameter shift rule computes exact gradients using how many circuit evaluations per parameter?</strong></p>
<p>(A)  1</p>
<p>(B)  2</p>
<p>(C)  3</p>
<p>(D)  O(n)</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q7.  The UCCSD ansatz T̂₁ operator generates which type of quantum state transitions?</strong></p>
<p>(A)  Spin-flip transitions only</p>
<p>(B)  Single electron excitations from occupied to virtual orbitals</p>
<p>(C)  Double excitations only</p>
<p>(D)  Triple excitations</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q8.  The 'gold standard' of classical quantum chemistry is:</strong></p>
<p>(A)  Hartree-Fock</p>
<p>(B)  MP2</p>
<p>(C)  CCSD(T)</p>
<p>(D)  DFT/B3LYP</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q9.  SPSA requires how many circuit evaluations per gradient step, regardless of parameter count?</strong></p>
<p>(A)  1</p>
<p>(B)  2</p>
<p>(C)  n</p>
<p>(D)  2n</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q10.  The NISQ era was formally named by:</strong></p>
<p>(A)  Peter Shor in 1994</p>
<p>(B)  Edward Farhi in 2014</p>
<p>(C)  John Preskill in 2018</p>
<p>(D)  Alán Aspuru-Guzik in 2014</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q11.  VQE was first demonstrated experimentally on which molecule?</strong></p>
<p>(A)  H₂ on a superconducting chip</p>
<p>(B)  HeH⁺ on a photonic chip (Peruzzo 2014)</p>
<p>(C)  LiH on a trapped-ion chip</p>
<p>(D)  N₂ on a neutral atom chip</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q12.  In the QUBO formulation of TSP with n cities, how many binary variables are required?</strong></p>
<p>(A)  n</p>
<p>(B)  n²</p>
<p>(C)  n log n</p>
<p>(D)  n!</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q13.  The hardware-efficient SU(2) ansatz is preferred over UCCSD when:</strong></p>
<p>(A)  Chemical accuracy is required</p>
<p>(B)  Circuit depth is the bottleneck on NISQ hardware</p>
<p>(C)  Many molecular orbitals must be included</p>
<p>(D)  Full quantum error correction is available</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q14.  Quantum Natural Gradient (QNG) uses the Fubini-Study metric tensor F. The update rule is:</strong></p>
<p>(A)  θ ← θ − ηF·∇C</p>
<p>(B)  θ ← θ − ηF⁻¹·∇C</p>
<p>(C)  θ ← θ − η∇C/‖∇C‖</p>
<p>(D)  θ ← θ − ηTr(F)·∇C</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q15.  The active space CAS(4,6) requires how many qubits with Jordan-Wigner mapping?</strong></p>
<p>(A)  4</p>
<p>(B)  6</p>
<p>(C)  12</p>
<p>(D)  24</p>
</div>

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  MCQ Answer Key — Chapter 7 (Q1–Q15)</strong></p>
<p>Q1:B   Q2:B   Q3:B   Q4:C   Q5:C   Q6:B   Q7:B   Q8:C   Q9:B   Q10:C</p>
<p>Q11:B   Q12:B   Q13:B   Q14:B   Q15:C</p>
</div>

## D. Theory Questions

## Chapter 7: Theory Questions

**Q1.** Describe the general VQA framework (hybrid quantum-classical loop). What are the roles of the ansatz, cost function, and classical optimiser? Why is this design suitable for NISQ hardware?

**Q2.** Define the QAOA circuit for a generic combinatorial optimisation problem. What are the cost Hamiltonian H\_C and mixer Hamiltonian H\_B? Write the QAOA state after p layers.

**Q3.** Prove (or derive) the QAOA cost function for MaxCut on a single edge (i,j) at p=1. Show the maximum over (γ,β) gives cut value 1/2 + 1/(2√2) for an unweighted edge.

**Q4.** How is the TSP mapped to a QUBO Hamiltonian? What are the constraint terms and why must the penalty A >> B? Estimate the qubit count for a 10-city TSP.

**Q5.** Describe the UCCSD ansatz for quantum chemistry. What is the physical motivation for the unitary variant, and how does it improve on classical (non-unitary) coupled cluster?

**Q6.** Explain the active space approximation CAS(m,n). How does one choose which orbitals to include? What are the consequences of choosing too small or too large an active space?

**Q7.** State and prove the parameter shift rule for Rx(θ). Why is this an exact rather than approximate finite difference? How many circuit evaluations does it require?

**Q8.** Compare COBYLA, SPSA, ADAM, and quantum natural gradient as optimisers for VQA. For each, give: circuit evaluations per step, noise robustness, and convergence speed.

**Q9.** Explain the shot noise budget for a VQE calculation. How does the number of Pauli terms in the Hamiltonian affect total shot count? How does grouping commuting Paulis help?

**Q10.** Describe the hardware-efficient SU(2) ansatz. What are its advantages over UCCSD on NISQ hardware, and what are its fundamental disadvantages for achieving chemical accuracy?

## E. Programming / Research Assignments

<div class="box box-real-world">
<p class="box-title"><strong>🌐  Assignment 1: QAOA for MaxCut on a Random Graph</strong></p>
<p>Implement and analyse QAOA for MaxCut using Qiskit:</p>
<p>(a) Generate a random 10-node 3-regular graph using NetworkX</p>
<p>(b) Build QAOA circuits for p = 1, 2, 3 in Qiskit; optimise angles with COBYLA</p>
<p>(c) Run on a statevector simulator (noiseless) and a noisy simulator (ε = 0.1% depolarising)</p>
<p>(d) Compute approximation ratio = ⟨C⟩ / MaxCut_exact (brute-force the exact value)</p>
<p>(e) Compare to Goemans-Williamson SDP (use cvxpy or scipy)</p>
<p>(f) Plot approximation ratio vs p for both noiseless and noisy runs</p>
<p>(g) At what noise level does adding more QAOA layers stop helping?</p>
<p>Bonus: Implement warm-start QAOA using the GW SDP solution as initial state.</p>
<p>Deliverable: Jupyter notebook with all circuits, plots, and a 2-page analysis.</p>
</div>

## F. Project Suggestions

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  Project 1: Comprehensive QAOA Benchmark vs Classical Algorithms</strong></p>
<p>Perform a systematic benchmarking study of QAOA vs classical MaxCut algorithms:</p>
<p>• Generate 100 random 3-regular graph instances for n = 10, 20, 30 nodes</p>
<p>• Implement QAOA at p = 1, 2, 3; optimise with COBYLA</p>
<p>• Classical baselines: Greedy, Simulated Annealing, Goemans-Williamson SDP</p>
<p>• Noise study: run QAOA at p=2 with ε = 0%, 0.1%, 0.5%, 1% per gate</p>
<p>• Statistical analysis: mean, std, and 5th percentile of approximation ratios</p>
<p>• At what n and ε does QAOA lose to classical algorithms? Characterise precisely.</p>
<p>Extension: Test Recursive QAOA (RQAOA) and warm-start QAOA; compare to standard QAOA.</p>
<p>Deliverable: 8-page research report with all figures, statistical tests, and conclusions.</p>
</div>

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  Project 2: VQE for Drug Discovery — Penicillin Thiazolidine Ring</strong></p>
<p>Investigate the quantum chemistry of the thiazolidine ring, central to penicillin's antibiotic mechanism:</p>
<p>• Use Qiskit Nature + PySCF to set up the Hamiltonian (STO-3G basis, ~20 orbitals)</p>
<p>• Design active space CAS(m,n) targeting the S-C bond: CAS(4,4) or CAS(6,6)</p>
<p>• Run VQE with UCCSD ansatz; record convergence curves and final energy</p>
<p>• Compare to CCSD(T) classical reference from PySCF</p>
<p>• Study bond dissociation: compute VQE energy at 10 bond lengths (0.9–2.5 Å)</p>
<p>• Analyse barren plateau: compare gradient magnitudes for UCCSD vs SU(2) ansatz</p>
<p>Research component: 2-page discussion of how understanding the S-C bond could help design</p>
<p>improved antibiotics resistant to beta-lactamase enzymes.</p>
</div>

## References and Further Reading — Chapter 7

## Chapter 7 References

- Farhi, E., Goldstone, J. & Gutmann, S. (2014). A Quantum Approximate Optimization Algorithm. arXiv:1411.4028.

- Peruzzo, A. et al. (2014). A variational eigenvalue solver on a photonic quantum processor. Nature Communications, 5, 4213.

- Goemans, M.X. & Williamson, D.P. (1995). Improved approximation algorithms for maximum cut using semidefinite programming. JACM, 42(6), 1115–1145.

- Mitarai, K. et al. (2018). Quantum circuit learning (parameter shift rule). Physical Review A, 98, 032309.

- Schuld, M. et al. (2019). Evaluating analytic gradients on quantum hardware. Physical Review A, 99, 032331.

- Kandala, A. et al. (2017). Hardware-efficient variational quantum eigensolver. Nature, 549, 242–246.

- Aspuru-Guzik, A. et al. (2005). Simulated Quantum Computation of Molecular Energies. Science, 309, 1704–1707.

- Cao, Y. et al. (2019). Quantum Chemistry in the Age of Quantum Computing. Chemical Reviews, 119, 10856–10915.

- Preskill, J. (2018). Quantum Computing in the NISQ Era and Beyond. Quantum, 2, 79.
