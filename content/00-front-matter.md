## Cover Page

## Dedicated to

## My Strong Father

<img class="fig-img" src="content/images/image1.jpeg" alt="figure">

**Shri Anand Prakash Jain**

**(Proudly served 39 years in the Indian Army and the Border Security Force)**

**Q.C. Series | Volume II**

## QUANTUM ALGORITHMS & COMPLEXITY

## Shor, Grover, QFT, HHL, VQE & Quantum Complexity Theory

A Comprehensive University Textbook for M.Sc. Physics for Specialization in Quantum Computing

**Dr. Sanjeev Kumar Jain**

Associate Professor and Ex-Head,

Department of Applied Sciences and Humanities  ·  Faculty of Science

Invertis University, Bareilly (U.P.), India

**An Overview**

| Programme | M.Sc. Physics — Quantum Computing Specialization |
|---|---|
| Semester | Semester IV |
| This volume for Course | Quantum Algorithms and Complexity |
| Chapter 1 | Shor's Algorithm, Amplitude Amplification & HHL |
| Chapter 2 | Quantum Simulation & Advanced Circuit Design |
| Chapter 3 | Complexity Classes, Query Complexity & the Polynomial Method |
| Chapter 4 | Quantum Advantage — Theory, Evidence & Reality |
| Chapter 5 | Quantum Error Correction Principles |
| Chapter 6 | Surface Codes and Fault-Tolerant Computing |
| Chapter 7 | Advanced Variational and Hybrid Algorithms |
| Chapter 8 | Barren Plateaus, Error Mitigation & the Limits of NISQ Advantage |
| Chapter 9 | Quantum Machine Learning — Kernels, Neural Networks & Gradients |
| Chapter 10 | Data Encoding, Transfer Learning, Dequantisation & PennyLane |
| Platform | Python / Qiskit 1.x · Qiskit Nature · PennyLane + PyTorch · IBM Quantum Hardware |
| Features | Each Chapter has: 6 types of Information boxes, 11+ Recap Qs, 8 Solved Examples · 15+ MCQs · 10+ Theory Qs · 8+ Problems · Figures, Programming Assignments and Project suggestions. |

## PREFACE

This textbook is the second volume of the Quantum Computing Series and is written for students of M.Sc. Physics who have completed the foundational course (Quantum Computers, Volume I) and are ready to study the algorithmic and complexity-theoretic heart of the field. The goal is dual: to provide rigorous derivations of the major quantum algorithms and their complexity-theoretic guarantees, and to develop genuine practical implementation skill using Qiskit and PennyLane. Both goals are essential — theory without implementation leads to abstract knowledge that cannot be deployed; implementation without theory leads to running circuits without understanding why, or whether, they actually provide an advantage.

Quantum algorithms are where the promise of quantum computing is either realised or exposed as hype. This volume treats Shor's algorithm and Grover's search as the historical anchors, then builds outward to quantum simulation, the complexity classes (BQP, QMA) that formally describe what quantum computers can and cannot do, the sobering realities of quantum advantage claims, quantum error correction and fault tolerance, and the variational and machine-learning algorithms that dominate the current NISQ era. For India's M.Sc. students, the National Quantum Mission (NQM, ₹6,003 crore, 2023–2031) continues to create career opportunities in quantum algorithm design, error correction research, and quantum software — at TCS, Wipro, Infosys, QpiAI, BosonQ Psi, and at the NQM hubs at IITs, IISc, and DRDO.

### How This Textbook Is Structured

The textbook is divided into five units spanning ten chapters, that build up the subject interestingly and thoroughly:

• Unit I (Chapters 1–2): Algorithms & Simulation. Chapter 1 develops Shor's algorithm, generalised amplitude amplification, quantum walks, and the HHL algorithm for linear systems. Chapter 2 covers Hamiltonian simulation via Trotter-Suzuki formulas and qubitisation, and the variational algorithms used for quantum chemistry and condensed matter simulation.

• Unit II (Chapters 3–4): Complexity & Advantage. Chapter 3 develops the complexity classes P, BPP, BQP, and QMA, quantum query complexity, and the polynomial and adversary lower-bound methods. Chapter 4 critically examines quantum advantage claims — Google's Sycamore experiment, boson sampling — and the hardware benchmarks used to assess real devices.

• Unit III (Chapters 5–6): Error Correction. Chapter 5 develops the principles of quantum error correction — the bit-flip, phase-flip, Shor, and Steane codes, and the Knill-Laflamme conditions. Chapter 6 covers the surface code, the threshold theorem, and the resource estimates for fault-tolerant quantum computing.

• Unit IV (Chapters 7–8): Variational Algorithms. Chapter 7 covers QAOA, advanced VQE for quantum chemistry, and classical optimiser strategies. Chapter 8 confronts the barren plateau problem and surveys quantum error mitigation techniques, closing with an honest assessment of NISQ-era limits.

• Unit V (Chapters 9–10): Quantum Machine Learning. Chapter 9 develops quantum kernels, QSVMs, and quantum neural networks with the parameter-shift rule. Chapter 10 covers data encoding strategies, quantum transfer learning, dequantisation results, and hybrid PennyLane–PyTorch programming.

**The laboratory manual - Quantum Computing Lab II - supports the laboratory part, and that practical course should be run together with the theory course.**

### Pedagogical Features

Each chapter contains:

•	📜 Anecdote boxes: Historical stories and scientists — making the subject interesting.

•	🔑 Key Concept boxes: Formal definitions, precisely stated.

•	🌐 Real World boxes: Applications in industry, government, India's quantum mission.

•	**⚠** Warning boxes: Common misconceptions and pitfalls.

•	Example boxes: 8+ worked examples per chapter, step-by-step.

•	Equation boxes: Key mathematical formulas with physical interpretation.

•	Dark code blocks: Complete Qiskit programs with line-by-line commentary.

•	Figures: Labelled, captioned figures throughout.

- Each chapter closes with: 11+ Recap Qs (with model answers) · 8 Solved Problems · 8+ Unsolved Problems (answers in brackets) · 15+ MCQs (answers collected at the very end of the chapter) · 8+ Theory Questions · Programming/Research Assignments · Project Suggestions — a focused review designed for strengthening understanding, application and for out of the classroom preparation.

The reader is encouraged to discuss among colleagues and try out all the examples, questions, program codes and the problems given at the end of a chapter. Consider them a part of the learning that can be gained from the chapter. Assignments and projects are designed for field learning.

### A note on honesty

Quantum algorithms are a field where media coverage often outpaces scientific reality, perhaps more than any other area of quantum computing. This textbook makes a deliberate effort to distinguish proven exponential speedups (Shor's algorithm) from conditional and caveat-laden speedups (HHL), from quadratic speedups (Grover, amplitude amplification), from claimed-but-contested advantage (some variational and quantum-ML applications), and from advantage that has since been dequantised entirely. Students who understand these distinctions will be better researchers, better engineers, and better communicators of science to the public.

The generation studying from this textbook will be among the first Indian-trained quantum scientists to work on nationally funded quantum hardware, quantum communication networks, and quantum software. It is hoped that this book contributes, in a small way, to prepare them for that responsibility.

— **Dr. S. K. Jain**

## The various information boxes in this textbook

<div class="box box-key-concept">
<p class="box-title"><strong>🔑  KEY CONCEPT BOXES</strong></p>
<p>Contain the formal mathematical definitions, theorems, and circuit constructions that you must know for examinations. Read these carefully and ensure you can reproduce the key equations.</p>
</div>

<div class="box box-anecdote">
<p class="box-title"><strong>📜  ANECDOTE BOXES</strong></p>
<p>Provide historical context and the human stories behind the science. These are not examinable but are important for understanding how the field developed and for communicating science to non-specialists.</p>
</div>

<div class="box box-real-world">
<p class="box-title"><strong>🌍  REAL WORLD BOXES</strong></p>
<p>Connect theory to current industrial applications, national programmes (especially India's NQM), and career-relevant context. These appear in examination short-answer questions.</p>
</div>

<div class="box box-warning">
<p class="box-title"><strong>⚠️  WARNING BOXES</strong></p>
<p>Explicitly correct common misconceptions. If a concept appears in a warning box, it is almost certainly something that students — and even professionals — frequently get wrong.</p>
</div>

<div class="box box-math">
<p class="box-title"><strong>🧮  MATHEMATICS BOXES</strong></p>
<p>Contain worked derivations and numerical examples. Work through these step-by-step, covering the solution and attempting each step yourself first.</p>
</div>

<div class="box box-generic">
<p class="box-title"><strong># CODE BOXES (Dark background)</strong></p>
<p>Contain working Qiskit/Python code. Every code box can be run on IBM Quantum (free account at quantum.ibm.com). Running the code is the best way to develop intuition.</p>
</div>

## Table of Contents

| Contents | Page |
|---|---|
| CHAPTER 1: Shor's Algorithm, Amplitude Amplification & HHL | 18 |
| 1.1  Shor's Factoring Algorithm — Complete Treatment | 18 |
| 1.2  Amplitude Amplification and Quantum Walks | 26 |
| 1.3  Linear Systems — HHL Algorithm | 31 |
| RECAP — Short Answer Questions & Model Answers | 37 |
| CHAPTER 2: Quantum Simulation & Advanced Circuit Design | 50 |
| 2.1  Product Formula (Trotter-Suzuki): First and Second Order | 50 |
| 2.2  Qubitisation and Quantum Signal Processing | 53 |
| 2.3  Variational Quantum Simulation | 55 |
| 2.4  Quantum Chemistry: VQE for H₂ and LiH | 57 |
| 2.5  Many-Body Physics: Ising and Hubbard Model Trotter Simulation | 61 |
| RECAP — Short Answer Questions & Model Answers | 66 |
| CHAPTER 3: Quantum Complexity Theory | 75 |
| 3.1  Foundations of Classical Complexity Theory | 76 |
| 3.2  BQP: Bounded-Error Quantum Polynomial Time | 78 |
| 3.3  QMA: Quantum Merlin-Arthur and the Quantum NP | 81 |
| 3.4  QCMA, PP, and the Polynomial Hierarchy | 82 |
| 3.5  Quantum Circuit Complexity and Black Holes | 83 |
| 3.6  Quantum Query Complexity | 84 |
| 3.7  The Polynomial Method | 85 |
| 3.8  The BBBV Theorem and the Adversary Method | 86 |
| 3.9  Simon's Problem: The First Exponential Separation | 88 |
| RECAP — Short Answer Questions & Model Answers | 91 |
| References and Further Reading — Chapter 3 | 101 |
| CHAPTER 4: Quantum Advantage: Theory, Evidence & Reality | 102 |
| 4.1  What Is Quantum Advantage? | 102 |
| 4.2  Google 2019: Random Circuit Sampling and Sycamore | 103 |
| 4.3  Boson Sampling: Photonic Quantum Advantage | 105 |
| 4.4  Quantum Advantage in Optimisation, ML, and Finance: Hype vs Reality | 106 |
| 4.5  Hardware Benchmarks: Quantum Volume and Beyond | 108 |
| RECAP — Short Answer Questions & Model Answers | 112 |
| CHAPTER 5: Quantum Error Correction: Principles, Codes & Stabilisers | 120 |
| 5.1  Why Quantum Error Correction Appears Impossible | 121 |
| 5.2  The 3-Qubit Bit-Flip Code | 123 |
| 5.3  The 3-Qubit Phase-Flip Code and Shor's 9-Qubit Code | 124 |
| 5.4  The Knill-Laflamme Quantum Error Correction Conditions | 125 |
| 5.5  The Stabiliser Formalism | 126 |
| 5.6  CSS Codes and the [[7,1,3]] Steane Code | 128 |
| RECAP — Short Answer Questions & Model Answers | 133 |
| References and Further Reading — Chapter 5 | 142 |
| CHAPTER 6: Surface Codes, Threshold Theorem & Fault-Tolerant Architecture | 143 |
| 6.1  From Stabiliser Codes to Topological Codes | 143 |
| 6.2  The Surface Code | 144 |
| 6.3  Syndrome Extraction and Decoding | 146 |
| 6.4  The Threshold Theorem: The Bedrock of Fault-Tolerant Computing | 147 |
| 6.5  Magic State Distillation: The T Gate Problem | 149 |
| 6.6  Resource Estimates: Factoring RSA-2048 | 150 |
| 6.7  IBM 2023: First Experimental Evidence of the Threshold Theorem | 151 |
| 6.8  Full Fault-Tolerant Quantum Computer Architecture | 152 |
| RECAP — Short Answer Questions & Model Answers | 155 |
| CHAPTER 7: QAOA, VQE for Molecular Systems & Optimiser Strategies | 163 |
| 7.1  The NISQ Era and the Variational Approach | 164 |
| 7.2  The Quantum Approximate Optimisation Algorithm (QAOA) | 166 |
| 7.3  QAOA Applications: MaxCut, Portfolio Optimisation, and TSP | 167 |
| 7.4  VQE for Molecular Systems: UCCSD, Active Space & Convergence | 168 |
| 7.5  Classical Optimiser Strategies for Variational Algorithms | 170 |
| 7.6  Hardware-Efficient Ansatz: When UCCSD Is Too Deep | 171 |
| RECAP — Short Answer Questions & Model Answers | 175 |
| References and Further Reading — Chapter 7 | 185 |
| CHAPTER 8: Barren Plateaus, Expressibility & Near-Term Quantum Advantage | 185 |
| 8.1  Barren Plateaus: The Trainability Crisis in Variational Quantum Algorithms | 186 |
| 8.2  Barren Plateau Mitigation Strategies | 188 |
| 8.3  Quantum Error Mitigation (QEM) | 189 |
| 8.4  Near-Term Quantum Advantage: A Rigorous Assessment | 191 |
| 8.5  Theoretical Depth: QAOA, Adiabaticity, and Optimisation Landscapes | 193 |
| RECAP — Short Answer Questions & Model Answers | 196 |
| References and Further Reading — Chapter 8 | 205 |
| CHAPTER 9: Quantum Kernels and Quantum Neural Networks | 205 |
| 9.1  Quantum Feature Maps and the Kernel Trick | 206 |
| 9.2  Quantum Neural Networks: Parameterised Circuits as Function Approximators | 211 |
| 9.3  The Parameter-Shift Rule: Exact Quantum Gradients | 214 |
| RECAP — Short Answer Questions & Model Answers | 218 |
| CHAPTER 10: Quantum Transfer Learning, Data Encoding Strategies & Dequantisation | 232 |
| 10.1  Data Encoding Strategies: Angle, Amplitude, Basis, and Re-uploading | 232 |
| 10.2  Quantum Transfer Learning | 235 |
| 10.3  Dequantisation: When Classical ML Matches Quantum ML | 238 |
| 10.4  PennyLane Integration with PyTorch: Hybrid Quantum-Classical Pipelines | 240 |
| RECAP — Short Answer Questions & Model Answers | 246 |
