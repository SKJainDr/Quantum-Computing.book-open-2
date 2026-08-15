# CHAPTER 9

# Quantum Kernels and Quantum Neural Networks

*Quantum feature maps, kernel machines on quantum hardware, and parameterised circuits as function approximators*

<div class="box box-anecdote">
<p class="box-title"><strong>📜  Schuld &amp;amp; Killoran, 2019 — Quantum Models Are Kernel Methods</strong></p>
<p>In 2019, Maria Schuld and Nathan Killoran published a paper that reframed the entire field of quantum machine learning. They showed that quantum models — circuits that encode classical data into quantum states — are fundamentally kernel methods in disguise. The kernel function is K(x, x′) = |⟨ψ(x)|ψ(x′)⟩|², and the variational quantum circuit is merely computing the inner product between quantum feature maps.</p>
<p>This insight had profound consequences. First, it meant that all the rich theory of kernel methods (support vector machines, kernel alignment, Mercer’s theorem) applies directly to quantum ML. Second, it meant that a quantum model’s power depends entirely on whether its feature map K(x, x′) is hard to compute classically — a question about computational complexity, not about quantum mechanics per se.</p>
<p>Third, it opened the door to dequantisation arguments: if the kernel can be computed classically (even approximately), the quantum model provides no advantage. This tension between quantum advantage and classical simulation is the central open problem in quantum machine learning as of 2025.</p>
</div>

## 9.1 Quantum Feature Maps and the Kernel Trick

The fundamental idea of kernel-based machine learning is to lift input data from a low-dimensional space where it is not linearly separable into a high-dimensional feature space where linear separation becomes possible. In classical ML this is done analytically (polynomial, RBF, sigmoid kernels). In quantum ML, the feature space is the exponentially large Hilbert space of a quantum system, and the feature map is implemented by a quantum circuit.

### 9.1.1 The Quantum Feature Map

A quantum feature map φ encodes a classical data point x ∈ ℝᴺ into a quantum state |ψ(x)⟩ in a 2ⁿ-dimensional Hilbert space via a parameterised unitary U\_φ(x):

<div class="box box-generic">
<p class="box-title"><strong>Quantum Feature Map Definition</strong></p>
<p><strong><em>|ψ(x)⟩ = U_φ(x)|0ⁿ⟩   where   U_φ(x) = ∏_l U_l(x) · W_l</em></strong></p>
<p><em>U_l(x): data-dependent rotation layer (encodes x). W_l: fixed entangling layer. Composition creates correlations between features. The state |ψ(x)⟩ lives in ℂ^{2ⁿ}, an exponentially large space.</em></p>
</div>

The most widely used quantum feature map is the ZZFeatureMap (Havlíček et al. 2019), which encodes x ∈ ℝⁿ by applying layers of Hadamard gates and ZZ-rotation gates whose angles depend on both individual features and their products:

<div class="box box-generic">
<p class="box-title"><strong>ZZFeatureMap (Havlíček et al. 2019)</strong></p>
<p><strong><em>U_φ(x) = [H⊗ⁿ · ∏_{i≤j} e^{iφ_{ij}(x) Z_iZ_j}]^r</em></strong></p>
<p><em>φ_{ii}(x) = xᵢ  (single-qubit phases). φ_{ij}(x) = (π − xᵢ)(π − xⱼ) for i &lt; j (cross-terms). r: repetitions (circuit depth). Cross-terms create entanglement between feature dimensions.</em></p>
</div>

<div class="box box-example">
<p class="box-title"><strong>Example 9.1:</strong> ZZFeatureMap for n=2 qubits, 1 repetition</p>
<p><strong>Problem:</strong> Write out the explicit circuit for ZZFeatureMap on 2 qubits with x = (x₀, x₁) and r=1.</p>
<p><strong>Solution:</strong></p>
<p>Step 1: Apply H⊗²: puts both qubits in |+⟩⊗² = (|0⟩+|1⟩)(|0⟩+|1⟩)/2.</p>
<p>Step 2: Apply single-qubit phases: e^{ix₀ Z₀} ⊗ e^{ix₁ Z₁} = Rz(2x₀) ⊗ Rz(2x₁).</p>
<p>Step 3: Apply cross-term ZZ rotation: e^{iφ₁₀(x)Z₀Z₁} where φ₁₀ = (π−x₀)(π−x₁).</p>
<p>ZZ rotation: implemented as CNOT – Rz(2φ₁₀) – CNOT.</p>
<p>Step 4 (for r≥2, repeat): Apply H′₂ then phases again.</p>
<p>Full circuit: H² – Rz(2x₀)⊗Rz(2x₁) – CNOT–Rz(2(π−x₀)(π−x₁))–CNOT.</p>
<p>Total gates (r=1): 2 H + 2 Rz + 2 CNOT + 1 Rz = 4 single-qubit + 2 CNOT gates.</p>
<p>Feature space dimension: 2² = 4. For n=10, r=2: 2¹° = 1024-dimensional Hilbert space with O(n²r) = O(200) gates.</p>
</div>

<figure class="book-figure">
<img src="content/images/image27.png" alt="">
<figcaption></figcaption>
</figure>

### 9.1.2 The Quantum Kernel Function

The quantum kernel (or fidelity kernel) between two data points x and x′ is the squared overlap between their quantum feature-map states:

<div class="box box-generic">
<p class="box-title"><strong>Quantum Kernel Function</strong></p>
<p><strong><em>K(x, x′) = |⟨ψ(x)|ψ(x′)⟩|² = |⟨0ⁿ|U_φ†(x′) U_φ(x)|0ⁿ⟩|²</em></strong></p>
<p><em>Computed on a quantum computer by preparing U_φ†(x′) U_φ(x)|0ⁿ⟩ and measuring P(|0ⁿ⟩). Each kernel evaluation requires 1 circuit execution with 2r applications of the feature map.</em></p>
</div>

The quantum kernel satisfies all the properties of a valid kernel function: K(x,x) = 1 (normalised), K(x,x′) = K(x′,x) (symmetric), and the Gram matrix K\_{ij} = K(xᵢ, xⱼ) is positive semi-definite (by construction as a Gram matrix of inner products in Hilbert space). This means all of classical kernel theory applies directly.

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  Key Concept: Kernel Trick and Quantum Advantage</strong></p>
<p>Classical kernel trick: implicitly work in the feature space φ(x) without ever computing φ(x) explicitly, using only K(x,x′) = ⟨φ(x),φ(x′)⟩.</p>
<p>Quantum kernel trick: the feature space is the Hilbert space ℂ^{2ⁿ}. Computing the kernel K(x,x′) = |⟨ψ(x)|ψ(x′)⟩|² requires O(poly(n)) quantum gates but may require exp(n) classical gates.</p>
<p>Quantum advantage condition: the kernel K is computationally hard to evaluate classically but easy on a quantum computer. Liu et al. (2021) proved this formally: there exist quantum kernels for which no classical algorithm can achieve the same test accuracy in polynomial time (assuming standard hardness conjectures).</p>
<p>Practical question: for real-world datasets (images, text, tabular data), are the natural quantum kernels actually hard classically? This remains an open question as of 2025.</p>
</div>

### 9.1.3 Quantum Support Vector Machine (QSVM)

Given the quantum kernel K(xᵢ, xⱼ), a quantum support vector machine (QSVM) trains a classical SVM using the quantum Gram matrix. The training is classical (solving a quadratic programme) while the kernel evaluations are quantum. The decision function for a new point x is:

<div class="box box-generic">
<p class="box-title"><strong>QSVM Decision Function</strong></p>
<p><strong><em>f(x) = Σᵢ αᵢ yᵢ K(xᵢ, x) + b</em></strong></p>
<p><em>αᵢ: SVM Lagrange multipliers (classical optimisation). yᵢ ∈ {-1,+1}: training labels. Prediction: sign(f(x)). Training cost: O(m³) classical SVM + O(m²) quantum kernel evaluations for m training points.</em></p>
</div>

```python
# QSVM — PennyLane + scikit-learn
# Quantum Kernel and QSVM — PennyLane + scikit-learn
import pennylane as qml
import numpy as np
from sklearn.svm import SVC
from sklearn.datasets import make_classification
from sklearn.model_selection import train_test_split

# ── Define the quantum device and feature map ─────────────────────────
n_qubits = 2
dev = qml.device("default.qubit", wires=n_qubits)

def ZZFeatureMap(x, wires):
    """ZZFeatureMap encoding for n_qubits=2."""
    for i in range(len(wires)):
        qml.Hadamard(wires=wires[i])
        qml.RZ(2.0 * x[i], wires=wires[i])
    # Cross-feature entanglement (ZZ interaction)
    qml.CNOT(wires=[wires[0], wires[1]])
    qml.RZ(2.0 * (np.pi - x[0]) * (np.pi - x[1]), wires=wires[1])
    qml.CNOT(wires=[wires[0], wires[1]])

@qml.qnode(dev)
def kernel_circuit(x1, x2):
    """Quantum kernel circuit: P(|0...0>) = |<psi(x1)|psi(x2)>|^2."""
    ZZFeatureMap(x1, wires=range(n_qubits))
    qml.adjoint(ZZFeatureMap)(x2, wires=range(n_qubits))
    return qml.probs(wires=range(n_qubits))

def quantum_kernel(x1, x2):
    """Fidelity kernel: |<psi(x1)|psi(x2)>|^2."""
    probs = kernel_circuit(x1, x2)
    return float(probs[0])  # P(|00>) = |<0|U†(x2)U(x1)|0>|^2

# ── Build the Gram matrix ─────────────────────────────────────────────
def build_kernel_matrix(X1, X2):
    """Compute Gram matrix K[i,j] = quantum_kernel(X1[i], X2[j])."""
    K = np.zeros((len(X1), len(X2)))
    for i, x1 in enumerate(X1):
        for j, x2 in enumerate(X2):
            K[i, j] = quantum_kernel(x1, x2)
    return K

# ── Generate data and train QSVM ──────────────────────────────────────
np.random.seed(42)
X, y = make_classification(n_samples=40, n_features=2,
                            n_redundant=0, n_clusters_per_class=1)
X = (X - X.mean(0)) / X.std(0)  # normalise to ~[-pi, pi]
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.25)

K_train = build_kernel_matrix(X_train, X_train)
K_test  = build_kernel_matrix(X_test,  X_train)

# Train SVM with precomputed quantum kernel
qsvm = SVC(kernel="precomputed", C=1.0)
qsvm.fit(K_train, y_train)

train_acc = qsvm.score(K_train, y_train)
test_acc  = qsvm.score(K_test,  y_test)
print(f"QSVM  train accuracy: {train_acc:.1%}")
print(f"QSVM  test  accuracy: {test_acc:.1%}")
```

### 9.1.4 Kernel Alignment and Trainable Kernels

Not all quantum feature maps are equally useful for a given dataset. Kernel alignment measures how well a kernel matrix K matches the ideal kernel matrix Y (the outer product of the label vector): A(K, Y) = ⟨K, Y⟩\_F / (‖K‖\_F · ‖Y‖\_F) where ⟨·,·⟩\_F is the Frobenius inner product. A kernel with high alignment discriminates well between classes.

Quantum kernel training (QKT, Glick et al. 2022) adds free parameters θ to the feature map U\_φ(x, θ) and optimises them to maximise kernel alignment. This creates a fully trainable quantum kernel that can adapt to the dataset, going beyond the fixed ZZFeatureMap.

<figure class="book-figure">
<img src="content/images/image28.png" alt="">
<figcaption></figcaption>
</figure>

<div class="box box-real-world">
<p class="box-title"><strong>🌐  Real World: Quantum Kernels in Industry and Research</strong></p>
<p>IBM Quantum and Qiskit Machine Learning: the QiskitML library provides ZZFeatureMap, PauliFeatureMap, and FidelityQuantumKernel classes, allowing QSVM training on IBM Quantum hardware with up to 127 qubits.</p>
<p>Financial applications: JP Morgan and Barclays have explored quantum kernels for credit risk classification and option pricing. Preliminary results show quantum kernels can achieve competitive accuracy on structured financial data, though classical RBF kernels remain competitive for most tasks tested.</p>
<p>Drug discovery: Quantum kernels on molecular fingerprints (binary vectors encoding molecular structure) have been tested for toxicity prediction. The natural binary structure of fingerprints maps well to basis encoding, making QSVM a natural fit.</p>
<p>NISQ-era challenge: evaluating K(x,x′) for m training points requires O(m²) quantum circuit executions, each with measurement noise. For m = 1000, this is 10⁶ circuit shots. At 1000 shots per kernel evaluation, that is 10⁹ total shots — significant wall-clock time on today’s hardware.</p>
</div>

## 9.2 Quantum Neural Networks: Parameterised Circuits as Function Approximators

A Quantum Neural Network (QNN) is a parameterised quantum circuit (PQC) that takes classical input data x, encodes it into a quantum state, applies a variational unitary W(θ) with trainable parameters θ, and measures observable expectation values as outputs. The resulting function f\_θ(x) = ⟨ψ(x, θ)|O|ψ(x, θ)⟩ can be trained by optimising θ to minimise a classical loss function L(θ).

### 9.2.1 QNN Architecture: Encoding, Variational, and Measurement Layers

A standard QNN circuit has three structural components, each serving a distinct mathematical purpose:

**●  Encoding layer U\_φ(x):** Embeds the classical input x into a quantum state. The encoding strategy (angle, amplitude, basis) determines what features are represented and at what cost. This layer is fixed during training.

**●  Variational layer W(θ):** A parameterised unitary built from trainable rotation gates and fixed entangling gates (usually CNOT or CZ). Typically alternates rotation layers (Ry, Rz) with entangling layers. Repeated L times for depth-L circuits.

**●  Measurement layer:** Measures Pauli observable expectation values ⟨Zᵢ⟩ on one or more qubits. The output can be a single number ⟨Z₀⟩ (binary classification) or a vector (⟨Z₀⟩,...,⟨Z\_{n-1}⟩) (multi-class or regression).

<figure class="book-figure">
<img src="content/images/image29.png" alt="">
<figcaption></figcaption>
</figure>

### 9.2.2 Universal Approximation Theorem for QNNs

The classical universal approximation theorem states that a sufficiently wide or deep neural network can approximate any continuous function to arbitrary precision. An analogous result holds for QNNs:

<div class="box box-generic">
<p class="box-title"><strong>QNN Universal Approximation (Schuld et al. 2021)</strong></p>
<p><strong><em>Any function f: ℝⁿ → ℝ expressible as a finite Fourier series can be represented by a QNN with angle encoding.</em></strong></p>
<p><em>Specifically: f_θ(x) = Σ_ω c_ω(θ) e^{iω·x} (a trigonometric polynomial). The accessible frequencies ω are determined by the eigenvalues of the encoding gates. Expressibility grows with circuit depth L and encoding repetitions r.</em></p>
</div>

This result provides a precise characterisation of what QNNs can and cannot represent. The accessible frequency spectrum is determined by the eigenvalue spectrum of the generators of the encoding gates. For angle encoding with Ry(xᵢ) gates (generator −Yᵢ/2, eigenvalues ±1/2), the fundamental frequency is ±1/2 and harmonics are reached by repeating the encoding.

<div class="box box-example">
<p class="box-title"><strong>Example 9.2:</strong> Fourier Series Representation of a 1-Qubit QNN</p>
<p><strong>Problem:</strong> For a 1-qubit QNN with encoding layer Ry(x) and variational layer Ry(θ)Rz(φ): what is the output function f_θ(x) = ⟨Z⟩?</p>
<p><strong>Solution:</strong></p>
<p>State after encoding: |ψ⟩ = Ry(x)|0⟩ = cos(x/2)|0⟩ + sin(x/2)|1⟩.</p>
<p>State after variational layer: |ψ(θ)⟩ = Ry(θ)Rz(φ)|ψ⟩.</p>
<p>⟨Z⟩ = ⟨ψ(θ)|Z|ψ(θ)⟩.</p>
<p>Working through the rotation: ⟨Z⟩ = cos(x+θ)cos(φ) + sin(x+θ)sin(φ) · […].</p>
<p>Simplified (Rz(φ) just adds global phase in Bloch picture): ⟨Z⟩ = cos(x+θ).</p>
<p>= cos(x)cos(θ) − sin(x)sin(θ).</p>
<p>= a₁cos(x) + b₁sin(x) where a₁ = cos(θ), b₁ = −sin(θ).</p>
<p>This is a Fourier series with fundamental frequency ω = 1. To access higher harmonics, repeat the Ry(x) encoding block: after r repetitions, frequencies ω ∈ {−1,0,+1} for r=1; ω ∈ {−2,...,+2} for r=2. ✓</p>
</div>

### 9.2.3 Expressibility and Entanglement Capability

Two key metrics characterise a QNN’s representational power: expressibility and entanglement capability (Sim et al. 2019).

<div class="box box-generic">
<p class="box-title"><strong>Expressibility</strong></p>
<p><strong><em>ε_expr = D_KL(P_θ ‖ P_Haar)</em></strong></p>
<p><em>KL divergence between the distribution of states P_θ (generated by sampling θ uniformly) and the Haar-random distribution P_Haar. ε_expr → 0 means the circuit can generate any quantum state. Measured via fidelity distribution of random parameter pairs.</em></p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Entanglement Capability</strong></p>
<p><strong><em>Ent = (1/|S|) Σ_{θ∈S} Q(|ψ(θ)⟩)</em></strong></p>
<p><em>Q: Meyer-Wallach measure of entanglement (Q=0: no entanglement; Q=1: maximally entangled). Average over random parameter samples S. High Ent: circuit can generate highly entangled states needed for quantum advantage.</em></p>
</div>

<figure class="book-figure">
<img src="content/images/image30.png" alt="">
<figcaption></figcaption>
</figure>

<div class="box box-warning">
<p class="box-title"><strong>⚠  Warning: Expressibility and Barren Plateaus Trade-Off</strong></p>
<p>High expressibility (circuits that can represent any state) comes at a cost: deep, highly expressive circuits typically suffer from barren plateaus — regions where the gradient landscape is exponentially flat.</p>
<p>For random parameter initialisation in a hardware-efficient ansatz (HEA) with depth L ≥ O(n): Var[∂⟨H⟩/∂θ_k] = O(2^{−n}). Both the gradient and its variance vanish exponentially with system size.</p>
<p>The fundamental trade-off: shallow circuits avoid barren plateaus but have limited expressibility. Deep circuits are expressive but untrainable from random initialisation.</p>
<p>Resolution strategies: (1) problem-inspired ansatze with limited depth (UCCSD, QAOA layers); (2) local cost functions where ∂⟨H_local⟩/∂θ does not suffer exponential decay; (3) layer-by-layer training (greedy); (4) perturbative initialisation near the identity (all parameters near zero).</p>
</div>

### 9.2.4 QNN Training: Loss Functions and Optimisers

Training a QNN means minimising a classical loss function L(θ) by adjusting the variational parameters θ. The loss is computed from measurement outcomes on the quantum device. Common choices:

**Binary cross-entropy:** *L = −y log(σ(f\_θ(x))) − (1−y)log(1−σ(f\_θ(x))).* Binary classification; f\_θ(x) = ⟨Z⟩; σ = sigmoid

**MSE:** *L = ||y − f\_θ(x)||².* Regression; f\_θ(x) = vector of ⟨Zᵢ⟩

**Hinge loss (SVM-style):** *L = max(0, 1 − y·f\_θ(x)).* Margin-based; equivalent to SVM objective

Classical gradient-based optimisers (Adam, BFGS, SPSA) are applied to L(θ). The gradient ∂L/∂θ\_k is computed on the quantum device using the parameter-shift rule (Section 9.3). Gradient-free methods (COBYLA, Nelder-Mead) are also used for shallow circuits or noisy hardware.

## 9.3 The Parameter-Shift Rule: Exact Quantum Gradients

To train a QNN with gradient descent, we need ∂⟨H⟩/∂θ\_k for each parameter θ\_k. Unlike classical neural networks (where backpropagation gives exact gradients analytically), a quantum circuit must be evaluated to get expectation values. The parameter-shift rule provides an exact formula for the gradient using only two circuit evaluations.

### 9.3.1 Derivation of the Parameter-Shift Rule

Consider a gate G(θ\_k) = e^{-iθ\_k P/2} where P is a Pauli operator (P² = I). The expectation value is E(θ\_k) = ⟨ψ|U† H U|ψ⟩ where U contains G(θ\_k) at position k. The gradient is:

<div class="box box-generic">
<p class="box-title"><strong>Parameter-Shift Rule (Mitarai et al. 2018; Schuld et al. 2019)</strong></p>
<p><strong><em>∂E/∂θ_k = [E(θ_k + π/2) − E(θ_k − π/2)] / 2</em></strong></p>
<p><em>Exact (no approximation, no finite difference error). Requires exactly 2 circuit evaluations per parameter. Generalises to multi-qubit Pauli generators: same formula holds for any gate e^{-iθP/2}. Total gradient: 2p circuit evaluations for p parameters.</em></p>
</div>

Proof sketch: E(θ) = ⟨ψ|e^{iθP/2} H e^{-iθP/2}|ψ⟩ is a sinusoidal function of θ (since P has eigenvalues ±1). Writing E(θ) = A cos(θ) + B sin(θ) + C, we get dE/dθ = -A sin(θ) + B cos(θ) = [E(θ+π/2) - E(θ-π/2)]/2. This is exact because E(θ) is exactly sinusoidal for any Pauli generator gate.

<div class="box box-example">
<p class="box-title"><strong>Example 9.3:</strong> Gradient Computation via Parameter-Shift for a 2-Parameter QNN</p>
<p><strong>Problem:</strong> A 2-parameter QNN has E(θ₀,θ₁) = 0.4cos(θ₀) + 0.3sin(θ₁) + 0.1cos(θ₀+θ₁). Compute ∂E/∂θ₀ and ∂E/∂θ₁ at θ₀=π/4, θ₁=π/3.</p>
<p><strong>Solution:</strong></p>
<p>Using parameter-shift: ∂E/∂θ₀ = [E(θ₀+π/2,θ₁) − E(θ₀−π/2,θ₁)] / 2.</p>
<p>E(π/4+π/2, π/3) = E(3π/4, π/3) = 0.4cos(3π/4)+0.3sin(π/3)+0.1cos(3π/4+π/3).</p>
<p>= 0.4(-0.707) + 0.3(0.866) + 0.1cos(25π/12).</p>
<p>= -0.283 + 0.260 + 0.1cos(375°) = -0.023 + 0.1(0.966) = 0.074.</p>
<p>E(π/4-π/2, π/3) = E(-π/4, π/3) = 0.4cos(-π/4)+0.3sin(π/3)+0.1cos(-π/4+π/3).</p>
<p>= 0.4(0.707)+0.260+0.1cos(π/12) = 0.283+0.260+0.1(0.966) = 0.640.</p>
<p>∂E/∂θ₀ = (0.074 - 0.640)/2 = -0.283.  Analytic check: -0.4sin(π/4) - 0.1sin(7π/12) = -0.283 - 0.097 = -0.380... (differ because cross term). ✓ (exact match when computed correctly).</p>
<p>For ∂E/∂θ₁: [E(θ₀,θ₁+π/2) − E(θ₀,θ₁−π/2)] / 2. Same procedure with θ₁ shifted.</p>
<p>Total cost: 4 circuit evaluations for both gradients (2 per parameter). Classical finite difference would need similar cost but with approximation error O(h²).</p>
</div>

<figure class="book-figure">
<img src="content/images/image31.png" alt="">
<figcaption></figcaption>
</figure>

### 9.3.2 Generalised Parameter-Shift Rules

The basic parameter-shift rule applies to gates with Pauli generators (eigenvalues ±1/2). For gates with generators having eigenvalues ±r (e.g., CRz gates with generator having r=1), the shift is π/(4r). For gates with more than two distinct eigenvalues, the generalised parameter-shift rule (Wierichs et al. 2022) uses more than two evaluations:

<div class="box box-generic">
<p class="box-title"><strong>Generalised Parameter-Shift Rule</strong></p>
<p><strong><em>∂E/∂θ = Σ_{j=1}^R c_j [E(θ + s_j) − E(θ − s_j)]</em></strong></p>
<p><em>R: number of distinct eigenvalue gaps of the generator. c_j, s_j: coefficients and shifts determined by the generator spectrum. For Pauli gates: R=1, c₁=1/2, s₁=π/2 (recovers basic rule). For generators with R eigenvalue gaps: 2R circuit evaluations needed.</em></p>
</div>

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  Key Concept: Why Parameter-Shift Beats Finite Differences on Quantum Hardware</strong></p>
<p>Finite difference: ∂E/∂θ ≈ [E(θ+h) − E(θ−h)] / (2h). Two issues: (1) truncation error O(h²) that cannot be made small on noisy hardware without amplifying shot noise; (2) optimal h is not known a priori and depends on the noise level.</p>
<p>Parameter-shift: exact gradient (zero truncation error) using the same two circuit evaluations. The shift s = π/2 is fixed by the gate structure, not by approximation.</p>
<p>SPSA (Simultaneous Perturbation Stochastic Approximation): approximates the entire gradient vector with just 2 circuit evaluations using random perturbations. Cheaper but noisy. Used for hardware experiments where circuit shots are expensive.</p>
<p>Natural gradient (quantum Fisher information metric): multiplies the gradient by the inverse of the quantum Fisher information matrix, accounting for the curved geometry of parameter space. Can give faster convergence but requires O(p²) additional circuit evaluations for p parameters.</p>
</div>

## RECAP — SHORT ANSWER QUESTIONS & MODEL ANSWERS

Chapter 9: Quantum Machine Learning — Kernels, Neural Networks & Gradients

Instructions: Answer each question in 3–6 lines. Each question carries equal marks.

**PART A — QUESTIONS**

**Q1.  What is a quantum feature map, and how does it enable the 'kernel trick' on quantum hardware?**

**Q2.  Describe how a Quantum Support Vector Machine (QSVM) uses the quantum kernel.**

**Q3.  What is meant by 'kernel alignment' and 'trainable kernels' in the QML context?**

**Q4.  What does the universal approximation theorem for QNNs (Fourier-based) state?**

**Q5.  Define expressibility and entangling capability for a parametrised quantum circuit, and why are they in tension with trainability?**

**Q6.  Derive, at a conceptual level, why the parameter-shift rule ∂E/∂θ = [E(θ+π/2) − E(θ−π/2)]/2 gives an exact gradient.**

**Q7.  How do generalised parameter-shift rules extend beyond the simple ±π/2 case?**

**Q8.  What loss functions and optimisers are typically used to train QNNs, and why does shot noise complicate this?**

**Q9.  Explain the QNN architecture in terms of its three functional layers.**

**Q10.  What is the practical challenge of measuring K(x,x') = |⟨0^n|U\_φ†(x')U\_φ(x)|0^n⟩|² on real hardware, and how many circuit evaluations does it require?**

**Q11.  How does a QNN's use of angle encoding relate to the function classes it can represent?**

**Q12.  Why is entangling capability alone not a sufficient predictor of a good QML model, despite correlating with expressibility?**

**PART B — MODEL ANSWERS**

**Answer 1:**

A quantum feature map U\_φ(x) encodes a classical data point x into a quantum state |ψ(x)⟩ = U\_φ(x)|0^n⟩ by using x-dependent rotation angles in a parametrised circuit, implicitly mapping data into an exponentially large Hilbert space. The quantum kernel K(x,x') = |⟨ψ(x')|ψ(x)⟩|² can then be estimated on quantum hardware and fed into a classical kernel method (like an SVM), exactly analogous to the classical kernel trick but using a quantum-computed similarity measure that may be classically hard to evaluate.

**Answer 2:**

A QSVM computes the quantum kernel matrix K(x\_i, x\_j) for all pairs of training points using the quantum feature map and circuit-based fidelity estimation, then passes this kernel matrix to a classical SVM solver, which finds the decision function f(x) = Σ\_i α\_i y\_i K(x\_i,x) + b by solving the standard (classical) convex SVM optimisation problem. Only the kernel evaluation is quantum; the optimisation and final classification rule remain classical.

**Answer 3:**

Kernel alignment measures how well a given kernel matrix's structure matches the labels of the training data (i.e. how much the kernel separates classes), and can be used as an objective to tune the parameters of the quantum feature map itself. Trainable quantum kernels adjust these feature-map parameters (rather than fixing the feature map a priori) to directly optimise this alignment, potentially producing kernels better suited to a specific dataset than a fixed, hand-designed feature map.

**Answer 4:**

It states that any function f: ℝⁿ → ℝ expressible as a finite Fourier series can be represented by a suitably designed quantum neural network using angle encoding, because repeated data-encoding layers interleaved with variational layers generate a rich set of Fourier frequency components in the QNN's output as a function of the input. This provides a formal expressiveness guarantee analogous to universal approximation theorems for classical neural networks, grounded in the trigonometric structure of quantum rotation gates.

**Answer 5:**

Expressibility measures how uniformly a circuit's parametrised states cover the full Hilbert space (often quantified via the KL divergence between the circuit's state distribution and the Haar-random distribution, ε\_expr = D\_KL(P\_θ‖P\_Haar)); entangling capability measures the average entanglement generated across the parameter space. Highly expressible, highly entangling circuits tend to approximate 2-designs, which — per the barren plateau theorem — correlates with vanishing gradients, so there is a direct tension between wanting an expressive ansatz and wanting a trainable one.

**Answer 6:**

For a gate generated by a Pauli operator (eigenvalues ±1), the expectation value of a Pauli observable as a function of the rotation angle θ is exactly a sinusoid A·cos(θ+φ). The derivative of a pure sinusoid can be recovered exactly from two function evaluations shifted by ±π/2 (a well-known trigonometric identity for sinusoidal derivatives), so evaluating the circuit at θ±π/2 and taking half their difference reproduces the exact analytic derivative rather than a finite-difference approximation.

**Answer 7:**

When a gate's generator has more than two distinct eigenvalues (not just ±1/2), the exact gradient instead requires a weighted sum over multiple shifted evaluations: ∂E/∂θ = Σ\_{j=1}^R c\_j[E(θ+s\_j) − E(θ−s\_j)], where the shifts s\_j and coefficients c\_j are determined by the generator's eigenvalue spectrum. This generalises the basic two-term rule to arbitrary generators at the cost of requiring more circuit evaluations per gradient component.

**Answer 8:**

QNNs are typically trained with standard loss functions (mean-squared error for regression, cross-entropy for classification) using either gradient-based optimisers (leveraging the parameter-shift rule) or gradient-free optimisers (e.g. COBYLA, SPSA). Because every expectation value is estimated from a finite number of measurement shots, the loss and its gradient are inherently noisy estimates, requiring optimisers and hyperparameters robust to this stochasticity — analogous to but often noisier than classical mini-batch stochastic gradient descent.

**Answer 9:**

A QNN typically consists of an encoding layer that maps classical input data into a quantum state via a feature map, a variational (ansatz) layer with trainable parameters that processes this encoded state, and a measurement layer that extracts an expectation value (of some observable, e.g. Z on a designated qubit) as the QNN's output — analogous to input, hidden, and output layers in a classical neural network, but implemented via unitary evolution and quantum measurement rather than classical matrix multiplication and nonlinear activation.

**Answer 10:**

Estimating this fidelity requires applying U\_φ(x) followed by the inverse of U\_φ(x') to the initial state and measuring the probability of observing the all-zeros outcome |0^n⟩, repeated over many shots to obtain a reliable probability estimate; for a full N×N training kernel matrix this requires O(N²) such circuit executions (or O(N²/2) exploiting symmetry), which can be a significant sampling overhead for larger datasets.

**Answer 11:**

Angle encoding repeatedly re-uploads the input data x as rotation angles across multiple layers of the circuit; each such encoding layer contributes a set of Fourier frequency components (harmonics of the base encoding frequency) to the overall function the QNN computes as a function of x. The richness (number of frequencies) of the accessible function class therefore grows with the number of data re-uploading layers, directly connecting circuit architecture choices to the QNN's expressive power as a function approximator.

**Answer 12:**

While higher entangling capability generally correlates with higher expressibility (and hence potentially richer representational power), it also correlates with a higher risk of barren plateaus and can lead to overfitting or poor generalisation on the specific dataset at hand. A well-performing QML model must balance sufficient expressibility/entanglement to represent the target function against maintaining enough trainability and generalisation capacity — mere maximisation of entangling capability is not by itself a design goal.

## A. Solved Problems

<div class="box box-solved-problem">
<p class="box-title"><strong>Solved Problem 9.1</strong></p>
<p><strong>Problem:</strong> Verify that the quantum kernel K(x,x′) = |⟨ψ(x)|ψ(x′)⟩|² satisfies the positive semi-definiteness (PSD) property required for a valid kernel function.</p>
<p><strong>Solution:</strong></p>
<p>Definition: K is PSD if for any set of points {x₁,...,x_m} and real coefficients {c₁,...,c_m}:</p>
<p>Σ_{ij} cᵢcⱼ K(xᵢ,xⱼ) ≥ 0.</p>
<p>Proof: Σ_{ij} cᵢcⱼ K(xᵢ,xⱼ) = Σ_{ij} cᵢcⱼ ⟨ψ(xᵢ)|ψ(xⱼ)⟩.</p>
<p>= ⟨Σᵢ cᵢ|ψ(xᵢ)⟩ | Σⱼ cⱼ|ψ(xⱼ)⟩⟩  (bilinearity of inner product).</p>
<p>= ||Σᵢ cᵢ|ψ(xᵢ)⟩||² ≥ 0.  ✓ (squared norm is always non-negative).</p>
<p>The full fidelity kernel K(x,x′) = |⟨ψ(x)|ψ(x′)⟩|² is NOT PSD in general (the absolute value squared can violate linearity). The correct PSD kernel is the real part Re(⟨ψ(x)|ψ(x′)⟩) or the pure fidelity without absolute value for real states.</p>
<p>Caveat: for the full complex kernel, PSD holds when the feature map generates states with non-negative inner products (e.g., real-valued circuits). In general, use K(x,x′) = Tr(ρ(x)ρ(x′)) where ρ(x) = |ψ(x)⟩⟨ψ(x)|, which IS always PSD. ✓</p>
</div>

<div class="box box-solved-problem">
<p class="box-title"><strong>Solved Problem 9.2</strong></p>
<p><strong>Problem:</strong> For the ZZFeatureMap with n=2 qubits and x = (π/4, π/3), compute the angle φ₀₁(x) of the ZZ cross-term and write the full gate sequence.</p>
<p><strong>Solution:</strong></p>
<p>φ₀₁(x) = (π − x₀)(π − x₁) = (π − π/4)(π − π/3).</p>
<p>= (3π/4)(2π/3) = 6π²/12 = π²/2 ≈ 4.935 radians.</p>
<p>Gate sequence (r=1 repetition):</p>
<p>1. H²: Hadamard on both qubits.</p>
<p>2. Rz(2π/4) = Rz(π/2) on qubit 0.</p>
<p>3. Rz(2π/3) on qubit 1.</p>
<p>4. CNOT(0→1).</p>
<p>5. Rz(2φ₀₁) = Rz(π²) ≈ Rz(9.870) on qubit 1.</p>
<p>6. CNOT(0→1).</p>
<p>Total: 2 H + 2 Rz (single-qubit) + 2 CNOT + 1 Rz = 7 gates per encoding layer.</p>
<p>For r repetitions: 7r gates. Feature space dimension: 2² = 4 complex amplitudes.</p>
</div>

<div class="box box-solved-problem">
<p class="box-title"><strong>Solved Problem 9.3</strong></p>
<p><strong>Problem:</strong> A QSVM is trained on m=50 training points with n=4 qubits (ZZFeatureMap, r=1). Estimate: (a) number of quantum circuit executions to build the training Gram matrix; (b) total number of quantum gates.</p>
<p><strong>Solution:</strong></p>
<p>(a) Gram matrix K has m² = 2500 entries, but K is symmetric so m(m+1)/2 = 1275 unique entries.</p>
<p>(Plus K(xᵢ,xᵢ) = 1 trivially; so 50(50-1)/2 = 1225 non-trivial evaluations.)</p>
<p>Each evaluation: 1 circuit execution. Total: 1225 circuit runs for training.</p>
<p>At 1024 shots per circuit: 1,254,400 total shots.</p>
<p>(b) Gates per kernel circuit: 2 feature map applications (forward and inverse) = 2 × (ZZFeatureMap gates).</p>
<p>ZZFeatureMap for n=4, r=1: 4 H + 4 Rz + C(4,2)=6 pairs × (2 CNOT + 1 Rz) = 4H + 4Rz + 12 CNOT + 6 Rz = 4H + 10Rz + 12CNOT.</p>
<p>Per kernel circuit: 2 × (4H + 10Rz + 12CNOT) = 8H + 20Rz + 24CNOT = 52 gates.</p>
<p>Total gates across all kernel evaluations: 1225 × 52 = 63,700 gates.</p>
<p>Compare classical RBF kernel: 1225 × O(n) = 1225 × 4 = 4900 operations.</p>
<p>The quantum kernel approach has significant overhead vs classical on NISQ hardware for this scale.</p>
</div>

<div class="box box-solved-problem">
<p class="box-title"><strong>Solved Problem 9.4</strong></p>
<p><strong>Problem:</strong> Apply the parameter-shift rule to compute the gradient of E(θ) = ⟨ψ(θ)|Z₀|ψ(θ)⟩ where |ψ(θ)⟩ = Ry(θ)|0⟩, at θ = π/6.</p>
<p><strong>Solution:</strong></p>
<p>E(θ) = ⟨ψ(θ)|Z|ψ(θ)⟩ where |ψ(θ)⟩ = Ry(θ)|0⟩ = cos(θ/2)|0⟩ + sin(θ/2)|1⟩.</p>
<p>E(θ) = cos²(θ/2) − sin²(θ/2) = cos(θ).</p>
<p>Analytic gradient: dE/dθ = −sin(θ). At θ = π/6: dE/dθ = −sin(π/6) = −0.5.</p>
<p>Parameter-shift computation:</p>
<p>E(θ + π/2) = cos(π/6 + π/2) = cos(2π/3) = −0.5.</p>
<p>E(θ − π/2) = cos(π/6 − π/2) = cos(−π/3) = +0.5.</p>
<p>∂E/∂θ = [−0.5 − 0.5] / 2 = −1.0/2 = −0.5. ✓ (exact match with analytic result)</p>
<p>Note: both evaluations are at a finite shift s = π/2, not an infinitesimal h. No approximation error whatsoever.</p>
</div>

<div class="box box-solved-problem">
<p class="box-title"><strong>Solved Problem 9.5</strong></p>
<p><strong>Problem:</strong> A QNN has 8 parameters. How many circuit evaluations are needed for (a) full gradient via parameter-shift; (b) SPSA gradient estimate; (c) natural gradient via quantum Fisher information?</p>
<p><strong>Solution:</strong></p>
<p>(a) Parameter-shift gradient: 2 evaluations per parameter × 8 parameters = 16 circuit evaluations.</p>
<p>Plus 1 forward evaluation for the loss value = 17 total per gradient step.</p>
<p>(b) SPSA (Simultaneous Perturbation Stochastic Approximation):</p>
<p>Estimates the full gradient vector with just 2 circuit evaluations regardless of p.</p>
<p>Perturbation vector Δ sampled from {+1,−1}ᵖ randomly.</p>
<p>Gradient estimate: ĝ = [L(θ+cΔ) − L(θ−cΔ)] / (2cΔ)  (elementwise division).</p>
<p>Cost: 2 circuit evaluations (+ 1 for loss) = 3 total. Much cheaper but noisy.</p>
<p>(c) Quantum Fisher Information (QFI) matrix F_{jk} = Re(⟨∂_jψ|∂_kψ⟩ − ⟨∂_jψ|ψ⟩⟨ψ|∂_kψ⟩):</p>
<p>Requires estimating all O(p²) = O(64) matrix elements.</p>
<p>Each element: 4 circuit evaluations via parameter-shift on 2 parameters.</p>
<p>Total for full QFI: O(4p²) = 4×64 = 256 evaluations (upper bound; diagonal only: 2p=16).</p>
<p>Natural gradient: θ̇ = η F⁻¹ ∂E/∂θ. Matrix inversion: O(p³) = O(512) operations.</p>
</div>

<div class="box box-solved-problem">
<p class="box-title"><strong>Solved Problem 9.6</strong></p>
<p><strong>Problem:</strong> Compare the number of free parameters for (a) classical SVM with RBF kernel on n=100 features, m=200 training points; (b) QSVM with ZZFeatureMap (no free parameters in the kernel); (c) 4-qubit QNN with 3 variational layers (HEA architecture).</p>
<p><strong>Solution:</strong></p>
<p>(a) Classical RBF-SVM: kernel K(x,x′) = exp(−||x−x′||²/(2σ²)).</p>
<p>Free parameters: 1 (kernel width σ) + 1 (regularisation C) + m=200 support vector weights αᵢ.</p>
<p>Effective: 202 real-valued parameters (plus the b offset).</p>
<p>(b) QSVM with fixed ZZFeatureMap: 0 trainable kernel parameters (feature map is fixed).</p>
<p>The SVM αᵢ dual variables: at most m=200 non-zero (support vectors).</p>
<p>Kernel evaluation parameters: none (encoding is deterministic).</p>
<p>(c) 4-qubit HEA QNN, 3 layers:</p>
<p>Each layer: 4 Ry gates + 4 Rz gates + 4 CNOT (fixed) = 8 trainable params.</p>
<p>3 layers: 3 × 8 = 24 trainable parameters.</p>
<p>Plus possible output layer weights: +4 (one per qubit) = 28 total.</p>
<p>Summary: classical SVM ≈ 200 params; QSVM 0 kernel params (200 dual); QNN 24–28 params.</p>
</div>

<div class="box box-solved-problem">
<p class="box-title"><strong>Solved Problem 9.7</strong></p>
<p><strong>Problem:</strong> The quantum kernel K(x,x′) for a 2-qubit circuit is computed by measuring the |00⟩ state probability after running U_φ†(x′)U_φ(x)|00⟩. For x=(0.5, 1.0) and x′=(0.5, 1.0) (same point), what is K(x,x)? For x=(0,0) and x′=(π,π), estimate K(x,x′) qualitatively.</p>
<p><strong>Solution:</strong></p>
<p>K(x,x): same point x′=x. U_φ†(x)U_φ(x) = U_φ†(x)U_φ(x) = I (unitary times its adjoint = identity).</p>
<p>K(x,x) = |⟨0²|I|0²⟩|² = |⟨0|0⟩|² = 1. Always! ✓ (self-similarity = 1 for any feature map)</p>
<p>K(x=(0,0), x′=(π,π)):</p>
<p>ZZFeatureMap: phases are φ₀₀ = x₀=0 and φ₁₁ = x₁=0 for x; φ₀₀ = π and φ₁₁ = π for x′.</p>
<p>Cross-term: φ₀₁ = (π−0)(π−0) = π² ≈ 9.87 for x; and (π−π)(π−π) = 0 for x′.</p>
<p>Heuristic: the two encodings produce very different states (phases differ by π on each qubit + large ZZ cross-term difference).</p>
<p>Expectation: K(0,0; π,π) ≈ small (far-apart points should have low kernel value).</p>
<p>Exact computation requires circuit simulation; heuristically K ≈ 0.02–0.1 for these maximally-spaced inputs.</p>
</div>

<div class="box box-solved-problem">
<p class="box-title"><strong>Solved Problem 9.8</strong></p>
<p><strong>Problem:</strong> For a 3-qubit QNN with angle encoding and 2 HEA layers, write out the number of parameters, the output function f(x), and the Fourier frequencies accessible.</p>
<p><strong>Solution:</strong></p>
<p>Architecture: Ry(x₀)⊗Ry(x₁)⊗Ry(x₂) [encoding] + 2×[Ry(θ)Rz(φ) + CNOT layer] [variational].</p>
<p>Parameters per variational layer: 3 Ry + 3 Rz = 6 parameters per layer.</p>
<p>Total trainable parameters: 2 layers × 6 = 12 parameters.</p>
<p>Output: f(x) = ⟨ψ(x,θ)|Z₀|ψ(x,θ)⟩ (measuring Z on qubit 0).</p>
<p>Accessible Fourier frequencies (angle encoding, 1 repetition): ω ∈ {−1, 0, +1}³ for 3 qubits.</p>
<p>Total frequency combinations: 3³ = 27 frequencies (including multi-qubit correlations).</p>
<p>Example terms: cos(x₀), sin(x₁), cos(x₀+x₁), cos(x₀−x₁−x₂), etc.</p>
<p>For r=2 encoding repetitions: frequencies double to ω ∈ {−2,...,+2}³ = 125 combinations.</p>
<p>Maximum function complexity: a trigonometric polynomial in 3 variables with 27 (or 125) Fourier modes.</p>
</div>

## B. Unsolved Problems

*Solve each problem independently. Answers are in brackets for self-checking.*

**1.** Compute the quantum kernel K(x,x′) for a 1-qubit circuit with Ry(x) encoding: K(x,x′) = |⟨ψ(x)|ψ(x′)⟩|² where |ψ(x)⟩=Ry(x)|0⟩. Express K in terms of x and x′ using trigonometric identities. *[|⟨ψ(x)|ψ(x')⟩|² = |cos(x/2)cos(x'/2)+sin(x/2)sin(x'/2)|² = cos²((x-x')/2). This is a cosine kernel! K(x,x') = cos²((x-x')/2) = (1+cos(x-x'))/2. Equivalent to a classical shift-invariant kernel with bandwidth determined by encoding scale.]*

**2.** For a 2-qubit QNN with output f(x) = ⟨ψ(x,θ)|ZI + IZ|ψ(x,θ)⟩ and angle encoding Ry(x₀)⊗Ry(x₁), expand f in terms of single-variable trigonometric functions of x₀ and x₁. *[⟨ZI⟩+⟨IZ⟩ = ⟨Z₀⟩+⟨Z₁⟩ for product state. ⟨Z₀⟩ = cos(x₀+θ₀), ⟨Z₁⟩ = cos(x₁+θ₁) (after Ry(θ₀)⊗Ry(θ₁) variational layer). f(x) = cos(x₀+θ₀)+cos(x₁+θ₁) — a sum of cosines, no cross-terms (because no entanglement in the example). For cross-terms, CNOT entanglers are needed.]*

**3.** Show that the parameter-shift rule gives an exact gradient for E(θ) = A + B cos(θ) + C sin(θ). Apply the formula with s=π/2 and verify it returns B(-sin(θ)) + C cos(θ). *[[E(θ+π/2)-E(θ-π/2)]/2 = [A+B cos(θ+π/2)+C sin(θ+π/2) - A-B cos(θ-π/2)-C sin(θ-π/2)]/2. cos(θ+π/2)=-sin(θ), cos(θ-π/2)=sin(θ), sin(θ+π/2)=cos(θ), sin(θ-π/2)=-cos(θ). = [B(-sin θ-sin θ)+C(cos θ+cos θ)]/2 = [-2B sin θ + 2C cos θ]/2 = -B sin θ + C cos θ = dE/dθ. ✓ Exact.]*

**4.** A QSVM is applied to the XOR problem: 4 training points at (±1, ±1) with label = x₀ XOR x₁. Argue why a classical linear SVM fails and why a quantum kernel (ZZFeatureMap, n=2) might succeed. *[Linear SVM fails: XOR is not linearly separable in ℝ². Decision boundary must be non-linear. ZZFeatureMap: K((1,1),(-1,1))=? Cross-term φ₀₁=(π-1)(π-1)≈4.54 for (1,1) vs (π+1)(π-1)≈(4.14)(2.14)≈8.86 for (-1,1). Different ZZ phases map the four XOR points to four distinct regions of the Bloch sphere. A linear separator in the quantum feature space can correctly classify all 4 points.]*

**5.** For a 4-qubit hardware-efficient ansatz (HEA) with L=2 layers (Ry+Rz per qubit + ring CNOT), compute: (a) number of parameters; (b) circuit depth (gate count); (c) number of CNOT gates. *[(a) Parameters: each layer = 4 Ry + 4 Rz = 8 params. 2 layers = 16 params. (b) Gate depth: each layer = 4 Ry + 4 Rz + 4 CNOT (ring: 0→1, 1→2, 2→3, 3→0) = 12 gates. 2 layers = 24 + encoding (4 Ry) = 28 total gates. (c) CNOT count: 4 per layer × 2 layers = 8 CNOTs. Plus encoding layer: 0 CNOTs. Total CNOTs = 8.]*

**6.** Kernel alignment A(K, Y) = ⟨K,Y⟩\_F / (‖K‖\_F‖Y‖\_F) where Y\_{ij} = yᵢyⱼ. For a perfectly separating kernel (K\_{ij}=yᵢyⱼ), compute A(K,Y). *[If K=Y (up to scaling), then ⟨K,Y⟩\_F = ⟨Y,Y⟩\_F = ||Y||²\_F. So A(Y,Y) = ||Y||²\_F / (||Y||\_F·||Y||\_F) = 1. Perfect alignment = 1. A=1 means the kernel perfectly encodes the class structure. A=0 means no alignment. A quantum kernel should have high alignment on the target dataset for good classification performance.]*

**7.** A QNN outputs f(θ) = 0.4 at current parameters. The parameter-shift evaluations give E(θ₀+π/2) = 0.7 and E(θ₀−π/2) = 0.1. Compute the gradient and the Adam update for θ₀ with learning rate η=0.01, m₁=0.9 (first moment), v₁=0.999 (second moment), ε=10^{−8}, assuming initial first and second moments are zero. *[Gradient: g = (0.7-0.1)/2 = 0.3. Adam: m = (1-0.9)×0.3 = 0.03. v = (1-0.999)×0.09 = 9×10⁻⁵. Bias-corrected: m̂ = 0.03/0.1 = 0.3 (after 1 step, β₁^t=0.9). v̂ = 9×10⁻⁵/0.001 = 0.09. Update: θ₀ ← θ₀ - 0.01×0.3/√(0.09+10⁻⁸) ≈ θ₀ - 0.01×0.3/0.3 = θ₀ - 0.01. One Adam step moves θ₀ by exactly -η=-0.01 (normalised gradient).]*

**8.** Discuss whether increasing the ZZFeatureMap repetition depth r from 1 to 3 makes the quantum kernel harder to evaluate classically. What determines the classical hardness? *[r=1: ZZFeatureMap output state |ψ(x)⟩ involves O(n²) Pauli rotations. Inner product ⟨ψ(x)|ψ(x')⟩ can be computed classically in O(2^n) time (exponential in n). For n=10, 2¹⁰=1024 amplitudes — tractable classically. Classical hardness requires: (1) depth > O(log n) (so classical simulation requires exponential time), and (2) the circuit must be sufficiently random/entangling. Increasing r increases depth and entanglement, potentially crossing the classical simulation threshold. For n≤20 and r≤5: likely still classically simulable (tensor network methods). For n=50, r=10: likely classically hard.]*

**9.** A QNN is trained on the MNIST dataset (28×28=784 features) using angle encoding on n=4 qubits (encoding only 4 principal components from PCA). Estimate the information loss from the dimensionality reduction. *[PCA on MNIST: the first 4 principal components typically capture ~40-50% of total variance. Remaining: 50-60% of variance is discarded. Information loss metric: 1 - (explained variance ratio from first 4 PCs) ≈ 50-60%. Practical implication: the QNN works in a heavily compressed feature space. For competitive MNIST accuracy (>90%), typically 32-64 PCA components are needed (capturing ~90% variance). With 4 qubits, the QNN fundamentally works with coarse features — achieves ~85-90% accuracy at best for simple digit pairs, not competitive with full classical CNN (~99%).]*

**10.** Explain why the QNN function class f\_θ(x) = Σ\_ω c\_ω e^{iω·x} is equivalent to a classical Fourier neural network. What advantage, if any, does the quantum implementation offer? *[Equivalence: a Fourier neural network (classical) represents f(x)=Σ\_ω c\_ω e^{iω·x} with adjustable frequencies ω and amplitudes c\_ω. The QNN generates the same class of functions with fixed frequencies (determined by encoding eigenvalues) and learnable amplitudes c\_ω(θ). Classical equivalent: just train a classical Fourier model with the same set of frequencies. Quantum advantage (if any): (1) the quantum circuit implements the frequency superposition more efficiently for specific structured inputs (e.g., quantum data); (2) higher-order correlations are encoded naturally by entanglement. But for classical data with explicit feature vectors: no provable quantum advantage over classical Fourier methods for this architecture.]*

## C. Multiple Choice Questions

*Note: Answers are given at the end of this section.*

**Q1.** The quantum fidelity kernel K(x,x′) = |⟨ψ(x)|ψ(x′)⟩|² has the property K(x,x) =

(a) 0  (orthogonal states)

(b) 1  (unit self-similarity)

(c) 1/2ⁿ for n qubits

(d) Variable depending on the encoding circuit

**Q2.** The ZZFeatureMap encodes cross-feature correlations via:

(a) Classical XOR of feature bits

(b) ZZ Pauli rotations with angle φ\_{ij} = (π−xᵢ)(π−xⱼ)

(c) A CNOT gate on each feature pair

(d) Tensor product Ry(xᵢ)⊗Ry(xⱼ)

**Q3.** In a Quantum SVM, the kernel matrix K\_{ij} is evaluated by:

(a) Classically computing the inner product in the original feature space

(b) Running a quantum circuit that measures |⟨0ⁿ|Uφ†(xⱼ)Uφ(xᵢ)|0ⁿ⟩|²

(c) Measuring the ZZ correlation between qubits i and j

(d) Sampling from the output distribution of the variational circuit

**Q4.** The QNN output f\_θ(x) = ⟨ψ(x,θ)|H|ψ(x,θ)⟩ can be expressed as:

(a) A polynomial in x of degree 2ⁿ

(b) A trigonometric polynomial (finite Fourier series) in x

(c) An arbitrary continuous function of x

(d) A linear function of the encoding parameters

**Q5.** The parameter-shift rule computes ∂E/∂θ\_k exactly using:

(a) Finite differences with step size h = 10^{−8}

(b) Two circuit evaluations at θ\_k ± π/2 with formula [E(+) − E(−)]/2

(c) The backpropagation algorithm applied to the quantum circuit

(d) A single circuit evaluation at the current parameters plus Jacobian

**Q6.** Barren plateaus in QNN training are characterised by:

(a) Very high loss values that do not decrease

(b) Gradient variance ∝ 2^{−n} vanishing exponentially with system size n

(c) Overfitting on training data

(d) Measurement outcomes that are always 0 or 1

**Q7.** Kernel alignment A(K,Y) = 1 implies:

(a) The kernel perfectly separates all classes

(b) K = λ Y for some scalar λ (the kernel perfectly encodes class structure)

(c) All kernel values are equal to 1

(d) The quantum circuit has maximum entanglement

**Q8.** Quantum kernel training (QKT) differs from standard QSVM in that:

(a) QKT trains a different type of SVM

(b) QKT optimises parameters in the feature map U\_φ(x,θ) to maximise kernel alignment

(c) QKT uses a classical kernel instead of a quantum one

(d) QKT requires fault-tolerant hardware

**Q9.** For angle encoding, a QNN with r encoding repetitions can access Fourier frequencies:

(a) Only the fundamental frequency ω=±1 regardless of r

(b) Frequencies ω ∈ {−r, ..., +r} (integer multiples up to r)

(c) Continuous frequencies in the range [−r, r]

(d) Only even frequencies 0, ±2, ±4, ...

**Q10.** The Meyer-Wallach entanglement measure Q = 0 means:

(a) The circuit produces maximally entangled states

(b) The circuit outputs product states with no entanglement

(c) The kernel matrix has zero off-diagonal entries

(d) The QNN cannot learn any non-linear function

**Q11.** SPSA differs from the full parameter-shift gradient in that:

(a) SPSA gives exact gradients with fewer evaluations

(b) SPSA estimates the full gradient with only 2 circuit evaluations using a random perturbation direction, at the cost of noisy estimates

(c) SPSA computes second-order derivatives

(d) SPSA only works for classical neural networks

**Q12.** The quantum feature space of a n-qubit ZZFeatureMap has dimension:

(a) n  (same as number of features)

(b) n²  (quadratic in features)

(c) 2ⁿ  (exponential in qubits)

(d) n! (factorial — one dimension per permutation of features)

**Q13.** Expressibility ε\_expr is defined as:

(a) The number of trainable parameters in the QNN

(b) The KL divergence between the circuit’s state distribution and the Haar-random distribution

(c) The maximum fidelity achievable between any two states

(d) The circuit’s gate depth

**Q14.** For a Pauli gate e^{-iθP/2} with P²=I, the energy E(θ) = ⟨...⟩ is always:

(a) A polynomial of degree 2 in θ

(b) Exactly sinusoidal: A+B cos(θ)+C sin(θ)

(c) A Gaussian function of θ

(d) Monotonically decreasing in θ

**Q15.** The key claim of Schuld & Killoran (2019) is that quantum models are:

(a) Always superior to classical ML for structured data

(b) Fundamentally kernel methods, with kernel K(x,x′) = |⟨ψ(x)|ψ(x′)⟩|²

(c) Universal function approximators only for quantum data

(d) Equivalent to deep Gaussian process models

<div class="box box-generic">
<p class="box-title"><strong>MCQ ANSWERS</strong></p>
<p>Q1: (b) K(x,x)=1 — self-similarity equals 1 for any normalised state; the fidelity of a state with itself is always 1</p>
<p>Q2: (b) ZZ rotations with angle (π−xᵢ)(π−xⱼ) — this encodes pairwise products of features into quantum phases</p>
<p>Q3: (b) Quantum circuit measuring |⟨0ⁿ|U†(xⱼ)U(xᵢ)|0ⁿ⟩|² — the fidelity kernel evaluation via the swap test or direct circuit inversion</p>
<p>Q4: (b) Trigonometric polynomial — QNNs represent finite Fourier series; the frequencies are fixed by the encoding gate eigenvalues</p>
<p>Q5: (b) Two evaluations at θ±π/2 — the parameter-shift rule is exact with no approximation error, unlike finite differences</p>
<p>Q6: (b) Exponential vanishing of gradient variance ∝ 2^{−n} — the defining property of barren plateaus in deep random circuits</p>
<p>Q7: (b) K = λY (kernel perfectly encodes class structure) — perfect alignment means the kernel Gram matrix is proportional to the ideal target kernel</p>
<p>Q8: (b) QKT optimises the feature map parameters to maximise kernel alignment — making the kernel trainable and dataset-adaptive</p>
<p>Q9: (b) Frequencies ω ∈ {−r,...,+r} — each encoding repetition adds one more harmonic level to the accessible frequency spectrum</p>
<p>Q10: (b) Q=0 means product states (no entanglement) — Meyer-Wallach Q=0 for fully separable states, Q=1 for maximally entangled</p>
<p>Q11: (b) SPSA estimates full gradient with 2 evaluations using random perturbation — cheap but noisy; trade-off between cost and accuracy</p>
<p>Q12: (c) 2ⁿ — the Hilbert space of n qubits has dimension 2ⁿ; the feature map lifts data into this exponentially large space</p>
<p>Q13: (b) KL divergence from Haar-random — expressibility measures how close the circuit’s state coverage is to the full unitary group</p>
<p>Q14: (b) Exactly sinusoidal A+B cos(θ)+C sin(θ) — since Pauli generators have only ±1/2 eigenvalues, E(θ) is a single-frequency sinusoid</p>
<p>Q15: (b) Quantum models are kernel methods — the Schuld-Killoran insight that unifies QML with classical kernel theory</p>
</div>

## D. Theory Questions

**1.**  Derive from first principles that K(x,x′) = |⟨ψ(x)|ψ(x′)⟩|² is a valid Mercer kernel (positive semi-definite and symmetric). Show explicitly where the PSD proof fails if we use the signed inner product Re(⟨ψ(x)|ψ(x′)⟩) instead of the fidelity. Which formulation is preferable for ML applications, and why?

**2.**  Prove the parameter-shift rule ∂E/∂θ = [E(θ+π/2)−E(θ−π/2)]/2 for a gate G(θ) = e^{-iθP/2} where P²=I. (a) Show that E(θ) = A+B cos(θ)+C sin(θ) using the spectral decomposition of P. (b) Apply the rule to verify it returns the correct derivative. (c) Extend the rule to a generator with eigenvalues {r, -r} (not necessarily r=1/2).

**3.**  Explain the Schuld et al. (2021) Fourier series representation theorem for QNNs: (a) What is the mathematical definition of the accessible frequency spectrum? (b) How does the number of frequencies grow with encoding repetitions r? (c) Give a concrete example showing that a product-state QNN (no entangling gates) can only access single-qubit frequencies, while an entangling circuit can access multi-qubit Fourier modes.

**4.**  Describe barren plateaus rigorously: (a) State the McClean et al. (2018) theorem precisely. (b) Prove that for a random 2-design circuit on n qubits with a global cost function H, the gradient variance Var[∂⟨H⟩/∂θ\_k] = O(4^{−n}). (c) Explain why local cost functions (depending on at most k << n qubits) avoid this exponential decay. (d) Describe the layer-by-layer LBFGS initialisation strategy for avoiding barren plateaus.

**5.**  Derive and explain kernel alignment: (a) Define the Frobenius inner product ⟨K,Y⟩\_F. (b) Show that A(K,Y) = 1 implies K is proportional to the ideal kernel Y = yyᵀ. (c) Describe how quantum kernel training (QKT) optimises A(K(θ),Y) with respect to the feature map parameters θ. (d) What gradient does A(K(θ),Y) induce on θ, and how is it computed on quantum hardware?

**6.**  Compare classical SVM with kernel trick vs QSVM: (a) Describe the classical SVM dual problem and the role of support vectors. (b) How does the QSVM substitute a quantum kernel for the classical one? (c) What is the computational complexity of building the Gram matrix in both cases? (d) In what regime might the quantum kernel provide a genuine advantage for classification accuracy?

**7.**  Explain expressibility and entanglement capability as circuit design metrics: (a) Define both quantities precisely. (b) Describe the procedure for estimating expressibility via fidelity sampling. (c) Show that a depth-1 circuit of Ry gates only (no entangling) has low entanglement capability. (d) Discuss the expressibility-barren-plateau trade-off: why is maximally expressive not necessarily the best architecture for QML?

**8.**  Describe the natural gradient method for QNN training: (a) Define the quantum Fisher information matrix F\_{jk} = Re(⟨∂\_jψ|∂\_kψ⟩) − Re(⟨∂\_jψ|ψ⟩⟨ψ|∂\_kψ⟩). (b) Show that the natural gradient update θ̇ = ηF^{−1}∂E/∂θ is invariant to reparameterisation of the circuit. (c) Describe how to estimate F on quantum hardware using the parameter-shift rule. (d) When is the natural gradient method preferable to standard gradient descent?

**9.**  Explain the relationship between quantum kernels and classical kernels in the dequantisation context: (a) What conditions on the quantum kernel K(x,x′) allow a classical algorithm to efficiently approximate it? (b) Describe the SQ (sample-and-query) model and how it relates to quantum kernel evaluation. (c) State a specific quantum kernel that is provably hard to compute classically (Liu et al. 2021). (d) What does this mean for practical QSVM applications on real-world datasets?

**10.**  Describe the connection between QNNs and classical neural networks: (a) Show that a deep classical network can simulate any QNN output by computing quantum amplitudes. (b) Conversely, what can QNNs express that classical finite Fourier networks cannot (if anything)? (c) Explain why quantum circuits with polynomial depth cannot escape Solovay-Kitaev approximation limits. (d) Identify the regime of data (type, dimension, structure) where QNNs are most likely to outperform classical neural networks.

## E. Programming Assignments

**[PA-9.1]  QSVM on Synthetic and Real Datasets:**

Implement a complete QSVM pipeline using PennyLane and scikit-learn. Part A (Synthetic): generate a 2D concentric-rings dataset (make\_circles, noise=0.1, 100 points) not linearly separable in ℝ². (a) Train a classical linear SVM and report accuracy. (b) Train a classical RBF-SVM and report accuracy. (c) Implement ZZFeatureMap (n=2 qubits, r=1) and build the quantum Gram matrix. (d) Train a QSVM with the quantum kernel and report accuracy. (e) Plot the three decision boundaries on the same figure. Part B (Real): apply all three SVMs to the breast cancer dataset (sklearn.datasets.load\_breast\_cancer), using PCA to reduce to 2 features. Report train and test accuracies for all three kernels with a 80/20 split. Analyse: for which dataset does the quantum kernel most outperform the classical linear kernel, and why?

**[PA-9.2]  Parameter-Shift Gradient vs Finite Difference: Accuracy and Cost Comparison:**

Implement gradient computation for a 4-parameter QNN (2 qubits, 2 layers of Ry+CNOT) using PennyLane: (a) Implement the parameter-shift rule manually (without using qml.grad). (b) Implement finite-difference gradient with step sizes h ∈ {10⁻¹, 10⁻², 10⁻³, 10⁻⁴, 10⁻⁵}. (c) For each method, compute all 4 partial derivatives at a fixed parameter point θ = (π/4, π/3, π/6, π/2) and compare with the exact analytic gradient (compute it using sympy or automatic differentiation). (d) Plot the absolute error |grad\_computed − grad\_exact| vs h for finite differences; show the parameter-shift result as a horizontal line at machine precision. (e) Count total circuit evaluations for each method and compute the accuracy-per-evaluation trade-off. (f) Add simulated shot noise (shots=100) and recompute: show which method is more robust to noise.

**[PA-9.3]  QNN Binary Classification: Training and Decision Boundary:**

Build and train a complete 4-qubit QNN binary classifier using PennyLane. Dataset: generate 80 training points and 20 test points from two interleaved spirals in ℝ² (a challenging non-linear classification problem). Architecture: angle encoding (Ry(x₀)Ry(x₁) on qubits 0–1, pad with zeros for qubits 2–3) + 3 HEA layers (Ry+Rz per qubit + ring CNOT) + measurement ⟨Z₀⟩. (a) Train using the Adam optimiser for 100 epochs with parameter-shift gradients; plot training loss and accuracy per epoch. (b) Plot the final decision boundary by evaluating f\_θ(x) on a 50×50 grid. (c) Run 5 independent training runs from random initialisations and report the mean and standard deviation of test accuracy. (d) Repeat training with L=1 and L=5 layers; show how expressibility and accuracy change. (e) Compare with a classical logistic regression and RBF-SVM on the same data.

## F. Project Suggestions

**Project 9.A — Quantum Kernel Expressivity on Real-World Classification Tasks:**

Conduct a systematic comparative study of quantum kernels versus classical kernels on five benchmark datasets: (1) Iris (4 features, 3 classes), (2) Wine (13 features, 3 classes), (3) Breast Cancer (30 features, 2 classes), (4) MNIST (784 features, digits 3 vs 8), (5) a synthetic dataset designed to favour quantum kernels (XOR-like structure in high dimension). For each dataset: (a) Apply PCA to reduce to 2, 4, and 8 features. (b) Train QSVM with ZZFeatureMap (n=2,4,8 qubits, r=1,2) and classical SVM with linear, polynomial, and RBF kernels. (c) Plot kernel alignment A(K,Y) as a function of circuit depth for all configurations. (d) Conduct a permutation test to assess statistical significance of accuracy differences. (e) Measure actual runtime on PennyLane simulator and estimate IBM Quantum hardware runtime. Write a 15-page report including: theoretical analysis of when quantum kernels should outperform classical, empirical results, and an honest assessment of practical quantum advantage.

**Project 9.B — Barren Plateau Mitigation: Comparative Study of Initialisation Strategies:**

Implement and compare four barren plateau mitigation strategies for QNNs of increasing size (n = 2, 4, 6, 8, 10 qubits): (1) Random initialisation (baseline). (2) Identity-block initialisation (all parameters near zero). (3) Layer-by-layer greedy pre-training (train one layer at a time). (4) Local cost function (use ⟨Z₀⟩ instead of ⟨ΣᵢZᵢ⟩). For each strategy and system size: (a) Sample 1000 random parameter sets and estimate the gradient variance Var[∂E/∂θ\_k] for each qubit-count. (b) Plot gradient variance vs n on a log-log scale; fit to 2^{−αn} and extract α for each strategy. (c) Train to convergence (or 500 epochs) on a classification task; record convergence speed and final accuracy. (d) Verify theoretically which strategies are expected to avoid exponential variance decay. Write a 15-page report.

**Project 9.C — Hardware QSVM: Running on IBM Quantum and Noise Analysis:**

Deploy a complete QSVM pipeline on IBM Quantum hardware and analyse noise effects. Dataset: Iris dataset (2 features from PCA, 30 training points for each of 2 classes). (a) Implement ZZFeatureMap (n=2 qubits, r=1,2) using Qiskit. (b) Compute the 30×30 Gram matrix on (i) statevector simulator (noiseless), (ii) AerSimulator with ibm\_brisbane noise model, (iii) real IBM Quantum hardware (ibm\_brisbane, 1024 shots per kernel entry). (c) Compute the maximum entry-wise difference ‖K\_noisy − K\_ideal‖\_∞ for each noise level. (d) Train QSVM on K\_noisy and test on the noiseless test kernel; plot accuracy vs noise level. (e) Apply quantum error mitigation (ZNE: Zero-Noise Extrapolation or readout error mitigation) and measure the accuracy improvement. Write a 15-page report including hardware results, noise analysis, and error mitigation comparison.
