# CHAPTER 10

# Quantum Transfer Learning, Data Encoding Strategies & Dequantisation

*Hybrid quantum-classical models, encoding depth analysis, PennyLane-PyTorch integration, and when classical ML matches quantum ML*

<div class="box box-anecdote">
<p class="box-title"><strong>📓  Data Encoding: The Unsung Bottleneck of Quantum Machine Learning</strong></p>
<p>Every quantum machine learning algorithm begins with the same fundamental challenge: translating classical data — stored as floating-point numbers in a classical computer — into a quantum state that a quantum processor can act on. This encoding step is deceptively important. The choice of encoding determines what features the quantum model can represent, how many qubits and gates are required, and whether any quantum advantage can survive the overhead of the encoding itself.</p>
<p>Amplitude encoding packs the most data into the fewest qubits (N data points into log₂N qubits) but requires an exponentially deep state preparation circuit. Angle encoding is shallow but uses one qubit per feature. Basis encoding is trivial for integer data but loses all continuous information. Each strategy creates a fundamentally different relationship between the data dimension and the quantum resource cost.</p>
<p>Perez-Salinas et al. (2020) showed that a single qubit — with repeated angle re-uploading — can approximate any function to arbitrary precision. This data re-uploading protocol challenges the assumption that more qubits are always needed for more complex models, and opens a path to practical NISQ-era QML even on minimal hardware.</p>
</div>

## 10.1 Data Encoding Strategies: Angle, Amplitude, Basis, and Re-uploading

The first step in any quantum ML pipeline is encoding classical data x ∈ ℝᴺ into a quantum state |ψ(x)⟩. There is no unique or universal encoding strategy — the choice involves explicit trade-offs between quantum resources (qubits, gates), information capacity, and trainability. This section analyses the three primary strategies and the powerful re-uploading extension.

### 10.1.1 Angle Encoding

Angle encoding maps each feature xᵢ to the rotation angle of a single-qubit gate. The most common choice is the Ry rotation: |ψ(x)⟩ = ⊗ᵢ Ry(xᵢ)|0⟩ = ⊗ᵢ [cos(xᵢ/2)|0⟩ + sin(xᵢ/2)|1⟩]. Variants use Rx, Rz, or multiple gates per qubit.

<div class="box box-generic">
<p class="box-title"><strong>Angle Encoding</strong></p>
<p><strong><em>|ψ(x)⟩ = ⊗_{i=0}^{n-1} Ry(xᵢ)|0⟩</em></strong></p>
<p><em>Resources: n qubits for n features. Gates: n single-qubit rotations (O(n) depth). No entanglement (product state). Feature space: individual Bloch sphere positions, no cross-correlations without additional entangling gates.</em></p>
</div>

Advantages: simple, shallow circuit (O(1) depth per feature), no approximation error, and directly compatible with hardware native gates. Disadvantage: one qubit per feature, so n=784 features (MNIST) would require 784 qubits. Requires feature preprocessing (normalisation to [0,π] or [0,2π]).

### 10.1.2 Amplitude Encoding

Amplitude encoding packs a normalised classical vector x ∈ ℝᴺ (with ||x||=1) into the amplitudes of an n-qubit state, where N = 2ⁿ:

<div class="box box-generic">
<p class="box-title"><strong>Amplitude Encoding</strong></p>
<p><strong><em>|ψ(x)⟩ = Σ_{i=0}^{N-1} xᵢ |i⟩   where   N = 2ⁿ,  ||x||=1</em></strong></p>
<p><em>Resources: n = log₂N qubits for N features (exponential compression!). Gates: O(N) = O(2ⁿ) gates for exact preparation (deep circuit). Feature space: the full 2ⁿ-dimensional Hilbert space is used, enabling rich correlations.</em></p>
</div>

The critical caveat: preparing an arbitrary amplitude-encoded state requires O(2ⁿ) gates — exponential in the number of qubits, defeating the qubit compression. Approximate methods (quantum RAM, recursive state preparation) can reduce this to O(n²) gates but require ancilla qubits and introduce approximation error. For random vectors, classical precomputation of the state preparation circuit is itself O(2ⁿ).

<div class="box box-warning">
<p class="box-title"><strong>⚠  Warning: Amplitude Encoding Does Not Automatically Give Quantum Speedup</strong></p>
<p>A common misconception: “Amplitude encoding puts 2ⁿ values into n qubits, giving exponential compression.” This is true for storage, but it does not give computational speedup by itself.</p>
<p>The problem: to extract all 2ⁿ amplitudes, O(2ⁿ) measurement outcomes are needed. This completely erases the qubit advantage.</p>
<p>The only way amplitude encoding helps is if: (1) the algorithm only needs O(1) properties of the amplitude vector (not all amplitudes), and (2) state preparation is fast (e.g., via QRAM or the data is already in a quantum device). Both conditions must hold simultaneously — which is the core of the HHL and quantum recommendation systems debates.</p>
<p>For NISQ QML on classical data: amplitude encoding is typically NOT recommended because the state preparation circuit is deeper than the variational circuit, and the exponential overhead negates any potential quantum speedup.</p>
</div>

### 10.1.3 Basis Encoding

Basis encoding maps a classical binary string (or integer) to a computational basis state. For an integer x with n-bit binary representation b\_{n-1}...b₀:

<div class="box box-generic">
<p class="box-title"><strong>Basis Encoding</strong></p>
<p><strong><em>|ψ(x)⟩ = |b_{n-1}...b₁b₀⟩   where   x = Σᵢ bᵢ 2ᵢ</em></strong></p>
<p><em>Resources: n qubits for an n-bit integer. Gates: at most n X gates (flip |0⟩→|1⟩ for bits equal to 1). Application: naturally suited to integer features, search problems, Grover oracles. Limitation: no continuous information, no superposition per data point.</em></p>
</div>

Basis encoding is most natural when: (1) the data is inherently discrete (binary images, categorical features, graph adjacency matrices); (2) the quantum algorithm works in the computational basis (Grover search, quantum walks); (3) a superposition of multiple data points is needed simultaneously: |ψ⟩ = (1/√m)Σᵢ|xᵢ⟩|yᵢ⟩ (quantum database superposition).

### 10.1.4 Data Re-uploading: One Qubit Is Enough

Perez-Salinas et al. (2020) introduced data re-uploading, a powerful technique that breaks the one-feature-one-qubit limitation of angle encoding. The idea: repeatedly apply the same encoding circuit interleaved with variational layers, re-encoding the classical data multiple times throughout the circuit.

<div class="box box-generic">
<p class="box-title"><strong>Data Re-uploading Protocol</strong></p>
<p><strong><em>|ψ(x,θ)⟩ = W_L(θ_L) U_φ(x) W_{L-1}(θ_{L-1}) ··· W_1(θ_1) U_φ(x) |0⟩</em></strong></p>
<p><em>U_φ(x): encoding unitary, applied L times (interleaved with variational layers W_l). Each re-upload adds new Fourier frequencies. Result: a single qubit with L re-uploads can represent any function f: ℝⁿ → [-1,1] to arbitrary precision.</em></p>
</div>

<div class="box box-example">
<p class="box-title"><strong>Example 10.1:</strong> Data Re-uploading vs Standard Angle Encoding: Frequency Comparison</p>
<p><strong>Problem:</strong> Compare the accessible Fourier frequencies for (a) standard angle encoding (1 Ry, no re-uploading) and (b) data re-uploading with L=3 repetitions, both on 1 qubit.</p>
<p><strong>Solution:</strong></p>
<p>(a) Standard angle encoding: |ψ(x)⟩ = Ry(x)|0⟩.</p>
<p>Output E(x) = ⟨Z⟩: frequencies accessible = {−1, 0, +1} (one fundamental frequency).</p>
<p>Function represented: A + B cos(x) + C sin(x) — a degree-1 trigonometric polynomial.</p>
<p>(b) Re-uploading with L=3: W₃Ry(x)W₂Ry(x)W₁Ry(x)|0⟩.</p>
<p>Each Ry(x) adds ±1 to the frequency spectrum. After 3 uploads: ω ∈ {−3, −2, −1, 0, +1, +2, +3}.</p>
<p>Function: A + B₁cos(x)+C₁sin(x) + B₂cos(2x)+C₂sin(2x) + B₃cos(3x)+C₃sin(3x).</p>
<p>This is a degree-3 trigonometric polynomial — 3× more expressive than standard encoding!</p>
<p>For multi-dimensional x ∈ ℝⁿ: each re-upload on a single qubit encodes all n features.</p>
<p>Conclusion: re-uploading grows the frequency set to {−1,...,−1,...,+L,...,+1} with L re-uploads.</p>
</div>

<figure class="book-figure">
<img src="content/images/image32.png" alt="">
<figcaption></figcaption>
</figure>

| Strategy | Qubits needed | Gates (depth) | Feature space | Recommended for |
|---|---|---|---|---|
| Angle | O(n) = n features | O(n) shallow | n-sphere (product) | NISQ, small n |
| Amplitude | O(log N) | O(2ⁿ) deep | Full Hilbert ℂ^{2ⁿ} | Large N, fault-tolerant |
| Basis | O(n) bits | O(n) trivial | Computational basis | Discrete/binary data |
| Re-uploading | O(1) to O(n) | O(L·n) moderate | Rich polynomial | Universal approx, NISQ |

## 10.2 Quantum Transfer Learning

Transfer learning is one of the most powerful techniques in classical deep learning: take a large neural network pre-trained on a massive dataset (e.g., ResNet-50 on ImageNet’s 1.2 million images), freeze its weights, and attach a small trainable head for a new task. The pre-trained network serves as a feature extractor; the small head learns the task-specific classification.

Quantum transfer learning (Mari et al. 2020) applies this same idea with a quantum head: replace the small classical head with a parameterised quantum circuit (PQC). The classical backbone extracts rich feature representations; the quantum circuit classifies in a quantum Hilbert space. This architecture is particularly promising for NISQ devices because the circuit depth remains shallow (matching current hardware constraints) while leveraging the power of large classical models.

### 10.2.1 Architecture: Classical Backbone + Quantum Head

The standard quantum transfer learning architecture has three stages:

**Stage 1 — Classical Pre-trained Network (frozen):** A large classical DNN (ResNet, EfficientNet, BERT) with frozen weights. Processes the input x (image, text) and outputs a compact feature vector z ∈ ℝ^d (typically d = 512 or 2048 from the penultimate layer).

**Stage 2 — Dimensional Reduction + Encoding:** A dimensionality reduction step (PCA, trainable linear layer) reduces z from d dimensions to n ≤ 20 features. These n features are then angle-encoded into an n-qubit quantum state via Ry rotations.

**Stage 3 — Quantum Variational Head:** A parameterised quantum circuit W(θ) on n qubits (typically 2–8) processes the encoded state and outputs an expectation value ⟨Z⟩ (or a vector) for classification. Only W(θ) is trained.

<figure class="book-figure">
<img src="content/images/image33.png" alt="">
<figcaption></figcaption>
</figure>

<div class="box box-generic">
<p class="box-title"><strong>Quantum Transfer Learning: Forward Pass</strong></p>
<p><strong><em>ŷ = f_θ(x) = sign(⟨ψ(W_freeze(x))|Z|ψ(W_freeze(x))⟩)</em></strong></p>
<p><em>W_freeze: classical network (frozen). ψ: angle encoding of the d→n projected feature vector z. Only θ in the quantum circuit is updated during training. Backpropagation flows through the quantum circuit via parameter-shift, then stops at the frozen backbone.</em></p>
</div>

<div class="box box-example">
<p class="box-title"><strong>Example 10.2:</strong> Quantum Transfer Learning for Ant vs Bee Classification (Mari et al. 2020)</p>
<p><strong>Problem:</strong> Describe the quantum transfer learning setup for the Hymenoptera dataset (ants vs bees, 245 training images).</p>
<p><strong>Solution:</strong></p>
<p>Classical backbone: ResNet-18 pre-trained on ImageNet (11 million parameters, frozen).</p>
<p>Input: 224×224 RGB images (150,528 features). Output of ResNet penultimate layer: z ∈ ℝ^{512}.</p>
<p>Dimensionality reduction: linear layer 512→4 (trainable or frozen).</p>
<p>Quantum encoding: angle encoding on n=4 qubits: |ψ(z)⟩ = Ry(z₀)⊗Ry(z₁)⊗Ry(z₂)⊗Ry(z₃)|0⟩.</p>
<p>Quantum head: 2 variational layers of Ry+Rz+CNOT (ring connectivity). 16 trainable parameters.</p>
<p>Output: ⟨Z₀⟩ ∈ [-1,+1]. Classification: ants (⟨Z₀⟩ &gt; 0) vs bees (⟨Z₀⟩ &lt; 0).</p>
<p>Training: Adam optimiser, 30 epochs, parameter-shift gradients.</p>
<p>Results (Mari et al.): Quantum head ≈ 95% test accuracy vs classical fully-connected head ≈ 96%.</p>
<p>Key insight: only 16 quantum parameters needed to match 512×2+2 = 1026 classical head parameters.</p>
</div>

### 10.2.2 Theoretical Motivation: Why a Quantum Head?

The quantum head operates in a 2ⁿ-dimensional Hilbert space. For n=4 qubits, this is 16 dimensions — comparable to a 4-node classical layer but with a richer geometry defined by the SU(2ⁿ) unitary group rather than the flat ℝ^{16} space of classical layers. The quantum circuit’s entangling structure creates correlations between features that cannot be expressed by a classical linear layer of the same size.

Moreover, the parameter-shift rule provides exact gradients even for shallow quantum circuits on noisy hardware, whereas classical networks require careful gradient clipping and normalisation to train. For small-data regimes (few training examples), the heavily parameterised classical head overfits; the quantum head’s implicit regularisation (constrained to the manifold of unitary evolutions) may help.

<div class="box box-real-world">
<p class="box-title"><strong>🌐  Real World: Quantum Transfer Learning Applications</strong></p>
<p>Medical imaging: Researchers at CERN and collaborating institutions have applied quantum transfer learning to classify high-energy physics events (jets) and to medical image classification (histopathology, retinal scans). Small quantum heads on 4–8 qubits achieve within 1–2% of classical fully-connected heads on balanced binary classification tasks.</p>
<p>Drug discovery: Molecular property prediction using graph neural network backbones (pre-trained on large molecular databases) + quantum variational heads for specific property prediction tasks. The structured quantum head can exploit molecular symmetries through equivariant encoding.</p>
<p>Natural language processing: Sentence classification using BERT embeddings + quantum heads has been explored by IBM Research. The approach is limited by the dimensionality reduction step (BERT’s 768-dimensional output must be aggressively reduced before quantum encoding).</p>
<p>India-specific: IIT Madras has explored quantum transfer learning for satellite image classification (crop type mapping for agricultural monitoring) using 4-qubit PennyLane circuits as heads on ResNet-18 backbones. India’s NQM supports such applied QML research at national remote sensing institutes.</p>
</div>

## 10.3 Dequantisation: When Classical ML Matches Quantum ML

Dequantisation is the process of finding a classical algorithm that achieves the same performance as a proposed quantum algorithm, under comparable conditions. The term was introduced in the quantum ML context by Ewin Tang (2019) who showed that classical algorithms with sample-and-query (SQ) access to data can match the performance of HHL and quantum recommendation systems. Understanding dequantisation is essential for making honest assessments of quantum ML advantage.

### 10.3.1 The Sample-and-Query (SQ) Data Model

The dequantisation framework relies on a specific classical data access model that mirrors quantum state access. In the SQ model, a classical algorithm has two types of access to a data vector v ∈ ℝᴺ:

<div class="box box-generic">
<p class="box-title"><strong>Sample-and-Query (SQ) Access Model</strong></p>
<p><strong><em>(1) Sample: draw index i with probability |vᵢ|²/||v||²   (analogous to quantum measurement)</em></strong></p>
<p><em>(2) Query: read any specific entry vᵢ in O(1) time   (analogous to quantum oracle access). SQ access is achievable classically when v is stored in a specific balanced binary tree data structure (KD-tree or similar) that enables both operations in O(polylog N) time.</em></p>
</div>

The key insight: quantum state preparation from amplitude encoding gives a quantum state |ψ⟩ = Σᵢ vᵢ|i⟩. Measuring this state produces sample i with probability |vᵢ|²/||v||². This is precisely a sample from the SQ model! So quantum amplitude encoding is essentially SQ access — but SQ access is achievable classically via appropriate data structures.

### 10.3.2 Tang’s Classical Algorithms for Quantum ML Tasks

Tang (2019) showed that the quantum recommendation systems algorithm (Kerenidis & Prakash 2017, claiming exponential quantum speedup) can be matched classically using SQ access. The key classical algorithm is Monte Carlo matrix multiplication using importance sampling:

<div class="box box-generic">
<p class="box-title"><strong>Classical Dequantisation of Quantum Recommendation Systems</strong></p>
<p><strong><em>Classical runtime with SQ access: O(poly(k, 1/ε) · polylog(mn)) for k-dimensional approximation</em></strong></p>
<p><em>Quantum algorithm (KP17): O(poly(k, 1/ε) · polylog(mn)). Same asymptotic complexity! Tang’s result: both are polynomial in the problem parameters for SQ-accessible data. The exponential quantum speedup over naive classical (O(mn)) is preserved; the speedup over SQ-enabled classical is removed.</em></p>
</div>

The same dequantisation approach applies to several other claimed quantum ML speedups. The pattern: whenever the proposed quantum algorithm uses amplitude-encoded data and outputs a quantum state that is then sampled, there is a classical SQ algorithm with the same asymptotic complexity.

<div class="box box-example">
<p class="box-title"><strong>Example 10.3:</strong> Which Quantum ML Algorithms Are Dequantised?</p>
<p><strong>Problem:</strong> Classify the following quantum ML algorithms as (D) dequantised or (ND) not dequantised, with justification.</p>
<p><strong>Solution:</strong></p>
<p>(a) HHL for linear systems: (D) Dequantised by Tang et al. (2019) for the SQ model — classical sampling algorithms match the quantum performance when input has SQ access and only O(1) output measurements needed.</p>
<p>(b) Quantum recommendation systems (KP17): (D) Dequantised by Tang (2019) — the original dequantisation result. Classical SQ achieves same O(polylog) complexity.</p>
<p>(c) Shor's factoring algorithm: (ND) NOT dequantised. Exploits multiplicative periodicity detected by QFT; no classical efficient algorithm for factoring is known. No SQ structure applies.</p>
<p>(d) Quantum kernel classification (QSVM): (ND) NOT dequantised. If the quantum kernel K(x,x′) is hard to evaluate classically, no SQ method provides the kernel values. Liu et al. (2021) proved a formal separation for specific quantum kernels.</p>
<p>(e) Quantum PCA (Lloyd et al. 2014): (D) Dequantised by Tang (2019) using the same SQ framework — classical sparse PCA with SQ access achieves the same polylog complexity.</p>
<p>(f) VQE for chemistry: (ND) NOT dequantised. VQE computes expectation values for quantum Hamiltonians — exponentially hard classically for general many-body systems. The input is inherently quantum (the state |ψ(θ)⟩).</p>
<p>Key principle: dequantisation applies to quantum algorithms with classical data encoded via amplitude encoding. Algorithms with inherently quantum inputs or that exploit quantum periodicity (Shor, Grover-like) are not dequantised.</p>
</div>

### 10.3.3 Quantum vs Classical ML: An Honest Comparison Map

The dequantisation results have reshaped the quantum ML landscape. It is now possible to draw a clearer map of where genuine quantum advantages exist, where they are speculative, and where they have been definitively ruled out:

| Algorithm | Claimed quantum advantage | Status (2025) | Condition for advantage |
|---|---|---|---|
| QSVM (hard kernel) | Exp. separation in classification | Proven (Liu et al.) | Kernel must be classically hard |
| HHL / quantum PCA | Exp. speedup for linear algebra | Dequantised (Tang) | SQ access removes advantage |
| Quantum recommendation | Exp. speedup for recomm. systems | Dequantised (Tang) | SQ data structure exists |
| VQE (chemistry) | Poly speedup for ground states | Not dequantised | Quantum input states needed |
| Shor's algorithm | Exp. speedup for factoring | Not dequantised | Periodic structure unique |
| QNN / PQC | Heuristic ML improvement | Task-dependent | No general proof either way |
| Quantum simulation | Exp. speedup for physics | Not dequantised | Quantum Hamiltonians are quantum |

<figure class="book-figure">
<img src="content/images/image34.png" alt="">
<figcaption></figcaption>
</figure>

## 10.4 PennyLane Integration with PyTorch: Hybrid Quantum-Classical Pipelines

PennyLane (Bergholm et al. 2018) is the leading open-source quantum ML framework, designed from the ground up for seamless integration with classical ML libraries. Its central abstraction is the QNode: a quantum circuit that behaves exactly like a classical PyTorch function, participating in automatic differentiation via the parameter-shift rule. This enables truly hybrid models where quantum and classical layers are mixed freely in a single computation graph.

### 10.4.1 The PennyLane QNode

A QNode wraps a quantum circuit function and a device, automatically handling the interface between quantum execution and classical autograd:

<div class="box box-generic">
<p class="box-title"><strong>PennyLane QNode</strong></p>
<p><strong><em>@qml.qnode(dev, interface="torch", diff_method="parameter-shift")</em></strong></p>
<p><em>interface="torch": QNode outputs PyTorch tensors; gradients flow into the PyTorch autograd graph. diff_method: "parameter-shift" (exact, hardware-compatible), "backprop" (fast, simulator-only), "adjoint" (most efficient for simulators). The QNode is a differentiable function ℝᵖ → ℝ from parameters to expectation values.</em></p>
</div>

```python
# Hybrid QNN — PennyLane + PyTorch Integration
# PennyLane + PyTorch: Complete Hybrid QNN Pipeline
import pennylane as qml
import torch
import torch.nn as nn
import numpy as np

# ── 1. Define the quantum device ─────────────────────────────────────
n_qubits = 4
n_layers = 2
dev = qml.device("default.qubit", wires=n_qubits)

# ── 2. Define the quantum circuit as a QNode ──────────────────────────
@qml.qnode(dev, interface="torch", diff_method="parameter-shift")
def quantum_circuit(inputs, weights):
    """Angle-encoded QNN: inputs -> encoding -> variational layers -> Z measurement."""
    # Encoding layer: Ry(xi) on each qubit
    for i in range(n_qubits):
        qml.RY(inputs[i], wires=i)
    # Variational layers: Ry + Rz + CNOT ring
    for layer in range(n_layers):
        for i in range(n_qubits):
            qml.RY(weights[layer, i, 0], wires=i)
            qml.RZ(weights[layer, i, 1], wires=i)
        for i in range(n_qubits):
            qml.CNOT(wires=[i, (i+1) % n_qubits])
    return [qml.expval(qml.PauliZ(i)) for i in range(n_qubits)]

# ── 3. Wrap as a PyTorch nn.Module ──────────────────────────────────
class HybridQNN(nn.Module):
    def __init__(self):
        super().__init__()
        self.classical_in = nn.Linear(8, n_qubits)
        weight_shapes = {"weights": (n_layers, n_qubits, 2)}
        self.qlayer = qml.qnn.TorchLayer(quantum_circuit, weight_shapes)
        self.classical_out = nn.Linear(n_qubits, 2)

    def forward(self, x):
        x = torch.tanh(self.classical_in(x))
        x = self.qlayer(x)
        x = self.classical_out(x)
        return x

# ── 4. Training loop ──────────────────────────────────────────────────
model = HybridQNN()
optimiser = torch.optim.Adam(model.parameters(), lr=0.01)
loss_fn = nn.CrossEntropyLoss()

torch.manual_seed(42)
X_train = torch.randn(100, 8)
y_train = (X_train[:, 0] > 0).long()

for epoch in range(50):
    model.train()
    optimiser.zero_grad()
    outputs = model(X_train)
    loss = loss_fn(outputs, y_train)
    loss.backward()   # parameter-shift gradients flow automatically
    optimiser.step()
    if (epoch + 1) % 10 == 0:
        acc = (outputs.argmax(1) == y_train).float().mean()
        print(f"Epoch {epoch+1:3d} | Loss: {loss:.4f} | Train Acc: {acc:.1%}")
```

### 10.4.2 Automatic Differentiation Through the Quantum Circuit

The magic of PennyLane’s PyTorch interface is that loss.backward() automatically computes gradients through the quantum circuit using the parameter-shift rule. Each QNode parameter θ\_k contributes a gradient ∂L/∂θ\_k = (∂L/∂⟨Z⟩) · (∂⟨Z⟩/∂θ\_k). The first factor is computed by standard PyTorch autograd from the classical layers; the second factor is computed by the parameter-shift rule requiring two additional quantum circuit evaluations. For p quantum parameters and batch size B: total circuit evaluations per training step = B × (1 forward + 2p backward) = B(1+2p).

<figure class="book-figure">
<img src="content/images/image35.png" alt="">
<figcaption></figcaption>
</figure>

<div class="box box-generic">
<p class="box-title"><strong>Computational Cost of Hybrid Training</strong></p>
<p><strong><em>Circuit evaluations per epoch = N_train × (1 + 2p) / batch_size</em></strong></p>
<p><em>N_train: training set size. p: number of quantum parameters. batch_size: mini-batch size. For N_train=1000, p=16, batch_size=32: 1000×33/32 ≈ 1031 circuit evaluations per epoch. At 1024 shots/circuit on hardware: ~10⁶ shots per epoch.</em></p>
</div>

### 10.4.3 Practical PennyLane Features for QML

PennyLane provides a rich ecosystem of tools for quantum ML beyond the basic QNode:

**●  qml.qnn.TorchLayer / KerasLayer:** Wraps a QNode as a PyTorch nn.Module or Keras Layer, enabling seamless integration in classical DL architectures with automatic parameter management.

**●  qml.gradients module:** Provides all gradient methods: parameter\_shift, finite\_diff, spsa, adjoint, hadamard. Can be applied to any QNode with @qml.qjit compilation.

**●  qml.kernels module:** Tools for quantum kernel evaluation: kernel\_matrix() for Gram matrix computation, target\_alignment() for kernel alignment optimisation, polarity() for kernel quality metrics.

**●  Device zoo:** default.qubit (exact simulation), lightning.qubit (fast C++ simulation), lightning.gpu (GPU-accelerated), remote devices via IBM Quantum, Amazon Braket, Azure Quantum.

**●  qml.transforms:** Circuit compilation, noise mitigation (ZNE, probabilistic error cancellation), circuit cutting, and device compilation passes.

```python
# Quantum Transfer Learning — PennyLane + PyTorch ResNet-18
# PennyLane: Quantum Transfer Learning with PyTorch ResNet backbone
import pennylane as qml
import torch
import torch.nn as nn
import torchvision.models as models

n_qubits = 4
dev = qml.device("default.qubit", wires=n_qubits)

@qml.qnode(dev, interface="torch", diff_method="parameter-shift")
def quantum_transfer_circuit(inputs, weights):
    """Angle-encode 4 features + 2 variational layers."""
    qml.AngleEmbedding(inputs, wires=range(n_qubits), rotation="Y")
    qml.StronglyEntanglingLayers(weights, wires=range(n_qubits))
    return qml.expval(qml.PauliZ(0))

class QuantumTransferModel(nn.Module):
    def __init__(self):
        super().__init__()
        # Stage 1: Frozen ResNet-18 backbone
        resnet = models.resnet18(pretrained=True)
        self.backbone = nn.Sequential(*list(resnet.children())[:-1])
        for param in self.backbone.parameters():
            param.requires_grad = False  # Freeze backbone
        # Stage 2: Classical dimensionality reduction 512 -> 4
        self.reducer = nn.Linear(512, n_qubits)
        # Stage 3: Quantum variational head
        weight_shapes = {"weights": (2, n_qubits, 3)}
        self.quantum_head = qml.qnn.TorchLayer(
            quantum_transfer_circuit, weight_shapes)

    def forward(self, x):
        with torch.no_grad():
            features = self.backbone(x).flatten(1)  # [B, 512]
        z = torch.tanh(self.reducer(features)) * torch.pi  # [B, 4]
        return self.quantum_head(z)                          # [B, 1]

model = QuantumTransferModel()
trainable_params = sum(p.numel() for p in model.parameters() if p.requires_grad)
print(f"Trainable parameters: {trainable_params}")  # ~2096
```

<figure class="book-figure">
<img src="content/images/image36.png" alt="">
<figcaption></figcaption>
</figure>

## RECAP — SHORT ANSWER QUESTIONS & MODEL ANSWERS

Chapter 10: Data Encoding, Transfer Learning, Dequantisation & PennyLane

Instructions: Answer each question in 3–6 lines. Each question carries equal marks.

**PART A — QUESTIONS**

**Q1.  Compare angle encoding, amplitude encoding, and basis encoding for representing classical data as quantum states.**

**Q2.  What is 'data re-uploading', and why can even a single qubit be a universal function approximator with this technique?**

**Q3.  Describe the architecture of a quantum transfer learning model combining a classical backbone with a quantum head.**

**Q4.  What is the theoretical motivation for using a quantum head rather than purely classical layers in transfer learning?**

**Q5.  What is the Sample-and-Query (SQ) data model used in dequantisation results?**

**Q6.  Summarise Tang's dequantisation result and its significance for claims of quantum advantage in ML.**

**Q7.  Provide an 'honest comparison map' between quantum and classical ML, as this chapter frames it.**

**Q8.  What is a PennyLane QNode, and how does it enable hybrid quantum-classical differentiable programming?**

**Q9.  How does automatic differentiation 'through' a quantum circuit work in PennyLane, given that quantum gradients use the parameter-shift rule rather than backpropagation?**

**Q10.  Why does the number of circuit evaluations per training epoch scale as N\_train × (1+2p)/batch\_size, and what does this imply for training cost?**

**Q11.  What is the key practical trade-off in choosing between amplitude encoding and angle/data re-uploading encoding for a NISQ-era QML model?**

**Q12.  Why is it important, from a scientific-integrity standpoint, to discuss dequantisation alongside quantum ML methods in a textbook?**

**PART B — MODEL ANSWERS**

**Answer 1:**

Angle encoding maps each classical feature x\_i to a rotation angle, e.g. |ψ(x)⟩=⊗\_i Ry(x\_i)|0⟩, using n qubits for n features with shallow circuits but limited information density per qubit. Amplitude encoding packs an entire normalised vector x into the amplitudes of a single n-qubit state, |ψ(x)⟩=Σ\_i x\_i|i⟩, representing N=2^n features with only log₂N qubits but requiring a potentially deep state-preparation circuit. Basis encoding represents a classical bitstring directly as a computational basis state |b\_{n-1}...b\_1b\_0⟩, useful for discrete/integer data but not naturally suited to continuous features.

**Answer 2:**

Data re-uploading repeatedly interleaves data-encoding rotation layers with trainable variational layers on the same (even single) qubit, so that the input x is 'uploaded' multiple times rather than just once. Because each re-uploading layer contributes additional Fourier frequency components to the circuit's output function of x, stacking enough re-uploading layers on a single qubit can, in principle, approximate a rich enough function to be a universal approximator — showing that expressive power can come from repeated encoding depth rather than qubit count alone.

**Answer 3:**

A pretrained classical neural network (e.g. a convolutional network) processes raw input data and produces a lower-dimensional feature vector, which is then fed into a quantum circuit ('quantum head') consisting of a data-encoding layer followed by trainable variational layers and measurement, producing the final output. Only the quantum head's parameters (and optionally a small classical output layer) are trained on the new task, reusing the classical backbone's already-learned feature extraction — analogous to classical transfer learning, but replacing the final classical layers with a quantum circuit.

**Answer 4:**

The hypothesis is that a quantum circuit's naturally high-dimensional Hilbert space and its capacity for generating classically-hard-to-simulate correlations (entanglement) might provide a more expressive or more parameter-efficient final decision layer for certain problems than an equivalent-sized classical layer, potentially improving performance or model compactness — though this advantage is not proven in general and remains an active empirical research question.

**Answer 5:**

The SQ model grants a classical algorithm two capabilities analogous to those exploited by quantum algorithms: the ability to sample an index i from a vector with probability proportional to |v\_i|²/‖v‖² (mimicking quantum measurement statistics), and the ability to query specific entries of the vector directly. Classical algorithms given this 'quantum-inspired' oracle access can sometimes match the asymptotic runtime of quantum algorithms for certain linear-algebra-flavoured tasks.

**Answer 6:**

Ewin Tang showed that, given SQ-model access, classical algorithms can solve certain recommendation-system and low-rank matrix problems in time polylogarithmic in the matrix dimensions — matching the asymptotic scaling previously thought to be an exclusively quantum advantage (e.g. for HHL-based recommendation systems). This significantly narrowed the set of provable quantum speedups for linear-algebra-based machine learning tasks and is a key cautionary example in assessing quantum ML advantage claims.

**Answer 7:**

For problems with efficient SQ-model or otherwise low-rank/well-conditioned structure, dequantisation results suggest classical methods can often match quantum approaches asymptotically, undermining claimed quantum ML advantage. For problems lacking such structure (e.g. genuinely high-rank, ill-conditioned, or requiring access to genuinely quantum data/states), quantum methods may retain a provable or heuristic edge — so an honest assessment requires checking, problem by problem, whether the specific structural assumptions behind a given quantum advantage claim actually hold or are dequantisable.

**Answer 8:**

A QNode (declared via the @qml.qnode decorator) wraps a quantum circuit function so that it can be called and differentiated like an ordinary function within a classical automatic-differentiation framework such as PyTorch, specifying a target device and a differentiation method (e.g. diff\_method='parameter-shift'). This allows quantum circuits to be embedded directly inside classical neural network architectures and trained end-to-end using standard optimisers, unifying quantum and classical differentiable computation in one pipeline.

**Answer 9:**

PennyLane registers the parameter-shift rule as a custom differentiation rule within the classical autodiff framework's computation graph, so that when the framework computes gradients via backpropagation through the overall hybrid model, any node corresponding to a quantum circuit evaluation is differentiated using parameter-shift circuit evaluations (extra forward-mode quantum circuit executions) instead of the reverse-mode chain rule used for classical layers, while still producing a gradient that integrates seamlessly into the rest of the classical backpropagation computation.

**Answer 10:**

Each parameter-shift gradient computation for a single trainable parameter requires 2 additional circuit evaluations (at θ±shift) beyond the 1 forward-pass evaluation, so a circuit with p trainable parameters requires (1+2p) evaluations per training example; multiplying by the number of training examples per epoch and dividing by batch size (for any batching/parallelisation) gives the total circuit evaluations needed. This implies that gradient-based QML training cost grows linearly with the number of variational parameters, which can become a significant practical bottleneck compared to classical backpropagation's typically much cheaper gradient computation.

**Answer 11:**

Amplitude encoding is highly qubit-efficient (log₂N qubits for N features) but generally requires a deep, hardware-intensive state-preparation circuit that can be impractical on noisy near-term devices; angle encoding and data re-uploading use more qubits (or more circuit depth on a fixed small qubit count) but with shallower, more NISQ-friendly per-layer circuits. The choice therefore trades qubit count against circuit depth/fidelity requirements, and is usually decided based on the specific hardware's qubit budget versus achievable circuit depth before noise dominates.

**Answer 12:**

Presenting dequantisation results alongside quantum ML techniques ensures students understand the precise conditions under which a quantum method's claimed advantage actually holds, rather than accepting broad, unqualified claims of 'quantum speedup' for machine learning. This matches the textbook's stated commitment to precisely distinguishing demonstrated capability from hype, and equips students to critically evaluate future quantum ML advantage claims using the same SQ-model / dequantisation lens applied here.

## A. Solved Problems

<div class="box box-solved-problem">
<p class="box-title"><strong>Solved Problem 10.1</strong></p>
<p><strong>Problem:</strong> For a dataset with N=256 features, compare the qubit requirements and gate complexity for angle encoding vs amplitude encoding. Which is recommended for a NISQ device with 20 qubits?</p>
<p><strong>Solution:</strong></p>
<p>Angle encoding: 1 qubit per feature = 256 qubits required. Gates: 256 Ry gates (O(n) depth).</p>
<p>NISQ verdict: INFEASIBLE — requires 256 qubits, far beyond any current NISQ device.</p>
<p>Amplitude encoding: log₂(256) = 8 qubits required. Gates: O(256) = O(2⁸) circuit depth.</p>
<p>Gate complexity: exact amplitude encoding requires 2^n-2 = 254 multi-controlled gates.</p>
<p>NISQ verdict: ALSO PROBLEMATIC — the 254-gate deep circuit has far too many CNOTs for NISQ fidelity.</p>
<p>Recommended approach for 20-qubit NISQ device with N=256 features:</p>
<p>Step 1: PCA dimensionality reduction to 20 principal components (classical, O(N) operations).</p>
<p>Step 2: Angle encode the 20 PCA features on 20 qubits (20 Ry gates, depth 1).</p>
<p>Trade-off: lose information from dimensions 21–256. For most datasets, 20 PCs retain 80–95% of variance.</p>
<p>Conclusion: for NISQ, always preprocess with PCA + angle encoding rather than amplitude encoding.</p>
</div>

<div class="box box-solved-problem">
<p class="box-title"><strong>Solved Problem 10.2</strong></p>
<p><strong>Problem:</strong> Show that data re-uploading with L=2 on a single qubit achieves a Fourier polynomial of degree 2 in x, by tracing W₂Ry(x)W₁Ry(x)|0⟩ where W₁ = Rz(φ₁)Ry(θ₁) and W₂ = Rz(φ₂)Ry(θ₂).</p>
<p><strong>Solution:</strong></p>
<p>Start: |0⟩. Apply Ry(x): cos(x/2)|0⟩ + sin(x/2)|1⟩. (Frequencies accessed: ω = ±1/2 in amplitude, ±1 in expectation value.)</p>
<p>Apply W₁ = Rz(φ₁)Ry(θ₁): a fixed SU(2) rotation with no x-dependence.</p>
<p>Result: state |ψ₁(x)⟩ = a₁(x)|0⟩ + b₁(x)|1⟩ where a₁,b₁ are degree-1 trig polynomials in x.</p>
<p>Apply Ry(x) again: multiplies amplitudes by cos(x/2) and sin(x/2) factors, adding ±1 to existing frequencies.</p>
<p>New frequency range: {−2, −1, 0, +1, +2} in the expectation value.</p>
<p>Apply W₂: another fixed rotation, does not change the frequency content.</p>
<p>Final: ⟨Z⟩ = c₀ + c₁cos(x)+s₁sin(x) + c₂cos(2x)+s₂sin(2x). ✔ Degree-2 Fourier polynomial.</p>
<p>The 5 coefficients c₀,c₁,s₁,c₂,s₂ are determined by θ₁,φ₁,θ₂,φ₂.</p>
<p>Universal approximation: as L→∞, re-uploading can represent any continuous function on ℝ. ✔</p>
</div>

<div class="box box-solved-problem">
<p class="box-title"><strong>Solved Problem 10.3</strong></p>
<p><strong>Problem:</strong> Explain why quantum transfer learning with frozen ResNet-18 + 4-qubit head does NOT constitute a computational complexity speedup over classical methods.</p>
<p><strong>Solution:</strong></p>
<p>Classical backbone runtime: ResNet-18 inference is O(1.8×10⁹) multiply-add operations (11M parameters).</p>
<p>Quantum head: 4-qubit circuit with 24 parameters. For 4 qubits, the Hilbert space has dimension 2⁴=16.</p>
<p>Classical equivalent head: a linear layer 4→16→1 has 4×16+16+1=81 parameters, runs in O(80) FLOPs.</p>
<p>4-qubit quantum head: requires 2×24=48 parameter-shift circuit evaluations per gradient step per sample.</p>
<p>Comparison per gradient step: classical = O(80) FLOPs; quantum = O(48) circuit evaluations.</p>
<p>Each 4-qubit circuit evaluation: ~50 ns on GPU vs 1 FLOP ~1 ps. Quantum is ~50,000× slower per operation.</p>
<p>No complexity advantage: the quantum head runs in a 16-dimensional space (same as 4-neuron classical layer) but evaluates O(p) times more expensive gradients.</p>
<p>What QTL does offer: the curved geometry of SU(16) provides different inductive bias from a flat linear layer; potential regularisation benefit. But this is a heuristic quality advantage, not a complexity speedup.</p>
<p>Conclusion: QTL is a hybrid heuristic with potential accuracy benefits in specific small-data regimes — not a polynomial or exponential speedup in any computational complexity sense.</p>
</div>

<div class="box box-solved-problem">
<p class="box-title"><strong>Solved Problem 10.4</strong></p>
<p><strong>Problem:</strong> A PennyLane + PyTorch hybrid model has 12 quantum parameters (p=12) and trains on N=500 examples with batch size B=32. Compute total quantum circuit evaluations per epoch.</p>
<p><strong>Solution:</strong></p>
<p>Forward pass evaluations per batch: B = 32 (one circuit per example per forward pass).</p>
<p>Backward pass evaluations per batch: 2p per example = 2×12 = 24 per example.</p>
<p>Total backward: B×24 = 32×24 = 768 evaluations per batch.</p>
<p>Total per batch: B(1+2p) = 32×(1+24) = 32×25 = 800 evaluations.</p>
<p>Batches per epoch: ceil(500/32) = 16 batches.</p>
<p>Total per epoch: 16×800 = 12,800 circuit evaluations.</p>
<p>At 1024 shots per evaluation on real hardware: 12,800×1024 ≈ 13.1 million shots per epoch.</p>
<p>IBM Quantum throughput (~1000 circuits/hour): 12,800 circuits ≈ 12.8 hours per epoch.</p>
<p>On default.qubit simulator: ~1 ms per circuit → 12.8 seconds per epoch. Feasible.</p>
<p>For 50 epochs: 640,000 circuit evaluations. Hardware training is currently impractical; simulation is essential.</p>
</div>

<div class="box box-solved-problem">
<p class="box-title"><strong>Solved Problem 10.5</strong></p>
<p><strong>Problem:</strong> Explain why the SQ-classical PCA algorithm (Tang 2019) achieves the same O(poly(k)/poly(ε)·polylog(mn)) complexity as quantum PCA.</p>
<p><strong>Solution:</strong></p>
<p>Setup: m×n data matrix A, want top-k singular vectors. Classical naive SVD: O(mn min(m,n)).</p>
<p>Quantum PCA (Lloyd et al. 2014): uses QPE on ρ = AᵀA/Tr(AᵀA) in quantum RAM. Complexity: O(poly(k,1/ε)·polylog(mn)).</p>
<p>Tang’s classical algorithm uses SQ access to rows of A:</p>
<p>Sample row i with probability ||A_i||²/||A||²_F in O(log m) from a balanced binary tree.</p>
<p>Query any element A_{ij} in O(1).</p>
<p>Monte Carlo SVD: estimate AᵀA by importance-sampled outer products of rows.</p>
<p>With k²/ε² sampled rows, the top-k subspace is approximated to error ε in spectral norm.</p>
<p>Total SQ operations: O(k²/ε² × polylog(n)) — same asymptotic as quantum!</p>
<p>Root cause: quantum state preparation of |ρ⟩ via QRAM gives exactly SQ access to the rows of A.</p>
<p>Measuring the quantum state samples a row index; querying the circuit gives individual elements.</p>
<p>Conclusion: the quantum PCA’s speedup was comparing to naive O(mn) classical — not to the optimal classical with SQ access. Tang’s algorithm removes the separation. ✔</p>
</div>

<div class="box box-solved-problem">
<p class="box-title"><strong>Solved Problem 10.6</strong></p>
<p><strong>Problem:</strong> Compare angle encoding and re-uploading for the function f(x) = sin(3x) + 0.5cos(2x) on [0,2π]. What minimum L is needed for each encoding to represent this function?</p>
<p><strong>Solution:</strong></p>
<p>f(x) = sin(3x) + 0.5cos(2x) contains Fourier frequencies ω ∈ {2, 3} (non-zero coefficients at harmonics 2 and 3).</p>
<p>Standard angle encoding (1 Ry(x), no re-uploading): accessible frequencies = {−1, 0, +1}.</p>
<p>Cannot represent ω=2 or ω=3. INSUFFICIENT with r=1.</p>
<p>Need r=3 encoding repetitions (three Ry(x) blocks) to access frequencies up to ω=±3.</p>
<p>Data re-uploading (L layers on 1 qubit): each re-upload adds ±1 to the frequency range.</p>
<p>L=1: {−1,0,+1}. L=2: {−2,...,+2}. L=3: {−3,...,+3}. Minimum L=3.</p>
<p>Both require exactly 3 "encounters" with x to access frequency 3.</p>
<p>Multi-qubit circuit with entanglement: a 2-qubit circuit with 1 encoding layer + CNOT creates</p>
<p>multi-qubit Fourier terms ω_0±ω_1 allowing ω=2 with only r=1 (if 2 qubits encode x₀+x₁ type terms).</p>
<p>Conclusion: for 1 qubit, minimum L=3 for both approaches. Multi-qubit entanglement can reduce r. ✔</p>
</div>

<div class="box box-solved-problem">
<p class="box-title"><strong>Solved Problem 10.7</strong></p>
<p><strong>Problem:</strong> In quantum transfer learning, what gradient is computed and what is frozen? Write the explicit parameter update rule for one training step with Adam optimiser.</p>
<p><strong>Solution:</strong></p>
<p>Trainable parameters: (a) quantum head parameters θ ∈ ℝᵖ (2 layers × 4 qubits × 3 = 24 params); (b) classical reducer W_r ∈ ℝ^{512×4} (if not frozen).</p>
<p>Frozen: all of ResNet-18 backbone (11M parameters). torch.no_grad() is used in backbone forward pass.</p>
<p>Forward: x → ResNet(frozen) → z ∈ ℝ^{512} → tanh(W_r z)·π → Ry encoding → W(θ) → ⟨Z₀⟩ → loss L.</p>
<p>Gradient (parameter-shift): ∂L/∂θ_k = (∂L/∂⟨Z₀⟩) × [f(θ_k+π/2)−f(θ_k−π/2)] / 2.</p>
<p>First factor from PyTorch autograd; second from two extra quantum circuit evaluations.</p>
<p>Gradient for reducer: ∂L/∂W_r = (∂L/∂⟨Z₀⟩) × J_θ × (∂θ/∂z) × (1−z²)·π (chain rule through tanh scaling).</p>
<p>Adam update (step t): m ← β₁m + (1−β₁)g; v ← β₂v + (1−β₂)g²; m̂ = m/(1−β₁^t); v̂ = v/(1−β₂^t).</p>
<p>θ ← θ − η·m̂/(√v̂+ε). No gradient enters ResNet. ✔</p>
</div>

<div class="box box-solved-problem">
<p class="box-title"><strong>Solved Problem 10.8</strong></p>
<p><strong>Problem:</strong> A quantum ML algorithm claims exponential speedup via amplitude encoding + QRAM. Outline the dequantisation challenge to this claim.</p>
<p><strong>Solution:</strong></p>
<p>Claim: algorithm uses QRAM to prepare |ψ⟩ = Σ_i x_i|i⟩ in O(polylog N), then achieves polylog N total complexity.</p>
<p>Step 1: Identify access model. QRAM enables: (a) sample index i with prob x_i²/||x||²; (b) query x_i in O(1).</p>
<p>This is exactly SQ access! Any data structure enabling QRAM preparation also enables classical SQ.</p>
<p>Step 2: Tang’s classical algorithm. For any quantum algorithm using amplitude-encoded data and returning O(1) measurements, a classical Monte Carlo algorithm with SQ access achieves the same polylog(N) complexity.</p>
<p>Step 3: Fair comparison. The claimed "exponential speedup" compares quantum O(polylog N) to naive classical O(N). But the fair comparison is quantum vs SQ-classical — and SQ-classical achieves the same O(polylog N).</p>
<p>Step 4: When dequantisation fails. (a) Input is already quantum (quantum sensor, quantum state pipeline); (b) SQ data structure is not achievable for the specific data format; (c) the algorithm exploits non-amplitude features (quantum periodicity, interference patterns, not just sampling).</p>
<p>Conclusion: without specifying why SQ access is unavailable, any amplitude-encoding-based quantum speedup claim is potentially dequantisable and must be verified case by case.</p>
</div>

## B. Unsolved Problems

*Solve each problem independently. Answers are in brackets for self-checking.*

**1.** A dataset has N=1024 continuous features. Compare qubit requirements for: (a) angle encoding; (b) amplitude encoding; (c) angle encoding after PCA to 10 features. Which is most practical for a 50-qubit NISQ device? *[(a) Angle: 1024 qubits — infeasible. (b) Amplitude: log₂(1024)=10 qubits — feasible in qubits, but requires O(2¹⁰)=1024-gate state prep circuit — too deep for NISQ. (c) PCA+angle: 10 qubits, 10 Ry gates, depth 1 — clearly most practical. Most datasets retain 85%+ variance in first 10 PCs. Recommendation: always PCA+angle for continuous data on NISQ hardware.]*

**2.** In data re-uploading on a single qubit, show that L=3 re-uploads of the form |ψ⟩ = W₃Ry(x)W₂Ry(x)W₁Ry(x)|0⟩ can represent any Fourier polynomial with frequencies |ω| ≤ 3. *[Each SU(2) gate e^{-ixσ/2} with σ having eigenvalues ±1/2 shifts frequency support by ±1/2. After 3 Ry(x) gates, accumulated frequency range in amplitude: [-3/2, 3/2] in steps of 1/2. In ⟨Z⟩ = |ψ|², this gives integer frequencies {-3,-2,-1,0,1,2,3}. By choosing the variational W₁,W₂,W₃ appropriately, any combination of 7 complex Fourier coefficients is achievable. Universal: the Fourier series with |ω|≤3 is a complete function class for L² functions that L=3 re-uploading can approximate arbitrarily well. ✔]*

**3.** For quantum transfer learning with ResNet-18 backbone: (a) how many circuit evaluations per epoch for 200 training images, batch size 20? (b) What fraction of runtime is quantum vs classical at typical speeds? *[(a) Batches/epoch: 200/20=10. Per batch: 20×(1+2×24)=20×49=980 circuit evaluations. Per epoch: 10×980=9,800. At 1024 shots/circuit: ~10 million shots/epoch. (b) Classical backbone: 200×1.8×10⁹ FLOPS × ~1 ps/FLOP = 0.36 s. Quantum head (simulator): 9,800 × 1 ms = 9.8 s. Quantum fraction: 9.8/(9.8+0.36) ≈ 96% of runtime is quantum (on CPU simulator). On GPU (lightning.gpu): ~0.1 ms/circuit → 0.98 s quantum, comparable to classical backbone. Hardware: 9,800 circuits ÷ 1000 circuits/hr = 9.8 hours/epoch — impractical.]*

**4.** Verify that a balanced prefix-sum tree (Fenwick tree) enables O(log N) sampling from pᵢ = vᵢ²/||v||². Describe the structure and the two SQ operations. *[Structure: array BIT[1..N] where BIT[i] = sum of v[j]² for j in a specific range (Fenwick tree pattern). Build: O(N) to initialise. SQ operations: (1) Sample — generate u ~ Uniform[0, ||v||²], binary search in BIT to find smallest i with prefix\_sum(i) ≥ u in O(log N); (2) Query — return v[i] directly from original array in O(1). This classical data structure mirrors quantum amplitude-encoding measurement: measuring |ψ⟩=Σ vᵢ|i⟩ samples i with probability vᵢ²/||v||² in O(1) quantum measurement. The Fenwick tree achieves the same distribution classically in O(log N). This is the key structure underlying Tang’s dequantisation algorithms.]*

**5.** A hybrid PennyLane model has p=8 quantum parameters. Compare SPSA vs full parameter-shift in circuit evaluations per epoch (N=1000, B=32) and discuss when SPSA is preferable. *[Parameter-shift: B(1+2p) = 32×17 = 544/batch × 32 batches = 17,408/epoch. SPSA: B×3 = 96/batch × 32 = 3,072/epoch. Speedup: 17,408/3,072 ≈ 5.7×. SPSA preferable when: (1) running on real quantum hardware where each circuit is expensive in time and money; (2) gradient quality is less critical (stochastic noise can help escape local minima); (3) p is large (advantage scales as p). Not preferable when: (1) exact gradients needed for diagnostics; (2) circuit evaluations are free (simulation); (3) convergence precision matters. Common practice: use SPSA for hardware, parameter-shift for simulators.]*

**6.** The quantum recommendation system (KP17) claims O(polylog(mn)) vs classical O(mn) speedup. Tang achieves O(poly(k,1/ε)·polylog(mn)). For k=5, ε=0.1, m=n=1000, numerically compare all three. *[Naive classical O(mn)=O(10⁶). Quantum (KP17): O(k³/ε²·log²(mn)) = O(125·100·log²(10⁶)) = O(12,500·144) ≈ O(1.8×10⁶). Wait — KP17 is actually O(polylog(mn)·poly(k,1/ε)) ≈ same as Tang. Naive: 10⁶. KP17/Tang: ~O(10⁵). Both beat naive by ~10×. For k=50, ε=0.01: KP17/Tang ≈ O(50³/0.01²·400) = O(5×10¹⁰) — much slower than naive O(10⁶). Lesson: the polylog(mn) factor is tiny, but the poly(k,1/ε) factor can dominate and make both quantum and Tang-classical slower than naive for large k or small ε.]*

**7.** Show that the PyTorch gradient chain rule ∂L/∂θ\_k = g′(f\_θ(x)) × [f(θ\_k+π/2)−f(θ\_k−π/2)]/2 works correctly for a loss L = g(⟨Z⟩) where g is a classical function. *[Chain rule: ∂L/∂θ\_k = (∂L/∂f\_θ)(∂f\_θ/∂θ\_k). Classical: ∂L/∂f\_θ = g'(f\_θ(x)) — PyTorch autograd computes this downstream. Quantum: ∂f\_θ/∂θ\_k = [f(θ\_k+π/2)−f(θ\_k−π/2)]/2 — PennyLane parameter-shift. PennyLane implements this as the custom VJP (vector-Jacobian product) for the QNode: when PyTorch calls backward() and passes g'(f\_θ) as the upstream gradient, PennyLane multiplies it by the parameter-shift Jacobian entries. This is exactly the chain rule. ✓ No approximation at any step. The PyTorch autograd engine treats the QNode as a black-box differentiable function with a custom backward method.]*

**8.** For quantum transfer learning, explain why the quantum head’s 2ⁿ-dimensional Hilbert space does NOT equal 2ⁿ free parameters. *[Dimension of SU(2ⁿ): the full n-qubit unitary group has 2²ⁿ−1 real parameters. For n=4: 255 parameters. A 2-layer hardware-efficient ansatz: 2×4×2 = 16 parameters. The 16 parameters trace a 16-dimensional sub-manifold of SU(16), covering a small fraction of the full group. Expressibility ε\_expr measures how much of SU(16) is reachable: with 16 params, ε\_expr ≈ 0.3 (moderate). Full expressibility needs L=O(4ⁿ/n) layers — exponential. The 2ⁿ=16 dimensional Hilbert space is the ambient space; the circuit only reaches 16/255 ≈ 6% of the unitary group with L=2 layers. More layers increase coverage at cost of barren plateau risk.]*

**9.** A NISQ hybrid model has 6 classical layers (128→128) and a 4-qubit QNN layer with 12 parameters. Estimate the fraction of gradient computation time on quantum vs classical (batch size B=32). *[Classical gradient: 6 layers of 128×128. Backprop FLOPs: O(B×6×128²) = 32×6×16,384 ≈ 3.1×10⁶ FLOPs. On GPU at ~10¹² FLOPS: 3.1×10⁶/10¹² = 3.1 μs. Quantum gradient: 2p=24 evaluations per sample × B=32 = 768 circuit evaluations. 4-qubit statevector sim: ~100 μs each (CPU) → 768×100 = 76.8 ms. On GPU lightning.qubit: ~5 μs each → 768×5 = 3.84 ms. Quantum fraction (CPU): 76,800/(76,800+3.1) ≈ 99.996%. Quantum fraction (GPU): 3,840/(3,840+3.1) ≈ 99.9%. The quantum circuit completely dominates gradient computation. Lesson: even a small quantum layer (12 params) is computationally dominant in a hybrid model.]*

**10.** Explain why data re-uploading can outperform multi-qubit QNNs without re-uploading for some 1D regression tasks. *[Data re-uploading on 1 qubit with L=10: accesses Fourier frequencies ω ∈ {-10,...,0,...,+10} — 21 distinct modes. 10-qubit product-state QNN (no entanglement, 1 encoding layer): each qubit individually represents cos(xᵢ) or sin(xᵢ) for its assigned feature. For 1D input (x₀ only), only qubit 0 has frequency ω=±1; others are constant. Net function: cos(x₀+θ) — just 1 frequency mode! 10-qubit entangling QNN with 1 encoding layer: multi-qubit Fourier products create frequency sums ω₀+ω₁+... up to ±n/2 = ±5, giving 11 modes. But with only 1 encoding layer, frequencies are still limited to ±1 per qubit. Conclusion: for 1D regression requiring high-frequency components, L=10 re-uploading on 1 qubit (21 modes) outperforms a 10-qubit product circuit (1 mode) and matches a deeply entangling 10-qubit circuit (up to 21 modes only with L≥10 encoding repetitions).]*

## C. Multiple Choice Questions

*Note: Answers are given at the end of this section.*

**Q1.** Amplitude encoding of N features requires how many qubits?

(a) N qubits

(b) N/2 qubits

(c) log₂N qubits

(d) √N qubits

**Q2.** The main disadvantage of amplitude encoding for NISQ devices is:

(a) It requires too many qubits

(b) The state preparation circuit has exponential O(2ⁿ) gate depth

(c) It cannot represent continuous-valued features

(d) It violates the no-cloning theorem

**Q3.** Data re-uploading is superior to single-layer angle encoding for function approximation because:

(a) It uses fewer qubits per feature

(b) Each re-upload adds new Fourier frequency harmonics, increasing the polynomial degree of the function class

(c) It automatically avoids barren plateaus

(d) It encodes data in the amplitude rather than angle of the qubit state

**Q4.** In quantum transfer learning, which components are frozen (NOT trained)?

(a) Only the quantum variational head

(b) Only the dimensionality reduction layer

(c) The classical pre-trained backbone (e.g., ResNet weights)

(d) Both the backbone and the quantum head

**Q5.** The SQ (Sample-and-Query) data model enables dequantisation because:

(a) It allows classical computers to run quantum circuits

(b) It provides classical algorithms with the same data access pattern as quantum amplitude encoding, enabling polylog-complexity Monte Carlo algorithms

(c) It replaces the quantum Gram matrix with a classical approximation

(d) It uses tensor networks to simulate quantum states classically

**Q6.** Tang’s dequantisation result (2019) showed that quantum recommendation systems:

(a) Are incorrect and provide no speedup over naive classical

(b) Can be matched by a classical algorithm with SQ data access in the same polylog time complexity

(c) Only work for sparse matrices

(d) Require fault-tolerant hardware, not just NISQ

**Q7.** A PennyLane QNode with diff\_method='parameter-shift' computes gradients:

(a) Using finite differences with a small step h

(b) Using classical backpropagation through the circuit simulation

(c) Exactly, using two circuit evaluations at θ\_k±π/2 per parameter

(d) Approximately, using a random gradient estimator

**Q8.** The TorchLayer wrapper in PennyLane allows a QNode to:

(a) Run on GPU hardware directly

(b) Be embedded as a trainable layer in a PyTorch nn.Module with automatic gradient support

(c) Replace all classical layers with quantum equivalents

(d) Simulate the circuit without a quantum device

**Q9.** Basis encoding is most suitable for which type of data?

(a) Real-valued continuous features like pixel intensities

(b) Binary or integer-valued data such as graph adjacency matrices or molecular fingerprints

(c) High-dimensional data with N > 2ⁿ features

(d) Time-series data requiring temporal encoding

**Q10.** The Liu et al. (2021) result on quantum kernel methods proved that:

(a) Quantum kernels always outperform classical kernels on real-world data

(b) There exists a quantum kernel for a specific classification problem that no efficient classical algorithm can match

(c) Kernel alignment always predicts classification accuracy

(d) Quantum kernels are dequantised by Tang’s SQ method

**Q11.** In PennyLane-PyTorch hybrid training, gradient ∂L/∂θ\_k requires:

(a) One forward pass through the circuit

(b) Two circuit evaluations (at θ\_k+π/2 and θ\_k−π/2) per quantum parameter

(c) A measurement of all N output qubits simultaneously

(d) Access to the circuit’s internal statevector

**Q12.** The number of Fourier frequency modes accessible to a 1-qubit re-uploading circuit with L=5 uploads is:

(a) 5

(b) 11 (frequencies from −5 to +5)

(c) 32 = 2⁵

(d) 1 (only the fundamental frequency)

**Q13.** Which statement about quantum transfer learning is correct?

(a) The quantum head always outperforms an equivalent classical head

(b) Training only the quantum head (frozen backbone) requires O(p) quantum circuit evaluations per gradient step where p is the number of quantum parameters

(c) The classical backbone must be re-trained alongside the quantum head

(d) Transfer learning is only possible with amplitude encoding

**Q14.** Dequantisation does NOT apply to:

(a) Quantum linear systems (HHL) with SQ-accessible input

(b) Quantum principal component analysis

(c) Quantum recommendation systems

(d) Shor’s factoring algorithm, which exploits quantum periodicity rather than data encoding

**Q15.** SPSA provides a speedup over full parameter-shift gradient in terms of circuit evaluations because:

(a) It computes exact gradients using fewer evaluations

(b) It estimates the full gradient vector with only 2 circuit evaluations via a random perturbation direction, at the cost of noisy estimates

(c) It skips gradient computation entirely

(d) It uses classical backpropagation instead of quantum circuits

<div class="box box-generic">
<p class="box-title"><strong>MCQ ANSWERS</strong></p>
<p>Q1: (c) log₂N qubits — amplitude encoding packs N amplitudes into n=log₂N qubits by using the full 2ⁿ-dimensional Hilbert space</p>
<p>Q2: (b) Exponential gate depth O(2ⁿ) for state preparation — the qubit compression is real but the circuit preparation cost negates it for NISQ devices</p>
<p>Q3: (b) Each re-upload adds Fourier harmonics — L re-uploads give access to frequencies up to order L, enabling degree-L polynomial function approximation</p>
<p>Q4: (c) The classical pre-trained backbone is frozen — only the quantum head (and optionally a classical reducer) are trained; this is the defining property of transfer learning</p>
<p>Q5: (b) SQ access matches quantum amplitude encoding — the sample operation mirrors quantum measurement, the query operation mirrors oracle access, enabling the same polylog algorithms classically</p>
<p>Q6: (b) Classical with SQ access achieves same polylog complexity — Tang's dequantisation shows quantum recommendation systems have no advantage over classical SQ algorithms</p>
<p>Q7: (c) Two circuit evaluations at ±π/2 per parameter — the parameter-shift rule is exact with zero approximation error, unlike finite differences</p>
<p>Q8: (b) Embedded as trainable layer in nn.Module with auto gradients — TorchLayer makes QNodes behave identically to classical nn.Linear layers in PyTorch</p>
<p>Q9: (b) Binary or integer-valued data — basis encoding maps integers directly to computational basis states, most natural for discrete structured data</p>
<p>Q10: (b) There exists a quantum kernel with formal classical hardness separation — Liu et al. proved a rigorous complexity-theoretic quantum advantage for a specific classification problem</p>
<p>Q11: (b) Two circuit evaluations per quantum parameter — the parameter-shift rule requires exactly two evaluations per parameter regardless of circuit size or depth</p>
<p>Q12: (b) 11 frequencies from −5 to +5 — L re-uploads give integer frequencies in {−L,...,0,...,+L} = 2L+1 = 11 modes for L=5</p>
<p>Q13: (b) O(p) quantum evaluations per gradient step — training only the quantum head with p parameters requires 2p parameter-shift evaluations per training example</p>
<p>Q14: (d) Shor's factoring — exploits quantum periodicity via QFT, not data encoding or amplitude access; no SQ structure applies; the algorithm is fundamentally quantum</p>
<p>Q15: (b) Full gradient with 2 evaluations using random perturbation — SPSA is efficient but noisy; full gradient quality at a fraction of the cost of parameter-shift</p>
</div>

## D. Theory Questions

**1.**  Derive the data re-uploading theorem (Perez-Salinas et al. 2020): show that a single qubit with L re-uploads can represent any function f: ℝⁿ → [-1,1] as a finite Fourier series. (a) What are the accessible frequency modes for L uploads on 1 qubit? (b) How many variational parameters are required for universal approximation of a degree-K polynomial? (c) Derive the minimum L required to approximate the function f(x) = sin(5x) + 2cos(3x).

**2.**  Prove that amplitude encoding satisfies |⟨ψ(x)|ψ(x′)⟩|² = (x·x′)² for normalised vectors x,x′ ∈ ℝ^N. Describe the data preprocessing step needed before applying amplitude encoding. Show that normalisation by ||x|| restricts the encoded function class to homogeneous degree-0 functions.

**3.**  Describe quantum transfer learning (QTL) theoretically: (a) Why does freezing the classical backbone and training only the quantum head reduce the risk of barren plateaus? (b) Prove that the gradient variance for a depth-L quantum head with n qubits scales as O(2^{-n}) for random encoding but O(1) for structured classical backbone outputs. (c) Describe two specific scenarios where QTL is likely to outperform classical transfer learning.

**4.**  Explain Tang’s dequantisation framework in detail: (a) Define the SQ access model precisely and explain what data structure realises it classically. (b) Describe the quantum-inspired Monte Carlo matrix multiplication technique. (c) Prove the key lemma: a matrix-vector product Av can be approximated to error ε in O(||A||²\_F/ε²) SQ operations. (d) Identify three conditions under which dequantisation applies and three conditions under which it does not.

**5.**  Describe the PennyLane-PyTorch interface in detail: (a) Explain how the parameter-shift Jacobian is incorporated into the PyTorch autograd graph using a custom VJP. (b) Describe the difference between diff\_method='parameter-shift', 'backprop', and 'adjoint' in terms of hardware compatibility and computational cost. (c) Derive the formula for total circuit evaluations per training epoch as a function of dataset size N, batch size B, and quantum parameter count p. (d) When is diff\_method='adjoint' preferable over parameter-shift?

**6.**  Analyse the information-theoretic limits of angle encoding: (a) Show that angle encoding with n qubits is equivalent to projecting data onto the n-sphere S^n in terms of what features are preserved. (b) Derive the input separation at which K(x,x′) = 1/2 for the Ry(x) angle kernel. (c) Explain how scaling x to [0,π] vs [0,2π] affects the kernel bandwidth and effective data resolution.

**7.**  Compare the classical and quantum descriptions of the ZZFeatureMap feature space: (a) Write the explicit 4×4 unitary matrix U\_φ(x) for n=2 qubits, r=1. (b) Show that K(x,x′) = |⟨0|U†(x′)U(x)|0⟩|² is equivalent to a classical inner product in a 4-dimensional feature space. (c) Identify a data distribution and circuit configuration where this kernel provides classification structure not captured by the classical RBF kernel.

**8.**  Discuss the current theoretical state of quantum advantage in QML (2025): (a) Summarise the Liu et al. (2021) formal quantum advantage result and its key assumptions. (b) Explain the Huang et al. (2021) results showing classical simulation of many QML models. (c) Identify the gap between these theoretical results and practical QML applications. (d) Describe what dataset structure would be required for a genuine, verifiable practical quantum ML advantage.

**9.**  Describe the StronglyEntanglingLayers architecture in PennyLane: (a) Specify the exact gate sequence per layer (Rot gates + controlled-Z in a specific ring+skip pattern). (b) Compute the number of parameters for n qubits and L layers. (c) Show analytically that the entanglement capability Q increases monotonically with L. (d) Compare to the hardware-efficient ansatz (HEA) in terms of expressibility, entanglement capability, and barren plateau risk for n=6 qubits.

**10.**  Explain the quantum natural gradient and quantum Fisher information (QFI): (a) Define QFI as the Fubini-Study metric on the quantum state manifold. (b) Show that the quantum natural gradient update θ̇ = ηF^{-1}∂E/∂θ performs Riemannian gradient descent on the unitary manifold. (c) Describe how to estimate the QFI matrix using four parameter-shift circuit evaluations per matrix entry. (d) Under what conditions does quantum natural gradient provably converge in fewer epochs than vanilla gradient descent?

## E. Programming Assignments

**[PA-10.1]  Data Encoding Comparison: Angle vs Re-uploading for Function Approximation:**

Implement and compare three encoding strategies for approximating f(x) = sin(3x) + 0.5cos(2x) on x ∈ [0, 2π] using PennyLane: (a) Standard angle encoding (1 qubit, 1 Ry(x) gate + variational layers, no re-uploading). (b) Data re-uploading with L=3 uploads (1 qubit, 3×[Ry(x)+W(θ)]). (c) Data re-uploading with L=5 uploads (same but more layers). For each: (i) train to minimise MSE between circuit output and f(x) on 50 training points using Adam (200 epochs, lr=0.05); (ii) evaluate on 200 test points and compute R²; (iii) plot the learned function vs true function; (iv) record final MSE and number of trainable parameters. Additionally: (d) plot the discrete Fourier spectrum of each learned function and overlay with the true spectrum — verify that only L≥3 captures the ω=3 mode. (e) Include a brief theoretical explanation of why L=1 cannot represent f(x) (missing frequency modes) and why L=3 is the minimum.

**[PA-10.2]  PennyLane-PyTorch Transfer Learning on the Iris Dataset:**

Implement quantum transfer learning for binary classification (setosa vs non-setosa) on the Iris dataset (4 features, 150 samples). Pipeline: (a) Classical "backbone": a 3-layer MLP (4→32→16→4) pre-trained for 100 epochs on 80% of the data, then frozen. (b) Quantum head: 4-qubit PQC (AngleEmbedding + 2 StronglyEntanglingLayers) as TorchLayer, with a 4→4 trainable linear reducer before encoding. (c) Train only the quantum head + reducer for 50 epochs using Adam (lr=0.01) with parameter-shift. (d) Compare with: (i) classical frozen backbone + classical linear head; (ii) full classical MLP trained end-to-end; (iii) QSVM with ZZFeatureMap (n=4, r=2). (e) Plot: training loss per epoch for all models; final confusion matrices on test set; quantum kernel matrices for QSVM. Report: final test accuracies, number of trainable parameters, circuit depth, and a discussion of when the quantum head can add value over the classical equivalent.

**[PA-10.3]  Dequantisation Demonstration: Classical SQ vs Quantum PCA:**

Implement and compare three approaches to finding the top-k subspace of a data matrix. Dataset: synthetic m×n matrix A (m=50, n=200, rank k=5), constructed as A = U\_kΣ\_kV\_kᵀ + 0.1·Noise. (a) Classical PCA via numpy.linalg.svd: record wall-clock runtime and reconstruction error ||A − A\_k||\_F/||A||\_F. (b) SQ-inspired classical algorithm: (i) build a prefix-sum tree over row norms ||A\_i||²; (ii) sample O(k²/ε²) rows with probability ∝ ||A\_i||²; (iii) compute SVD of the sampled submatrix; (iv) record number of sample+query operations and reconstruction error. (c) Simulated quantum PCA via PennyLane: (i) form the n×n density matrix ρ = AᵀA/Tr(AᵀA) (use n=8 for tractability, so modify A accordingly); (ii) simulate QPE on ρ for k=3 eigenvalues; (iii) record circuit evaluations and eigenvalue accuracy vs true eigenvalues. (d) Plot accuracy vs cost for all three methods on a log-log scale. (e) Analyse: under what theoretical conditions would quantum PCA outperform the SQ-classical algorithm? Are those conditions satisfied in your experiment? Deliver a 3-page written analysis discussing the dequantisation implications.

## F. Project Suggestions

**Project 10.A — Comprehensive Data Encoding Benchmark: Accuracy, Cost, and Quantum Advantage:**

Conduct a rigorous comparative study of all four encoding strategies (angle, amplitude, basis, re-uploading) across five benchmark classification datasets: (1) Iris (4 features, 3 classes), (2) MNIST 3-vs-8 (784 features, 2 classes), (3) Breast Cancer Wisconsin (30 features, 2 classes), (4) a synthetic dataset with XOR-like structure in 4D, (5) a molecular property dataset using binary Morgan fingerprints (512 bits). For each dataset-encoding pair: (a) Implement the encoding in PennyLane and build the complete QNN pipeline (encoding + 2-layer HEA + measurement). (b) For amplitude encoding only: implement both exact state preparation (small n) and approximate preparation (quantum RAM simulation via qiskit’s StatePreparation). (c) Train for 100 epochs using Adam with parameter-shift on 70% train / 15% val / 15% test splits. (d) Record: test accuracy, number of circuit evaluations per epoch, circuit depth (CNOT count), number of trainable parameters, training wall-clock time. (e) For the re-uploading circuit: vary L ∈ {1, 2, 3, 5, 10} and plot accuracy vs L. (f) Compare all QML results with classical SVM (linear, RBF, polynomial) on the same splits. Write a 20-page report with: encoding strategy theory, implementation details, full benchmark tables, learning curves, and a section on when (if ever) quantum encoding provides measurable accuracy improvements over classical RBF-SVM.

**Project 10.B — Dequantisation in Practice: Building a Tang-Inspired Classical ML Library:**

Implement a practical Python library for "quantum-inspired" classical ML algorithms following the SQ-model framework, and benchmark it against both naive classical and simulated quantum algorithms. The library should implement: (a) SQ data structure: a Fenwick-tree-based prefix-sum data structure supporting O(log N) sampling from a distribution proportional to |vᵢ|² for any vector v. (b) Quantum-inspired PCA: Tang’s importance-sampled SVD algorithm that approximates the top-k singular subspace in O(k² poly(1/ε) polylog(mn)) SQ operations. (c) Quantum-inspired linear system solver: apply the SQ sampling approach to approximate the solution to Ax=b for a well-conditioned sparse A. (d) Quantum-inspired recommendation system: estimate user-item scores using importance-sampled low-rank approximation. Benchmarks: for each algorithm, compare: (i) naive classical (numpy), (ii) your SQ-inspired library, (iii) simulated quantum algorithm (PennyLane statevector). Datasets: synthetic matrices with controlled rank and condition number (k=5, κ∈{5,50,500}, N∈{64,256,1024}). Deliverables: documented Python package (hosted on GitHub), a benchmark report showing runtime and accuracy comparisons on all datasets, and a 15-page theoretical analysis explaining precisely where the SQ classical algorithm falls short of the quantum algorithm and what additional hardware capability (beyond SQ access) would restore the quantum advantage.

**Project 10.C — Quantum Transfer Learning on Real-World Image Data: Hardware Deployment:**

Implement and deploy a complete quantum transfer learning system for binary image classification on real IBM Quantum hardware. Choose a classification task from one of: (A) Ants vs Bees (the Mari et al. 2020 benchmark, 245 training images), (B) COVID vs Normal chest X-rays (subset of 300 images), or (C) a satellite image dataset of two terrain types (forest vs urban) from the EuroSAT dataset. Pipeline: (a) Classical backbone: ResNet-18 pre-trained on ImageNet, frozen; extract 512-dimensional feature vectors for all images. (b) Classical reducer: trainable linear 512→4. (c) Quantum head: 4-qubit PQC using AngleEmbedding + 3 StronglyEntanglingLayers, implemented in PennyLane + Qiskit backend. Training: (d) Train entirely on PennyLane default.qubit simulator (noiseless) for 50 epochs. (e) Deploy on IBM Quantum hardware (ibm\_brisbane or equivalent): evaluate test set accuracy with 2048 shots per circuit. (f) Apply measurement error mitigation (mthree or Qiskit Runtime M3) and ZNE (Zero Noise Extrapolation) and report accuracy improvements. (g) Compare with: classical ResNet-18 + linear head; classical ResNet-18 + MLP head (same parameter count as quantum); QSVM with 4-qubit ZZFeatureMap. Hardware analysis: (h) Report qubit connectivity, transpiled circuit depth, CNOT count after transpilation, and device calibration data (T1, T2, gate fidelities) at time of experiment. (i) Model the relationship between noise level and accuracy using the depolarising noise model. Write a 20-page report including your experimental design, full results with confidence intervals (from 5 independent hardware runs), noise analysis, and a critical assessment of whether the quantum head provides measurable value beyond a classical head of equivalent size.
