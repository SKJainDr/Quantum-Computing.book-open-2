# CHAPTER 4

# Quantum Advantage: Theory, Evidence & Reality

*Random Circuit Sampling  |  Boson Sampling  |  Near-Term Prospects  |  Hype vs Reality*

<div class="box box-anecdote">
<p class="box-title"><strong>📜  Chapter Epigraph — Scott Aaronson, 2019</strong></p>
<p>"Quantum supremacy does not mean quantum computers are better than classical computers.</p>
<p>It means they are better at something — even if that something is completely artificial."</p>
<p>— Scott Aaronson, 2019</p>
<p>The debate around 'quantum advantage' and 'quantum supremacy' cuts to the heart of what we mean</p>
<p>when we say one computational device outperforms another. This chapter examines the evidence,</p>
<p>the controversy, and the road ahead.</p>
</div>

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  Chapter 4 Learning Objectives</strong></p>
<p>After studying this chapter, you will be able to:</p>
<p>•  Define quantum supremacy and distinguish it from practical quantum advantage</p>
<p>•  Describe the Google 2019 random circuit sampling experiment and its claims</p>
<p>•  Understand the connection between random circuit sampling and PH collapse</p>
<p>•  Describe boson sampling (Xanadu Borealis 2022) and its theoretical basis</p>
<p>•  Critically evaluate claimed quantum advantages in optimisation, ML, and finance</p>
<p>•  Understand Quantum Volume and other hardware benchmarks</p>
<p>•  Assess the current state and near-term prospects of practical quantum advantage</p>
</div>

## 4.1 What Is Quantum Advantage?

The terms 'quantum advantage', 'quantum supremacy', and 'quantum computational advantage' are used with varying precision. We establish precise definitions, because the distinctions matter enormously for evaluating claims.

| Term | Definition | Status (2024) |
|---|---|---|
| Quantum supremacy | A QC completes a task that no classical computer can in reasonable time (even if artificial) | Claimed by Google 2019; partially disputed |
| Quantum computational advantage | QC outperforms best classical algorithm on ANY task | Demonstrated for special-purpose tasks |
| Practical quantum advantage | QC solves a practically relevant problem faster than classical | NOT yet demonstrated |
| Quantum utility (IBM) | QC computes something useful even if classical could also do it | IBM 2023 Nature paper claims this |

## 4.2 Google 2019: Random Circuit Sampling and Sycamore

In October 2019, Google published in Nature 'Quantum supremacy using a programmable superconducting processor'. Their 54-qubit Sycamore processor (53 active qubits) sampled from the output distribution of a random quantum circuit in 200 seconds — a task they claimed would take the best classical supercomputer 10,000 years.

### 4.2.1 What Is Random Circuit Sampling?

- Generate a random quantum circuit C by choosing random 1-qubit and 2-qubit gates.

- Run the circuit on a quantum computer and measure the output bit string z.

- Repeat many times to build the output distribution {p(z) = |⟨z|C|0^n⟩|²}.

- Verify the distribution matches the expected distribution via cross-entropy benchmarking (XEB).

<div class="box box-generic">
<p class="box-title"><strong>Theorem 4.1  Hardness of RCS (Informal — NOT a proven theorem)</strong></p>
<p>If a polynomial-time classical algorithm exists that can spoof the cross-entropy benchmark (XEB)</p>
<p>of a sufficiently deep random circuit, then the polynomial hierarchy collapses to the third level.</p>
<p>This is believed to be false — hence RCS is CONJECTURED to be classically hard.</p>
<p>CRITICAL CAVEAT: This is NOT a proven theorem like BBBV or the polynomial method.</p>
<p>It is a conditional hardness result based on complexity-theoretic conjectures.</p>
<p>Unlike Grover's proven quadratic speedup, the RCS hardness claim could potentially be refuted.</p>
</div>

### 4.2.2 Google Sycamore: Technical Details

| Parameter | Value |
|---|---|
| Processor | Sycamore (Google, 2019) |
| Active qubits | 53 |
| Circuit depth | 20 cycles |
| Gate fidelity (2-qubit) | ~99.4% average |
| Sampling time | 200 seconds (3.3 minutes) |
| Classical simulation claim | 10,000 years on Summit supercomputer |
| Verification method | Linear cross-entropy benchmarking (XEB) |
| Publication | Nature 574, 505–510 (2019) |

<div class="box box-anecdote">
<p class="box-title"><strong>📜  The Leaked Paper — September 2019</strong></p>
<p>Google's quantum supremacy paper was briefly posted on a NASA server in September 2019 — accidentally,</p>
<p>before peer review was complete. A journalist spotted it, and within hours the news had spread worldwide.</p>
<p>Google had to issue a 'no comment' while frantically completing peer review, IBM prepared a rebuttal,</p>
<p>and the quantum computing community erupted.</p>
<p>When the paper was formally published in Nature on October 23, 2019, it was front-page news globally.</p>
<p>The Financial Times called it 'the space race of computing'. John Preskill, who coined 'quantum supremacy'</p>
<p>in 2012, said he was 'thrilled' while noting the task had no immediate applications.</p>
<p>IBM CEO Arvind Krishna later quipped: 'Classical computers will not stop getting better just because</p>
<p>quantum computers got a little better.' This captures the nuance perfectly.</p>
</div>

### 4.2.3 The IBM Rebuttal and Resolution

<div class="box box-warning">
<p class="box-title"><strong>⚠  The Google-IBM Debate: Key Points and Resolution</strong></p>
<p>Google's claim (October 2019):</p>
<p>• Sycamore samples the circuit distribution in 200 seconds</p>
<p>• Classical simulation would take ~10,000 years on Summit supercomputer</p>
<p>IBM's rebuttal (October 2019, 2 days before publication):</p>
<p>• Using tensor network methods with disk storage: ~2.5 days on IBM Summit</p>
<p>• Google's estimate assumed insufficient classical memory use</p>
<p>Resolution (2022–2023):</p>
<p>• Multiple classical algorithms improved (Gao-Duan, Pan-Liu-Zhang, others)</p>
<p>• At depth 20: classical simulation is within ~5–10× of quantum — NOT 10,000 years!</p>
<p>• At deeper circuits (depth 40+): quantum clearly wins</p>
<p>• The debate drove BOTH quantum and classical advances — science won</p>
<p>• Verdict: genuine quantum advantage at deep circuits, but not at the originally claimed scale</p>
</div>

## 4.3 Boson Sampling: Photonic Quantum Advantage

Boson sampling is an alternative route to quantum advantage, proposed by Aaronson and Arkhipov in 2011. Unlike RCS (universal quantum circuits), boson sampling uses linear-optical networks — a much simpler physical implementation that is easier to build but harder to classically simulate.

### 4.3.1 The Aaronson-Arkhipov Theorem

The output distribution of a boson sampler is determined by the PERMANENT of a complex matrix — a quantity that is #P-hard to compute:

**p(S) = |Perm(U\_S)|² / (s₁! s₂! ··· s\_m!)   where U\_S is a submatrix of the optical unitary U**

<div class="box box-generic">
<p class="box-title"><strong>Theorem 4.2  Aaronson-Arkhipov Hardness of Boson Sampling (2011)</strong></p>
<p>If there exists a polynomial-time classical algorithm to approximate the boson sampling</p>
<p>distribution (within constant total variation distance), then:</p>
<p>• The permanent of Gaussian random matrices is easy to approximate on average</p>
<p>• This would imply PH collapses to the third level</p>
<p>Since PH collapse is believed false, boson sampling is conjectured to be classically hard.</p>
<p>Key advantage over RCS: The hardness basis is cleaner — permanent = #P-hard is a firm</p>
<p>mathematical result (not just a conjecture about random circuits).</p>
<p>Hardware advantage: No universal quantum gates needed — only beamsplitters and phase shifters.</p>
</div>

### 4.3.2 Major Boson Sampling Experiments

| Experiment | Year | System | Scale | Classical Hardness Claim |
|---|---|---|---|---|
| Jiuzhang 1.0 (USTC) | 2020 | Gaussian BS photonic | 76 photons, 100 modes | ~2.5 billion years |
| Jiuzhang 2.0 (USTC) | 2021 | Gaussian BS photonic | 113 photons | Even harder |
| Zuchongzhi (USTC) | 2021 | Superconducting (RCS) | 66 qubits | ~8 years |
| Borealis (Xanadu) | 2022 | Time-multiplexed GBS | 216 modes | ~9,000 years |
| Sycamore v2 (Google) | 2023 | Superconducting (RCS) | 70 qubits | Deep circuit, hard |

<div class="box box-warning">
<p class="box-title"><strong>⚠  Important Caveat on GBS Applications</strong></p>
<p>All proposed applications of Gaussian Boson Sampling are at the 'quantum-inspired' or 'heuristic' level.</p>
<p>No provable quantum speedup for any practically relevant problem using GBS has been demonstrated.</p>
<p>The connection to molecular spectra is real: GBS output is related to Franck-Condon factors.</p>
<p>BUT: computing spectra via GBS requires exponentially many samples to estimate output probabilities</p>
<p>— which eliminates the classical hardness advantage in practice.</p>
<p>Graph problems (maximum clique, dense subgraph): HEURISTIC only — no proven speedup.</p>
<p>Quantum ML: GBS-based kernel methods — noise typically hurts on real NISQ hardware.</p>
</div>

## 4.4 Quantum Advantage in Optimisation, ML, and Finance: Hype vs Reality

The hype around quantum computing is most intense in optimisation, machine learning, and finance. We critically assess each with current evidence.

### 4.4.1 Quantum Optimisation

| Claim | Algorithm | Reality Check (2024) |
|---|---|---|
| QAOA solves MaxCut faster | QAOA (Farhi 2014) | p=1: 11/16 approx. ratio; classical GW SDP achieves 0.878. No proven advantage. |
| Quantum annealing beats classical | D-Wave | No systematic speedup over classical simulated annealing for practical instances. |
| Grover gives √N speedup for NP | Amplitude amplification on brute-force | True BUT: √(2^n) = 2^(n/2) — still EXPONENTIAL. Does NOT solve NP in poly time. |
| Quantum solves TSP faster | Various proposals | No polynomial quantum algorithm for TSP; best is Grover-based O(2^(n/2)). |

### 4.4.2 Quantum Machine Learning

| QML Claim | Status (2024) |
|---|---|
| HHL-based linear algebra speedups in ML | DEQUANTISED by Tang (2018); classical matches quantum in relevant input models |
| Quantum neural networks learn faster | No proven advantage; barren plateaus may make QNNs SLOWER than classical |
| Quantum kernels provide advantage | On NISQ: noise typically hurts; advantage requires very specific data structure |
| Quantum data encoding gives speedup | Loading N classical data points takes O(N) — eliminates speedup for classical data |
| PQC as universal function approximator | True but no advantage over classical NN; expressibility ≠ quantum advantage |

### 4.4.3 Quantum Finance

Finance is a major target for quantum computing investment. The assessment is nuanced:

- Monte Carlo estimation: Amplitude estimation gives genuine O(1/ε) speedup vs classical O(1/ε²) — a REAL quadratic speedup. But requires fault-tolerant quantum computers with thousands of logical qubits.

- Portfolio optimisation: Mapped to QUBO and solved with QAOA/VQE — no proven advantage over classical Gurobi/SDP solvers for industry-scale problems.

- Derivative pricing: Genuine quadratic speedup possible — but 'quantum-ready' classical computers (GPU Monte Carlo) have narrowed the practical gap substantially.

<div class="box box-real-world">
<p class="box-title"><strong>🌐  Honest Assessment: When Will Practical Quantum Advantage Arrive?</strong></p>
<p>Expert consensus (McKinsey, BCG, IBM Research, 2023):</p>
<p>NISQ era (2024–2030): No practical advantage for commercial optimisation, ML, or finance.</p>
<p>• Too few qubits, too much noise, insufficient error correction.</p>
<p>• Most claimed 'quantum advantages' are for toy/artificial problems.</p>
<p>Early fault-tolerant era (2030–2040): First real advantages in quantum chemistry &amp; materials.</p>
<p>• Drug discovery: Simulating large molecules (FeMoco, Cytochrome P450) for pharma R&amp;D</p>
<p>• Materials science: High-Tc superconductors, battery cathode materials</p>
<p>• Cryptanalysis: Shor's algorithm threatening RSA (post-quantum transition needed NOW)</p>
<p>Full fault-tolerant era (2040+): Broad quantum advantage across optimisation and ML.</p>
<p>• Requires millions of physical qubits with error rates below 0.1%</p>
<p>Bottom line: Be sceptical of near-term commercial quantum advantage claims.</p>
<p>The physics is sound. The engineering challenges remain enormous.</p>
</div>

## 4.5 Hardware Benchmarks: Quantum Volume and Beyond

Raw qubit count is misleading — a 1000-qubit device with 10% error rates is useless. Several standardised metrics have been developed to measure quantum processor quality more faithfully.

### 4.5.1 Quantum Volume

Quantum Volume (QV), introduced by IBM in 2019, measures the largest random square circuit (depth = width = n) that can be executed with heavy-output generation fidelity > 2/3:

**QV = 2^n   where n = max square circuit dimension executed with fidelity > 2/3**

| System | Year | Physical Qubits | Quantum Volume |
|---|---|---|---|
| IBM Falcon | 2020 | 5 | QV = 4 |
| IBM Hummingbird | 2021 | 65 | QV = 64 |
| IBM Eagle | 2022 | 127 | QV = 128 |
| IBM Heron (R1) | 2023 | 133 | QV = 256+ |
| IonQ Aria | 2023 | 25 (trapped ion) | QV ≈ 1024+ |
| Quantinuum H2 | 2024 | 56 (trapped ion) | QV > 65,536 |

<img class="fig-img" src="content/images/image15.png" alt="figure">

**Figure 4: IBM Quantum Volume Progress (2017–2024)** *— Left: QV on log scale showing IBM superconducting (blue) and trapped-ion leaders (orange/purple). Right: log₂(QV) grows roughly 1 bit/year — a 'Moore's Law for Quantum'*

### 4.5.2 Other Hardware Benchmarks

| Benchmark | Measures | Notes |
|---|---|---|
| Quantum Volume (QV) | Overall processor quality | Single-number metric; widely used |
| CLOPS (Circuit Layer Ops/sec) | Speed of circuit execution | IBM metric; important for NISQ apps |
| Error Per Layer of Gate (EPLG) | Noise per circuit layer | Better for deep circuits |
| Algorithmic Qubits (AQ) | Effective logical qubits | IonQ metric |
| T-gate fidelity | Fault-tolerant gate quality | Critical for error-corrected algorithms |
| Mirror Benchmarking | System-wide noise model | Sandia Labs; hardware-agnostic |

<div class="box box-generic">
<p class="box-title"><strong>📋  Chapter 4 Summary</strong></p>
<p>Quantum supremacy (Google 2019): RCS in 200s; 10,000yr classical claim partially refuted; genuine adv. at depth 40+</p>
<p>RCS hardness:      Conditional on PH non-collapse — NOT a proven theorem like BBBV</p>
<p>Boson sampling:    Permanent = #P-hard; Aaronson-Arkhipov: classical simulation implies PH collapse</p>
<p>Xanadu Borealis:   216-mode GBS (2022); fastest photonic advantage demo; applications limited</p>
<p>Quantum optimisation: No proven advantage; QAOA p=1 underperforms classical GW for MaxCut</p>
<p>Quantum ML:        Mostly dequantised; barren plateaus; no advantage for classical data loading</p>
<p>Quantum finance:   Genuine quadratic speedup for Monte Carlo (amplitude estimation) — needs FT HW</p>
<p>Quantum Volume:    QV = 2^n; single metric for processor quality; trapped-ion systems now lead</p>
<p>Near-term outlook: No commercial advantage before 2030; first real applications: quantum chemistry ~2035</p>
</div>

## RECAP — SHORT ANSWER QUESTIONS & MODEL ANSWERS

Chapter 4: Quantum Advantage — Theory, Evidence & Reality

Instructions: Answer each question in 3–6 lines. Each question carries equal marks.

**PART A — QUESTIONS**

**Q1.  Define 'quantum advantage' and distinguish it from 'quantum supremacy'.**

**Q2.  Summarise Google's 2019 random circuit sampling experiment and its central claim.**

**Q3.  Why was Google's 2019 supremacy claim later disputed by IBM?**

**Q4.  What is boson sampling, and what makes it a plausible route to quantum advantage on photonic hardware?**

**Q5.  Assess critically whether quantum computers currently provide practical advantage in optimisation, machine learning, or finance.**

**Q6.  What conditions must be satisfied for a claimed quantum advantage result to be considered credible?**

**Q7.  What is Quantum Volume and how does it serve as a hardware benchmark?**

**Q8.  Beyond Quantum Volume, name another hardware benchmark discussed and explain what it measures.**

**Q9.  Why is 'hype vs reality' an important framing for quantum advantage claims in industry contexts?**

**Q10.  What role does cross-entropy benchmarking (XEB) play in certifying quantum advantage experiments?**

**Q11.  Explain why 'quantum advantage in optimisation' claims must be evaluated against continually improving classical heuristics.**

**PART B — MODEL ANSWERS**

**Answer 1:**

Quantum advantage refers to a quantum computer solving a useful, practically relevant problem faster than any known classical method. Quantum supremacy (or 'computational advantage') is a narrower, more contested claim: that a quantum device performs some specific task — not necessarily useful — that is intractable for any classical computer, typically demonstrated via contrived sampling problems designed to be classically hard rather than for practical application.

**Answer 2:**

Google's Sycamore processor (53 qubits) sampled from the output distribution of a pseudo-random quantum circuit in about 200 seconds, a task Google claimed would take the fastest classical supercomputer roughly 10,000 years to simulate. The experiment used linear cross-entropy benchmarking (XEB) to certify that the sampled outputs matched the ideal distribution better than random guessing, providing statistical evidence of a genuine quantum sampling advantage.

**Answer 3:**

IBM argued that with sufficient classical disk storage and an optimised simulation strategy (using secondary storage rather than requiring everything to fit in RAM), the same random-circuit-sampling task could in principle be simulated classically in about 2.5 days rather than 10,000 years — vastly reducing, though not eliminating, the claimed classical-quantum gap. This dispute illustrates how supremacy claims are inherently tied to the best available classical algorithms and hardware at the time, and can be eroded by classical algorithmic improvements.

**Answer 4:**

Boson sampling asks for samples from the output distribution of identical photons passed through a linear optical network (an interferometer), a distribution believed to be classically hard to sample from because it is related to computing matrix permanents (#P-hard). Xanadu's 2022 photonic experiment claimed a computational advantage using a room-temperature photonic processor, exploiting the fact that photonic implementations avoid the extreme cryogenic cooling required by superconducting qubits.

**Answer 5:**

As of the current NISQ era, no quantum algorithm has demonstrated a proven, practically significant advantage over classical methods for real-world optimisation, ML, or finance problems at useful problem sizes — most claimed 'quantum advantage' results in these domains are either restricted to artificially constructed instances, rely on unverified classical-hardness assumptions, or are matched by improved classical heuristics (dequantisation). The honest assessment is one of promising theoretical potential (e.g. QAOA, quantum amplitude estimation) but no settled practical quantum advantage yet in these applied domains.

**Answer 6:**

A credible quantum advantage claim requires: (1) a well-defined computational task with a precisely stated complexity-theoretic hardness argument, (2) verification that the quantum device actually solves the task with adequate fidelity (not just noise), (3) a genuinely optimised classical competitor algorithm (not merely a naive baseline), and (4) reproducibility across independent implementations. Many early claims have been partially or fully overturned when the classical baseline was later improved, underscoring the need for all four conditions.

**Answer 7:**

Quantum Volume (QV) is a single-number benchmark defined as QV = 2^n, where n is the largest number of qubits for which a randomly-generated depth-n square circuit can be executed with heavy-output probability greater than 2/3 on real hardware. It jointly captures qubit count, gate fidelity, connectivity, and calibration quality in one metric, making it useful for comparing overall device capability across vendors rather than looking at qubit count alone.

**Answer 8:**

CLOPS (Circuit Layer Operations Per Second) measures the speed (throughput) at which a device can execute the many parameterised circuit variants required by variational algorithms such as VQE and QAOA, capturing not just fidelity (as QV does) but practical runtime performance for iterative hybrid workloads — a metric increasingly emphasised as devices are used for real algorithmic workloads rather than one-shot demonstrations.

**Answer 9:**

Media and marketing narratives around quantum computing often overstate near-term capabilities, especially for optimisation, ML, and finance applications, creating a gap between public perception and the actual NISQ-era state of the art. A rigorous, evidence-based assessment — distinguishing demonstrated results from speculative extrapolation — helps students, engineers, and policymakers make sound decisions about research investment and expectations, avoiding both unwarranted hype and unwarranted dismissal.

**Answer 10:**

Linear XEB estimates the fidelity of a noisy quantum device's output distribution by comparing the empirically measured bitstring probabilities against the ideal (noiseless) simulated probabilities for the same random circuit, producing a single fidelity-like score. It was the primary statistical tool used to certify that Google's Sycamore samples were closer to the true quantum distribution than to a uniform random distribution, providing quantitative evidence the device was executing genuine quantum dynamics rather than just noise.

**Answer 11:**

Because classical optimisation algorithms (simulated annealing, tensor-network methods, specialised heuristics) are actively improved over time, a quantum algorithm's apparent edge at one point in time can be erased by subsequent classical algorithmic advances — exactly as happened with IBM's classical rebuttal of Google's 2019 claim. This 'moving target' nature of classical competition means genuine, durable quantum advantage claims must be robust to reasonably foreseeable classical improvements, not just current classical implementations.

## A. Solved Problems

<div class="box box-generic">
<p class="box-title"><strong>Solved Example 6  Cross-Entropy Benchmarking (XEB) Calculation</strong></p>
<p>Problem: Calculate the linear XEB score for a 2-qubit random circuit.</p>
<p>Ideal probabilities: p(00)=0.6, p(01)=0.2, p(10)=0.15, p(11)=0.05.</p>
<p>Actual measurement outcomes (M=4 shots): {00, 00, 01, 10}.</p>
<p>Linear XEB fidelity formula:</p>
<p>F_XEB = 2^n × (1/M) Σᵢ p_ideal(zᵢ) − 1</p>
<p>For n=2 qubits: 2^n = 4</p>
<p>Sum of ideal probabilities for measured outcomes:</p>
<p>p(00) + p(00) + p(01) + p(10) = 0.6 + 0.6 + 0.2 + 0.15 = 1.55</p>
<p>Average: 1.55/4 = 0.3875</p>
<p>F_XEB = 4 × 0.3875 − 1 = 1.55 − 1 = 0.55</p>
<p>Interpretation: 55% fidelity — processor outputs match ideal distribution at 55% level.</p>
<p>For Google's 53-qubit Sycamore: F_XEB ≈ 0.002 (low but significant above noise floor of 0).</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Solved Example 8  Assessing Quantum Advantage for Portfolio Optimisation</strong></p>
<p>Problem: Critically evaluate quantum advantage prospects for optimising a portfolio of n=500 assets.</p>
<p>Classical formulation: Markowitz mean-variance optimisation</p>
<p>minimise w^TΣw − λμ^Tw  subject to Σwᵢ=1, wᵢ≥0</p>
<p>With binary weights → QUBO with n binary variables</p>
<p>Classical solver: Gurobi MILP → solves practical instances in seconds</p>
<p>QAOA approach for QUBO:</p>
<p>n=500 binary variables → 500 qubits needed (at current noise levels: infeasible)</p>
<p>QAOA at p=1-5: approximation ratio worse than classical SDP in practice</p>
<p>Assessment:</p>
<p>• Current hardware: 500 fault-tolerant qubits unavailable until ~2035+</p>
<p>• NISQ QAOA at p=1-5: consistently worse than classical Gurobi for 500 assets</p>
<p>• Genuine quantum speedup for portfolio: NOT demonstrated, unlikely before 2035</p>
<p>• Hype level: HIGH;  Actual near-term advantage: VERY LOW</p>
<p>• When it might matter: Quantum Monte Carlo for risk estimation (~2030s, needs fault tolerance)</p>
</div>

## B. Unsolved Problems

## Chapter 4: Problems

**11.** Calculate F\_XEB for 2-qubit circuit: p(00)=0.5, p(01)=0.3, p(10)=0.15, p(11)=0.05. Shots: {00,00,00,01,10} (M=5).  *[Ans: F\_XEB = 4×(0.5+0.5+0.5+0.3+0.15)/5 − 1 = 4×0.39 − 1 = 0.56]*

**12.** A d=20 RCS experiment claims classical simulation needs 10^20 operations. Estimate the number of physical qubits if each needs 10 qubits for error correction.  *[Ans: 20 logical × 10 physical = 200 physical qubits minimum (very modest — claims at d=53 need ~530+)]*

**13.** Compute Perm([[1,i],[i,1]]) and its modulus squared.  *[Ans: Perm = 1×1 + i×i = 1 − 1 = 0; |Perm|² = 0 (dark port — no photons detected at this output)]*

**14.** QAOA p=1 for MaxCut on K₃ (triangle). Compute ⟨C⟩ for γ=π/8, β=π/8.  *[Ans: Using K₃ analytic formula: ⟨C⟩ ≈ 2.25/3 ≈ 0.75 approximation ratio — close to 11/16 bound]*

**15.** A QV=64 processor: what is the max circuit dimension n? How many 2-qubit gates in an n×n circuit?  *[Ans: QV=64=2^6 → n=6; a 6×6 circuit has ~3 two-qubit gates per layer × 6 layers ≈ 18 CNOTs]*

**16.** Estimate classical simulation time for a 50-qubit depth-20 circuit using tensor contraction, given peak memory 2^25 × 16 bytes.  *[Ans: 2^25 × 16 = 536 MB (manageable); contraction time depends on tree — typically days on a GPU cluster]*

**17.** Monte Carlo precision ε=0.01: compare classical (10^4 samples) vs quantum (100 queries) at 100× slower gate.  *[Ans: Classical: 10^4 units; Quantum: 100 × 100 = 10^4 units — exactly equal! Need even better hardware or ε≪0.01 to win]*

**18.** The data loading problem: if loading N data points takes O(N), what happens to HHL's speedup?  *[Ans: HHL is O(log N) quantum queries, but loading data takes O(N) → total O(N) = same as classical. Speedup eliminated.]*

**19.** For n=5 boson sampling with a 5×5 Haar-random unitary, estimate the typical output probability.  *[Ans: For Haar-random: p(specific output) ≈ n!/n^n ≈ 1/e^n = 1/e^5 ≈ 0.0067 ≈ 0.67%]*

**20.** Classify as (A) Proven, (B) Complexity-evidence, or (C) Unlikely/unproven: (a) Shor, (b) Grover, (c) QAOA MaxCut, (d) QML image classification, (e) Quantum Monte Carlo finance.  *[Ans: (a) A — unconditional poly vs sub-exp; (b) A — proven quadratic (BBBV); (c) C — no proven advantage; (d) C — dequantised; (e) B — genuine quadratic speedup, hardware requirements unsatisfied]*

## C. Multiple Choice Questions

## Chapter 4 MCQs

<div class="box box-generic">
<p class="box-title"><strong>Q16.  Google's 2019 quantum supremacy experiment used which processor?</strong></p>
<p>(A)  Summit</p>
<p>(B)  Sycamore</p>
<p>(C)  Borealis</p>
<p>(D)  Zuchongzhi</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q17.  Cross-entropy benchmarking (XEB) measures:</strong></p>
<p>(A)  The entropy of the quantum state</p>
<p>(B)  How well measured outcomes match the ideal circuit output distribution</p>
<p>(C)  The fidelity of individual gates</p>
<p>(D)  The quantum volume of the processor</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q18.  Boson sampling hardness is related to computing:</strong></p>
<p>(A)  The determinant of a matrix</p>
<p>(B)  The permanent of a matrix</p>
<p>(C)  The trace of a unitary</p>
<p>(D)  Eigenvalues of a Hamiltonian</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q19.  Xanadu demonstrated Gaussian Boson Sampling with 216 modes in:</strong></p>
<p>(A)  2019</p>
<p>(B)  2020</p>
<p>(C)  2021</p>
<p>(D)  2022</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q20.  Tang's 2018 dequantisation result showed:</strong></p>
<p>(A)  Quantum computers cannot learn from data</p>
<p>(B)  A classical algorithm matches HHL in the quantum-inspired sampling model</p>
<p>(C)  Recommendation systems are NP-complete</p>
<p>(D)  BQP = BPP</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q21.  Quantum Volume QV = 2^n means the processor can reliably execute:</strong></p>
<p>(A)  n-qubit circuits of any depth</p>
<p>(B)  Random n×n square circuits with fidelity &gt; 2/3</p>
<p>(C)  n-qubit circuits for n hours</p>
<p>(D)  2^n different algorithms simultaneously</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q22.  QAOA at p=1 for MaxCut on 3-regular graphs achieves approximation ratio:</strong></p>
<p>(A)  Optimal (exact)</p>
<p>(B)  0.878 (Goemans-Williamson)</p>
<p>(C)  11/16 ≈ 0.688</p>
<p>(D)  1/2 (random partition)</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q23.  The 'data loading problem' in quantum ML refers to:</strong></p>
<p>(A)  Quantum computers cannot store classical data</p>
<p>(B)  Loading N classical data points into a quantum state takes O(N) operations, erasing the speedup</p>
<p>(C)  Quantum data decoheres too quickly</p>
<p>(D)  Classical data is too large for quantum memories</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q24.  Which application has a PROVEN quadratic quantum speedup?</strong></p>
<p>(A)  Portfolio optimisation via QAOA</p>
<p>(B)  Neural network training via quantum circuits</p>
<p>(C)  Monte Carlo estimation via amplitude estimation</p>
<p>(D)  Drug discovery via VQE on NISQ</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q25.  IBM's response to Google's supremacy claim was:</strong></p>
<p>(A)  Google's circuit was too shallow</p>
<p>(B)  Using tensor networks with disk storage, classical simulation takes ~2.5 days (not 10,000 years)</p>
<p>(C)  XEB is not a valid fidelity measure</p>
<p>(D)  Sycamore is not actually a quantum computer</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q26.  The term 'quantum utility' was introduced by:</strong></p>
<p>(A)  Google</p>
<p>(B)  Xanadu</p>
<p>(C)  IBM</p>
<p>(D)  IonQ</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q27.  For practical quantum chemistry advantage, the minimum hardware requirement is approximately:</strong></p>
<p>(A)  50 physical NISQ qubits</p>
<p>(B)  ~1000+ logical qubits with full fault tolerance</p>
<p>(C)  100 qubits with 90% gate fidelity</p>
<p>(D)  10,000 physical qubits with no error correction</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q28.  The Jiuzhang boson sampling experiments were conducted by:</strong></p>
<p>(A)  Google (USA)</p>
<p>(B)  USTC (China)</p>
<p>(C)  Xanadu (Canada)</p>
<p>(D)  IBM (USA)</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q29.  IonQ's 'Algorithmic Qubits (AQ)' metric measures:</strong></p>
<p>(A)  Number of physical ions in the trap</p>
<p>(B)  Number of physical qubits</p>
<p>(C)  Effective logical qubits for benchmark tasks</p>
<p>(D)  Quantum volume divided by 10</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>Q30.  The most honest assessment of near-term (2024–2030) quantum advantage is:</strong></p>
<p>(A)  Quantum computers will solve all NP problems within 5 years</p>
<p>(B)  No practical advantage for real commercial problems; first real advantage in quantum chemistry ~2030–2035</p>
<p>(C)  Quantum computers already solve problems classical computers cannot in practice</p>
<p>(D)  Quantum advantage is impossible due to decoherence</p>
</div>

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  MCQ Answer Key — Chapter 3 (Q1–Q15)</strong></p>
<p>Q1:A   Q2:C   Q3:B   Q4:B   Q5:D   Q6:C   Q7:B   Q8:C   Q9:C   Q10:D</p>
<p>Q11:B   Q12:B   Q13:B   Q14:B   Q15:C</p>
</div>

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  MCQ Answer Key — Chapter 4 (Q16–Q30)</strong></p>
<p>Q16:B   Q17:B   Q18:B   Q19:D   Q20:B   Q21:B   Q22:C   Q23:B   Q24:C   Q25:B</p>
<p>Q26:C   Q27:B   Q28:B   Q29:C   Q30:B</p>
</div>

## D. Theory Questions

## Chapter 4: Theory Questions

**Q11.** Explain the Google Sycamore experiment. What is cross-entropy benchmarking (XEB), and how does it verify that a quantum processor executes the target circuit faithfully?

**Q12.** What is random circuit sampling (RCS)? Why is it conjectured to be classically hard, and what complexity-theoretic assumption underlies this conjecture? Why is this different from proven lower bounds?

**Q13.** Describe the Aaronson-Arkhipov result on boson sampling. What is the connection between the permanent of a matrix and the output probabilities of a linear-optical network?

**Q14.** Critically evaluate the claim that QAOA provides quantum advantage for combinatorial optimisation. What are the known theoretical limitations, and what would a genuine quantum advantage in optimisation require?

**Q15.** Explain Quantum Volume as a hardware benchmark. Why is it better than raw qubit count? What are its limitations, and what alternative benchmarks address those limitations?

**Q16.** Discuss the 'dequantisation' phenomenon. How did Tang's result (2018) affect the field of quantum machine learning, and what does it tell us about the nature of quantum speedups?

**Q17.** What conditions are necessary for a genuine practical quantum advantage? Discuss: (a) fault tolerance requirements, (b) input/output bottlenecks, (c) the need for quantum-native problems.

**Q18.** Describe the IBM-Google debate over quantum supremacy (2019). What were the key points of disagreement, and how has subsequent research resolved the controversy?

**Q19.** Explain why quantum computers offer a genuine quadratic speedup for Monte Carlo estimation (via amplitude estimation). What hardware requirements must be met for this to be practically useful in finance?

**Q20.** Summarise the current state of quantum advantage: what has been demonstrated, what is conjectured, and what is pure hype? Use specific examples from optimisation, chemistry, ML, and cryptography.

## E. Programming / Research Assignments

<div class="box box-real-world">
<p class="box-title"><strong>🌐  Assignment 2: Critical Analysis of a Quantum Advantage Claim</strong></p>
<p>Select ONE of the following papers and write a critical analysis (3–4 pages):</p>
<p>Option A: Google, 'Quantum supremacy using a programmable superconducting processor', Nature 2019</p>
<p>Option B: Madsen et al. (Xanadu), 'Quantum computational advantage with a programmable photonic processor', Nature 2022</p>
<p>Option C: IBM Research, 'Evidence for the utility of quantum computing before fault tolerance', Nature 2023</p>
<p>Your analysis must address:</p>
<p>(a) What task was performed, and why is it claimed to be classically hard?</p>
<p>(b) What complexity-theoretic assumptions underlie the hardness claim?</p>
<p>(c) What were the main criticisms, and how valid are they?</p>
<p>(d) Does this constitute 'quantum advantage' in a practically meaningful sense?</p>
<p>(e) What would a more convincing demonstration of practical quantum advantage require?</p>
</div>

## F. Project Suggestions
