# REFERENCES AND FURTHER READING

### Foundational QML Papers

Schuld M. & Killoran N. (2019). Quantum Machine Learning in Feature Hilbert Spaces. PRL 122, 040504. — The landmark paper proving quantum models are kernel methods.

Mitarai K., Negoro M., Kitagawa M., Fujii K. (2018). Quantum Circuit Learning. Physical Review A 98, 032309. — First derivation of the parameter-shift rule.

Schuld M., Bergholm V., Gogolin C., Izaac J., Killoran N. (2019). Evaluating Analytic Gradients on Quantum Hardware. PRA 99, 032331. — Full parameter-shift framework.

Perez-Salinas A., Cervera-Lierta A., Gil-Fuster E., Latorre J.I. (2020). Data Re-uploading for a Universal Quantum Classifier. Quantum 4, 226.

Schuld M., Sweke R., Meyer J.J. (2021). Effect of Data Encoding on the Expressive Power of Variational Quantum-Machine-Learning Models. PRA 103, 032430. — Fourier series representation theorem.

Havlíček V. et al. (2019). Supervised Learning with Quantum-Enhanced Feature Spaces. Nature 567, 209–212. — ZZFeatureMap and QSVM experimental demonstration.

Mari A., Bromley T.R., Izaac J., Schuld M., Killoran N. (2020). Transfer Learning in Hybrid Classical-Quantum Neural Networks. Quantum 4, 340.

Sim S., Johnson P.D., Aspuru-Guzik A. (2019). Expressibility and Entangling Capability of Parameterized Quantum Circuits for Hybrid Quantum-Classical Algorithms. Advanced Quantum Technologies 2, 1900070.

McClean J.R., Boixo S., Smelyanskiy V.N., Babbush R., Neven H. (2018). Barren Plateaus in Quantum Neural Network Training Landscapes. Nature Communications 9, 4812.

### Dequantisation and Classical ML Comparison

Tang E. (2019). A Quantum-Inspired Classical Algorithm for Recommendation Systems. STOC 2019. — The original dequantisation paper.

Tang E. (2021). Quantum Principal Component Analysis Only Achieves an Exponential Speedup Because of Its Superposition of Data Access. Physical Review Letters 127, 060503.

Chia N.H., Li T., Lin H.H., Wang C. (2020). Sampling-Based Sublinear Low-Rank Matrix Arithmetic Framework for Dequantizing Quantum Machine Learning. STOC 2020.

Liu Y., Arunachalam S., Temme K. (2021). A Rigorous and Robust Quantum Speed-Up in Supervised Machine Learning. Nature Physics 17, 1013–1017. — Formal quantum kernel advantage.

Huang H.Y. et al. (2021). Power of Data in Quantum Machine Learning. Nature Communications 12, 2631.

Kerenidis I. & Prakash A. (2017). Quantum Recommendation Systems. ITCS 2017. — The original quantum recommendation algorithm that Tang dequantised.

### PennyLane, PyTorch, and Tools

Bergholm V. et al. (2018). PennyLane: Automatic Differentiation of Hybrid Quantum-Classical Computations. arXiv:1811.04968. — The PennyLane framework paper.

PennyLane Documentation: pennylane.ai/qml. Comprehensive tutorials on QNNs, kernels, transfer learning, and PyTorch integration.

Paszke A. et al. (2019). PyTorch: An Imperative Style, High-Performance Deep Learning Library. NeurIPS 2019. — PyTorch autograd framework.

Qiskit Machine Learning Documentation: qiskit.org/documentation/machine-learning. QiskitML library with QNN, QSVM, and feature map implementations.

Wierichs D., Izaac J., Wang C., Lin C.Y.Y. (2022). General Parameter-Shift Rules for Quantum Gradients. Quantum 6, 677. — Generalised parameter-shift for arbitrary generators.

### Review Articles and Textbooks

Biamonte J. et al. (2017). Quantum Machine Learning. Nature 549, 195–202. — The first major QML review.

Schuld M. & Petruccione F. (2021). Machine Learning with Quantum Computers. Springer. — The most complete textbook on QML.

Cerezo M. et al. (2021). Variational Quantum Algorithms. Nature Reviews Physics 3, 625–644.

Dunjko V. & Briegel H.J. (2018). Machine Learning & Artificial Intelligence in the Quantum Domain. Reports on Progress in Physics 81, 074001.

Preskill J. (2018). Quantum Computing in the NISQ Era and Beyond. Quantum 2, 79. — Essential context for NISQ-era QML.

### India and NQM Context

Department of Science and Technology (2023). National Quantum Mission: Mission Document. Government of India. — The NQM strategic roadmap including AI/ML + quantum integration.

QpiAI (Bengaluru): Indian quantum computing start-up developing hybrid quantum-classical ML frameworks for industrial optimisation.

IIT Madras Quantum Lab, IIT Bombay Quantum Computing Group, IISc Centre for Quantum Information and Quantum Computing — Leading NQM hub institutions for QML research.

CDAC Pune: Developing indigenous quantum computing software stack with QML modules for national applications.

**— End of MPY405 Unit V: Quantum Machine Learning Foundations (Chapters 9–10) —**

Dr. S. K. Jain  ·  Department of Physics

# INDEX

This index covers principal algorithms, complexity classes, error-correcting codes, and machine-learning concepts introduced in this volume.

| Term / Concept | Definition and Location |
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
| Fault-tolerance threshold theorem | Below threshold error rate p_th, larger code distance exponentially suppresses logical error. Ch. 6, §6.2 |
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
| Qiskit Runtime resilience_level | Estimator primitive parameter selecting preset bundles of automatic error mitigation. Ch. 8, §8.3 |
| QNN (Quantum Neural Network) | Encoding + variational + measurement layer architecture for quantum machine learning. Ch. 9, §9.2 |
| Quantum advantage | A quantum computer solving a useful, practically relevant problem faster than any known classical method. Ch. 4, §4.1 |
| Quantum feature map | Circuit U_φ(x) encoding classical data into a quantum state for kernel-based QML. Ch. 9, §9.1 |
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

<div class="box box-generic">
<p class="box-title">A B O U T   T H I S   B O O K</p>
<p><strong>The Complete Guide to Quantum Algorithms &amp; Complexity</strong></p>
<p>From Shor's algorithm to the frontiers of quantum machine learning, this textbook takes M.Sc. Physics students from Volume I's foundations into the algorithms, complexity theory, and error-correcting codes that define what quantum computers can and cannot do. It bridges rigorous theory with hands-on Qiskit programming on real IBM Quantum hardware.</p>
<p><strong>Dr. Sanjeev Kumar Jain</strong></p>
<p><em>Associate Professor and Ex-Head, Dept. of Applied Sciences &amp; Humanities</em></p>
<p><em>Invertis University, Bareilly, U.P., India</em></p>
<p>•  Shor's Algorithm, amplitude amplification &amp; the HHL linear-systems algorithm</p>
<p>•  Advanced circuit simulation: Trotter-Suzuki, qubitisation &amp; variational methods</p>
<p>•  Quantum complexity theory: BQP, QMA, query complexity &amp; the polynomial method</p>
<p>•  Quantum advantage: random circuit sampling, boson sampling, hype vs reality</p>
<p>•  Quantum error correction: stabiliser codes, the Steane code &amp; surface codes</p>
<p>•  Fault tolerance: the threshold theorem &amp; magic state distillation</p>
<p>•  Variational algorithms: QAOA and VQE for real molecular systems</p>
<p>•  Barren plateaus, error mitigation &amp; the limits of near-term (NISQ) advantage</p>
<p>•  Quantum machine learning: kernels, QNNs &amp; the parameter-shift rule</p>
<p>•  Transfer learning, data encoding strategies &amp; dequantisation</p>
</div>

<div class="box box-generic">
<p class="box-title">© 2026 Dr. Sanjeev Kumar Jain. All rights reserved. No part of this publication may be reproduced without written permission of the copyright holder.</p>

</div>
