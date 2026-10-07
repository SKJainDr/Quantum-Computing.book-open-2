## Cover Page

**Q. C. Series | Volume II**

<img class="fig-img" src="content/images/image2.png" alt="figure">

**QUANTUM ALGORITHMS**

**&amp; COMPLEXITY**

**Shor, Grover, QFT, HHL, VQE &amp; Quantum Complexity Theory**

## Dedicated to

## My Strong Father

<img class="fig-img" src="content/images/image3.png" alt="figure">

## Shri Anand Prakash Jain

**(Proudly served 39 years in the Indian Army and the Border Security Force)**

**Q.C. Series | Volume II**

## QUANTUM ALGORITHMS &amp; COMPLEXITY

## Shor, Grover, QFT, HHL, VQE &amp; Quantum Complexity Theory

A Comprehensive University Textbook for the M.Sc. Physics Program with a Specialization in Quantum Computing: Volume 2

**Dr. Sanjeev Kumar Jain**

Associate Professor and Ex-Head,

Department of Applied Sciences and Humanities  ·  Faculty of Science

Invertis University, Bareilly (U.P.), India

**An Overview**

| **Programme** | **M.Sc. Physics — Quantum Computing Specialization** |
|---|---|
| **Semester** | Semester IV |
| **This volume for Course** | **Quantum Algorithms and Complexity** |
| **Chapter 1** | Shor's Algorithm, Amplitude Amplification &amp; HHL |
| **Chapter 2** | Quantum Simulation &amp; Advanced Circuit Design |
| **Chapter 3** | Complexity Classes, Query Complexity &amp; the Polynomial Method |
| **Chapter 4** | Quantum Advantage — Theory, Evidence &amp; Reality |
| **Chapter 5** | Quantum Error Correction Principles |
| **Chapter 6** | Surface Codes and Fault-Tolerant Computing |
| **Chapter 7** | Advanced Variational and Hybrid Algorithms |
| **Chapter 8** | Barren Plateaus, Error Mitigation &amp; the Limits of NISQ Advantage |
| **Chapter 9** | Quantum Machine Learning — Kernels, Neural Networks &amp; Gradients |
| **Chapter 10** | Data Encoding, Transfer Learning, Dequantisation &amp; PennyLane |
| **Platform** | Python / Qiskit 1.x · Qiskit Nature · PennyLane + PyTorch · IBM Quantum Hardware |
| **Features** | Each Chapter has: 6 types of Information boxes, 11+ Recap Qs, 8 Solved Examples · 15+ MCQs · 10+ Theory Qs · 8+ Problems · Figures, Programming Assignments and Project suggestions. |

## PREFACE

<div class="box box-generic">
<p class="box-title"><strong>🧭 A Note on This Expanded Edition</strong></p>
<p>This edition substantially expands the original volume: every chapter now carries at least six original figures, a full complement of learning-objective, key-concept, real-world, warning, mathematics, definition, tip, and roadmap boxes, and one or more new sections extending each chapter's coverage toward the current state of the field. A new closing chapter (Chapter 11) surveys topological qubits, quantum networking, architecture comparisons, and India's quantum computing ecosystem, and several appendices add a quick-reference complexity table, a notation glossary, a software setup guide, twenty additional solved problems, and further supplementary figures. The aim throughout has been to deepen and modernise the material while preserving the structure and voice of the original.</p>
</div>

This textbook is the second volume of the Quantum Computing Series and is written for students of M.Sc. Physics who have completed the foundational course (Quantum Computers, Volume I) and are ready to study the algorithmic and complexity-theoretic heart of the field. The goal is dual: to provide rigorous derivations of the major quantum algorithms and their complexity-theoretic guarantees, and to develop genuine practical implementation skill using Qiskit and PennyLane. Both goals are essential — theory without implementation leads to abstract knowledge that cannot be deployed; implementation without theory leads to running circuits without understanding why, or whether, they actually provide an advantage.

Quantum algorithms are where the promise of quantum computing is either realised or exposed as hype. This volume treats Shor's algorithm and Grover's search as the historical anchors, then builds outward to quantum simulation, the complexity classes (BQP, QMA) that formally describe what quantum computers can and cannot do, the sobering realities of quantum advantage claims, quantum error correction and fault tolerance, and the variational and machine-learning algorithms that dominate the current NISQ era. For India's M.Sc. students, the National Quantum Mission (NQM, ₹6,003 crore, 2023–2031) continues to create career opportunities in quantum algorithm design, error correction research, and quantum software — at TCS, Wipro, Infosys, QpiAI, BosonQ Psi, and at the NQM hubs at IITs, IISc, and DRDO.

### How This Textbook Is Structured

The textbook is divided into five units spanning ten chapters, that build up the subject interestingly and thoroughly:

• **Unit I (Chapters 1–2):** Algorithms &amp; Simulation. Chapter 1 develops Shor's algorithm, generalised amplitude amplification, quantum walks, and the HHL algorithm for linear systems. Chapter 2 covers Hamiltonian simulation via Trotter-Suzuki formulas and qubitisation, and the variational algorithms used for quantum chemistry and condensed matter simulation.

• **Unit II (Chapters 3–4):** Complexity &amp; Advantage. Chapter 3 develops the complexity classes P, BPP, BQP, and QMA, quantum query complexity, and the polynomial and adversary lower-bound methods. Chapter 4 critically examines quantum advantage claims — Google's Sycamore experiment, boson sampling — and the hardware benchmarks used to assess real devices.

• **Unit III (Chapters 5–6):** Error Correction. Chapter 5 develops the principles of quantum error correction — the bit-flip, phase-flip, Shor, and Steane codes, and the Knill-Laflamme conditions. Chapter 6 covers the surface code, the threshold theorem, and the resource estimates for fault-tolerant quantum computing.

• **Unit IV (Chapters 7–8):** Variational Algorithms. Chapter 7 covers QAOA, advanced VQE for quantum chemistry, and classical optimiser strategies. Chapter 8 confronts the barren plateau problem and surveys quantum error mitigation techniques, closing with an honest assessment of NISQ-era limits.

• **Unit V (Chapters 9–10):** Quantum Machine Learning. Chapter 9 develops quantum kernels, QSVMs, and quantum neural networks with the parameter-shift rule. Chapter 10 covers data encoding strategies, quantum transfer learning, dequantisation results, and hybrid PennyLane–PyTorch programming.

**The laboratory manual - Quantum Computing Lab II - supports the laboratory part, and that practical course should be run together with the theory course.**

### Pedagogical Features

Each chapter contains:

•  📜 Anecdote boxes: Historical stories and scientists — making the subject interesting.

•  🔑 Key Concept boxes: Formal definitions, precisely stated.

•  🌐 Real World boxes: Applications in industry, government, India's quantum mission.

•  **⚠** Warning boxes: Common misconceptions and pitfalls.

•  Example boxes: 8+ worked examples per chapter, step-by-step.

•  Equation boxes: Key mathematical formulas with physical interpretation.

•  Dark code blocks: Complete Qiskit programs with line-by-line commentary.

•  Figures: Labelled, captioned figures throughout.

- Each chapter closes with: 11+ Recap Qs (with model answers) · 8 Solved Problems · 8+ Unsolved Problems (answers in brackets) · 15+ MCQs (answers collected at the very end of the chapter) · 8+ Theory Questions · Programming/Research Assignments · Project Suggestions — a focused review designed for strengthening understanding, application and for out of the classroom preparation.

The reader is encouraged to discuss among colleagues and try out all the examples, questions, program codes and the problems given at the end of a chapter. Consider them a part of the learning that can be gained from the chapter. Assignments and projects are designed for field learning.

### A note on honesty

Quantum algorithms are a field where media coverage often outpaces scientific reality, perhaps more than any other area of quantum computing. This textbook makes a deliberate effort to distinguish proven exponential speedups (Shor's algorithm) from conditional and caveat-laden speedups (HHL), from quadratic speedups (Grover, amplitude amplification), from claimed-but-contested advantage (some variational and quantum-ML applications), and from advantage that has since been dequantised entirely. Students who understand these distinctions will be better researchers, better engineers, and better communicators of science to the public.

The generation studying from this textbook will be among the first Indian-trained quantum scientists to work on nationally funded quantum hardware, quantum communication networks, and quantum software. It is hoped that this book contributes, in a small way, to prepare them for that responsibility.

— ***Dr. Sanjeev Kumar Jain***

## The various information boxes in this textbook

<div class="box box-key-concept">
<p class="box-title"><strong>🔑 KEY CONCEPT BOXES</strong></p>
<p>Contain the formal mathematical definitions, theorems, and circuit constructions that you must know for examinations. Read these carefully and ensure you can reproduce the key equations.</p>
</div>

<div class="box box-anecdote">
<p class="box-title"><strong>📜 ANECDOTE BOXES</strong></p>
<p>Provide historical context and the human stories behind the science. These are not examinable but are important for understanding how the field developed and for communicating science to non-specialists.</p>
</div>

<div class="box box-real-world">
<p class="box-title"><strong>🌍 REAL WORLD BOXES</strong></p>
<p>Connect theory to current industrial applications, national programmes (especially India's NQM), and career-relevant context. These appear in examination short-answer questions.</p>
</div>

<div class="box box-warning">
<p class="box-title"><strong>⚠️ WARNING BOXES</strong></p>
<p>Explicitly correct common misconceptions. If a concept appears in a warning box, it is almost certainly something that students — and even professionals — frequently get wrong.</p>
</div>

<div class="box box-math">
<p class="box-title"><strong>🧮 MATHEMATICS BOXES</strong></p>
<p>Contain worked derivations and numerical examples. Work through these step-by-step, covering the solution and attempting each step yourself first.</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong># CODE BOXES (Dark background)</strong></p>
<p>Contain working Qiskit/Python code. Every code box can be run on IBM Quantum (free account at quantum.ibm.com). Running the code is the best way to develop intuition.</p>
<p><strong>🎯  LEARNING OBJECTIVES BOXES</strong></p>
<p>Open every chapter. State the specific, measurable outcomes you should master by the end of the chapter - use them as a checklist before and after reading.</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>📘 DEFINITION / THEOREM BOXES</strong></p>
<p>Give a formal statement of a definition, theorem, or protocol, stated precisely and concisely, separated from the surrounding narrative discussion.</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>💡 TIP BOXES</strong></p>
<p>Offer practical advice on problem-solving, hardware intuition, choosing parameters, or working efficiently with Qiskit and PennyLane.</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong>🧭 ROADMAP / PROTOCOL BOXES</strong></p>
<p>Give a short orientation to what a section or protocol covers before you dive into the detail, or point forward to where a concept reappears later in the book.</p>
</div>

## Table of Contents

| Contents | Page |
|---|---|
| Dedicated to | ii |
| Title: QUANTUM ALGORITHMS &amp; COMPLEXITY: Shor, Grover, QFT, HHL, VQE &amp; Quantum Complexity Theory | . iii |
| PREFACE | iv |
| How This Textbook Is Structured | iv |
| Pedagogical Features | v |
| A note on honesty | v |
| The various information boxes in this textbook | vi |
| Table of Contents | viii |
| LIST OF FIGURES | xix |
| LIST OF SYMBOLS AND NOTATION | xxi |
| CHAPTER 1: Shor's Algorithm, Amplitude Amplification &amp; HHL | 1 |
| 1.1 Shor's Factoring Algorithm — Complete Treatment | 1 |
| 1.1.1 RSA Cryptography and the Hardness of Factoring | 2 |
| 1.1.2 Reduction of Factoring to Order-Finding | 2 |
| 1.1.3 Quantum Order-Finding via QPE: Modular Exponentiation Circuit | 3 |
| 1.1.4 Period Finding with QFT: Continued Fractions Recovery | 4 |
| 1.1.5 Shor's Complexity: O((log N)³) Quantum Gates vs Sub-exponential Classical | 5 |
| 1.1.6 Qiskit Implementation for Small N (15, 21, 35) | 6 |
| 1.1.7 Post-Quantum Cryptography: CRYSTALS-Kyber and CRYSTALS-Dilithium | 7 |
| 1.2 Amplitude Amplification and Quantum Walks | 8 |
| 1.2.1 Generalised Amplitude Amplification | 9 |
| 1.2.2 Quantum Walk on Graphs: Coined and Continuous-Time | 10 |
| Coined Quantum Walk | 10 |
| Continuous-Time Quantum Walk | 11 |
| 1.2.3 Quantum Walk Search Algorithm: O(√N) Queries on Well-Connected Graphs | 12 |
| 1.2.4 Quantum Walk for Element Distinctness: O(N^{2/3}) — Better than Grover | 12 |
| 1.3 Linear Systems — HHL Algorithm | 13 |
| 1.3.1 Problem Setting: Classical O(N³) vs Quantum O(log N) | 14 |
| 1.3.2 HHL Circuit: QPE for Eigenvalue Estimation, Conditional Rotation, Uncomputation | 14 |
| 1.3.3 Caveats: Input Preparation, Output Measurement, Sparsity and Condition Number | 16 |
| 1.3.4 Dequantisation Results: Classical Algorithms Matching HHL | 16 |
| 1.4 Quantum Counting and Amplitude Estimation | 17 |
| 1.4.1 From Amplitude Amplification to Counting | 17 |
| 1.4.2 The Estimation Circuit and Its Precision | 18 |
| 1.4.3 Applications: Estimating Without Full Search | 18 |
| 1.4.4 Worked Example: Counting Solutions to a Toy Search Problem | 18 |
| RECAP — SHORT ANSWER QUESTIONS &amp; MODEL ANSWERS | 21 |
| A. Solved Problems | 23 |
| B. Unsolved Problems | 26 |
| C. Multiple Choice Questions | 27 |
| D. Theory Questions | 29 |
| E. Programming Assignments | 30 |
| F. Project Suggestions | 31 |
| CHAPTER 2: Quantum Simulation &amp; Advanced Circuit Design | 32 |
| 2.1 Product Formula (Trotter-Suzuki): First and Second Order | 32 |
| 2.1.1 First-Order Lie-Trotter Formula | 33 |
| 2.1.2 Second-Order Suzuki Formula (Palindromic) | 33 |
| 2.1.3 Higher-Order Suzuki Formulas and the Trotter-Error Zoo | 34 |
| 2.2 Qubitisation and Quantum Signal Processing | 35 |
| 2.2.1 Block-Encoding: Embedding the Hamiltonian in a Larger Unitary | 35 |
| 2.2.2 The Qubitisation Walk Operator | 36 |
| 2.2.3 Quantum Signal Processing (QSP) for Near-Optimal Simulation | 36 |
| 2.3 Variational Quantum Simulation | 37 |
| 2.3.1 Variational Principle: McLachlan Equations of Motion | 37 |
| 2.3.2 Variational Quantum Eigensolver (VQE): Static Ground States | 37 |
| 2.4 Quantum Chemistry: VQE for H₂ and LiH | 39 |
| 2.4.1 The Electronic Structure Problem and Second Quantisation | 39 |
| 2.4.2 Jordan-Wigner Transformation: Fermions to Qubits | 39 |
| 2.4.3 UCCSD Ansatz: Unitary Coupled Cluster | 40 |
| 2.4.4 VQE for H₂ and LiH: Numerical Results | 40 |
| 2.5 Many-Body Physics: Ising and Hubbard Model Trotter Simulation | 42 |
| 2.5.1 Transverse-Field Ising Model (TFIM): Quantum Phase Transitions | 43 |
| 2.5.2 Fermi-Hubbard Model: Mott Physics and Strong Correlations | 45 |
| 2.6 Beyond Fixed Ansatze: ADAPT-VQE and the Software Ecosystem | 47 |
| 2.6.1 The ADAPT-VQE Growth Loop | 47 |
| 2.6.2 The Wider Software Ecosystem | 48 |
| 2.6.3 Worked Example: Trotter Error Scaling in Practice | 48 |
| RECAP — SHORT ANSWER QUESTIONS &amp; MODEL ANSWERS | 50 |
| A. Solved Problems | 52 |
| B. Unsolved Problems | 55 |
| C. Multiple Choice Questions | 56 |
| D. Theory Questions | 58 |
| E. Programming Assignments | 59 |
| F. Project Suggestions | 60 |
| References and Further Reading — Chapters 1–2 | 62 |
| CHAPTER 3: Quantum Complexity Theory | 63 |
| 3.1 Foundations of Classical Complexity Theory | 64 |
| 3.1.1 The Turing Machine Model | 64 |
| 3.1.2 The Class P: Polynomial Time | 65 |
| 3.1.3 The Class NP: Non-deterministic Polynomial Time | 65 |
| 3.1.4 The Class BPP: Bounded-Error Probabilistic Polynomial Time | 65 |
| 3.2 BQP: Bounded-Error Quantum Polynomial Time | 66 |
| 3.2.1 Problems Known to Be in BQP | 66 |
| 3.2.2 The BQP vs BPP Question and Known Containments | 66 |
| 3.3 QMA: Quantum Merlin-Arthur and the Quantum NP | 67 |
| 3.3.1 The k-Local Hamiltonian Problem: QMA-Complete | 68 |
| 3.3.2 Other QMA-Complete Problems | 69 |
| 3.4 QCMA, PP, and the Polynomial Hierarchy | 69 |
| 3.4.1 QCMA: Classical Witnesses for Quantum Verifiers | 69 |
| 3.4.2 PP: Unbounded Error Probabilistic Polynomial Time | 69 |
| 3.4.3 The Polynomial Hierarchy and Boson Sampling | 69 |
| 3.5 Quantum Circuit Complexity and Black Holes | 70 |
| 3.6 Quantum Query Complexity | 70 |
| 3.6.1 The Quantum Query Model | 70 |
| 3.6.2 Key Query Complexity Separations | 71 |
| 3.7 The Polynomial Method | 71 |
| 3.8 The BBBV Theorem and the Adversary Method | 72 |
| 3.8.1 The Quantum Adversary Method | 73 |
| 3.9 Simon's Problem: The First Exponential Separation | 74 |
| 3.10 The Complexity Landscape, Simon’s Algorithm, and the Quantum PCP Frontier | 76 |
| 3.10.1 The Class Landscape at a Glance | 76 |
| 3.10.2 Simon’s Algorithm Revisited | 76 |
| 3.10.3 The Quantum PCP Conjecture and NLTS | 77 |
| 3.10.4 Worked Example: The Deutsch-Jozsa Algorithm | 78 |
| RECAP — SHORT ANSWER QUESTIONS &amp; MODEL ANSWERS | 80 |
| A. Solved Problems | 82 |
| B. Unsolved Problems | 84 |
| C. Multiple Choice Questions | 85 |
| D. Theory Questions | 87 |
| E. Programming / Research Assignments | 88 |
| F. Project Suggestions | 88 |
| References and Further Reading — Chapter 3 | 89 |
| CHAPTER 4: Quantum Advantage: Theory, Evidence &amp; Reality | 89 |
| 4.1 What Is Quantum Advantage? | 90 |
| 4.2 Google 2019: Random Circuit Sampling and Sycamore | 90 |
| 4.2.1 What Is Random Circuit Sampling? | 91 |
| 4.2.2 Google Sycamore: Technical Details | 91 |
| 4.2.3 The IBM Rebuttal and Resolution | 92 |
| 4.3 Boson Sampling: Photonic Quantum Advantage | 92 |
| 4.3.1 The Aaronson-Arkhipov Theorem | 92 |
| 4.3.2 Major Boson Sampling Experiments | 93 |
| 4.4 Quantum Advantage in Optimisation, ML, and Finance: Hype vs Reality | 93 |
| 4.4.1 Quantum Optimisation | 94 |
| 4.4.2 Quantum Machine Learning | 94 |
| 4.4.3 Quantum Finance | 94 |
| 4.5 Hardware Benchmarks: Quantum Volume and Beyond | 95 |
| 4.5.1 Quantum Volume | 95 |
| 4.5.2 Other Hardware Benchmarks | 96 |
| 4.6 The Landscape of Claimed Quantum Advantage: Evidence, Timeline, and Verifiability | 98 |
| 4.6.1 Random Circuit Sampling and Its Statistical Fingerprint | 98 |
| 4.6.2 Boson Sampling: A Photonic Alternative | 98 |
| 4.6.3 A Timeline, and an Honest Scorecard | 99 |
| 4.6.4 Verifiable Advantage and Certified Randomness | 100 |
| 4.6.5 Classical Simulation Is a Moving Target | 101 |
| RECAP — SHORT ANSWER QUESTIONS &amp; MODEL ANSWERS | 103 |
| A. Solved Problems | 105 |
| B. Unsolved Problems | 106 |
| C. Multiple Choice Questions | 106 |
| D. Theory Questions | 109 |
| E. Programming / Research Assignments | 110 |
| F. Project Suggestions | 110 |
| References and Further Reading — Chapter 4 | 110 |
| CHAPTER 5: Quantum Error Correction: Principles, Codes &amp; Stabilisers | 112 |
| 5.1 Why Quantum Error Correction Appears Impossible | 112 |
| 5.1.1 Obstacle 1: The No-Cloning Theorem | 113 |
| 5.1.2 Obstacle 2: Continuous Errors | 113 |
| 5.1.3 Obstacle 3: Measurement Destroys Information | 114 |
| 5.2 The 3-Qubit Bit-Flip Code | 114 |
| 5.2.1 Encoding | 114 |
| 5.2.2 Syndrome Measurement and Error Table | 115 |
| 5.2.3 The Discretisation Miracle | 115 |
| 5.3 The 3-Qubit Phase-Flip Code and Shor's 9-Qubit Code | 115 |
| 5.3.1 Phase-Flip Errors and the Phase-Flip Code | 115 |
| 5.3.2 Shor's 9-Qubit Code: Protecting Against Both X and Z | 116 |
| 5.4 The Knill-Laflamme Quantum Error Correction Conditions | 117 |
| 5.5 The Stabiliser Formalism | 118 |
| 5.5.1 The Pauli Group | 118 |
| 5.5.2 Stabiliser Groups and Codes | 118 |
| 5.5.3 Syndrome Measurement in the Stabiliser Picture | 118 |
| 5.6 CSS Codes and the [[7,1,3]] Steane Code | 119 |
| 5.6.1 The [[7,1,3]] Steane Code | 119 |
| 5.6.2 Transversal Gates on the Steane Code | 120 |
| 5.6.3 The [[5,1,3]] Perfect Code | 121 |
| 5.7 Foundations Revisited: Knill-Laflamme, Stabilisers, and Quantum LDPC Codes | 123 |
| 5.7.1 The Knill-Laflamme Conditions | 123 |
| 5.7.2 Why the Stabiliser Formalism Works | 123 |
| 5.7.3 Quantum LDPC Codes: Reducing the Overhead | 124 |
| 5.7.4 Worked Example: Distance Scaling for Small Codes | 125 |
| RECAP — SHORT ANSWER QUESTIONS &amp; MODEL ANSWERS | 127 |
| A. Solved Problems | 129 |
| B. Unsolved Problems | 131 |
| C. Multiple Choice Questions | 132 |
| D. Theory Questions | 134 |
| E. Programming / Research Assignments | 134 |
| F. Project Suggestions | 135 |
| References and Further Reading — Chapter 5 | 136 |
| CHAPTER 6: Surface Codes, Threshold Theorem &amp; Fault-Tolerant Architecture | 136 |
| 6.1 From Stabiliser Codes to Topological Codes | 137 |
| 6.2 The Surface Code | 137 |
| 6.2.1 Lattice Structure and Qubit Count | 138 |
| 6.2.2 Stabiliser Generators | 139 |
| 6.2.3 Logical Operators — Topological Strings | 139 |
| 6.3 Syndrome Extraction and Decoding | 139 |
| 6.3.1 Minimum Weight Perfect Matching (MWPM) Decoder | 140 |
| 6.4 The Threshold Theorem: The Bedrock of Fault-Tolerant Computing | 140 |
| 6.5 Magic State Distillation: The T Gate Problem | 142 |
| 6.5.1 Magic States and Gate Teleportation | 142 |
| 6.5.2 The Bravyi-Kitaev 15-to-1 Distillation Protocol | 142 |
| 6.6 Resource Estimates: Factoring RSA-2048 | 143 |
| 6.7 IBM 2023: First Experimental Evidence of the Threshold Theorem | 144 |
| 6.8 Full Fault-Tolerant Quantum Computer Architecture | 144 |
| 6.8.1 Concatenated Codes vs Surface Codes | 145 |
| 6.9 Engineering Reality: Real-Time Decoding and Code Comparisons | 147 |
| 6.9.1 The Decoding Bottleneck | 147 |
| 6.9.2 Concatenated vs Surface Codes, Side by Side | 147 |
| 6.9.3 Worked Example: Surface-Code Overhead in Numbers | 148 |
| RECAP — SHORT ANSWER QUESTIONS &amp; MODEL ANSWERS | 150 |
| A. Solved Problems | 152 |
| B. Unsolved Problems | 153 |
| C. Multiple Choice Questions | 154 |
| D. Theory Questions | 156 |
| E. Programming / Research Assignments | 157 |
| F. Project Suggestions | 158 |
| References and Further Reading — Chapter 6 | 159 |
| CHAPTER 7: QAOA, VQE for Molecular Systems &amp; Optimiser Strategies | 160 |
| 7.1 The NISQ Era and the Variational Approach | 160 |
| 7.2 The Quantum Approximate Optimisation Algorithm (QAOA) | 161 |
| 7.2.1 General QAOA Framework | 162 |
| 7.2.2 QAOA Circuit for MaxCut — K₃ Example | 162 |
| 7.3 QAOA Applications: MaxCut, Portfolio Optimisation, and TSP | 163 |
| 7.3.1 MaxCut Problem Formulation | 163 |
| 7.3.2 Portfolio Optimisation | 163 |
| 7.3.3 Travelling Salesman Problem | 164 |
| 7.4 VQE for Molecular Systems: UCCSD, Active Space &amp; Convergence | 164 |
| 7.4.1 The Active Space Approximation CAS(m,n) | 164 |
| 7.4.2 The UCCSD Ansatz | 165 |
| 7.4.3 Classical Benchmarks: CCSD(T) | 165 |
| 7.5 Classical Optimiser Strategies for Variational Algorithms | 166 |
| 7.5.1 The Parameter Shift Rule: Exact Quantum Gradients | 166 |
| 7.5.2 Gradient-Free Optimisers | 166 |
| 7.5.3 Gradient-Based Optimisers | 167 |
| 7.6 Hardware-Efficient Ansatz: When UCCSD Is Too Deep | 167 |
| 7.7 Beyond a Single Problem: Applications, Gradients, Ansatze, and Quantum Annealing | 169 |
| 7.7.1 Where the Same Recipe Applies | 169 |
| 7.7.2 The Parameter-Shift Rule | 169 |
| 7.7.3 Ansatz Choice: Chemical Motivation vs Circuit Depth | 170 |
| 7.7.4 Quantum Annealing: The Adiabatic Cousin of QAOA | 171 |
| 7.7.5 Worked Example: How Much Does Depth Actually Buy You? | 172 |
| RECAP — SHORT ANSWER QUESTIONS &amp; MODEL ANSWERS | 174 |
| A. Solved Problems | 176 |
| B. Unsolved Problems | 178 |
| C. Multiple Choice Questions | 179 |
| D. Theory Questions | 181 |
| E. Programming / Research Assignments | 182 |
| F. Project Suggestions | 182 |
| References and Further Reading — Chapter 7 | 183 |
| CHAPTER 8: Barren Plateaus, Expressibility &amp; Near-Term Quantum Advantage | 183 |
| 8.1 Barren Plateaus: The Trainability Crisis in Variational Quantum Algorithms | 184 |
| 8.1.1 The McClean et al. Theorem (2018) | 184 |
| 8.1.2 When Do Barren Plateaus Occur? | 185 |
| 8.1.3 Noise-Induced Barren Plateaus | 185 |
| 8.2 Barren Plateau Mitigation Strategies | 186 |
| 8.2.1 Local Cost Functions | 186 |
| 8.2.2 Layerwise Training and Identity Initialisation | 186 |
| 8.2.3 Structure-Preserving Ansatze | 186 |
| 8.3 Quantum Error Mitigation (QEM) | 187 |
| 8.3.1 Zero-Noise Extrapolation (ZNE) | 187 |
| 8.3.2 Probabilistic Error Cancellation (PEC) | 187 |
| 8.3.3 Classical Shadows | 188 |
| 8.4 Near-Term Quantum Advantage: A Rigorous Assessment | 188 |
| 8.5 Theoretical Depth: QAOA, Adiabaticity, and Optimisation Landscapes | 190 |
| 8.5.1 Connection to Adiabatic Quantum Computing | 190 |
| 8.6 The Practical Toolkit: Mitigation Strategies, the Road Ahead, and an Honest Scorecard | 191 |
| 8.6.1 Comparing Barren-Plateau Mitigations | 191 |
| 8.6.2 Error Mitigation Without Error Correction | 191 |
| 8.6.3 The Road from NISQ to Fault Tolerance | 192 |
| 8.6.4 An Honest Scorecard | 193 |
| 8.6.5 Worked Example: The Expressibility-Trainability Tension | 193 |
| RECAP — SHORT ANSWER QUESTIONS &amp; MODEL ANSWERS | 195 |
| A. Solved Problems | 197 |
| B. Unsolved Problems | 198 |
| C. Multiple Choice Questions | 199 |
| D. Theory Questions | 201 |
| E. Programming / Research Assignments | 202 |
| F. Project Suggestions | 202 |
| References and Further Reading — Chapter 8 | 203 |
| CHAPTER 9: Quantum Kernels and Quantum Neural Networks | 203 |
| 9.1 Quantum Feature Maps and the Kernel Trick | 204 |
| 9.1.1 The Quantum Feature Map | 204 |
| 9.1.2 The Quantum Kernel Function | 205 |
| 9.1.3 Quantum Support Vector Machine (QSVM) | 206 |
| 9.1.4 Kernel Alignment and Trainable Kernels | 207 |
| 9.2 Quantum Neural Networks: Parameterised Circuits as Function Approximators | 208 |
| 9.2.1 QNN Architecture: Encoding, Variational, and Measurement Layers | 208 |
| 9.2.2 Universal Approximation Theorem for QNNs | 209 |
| 9.2.3 Expressibility and Entanglement Capability | 210 |
| 9.2.4 QNN Training: Loss Functions and Optimisers | 211 |
| 9.3 The Parameter-Shift Rule: Exact Quantum Gradients | 211 |
| 9.3.1 Derivation of the Parameter-Shift Rule | 212 |
| 9.3.2 Generalised Parameter-Shift Rules | 213 |
| 9.4 Quantum Generative Models: Born Machines | 214 |
| 9.4.1 The Born Machine Recipe | 214 |
| 9.4.2 Worked Example: When Do Quantum Kernels Actually Help? | 215 |
| RECAP — SHORT ANSWER QUESTIONS &amp; MODEL ANSWERS | 216 |
| A. Solved Problems | 218 |
| B. Unsolved Problems | 221 |
| C. Multiple Choice Questions | 223 |
| D. Theory Questions | 225 |
| E. Programming Assignments | 226 |
| F. Project Suggestions | 227 |
| CHAPTER 10: Quantum Transfer Learning, Data Encoding Strategies &amp; Dequantisation | 228 |
| 10.1 Data Encoding Strategies: Angle, Amplitude, Basis, and Re-uploading | 228 |
| 10.1.1 Angle Encoding | 229 |
| 10.1.2 Amplitude Encoding | 229 |
| 10.1.3 Basis Encoding | 230 |
| 10.1.4 Data Re-uploading: One Qubit Is Enough | 230 |
| 10.2 Quantum Transfer Learning | 231 |
| 10.2.1 Architecture: Classical Backbone + Quantum Head | 231 |
| 10.2.2 Theoretical Motivation: Why a Quantum Head? | 233 |
| 10.3 Dequantisation: When Classical ML Matches Quantum ML | 233 |
| 10.3.1 The Sample-and-Query (SQ) Data Model | 233 |
| 10.3.2 Tang’s Classical Algorithms for Quantum ML Tasks | 234 |
| 10.3.3 Quantum vs Classical ML: An Honest Comparison Map | 235 |
| 10.4 PennyLane Integration with PyTorch: Hybrid Quantum-Classical Pipelines | 236 |
| 10.4.1 The PennyLane QNode | 236 |
| 10.4.2 Automatic Differentiation Through the Quantum Circuit | 237 |
| 10.4.3 Practical PennyLane Features for QML | 238 |
| 10.5 Careers, Responsibility, and India’s Quantum Mission | 240 |
| 10.5.1 A Student’s Roadmap | 240 |
| 10.5.2 India’s National Quantum Mission | 240 |
| 10.5.3 Worked Example: Does More Encoding Depth Always Help? | 241 |
| RECAP — SHORT ANSWER QUESTIONS &amp; MODEL ANSWERS | 242 |
| A. Solved Problems | 244 |
| B. Unsolved Problems | 248 |
| C. Multiple Choice Questions | 249 |
| D. Theory Questions | 252 |
| E. Programming Assignments | 253 |
| F. Project Suggestions | 254 |
| REFERENCES AND FURTHER READING | 256 |
| CHAPTER 11: Current Frontiers and the Path Ahead | 258 |
| 11.1 Topological Qubits and Non-Abelian Anyons | 258 |
| 11.2 Quantum Networking and the Quantum Internet | 259 |
| 11.3 Comparing Qubit Architectures | 260 |
| 11.4 Recent Experimental Milestones in Error Correction | 261 |
| 11.5 India’s Quantum Computing Ecosystem | 262 |
| 11.6 Where This Leaves Us: A Final Synthesis | 263 |
| 11.7 Quantum-Safe Cryptography and the Migration Challenge | 264 |
| 11.8 Quantum Computing Across Industry | 265 |
| RECAP — SHORT ANSWER QUESTIONS &amp; MODEL ANSWERS | 267 |
| APPENDIX C: Quick Reference — Algorithms, Complexity, and Speedups at a Glance | 268 |
| APPENDIX D: Glossary of Notation and Symbols | 268 |
| APPENDIX E: Software and Hardware Access Guide | 269 |
| E.1 Qiskit (IBM) | 269 |
| E.2 PennyLane (Xanadu) | 269 |
| E.3 Amazon Braket, Google Cirq, and Others | 269 |
| APPENDIX F: Further Reading, Organised by Chapter | 269 |
| APPENDIX G: Twenty Additional Solved Problems, Organised by Chapter | 271 |
| G.1 Chapter 1: Shor, Amplitude Amplification, and HHL | 271 |
| G.2 Chapter 2: Quantum Simulation and Advanced Circuit Design | 271 |
| G.3 Chapter 3: Quantum Complexity Theory | 272 |
| G.4 Chapter 4: Quantum Advantage | 272 |
| G.5 Chapter 5: Quantum Error Correction | 273 |
| G.6 Chapter 6: Surface Codes and Fault Tolerance | 273 |
| G.7 Chapter 7: QAOA, VQE, and Optimisation | 274 |
| G.8 Chapter 8: Barren Plateaus and NISQ Reality | 275 |
| G.9 Chapter 9: Quantum Kernels and Quantum Neural Networks | 275 |
| G.10 Chapter 10: Quantum Transfer Learning and Data Encoding | 276 |
| APPENDIX H: Supplementary Data Figures, Organised by Chapter | 277 |
| H.1 Supporting Chapter 1 | 277 |
| H.2 Supporting Chapter 2 | 278 |
| H.3 Supporting Chapter 3 | 279 |
| H.4 Supporting Chapter 4 | 279 |
| H.5 Supporting Chapter 5 | 280 |
| H.6 Supporting Chapter 6 | 281 |
| H.7 Supporting Chapter 7 | 281 |
| H.8 Supporting Chapter 8 | 282 |
| H.9 Supporting Chapter 9 | 282 |
| H.10 Supporting Chapter 10 | 283 |
| APPENDIX I: Final Supplementary Figures | 284 |
| APPENDIX J: Closing Supplementary Figures | 290 |
| APPENDIX K: Closing Figures on Hardware Trends and Risk | 294 |
| INDEX | 297 |

| **Figure** | **Description** |
|---|---|
| Fig. 1.1 | QFT Circuit for 3 Qubits — the essential building block of QPE in Shor's algorithm |
| Fig. 1.2 | Shor's Algorithm Flow — classical pre/post-processing wraps the quantum order-finding core |
| Fig. 1.3 | Amplitude Amplification — Success Probability vs Iterations and Geometric Picture |
| Fig. 1.4 | Quantum Walk vs Classical Walk Distribution — ballistic vs diffusive spreading after 100 steps |
| Fig. 1.5 | Coined vs Continuous-Time Quantum Walk — probability dynamics on a line |
| Fig. 1.6 | HHL Algorithm Circuit — QPE, conditional rotation, inverse QPE, ancilla post-selection |
| Fig. 2.1 | Trotter vs Qubitisation Complexity — gate count vs t and ε |
| Fig. 2.2 | VQE Hybrid Classical-Quantum Loop — quantum circuit prepares \|ψ(θ)⟩, classical optimiser updates θ |
| Fig. 2.3 | VQE Energy Landscape for H₂ (2-parameter UCCSD) — surface and minimum |
| Fig. 2.4 | Trotterised TFIM Circuit (n=4 qubits, 1 Trotter step) — ZZ and Rx gates |
| Fig. 3.1 | Quantum Complexity Class Hierarchy — Conjectured containment structure: P ⊆ BPP ⊆ BQP ⊆ PP ⊆ PSPACE; QMA = quantum NP; key unknown: NP ⊆ BQP? |
| Fig. 3.2 | Quantum Query Algorithm Structure — Fixed unitaries U₀,...,U\_k interleaved with k oracle queries O\_f; the minimum k is the quantum query complexity Q(f) |
| Fig. 3.3 | Quantum Speedup Taxonomy — Classification of known quantum speedups from exponential (Shor, Simon) to provably none (PARITY) |
| Fig. 4.1 | IBM Quantum Volume Progress (2017–2024) — Left: QV on log scale showing IBM superconducting (blue) and trapped-ion leaders (orange/purple). Right: log₂(QV) grows roughly 1 bit/year — a 'Moore's Law for Quantum' |
| Fig. 5.1 | 3-Qubit Bit-Flip Code: Encoding, Error &amp; Syndrome Circuit — Encoding uses 2 CNOT gates; syndrome uses ancilla measurements to identify the erroneous qubit without disturbing the data |
| Fig. 5.2 | Shor 9-Qubit Code: Two-Level Concatenated Structure — Inner 3-qubit bit-flip codes protect X errors; outer 3-qubit phase-flip code protects Z errors; together they correct any single-qubit error |
| Fig. 5.3 | Steane [[7,1,3]] Code: Parity-Check Matrix &amp; Tanner Graph — Left: the H matrix of the [7,4,3] Hamming code used for both X and Z stabilisers. Right: Tanner graph connecting qubit nodes to stabiliser check nodes |
| Fig. 6.1 | Surface Code d=3: 2D Qubit Lattice — 9 data qubits (blue circles), 4 X-plaquette checks (blue diamonds), 4 Z-vertex checks (orange diamonds); 17 physical qubits total encode 1 logical qubit with distance d=3 |
| Fig. 6.2 | X-Plaquette Syndrome Extraction Circuit — 4 CNOT gates + 1 ancilla qubit measure the X-plaquette operator; +1 outcome = no error, −1 = syndrome detected |
| Fig. 6.3 | Surface Code: Logical vs Physical Error Rate — Below threshold p\_th≈1%, increasing distance d exponentially suppresses p\_L; above threshold, larger codes make things worse |
| Fig. 6.4 | Fault-Tolerant Quantum Computer: Full Architecture Stack — Six layers from physical qubits to user algorithm; QEC and magic state distillation dominate layers 2 and 3 |
| Fig. 7.1 | VQA Hybrid Classical-Quantum Loop — The quantum processor evaluates cost values; the classical optimiser drives parameter updates toward the minimum |
| Fig. 7.2 | QAOA p=1 Circuit for MaxCut on K₃ (Triangle Graph) — Hadamard initialisation, ZZ phase separation on each of 3 edges, X-rotation mixing per vertex; 6 CNOT gates total |
| Fig. 8.1 | Barren Plateau: Cost Landscape for n = 2, 10, 20 Qubits — As n increases, the gradient variance shrinks as 1/4^n — the landscape becomes exponentially flat, requiring exponential shots to escape |
| Fig. 8.2 | Zero-Noise Extrapolation Protocol — Left: gate folding at λ=1,2,3,4× noise levels. Right: linear fit + Richardson extrapolation to zero-noise C\_ideal |
| Fig. 9.1 | Quantum Feature Map — lifting classical data into an exponentially large Hilbert space |
| Fig. 9.2 | Quantum Kernel Gram Matrix and Kernel Alignment vs Circuit Depth |
| Fig. 9.3 | QNN Architecture — Encoding, variational, measurement layers with classical feedback loop |
| Fig. 9.4 | QNN Expressibility and Entanglement Capability vs Circuit Depth for three ansatz types |
| Fig. 9.5 | Parameter-Shift Rule — exact quantum gradient from two circuit evaluations |
| Fig. 10.1 | Data Encoding Strategies — Angle, Amplitude, and Basis encoding compared |
| Fig. 10.2 | Quantum Transfer Learning Architecture — frozen classical backbone + trainable quantum head |
| Fig. 10.3 | Dequantisation — classical SQ model vs quantum scaling, and quantum advantage map |
| Fig. 10.4 | PennyLane–PyTorch Hybrid Computation Graph — forward pass and parameter-shift backward pass |
| Fig. 10.5 | QNN Training Dynamics — Loss and Accuracy per Epoch during Hybrid Training |
| Fig. 1.7 | Quantum Counting via Amplitude Estimation |
| Fig. 2.5 | ADAPT-VQE — Iterative, Problem-Tailored Ansatz Growth |
| Fig. 2.6 | Energy Convergence — ADAPT-VQE Reaches Chemical Accuracy at Lower Depth |
| Fig. 3.4 | The Quantum Complexity Class Landscape |
| Fig. 3.5 | The Quantum PCP Conjecture and NLTS — Why Local Checking Is Hard |
| Fig. 3.6 | Simon's Algorithm Circuit — Exponential Query Separation |
| Fig. 4.2 | Porter-Thomas Distribution — The Statistical Signature of Random Circuit Sampling |
| Fig. 4.3 | Boson Sampling — Photonic Interferometer Schematic |
| Fig. 4.4 | A Timeline of Claimed Quantum Computational Advantage (2019-2024) |
| Fig. 4.5 | The Gap Between Hype and Demonstrated Quantum Advantage by Application Domain |
| Fig. 4.6 | Certified Randomness from Verifiable Quantum Advantage |
| Fig. 5.4 | The Knill-Laflamme Conditions for Quantum Error Correction |
| Fig. 5.5 | The Pauli Group and Stabiliser Formalism |
| Fig. 5.6 | Quantum LDPC Codes Promise Much Lower Qubit Overhead Than Surface Codes at Scale |
| Fig. 6.5 | The Real-Time Decoding Pipeline for Surface-Code QEC |
| Fig. 6.6 | Surface Codes Tolerate a Much Higher Physical Error Rate Than Concatenated Codes |
| Fig. 7.3 | QAOA Application Mapping — From Problem to Cost Hamiltonian |
| Fig. 7.4 | The Parameter-Shift Rule Gives the Exact Gradient from Two Circuit Evaluations |
| Fig. 7.5 | Hardware-Efficient Ansatze Trade Chemical Motivation for Shallower Circuits |
| Fig. 7.6 | Adiabatic Quantum Annealing — Staying in the Ground State Across the Anneal |
| Fig. 8.3 | Barren-Plateau Mitigation Strategies Slow the Exponential Gradient Decay |
| Fig. 8.4 | Three Quantum Error Mitigation Techniques at a Glance |
| Fig. 8.5 | The Roadmap from NISQ to Fault-Tolerant Quantum Computing |
| Fig. 8.6 | Honest Assessment of Near-Term Quantum Advantage by Application |
| Fig. 9.6 | Quantum Generative Models — The Born Machine |
| Fig. 10.6 | Building a Career in Quantum Computing — A Student's Roadmap |

## LIST OF SYMBOLS AND NOTATION

The following symbols and notation are used consistently throughout the textbook. Quantum mechanics notation follows the Dirac bra-ket convention; circuit notation follows standard quantum computing practice.

| **Symbol / Notation** | **Meaning and Usage** |
|---|---|
| \|ψ⟩ | Ket vector — quantum state in Hilbert space |
| ⟨ψ\| | Bra vector — dual of the ket (conjugate transpose) |
| ⟨φ\|ψ⟩ | Inner product (braket) — complex number |
| \|φ⟩⟨ψ\| | Outer product — a matrix (linear operator) |
| ⟨A⟩ | Expectation value of observable A |
| Ρ | Density matrix — describes pure and mixed quantum states |
| Tr(ρ) | Trace of density matrix (= 1 for all physical states) |
| Tr(ρ²) | Purity of quantum state (= 1 for pure, &lt; 1 for mixed) |
| U† | Hermitian conjugate (adjoint) of unitary operator U |
| H | Hadamard gate (also Hamiltonian when context is clear) |
| X, Y, Z | Pauli matrices — fundamental single-qubit operators |
| S | Phase gate S = [[1,0],[0,i]] = √Z (π/2 phase rotation) |
| T | π/8 phase gate T = [[1,0],[0,exp(iπ/4)]] = ⁴√Z |
| Rx(θ) | Rotation gate about x-axis by angle θ |
| Ry(θ) | Rotation gate about y-axis by angle θ |
| Rz(θ) | Rotation gate about z-axis by angle θ |
| U(θ,φ,λ) | IBM's universal single-qubit native gate |
| CNOT / CX | Controlled-NOT two-qubit gate |
| CCX | Toffoli gate (controlled-controlled-NOT) |
| \|Φ+⟩, \|Φ-⟩ | Bell states (maximally entangled two-qubit states) |
| \|Ψ+⟩, \|Ψ-⟩ | Bell states (maximally entangled two-qubit states) |
| O\_f | Quantum oracle for function f |
| QFT | Quantum Fourier Transform |
| QPE | Quantum Phase Estimation |
| VQE | Variational Quantum Eigensolver |
| QAOA | Quantum Approximate Optimization Algorithm |
| T₁ | Energy relaxation time (amplitude damping) |
| T₂ | Dephasing time (phase coherence lifetime) |
| T₂\* | Free induction decay time (inhomogeneously broadened) |
| ε(ρ) | Quantum channel (CPTP map) acting on density matrix |
| Kₖ | Kraus operators of a quantum channel |
| P | Error probability per gate operation |
| N | Database size or number of computational basis states |
| N | Number of qubits |
| k\_opt | Optimal Grover iterations = round(π√N/4) |
| QV | Quantum Volume benchmark metric |
| CLOPS | Circuit Layer Operations Per Second |
| NISQ | Noisy Intermediate-Scale Quantum (era, Preskill 2018) |
| RSA | Rivest–Shamir–Adleman public-key cryptosystem |
| GNFS | General Number Field Sieve (best classical factoring) |
| ℋ | Hilbert space |
| ℂ² | Two-dimensional complex vector space (single qubit) |
| ⊗ | Tensor product of quantum systems |
| ⊕ | Bitwise XOR (exclusive-or) |
| Χ | Holevo bound on classically extractable information |
| S(ρ) | Von Neumann entropy = -Tr(ρ log₂ ρ) |
| θ, φ | Bloch sphere polar (θ) and azimuthal (φ) angles |
| NQM | India's National Quantum Mission (2023–2031) |
| 𝒢 | Universal gate set |
| 𝒞ₙ | n-qubit Clifford group |
| 𝒫ₙ | n-qubit Pauli group |
| F | Fidelity between quantum states or operations |
| Γ | Amplitude damping parameter = 1 − exp(−t/T₁) |
| Λ | Phase damping parameter = 1 − exp(−t/T₂) |
| BQP / BPP / P | Bounded-error Quantum/Probabilistic Polynomial time; deterministic Polynomial time (complexity classes) |
| QMA / QCMA | Quantum (Classical) Merlin-Arthur — quantum analogues of NP |
| PSPACE | Class of problems solvable in polynomial space |
| Q(f) / D(f) | Quantum / classical (deterministic) query complexity of function f |
| κ | Condition number of a matrix (ratio of largest to smallest singular value) |
| [[n,k,d]] | Stabiliser code notation: n physical qubits, k logical qubits, code distance d |
| p\_th | Error-correction threshold — physical error rate below which scaling helps |
| γ, β | QAOA variational parameters for the cost and mixer unitaries |
| p (QAOA) | Number of QAOA alternating cost/mixer layers |
| U\_φ(x) | Quantum feature map encoding classical data x into a quantum state |
| K(x,x′) | Quantum kernel — fidelity/overlap between two feature-mapped states |
| T-gate / T-count | Non-Clifford π/8 phase gate; the dominant resource cost under fault tolerance |
