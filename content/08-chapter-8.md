# CHAPTER 8

# Barren Plateaus, Expressibility & Near-Term Quantum Advantage

*Gradient Vanishing  |  Noise-Induced BPs  |  Mitigation Strategies  |  Error Mitigation  |  NISQ Advantage*

<div class="box box-anecdote">
<p class="box-title"><strong>📜  Chapter Epigraph — Jarrod McClean, Google Quantum AI, 2018</strong></p>
<p>"Barren plateaus are not just a numerical inconvenience.</p>
<p>They are a fundamental obstruction — a theorem — that limits trainability of quantum neural networks.</p>
<p>The more expressive the circuit, the flatter the landscape."</p>
<p>— Jarrod McClean, on introducing the barren plateau theorem</p>
</div>

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  Chapter 8 Learning Objectives</strong></p>
<p>After studying this chapter, you will be able to:</p>
<p>•  Define barren plateaus and state the McClean et al. theorem precisely</p>
<p>•  Distinguish noise-induced barren plateaus from expressibility-induced ones</p>
<p>•  Describe mitigation strategies: local cost functions, layerwise training, structured ansatze</p>
<p>•  Explain quantum error mitigation: ZNE, PEC, classical shadows</p>
<p>•  Critically assess near-term quantum advantage from VQAs with specific evidence</p>
<p>•  Understand QAOA's theoretical guarantees and their limits</p>
</div>

## 8.1 Barren Plateaus: The Trainability Crisis in Variational Quantum Algorithms

Barren plateaus (BPs) are regions of the variational parameter landscape where the gradient is exponentially small in the number of qubits. They represent the most serious fundamental challenge to scaling variational quantum algorithms.

### 8.1.1 The McClean et al. Theorem (2018)

<div class="box box-generic">
<p class="box-title"><strong>Theorem 8.1  Barren Plateau Theorem (McClean, Boixo, Smelyanskiy, Babbush, Neven — Nature Comms 2018)</strong></p>
<p>Consider a parametrised quantum circuit U(θ) of depth O(poly(n)) on n qubits,</p>
<p>where θ is randomly initialised and the circuit forms an approximate unitary 2-design.</p>
<p>For any global cost function C = Tr(O ρ(θ)) where O acts on all n qubits:</p>
<p>E_θ [ ∂C/∂θ_k ] = 0             (expected gradient is zero)</p>
<p>Var_θ [ ∂C/∂θ_k ] ≤ poly(n) / 4^n   (variance is EXPONENTIALLY small)</p>
<p>Consequence: Resolving a gradient of size ε with confidence 1−δ requires</p>
<p>at least  Ω( 4^n / (ε² δ) )  measurements — EXPONENTIAL in n.</p>
<p>This makes VQAs with global cost functions and deep random circuits untrainable at scale.</p>
<p>Intuition: A deep random circuit explores the entire Hilbert space so uniformly that the</p>
<p>cost function is nearly constant everywhere — no 'uphill' direction can be found.</p>
</div>

<img class="fig-img" src="content/images/image25.png" alt="figure">

**Figure 3: Barren Plateau: Cost Landscape for n = 2, 10, 20 Qubits** *— As n increases, the gradient variance shrinks as 1/4^n — the landscape becomes exponentially flat, requiring exponential shots to escape*

### 8.1.2 When Do Barren Plateaus Occur?

| Cause | Trigger Condition | BP Severity | Mathematical Reason |
|---|---|---|---|
| Global cost function | Observable O acts on all n qubits | Exponential in n | 2-design + global O → Var ∝ 1/4^n |
| Deep random circuits | Depth L = O(poly(n)); random gates | Exponential in n | Circuit forms 2-design; Haar measure |
| Hardware noise | Physical gate errors; decoherence | Exponential in n AND depth | Noise → maximally mixed state → dC=0 |
| High expressibility | Large unitary group covered | Correlates with BP | Expressibility ≡ approximate 2-design |

### 8.1.3 Noise-Induced Barren Plateaus

<div class="box box-generic">
<p class="box-title"><strong>Theorem 8.2  Noise-Induced Barren Plateaus (Wang et al., Nature Communications 2021)</strong></p>
<p>For a variational circuit of depth L with single-qubit depolarising noise ε per gate:</p>
<p>Var[ ∂C/∂θ_k ] ≤ 2 · (1 − ε)^L · Var[ ∂C/∂θ_k ]_{noiseless}</p>
<p>The gradient variance decays EXPONENTIALLY with circuit depth L.</p>
<p>Example: L = 200 layers, ε = 0.1% per gate: (0.999)^200 ≈ 0.82  (18% suppression)</p>
<p>Example: L = 1000 layers, ε = 0.1% per gate: (0.999)^1000 ≈ 0.37  (63% suppression)</p>
<p>Example: L = 100 layers, ε = 1% per gate: (0.99)^100 ≈ 0.37  (63% suppression)</p>
<p>Critical insight: Noise-induced BPs are INDEPENDENT of the expressibility-induced BPs.</p>
<p>Even a shallow, locally-structured circuit eventually hits noise-induced BPs at large depth.</p>
</div>

## 8.2 Barren Plateau Mitigation Strategies

Given the severity of barren plateaus, extensive research has developed mitigation strategies. The key insight: BPs arise specifically from global cost functions + deep random circuits. Targeting either cause provides relief.

### 8.2.1 Local Cost Functions

Switching from global to local cost functions is the most impactful mitigation. A k-local cost function uses only k-body observables (acting on at most k = O(1) qubits):

**C\_local = (1/n) Σᵢ Tr(O\_i^(k) ρ)   where each O\_i^(k) acts on qubit i and its k−1 neighbours**

| Cost Function Type | Gradient Variance Scaling | Training Feasibility |
|---|---|---|
| Global (all n qubits) | Var ∝ 1/4^n | Exponentially hard; BP certain for n > 20 |
| Semi-local (n/2 qubits) | Var ∝ 1/4^(n/2) | Better but still exponential |
| Local (k = O(1) qubits) | Var ∝ 1/poly(n) | Polynomial cost — BP-free! |
| Single qubit (k = 1) | Var = Θ(1/n) | Best; only 1/n suppression |

### 8.2.2 Layerwise Training and Identity Initialisation

Layerwise training adds circuit layers one at a time, training each new layer to convergence before adding the next. This keeps the effective circuit depth small during training, avoiding the 2-design regime that causes BPs.

- Train Layer 1 with all other layers fixed → converge to local minimum.

- Fix Layer 1 parameters. Add Layer 2 and train → converge.

- Repeat until all p layers are trained.

Identity initialisation (Cerezo et al. 2021): initialise all parameters near zero so U(θ ≈ 0) ≈ I. At the identity, gradients are not exponentially suppressed — the circuit can 'grow' its complexity as needed from a well-conditioned starting point.

### 8.2.3 Structure-Preserving Ansatze

Ansatze that preserve physical symmetries (particle number, spin, spatial symmetry) naturally avoid BPs because they operate on a restricted subspace of the full Hilbert space — exponentially smaller than the full 2^n-dimensional space.

- Particle-number-preserving ansatze (chemistry): only explore states with fixed electron count, reducing effective dimension from 2^n to C(n,k).

- Equivariant QNNs (EQNNs): networks that commute with a symmetry group; gradients scale polynomially if the symmetry group is sufficiently large.

- UCCSD with chemical pre-selection: only include the most relevant excitations — dramatically reduces parameter count and avoids BP regime.

## 8.3 Quantum Error Mitigation (QEM)

Quantum error mitigation is distinct from quantum error correction. While QEC uses redundant encoding to eliminate errors completely, QEM extracts more accurate estimates from noisy circuits via classical post-processing — without requiring extra physical qubits.

### 8.3.1 Zero-Noise Extrapolation (ZNE)

ZNE (Temme, Bravyi & Gambetta 2017) deliberately amplifies circuit noise by known factors λ, measures C(λ) at each level, then extrapolates back to the zero-noise limit λ → 0:

**C\_ideal ≈ Extrapolate( {C(λ₁), C(λ₂), ..., C(λ\_m)} )  as  λ → 0**

Noise amplification is achieved by gate folding: replace each gate U with U U† U (tripling the noise contribution of that gate). Richardson extrapolation with m noise levels eliminates the first m−1 leading error terms.

<img class="fig-img" src="content/images/image26.png" alt="figure">

**Figure 4: Zero-Noise Extrapolation Protocol** *— Left: gate folding at λ=1,2,3,4× noise levels. Right: linear fit + Richardson extrapolation to zero-noise C\_ideal*

### 8.3.2 Probabilistic Error Cancellation (PEC)

PEC decomposes the ideal quantum channel as a linear combination of noisy, physically implementable channels (quasi-probability decomposition). By sampling from this decomposition with correct signs, one recovers ideal expectation values. The sampling overhead scales as γ^(2L) where γ = Σ\_k |c\_k| > 1:

**Total sampling overhead ≈ e^(2Lε)  for L gates each with error rate ε**

For L = 100 gates, ε = 0.1%: overhead ≈ e^0.2 ≈ 1.22 (modest). For L = 1000 gates: overhead ≈ e^2 ≈ 7 (still tractable). PEC is the gold standard for short circuits with well-characterised noise.

### 8.3.3 Classical Shadows

Classical shadows (Huang, Kueng & Preskill, Nature Physics 2020) is a tomography protocol that estimates many properties of a quantum state simultaneously with far fewer measurements than full state tomography. Using M shadow samples, one estimates any K different k-local observables to precision ε using:

**M = O( 3^k log(K) / ε² )  measurements  —  independent of the total qubit count n !**

| QEM Method | Overhead | Accuracy | Best For |
|---|---|---|---|
| Zero-Noise Extrapolation (ZNE) | 2–5× shots | Moderate; limited by noise model | Any NISQ circuit; cheap |
| Probabilistic Error Cancellation (PEC) | e^(2Lε) shots | Exact in principle | Short circuits; well-characterised noise |
| Measurement Error Mitigation (MEM) | 1× + calibration | Excellent for readout errors | Always apply — cheap |
| Classical Shadows | 3^k log(K)/ε² shots | Exact for k-local observables | Many simultaneous observables |
| Symmetry Verification | ~2× shots | Detects certain error classes | When problem has known symmetries |

## 8.4 Near-Term Quantum Advantage: A Rigorous Assessment

After studying QAOA, VQE, barren plateaus, and error mitigation, we are in a position to give an honest, evidence-based assessment of when and how near-term quantum advantage from variational algorithms might be achieved.

| Problem Domain | Classical SOTA (2024) | VQA Status | Realistic Advantage? |
|---|---|---|---|
| MaxCut (3-regular) | GW SDP: ≥0.878×OPT; milliseconds | QAOA p=1: 0.69×OPT | NOT demonstrated; GW wins |
| H₂/LiH/H₂O chemistry | CCSD(T): near-exact | VQE matches for small systems | NOT for these molecules |
| FeMoco / Cytochrome P450 | Classical FCI: infeasible | VQE (fault-tolerant): possible | YES — once FT hardware exists |
| Portfolio optimisation (n=50) | Gurobi: exact; fast | QAOA: heuristic, often worse | NOT demonstrated |
| Monte Carlo (risk estimation) | GPU: O(1/ε²) samples | Amplitude estimation: O(1/ε) | YES — quadratic speedup (needs FT) |
| Quantum simulation (3D models) | Tensor network: limited | Trotterised VQE: promising | Possible near-term for 3D |

<div class="box box-warning">
<p class="box-title"><strong>⚠  QAOA: What Is and Is NOT Proven</strong></p>
<p>PROVEN advantages:</p>
<p>• QAOA p=1 achieves ≥11/16 approximation for MaxCut on 3-regular unweighted graphs (Farhi 2014)</p>
<p>• QAOA at p → ∞ converges to optimal solution (adiabatic theorem)</p>
<p>UNPROVEN (conjectured but NOT demonstrated):</p>
<p>• QAOA at any constant p beats Goemans-Williamson for MaxCut</p>
<p>• QAOA provides super-polynomial speedup over classical optimisation for any problem</p>
<p>PROVEN NEGATIVE results:</p>
<p>• QAOA at constant p cannot solve 3-SAT in polynomial time (Bravyi et al. 2020)</p>
<p>• Barren plateaus make QAOA untrainable for global cost functions at scale (McClean 2018)</p>
<p>• Classical algorithms (GW, SDP) remain better than QAOA for MaxCut at all demonstrated p</p>
</div>

<div class="box box-anecdote">
<p class="box-title"><strong>📜  Farhi's Bet and the QAOA Race (2014–2022)</strong></p>
<p>When Farhi introduced QAOA in 2014, he made a bold informal bet: that QAOA at constant p would</p>
<p>eventually beat the Goemans-Williamson SDP for MaxCut. The quantum community got excited.</p>
<p>By 2019, Hastings (Microsoft Research) published numerical evidence suggesting constant-p QAOA</p>
<p>might NOT beat GW. Bravyi et al. proved negative results for 3-SAT in 2020.</p>
<p>In 2022, Wurtz and Love proved that QAOA does approach GW's ratio as p grows large — but</p>
<p>the question of whether any FINITE p can beat GW for all graphs remains open.</p>
<p>Farhi's response: 'QAOA is not trying to beat GW. It's trying to be useful on quantum hardware,</p>
<p>which is a different question.' This pragmatic reframing captures the spirit of NISQ research:</p>
<p>not 'does it beat the best classical algorithm?' but 'does it work well on real quantum hardware?'</p>
<p>The honest answer, as of 2024: on noisy hardware, QAOA rarely outperforms GW even at small n.</p>
</div>

<div class="box box-real-world">
<p class="box-title"><strong>🌐  Honest Near-Term Quantum Advantage Outlook (2024–2035)</strong></p>
<p>NISQ era (2024–2030): No demonstrated practical advantage for commercial problems.</p>
<p>• Quantum chemistry (small molecules): matches CCSD but cannot surpass CCSD(T) on NISQ</p>
<p>• Optimisation (MaxCut, portfolio): classical solvers consistently outperform QAOA</p>
<p>• Machine learning: dequantised; classical NNs and SVMs outperform QML on classical data</p>
<p>Early fault-tolerant era (2030–2040): First real advantages in quantum chemistry.</p>
<p>• Drug discovery: simulate FeMoco, Cytochrome P450, large catalyst systems</p>
<p>• Materials science: high-Tc superconductors, battery cathode materials</p>
<p>• Finance: amplitude estimation for Monte Carlo (genuine quadratic speedup)</p>
<p>Bottom line: Be sceptical of near-term VQA advantage claims. Always ask:</p>
<p>1. What is the classical baseline being beaten?</p>
<p>2. Is the speedup proven or only heuristic/empirical?</p>
<p>3. Does the quantum algorithm scale, or does the advantage vanish at larger n?</p>
</div>

## 8.5 Theoretical Depth: QAOA, Adiabaticity, and Optimisation Landscapes

### 8.5.1 Connection to Adiabatic Quantum Computing

QAOA can be understood as a Trotterised adiabatic evolution. The adiabatic algorithm smoothly interpolates between H\_B (easy initial state) and H\_C (target ground state):

**H(s) = (1−s) H\_B + s H\_C,   s ∈ [0,1],   evolving slowly from s=0 to s=1**

A p-layer QAOA circuit approximates this evolution in p discrete steps. As p → ∞, QAOA converges to the adiabatic algorithm and achieves the exact optimal solution.

| QAOA Depth p | MaxCut Approx. Ratio (3-regular) | 2-Qubit Gate Count | Notes |
|---|---|---|---|
| p = 1 | 11/16 ≈ 0.688 | O(n) | Proven; worse than classical GW |
| p = 2 | ~0.756 (numerical) | O(2n) | Better; still below GW |
| p = 3 | ~0.792 (numerical) | O(3n) | Approaching GW |
| p ≈ 12 (conjectured) | ≥ 0.878 (conjectured) | O(12n) | May match GW — unproven |
| p → ∞ | = OPT | → ∞ | Exact; requires infinite depth |

<div class="box box-generic">
<p class="box-title"><strong>📋  Chapter 8 Summary</strong></p>
<p>Barren Plateau Theorem:  Var[∂C/∂θ] ≤ poly(n)/4^n for global cost + random deep circuit</p>
<p>Noise-induced BPs:       Var decays as (1−ε)^L with depth L and gate error ε</p>
<p>Local cost functions:    Var ∝ 1/poly(n) instead of 1/4^n — essential for scalable training</p>
<p>Layerwise training:      Train one layer at a time; stays away from 2-design regime</p>
<p>Parameter shift rule:    ∂C/∂θ = [C(θ+π/2)−C(θ−π/2)]/2  — exact gradient, 2 evals per param</p>
<p>ZNE:                     Amplify noise; extrapolate to zero; practical overhead 2–5×</p>
<p>PEC:                     Quasi-probability decomposition; exact but exponential overhead</p>
<p>Classical shadows:       Estimate k-local observables in O(3^k log K) shots; scalable</p>
<p>QAOA p=1 MaxCut:         11/16 = 0.6875 for 3-regular; classical GW achieves 0.878</p>
<p>Near-term VQA advantage: Only quantum chemistry (strongly correlated) with fault-tolerant HW</p>
</div>

## RECAP — SHORT ANSWER QUESTIONS & MODEL ANSWERS

Chapter 8: Barren Plateaus, Error Mitigation & the Limits of NISQ Advantage

Instructions: Answer each question in 3–6 lines. Each question carries equal marks.

**PART A — QUESTIONS**

**Q1.  State the McClean et al. (2018) barren plateau theorem and its key consequence.**

**Q2.  List two structural triggers for barren plateaus and explain, briefly, why each causes the effect.**

**Q3.  What is a noise-induced barren plateau, and how does it differ from the expressibility-induced barren plateau?**

**Q4.  How do local cost functions mitigate barren plateaus, and what is the trade-off?**

**Q5.  Describe layerwise training and identity initialisation as barren plateau mitigation strategies.**

**Q6.  Name three quantum error mitigation (QEM) techniques and briefly describe each.**

**Q7.  Why might structure-preserving ansätze (e.g. particle-number-conserving circuits) avoid barren plateaus more effectively than generic hardware-efficient ansätze?**

**Q8.  What does 'resilience\_level' control in Qiskit Runtime's Estimator primitive, referenced in this chapter's discussion of error mitigation?**

**Q9.  Give a rigorous, non-hyped assessment of near-term quantum advantage prospects for variational algorithms, as discussed in this chapter.**

**Q10.  How does circuit depth interact with both barren plateaus and hardware error rates to constrain feasible NISQ algorithms?**

**Q11.  What theoretical guarantee, if any, does QAOA provide for the MaxCut problem, and how does this compare to its practical NISQ performance?**

**Q12.  Why is 'exponential-in-error sampling overhead' a concern for practical use of error mitigation techniques like ZNE and PEC?**

**PART B — MODEL ANSWERS**

**Answer 1:**

For a parametrised circuit forming an approximate unitary 2-design with a global cost function (an observable acting on all n qubits), the expected gradient is exactly zero and its variance is upper-bounded by poly(n)/4^n — exponentially small in the number of qubits. The consequence is that resolving a gradient of size ε with confidence 1−δ requires Ω(4^n/(ε²δ)) measurement shots, an exponential resource requirement that makes such circuits untrainable at any meaningful scale.

**Answer 2:**

(1) Global cost functions, where the observable acts on all n qubits, cause variance to scale as 1/4^n because a random 2-design circuit spreads probability so uniformly across the exponentially large Hilbert space that any global expectation value becomes nearly parameter-independent. (2) Deep random circuits (depth O(poly(n)) with sufficiently random gates) cause the circuit itself to approximate a Haar-random unitary (a 2-design), which is the mathematical precondition triggering the McClean theorem's exponential suppression regardless of the cost function used.

**Answer 3:**

A noise-induced barren plateau (Wang et al., 2021) arises purely from accumulated gate noise: for depolarising noise ε per gate over depth L, gradient variance is suppressed by a factor of roughly (1−ε)^L relative to the noiseless case, becoming severe even in shallow, locally-structured circuits at sufficiently large depth. This is a distinct mechanism from expressibility-induced barren plateaus (which arise from the circuit approximating a 2-design regardless of noise) — meaning even a well-designed, symmetry-respecting ansatz can still hit a barren plateau purely due to hardware noise if run at large enough depth.

**Answer 4:**

Restricting the cost function to act on only k = O(1) qubits (a 'local' cost function) changes the gradient variance scaling from 1/4^n (global) to only 1/poly(n) (local), making training feasible even at large qubit counts. The trade-off is that a local cost function may not directly encode the true global objective of interest, so care is needed to ensure the local proxy cost still guides the optimisation toward states with good global properties.

**Answer 5:**

Layerwise training adds circuit layers incrementally, fully training each new layer (with later layers fixed) before adding the next, which keeps the effective trained circuit depth small at each stage and avoids the 2-design regime associated with deep random circuits. Identity initialisation instead starts all parameters near zero so the initial circuit is close to the identity operation; because gradients near the identity are not exponentially suppressed, the circuit can then 'grow' its expressive complexity gradually from a well-conditioned starting point.

**Answer 6:**

Zero-noise extrapolation (ZNE) deliberately amplifies circuit noise (e.g. by gate folding) at several noise levels and extrapolates the resulting expectation values back to the zero-noise limit. Probabilistic error cancellation (PEC) decomposes the inverse of the noise channel into a quasi-probability distribution over implementable operations, applying random corrections that cancel noise in expectation at the cost of increased sampling variance. Readout error mitigation applies a calibration matrix (or scalable methods like M3) to correct measurement outcome statistics for known state-preparation-and-measurement errors.

**Answer 7:**

By restricting the explored Hilbert space to a symmetry-respecting subspace (e.g. fixed particle number, giving dimension C(n,k) rather than the full 2^n), such ansätze never approximate a full Haar-random 2-design over the entire space, so the McClean theorem's exponential suppression — which relies on that 2-design property — does not directly apply, and gradients can remain polynomially resolvable even for larger qubit counts.

**Answer 8:**

The resilience\_level parameter selects a preset bundle of automatic error mitigation techniques (such as readout error mitigation and zero-noise extrapolation) applied to expectation-value estimation, with higher levels applying more aggressive (and more sampling-expensive) mitigation. This allows users to trade off mitigation strength against classical/quantum resource overhead without manually implementing each mitigation technique.

**Answer 9:**

Given the combined challenges of barren plateaus, noise accumulation, and the need for extensive error mitigation (which itself incurs exponential-in-error sampling overhead in the worst case), current variational algorithms have not demonstrated a proven, practically significant advantage over classical heuristics for real-world problems at useful scale. The honest near-term assessment is that variational algorithms remain a promising research direction with theoretical motivation, but robust, reproducible practical quantum advantage for optimisation or chemistry problems is not yet an established fact.

**Answer 10:**

Increasing circuit depth (e.g. more QAOA layers or a more expressive ansatz) generally improves the theoretical solution quality achievable in the noiseless limit, but also increases susceptibility to noise-induced barren plateaus and accumulates more gate errors overall — so beyond some device- and noise-dependent optimal depth, further increasing circuit depth actually degrades practical performance even though it improves theoretical capacity, creating a sweet-spot trade-off that must be tuned per device.

**Answer 11:**

For p=1, QAOA provably achieves an approximation ratio of at least 0.6924 for MaxCut on 3-regular graphs (Farhi, Goldstone, Gutmann 2014), a modest but rigorous guarantee; ratios generally improve as p increases in the idealised noiseless setting. In practice on NISQ hardware, noise accumulation and barren-plateau-related training difficulty at larger p often prevent these theoretical improvements from being realised, so practical performance frequently falls short of the idealised approximation-ratio guarantees.

**Answer 12:**

Rigorous analyses show that as circuit noise or circuit size increases, the number of samples (shots) required for techniques like ZNE and PEC to achieve a target precision can itself scale exponentially, effectively shifting (rather than removing) the same exponential cost that error correction would otherwise pay directly. This means error mitigation is best suited to moderate noise levels and near-term circuit sizes, but is not considered a substitute for genuine fault-tolerant error correction at scale.

## A. Solved Problems

<div class="box box-generic">
<p class="box-title"><strong>Solved Example 4  Barren Plateau: Shot Count Estimate for n=20 Qubits</strong></p>
<p>Problem: For a random 20-qubit circuit (global cost, 2-design), estimate the number of</p>
<p>shots to estimate ∂C/∂θ₁ with signal-to-noise ratio of 10.</p>
<p>Barren plateau theorem: Var[∂C/∂θ] ≤ poly(n)/4^n</p>
<p>For n=20:  Var ≤ 20/4^20 = 20/(1.1×10¹²) ≈ 1.8×10⁻¹¹</p>
<p>σ_gradient = √(1.8×10⁻¹¹) ≈ 4.2×10⁻⁶</p>
<p>Required: σ_measurement/√S ≤ σ_gradient / 10</p>
<p>σ_measurement ≈ 1 (bounded observable)</p>
<p>S ≥ (10 / σ_gradient)² = (10 / 4.2×10⁻⁶)² ≈ 5.7×10¹² shots</p>
<p>At 1 MHz measurement rate: ≈ 5.7×10⁶ seconds ≈ 66 DAYS per gradient component!</p>
<p>Training is COMPLETELY INFEASIBLE. This is the barren plateau obstruction in practice.</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Solved Example 6  Zero-Noise Extrapolation: Richardson Calculation</strong></p>
<p>Problem: A noisy VQE gives: C(1×) = 0.820,  C(2×) = 0.740,  C(3×) = 0.660.</p>
<p>Use Richardson extrapolation to estimate C_ideal.</p>
<p>Linear extrapolation (points at λ=1, λ=2):</p>
<p>Slope: a = (0.740 − 0.820)/(2 − 1) = −0.080</p>
<p>C₀ = 0.820 − (−0.080)×1 = 0.900</p>
<p>Extrapolated: C_ideal ≈ 0.900</p>
<p>3-point Richardson (eliminates linear error term):</p>
<p>Solve C(λ) = C₀ + a₁λ + a₂λ² for λ = 1,2,3:</p>
<p>C₀ = 0.900,  a₁ = −0.080,  a₂ = 0.000</p>
<p>(Perfectly linear noise model — Richardson agrees with linear: 0.900)</p>
<p>In practice with quadratic noise component, Richardson would correct further.</p>
<p>ZNE works best when the noise model is well-approximated by a polynomial in λ.</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Solved Example 8  Classical vs Quantum Optimisation: Break-Even Analysis</strong></p>
<p>Problem: Compare QAOA and Goemans-Williamson for MaxCut on a 100-node 3-regular graph.</p>
<p>Assess whether QAOA can win.</p>
<p>(a) Classical GW SDP:</p>
<p>SDP size: O(n²) = 10,000 variables</p>
<p>Runtime: O(n^3.5) ≈ 10⁷ operations ≈ milliseconds on modern CPU</p>
<p>Approximation guarantee: ≥ 0.878 × OPT</p>
<p>(b) QAOA p=5 on 100 qubits:</p>
<p>Edges in 3-regular graph: n×3/2 = 150</p>
<p>CNOTs per layer: 150 edges × 2 CNOTs = 300</p>
<p>Total CNOTs (p=5): 5 × 300 = 1500</p>
<p>At 0.1% CNOT error: circuit fidelity ≈ (0.999)^1500 ≈ 0.22 (very poor!)</p>
<p>(c) Break-even:</p>
<p>Need error rate &lt; 0.001% — 100× better than best 2024 hardware</p>
<p>Even then, classical GW still runs in milliseconds on a laptop</p>
<p>Conclusion: QAOA has no demonstrated advantage for MaxCut at any near-term hardware spec.</p>
</div>

## B. Unsolved Problems

## Chapter 8: Problems

**11.** For a 10-qubit random 2-design circuit with global cost, estimate Var[∂C/∂θ] and shots for SNR=10.  *[Ans: Var ≤ 10/4^10 ≈ 10^(-5); shots S ≥ 10^4/Var ≈ 10^9 shots — exponentially many]*

**12.** Show why a 1-local cost C=(1/n)Σᵢ⟨Zᵢ⟩ has Var ∝ 1/n rather than 1/4^n.  *[Ans: Each ⟨Zᵢ⟩ involves only qubit i; gradient localised to constant # qubits → Var ∝ 1/n]*

**13.** For ZNE with gate folding, if each gate has depolarising noise ε, what is noise at 3× fold?  *[Ans: 3× fold → 3ε noise (3 copies of gate → 3× noise for small ε)]*

**14.** PEC for 50 CNOT gates each with ε=0.5%. Compute sampling overhead γ².  *[Ans: γ=(1/(1-0.005))^50≈1.284; γ²≈1.65; 65% more shots than noiseless]*

**15.** Classical shadows: for k=2, K=100 observables, ε=0.05, how many measurements?  *[Ans: M = 9 × log₂(100) / 0.0025 ≈ 9 × 6.64 / 0.0025 ≈ 23,900 measurements]*

**16.** ADAPT-VQE starts from |HF⟩. Why does selecting largest |∂E/∂θ\_k|\_{θ=0} avoid barren plateaus?  *[Ans: At θ=0, circuit=I, gradients = ⟨HF|[H,Aₖ]|HF⟩ — nonzero for relevant operators; circuit stays shallow]*

**17.** With L=200, ε=0.2% per gate, compute noise-induced BP gradient suppression factor.  *[Ans: (1−0.002)^200 ≈ e^(-0.4) ≈ 0.67 — only 33% suppression; but L=1000 gives 87% suppression]*

**18.** Why does QNG converge faster than standard GD? Explain using Fisher information analogy.  *[Ans: GD steps in parameter space ignoring geometry; QNG steps in state-space geometry (F^{-1} preconditioning) → removes redundant directions, more efficient steps toward minimum]*

**19.** QAOA on 50-node 3-regular graph at p=1. Circuit depth? How does it compare to GW runtime?  *[Ans: 75 edges × 2 CNOT + 50 Rx = 200 2Q gates; ~1ms simulator; GW runs in <1ms on laptop — quantum NOT faster]*

**20.** Describe an experiment to test whether QAOA p=3 beats GW for MaxCut on 20-node 3-regular graphs.  *[Ans: Run 100+ random instances; compute approx ratio = cut/OPT; QAOA target > 0.878; t-test p<0.01; must show QAOA consistently beats GW]*

## C. Multiple Choice Questions

<div class="box box-generic">
<p class="box-title"><strong>Q16.  The barren plateau theorem states that for global cost functions and random deep circuits:</strong></p>
<p>(A)  Gradients vanish as 1/n</p>
<p>(B)  Gradients vanish as 1/4^n (exponentially in qubit count)</p>
<p>(C)  Gradients are always exactly zero</p>
<p>(D)  Gradients vanish as 1/poly(n)</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q17.  Noise-induced barren plateaus arise because:</strong></p>
<p>(A)  The cost function is globally defined</p>
<p>(B)  Physical gate errors decohere the state toward the maximally mixed state as circuit depth increases</p>
<p>(C)  The ansatz is too expressive</p>
<p>(D)  The optimiser is not gradient-free</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q18.  The most effective mitigation for expressibility-induced barren plateaus is:</strong></p>
<p>(A)  Using more qubits</p>
<p>(B)  Switching from global to local (k-local) cost functions</p>
<p>(C)  Increasing the circuit depth</p>
<p>(D)  Using a higher learning rate</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q19.  Layerwise training avoids barren plateaus because:</strong></p>
<p>(A)  It uses gradient-free optimisation only</p>
<p>(B)  It keeps effective circuit depth small during training, avoiding the 2-design regime</p>
<p>(C)  It reduces the number of parameters automatically</p>
<p>(D)  It initialises all parameters to exactly π/4</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q20.  Zero-Noise Extrapolation (ZNE) works by:</strong></p>
<p>(A)  Running the circuit at lower physical temperature</p>
<p>(B)  Amplifying circuit noise by known factors and extrapolating back to zero noise</p>
<p>(C)  Using error-corrected logical qubits</p>
<p>(D)  Post-selecting on measurement outcomes after the fact</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q21.  The sampling overhead of PEC for L noisy gates with individual gate error ε scales as:</strong></p>
<p>(A)  O(Lε)</p>
<p>(B)  O(e^(2Lε))</p>
<p>(C)  O(L/ε²)</p>
<p>(D)  O(1) — no overhead</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q22.  Classical shadows require O(3^k log K / ε²) measurements to estimate:</strong></p>
<p>(A)  Full state tomography of n qubits</p>
<p>(B)  K different k-local observables to precision ε, independent of n</p>
<p>(C)  A single global observable to precision ε</p>
<p>(D)  A variational quantum circuit with K parameters</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q23.  QAOA at p=1 cannot match the Goemans-Williamson approximation ratio because:</strong></p>
<p>(A)  QAOA uses too many qubits</p>
<p>(B)  p=1 achieves ratio 11/16 ≈ 0.69, less than GW's 0.878</p>
<p>(C)  QAOA requires fault-tolerant hardware to be competitive</p>
<p>(D)  QAOA violates the quantum Hamming bound</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q24.  ADAPT-VQE selects new operators by:</strong></p>
<p>(A)  Random selection from the operator pool</p>
<p>(B)  Greedily selecting the operator with the largest gradient |∂E/∂θ_k| at the current state</p>
<p>(C)  Selecting all operators simultaneously in one layer</p>
<p>(D)  Using the Hartree-Fock energy to rank all operators</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q25.  Warm-start QAOA initialises the quantum state using:</strong></p>
<p>(A)  The uniform |+⟩^⊗n superposition</p>
<p>(B)  A quantum state encoding the classical SDP solution</p>
<p>(C)  Random initialisation near |0⟩^⊗n</p>
<p>(D)  The Hartree-Fock molecular ground state</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q26.  Measurement Error Mitigation (MEM) corrects for which type of error?</strong></p>
<p>(A)  Two-qubit gate errors in the middle of the circuit</p>
<p>(B)  Decoherence during the computation</p>
<p>(C)  Readout errors: confusion between |0⟩ and |1⟩ at measurement</p>
<p>(D)  Barren plateaus in the variational landscape</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q27.  Which application represents the most realistic near-term quantum advantage from VQAs?</strong></p>
<p>(A)  MaxCut optimisation for large logistics networks</p>
<p>(B)  Portfolio optimisation for financial institutions</p>
<p>(C)  Quantum simulation of strongly correlated molecules (FeMoco, high-Tc SC)</p>
<p>(D)  Neural network training for image classification</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q28.  The Recursive QAOA (RQAOA) improves on standard QAOA by:</strong></p>
<p>(A)  Using more layers to increase approximation ratio</p>
<p>(B)  Iteratively eliminating the most correlated variable pairs and reducing problem size</p>
<p>(C)  Running QAOA classically via tensor network simulation</p>
<p>(D)  Replacing the standard mixer with a fermionic mixer</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q29.  For a sinusoidal C(θ) = A + B cos(θ), both the parameter shift result and direct differentiation give:</strong></p>
<p>(A)  −B sin(θ) — they agree (parameter shift is exact, not approximate)</p>
<p>(B)  Different results because shift introduces approximation error</p>
<p>(C)  B cos(θ + π/2)</p>
<p>(D)  A − B sin(θ)</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q30.  The barren plateau problem is fundamentally caused by which property of random quantum circuits?</strong></p>
<p>(A)  They generate maximal entanglement in the output state</p>
<p>(B)  They form approximate unitary 2-designs, making the cost function nearly constant everywhere</p>
<p>(C)  They have too many parameters for any classical optimiser to handle</p>
<p>(D)  They violate the no-cloning theorem for gradient computation</p>
</div>

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  MCQ Answer Key — Chapter 8 (Q16–Q30)</strong></p>
<p>Q16:B   Q17:B   Q18:B   Q19:B   Q20:B   Q21:B   Q22:B   Q23:B   Q24:B   Q25:B</p>
<p>Q26:C   Q27:C   Q28:B   Q29:A   Q30:B</p>
</div>

## D. Theory Questions

## Chapter 8: Theory Questions

**Q11.** State the barren plateau theorem (McClean et al. 2018). What does it mean for a circuit to form an approximate 2-design? Why does this lead to exponentially vanishing gradients for global cost functions?

**Q12.** Distinguish expressibility-induced from noise-induced barren plateaus. State the Wang et al. (2021) result on noise-induced BPs and its implication for deep NISQ circuits.

**Q13.** Explain why local cost functions avoid barren plateaus. How is a k-local cost function constructed, and what is the tradeoff between BP mitigation and solution quality?

**Q14.** Describe layerwise training as a BP mitigation strategy. Why does adding one layer at a time prevent entering the 2-design regime that causes exponentially small gradients?

**Q15.** Derive the sampling overhead of probabilistic error cancellation (PEC) for a circuit of L noisy gates each with depolarising error ε. At what depth does PEC become impractical?

**Q16.** Explain the classical shadows protocol. How does it achieve exponential savings in measurement overhead for estimating many local observables simultaneously?

**Q17.** Explain the connection between QAOA and adiabatic quantum computing. How does the adiabatic theorem guarantee QAOA converges as p → ∞? What determines the practical p needed?

**Q18.** Describe ADAPT-VQE. How does it select operators from the operator pool? Why does the greedy approach produce compact circuits that naturally avoid barren plateaus?

**Q19.** Critically evaluate the statement: 'QAOA will provide quantum advantage for combinatorial optimisation on near-term quantum computers.' Cite specific theoretical results for and against.

**Q20.** Summarise the conditions under which a near-term VQA could genuinely outperform classical methods. Give one problem class where all conditions are likely satisfied and explain why.

## E. Programming / Research Assignments

<div class="box box-real-world">
<p class="box-title"><strong>🌐  Assignment 2: VQE with Barren Plateau Analysis</strong></p>
<p>Explore barren plateaus in VQE using Qiskit:</p>
<p>(a) Run VQE for H₂ with UCCSD ansatz; compare COBYLA and ADAM convergence</p>
<p>(b) Replace UCCSD with a random hardware-efficient SU(2) ansatz (8 layers).</p>
<p>Use the parameter shift rule to compute all gradients at random θ.</p>
<p>(c) Plot gradient magnitude |∂C/∂θ_k| as a function of layer index and qubit count</p>
<p>for n = 2, 4, 6, 8 qubits (use different molecules to scale n)</p>
<p>(d) Verify that gradient variance decreases approximately as 1/4^n for SU(2) ansatz</p>
<p>(e) Demonstrate that a 1-local cost function C_local = (1/n)Σᵢ⟨Zᵢ⟩ restores gradient</p>
<p>magnitudes from O(1/4^n) to O(1/n)</p>
<p>Deliverable: Jupyter notebook + 3-page report documenting the barren plateau transition.</p>
</div>

## F. Project Suggestions

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  Project 3: Barren Plateau Mitigation Toolkit</strong></p>
<p>Build a comprehensive toolkit for barren plateau mitigation in VQAs:</p>
<p>• Implement and compare 4 mitigation strategies on a 10-qubit VQE problem:</p>
<p>(1) Global vs local cost function</p>
<p>(2) Random vs identity initialisation vs CCSD warm-start</p>
<p>(3) Standard training vs layerwise training vs ADAPT-VQE</p>
<p>(4) Standard ansatz vs particle-number-preserving ansatz</p>
<p>• For each strategy: measure Var[∂C/∂θ] as a function of n and circuit depth</p>
<p>• Plot all strategies on the same axes; compare final energies and convergence speed</p>
<p>• Write a decision flowchart: 'Which BP mitigation should I use for my VQA?'</p>
<p>Deliverable: Python package + interactive Streamlit dashboard + 6-page technical report.</p>
</div>

## References and Further Reading — Chapter 8

## Chapter 8 References

- McClean, J.R. et al. (2018). Barren plateaus in quantum neural network training landscapes. Nature Communications, 9, 4812.

- Wang, S. et al. (2021). Noise-induced barren plateaus in variational quantum algorithms. Nature Communications, 12, 6961.

- Cerezo, M. et al. (2021). Cost function dependent barren plateaus in shallow parametrized quantum circuits. Nature Communications, 12, 1791.

- Temme, K., Bravyi, S. & Gambetta, J.M. (2017). Error Mitigation for Short-Depth Quantum Circuits. Physical Review Letters, 119, 180509.

- Huang, H.-Y., Kueng, R. & Preskill, J. (2020). Predicting many properties of a quantum system from very few measurements. Nature Physics, 16, 1050–1057.

- Bravyi, S. et al. (2020). Obstacles to Variational Quantum Optimization from Symmetry Protection. PRL, 125, 260505.

- Grimsley, H.R. et al. (2019). An adaptive variational algorithm for exact molecular simulations. Nature Communications, 10, 3007.

- Farhi, E. & Harrow, A.W. (2016). Quantum Supremacy through the QAOA. arXiv:1602.07674.
