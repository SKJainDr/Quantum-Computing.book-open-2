# CHAPTER 1

# Shor's Algorithm, Amplitude Amplification & HHL

## 1.1 Shor's Factoring Algorithm — Complete Treatment

Integer factorisation — the problem of finding the prime factors p and q of a composite number N = pq — is the foundation of RSA public-key cryptography. For small N it is trivial: 15 = 3×5, 21 = 3×7. But as N grows, every known classical algorithm requires time that grows sub-exponentially (for the best classical algorithm, the General Number Field Sieve) or worse. Shor's quantum algorithm solves factoring in polynomial time O((log N)³), an exponential separation from the best classical method.

### 1.1.1 RSA Cryptography and the Hardness of Factoring

The RSA cryptosystem (Rivest, Shamir, Adleman, 1977) is the most widely deployed public-key protocol in history. Its security rests entirely on the apparent computational difficulty of factoring large integers. RSA key generation works as follows:

(1)  Choose two large random primes p and q, each approximately 1024 bits.

(2)  Compute N = p × q (the public modulus, ~2048 bits) and φ(N) = (p−1)(q−1).

(3)  Choose e coprime to φ(N): typically e = 65537 (public exponent).

(4)  Compute d = e⁻¹ mod φ(N) using the extended Euclidean algorithm (private key).

(5)  Public key: (N, e). Private key: d.

(6)  Encryption: C = Mᵉ mod N.  Decryption: M = Cᵈ mod N.

The security of RSA rests on the hardness of recovering p and q from N alone. The General Number Field Sieve (GNFS) — the best classical factoring algorithm — runs in sub-exponential time: exp(c · n^(1/3) · (ln n)^(2/3)) where n = log₂N and c ≈ 1.923. For RSA-2048 (n = 2048), this amounts to approximately 10⁴¹ elementary operations — roughly 10¹⁵ times the age of the universe on the world's fastest supercomputer.

<div class="box box-generic">
<p class="box-title"><strong>GNFS Classical Complexity vs Shor's Quantum Algorithm</strong></p>
<p><strong><em>Classical GNFS: T ∝ exp(1.923 · n^(1/3) · (ln n)^(2/3))   where n = log₂N</em></strong></p>
<p><em>Shor's Algorithm: T = O(n³) quantum gates. Exponential quantum speedup over classical.</em></p>
</div>

### 1.1.2 Reduction of Factoring to Order-Finding

Shor's key classical insight is that the problem of factoring N can be efficiently reduced to the problem of finding the multiplicative order of an integer modulo N. The multiplicative order (or period) of a with respect to N is:

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  Definition: Multiplicative Order</strong></p>
<p><strong><em>ord_N(a) = r  ⟺  aʳ ≡ 1 (mod N),  gcd(a,N) = 1,  r = smallest such positive integer</em></strong></p>
<p><em>By Euler's theorem, r divides φ(N) = (p−1)(q−1). The function f(k) = aᵏ mod N is periodic with period r.</em></p>
</div>

Given r, the factors of N are extracted by the following number-theoretic argument. Since aʳ ≡ 1 (mod N), we have (aʳ/² )² ≡ 1 (mod N). Let x = aʳ/² mod N. Then x² − 1 = (x+1)(x−1) ≡ 0 (mod N = pq). If x ≢ ±1 (mod N), then each of the factors (x±1) shares exactly one of p or q with N, giving a non-trivial factorisation via GCD computation.

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  Key Concept: Why Order-Finding Enables Factoring</strong></p>
<p>The sequence aⁱ mod N for i = 0, 1, 2, ... is periodic with period r (the order of a mod N).</p>
<p>If r is even: let y = aʳ/². Then y² ≡ 1 (mod N), so y is a non-trivial square root of 1 modulo N = pq.</p>
<p>Over the integers, √1 = ±1. Modulo N = pq, there are four square roots: ±1 and two non-trivial ones ±y₀.</p>
<p>The non-trivial square roots satisfy gcd(y₀ ± 1, N) = p or q — giving the complete factorisation.</p>
<p>The quantum computer finds r in O(n³) gates. All other steps (GCD, continued fractions) are classical O(n²).</p>
<p>Success probability per run: at least 1/2 when r is even (true for ≥ 3/4 of all a coprime to N).</p>
</div>

<div class="box box-example">
<p class="box-title"><strong>Example 1.1:</strong> Order-Finding by Inspection for N = 15, a = 7</p>
<p><strong>Problem:</strong> Compute the order r = ord₁₅(7) directly, then use it to factor N = 15.</p>
<p><strong>Solution:</strong></p>
<p>7¹ = 7 (mod 15)</p>
<p>7² = 49 = 3×15 + 4 ≡ 4 (mod 15)</p>
<p>7³ = 7×4 = 28 = 15 + 13 ≡ 13 (mod 15)</p>
<p>7⁴ = 7×13 = 91 = 6×15 + 1 ≡ 1 (mod 15)  ← period found!</p>
<p>Therefore r = ord₁₅(7) = 4.</p>
<p>Post-processing: r = 4 (even). Compute x = 7^(4/2) mod 15 = 49 mod 15 = 4.</p>
<p>gcd(x+1, N) = gcd(5, 15) = 5  ✓</p>
<p>gcd(x−1, N) = gcd(3, 15) = 3  ✓</p>
<p>Factors: p = 5, q = 3. Verification: 5 × 3 = 15  ✓</p>
</div>

### 1.1.3 Quantum Order-Finding via QPE: Modular Exponentiation Circuit

The quantum speedup in Shor's algorithm comes from Quantum Phase Estimation (QPE) applied to the modular exponentiation unitary U\_a. This operator acts on a work register of n qubits (enough to represent integers mod N):

<div class="box box-generic">
<p class="box-title"><strong>Modular Exponentiation Oracle U_a</strong></p>
<p><strong><em>U_a |x⟩ = |ax mod N⟩   for   x ∈ {0, 1, ..., N−1}</em></strong></p>
<p><em>U_a is a permutation matrix (unitary). Its eigenstates |u_s⟩ encode the order r in their eigenphases.</em></p>
</div>

The eigenstates of U\_a are the r states |u\_s⟩, one for each s = 0, 1, ..., r−1:

<div class="box box-generic">
<p class="box-title"><strong>Eigenstates of U_a and Their Phases</strong></p>
<p><strong><em>|u_s⟩ = (1/√r) Σ_{k=0}^{r−1} e^{−2πisk/r} |aᵏ mod N⟩,   eigenvalue: e^{2πis/r}</em></strong></p>
<p><em>QPE on |u_s⟩ measures s/r in the clock register. Crucially, (1/√r) Σ_s |u_s⟩ = |1⟩, so starting in |1⟩ samples uniformly over all s.</em></p>
</div>

The QPE circuit applies controlled-U\_a^(2^k) for k = 0, 1, …, t−1 using t clock qubits, then applies the inverse Quantum Fourier Transform (QFT†) to extract the phase s/r. The full circuit structure requires:

● t clock qubits: t = 2n + O(log n) for reliable period recovery via continued fractions.

● n work qubits: to store values aᵏ mod N (needs at most 2n+3 qubits with optimised encoding).

● Controlled-U\_a^(2^k) gates: the most expensive component, implemented via repeated modular squaring.

● Inverse QFT on the t clock qubits: O(t²) gates.

<figure class="book-figure">
<img src="content/images/image2.png" alt="">
<figcaption></figcaption>
</figure>

The modular exponentiation step dominates the gate count. Computing aˣ mod N for an n-bit N requires O(n²) modular multiplications, each requiring O(n²) elementary gates. Total: O(n⁴) naive, reduced to O(n³) with Beauregard's 2003 optimised circuit using only 2n+3 qubits.

### 1.1.4 Period Finding with QFT: Continued Fractions Recovery

The QPE measurement yields a t-bit integer y that is close to s × 2^t / r for some random eigenstate index s ∈ {0, …, r−1}. From this measurement, we must recover the period r. The classical continued fractions algorithm solves this precisely.

The measured value y satisfies |y/2^t − s/r| ≤ 1/2^(t+1). We want to find the rational s/r from its decimal approximation y/2^t. The key theorem from number theory states: if |y/2^t − s/r| ≤ 1/(2r²) and gcd(s,r) = 1, then s/r is uniquely identified as a convergent of the continued fraction expansion of y/2^t.

<div class="box box-generic">
<p class="box-title"><strong>Continued Fractions Recovery Theorem</strong></p>
<p><strong><em>Given y/2^t close to s/r: compute convergents [a₀; a₁, ..., aₖ] of y/2^t.</em></strong></p>
<p><em>The convergent with denominator ≤ N closest to y/2^t gives r. Requires t ≥ 2 log₂N + 1 clock qubits. Classical post-processing: O(t²) time.</em></p>
</div>

<div class="box box-example">
<p class="box-title"><strong>Example 1.2:</strong> QPE Output and Period Recovery for N = 15, a = 7</p>
<p><strong>Problem:</strong> With t = 8 clock qubits and r = 4, compute all possible QPE outputs and recover r.</p>
<p><strong>Solution:</strong></p>
<p>Expected QPE outputs: y = s × 2⁸ / r = s × 64 for s = 0, 1, 2, 3.</p>
<p>s=0: y = 0.   Continued fraction: 0/256 = 0/1. Denominator 1 → trivial, retry.</p>
<p>s=1: y = 64.  CF(64/256) = CF(1/4) = [0; 4]. Convergent 1/4 → r = 4. ✓</p>
<p>s=2: y = 128. CF(128/256) = CF(1/2) = [0; 2]. Convergent 1/2 → denominator 2. Since 2 | r = 4, we test: 7² mod 15 = 4 ≠ 1. Try r = 4 (next multiple). ✓</p>
<p>s=3: y = 192. CF(192/256) = CF(3/4) = [0; 1, 3]. Convergent 3/4 → r = 4. ✓</p>
<p>Success for s ∈ {1, 2, 3}: probability 3/4. Expected number of QPE runs: 4/3 ≈ 1.33.</p>
<p>After recovering r = 4: gcd(7² + 1, 15) = gcd(50, 15) = 5; gcd(7² − 1, 15) = gcd(48, 15) = 3. ✓</p>
</div>

<figure class="book-figure">
<img src="content/images/image3.png" alt="">
<figcaption></figcaption>
</figure>

### 1.1.5 Shor's Complexity: O((log N)³) Quantum Gates vs Sub-exponential Classical

The total gate count for Shor's algorithm is O(n³) = O((log N)³), dominated by modular exponentiation. The comparison with classical GNFS is staggering. The table below shows the contrast across standard RSA key sizes:

| Key Size | GNFS Classical (ops) | Shor's Quantum (gates) | Speedup Ratio |
|---|---|---|---|
| RSA-512 | ≈10¹⁴ | ≈10⁸ | ~10⁶× |
| RSA-1024 | ≈10²² | ≈10⁹ | ~10¹³× |
| RSA-2048 | ≈10⁴¹ | ≈10¹⁰ | ~10³¹× |
| RSA-4096 | ≈10⁶² | ≈10¹¹ | ~10⁵¹× |

<div class="box box-example">
<p class="box-title"><strong>Example 1.3:</strong> Shor's Algorithm: Quantitative Time Comparison for RSA-2048</p>
<p><strong>Problem:</strong> Estimate the time to factor RSA-2048 (a) classically using GNFS and (b) using Shor's algorithm on a fault-tolerant quantum computer.</p>
<p><strong>Solution:</strong></p>
<p>(a) Classical GNFS for n = 2048:</p>
<p>Exponent = 1.923 × 2048^(1/3) × (ln 2048)^(2/3) = 1.923 × 12.70 × 3.87 ≈ 94.6</p>
<p>Operations ≈ e^(94.6) ≈ 10^41.</p>
<p>At 10^18 ops/second (best supercomputer): ≈ 10^23 seconds ≈ 3×10^15 years.</p>
<p>(b) Shor's algorithm for n = 2048:</p>
<p>Gate count ≈ 72n³ ≈ 72 × 2048³ ≈ 6×10^11 gates (optimised Beauregard circuit).</p>
<p>Physical qubits required: ~20 million (with surface code at 0.1% error rate).</p>
<p>At 10^6 logical gates/second: ≈ 6×10^5 seconds ≈ 7 days.</p>
<p>At 10^9 logical gates/second (future hardware): ≈ 600 seconds ≈ 10 minutes.</p>
<p>Speedup: From 3×10^15 years → 10 minutes is a factor of ~10^26.</p>
</div>

### 1.1.6 Qiskit Implementation for Small N (15, 21, 35)

For small N such as 15, 21, and 35, the modular exponentiation circuit can be pre-compiled to a hand-optimised sequence of standard gates. The following complete Qiskit implementation demonstrates Shor's algorithm for N = 15:

```python
# Shor's Algorithm — Qiskit Implementation for N = 15, 21, 35
# Shor's Algorithm — Complete Qiskit Implementation for N = 15
from qiskit import QuantumCircuit, QuantumRegister, ClassicalRegister
from qiskit_aer import AerSimulator
from fractions import Fraction
from math import gcd, pi
import numpy as np

# ─── Modular multiplication oracle for a=7, N=15 ─────────────────────
# Pre-compiled: U_7 |x⟩ = |7x mod 15⟩ on 4-qubit work register
# Uses swap-based construction; exact circuit depends on a and N.

def c_amod15(a, power):
    """Controlled-Ua^power for N=15."""
    U = QuantumCircuit(4)
    for _ in range(power):
        if a in [2, 13]:
            U.swap(2, 3); U.swap(1, 2); U.swap(0, 1)
        elif a in [7, 8]:
            U.swap(0, 1); U.swap(1, 2); U.swap(2, 3)
        elif a in [4, 11]:
            U.swap(1, 3); U.swap(0, 2)
        elif a in [14, 1]:
            pass  # a=14: -1 mod 15; a=1: identity
    U = U.to_gate(); U.name = f"{a}^{power} mod 15"
    return U.control(1)

# ─── Inverse QFT ──────────────────────────────────────────────────────
def qft_dagger(qc, n):
    """Inverse QFT on first n qubits of circuit."""
    for qubit in range(n//2):
        qc.swap(qubit, n-qubit-1)
    for j in range(n):
        for m in range(j):
            qc.cp(-pi/float(2**(j-m)), m, j)
        qc.h(j)

# ─── Full Shor circuit ────────────────────────────────────────────────
def shor_circuit(a=7, n_count=8, N=15):
    """Build Shor's algorithm circuit."""
    qc = QuantumCircuit(n_count + 4, n_count)

    # Initialise: |0...0⟩_clock ⊗ |0001⟩_work = |1 mod 15⟩
    qc.x(n_count)  # work register LSB = 1

    # Step 1: Hadamard on all clock qubits
    for q in range(n_count):
        qc.h(q)

    # Step 2: Controlled-U^(2^j) gates
    for j in range(n_count):
        qc.append(c_amod15(a, 2**j), [j] + list(range(n_count, n_count+4)))

    # Step 3: Inverse QFT on clock register
    qft_dagger(qc, n_count)

    # Step 4: Measure clock register
    qc.measure(range(n_count), range(n_count))
    return qc

# ─── Run and extract factors ──────────────────────────────────────────
def shor_factor(N=15, a=7, shots=2048):
    """Run Shor's algorithm and return factors."""
    qc = shor_circuit(a=a, n_count=8, N=N)
    sim = AerSimulator()
    job = sim.run(qc, shots=shots)
    counts = job.result().get_counts()

    factors = set()
    for bitstring in counts:
        y = int(bitstring, 2)
        phase = y / 256  # 2^8 = 256
        frac = Fraction(phase).limit_denominator(N)
        r = frac.denominator
        if r % 2 != 0: continue
        for k in [gcd(a**(r//2)+1, N), gcd(a**(r//2)-1, N)]:
            if 1 < k < N:
                factors.add(k)

    print(f"N={N}, a={a}: Factors found = {factors}")
    return factors

# Test for N=15, 21, 35
for N, a in [(15, 7), (21, 2), (35, 3)]:
    shor_factor(N, a)
```

### 1.1.7 Post-Quantum Cryptography: CRYSTALS-Kyber and CRYSTALS-Dilithium

The threat from Shor's algorithm has driven an international effort to standardise cryptographic algorithms that are secure against quantum adversaries. In 2022–2024, NIST finalised four post-quantum cryptographic standards, all based on mathematical problems believed to be hard for quantum computers:

**CRYSTALS-Kyber (ML-KEM, FIPS 203):** Security basis: Module Lattice Learning With Errors (MLWE). Use: Key encapsulation — replaces RSA/DH for key exchange.

**CRYSTALS-Dilithium (ML-DSA, FIPS 204):** Security basis: Module Lattice Short Integer Solution. Use: Digital signatures — replaces RSA/ECDSA signatures.

**SPHINCS+ (SLH-DSA, FIPS 205):** Security basis: Hash-based signatures (SHA-3 family). Use: Conservative alternative digital signatures.

**FALCON (FN-DSA, FIPS 206):** Security basis: NTRU lattice (Ring-LWE variant). Use: Compact digital signatures for bandwidth-limited contexts.

<div class="box box-real-world">
<p class="box-title"><strong>🌐  Real World: Post-Quantum Migration — A Global Engineering Challenge</strong></p>
<p>"Harvest now, decrypt later" attacks: intelligence agencies are believed to be storing encrypted traffic today, planning to decrypt it once quantum computers mature. Data with long-term sensitivity (medical records, state secrets, financial instruments) is already at risk from future quantum decryption.</p>
<p>NIST PQC migration timeline: US federal agencies must inventory cryptographic assets by 2025 and complete migration to approved PQC algorithms by 2035 (CISA mandate). The banking sector, telecoms, and cloud providers are accelerating their own timelines.</p>
<p>India's NQM and cryptographic sovereignty: India's National Quantum Mission allocates specific funding for indigenous PQC research, testing of CRYSTALS-Kyber/Dilithium in e-Governance PKI, and training cryptographic engineers. IIT Bombay, IIT Madras, and C-DAC are leading implementation efforts. India's CERT-In has published PQC migration guidelines for critical infrastructure.</p>
<p>Performance overhead: Kyber-1024 key generation is ~50× faster than RSA-2048 key generation; Kyber ciphertext is 1568 bytes vs 256 bytes for RSA-2048. The main practical challenge is retrofitting existing protocols (TLS, SSH, S/MIME) to support PQC algorithms alongside classical ones.</p>
<p>Hybrid key exchange: The recommended transition strategy is to use both classical (X25519 ECDH) and post-quantum (Kyber) simultaneously. Even if one algorithm is broken, the other provides protection. Google, Mozilla, and Apple have deployed hybrid TLS in their browsers.</p>
</div>

<div class="box box-warning">
<p class="box-title"><strong>⚠  Warning: Shor's Algorithm Does NOT Break Symmetric Cryptography</strong></p>
<p>Shor's algorithm exploits multiplicative periodicity — a mathematical structure specific to RSA (based on integer factoring) and ECC/Diffie-Hellman (based on discrete logarithms). It provides no speedup for symmetric ciphers like AES or hash functions like SHA-256.</p>
<p>Grover's algorithm does give a quadratic speedup against symmetric keys, effectively halving the key length from a security perspective. AES-128 → 64-bit quantum security (insecure). AES-256 → 128-bit quantum security (still secure post-quantum).</p>
<p>The correct post-quantum response for symmetric systems is to double key lengths (AES-128 → AES-256, SHA-256 → SHA-512), NOT to replace the algorithms entirely. This is far simpler than the public-key migration.</p>
<p>Many popular accounts claim 'quantum computers break all encryption'. This is misleading. The threat is specifically to public-key cryptography (RSA, ECC, DH). A quantum computer running Shor's algorithm cannot read your AES-256 encrypted hard drive.</p>
</div>

## 1.2 Amplitude Amplification and Quantum Walks

Grover's 1996 quantum search algorithm demonstrated that quantum computers can search an unsorted database of N items in O(√N) steps rather than O(N) classically. But Grover's algorithm is a special case of a vastly more general quantum tool: generalised amplitude amplification. This section develops the complete theory and connects it to the important and growing family of quantum walk algorithms.

### 1.2.1 Generalised Amplitude Amplification

Let A be any quantum unitary that prepares a state from |0⟩. Let χ be a Boolean function that identifies "good" target states. The generalised amplitude amplification framework (Brassard, Hoyer, Mosca, Tapp 2002) defines the Grover iterate Q:

<div class="box box-generic">
<p class="box-title"><strong>Amplitude Amplification Operators</strong></p>
<p><strong><em>S_χ = I − 2 Σ_{x:χ(x)=1} |x⟩⟨x|   (phase oracle: marks good states with −1)</em></strong></p>
<p><em>S₀ = I − 2|0⟩⟨0|   (reflection about |0⟩)</em></p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Grover Iterate Q (Amplitude Amplification Operator)</strong></p>
<p><strong><em>Q = −A S₀ A⁻¹ S_χ</em></strong></p>
<p><em>One application of Q rotates the state vector by angle 2θ in the {|good⟩, |bad⟩} 2-dimensional subspace, where sin²(θ) = a = initial success probability Pr[good].</em></p>
</div>

The amplitude amplification theorem states: starting from A|0⟩ with success probability a, after k applications of Q the state is:

<div class="box box-generic">
<p class="box-title"><strong>Amplitude Amplification Theorem</strong></p>
<p><strong><em>Q^k A|0⟩ = sin((2k+1)θ)|good⟩ + cos((2k+1)θ)|bad⟩,   sin(θ) = √a</em></strong></p>
<p><em>Success probability: P_k = sin²((2k+1)θ). Optimal iterations: k_opt = ⌊π/(4θ) − 1/2⌋ ≈ ⌊π/(4√a)⌋. Then P_{k_opt} ≥ 1 − a.</em></p>
</div>

<div class="box box-example">
<p class="box-title"><strong>Example 1.4:</strong> Grover's Algorithm as Special Case of Amplitude Amplification</p>
<p><strong>Problem:</strong> Verify that standard Grover's search (N=16, M=1 target, A = H⊗⁴) is a special case of amplitude amplification with a = 1/N.</p>
<p><strong>Solution:</strong></p>
<p>Initial state: A|0⟩ = H⊗⁴|0000⟩ = (1/4) Σ_{x=0}^{15} |x⟩.</p>
<p>Success probability: a = |⟨target|A|0⟩|² = (1/4)² = 1/16.</p>
<p>θ = arcsin(√(1/16)) = arcsin(1/4) ≈ 0.2527 radians.</p>
<p>Optimal iterations: k_opt = ⌊π/(4×0.2527) − 0.5⌋ = ⌊3.10 − 0.5⌋ = ⌊2.60⌋ = 2.</p>
<p>Wait, recompute: k_opt = round(π/(4θ)) = round(π/(4×0.2527)) = round(3.10) = 3.</p>
<p>P₃ = sin²(7×0.2527) = sin²(1.769) ≈ 0.9785 ≈ 97.9%.</p>
<p>General formula: for M marked states out of N total, k_opt ≈ (π/4)√(N/M). For N=16, M=1: k_opt ≈ π ≈ 3. ✓</p>
<p>The operator Q = −H⊗ⁿ S₀ H⊗ⁿ S_χ exactly matches the standard Grover iterate. ✓</p>
</div>

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  Key Concept: Amplitude Amplification as a Universal Quantum Subroutine</strong></p>
<p>Standard Grover search: A = H^⊗n, χ = indicator of one target element → O(√N) queries.</p>
<p>Quantum counting: Run QPE on Q to estimate θ, giving M (number of solutions) to precision ε in O(√(N/M)/ε) queries.</p>
<p>Amplitude estimation: Estimate a = sin²(θ) to precision ε in O(1/ε) queries — quadratic improvement over classical Monte Carlo which needs O(1/ε²) samples.</p>
<p>Exponential speedup composition: If A already runs a subproblem in time T with success probability a, amplitude amplification wraps it to succeed with probability 1 in time O(T/√a), saving a √(1/a) factor overall.</p>
<p>QRAM-based search: A loads a data item from quantum memory; amplitude amplification finds the item satisfying a predicate. Foundational for near-term quantum database applications.</p>
</div>

<figure class="book-figure">
<img src="content/images/image4.png" alt="">
<figcaption></figcaption>
</figure>

### 1.2.2 Quantum Walk on Graphs: Coined and Continuous-Time

A quantum walk is the quantum mechanical analogue of a classical random walk on a graph. The fundamental difference: while a classical random walker maintains a probability distribution over vertices, a quantum walker maintains a probability amplitude, enabling interference effects that produce qualitatively different — and often faster — exploration behaviour.

#### Coined Quantum Walk

The coined quantum walk on a graph G = (V, E) operates in a Hilbert space H\_P ⊗ H\_C where H\_P = span{|v⟩ : v ∈ V} is the position space and H\_C is the coin space (recording direction). One step of the walk applies two operators:

<div class="box box-generic">
<p class="box-title"><strong>Coined Quantum Walk Step</strong></p>
<p><strong><em>W = S · (I_P ⊗ C)</em></strong></p>
<p><em>Coin C: applies a unitary transformation (e.g., Hadamard H or Grover coin G = 2|+⟩⟨+|−I) to the coin register at each vertex. Shift S: moves the walker along each edge: S|v, (v,u)⟩ = |u, (u,v)⟩. After t steps: state = Wᵗ|initial⟩.</em></p>
</div>

The coined quantum walk on a line with Hadamard coin was one of the first quantum walk models studied. Starting from vertex 0 with coin |+⟩, the probability distribution after t steps is bimodal (peaked near ±t/√2) rather than Gaussian. The standard deviation grows as σ ∝ t (ballistic), not σ ∝ √t (diffusive) as in classical random walks. This quadratic difference in spreading speed is the source of quantum walk's advantage.

#### Continuous-Time Quantum Walk

The continuous-time quantum walk (CTQW, Farhi & Gutmann 1998) on a graph G with adjacency matrix A evolves under the Schrödinger equation with the adjacency matrix as the Hamiltonian:

<div class="box box-generic">
<p class="box-title"><strong>Continuous-Time Quantum Walk (CTQW)</strong></p>
<p><strong><em>i d/dt |ψ(t)⟩ = A|ψ(t)⟩   ⟹   |ψ(t)⟩ = e^{−iAt}|ψ(0)⟩</em></strong></p>
<p><em>No coin register needed. Evolution driven directly by graph structure. Eigenvalues of A determine oscillation frequencies. Interference at target vertices creates search amplification.</em></p>
</div>

<figure class="book-figure">
<img src="content/images/image5.png" alt="">
<figcaption></figcaption>
</figure>

<div class="box box-example">
<p class="box-title"><strong>Example 1.5:</strong> Coined Quantum Walk on C₄ (4-Cycle): Probability Distribution</p>
<p><strong>Problem:</strong> Analyse one step of the Hadamard-coin quantum walk on the 4-cycle starting from |0,↑⟩.</p>
<p><strong>Solution:</strong></p>
<p>State space: {|v⟩ : v=0,1,2,3} ⊗ {|↑⟩, |↓⟩}. Coin: H on coin register.</p>
<p>Initial state: |ψ₀⟩ = |0⟩|↑⟩.</p>
<p>Coin step: C|ψ₀⟩ = |0⟩ ⊗ H|↑⟩ = |0⟩ ⊗ (|↑⟩+|↓⟩)/√2.</p>
<p>Shift step: S moves |v,↑⟩→|v+1 mod 4, ↑⟩ and |v,↓⟩→|v−1 mod 4, ↓⟩.</p>
<p>After 1 step: |ψ₁⟩ = (|1,↑⟩ + |3,↓⟩)/√2. Position distribution: P(0)=0, P(1)=1/2, P(2)=0, P(3)=1/2.</p>
<p>Classical walk: after 1 step from vertex 0, P(1)=1/2, P(3)=1/2 (same here, but distributions diverge after more steps).</p>
<p>After 2 steps: quantum interference creates |ψ₂⟩ = (|0,↑⟩ + |2,↑⟩ − |2,↓⟩ − |0,↓⟩)/2. P(0)=1/2, P(2)=1/2.</p>
<p>Key: quantum walk can stay on even/odd vertices alternately — interference creates pattern not seen classically.</p>
</div>

### 1.2.3 Quantum Walk Search Algorithm: O(√N) Queries on Well-Connected Graphs

The quantum walk search algorithm (Ambainis 2003; Childs and Goldstone 2004; Szegedy 2004) achieves O(√N) query complexity on well-connected graphs using quantum walk dynamics rather than oracle reflection. The algorithm applies to any graph G where the classical random walk has a small hitting time.

Szegedy's quantum walk framework (2004) provides the most general version: given a classical Markov chain with transition matrix P, define a quantum walk on the bipartite double cover of G. The quantum walk naturally implements amplitude amplification over the classical walk's stationary distribution.

<div class="box box-generic">
<p class="box-title"><strong>Quantum Walk Search Complexity (Ambainis-Szegedy Framework)</strong></p>
<p><strong><em>Query complexity: O(√(HT)) where HT is the classical hitting time of the random walk</em></strong></p>
<p><em>For an s-sparse d-regular graph on N vertices with spectral gap δ: HT = O(N/δ). Quantum walk search: O(√(N/δ)). Complete graph K_N: δ=1, search in O(√N). 2D grid: δ=O(1/N), HT=O(N log N), quantum: O(√(N log N)).</em></p>
</div>

<div class="box box-example">
<p class="box-title"><strong>Example 1.6:</strong> Quantum Walk Search on a Complete Graph K_N</p>
<p><strong>Problem:</strong> Estimate the query complexity of quantum walk search for a marked vertex on K_N (complete graph, N=1000).</p>
<p><strong>Solution:</strong></p>
<p>Classical random walk on K_N: at each step, move to a random neighbour with probability 1/(N−1) ≈ 1/N.</p>
<p>Classical hitting time: HT(K_N) ≈ N = 1000. O(N) queries classically.</p>
<p>Spectral gap of K_N: δ = 1 (only eigenvalue λ=1 for the stationary distribution; all others are 0 for the Markov chain).</p>
<p>Quantum walk search: O(√(HT)) = O(√N) = O(√1000) ≈ O(32) queries.</p>
<p>Each query (quantum walk step): O(log N) = O(10) gates for an efficient circuit implementation.</p>
<p>Total gate complexity: O(32 × 10) = O(320) gates vs O(1000) classical queries.</p>
<p>Speedup: √N ≈ 31.6× for N=1000; grows without bound as N increases.</p>
<p>Note: same O(√N) asymptotic as Grover, but mechanistically different — local walk dynamics vs global oracle reflection.</p>
</div>

### 1.2.4 Quantum Walk for Element Distinctness: O(N^{2/3}) — Better than Grover

The element distinctness problem (ED): given N elements x₁, …, x\_N accessible through an oracle, do any two elements have equal values? This is a collision-finding problem. Classically, Ω(N) queries are required in the worst case. Grover's algorithm gives O(√(N²)) = O(N) queries (treating all pairs as the search space) — no improvement. However, Ambainis (2004) constructed a quantum walk algorithm achieving O(N^{2/3}) queries: a provably better-than-Grover speedup for a natural problem.

<div class="box box-generic">
<p class="box-title"><strong>Element Distinctness: O(N^{2/3}) via Johnson Graph Quantum Walk</strong></p>
<p><strong><em>Algorithm runs on Johnson graph J(N, r) with r = N^{2/3}: vertices = r-subsets S ⊆ [N], edges connect subsets differing by 1 element.</em></strong></p>
<p><em>Setup: O(N^{2/3}) queries to load S. Walk: O(N^{1/6}) steps of O(N^{1/3}) each = O(N^{1/2}) total. Detection + amplification: O(N^{2/3}) overall. Proven optimal: Ω(N^{2/3}) quantum lower bound (Ambainis 2005).</em></p>
</div>

The three-stage structure of quantum walk algorithms — Setup (prepare an r-subset), Walk (update the subset by replacing one element), and Check (detect a collision) — generalises to many combinatorial problems:

**● Element Distinctness:** O(N^{2/3}). Find equal pair in N elements.

**● Triangle Finding:** O(N^{10/7}). Find triangle in N-vertex graph (Le Gall 2014).

**● Matrix Product Verification:** O(N^{5/3}). Verify A·B=C for n×n matrices.

**● Group Commutativity:** O(N^{2/3}). Test if group of N elements is abelian.

<figure class="book-figure">
<img src="content/images/image6.png" alt="">
<figcaption></figcaption>
</figure>

## 1.3 Linear Systems — HHL Algorithm

Solving a system of linear equations Ax = b is one of the most fundamental computational problems in science and engineering, appearing in machine learning, fluid dynamics, structural analysis, circuit simulation, and numerical methods for differential equations. Classically, Gaussian elimination runs in O(N³) for N×N dense matrices. In 2009, Harrow, Hassidim, and Lloyd (HHL) showed that quantum computers can solve sparse, well-conditioned linear systems in O(log N) — an exponential improvement — subject to specific input and output conditions.

<div class="box box-anecdote">
<p class="box-title"><strong>📜  HHL — The Algorithm That Sparked a Debate</strong></p>
<p>The 2009 HHL paper appeared in Physical Review Letters and generated enormous excitement: here was an exponential quantum speedup for a problem of vast practical importance, beyond the narrow domain of period-finding. The paper rapidly accumulated thousands of citations and spawned a wave of proposed quantum machine learning algorithms built on HHL as a subroutine.</p>
<p>A decade later, the picture is more nuanced. In 2019, Ewin Tang — then an undergraduate at UT Austin — proved that classical algorithms using "quantum-inspired" sampling techniques could match HHL's performance in the regimes where HHL's input conditions are efficiently satisfiable. This "dequantisation" result, building on work by Tang, Raz, and others, showed that many proposed quantum ML speedups were illusory: the quantum algorithm's advantage relied on conditions (efficient quantum state preparation) that were not achievable in the applications claimed.</p>
<p>HHL remains a landmark result: it proved that quantum speedup extends beyond algebraic periodicity. But understanding its true range of applicability requires careful analysis of the input preparation problem, the output measurement problem, the condition number, and the sparsity constraints.</p>
</div>

### 1.3.1 Problem Setting: Classical O(N³) vs Quantum O(log N)

Given an N×N Hermitian matrix A and vector b (encoded as a quantum state |b⟩), find |x⟩ ∝ A⁻¹|b⟩. The complexity comparison between classical and quantum is:

| Method | Complexity | Requirements | Notes |
|---|---|---|---|
| Gaussian Elim. | O(N³) | Dense A | Full classical solution |
| CG / Krylov | O(N·s·κ·log(1/ε)) | Sparse, SPD A | s = nnz/row, κ = cond. no. |
| Classical Sampling | O(N·poly(κ,s)) | QRAM-like access | Tang 2019 dequantisation |
| HHL Quantum | O(log(N)·κ²·s) | Sparse, κ=poly(log N), quantum I/O | All caveats apply |

### 1.3.2 HHL Circuit: QPE for Eigenvalue Estimation, Conditional Rotation, Uncomputation

The HHL algorithm proceeds in three stages, each represented by a distinct circuit block. The quantum state at each stage is as follows:

Stage 1 — State Preparation and QPE: Prepare the b-register in state |b⟩ = Σⱼ βⱼ|uⱼ⟩ (decomposed in eigenbasis of A). Apply Quantum Phase Estimation to the b-register using A as the evolution operator, estimating eigenvalues λⱼ in the clock register:

<div class="box box-generic">
<p class="box-title"><strong>HHL Stage 1: After QPE</strong></p>
<p><strong><em>Σⱼ βⱼ |λⱼ⟩_clock |uⱼ⟩_b |0⟩_ancilla</em></strong></p>
<p><em>Clock register holds binary approximation of eigenvalue λⱼ. |uⱼ⟩ is the j-th eigenstate of A.</em></p>
</div>

Stage 2 — Conditional Rotation: Rotate the ancilla qubit conditioned on the clock register value λⱼ:

<div class="box box-generic">
<p class="box-title"><strong>HHL Stage 2: Conditional Rotation</strong></p>
<p><strong><em>Σⱼ βⱼ |λⱼ⟩ |uⱼ⟩ (√(1−C²/λⱼ²)|0⟩ + C/λⱼ |1⟩)</em></strong></p>
<p><em>C is a normalisation constant (C ≤ min|λⱼ|). Rotation angle: arcsin(C/λⱼ). The 1/λⱼ amplitude encodes the matrix inversion!</em></p>
</div>

Stage 3 — Uncomputation and Post-selection: Apply inverse QPE to uncompute the clock register. Post-select on measuring |1⟩ in the ancilla qubit, which collapses the state to the solution:

<div class="box box-generic">
<p class="box-title"><strong>HHL Stage 3: Output State (post-selected on ancilla = 1)</strong></p>
<p><strong><em>|x⟩ ∝ Σⱼ βⱼ/λⱼ |uⱼ⟩ = A⁻¹|b⟩ / ||A⁻¹|b⟩||</em></strong></p>
<p><em>Post-selection probability: Σⱼ |βⱼ|²C²/λⱼ² ∝ ||A⁻¹|b⟩||² / (N·κ²). For small eigenvalues, this probability is very small.</em></p>
</div>

<figure class="book-figure">
<img src="content/images/image7.png" alt="">
<figcaption></figcaption>
</figure>

<div class="box box-example">
<p class="box-title"><strong>Example 1.7:</strong> HHL for a 2×2 System: Full Worked Example</p>
<p><strong>Problem:</strong> Solve Ax = b with A = (3/2)I + (1/2)X = [[3/2, 1/2],[1/2, 3/2]] and |b⟩ = |0⟩.</p>
<p><strong>Solution:</strong></p>
<p>Eigenvalues of A: λ₁ = 2, λ₂ = 1.</p>
<p>Eigenvectors: |u₁⟩ = (|0⟩+|1⟩)/√2 = |+⟩, |u₂⟩ = (|0⟩−|1⟩)/√2 = |−⟩.</p>
<p>Decompose |b⟩ = |0⟩ = (|+⟩ + |−⟩)/√2. So β₁ = β₂ = 1/√2.</p>
<p>HHL output: |x⟩ ∝ β₁/λ₁ |u₁⟩ + β₂/λ₂ |u₂⟩ = (1/√2)(1/2|+⟩ + 1|−⟩).</p>
<p>= (1/√2)[(1/2)(|0⟩+|1⟩)/√2 + (|0⟩−|1⟩)/√2]</p>
<p>= (1/2)[|0⟩(1/2+1) + |1⟩(1/2−1)] = (1/4)·3|0⟩ + (1/4)·(−1)|1⟩  ∝  (3, −1).</p>
<p>Classical solution: A⁻¹|0⟩ = [[3/4, −1/4],[−1/4, 3/4]][1,0]ᵀ = [3/4, −1/4]ᵀ ∝ (3, −1). ✓</p>
<p>Note: HHL gives the normalised solution state; to get the exact numerical values, one would need to tomograph |x⟩ (which requires O(N) measurements, erasing the speedup in general).</p>
</div>

### 1.3.3 Caveats: Input Preparation, Output Measurement, Sparsity and Condition Number

The O(log N) speedup of HHL is subject to four fundamental caveats, each of which can negate the advantage in specific applications. A critical evaluation of any claimed HHL-based speedup must address all four:

**Caveat 1: Input Preparation Problem.** Loading |b⟩ into a quantum register requires O(N) classical operations in general. If the b-vector must be read from classical memory, this preparation cost dominates and the O(log N) speedup is entirely erased. The only way to avoid this is with QRAM (Quantum Random Access Memory), which can prepare |b⟩ in O(polylog N) time but does not yet exist at scale.

**Caveat 2: Output Measurement Problem.** The quantum output |x⟩ is a quantum state encoding the solution. Reading out all N entries of x classically requires O(N) measurements (quantum state tomography). If only a single linear functional of x is needed (e.g., ⟨w|x⟩ for a known vector w), this can be estimated in O(1) measurements. But for most practical applications, the full solution vector is needed, restoring O(N) cost.

**Caveat 3: Sparsity Requirement.** HHL complexity scales as O(s) where s is the maximum number of non-zero entries per row (sparsity). For dense matrices (s = N), HHL offers no speedup. Most physical simulation matrices are sparse (finite-difference, finite-element, molecular Hamiltonians), but many machine learning and statistical matrices are dense.

**Caveat 4: Condition Number Requirement.** HHL complexity scales as O(κ²) where κ = λ\_max/λ\_min is the condition number. For ill-conditioned systems (κ = O(N) or worse), HHL provides no improvement over classical methods. Well-conditioned problems require κ = O(polylog N).

<div class="box box-warning">
<p class="box-title"><strong>⚠  Warning: The HHL Speedup Requires All Four Conditions Simultaneously</strong></p>
<p>The full O(log N) quantum speedup over classical O(N³) requires SIMULTANEOUSLY: (1) QRAM-based O(polylog N) state preparation; (2) only O(1) linear measurements of |x⟩ needed; (3) A is s-sparse with s = O(polylog N); and (4) κ = O(polylog N).</p>
<p>In most real-world applications, at least one of these fails. For example, in finance portfolio optimisation: the covariance matrix A is dense (condition 3 fails). In ML ridge regression: κ scales with the number of features (condition 4 can fail). In fluid dynamics: conditions 3 and 4 often hold, but condition 2 fails if the full velocity field is needed.</p>
<p>The dequantisation results (Tang 2019, Chia et al. 2020) showed that when conditions 1 and 2 hold (QRAM access + only a few linear measurements), classical algorithms can also run in poly(κ, s, log N) time using "quantum-inspired" classical sampling. The exponential separation survives only when the quantum computer can exploit all four conditions simultaneously in ways that classical algorithms cannot.</p>
<p>HHL is still a landmark theoretical result and may find future applications in domains like cryptanalysis, differential equation solving with sparse/well-conditioned systems, and quantum chemistry post-processing.</p>
</div>

### 1.3.4 Dequantisation Results: Classical Algorithms Matching HHL

Tang (2019) introduced the "sample-and-query" (SQ) classical model as a classical analogue of quantum state access. In this model, a classical algorithm can: (1) sample an index i from distribution |bᵢ|²/||b||², and (2) query any specific entry bᵢ in O(1) time. Under these access conditions — which are achievable when b is stored in a suitable classical data structure — Tang showed that classical algorithms can solve the same linear algebra problems as HHL in time poly(κ, s, log N), matching HHL's speedup.

The key implication: the exponential speedup of HHL (and many quantum ML algorithms built on it) may be an artefact of comparing quantum computation to classical algorithms without efficient data access, rather than a fundamental quantum advantage. The debate continues in the research community, with some HHL applications (particularly those with genuinely quantum input states from quantum sensors or other quantum algorithms) remaining potentially advantageous.

## RECAP — SHORT ANSWER QUESTIONS & MODEL ANSWERS

Chapter 1: Shor's Algorithm, Amplitude Amplification & HHL

Instructions: Answer each question in 3–6 lines. Each question carries equal marks.

**PART A — QUESTIONS**

**Q1.  What is the multiplicative order ord\_N(a), and why does Shor's algorithm reduce factoring to finding it?**

**Q2.  Describe the role of Quantum Phase Estimation in Shor's algorithm.**

**Q3.  Why is a continued-fractions expansion needed after the QPE measurement, and what precision does it require?**

**Q4.  Compare the classical (GNFS) and quantum (Shor) time complexities for factoring an n-bit number, and state the resulting speedup class.**

**Q5.  What are CRYSTALS-Kyber and CRYSTALS-Dilithium, and why do they matter for the post-quantum transition?**

**Q6.  What is generalised amplitude amplification, and how does it differ from Grover's original algorithm?**

**Q7.  Describe a quantum walk search algorithm and state its query complexity advantage over classical random walks.**

**Q8.  State the equation the HHL algorithm solves and its complexity advantage, along with its key caveats.**

**Q9.  Outline the HHL circuit structure at a high level.**

**Q10.  What is the first-order Lie-Trotter product formula and what is its error scaling?**

**Q11.  Why does the second-order (palindromic) Suzuki formula outperform first-order Trotter, and by how much?**

**Q12.  What is the UCCSD ansatz and what molecular systems is it applied to in VQE?**

**PART B — MODEL ANSWERS**

**Answer 1:**

The multiplicative order r = ord\_N(a) is the smallest positive integer such that a^r ≡ 1 (mod N). If r is even, then y = a^(r/2) mod N is a non-trivial square root of 1 modulo N, so N = pq divides y²−1 = (y+1)(y−1); computing gcd(y±1, N) then reveals the prime factors. This reduces the hard problem of factoring to the (quantum-tractable) problem of period-finding.

**Answer 2:**

QPE is applied to the modular exponentiation unitary U\_a|x⟩ = |ax mod N⟩, whose eigenstates |u\_s⟩ carry eigenphase s/r. Using t = 2n+O(log n) clock qubits and controlled-U\_a^(2^k) gates followed by an inverse QFT, QPE outputs a t-bit estimate y ≈ s·2^t/r for a random s. Classical continued-fractions post-processing then recovers r from y/2^t.

**Answer 3:**

The QPE measurement gives only an approximation y/2^t to the true fraction s/r; continued fractions are the standard number-theoretic tool for recovering the exact low-denominator fraction closest to a given decimal. Provided t ≥ 2log₂N + 1 clock qubits are used, the convergent of y/2^t with denominator ≤ N is guaranteed to equal s/r exactly (when gcd(s,r)=1), so classical O(t²) processing recovers r reliably.

**Answer 4:**

The General Number Field Sieve runs in sub-exponential time exp(1.923·n^(1/3)(ln n)^(2/3)), whereas Shor's algorithm uses only O(n³) quantum gates. For RSA-2048 (n=2048) this is roughly 10^41 classical operations versus about 10^10–10^11 quantum gates — an exponential quantum speedup, the most dramatic known algorithmic advantage of quantum over classical computation.

**Answer 5:**

They are NIST-standardised (2024) post-quantum cryptographic algorithms — Kyber for key encapsulation and Dilithium for digital signatures — based on lattice problems believed to be hard even for quantum computers. Because a sufficiently large fault-tolerant quantum computer running Shor's algorithm would break RSA and elliptic-curve cryptography, migrating global infrastructure (TLS, VPNs, digital certificates) to these quantum-safe standards is considered the largest cryptographic migration in history.

**Answer 6:**

Generalised amplitude amplification replaces Grover's assumption of an oracle acting on a uniform superposition with an arbitrary state-preparation operator A (not necessarily H^⊗n) and a general 'good-state' reflection S\_χ, combined with the reflection S₀ about A|0⟩ via Q = −A S₀ A⁻¹ S\_χ. It amplifies the amplitude of marked outcomes for ANY algorithm A that prepares a superposition with some success amplitude √a, generalising Grover search to arbitrary quantum subroutines.

**Answer 7:**

A quantum walk search algorithm builds a quantum analogue of a classical Markov chain by combining a coin operator and shift/walk operator W = S·(I\_P⊗C), then uses a quantum-walk version of amplitude amplification to find marked vertices. Its query complexity is O(√(HT)), the square root of the classical hitting time HT — a quadratic speedup over classical random walk search, generalising Grover's O(√N) to structured search spaces.

**Answer 8:**

HHL solves the linear system Ax = b, producing a quantum state |x⟩ proportional to the solution vector in time O(log N) for sparse, well-conditioned A — an exponential speedup over the classical O(N³). However, this speedup is conditional on four caveats: (1) efficient preparation of |b⟩, (2) A being sparse and well-conditioned (condition number κ enters the complexity), (3) only certain global properties of |x⟩ being efficiently extractable by measurement (not the full vector), and (4) dequantisation results showing classical sample-based algorithms can match HHL in some regimes.

**Answer 9:**

The HHL circuit (1) uses QPE with the unitary e^{iAt} to write the eigenvalues λ\_j of A into an ancilla clock register, entangling each eigencomponent |u\_j�REG with its eigenvalue; (2) applies a controlled rotation on an ancilla qubit with amplitude ∝ C/λ\_j, conditioned on the clock register; (3) uncomputes (inverse QPE) the clock register; and (4) postselects on the ancilla being |1⟩, leaving the work register in a state proportional to A⁻¹|b⟩ = |x⟩.

**Answer 10:**

The Lie product formula approximates e^{−i(A+B)t} by (e^{−iAt/r}e^{−iBt/r})^r for large r. For a Hamiltonian H = Σ\_k H\_k with L non-commuting terms, the error after r Trotter steps scales as O(t²/r)·Σ‖[H\_j,H\_k]‖, i.e. O(Δt²) error per step; achieving error ε requires r = O(L²t²/ε) steps and hence O(L³t²/ε) total gates.

**Answer 11:**

The palindromic arrangement S₂(Δt) = e^{−iH₁Δt/2}···e^{−iH\_LΔt/2}·e^{−iH\_LΔt/2}···e^{−iH₁Δt/2} causes the O(Δt²) commutator terms from the forward and backward halves to cancel exactly, leaving only O(Δt³) error per step. This reduces the number of steps needed for target error ε from O(1/ε) (first order) to O(1/√ε) (second order) — in a worked textbook example this represents roughly a 95× reduction in gate count for ε = 10⁻³.

**Answer 12:**

UCCSD (Unitary Coupled Cluster with Singles and Doubles) is a physically motivated variational ansatz e^{T−T†}|HF⟩, where T = T₁+T₂ generates single and double particle-hole excitations from the Hartree-Fock reference state, and the fermionic operators are mapped to qubits via the Jordan-Wigner transformation. It is applied in this chapter to compute the binding-energy curves of H₂ and LiH via VQE, benchmarked against CCSD(T) accuracy.

## A. Solved Problems

<div class="box box-solved-problem">
<p class="box-title"><strong>Solved Problem 1.1</strong></p>
<p><strong>Problem:</strong> Factor N = 35 using Shor's algorithm with a = 6. Find r = ord₃₅(6) by direct computation, then compute the non-trivial factors.</p>
<p><strong>Solution:</strong></p>
<p>6¹ = 6 (mod 35)</p>
<p>6² = 36 ≡ 1 (mod 35).  Period found: r = ord₃₅(6) = 2.</p>
<p>r = 2 is even.  x = 6^(2/2) mod 35 = 6¹ mod 35 = 6.</p>
<p>Compute GCDs:  gcd(x+1, N) = gcd(7, 35) = 7.  gcd(x−1, N) = gcd(5, 35) = 5.</p>
<p>Factors: p = 7, q = 5.  Verification: 7×5 = 35.  ✓</p>
<p>This is the simplest non-trivial order (r=2). The quantum speedup is most pronounced when r is large (O(N)), but for any r, the GCD post-processing is O(n²) classical.</p>
<p>Note: r=2 occurs when a ≡ −1 (mod p) or (mod q). Here 6 ≡ −1 (mod 7) since 7−1=6. ✓</p>
</div>

<div class="box box-solved-problem">
<p class="box-title"><strong>Solved Problem 1.2</strong></p>
<p><strong>Problem:</strong> A QPE circuit for Shor's algorithm uses t = 10 clock qubits on N = 15, a = 7 (known r = 4). What are the four possible measurement outcomes in decimal? What continued fraction recovers r from each?</p>
<p><strong>Solution:</strong></p>
<p>QPE output formula: y = ⌊s × 2^t / r⌋ for eigenstate index s = 0, 1, ..., r−1.</p>
<p>s=0: y = 0×1024/4 = 0.     CF(0/1024) = 0/1. Trivial denominator → retry.</p>
<p>s=1: y = 1×1024/4 = 256.   CF(256/1024) = CF(1/4) = 1/4. Denominator = 4 = r. ✓</p>
<p>s=2: y = 2×1024/4 = 512.   CF(512/1024) = CF(1/2) = 1/2. Denominator = 2 (divisor of r=4). Try 2r: 7⁴ mod 15 = 1. ✓ → r=4.</p>
<p>s=3: y = 3×1024/4 = 768.   CF(768/1024) = CF(3/4) = 3/4. Denominator = 4 = r. ✓</p>
<p>Success probability per run: 3/4 (s=1,2,3 all eventually give r=4). Expected QPE runs: 4/3 ≈ 1.33.</p>
<p>Each measurement is equally likely (probability 1/r = 1/4 each) because |b_register=1⟩ = (1/√r)Σ_s|u_s⟩.</p>
</div>

<div class="box box-solved-problem">
<p class="box-title"><strong>Solved Problem 1.3</strong></p>
<p><strong>Problem:</strong> Estimate the physical qubit count to run Shor's algorithm on RSA-2048 using the surface code with physical error rate p_phys = 0.1% per two-qubit gate.</p>
<p><strong>Solution:</strong></p>
<p>Logical qubit count for Shor on RSA-2048 (n=2048): approximately 4n = 8192 logical qubits (Beauregard 2003 optimised circuit).</p>
<p>Surface code threshold: error threshold p_th ≈ 1%.</p>
<p>Physical qubits per logical qubit with physical error rate p_phys = 0.1% = 10⁻³:</p>
<p>Code distance d needed for logical error rate p_L ≤ 10⁻¹⁵ (per gate, ~10¹⁰ gates in circuit):</p>
<p>p_L ≈ (p_phys/p_th)^((d+1)/2) ≤ 10⁻¹⁵. With p_phys/p_th = 0.1:</p>
<p>0.1^((d+1)/2) ≤ 10⁻¹⁵ → (d+1)/2 ≥ 15 → d ≥ 29.</p>
<p>Physical qubits per logical qubit: 2d² ≈ 2×29² ≈ 1682.</p>
<p>Total physical qubits: 8192 logical × 1682 physical/logical ≈ 13.8 million ≈ 20 million (rounding for ancillae).</p>
<p>Compare to 2025 hardware: IBM Quantum has ~1000 physical qubits. Gap: ~20,000×.</p>
<p>Conclusion: cryptographically relevant Shor's algorithm requires ~10–15 years of hardware scaling.</p>
</div>

<div class="box box-solved-problem">
<p class="box-title"><strong>Solved Problem 1.4</strong></p>
<p><strong>Problem:</strong> Amplitude amplification with a = 1/10 (success probability = 10%). Find k_opt and the resulting success probability.</p>
<p><strong>Solution:</strong></p>
<p>θ = arcsin(√a) = arcsin(√(1/10)) = arcsin(0.3162) ≈ 0.3218 radians.</p>
<p>Optimal iterations: k_opt = ⌊π/(4θ) − 1/2⌋.</p>
<p>π/(4×0.3218) = 3.1416/1.2872 ≈ 2.44. So k_opt = ⌊2.44 − 0.5⌋ = ⌊1.94⌋ = 1.</p>
<p>Alternative: k_opt = round(π/(4θ)) = round(2.44) = 2.</p>
<p>Check k=1: P₁ = sin²(3θ) = sin²(3×0.3218) = sin²(0.9654) = (0.8222)² ≈ 0.6760 (67.6%).</p>
<p>Check k=2: P₂ = sin²(5θ) = sin²(1.609) = (0.9993)² ≈ 0.9987 (99.9%).</p>
<p>Optimal k = 2 iterations (not 1). P_{k_opt} ≈ 99.9% from initial 10% success probability.</p>
<p>Classical requires 1/a = 10 expected trials. Quantum requires 2 iterations. Speedup ≈ 5×.</p>
</div>

<div class="box box-solved-problem">
<p class="box-title"><strong>Solved Problem 1.5</strong></p>
<p><strong>Problem:</strong> Quantum walk on C₈ (8-cycle): compare classical and quantum mixing times, and estimate the quantum walk search advantage for a marked vertex.</p>
<p><strong>Solution:</strong></p>
<p>Classical random walk on C₈: transition matrix P has eigenvalues λ_k = cos(2πk/8) for k=0,...,7.</p>
<p>Spectral gap: δ = 1 − max{|λ_k| : k ≠ 0} = 1 − cos(2π/8) = 1 − cos(π/4) = 1 − 1/√2 ≈ 0.293.</p>
<p>Classical mixing time: T_mix = O(1/δ) ≈ 3.4 steps (for 8-cycle, exact: T_mix ≈ n²/π² = 64/9.87 ≈ 6.5).</p>
<p>Classical hitting time to reach a specific vertex: HT = O(N) = O(8) steps.</p>
<p>Quantum walk search (Ambainis): O(√(HT)) = O(√8) ≈ O(2.83) queries.</p>
<p>Classical search on C₈: O(8) queries (linear scan).</p>
<p>For larger cycles: Classical O(N), Quantum O(√N) → quadratic speedup scales with N.</p>
<p>At N = 10⁶: Classical: 10⁶ queries; Quantum walk: ~10³ queries. Speedup: 1000×.</p>
</div>

<div class="box box-solved-problem">
<p class="box-title"><strong>Solved Problem 1.6</strong></p>
<p><strong>Problem:</strong> Element distinctness: N = 1000 elements with oracle access. Compare classical, Grover, and quantum walk complexities.</p>
<p><strong>Solution:</strong></p>
<p>Problem: are all 1000 elements distinct, or is there a repeated value?</p>
<p>Classical lower bound: Ω(N) = Ω(1000) queries (read all elements in worst case).</p>
<p>Grover approach: search over all N(N−1)/2 ≈ 500,000 pairs for an equal pair.</p>
<p>Oracle for each pair: O(1) queries. Grover on M=N²/2 pairs: O(√(N²/2)) = O(N/√2) ≈ O(707) queries.</p>
<p>Note: Grover here gives only √2 speedup over classical! Not useful.</p>
<p>Quantum walk (Ambainis): O(N^{2/3}) = O(1000^{2/3}) = O(100) queries.</p>
<p>100 vs 1000 classical: 10× quantum speedup for N=1000.</p>
<p>Scaling: for N = 10⁹: Classical: 10⁹; QW: 10⁶. Speedup: 1000×. Grows as N^{1/3}.</p>
<p>This is proved optimal: the quantum lower bound for ED is Ω(N^{2/3}) (Ambainis 2005).</p>
</div>

<div class="box box-solved-problem">
<p class="box-title"><strong>Solved Problem 1.7</strong></p>
<p><strong>Problem:</strong> HHL condition number sensitivity: A is 100×100 with λ_min = 10⁻³ and λ_max = 10³ (κ = 10⁶). Compare HHL vs conjugate gradient complexity.</p>
<p><strong>Solution:</strong></p>
<p>Condition number: κ = λ_max / λ_min = 10³ / 10⁻³ = 10⁶.</p>
<p>HHL complexity: O(log(N) · κ² · s / ε) = O(log(100) · 10¹² · s / ε).</p>
<p>With s = 5 (sparse) and ε = 10⁻³: O(6.64 · 10¹² · 5 / 10⁻³) = O(3.3 × 10¹⁶) operations.</p>
<p>Conjugate gradient complexity: O(N · √κ · log(1/ε)) = O(100 · 10³ · 10) = O(10⁶) operations.</p>
<p>Comparison: CG is 3.3×10¹⁰ times FASTER than HHL for this matrix!</p>
<p>Root cause: HHL's O(κ²) scaling is catastrophic for ill-conditioned matrices.</p>
<p>Lesson: HHL only wins when κ = O(polylog N). Here κ = 10⁶ &gt;&gt; polylog(100) ≈ 13.</p>
<p>HHL-favorable case: κ = 10, N = 10⁶, s = 5, ε = 10⁻³. Then HHL: O(log(10⁶)·100·5/10⁻³) ≈ O(10⁷). CG: O(10⁶·√10·10) ≈ O(3×10⁷). Here HHL is marginally competitive.</p>
</div>

<div class="box box-solved-problem">
<p class="box-title"><strong>Solved Problem 1.8</strong></p>
<p><strong>Problem:</strong> Shor's algorithm success probability analysis for N = p×q×r (three prime factors). What is the probability of choosing a useful a per run?</p>
<p><strong>Solution:</strong></p>
<p>For N with k prime factors, the probability that a random a (with gcd(a,N)=1) gives a useful order:</p>
<p>P(r is even AND a^(r/2) ≢ −1 mod N) ≥ 1 − 1/2^(k−1).</p>
<p>For k=2 (RSA: N=pq): P ≥ 1 − 1/2 = 1/2.</p>
<p>For k=3 (N=pqr): P ≥ 1 − 1/2² = 3/4.</p>
<p>For k=4 (N=pqrs): P ≥ 1 − 1/2³ = 7/8.</p>
<p>Expected number of "order-finding + post-processing" runs: 1/P.</p>
<p>For RSA-2048 (k=2): ≤ 2 expected QPE runs to find factors.</p>
<p>For a=7, N=15: r=4 (even), 7^(r/2) = 7² = 49 ≡ 4 ≢ −1 (mod 15). This is a "useful" a. ✓</p>
<p>The O(1) expected number of QPE runs is a crucial part of the O(n³) total complexity.</p>
</div>

## B. Unsolved Problems

*Solve each problem independently. Answers are in brackets for self-checking.*

**1.** Factor N = 21 using Shor's algorithm with a = 2. Compute the sequence 2^k mod 21 until it returns to 1, identify r, then compute the factors using GCD post-processing. *[2¹=2, 2²=4, 2³=8, 2⁴=16, 2⁵=32≡11, 2⁶=22≡1. So r=6 (even). x=2³=8. gcd(9,21)=3, gcd(7,21)=7. Factors: 3 and 7. Check: 3×7=21. ✓]*

**2.** A QPE circuit with t=10 clock qubits measures y=384 from Shor's algorithm for an unknown N. What phase φ = y/2^t does this correspond to? Use continued fractions to find the most likely period r. *[φ = 384/1024 = 3/8. CF(3/8) = [0;2,1,2]. Convergents: 0/1, 1/2, 1/3, 3/8. Denominator 8 is the candidate r. Verify: a^8 ≡ 1 (mod N) if r=8.]*

**3.** Amplitude amplification: A quantum circuit A prepares a state with success probability a = 1/20. Compute k\_opt and P(k\_opt). How many classical trials are needed on average for the same success probability? *[θ=arcsin(√(1/20))=arcsin(0.2236)≈0.2257 rad. k\_opt=round(π/(4θ))=round(3.48)=3. P₃=sin²(7×0.2257)=sin²(1.580)≈0.9998≈99.98%. Classical: 1/a=20 trials expected. Speedup: 20/3≈6.7×.]*

**4.** Quantum walk on a complete bipartite graph K\_{2,2} (4 vertices, 2 per side, all cross-edges): what is the spectral gap δ of the corresponding Markov chain? What is the quantum walk search complexity? *[K\_{2,2}: each vertex has degree 2. Transition matrix: P = 1/2·A where A is adjacency matrix. Eigenvalues of A: 2, 0, 0, −2. Spectral gap: δ = 1 − |second eigenvalue of P| = 1 − 0 = 1. Quantum walk search: O(√(N/δ)) = O(√4) = O(2) queries. Classical: O(4) queries. For K\_{n,n}: O(√(2n)) vs O(2n).]*

**5.** HHL for 4×4 diagonal system A = diag(1, 2, 4, 8) with |b⟩ = (1,1,1,1)/2. Compute the exact solution |x⟩ ∝ A⁻¹|b⟩ and the condition number κ. *[A⁻¹ = diag(1, 1/2, 1/4, 1/8). x = A⁻¹b = (1/2, 1/4, 1/8, 1/16)·(1/2) ≡ (1, 1/2, 1/4, 1/8) (unnorm.). ||x||=√(1+1/4+1/16+1/64)=√(85/64)=√85/8≈1.153. Normalised: (0.866, 0.433, 0.217, 0.108). κ = λ\_max/λ\_min = 8/1 = 8.]*

**6.** Post-quantum key sizes: CRYSTALS-Kyber Level 3 has public key 1184 bytes; RSA-2048 has 256 bytes. What is the ratio? How does the Kyber signature (2420 bytes) compare to RSA-2048 signature (256 bytes)? *[Public key ratio: 1184/256 ≈ 4.6× larger for Kyber. Signature ratio: 2420/256 ≈ 9.5× larger. However, Kyber key generation is ~100× faster and encryption ~10× faster than RSA-2048. The bandwidth overhead of PQC is offset by computational gains.]*

**7.** Show that the quantum walk on a hypercube Q\_n (n-dimensional hypercube, N=2^n vertices) has spectral gap δ = 2/n. Derive the quantum walk search complexity and compare with classical. *[Hypercube Q\_n: each vertex has degree n, N=2^n vertices. Laplacian eigenvalues: 2k/n for k=0,1,...,n. Spectral gap δ=2/n. Hitting time HT=O(N/δ)=O(N·n/2)=O(2^n·n/2). Quantum walk: O(√(HT))=O(√(2^n·n))=O(√(N·log N)). Classical: O(N log N). Both quadratic factor apart.]*

**8.** Estimate how long it would take the classical GNFS to factor RSA-4096 (n=4096 bits), assuming 10^18 operations per second on a future classical supercomputer. *[Exponent = 1.923×4096^(1/3)×(ln 4096)^(2/3) = 1.923×16.0×5.47 ≈ 168.5. Operations ≈ e^168.5 ≈ 10^73. Time = 10^73/10^18 = 10^55 seconds. Age of universe ≈ 4.3×10^17 s. RSA-4096 GNFS time ≈ 2.3×10^37 times the age of the universe. Completely infeasible for any foreseeable classical computer.]*

**9.** Grover search on N = 10^6 items: (a) how many iterations k\_opt? (b) What is P(k\_opt)? (c) If the quantum computer runs at 10^6 gate operations/second and each Grover iteration is 100 gates, what is the total search time? *[(a) k\_opt = ⌊π√N/4⌋ = ⌊π×1000/4⌋ = ⌊785.4⌋ = 785 iterations. (b) P(k\_opt) = sin²((2×785+1)×arcsin(10⁻³)) ≈ sin²(785.5×10⁻³) = sin²(0.7855) ≈ 0.9998 ≈ 99.98%. (c) Total gates = 785×100 = 78,500. Time = 78,500/10⁶ = 0.0785 seconds ≈ 79 ms. Classical average: 500,000 checks. At 10⁶/100 = 10⁴ checks/second: 50 seconds. Speedup: 50/0.079 ≈ 635×.]*

**10.** Dequantisation: Tang's result shows classical algorithms match HHL when input has SQ (sample-and-query) access. Give one real-world scenario where HHL retains an exponential advantage over Tang's classical algorithm. *[HHL retains advantage when the input |b⟩ is already a quantum state (e.g., output of another quantum algorithm like VQE or QPE). In this case, SQ access is not available to classical algorithms and the input preparation cost is already paid quantumly. Example: solving a linear system whose right-hand side b is the output state of a quantum chemistry calculation. Tang's dequantisation does not apply to purely quantum-to-quantum pipelines.]*

## C. Multiple Choice Questions

*Note: Answers are given at the end of this section.*

**Q1.** Shor's algorithm factoring N = pq runs in quantum gate complexity:

(a) O(N) gates

(b) O((log N)³) gates = O(n³) where n = log₂N

(c) O(√N) gates

(d) O(N²) gates

**Q2.** The multiplicative order r = ord\_N(a) is defined as:

(a) r = φ(N) = (p−1)(q−1) always

(b) the smallest positive integer with aʳ ≡ 1 (mod N)

(c) r = gcd(a, N)

(d) r = log\_a(N)

**Q3.** The eigenstates of U\_a|x⟩ = |ax mod N⟩ have eigenphases:

(a) e^{2πij/N} for j = 0,...,N−1

(b) e^{2πis/r} for s = 0,...,r−1 where r = ord\_N(a)

(c) Random phases that depend on the input |b⟩

(d) e^{2πij/2^t} for t clock qubits

**Q4.** The Quantum Fourier Transform on t qubits maps |j⟩ to:

(a) (1/√2^t) Σ\_k (−1)^{jk} |k⟩  (Walsh-Hadamard)

(b) (1/√2^t) Σ\_k e^{2πijk/2^t} |k⟩  (quantum DFT)

(c) |j XOR k⟩ for a fixed register k

(d) |j+1 mod 2^t⟩  (cyclic shift)

**Q5.** In amplitude amplification, the Grover iterate Q = −AS₀A⁻¹S\_χ rotates the state vector by angle:

(a) θ per iteration where sin(θ) = √a

(b) 2θ per iteration in the {|good⟩, |bad⟩} subspace

(c) π/2 per iteration regardless of a

(d) π/N for an N-item database

**Q6.** Quantum walk search on a graph G achieves O(√(HT)) complexity where HT is:

(a) The Hamiltonian time for quantum evolution

(b) The classical hitting time of the random walk on G

(c) The Heisenberg time 2πℏ/ΔE

(d) The hyperbolic tangent of the graph Laplacian

**Q7.** The element distinctness problem has quantum complexity O(N^{2/3}) because:

(a) Grover's algorithm gives a cubic speedup for collision problems

(b) A quantum walk on the Johnson graph J(N, N^{2/3}) balances setup, walk, and detection costs optimally

(c) The QFT detects collisions in O(N^{2/3}) time by Fourier analysis

(d) Classical algorithms already run in O(N^{2/3}) for this special problem class

**Q8.** HHL solves Ax = b in O(log N) assuming all of the following EXCEPT:

(a) A is s-sparse with s = O(polylog N)

(b) The condition number κ = O(polylog N)

(c) Only O(1) linear functions of |x⟩ are needed as output

(d) A is a dense matrix with full rank

**Q9.** Tang's 2019 dequantisation result showed that:

(a) HHL is incorrect and contains an error in the complexity analysis

(b) Classical algorithms with sample-and-query access can solve the same linear algebra problems as HHL in polynomial time

(c) Quantum computers cannot solve linear systems faster than O(N²)

(d) Only the condition number caveat limits HHL; sparsity is not a real constraint

**Q10.** CRYSTALS-Kyber's security is based on the hardness of:

(a) Integer factorisation (like RSA)

(b) Discrete logarithm over elliptic curves (like ECDH)

(c) Module Learning With Errors (MLWE) over lattices

(d) Subset sum (NP-complete problem)

**Q11.** The optimal number of Grover iterations for N items, M marked states is approximately:

(a) π√N/4

(b) (π/4)√(N/M)

(c) N/(4M)

(d) √N

**Q12.** In Shor's algorithm, the number of clock qubits t required for reliable period recovery is:

(a) t = n (same as the key size n = log₂N)

(b) t = 2n + O(log n) where n = log₂N

(c) t = O(log log N)

(d) t = O(√n)

**Q13.** The continued fractions algorithm is used in Shor's algorithm because:

(a) It directly computes the prime factorisation from the period r

(b) It recovers the denominator r from the rational approximation y/2^t ≈ s/r

(c) It provides the eigenvalues of the modular exponentiation operator

(d) It computes the GCD faster than the Euclidean algorithm

**Q14.** The continuous-time quantum walk |ψ(t)⟩ = e^{−iAt}|ψ₀⟩ differs from the coined walk because:

(a) It uses a coin register to record direction

(b) It spreads sub-diffusively (slower than classical walks)

(c) No coin register is needed — evolution is driven directly by the graph adjacency matrix A

(d) It only works on regular graphs where all vertices have the same degree

**Q15.** A 'harvest now, decrypt later' quantum attack on RSA is dangerous because:

(a) RSA-encrypted data can be broken immediately by today's quantum computers

(b) Adversaries can store encrypted traffic today and decrypt it once a sufficiently large quantum computer exists in the future

(c) Quantum computers can already simulate RSA key generation

(d) Post-quantum algorithms are currently less secure than RSA against classical attacks

<div class="box box-generic">
<p class="box-title"><strong>MCQ ANSWERS</strong></p>
<p>Q1: (b) O((log N)³) = O(n³) — polynomial in the number of bits n = log₂N; exponential speedup over GNFS</p>
<p>Q2: (b) Smallest positive integer with aʳ ≡ 1 (mod N) — definition of multiplicative order</p>
<p>Q3: (b) e^{2πis/r} for s = 0,...,r−1 — the period r is encoded in the denominators of the eigenphases</p>
<p>Q4: (b) (1/√2^t) Σ_k e^{2πijk/2^t} |k⟩ — quantum discrete Fourier transform with complex exponential phases</p>
<p>Q5: (b) 2θ per iteration in the good/bad subspace — each Q application rotates by exactly 2θ</p>
<p>Q6: (b) Classical hitting time HT — quantum walk converts the classical hitting time to O(√(HT)) queries</p>
<p>Q7: (b) Johnson graph J(N, N^{2/3}) — setup, walk, and detection costs each become O(N^{2/3}) at this balance point</p>
<p>Q8: (d) Dense matrix — HHL requires sparse A; density (s=N) removes the log N speedup entirely</p>
<p>Q9: (b) Classical with SQ access matches HHL — dequantisation shows quantum is not needed for some regimes</p>
<p>Q10: (c) Module Learning With Errors (MLWE) — CRYSTALS-Kyber security reduces to MLWE, believed hard for quantum computers</p>
<p>Q11: (b) (π/4)√(N/M) — the standard formula for optimal Grover iterations with M marked states among N total</p>
<p>Q12: (b) t = 2n + O(log n) — needed to approximate s/r to sufficient precision for continued fractions to recover r ≤ N</p>
<p>Q13: (b) Recovers denominator r from approximation y/2^t ≈ s/r — the continued fractions theorem guarantees unique recovery</p>
<p>Q14: (c) No coin register — CTQW uses graph adjacency matrix A as Hamiltonian, requires no auxiliary coin system</p>
<p>Q15: (b) Store now, decrypt later — encrypted data with long-term sensitivity is already at risk from future quantum hardware</p>
</div>

## D. Theory Questions

**1.**  State and prove Euler's theorem (aφ(N) ≡ 1 (mod N) for gcd(a,N)=1). Use this to prove that the multiplicative order r = ord\_N(a) always divides φ(N) = (p−1)(q−1). Explain what this tells us about the structure of the RSA group Z\*\_N and why it makes order-finding a well-posed problem.

**2.**  Describe the complete Shor's algorithm circuit architecture for factoring an n-bit number N: (a) State the exact qubit counts for the clock register and work register. (b) Explain the role of each register and why the work register is initialised to |1⟩. (c) Describe the structure of the controlled-U\_a^(2^k) gate. (d) Explain why measuring the clock register after QFT† gives phase information about s/r.

**3.**  Derive the amplitude amplification theorem: starting from A|0⟩ = √a|good⟩ + √(1−a)|bad⟩, show by induction that after k applications of Q = −AS₀A⁻¹S\_χ, the state is sin((2k+1)θ)|good⟩ + cos((2k+1)θ)|bad⟩ where sin(θ) = √a. Prove that this is equivalent to a 2θ rotation in the 2D subspace spanned by |good⟩ and |bad⟩.

**4.**  Explain the Szegedy quantum walk framework: (a) Define the bipartite double cover of a graph. (b) Define the Szegedy walk operator W from a Markov chain P. (c) Prove that the eigenvalues of W are e^{±i arccos(√λ)} where λ are eigenvalues of P²P†. (d) Explain why this encodes the mixing time of P in the quantum phase angles of W.

**5.**  Derive the three stages of the HHL algorithm from first principles: (a) Show that QPE on A with input |b⟩ = Σⱼ βⱼ|uⱼ⟩ produces Σⱼ βⱼ|λⱼ⟩|uⱼ⟩. (b) Show how the conditional rotation Ry(2 arcsin(C/λⱼ)) on the ancilla encodes 1/λⱼ in the ancilla amplitude. (c) Explain why uncomputing QPE and post-selecting on |1⟩ in the ancilla gives |x⟩ ∝ A⁻¹|b⟩.

**6.**  Discuss in detail the four caveats of the HHL algorithm. For each caveat, (a) state precisely what assumption is required, (b) describe a specific real-world application domain where the caveat fails, (c) describe any mitigation strategy (partial or complete) that has been proposed.

**7.**  Compare and contrast the coined quantum walk and the continuous-time quantum walk (CTQW) on the complete graph K\_N: (a) Write the explicit coin+shift operators for the coined walk. (b) Write the Schrödinger equation for the CTQW. (c) Show that both achieve O(√N) search. (d) Identify one mathematical structure each walk exploits that the other does not.

**8.**  Explain the element distinctness quantum walk algorithm on the Johnson graph J(N, r) in detail: (a) Define the vertices, edges, and adjacency structure of J(N, r). (b) Describe the three-phase algorithm (Setup, Walk, Check). (c) Show that choosing r = N^{2/3} minimises the total query complexity. (d) State the quantum lower bound and explain why it shows O(N^{2/3}) is optimal.

**9.**  State and explain Tang's dequantisation theorem for the quantum recommendation systems algorithm. (a) Define the Sample-and-Query (SQ) classical access model. (b) Explain why SQ access can simulate quantum state preparation. (c) Identify the regime where HHL retains a genuine quantum advantage over Tang's classical algorithm. (d) Discuss the implications for other quantum machine learning algorithms.

**10.**  Describe the post-quantum cryptographic landscape: (a) Explain the mathematical basis of CRYSTALS-Kyber (MLWE). (b) Compare the key sizes and computational costs of Kyber-1024 vs RSA-2048 vs ECDH-P256. (c) Describe the "hybrid" transition strategy and why it is recommended. (d) Discuss India's specific challenges and opportunities in the post-quantum migration, referencing the NQM.

## E. Programming Assignments

**[PA-1.1]  Shor's Algorithm for N = 15, 21, 35:**

Implement the complete Shor's algorithm in Qiskit for N = 15, 21, and 35. For each N: (a) choose two coprime values of a; (b) pre-compile the modular exponentiation oracle as a hand-optimised gate sequence (use the swap-based circuit given in the lecture for N=15; derive it for N=21 and N=35 using the cyclic group structure); (c) apply QPE with t=8 clock qubits; (d) apply the Inverse QFT; (e) measure, apply continued fractions, and compute the factors. Run on Qiskit AerSimulator with 4096 shots. Plot the measurement histogram for each run (label peaks at y = s·2^t/r). Report: (i) number of distinct period values r observed; (ii) success rate (fraction of shots leading to correct factorisation) over 10 independent runs; (iii) circuit depth and gate count.

**[PA-1.2]  Generalised Amplitude Amplification with a Non-Uniform Initialisation:**

Implement amplitude amplification in Qiskit where the algorithm A is NOT the Hadamard transform but a custom circuit that gives success probability a = 0.05. Specifically, prepare the state: A|0000⟩ = √(0.05)|1010⟩ + √(0.95)|other⟩ using an appropriate Ry + CNOT circuit. (a) Identify the "good" oracle S\_χ that marks |1010⟩. (b) Implement the full Grover iterate Q = −AS₀A†S\_χ. (c) Run k = 1, 2, ..., 7 iterations and measure P\_k. (d) Plot P\_k vs k and compare with the theoretical prediction sin²((2k+1)arcsin(√0.05)). (e) Find k\_opt experimentally and verify against the formula. Run on AerSimulator (8192 shots) and on IBM Quantum hardware (1024 shots). Report the noise-induced deviation from theory on hardware.

**[PA-1.3]  Coined Quantum Walk on a Cycle C₈ — Ballistic Spreading Verification:**

Implement a coined quantum walk on a cycle C₈ (8 vertices) using: position register (3 qubits for 8=2³ states) and coin register (1 qubit). Coin operator: Hadamard H. Shift operator: controlled increment/decrement on position. (a) Initialise in |0⟩\_position ⊗ |+⟩\_coin. (b) Apply t = 1, 2, 4, 8, 16, 32 walk steps. (c) For each t, measure the position register with 4096 shots and plot the probability distribution P(vertex, t). (d) Compute the variance σ²(t) of the position distribution and plot σ² vs t. (e) Fit the data to σ² = c·t^α and determine α. Verify that α ≈ 2 (ballistic) for the quantum walk vs α = 1 (diffusive) for a classical random walk on C₈ with the same number of steps.

## F. Project Suggestions

**Project 1.A — Post-Quantum Cryptography Implementation and Benchmarking:**

Design and implement a complete comparative study of RSA-2048 versus CRYSTALS-Kyber-1024 (NIST Level 3) for key exchange and CRYSTALS-Dilithium Level 3 versus RSA-PSS for digital signatures. (a) Implement RSA-2048 using the Python cryptography library. (b) Implement Kyber-1024 and Dilithium-3 using the liboqs (Open Quantum Safe) Python bindings. (c) Benchmark: key generation time, encapsulation/decapsulation time, signing/verification time, and key/ciphertext/signature sizes. (d) Implement a hybrid TLS 1.3-style key exchange using both ECDH (X25519) and Kyber-1024 simultaneously (concatenate the two shared secrets and hash for the session key). (e) Apply the implementation to a concrete use case relevant to India: e-governance digital signatures under DigiLocker, Aadhaar-based authentication, or UPI transaction signing. Write a 15-page report including: algorithm descriptions, benchmark results, migration recommendations, and policy implications for India's NQM digital infrastructure programme.

**Project 1.B — Quantum Walk Algorithms: Implementation and Complexity Validation:**

Implement quantum walks on four graph types and rigorously benchmark their complexity scaling: (a) Line graph P\_n for n = 8, 16, 32 with Hadamard coin — verify σ(t) ∝ t (ballistic). (b) Cycle C\_n for n = 8, 16, 32 — verify bimodal distribution and O(n) return time. (c) 2D grid (n×n) for n = 4, 8 — verify σ ∝ t and quantum walk search in O(n√(log n)) steps. (d) Complete graph K\_n for n = 8, 16 — verify O(√n) search. For each graph and walk type: implement both coined (Grover coin) and CTQW (using matrix exponentiation). Implement quantum walk search (mark one vertex) and record query counts to find it. Plot variance σ²(t) and fit to t^α; compare α for quantum vs classical. Write a 15-page report comparing all graph types, both walk formulations, and the scaling of the classical-to-quantum speedup ratio.

**Project 1.C — HHL Algorithm for Physical Systems: Analysis of Quantum Advantage:**

Apply the HHL algorithm to the linear system arising from three progressively larger physical problems and rigorously evaluate when (if ever) the quantum advantage over classical emerges: Problem A: 1D Poisson equation −d²u/dx² = f(x) discretised on n=4, 8, 16 grid points (tridiagonal matrix). Problem B: 2D Laplacian on n×n grid, n=2, 4 (banded sparse matrix). Problem C: Tight-binding model for a quantum wire of length L=4, 8, 16 sites (tridiagonal complex Hermitian). For each problem: (a) Construct A and b explicitly; compute κ and sparsity s. (b) Implement HHL in Qiskit (exact statevector simulation). (c) Implement classical solution using numpy.linalg.solve. (d) Compute fidelity F = |⟨x\_quantum|x\_classical⟩|² between quantum and classical normalised solutions. (e) Plot fidelity vs problem size and noise level (add depolarising noise at p = 0, 0.1%, 1%). (f) Determine the critical condition number κ\_critical above which HHL becomes impractical due to small post-selection probability. Write a 15-page report with a clear verdict on the practical quantum advantage regime.
