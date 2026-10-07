# APPENDIX C: Quick Reference — Algorithms, Complexity, and Speedups at a Glance

This appendix collects, in one place, the complexity comparison that matters most throughout this volume: for each major algorithm or technique covered, the best known classical approach, the quantum approach, the type of speedup obtained, and where in the book it is developed in full.

| **Algorithm / Technique** | **Classical** | **Quantum** | **Speedup Type** | **Reference** |
|---|---|---|---|---|
| Shor's factoring | GNFS: sub-exponential | Quantum: O((log N)^3) | Exponential | Ch. 1, §1.1 |
| Grover's search | O(N) | O(sqrt(N)) | Quadratic (provably optimal) | Ch. 1, §1.2 |
| Quantum counting | O(1/eps^2) sampling | O(1/eps) | Quadratic | Ch. 1, §1.4 |
| HHL linear systems | O(N\*s\*kappa) (sparse) | O(log(N)\*kappa^2\*s) | Exponential (with caveats) | Ch. 1, §1.3 |
| Hamiltonian simulation (Trotter) | Problem-dependent | O(t^2/eps) gates (1st order) | Polynomial improvement | Ch. 2, §2.1 |
| VQE / ADAPT-VQE | Exponential (exact diagonalisation) | Heuristic, NISQ-compatible | Heuristic / unproven | Ch. 2, §2.6 |
| Simon's problem | Omega(2^(n/2)) queries | O(n) queries | Exponential (query model) | Ch. 3, §3.10 |
| Deutsch-Jozsa | Omega(2^(n-1)+1) queries (deterministic) | 1 query | Exponential (query model) | Ch. 3, §3.10 |
| Random circuit sampling | Believed intractable at scale | Native | Sampling advantage only | Ch. 4, §4.6 |
| Boson sampling | #P-hard (permanents) | Native | Sampling advantage only | Ch. 4, §4.6 |
| Surface code QEC | N/A | O(1%) threshold, O(d^2) overhead | Enables fault tolerance | Ch. 5-6 |
| Quantum LDPC codes | N/A | Lower overhead than surface code | Enables fault tolerance | Ch. 5, §5.7 |
| QAOA | NP-hard in general | Heuristic approximation | Heuristic / unproven | Ch. 7 |
| Quantum kernels / QML | Kernel methods, classical NN | Quantum feature maps | Proven only on constructed problems | Ch. 9-10 |

# APPENDIX D: Glossary of Notation and Symbols

Notation is mostly introduced locally where first used, but a few symbols recur across many chapters. This glossary collects them for quick lookup.

| **Symbol** | **Meaning** |
|---|---|
| \|psi&gt;, &lt;psi\| | Ket and bra notation for a quantum state and its conjugate transpose (Dirac notation) |
| H | Hadamard gate; also used for a system Hamiltonian, disambiguated by context |
| U^dagger | Conjugate transpose (Hermitian adjoint) of a unitary operator U |
| O(f(n)), Omega(f(n)) | Big-O upper bound and big-Omega lower bound on asymptotic growth |
| BQP, BPP, NP, QMA, PSPACE | Complexity classes defined and compared in Chapter 3 |
| kappa | Condition number of a matrix (ratio of largest to smallest singular value); central to HHL, Ch. 1 |
| theta, gamma, beta | Variational circuit parameters (rotation angles) trained by a classical optimiser |
| p\_th | Error-correction threshold: the physical error rate below which larger code distance helps |
| d | Code distance (Ch. 5-6) or circuit depth / QAOA layer count (Ch. 7), disambiguated by context |
| epsilon | A small error or precision parameter, used throughout for approximation bounds |
| r | Number of Trotter steps (Ch. 2) or the hidden period/order being sought (Ch. 1), by context |

# APPENDIX E: Software and Hardware Access Guide

Every algorithm in this book can be implemented and run - on a simulator, and in most cases on real quantum hardware - using freely available tools. This appendix is a practical, minimal starting point.

## E.1 Qiskit (IBM)

Install with pip install qiskit qiskit-ibm-runtime. Create a free IBM Quantum account at quantum.ibm.com to access real superconducting hardware (subject to queue times and a monthly free-tier usage allowance) alongside the local Aer simulator used for most of this book's worked examples.

## E.2 PennyLane (Xanadu)

Install with pip install pennylane pennylane-lightning. PennyLane's automatic differentiation integrates directly with PyTorch and TensorFlow, making it the natural choice for the hybrid quantum-classical models and quantum machine learning workflows of Chapters 9 and 10.

## E.3 Amazon Braket, Google Cirq, and Others

Amazon Braket (pip install amazon-braket-sdk) provides pay-per-use access to IonQ, Rigetti, and QuEra hardware from a single interface; Google's Cirq (pip install cirq) is the natural choice if targeting Google's own superconducting architecture directly. Code examples throughout this book are written in Qiskit and PennyLane, but the underlying algorithms translate directly to any of these frameworks.

<div class="box box-generic">
<p class="box-title"><strong>💡 Tip: Start on a Simulator, Not Real Hardware</strong></p>
<p>Every circuit in this book runs correctly on a local noiseless simulator before it is worth spending queue time or paid credits on real hardware. Debug logic and verify expected output distributions locally first; only move to real hardware once you specifically want to study the effect of physical noise, which no simulator fully reproduces.</p>
</div>

# APPENDIX F: Further Reading, Organised by Chapter

Each chapter's own References and Further Reading section gives detailed citations. This appendix adds a short list of accessible, freely available entry points for readers who want a second perspective on each chapter's core topic before diving into the primary literature.

| **Chapter** | **Accessible entry points** |
|---|---|
| Ch. 1 (Shor, Grover, HHL) | Nielsen &amp; Chuang, Quantum Computation and Quantum Information, Ch. 5-6; Qiskit Textbook online, 'Shor's Algorithm' and 'Grover's Algorithm' chapters |
| Ch. 2 (Simulation, VQE) | McArdle et al. (2020), 'Quantum computational chemistry', Rev. Mod. Phys.; PennyLane's QChem demos |
| Ch. 3 (Complexity) | Watrous, 'Quantum Computational Complexity' (survey); Aaronson's lecture notes, 'Quantum Computing Since Democritus' |
| Ch. 4 (Quantum Advantage) | Arute et al. (2019), Nature, 'Quantum supremacy using a programmable superconducting processor' |
| Ch. 5-6 (QEC, Surface Codes) | Fowler et al. (2012), 'Surface codes: Towards practical large-scale quantum computation'; Google Quantum AI blog series on error correction |
| Ch. 7 (QAOA, VQE optimisation) | Farhi, Goldstone, Gutmann (2014), 'A Quantum Approximate Optimization Algorithm' (original QAOA paper) |
| Ch. 8 (Barren Plateaus, NISQ) | McClean et al. (2018), Nature Communications, 'Barren plateaus in quantum neural network training landscapes' |
| Ch. 9-10 (Quantum ML) | Schuld &amp; Petruccione, Machine Learning with Quantum Computers (Springer); PennyLane QML demos site |
| Ch. 11 (Frontiers) | Microsoft Quantum blog (topological qubits); ITU/IEEE Quantum Internet Alliance publications |

# APPENDIX G: Twenty Additional Solved Problems, Organised by Chapter

The recap sections at the end of each chapter test conceptual understanding. This appendix adds two additional, more computational problems per chapter, worked through step by step, for readers who want further quantitative practice before attempting the exercises in the companion problem sets.

## G.1 Chapter 1: Shor, Amplitude Amplification, and HHL

**Problem 1 (Ch. 1)**

N = 21. A random base a = 2 is chosen. Verify gcd(2, 21) = 1, then find the order r of 2 mod 21, and use it to factor 21.

<div class="box box-math">
<p class="box-title"><strong>🧮 Solution to Problem 1</strong></p>
<p>Compute powers of 2 mod 21: 2^1=2, 2^2=4, 2^3=8, 2^4=16, 2^5=32 mod 21=11, 2^6=22 mod 21=1.</p>
<p>So r = 6 (the smallest positive integer with 2^r = 1 mod 21). r is even, so proceed.</p>
<p>Compute a^(r/2) mod N = 2^3 mod 21 = 8. Since 8 != -1 mod 21 (i.e. != 20), the algorithm succeeds.</p>
<p>p = gcd(8+1, 21) = gcd(9,21) = 3.  q = gcd(8-1, 21) = gcd(7,21) = 7.</p>
<p>Check: 3 x 7 = 21. Correct factorisation found using only order-finding, exactly as Section 1.1 describes.</p>
</div>

**Problem 2 (Ch. 1)**

A search space has N = 1024 items with exactly M = 4 marked items. How many Grover iterations are needed to maximise the success probability, and what is that probability?

<div class="box box-math">
<p class="box-title"><strong>🧮 Solution to Problem 2</strong></p>
<p>Optimal iteration count: k ~ (pi/4) * sqrt(N/M) = (pi/4) * sqrt(256) = (pi/4)*16 ~ 12.57, so k=13 (round to nearest integer).</p>
<p>With theta defined by sin(theta) = sqrt(M/N) = sqrt(4/1024) = 1/16, the success probability after k iterations is sin^2((2k+1)*theta).</p>
<p>Substituting k=13 and theta = arcsin(1/16) ~ 0.0625 rad gives (2*13+1)*theta ~ 27*0.0625 ~ 1.6875 rad, and sin^2(1.6875) ~ 0.99 - a success probability of about 99%, versus the O(N/M)=256 classical queries an unstructured classical search would need on average.</p>
</div>

## G.2 Chapter 2: Quantum Simulation and Advanced Circuit Design

**Problem 3 (Ch. 2)**

A Hamiltonian H = A + B is simulated for time t=1 using first-order Trotterisation with r=10 steps. If the commutator norm ||[A,B]|| = 2, estimate the Trotter error bound.

<div class="box box-math">
<p class="box-title"><strong>🧮 Solution to Problem 3</strong></p>
<p>First-order Trotter error bound: ||e^{-iHt} - (e^{-iAt/r} e^{-iBt/r})^r|| &lt;= (t^2/(2r)) * ||[A,B]||.</p>
<p>Substituting t=1, r=10, ||[A,B]||=2: error &lt;= (1/(20)) * 2 = 0.1.</p>
<p>Doubling r to 20 would halve this bound to 0.05, illustrating the O(1/r) first-order scaling discussed in Section 2.7.</p>
</div>

**Problem 4 (Ch. 2)**

An ADAPT-VQE run for a small molecule converges after appending 6 operators from a pool of 20. Compare the resulting circuit's parameter count to a fixed UCCSD ansatz using all 20 pool operators.

<div class="box box-math">
<p class="box-title"><strong>🧮 Solution to Problem 4</strong></p>
<p>ADAPT-VQE circuit: 6 variational parameters (one per appended operator).</p>
<p>Fixed UCCSD using the full pool: 20 variational parameters.</p>
<p>ADAPT-VQE uses roughly 3.3x fewer parameters here while reaching the same convergence threshold, consistent with the resource savings illustrated in Figure 2.6.</p>
</div>

## G.3 Chapter 3: Quantum Complexity Theory

**Problem 5 (Ch. 3)**

A classical algorithm for a promise problem uses O(2^(n/2)) queries in the worst case. A quantum algorithm solves the same problem with O(n^2) queries. Classify the type of separation.

<div class="box box-math">
<p class="box-title"><strong>🧮 Solution to Problem 5</strong></p>
<p>Compare growth rates: 2^(n/2) is exponential in n, while n^2 is polynomial in n.</p>
<p>A polynomial quantum algorithm against an exponential classical lower bound is an EXPONENTIAL separation - the same category as Simon's algorithm (Section 3.10), not a quadratic (Grover-type) separation.</p>
</div>

**Problem 6 (Ch. 3)**

Is BQP subset of PSPACE, PSPACE subset of BQP, both, or neither, and why does this matter practically?

<div class="box box-math">
<p class="box-title"><strong>🧮 Solution to Problem 6</strong></p>
<p>BQP is contained in PSPACE: any quantum circuit can be simulated (inefficiently, but correctly) by a classical algorithm using polynomial SPACE, even though it may take exponential TIME.</p>
<p>PSPACE is not known to be contained in BQP - this containment is a central open problem.</p>
<p>Practically: this tells us quantum computers cannot solve anything that isn't at least theoretically computable by an ordinary (if very slow) classical machine - quantum computers extend efficiency, not the boundary of computability itself.</p>
</div>

## G.4 Chapter 4: Quantum Advantage

**Problem 7 (Ch. 4)**

A random-circuit-sampling experiment reports a linear cross-entropy benchmark (XEB) fidelity of 0.002. Explain, in one or two sentences, what this number represents and why it is not close to 1.

<div class="box box-math">
<p class="box-title"><strong>🧮 Solution to Problem 7</strong></p>
<p>XEB fidelity estimates what fraction of the ideal (noiseless) output distribution's structure survives in the noisy experimental samples; it is normalised so that 1 represents a perfect noiseless circuit and 0 represents a fully randomised (uniform) output.</p>
<p>A small but statistically significant positive value (like 0.002) is expected and sufficient for a quantum advantage claim, because reproducing even this small a correlation is what is classically hard - XEB is not meant to resemble a classical accuracy score.</p>
</div>

**Problem 8 (Ch. 4)**

A boson-sampling experiment uses 50 photons across 100 modes. Roughly why is this specific choice (photons much fewer than modes) important for the hardness argument?

<div class="box box-math">
<p class="box-title"><strong>🧮 Solution to Problem 8</strong></p>
<p>The Aaronson-Arkhipov hardness argument relies on computing the permanent of a 50x50 sub-matrix drawn from the full 100x100 interferometer unitary - a genuinely different, harder computational object than the full matrix.</p>
<p>Using far more modes than photons also keeps collision events (two photons landing in the same mode) rare, which is required for the mathematical argument connecting output probabilities to matrix permanents to hold cleanly.</p>
</div>

## G.5 Chapter 5: Quantum Error Correction

**Problem 9 (Ch. 5)**

Verify that the 3-qubit bit-flip code satisfies the Knill-Laflamme conditions for the single-qubit bit-flip error set {I, X1, X2, X3}.

<div class="box box-math">
<p class="box-title"><strong>🧮 Solution to Problem 9</strong></p>
<p>Codewords: |0_L&gt; = |000&gt;, |1_L&gt; = |111&gt;.</p>
<p>Check cross terms, e.g. &lt;0_L|X1^dagger X2|0_L&gt; = &lt;000|X1 X2|000&gt; = &lt;000|110&gt; = 0 - orthogonal, as required whenever a != b.</p>
<p>Check same-error terms, e.g. &lt;0_L|X1^dagger X1|0_L&gt; = &lt;000|000&gt; = 1 = &lt;1_L|X1^dagger X1|1_L&gt;, independent of the codeword - satisfying C_aa being codeword-independent.</p>
<p>All such checks pass, confirming (as asserted in Section 5.1) that the code corrects any single bit-flip error.</p>
</div>

**Problem 10 (Ch. 5)**

A stabiliser code has n=7 physical qubits and k=1 logical qubit (the Steane code). How many independent stabiliser generators does it have, and what does each syndrome measurement outcome tell you?

<div class="box box-math">
<p class="box-title"><strong>🧮 Solution to Problem 10</strong></p>
<p>Number of generators = n - k = 7 - 1 = 6.</p>
<p>Each of the 6 generators is measured to give a +1 or -1 outcome, jointly forming a 6-bit syndrome that identifies which (if any) correctable error occurred, without collapsing the encoded logical qubit's state, exactly as the general stabiliser formalism of Section 5.7 describes.</p>
</div>

## G.6 Chapter 6: Surface Codes and Fault Tolerance

**Problem 11 (Ch. 6)**

A surface code with distance d=9 is run at physical error rate p=0.1%, with a threshold of p\_th=1%. Using the simple scaling logical\_error ~ (p/p\_th)^((d+1)/2), estimate the logical error rate.

<div class="box box-math">
<p class="box-title"><strong>🧮 Solution to Problem 11</strong></p>
<p>Compute p/p_th = 0.001/0.01 = 0.1.</p>
<p>Compute the exponent (d+1)/2 = (9+1)/2 = 5.</p>
<p>logical_error ~ (0.1)^5 = 0.00001 = 1e-5, several orders of magnitude below the physical error rate - illustrating why operating comfortably below threshold is so valuable.</p>
</div>

**Problem 12 (Ch. 6)**

Using the overhead formula 2d^2 - 1 physical qubits per logical qubit, how many physical qubits are needed for 100 logical qubits at distance d=15?

<div class="box box-math">
<p class="box-title"><strong>🧮 Solution to Problem 12</strong></p>
<p>Per-logical-qubit overhead: 2*(15)^2 - 1 = 2*225 - 1 = 449 physical qubits.</p>
<p>For 100 logical qubits: 100 * 449 = 44,900 physical qubits - illustrating why useful fault-tolerant algorithms are typically discussed in terms of millions of physical qubits once realistic logical qubit counts (hundreds to thousands) are considered.</p>
</div>

## G.7 Chapter 7: QAOA, VQE, and Optimisation

**Problem 13 (Ch. 7)**

For a single-qubit expectation value f(theta) = cos(theta), use the parameter-shift rule to compute the gradient at theta = pi/3, and compare to the exact analytic derivative.

<div class="box box-math">
<p class="box-title"><strong>🧮 Solution to Problem 13</strong></p>
<p>Parameter-shift rule: f'(theta) = (1/2)[f(theta+pi/2) - f(theta-pi/2)].</p>
<p>f(pi/3 + pi/2) = cos(5pi/6) = -sqrt(3)/2.  f(pi/3 - pi/2) = cos(-pi/6) = sqrt(3)/2.</p>
<p>f'(pi/3) = (1/2)[-sqrt(3)/2 - sqrt(3)/2] = (1/2)(-sqrt(3)) = -sqrt(3)/2.</p>
<p>Exact derivative: d/d(theta) cos(theta) = -sin(theta); at theta=pi/3, -sin(pi/3) = -sqrt(3)/2. Matches exactly, confirming the parameter-shift rule gives the exact gradient, not an approximation.</p>
</div>

**Problem 14 (Ch. 7)**

A MaxCut instance on a 3-regular graph with 100 edges is solved with QAOA at p=1, achieving an approximation ratio of 0.6924 (the known Farhi-Goldstone-Gutmann guarantee). How many edges are cut, at minimum, in expectation?

<div class="box box-math">
<p class="box-title"><strong>🧮 Solution to Problem 14</strong></p>
<p>Expected cut edges &gt;= approximation_ratio * total_edges = 0.6924 * 100 ~ 69.24.</p>
<p>So QAOA at p=1 is guaranteed, in expectation, to cut at least approximately 69 of the 100 edges - though as Section 7.7 discusses, actual performance on structured graphs is often better than this worst-case guarantee.</p>
</div>

## G.8 Chapter 8: Barren Plateaus and NISQ Reality

**Problem 15 (Ch. 8)**

A hardware-efficient ansatz's gradient variance is empirically found to scale as Var[dC/d(theta)] ~ 4^(-n) for n qubits. Estimate how many additional qubits it takes to reduce the gradient variance by a factor of 16, and comment on the practical implication.

<div class="box box-math">
<p class="box-title"><strong>🧮 Solution to Problem 15</strong></p>
<p>Var ~ 4^(-n), so Var(n+k)/Var(n) = 4^(-k). Setting 4^(-k) = 1/16 gives k=2.</p>
<p>So each additional 2 qubits reduces the gradient variance by a further factor of 16 - an exponential decay that quickly makes gradient-based training statistically indistinguishable from noise as n grows, which is precisely the barren-plateau phenomenon of Section 8.1.</p>
</div>

**Problem 16 (Ch. 8)**

Zero-noise extrapolation (ZNE) measures an expectation value at three noise scale factors: 1x, 2x, 3x, giving values 0.80, 0.65, 0.50. Assuming a linear model, extrapolate to the zero-noise limit.

<div class="box box-math">
<p class="box-title"><strong>🧮 Solution to Problem 16</strong></p>
<p>Fit a line through the points (1,0.80), (2,0.65), (3,0.50): slope = (0.50-0.80)/(3-1) = -0.15.</p>
<p>Intercept at scale factor 0: value(1) - slope*1 = 0.80 - (-0.15) = 0.95.</p>
<p>Zero-noise extrapolated estimate ~ 0.95 - noticeably closer to an ideal, noiseless expectation value than any individual noisy measurement, illustrating the technique introduced in Section 8.6.</p>
</div>

## G.9 Chapter 9: Quantum Kernels and Quantum Neural Networks

**Problem 17 (Ch. 9)**

A quantum kernel matrix K is computed for 4 training points and found to be K = [[1,0.9,0.1,0.05],[0.9,1,0.05,0.1],[0.1,0.05,1,0.9],[0.05,0.1,0.9,1]]. What clustering structure does this suggest?

<div class="box box-math">
<p class="box-title"><strong>🧮 Solution to Problem 17</strong></p>
<p>High off-diagonal values (0.9) between points 1&amp;2 and between points 3&amp;4 indicate those pairs are highly similar under the quantum feature map.</p>
<p>Low values (0.05-0.1) between {1,2} and {3,4} indicate the quantum feature map strongly separates two clusters: {point1, point2} and {point3, point4} - exactly the kind of structure an SVM built on this kernel (Section 9.2) could exploit for classification.</p>
</div>

**Problem 18 (Ch. 9)**

A Born machine (Section 9.4) samples the 2-bit distribution p(00)=0.4, p(01)=0.3, p(10)=0.2, p(11)=0.1. The target distribution is uniform. Compute the total variation distance between them.

<div class="box box-math">
<p class="box-title"><strong>🧮 Solution to Problem 18</strong></p>
<p>Total variation distance = (1/2) * sum(|p_i - q_i|), with q_i = 0.25 for all four outcomes.</p>
<p>|0.4-0.25|+|0.3-0.25|+|0.2-0.25|+|0.1-0.25| = 0.15+0.05+0.05+0.15 = 0.40.</p>
<p>TV distance = 0.40/2 = 0.20 - a training objective like the one in Section 9.4 would push this distance toward zero as theta is optimised.</p>
</div>

## G.10 Chapter 10: Quantum Transfer Learning and Data Encoding

**Problem 19 (Ch. 10)**

A hybrid model uses a 4-qubit quantum layer with 3 data re-uploading layers. Each layer encodes 2 classical features via angle encoding. How many total encoding gates are applied, and what does adding a 4th re-uploading layer change, structurally, about the model?

<div class="box box-math">
<p class="box-title"><strong>🧮 Solution to Problem 19</strong></p>
<p>Total encoding gates = 3 layers * 2 features/layer = 6 encoding gates (assuming one gate per feature per layer).</p>
<p>Adding a 4th re-uploading layer adds 2 more encoding gates (8 total) and, per Section 10.5's discussion, increases the model's accessible Fourier spectrum - it can represent higher-frequency functions of the input data, at the cost of a deeper circuit and (per Chapter 8) increased barren-plateau risk.</p>
</div>

**Problem 20 (Ch. 10)**

A quantum ML paper reports 95% test accuracy on a benchmark where a simple classical logistic regression achieves 94% accuracy. Applying this book's dequantisation-aware framework, what should a careful reader check before concluding the quantum model is meaningfully better?

<div class="box box-math">
<p class="box-title"><strong>🧮 Solution to Problem 20</strong></p>
<p>Check whether the 1-percentage-point gap is statistically significant given the test set size and any variance across random seeds/training runs - many single-point comparisons in the QML literature are not.</p>
<p>Check whether the benchmark was specifically engineered to favour the quantum feature map (as in Section 9.4.2's engineered-vs-standard-dataset distinction), which would make the result unlikely to generalise to other real-world datasets.</p>
<p>Check whether a moderately tuned classical model (not just plain logistic regression) closes the gap entirely - consistent with the dequantisation results (Section 10.3) that have repeatedly narrowed earlier quantum ML advantage claims.</p>
</div>

# APPENDIX H: Supplementary Data Figures, Organised by Chapter

This final appendix collects twelve additional supplementary figures - illustrative data visualisations that support and extend points made in the main chapters, gathered here so they do not interrupt the narrative flow of the text itself. Each is labelled with the chapter and section it most directly supports.

## H.1 Supporting Chapter 1

Gate counts grow polynomially in the bit-length of N, the source of Shor's exponential advantage over classical factoring's sub-exponential growth.

<figure class="book-figure">
<img src="content/images/image83.png" alt="Figure H.1: Physical Gate Count for Shor&#x27;s Algorithm vs Integer Bit-Length">
<figcaption>Figure H.1: Physical Gate Count for Shor's Algorithm vs Integer Bit-Length</figcaption>
</figure>

The speedup factor widens as N grows, reflecting HHL's logarithmic dependence on system size discussed in Section 1.3.

<figure class="book-figure">
<img src="content/images/image84.png" alt="Figure H.2: HHL Speedup Factor vs Matrix Size N (Fixed Condition Number)">
<figcaption>Figure H.2: HHL Speedup Factor vs Matrix Size N (Fixed Condition Number)</figcaption>
</figure>

## H.2 Supporting Chapter 2

A typical VQE optimisation trajectory converging toward the exact ground-state energy over dozens of classical optimiser iterations.

<figure class="book-figure">
<img src="content/images/image85.png" alt="Figure H.3: VQE Energy Convergence Across Optimiser Iterations (H2 Molecule)">
<figcaption>Figure H.3: VQE Energy Convergence Across Optimiser Iterations (H2 Molecule)</figcaption>
</figure>

Qubit requirements scale with the number of spin-orbitals included in the active space, motivating the resource-saving techniques of Section 2.6.

<figure class="book-figure">
<img src="content/images/image86.png" alt="Figure H.4: Qubits Required for Common Molecules Under Jordan-Wigner Mapping">
<figcaption>Figure H.4: Qubits Required for Common Molecules Under Jordan-Wigner Mapping</figcaption>
</figure>

## H.3 Supporting Chapter 3

A compact comparison of the query-complexity scaling exponents for the separations discussed throughout Chapter 3.

<figure class="book-figure">
<img src="content/images/image87.png" alt="Figure H.5: Query Complexity Exponents Across Problems in This Chapter">
<figcaption>Figure H.5: Query Complexity Exponents Across Problems in This Chapter</figcaption>
</figure>

## H.4 Supporting Chapter 4

Device sizes used in headline advantage claims have grown steadily, though as Section 4.6 discusses, classical simulation capability has grown alongside them.

<figure class="book-figure">
<img src="content/images/image88.png" alt="Figure H.6: Reported Qubit/Mode Counts in Major Advantage Claims by Year">
<figcaption>Figure H.6: Reported Qubit/Mode Counts in Major Advantage Claims by Year</figcaption>
</figure>

## H.5 Supporting Chapter 5

The safety margin below the error-correction threshold shrinks rapidly as physical error rates approach it, underscoring why hardware improvements below threshold are prioritised.

<figure class="book-figure">
<img src="content/images/image89.png" alt="Figure H.7: Safety Margin Below Threshold vs Physical Error Rate">
<figcaption>Figure H.7: Safety Margin Below Threshold vs Physical Error Rate</figcaption>
</figure>

## H.6 Supporting Chapter 6

Simpler decoders such as lookup tables and Union-Find tend to run faster than more accurate but heavier algorithms such as MWPM, the trade-off central to Section 6.9.

<figure class="book-figure">
<img src="content/images/image90.png" alt="Figure H.8: Reported Decoder Latencies by Decoding Algorithm">
<figcaption>Figure H.8: Reported Decoder Latencies by Decoding Algorithm</figcaption>
</figure>

## H.7 Supporting Chapter 7

A representative convergence trace for the classical outer-loop optimiser that tunes QAOA's gamma and beta parameters.

<figure class="book-figure">
<img src="content/images/image91.png" alt="Figure H.9: Classical Optimiser Convergence for QAOA Parameters">
<figcaption>Figure H.9: Classical Optimiser Convergence for QAOA Parameters</figcaption>
</figure>

## H.8 Supporting Chapter 8

Probabilistic error cancellation typically demands far more circuit repetitions than zero-noise extrapolation for a comparable bias reduction, a trade-off introduced in Section 8.6.

<figure class="book-figure">
<img src="content/images/image92.png" alt="Figure H.10: Sampling Overhead of Error Mitigation Techniques">
<figcaption>Figure H.10: Sampling Overhead of Error Mitigation Techniques</figcaption>
</figure>

## H.9 Supporting Chapter 9

The exponential growth of the accessible feature space with qubit count is the theoretical basis for quantum kernel methods' potential expressive advantage.

<figure class="book-figure">
<img src="content/images/image93.png" alt="Figure H.11: Effective Feature Space Dimension vs Number of Qubits">
<figcaption>Figure H.11: Effective Feature Space Dimension vs Number of Qubits</figcaption>
</figure>

## H.10 Supporting Chapter 10

Amplitude encoding and multi-layer data re-uploading both increase circuit depth substantially relative to simple basis or single-layer angle encoding, as discussed in Section 10.5.3.

<figure class="book-figure">
<img src="content/images/image94.png" alt="Figure H.12: Circuit Depth by Data Encoding Strategy">
<figcaption>Figure H.12: Circuit Depth by Data Encoding Strategy</figcaption>
</figure>

# APPENDIX I: Final Supplementary Figures

A last set of illustrative figures, gathered here to close out the volume's visual material without further lengthening the main chapters. As with Appendix H, treat exact numbers here as illustrative rather than as precise, individually cited measurements.

A reminder of Section 11.7's point: the honest timeline for a cryptographically relevant quantum computer depends heavily on which hardware-improvement scenario proves correct.

<figure class="book-figure">
<img src="content/images/image95.png" alt="Figure I.1: Estimated Years Until RSA-2048 Is Broken Under Different Hardware Improvement Rates">
<figcaption>Figure I.1: Estimated Years Until RSA-2048 Is Broken Under Different Hardware Improvement Rates</figcaption>
</figure>

UCCSD's parameter count grows faster with system size than a hardware-efficient ansatz's, reinforcing the trade-off explored in Section 7.7.3.

<figure class="book-figure">
<img src="content/images/image96.png" alt="Figure I.2: Variational Parameter Count vs Molecule Size Across Ansatz Families">
<figcaption>Figure I.2: Variational Parameter Count vs Molecule Size Across Ansatz Families</figcaption>
</figure>

A schematic, not a measured result: it illustrates why an exponential quantum advantage widens over time even from a modest starting point.

<figure class="book-figure">
<img src="content/images/image97.png" alt="Figure I.3: Illustrative Growth of Solvable Problem Size, Classical vs Quantum">
<figcaption>Figure I.3: Illustrative Growth of Solvable Problem Size, Classical vs Quantum</figcaption>
</figure>

Fidelity decays roughly exponentially with depth as gate errors accumulate, the practical limit on how deep a near-term random-circuit-sampling experiment can go.

<figure class="book-figure">
<img src="content/images/image98.png" alt="Figure I.4: Linear XEB Fidelity vs Circuit Depth for a Fixed Qubit Count">
<figcaption>Figure I.4: Linear XEB Fidelity vs Circuit Depth for a Fixed Qubit Count</figcaption>
</figure>

A compact numerical comparison extending Section 5.7's qualitative discussion of code overhead.

<figure class="book-figure">
<img src="content/images/image99.png" alt="Figure I.5: Physical-to-Logical Qubit Ratio Across Code Families">
<figcaption>Figure I.5: Physical-to-Logical Qubit Ratio Across Code Families</figcaption>
</figure>

Scaling the per-logical-qubit overhead from Section 6.9 across the range of logical qubit counts different algorithms are estimated to need.

<figure class="book-figure">
<img src="content/images/image100.png" alt="Figure I.6: Projected Physical Qubit Counts for Milestone Fault-Tolerant Algorithms">
<figcaption>Figure I.6: Projected Physical Qubit Counts for Milestone Fault-Tolerant Algorithms</figcaption>
</figure>

On small instances, well-tuned classical heuristics remain competitive with or ahead of QAOA - consistent with Chapter 8's honest scorecard.

<figure class="book-figure">
<img src="content/images/image101.png" alt="Figure I.7: QAOA vs Best Classical Heuristic on Small MaxCut Instances">
<figcaption>Figure I.7: QAOA vs Best Classical Heuristic on Small MaxCut Instances</figcaption>
</figure>

Raw qubit count has grown rapidly, though as this book emphasises throughout, qubit count alone does not determine computational usefulness.

<figure class="book-figure">
<img src="content/images/image102.png" alt="Figure I.8: Reported Maximum Qubit Counts on Cloud-Accessible NISQ Devices Over Time">
<figcaption>Figure I.8: Reported Maximum Qubit Counts on Cloud-Accessible NISQ Devices Over Time</figcaption>
</figure>

Rapid publication growth in quantum machine learning underscores why Chapters 9 and 10 emphasise a critical, dequantisation-aware reading of new results.

<figure class="book-figure">
<img src="content/images/image103.png" alt="Figure I.9: Growth in Published Quantum Machine Learning Papers Over Time">
<figcaption>Figure I.9: Growth in Published Quantum Machine Learning Papers Over Time</figcaption>
</figure>

Transfer learning's advantage is largest precisely where classical training data is scarcest, matching the motivation given in Section 10.2.

<figure class="book-figure">
<img src="content/images/image104.png" alt="Figure I.10: Accuracy Gain from Quantum Transfer Learning vs Training From Scratch">
<figcaption>Figure I.10: Accuracy Gain from Quantum Transfer Learning vs Training From Scratch</figcaption>
</figure>

An illustrative breakdown extending Section 11.5's overview of India's quantum computing ecosystem.

<figure class="book-figure">
<img src="content/images/image105.png" alt="Figure I.11: Illustrative National Quantum Mission Funding Allocation by Focus Area">
<figcaption>Figure I.11: Illustrative National Quantum Mission Funding Allocation by Focus Area</figcaption>
</figure>

A snapshot complementing the architecture comparison of Section 11.3 - expect these figures to be quickly outdated, per this book's own repeated caution about the field's pace.

<figure class="book-figure">
<img src="content/images/image106.png" alt="Figure I.12: Largest Reported Qubit Counts by Architecture, as of Writing">
<figcaption>Figure I.12: Largest Reported Qubit Counts by Architecture, as of Writing</figcaption>
</figure>

# APPENDIX J: Closing Supplementary Figures

A final short set of figures, closing out the expanded material added throughout this edition.

The quadratic gap between O(N) and O(sqrt(N)) widens visibly on a log-log scale as N grows, the clearest possible picture of Grover's proven advantage from Section 1.2.

<figure class="book-figure">
<img src="content/images/image107.png" alt="Figure J.1: Query Count - Grover vs Classical Search Across Problem Sizes">
<figcaption>Figure J.1: Query Count - Grover vs Classical Search Across Problem Sizes</figcaption>
</figure>

The crossover between polynomial quantum growth and sub-exponential classical growth is the single most important plot in this entire volume, underpinning Section 1.1.

<figure class="book-figure">
<img src="content/images/image108.png" alt="Figure J.2: Shor&#x27;s Algorithm vs GNFS - Time Complexity Growth">
<figcaption>Figure J.2: Shor's Algorithm vs GNFS - Time Complexity Growth</figcaption>
</figure>

Each successive distance increase costs more additional physical qubits than the last, a direct consequence of the quadratic overhead formula in Section 6.9.3.

<figure class="book-figure">
<img src="content/images/image109.png" alt="Figure J.3: Cost to Improve Surface-Code Distance by 2">
<figcaption>Figure J.3: Cost to Improve Surface-Code Distance by 2</figcaption>
</figure>

NISQ-era VQE accuracy degrades with molecule size, while fault-tolerant simulation is projected to hold chemical accuracy regardless of size - the long-term promise motivating Chapter 2.

<figure class="book-figure">
<img src="content/images/image110.png" alt="Figure J.4: Chemical Accuracy - VQE (NISQ) vs Projected Fault-Tolerant Simulation">
<figcaption>Figure J.4: Chemical Accuracy - VQE (NISQ) vs Projected Fault-Tolerant Simulation</figcaption>
</figure>

A reminder, extending Section 9.4.2, that a meaningful fraction of quantum ML results are reported on datasets engineered specifically to favour a quantum feature map.

<figure class="book-figure">
<img src="content/images/image111.png" alt="Figure J.5: Common Benchmark Datasets Used in Quantum ML Papers">
<figcaption>Figure J.5: Common Benchmark Datasets Used in Quantum ML Papers</figcaption>
</figure>

Roughly exponential growth in raw qubit count - impressive, but as this book has repeatedly stressed, only one of several metrics that determine real computational usefulness.

<figure class="book-figure">
<img src="content/images/image112.png" alt="Figure J.6: Physical Qubit Count on Leading Devices, 2016-2025">
<figcaption>Figure J.6: Physical Qubit Count on Leading Devices, 2016-2025</figcaption>
</figure>

As of this writing, NIST has standardised one key-encapsulation mechanism and multiple signature schemes, referenced in Section 11.7's migration discussion.

<figure class="book-figure">
<img src="content/images/image113.png" alt="Figure J.7: NIST-Standardised Post-Quantum Algorithms by Category">
<figcaption>Figure J.7: NIST-Standardised Post-Quantum Algorithms by Category</figcaption>
</figure>

A fitting closing figure: this expanded edition brings every chapter to at least six original figures, up from as few as one or two in earlier printings.

<figure class="book-figure">
<img src="content/images/image114.png" alt="Figure J.8: Figures per Chapter in This Expanded Edition">
<figcaption>Figure J.8: Figures per Chapter in This Expanded Edition</figcaption>
</figure>

# APPENDIX K: Closing Figures on Hardware Trends and Risk

A final, short set of figures on hardware trends and migration risk, rounding out the supplementary material added throughout this edition.

Steady fidelity improvement across the industry is the single hardware trend most responsible for the experimental milestones catalogued in Section 11.4.

<figure class="book-figure">
<img src="content/images/image115.png" alt="Figure K.1: Reported Two-Qubit Gate Fidelities by Platform Over Time">
<figcaption>Figure K.1: Reported Two-Qubit Gate Fidelities by Platform Over Time</figcaption>
</figure>

Even under optimistic assumptions about gate fidelity and code overhead, breaking RSA-2048 requires physical qubit counts far beyond any device built to date - the basis for this book's calibrated, non-alarmist treatment of the cryptographic threat in Section 11.7.

<figure class="book-figure">
<img src="content/images/image116.png" alt="Figure K.2: Estimated Physical Qubits to Run Shor&#x27;s Algorithm on RSA-2048">
<figcaption>Figure K.2: Estimated Physical Qubits to Run Shor's Algorithm on RSA-2048</figcaption>
</figure>

A summary of how the nine information box types introduced in the front matter are put to use across the new material added throughout this edition.

<figure class="book-figure">
<img src="content/images/image117.png" alt="Figure K.3: Information Box Types Introduced or Expanded in This Edition">
<figcaption>Figure K.3: Information Box Types Introduced or Expanded in This Edition</figcaption>
</figure>

Collecting every algorithm covered across all eleven chapters into the same evidentiary categories this book has applied consistently throughout: proven exponential, proven quadratic, conditional exponential, and heuristic/unproven.

<figure class="book-figure">
<img src="content/images/image118.png" alt="Figure K.4: This Book&#x27;s Algorithms Classified by Speedup Type">
<figcaption>Figure K.4: This Book's Algorithms Classified by Speedup Type</figcaption>
</figure>

Publicly announced hardware roadmaps project roughly exponential qubit-count growth through the decade - projections which, consistent with this book's repeated caution, should be read as targets rather than guarantees.

<figure class="book-figure">
<img src="content/images/image119.png" alt="Figure K.5: Published Hardware Roadmaps - Projected Qubit Counts to 2030">
<figcaption>Figure K.5: Published Hardware Roadmaps - Projected Qubit Counts to 2030</figcaption>
</figure>

The remediation cost of a delayed cryptographic migration compounds over time, reinforcing Section 11.7's warning that preparation should begin well ahead of any specific quantum-computing milestone.

<figure class="book-figure">
<img src="content/images/image120.png" alt="Figure K.6: Illustrative Cost of Delaying Post-Quantum Migration">
<figcaption>Figure K.6: Illustrative Cost of Delaying Post-Quantum Migration</figcaption>
</figure>

# INDEX

This index covers principal algorithms, complexity classes, error-correcting codes, and machine-learning concepts introduced in this volume.

| **Term / Concept** | **Definition and Location** |
|---|---|
| Adversary method (quantum) | Query lower-bound technique based on a weighted relation between hard-to-distinguish inputs. Ch. 3, §3.6 |
| Amplitude amplification (generalised) | Amplifies success amplitude of any state-preparation operator A, generalising Grover search. Ch. 1, §1.4 |
| Amplitude encoding | Packs a normalised data vector into the amplitudes of an n-qubit state using log₂N qubits. Ch. 10, §10.1 |
| Angle encoding | Maps classical features to rotation angles of single-qubit gates. Ch. 10, §10.1 |
| Approximate degree (deg̃(f)) | Minimum degree of a real polynomial approximating a Boolean function within bounded error. Ch. 3, §3.4 |
| Barren plateau | Region where cost-function gradient variance vanishes exponentially in qubit number. Ch. 7, §7.6; Ch. 8, §8.1 |
| Basis encoding | Represents a classical bitstring directly as a computational basis state. Ch. 10, §10.1 |
| BBBV theorem | Ω(√N) queries are required for unstructured search by any quantum algorithm. Ch. 3, §3.5 |
| Bit-flip code (3-qubit) | Encodes \|0⟩→\|000⟩, \|1⟩→\|111⟩; corrects a single X error via Z₁Z₂, Z₂Z₃ syndromes. Ch. 5, §5.2 |
| Block-encoding | Embeds a Hamiltonian H/α into a unitary acting on system + ancilla registers. Ch. 2, §2.5 |
| Boson sampling | Sampling from photon-interferometer output distributions; believed classically hard (#P-hard). Ch. 4, §4.4 |
| BPP | Bounded-error Probabilistic Polynomial time — classical randomised complexity class. Ch. 3, §3.2 |
| BQP | Bounded-error Quantum Polynomial time — the standard quantum analogue of BPP. Ch. 3, §3.2 |
| Circuit knitting / T-count | Non-Clifford gate count; dominant resource metric under fault tolerance. Ch. 2, §2.5; Ch. 6, §6.4 |
| CLOPS | Circuit Layer Operations Per Second — throughput benchmark for variational workloads. Ch. 4, §4.6 |
| COBYLA | Gradient-free classical optimiser robust to shot noise, common for VQE/QAOA. Ch. 7, §7.5 |
| Code distance (d) | Minimum weight of an undetectable logical error in a stabiliser code [[n,k,d]]. Ch. 5, §5.5; Ch. 6, §6.1 |
| Continued fractions (Shor's algorithm) | Classical post-processing recovering the period r from a QPE measurement y/2^t. Ch. 1, §1.1 |
| CRYSTALS-Kyber / Dilithium | NIST-standardised (2024) post-quantum lattice-based key encapsulation and signature schemes. Ch. 1, §1.1 |
| Cross-entropy benchmarking (XEB) | Statistical fidelity estimate comparing sampled outputs to ideal simulated distribution. Ch. 4, §4.2 |
| CSS codes | Calderbank-Shor-Steane codes built from nested classical codes; e.g. the Steane code. Ch. 5, §5.6 |
| Data re-uploading | Repeated interleaving of data-encoding and variational layers on the same qubit(s). Ch. 10, §10.1 |
| Dequantisation | Classical algorithms matching quantum runtime given sample-and-query (SQ) access. Ch. 10, §10.2 |
| Entangling capability | Measure of average entanglement generated by a parametrised circuit across its parameter space. Ch. 9, §9.2 |
| Expressibility | How uniformly a circuit's states cover the Hilbert space relative to Haar-random. Ch. 9, §9.2 |
| Fault-tolerance threshold theorem | Below threshold error rate p\_th, larger code distance exponentially suppresses logical error. Ch. 6, §6.2 |
| Fermi-Hubbard model | Lattice fermion model with hopping and on-site Coulomb repulsion; models strong correlation. Ch. 2, §2.3 |
| Hardware-efficient ansatz | Variational circuit built from native hardware gates rather than a physically motivated operator. Ch. 7, §7.4 |
| HHL algorithm | Solves Ax=b in O(log N) for sparse, well-conditioned A; exponential speedup with caveats. Ch. 1, §1.1 |
| Jordan-Wigner transformation | Maps fermionic creation/annihilation operators to qubit Pauli strings. Ch. 2, §2.3 |
| Kernel alignment | Measure of how well a kernel matrix's structure matches training-data labels. Ch. 9, §9.1 |
| k-Local Hamiltonian problem | QMA-complete problem: decide if a k-local Hamiltonian's ground energy is below a threshold. Ch. 3, §3.3 |
| Knill-Laflamme conditions | Necessary and sufficient conditions for a quantum code to correct a given error set. Ch. 5, §5.3 |
| Magic state distillation | Purifies noisy magic states to implement fault-tolerant non-Clifford (T) gates. Ch. 6, §6.4 |
| McLachlan variational principle | Minimises deviation between true Schrödinger evolution and a variational ansatz's time derivative. Ch. 2, §2.2 |
| No-cloning theorem | Forbids copying an arbitrary unknown quantum state; motivates QEC via entangled encoding. Ch. 5, §5.1 |
| Noise-induced barren plateau | Gradient suppression from accumulated gate noise, distinct from expressibility-induced plateaus. Ch. 8, §8.1 |
| Parameter-shift rule | Exact analytic gradient of a quantum expectation value via two shifted circuit evaluations. Ch. 7, §7.5; Ch. 9, §9.3 |
| PennyLane QNode | Decorator wrapping a quantum circuit for differentiable, hybrid quantum-classical programming. Ch. 10, §10.3 |
| Polynomial method (BBCMdW) | Query lower-bound technique relating quantum query complexity to polynomial degree. Ch. 3, §3.4 |
| Probabilistic error cancellation (PEC) | Error mitigation technique cancelling noise via quasi-probability sampling of inverse channel. Ch. 8, §8.2 |
| PSPACE | Class of problems solvable in polynomial space; known that BQP ⊆ PSPACE. Ch. 3, §3.2 |
| QAOA | Quantum Approximate Optimisation Algorithm; alternates cost and mixer unitaries for p layers. Ch. 7, §7.1 |
| QCMA | Quantum Classical Merlin-Arthur; witness is classical, verifier is quantum. Ch. 3, §3.3 |
| QMA | Quantum Merlin-Arthur — the quantum analogue of NP with a quantum witness. Ch. 3, §3.3 |
| Qiskit Runtime resilience\_level | Estimator primitive parameter selecting preset bundles of automatic error mitigation. Ch. 8, §8.3 |
| QNN (Quantum Neural Network) | Encoding + variational + measurement layer architecture for quantum machine learning. Ch. 9, §9.2 |
| Quantum advantage | A quantum computer solving a useful, practically relevant problem faster than any known classical method. Ch. 4, §4.1 |
| Quantum feature map | Circuit U\_φ(x) encoding classical data into a quantum state for kernel-based QML. Ch. 9, §9.1 |
| Quantum kernel | Fidelity-based similarity measure K(x,x')=\|⟨ψ(x')\|ψ(x)⟩\|² used in QSVMs. Ch. 9, §9.1 |
| Quantum query complexity | Number of oracle queries a quantum algorithm needs to compute a Boolean function. Ch. 3, §3.4 |
| Quantum signal processing (QSP) | Constructs polynomial transformations of a block-encoded Hamiltonian via phased walk-operator products. Ch. 2, §2.5 |
| Quantum supremacy / computational advantage | A quantum device performing a specific (not necessarily useful) task intractable classically. Ch. 4, §4.1 |
| Quantum transfer learning | Combines a pretrained classical backbone with a trainable quantum circuit 'head'. Ch. 10, §10.2 |
| Quantum Volume (QV) | Single-number benchmark 2^n capturing qubit count, fidelity, connectivity, and calibration. Ch. 4, §4.5 |
| Quantum walk (coined / continuous-time) | Quantum analogue of a random walk; search complexity O(√(hitting time)). Ch. 1, §1.2 |
| Qubitisation | Block-encoding-based simulation technique achieving near-optimal gate complexity in 1/ε. Ch. 2, §2.5 |
| RSA / GNFS | Public-key cryptosystem broken by Shor's algorithm; GNFS is the best classical factoring algorithm. Ch. 1, §1.1 |
| Sample-and-Query (SQ) model | Classical oracle model granting proportional sampling and index-query access to a vector. Ch. 10, §10.2 |
| Shor's algorithm | Factors integers in polynomial time via quantum phase estimation and period-finding. Ch. 1, §1.1 |
| Simon's problem | First problem with proven exponential quantum-classical query separation; inspired Shor's algorithm. Ch. 3, §3.7 |
| Stabiliser formalism | Describes a quantum code via its commuting Pauli-group stabiliser generators. Ch. 5, §5.4 |
| Steane code [[7,1,3]] | CSS code encoding 1 logical qubit in 7 physical qubits; supports transversal Clifford gates. Ch. 5, §5.6 |
| Suzuki-Trotter formulas (S₂, S₄) | Higher-order product formulas reducing Hamiltonian-simulation error via palindromic composition. Ch. 2, §2.1 |
| Surface code | Topological 2D stabiliser code; leading candidate for scalable fault-tolerant hardware. Ch. 6, §6.1 |
| Threshold theorem | See Fault-tolerance threshold theorem. Ch. 6, §6.2 |
| Trotter error / Lie product formula | e^{-i(A+B)t}≈(e^{-iAt/r}e^{-iBt/r})^r; error scales as O(t²/r) per step for non-commuting terms. Ch. 2, §2.1 |
| UCCSD ansatz | Unitary Coupled Cluster Singles-Doubles variational ansatz for quantum chemistry VQE. Ch. 1, §1.5; Ch. 7, §7.2 |
| Zero-noise extrapolation (ZNE) | Error mitigation technique extrapolating amplified-noise expectation values to the zero-noise limit. Ch. 8, §8.2 |
| ADAPT-VQE | Adaptive variational algorithm that grows its ansatz operator-by-operator using gradient information. Ch. 2, §2.6 |
| Amplitude estimation (quantum counting) | Estimates a solution count M to precision epsilon using O(1/epsilon) Grover iterates via QPE on the Grover operator. Ch. 1, §1.4 |
| Born machine | Quantum generative model that samples from \|&lt;x\|U(theta)\|0&gt;\|^2, trained to match a target data distribution. Ch. 9, §9.4 |
| Boson sampling | Photonic sampling task whose output probabilities are proportional to matrix permanents, believed classically hard. Ch. 4, §4.6 |
| Certified randomness | Randomness verified via an efficient classical check that a quantum server's sample could not have been precomputed classically. Ch. 4, §4.6 |
| Decoding bottleneck | The requirement that a classical decoder process each syndrome faster than physical errors accumulate. Ch. 6, §6.9 |
| Knill-Laflamme conditions | Necessary and sufficient algebraic conditions for a code to correct a given error set. Ch. 5, §5.7 |
| National Quantum Mission (India) | India's 2023-2031 national research programme funding quantum hardware, communication, and algorithms research. Ch. 10, §10.5 |
| NLTS theorem | "No Low-Energy Trivial State" theorem (Anshu-Breuckmann-Nirkhe, 2022); a key stepping stone toward the quantum PCP conjecture. Ch. 3, §3.10 |
| Parameter-shift rule | Exact analytic gradient of a variational circuit obtained from two shifted circuit evaluations. Ch. 7, §7.7 |
| Porter-Thomas distribution | Statistical distribution of output probabilities from a Haar-random quantum circuit; the fingerprint used to validate random circuit sampling. Ch. 4, §4.6 |
| Quantum LDPC codes | Low-density parity-check quantum codes (e.g. bivariate bicycle codes) offering lower qubit overhead than the surface code. Ch. 5, §5.7 |
| Quantum PCP conjecture | Open conjecture asking whether QMA witnesses can be verified by a constant-size local measurement, analogous to the classical PCP theorem. Ch. 3, §3.10 |
| Quantum annealing | Continuous adiabatic optimisation on purpose-built hardware; the physical inspiration for QAOA. Ch. 7, §7.7 |
| Simon's algorithm | First algorithm to prove an exponential quantum-classical query separation, using O(n) queries versus Omega(2^(n/2)) classically. Ch. 3, §3.10 |
| Stabiliser formalism | Framework specifying a code via an abelian Pauli subgroup S; syndromes are measured without disturbing encoded information. Ch. 5, §5.7 |
