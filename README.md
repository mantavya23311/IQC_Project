# IQC_Project
QAOA based project
# IQC Project — Two-Step QAOA for NP-Hard Combinatorial Optimization

> **QAOA for NP-Hard Combinatorial Optimization: QUBO Formulations and Benchmarking Across Problem Classes**
>
> **Author:** Mantavya Kaushik | Roll No: 2023311 | IIIT-Delhi
> **Instructor:** Prof. Debajyoti Bera | **Submitted:** 3 May 2026

---

## Table of Contents

1. [Project Overview](#1-project-overview)
2. [Repository Structure](#2-repository-structure)
3. [Report → Code Mapping (Full)](#3-report--code-mapping-full)
   - [Part A: Problem Definitions (P1/P2/P3)](#part-a-problem-definitions)
   - [Part B §10: What Has Been Implemented](#part-b-10-what-has-been-implemented)
   - [Final Report — Methodology: 5 Modules](#final-report--methodology-5-modules)
   - [Final Report — Observation 1: Stage-1 Fidelity Benchmark (Table 1)](#final-report--observation-1-stage-1-fidelity-benchmark-table-1)
   - [Final Report — Observation 2: Full Two-Step Pipeline (Qiskit)](#final-report--observation-2-full-two-step-pipeline-qiskit)
   - [Final Report — Observation 3: Noise Sensitivity Collapse](#final-report--observation-3-noise-sensitivity-collapse)
   - [Final Report — Observation 4: Knapsack Encoding Advantage](#final-report--observation-4-knapsack-encoding-advantage)
4. [Notebook-by-Notebook Guide](#4-notebook-by-notebook-guide)
5. [Key Formulas Implemented](#5-key-formulas-implemented)
6. [Dependencies](#6-dependencies)
7. [How to Run](#7-how-to-run)
8. [References](#8-references)

---

## 1. Project Overview

This project implements and benchmarks the **Two-Step QAOA** framework (Minato, arXiv:2408.05383) across three NP-hard problem classes, and extends it with two **novel Stage-1 loss functions** (M6 Projector, M7 Projector+KL) to solve the landscape degeneracy problem during Dicke state preparation.

**Three target problems:**

| ID | Problem | Constraint Type | Encoding Strategy |
|----|---------|----------------|----------|
| P1 | Max-Cut | Unconstrained | Direct Ising / ZiZj couplings |
| P2 | 0/1 Knapsack | Linear inequality | Slack variables vs Unbalanced Penalisation |
| P3 | Portfolio Optimization | Cardinality equality (k-hot) | Markowitz QUBO + X-mixer / XY-mixer |

**Core contribution — Four Stage-1 loss functions benchmarked across 15 Dicke states `|D^n_k⟩` at depth p=6:**

| Method | Formula | Origin |
|--------|---------|--------|
| Baseline | `⟨(N−k)²⟩` | Minato 2025 |
| M5 Adaptive-α | `⟨(N−k)²⟩ + α(p)·Var(N)` | Prior extension |
| **M6 Projector** | `1 − ⟨ψ\|P_k\|ψ⟩²` | **This project (novel)** |
| **M7 Proj+KL** | `(1−⟨P_k\|ψ⟩²) + β·KL(q ∥ Uniform)` | **This project (novel)** |

---

## 2. Repository Structure

```
IQC_Project/
├── QAOA_Project_2023311.ipynb          # Main deliverable: P1+P2+P3 full pipeline
├── two_step_qaoa_corrected.ipynb       # Authoritative M5/M6/M7 benchmark (Table 1 source)
├── two_step_qaoa_improved.ipynb        # M6 & M7 first implementation + theory
├── two_step_qaoa_qiskit_stage2.ipynb   # End-to-end Qiskit Aer pipeline (Observations 2 & 3)
├── expanded_benchmark.ipynb            # Baseline/M1/M4/M5 expanded benchmark
├── expanded_benchmark - Copy.ipynb     # M5 sigmoid α-schedule variant
└── Qaoa_report_final.pdf               # Final project report
```

---

## 3. Report → Code Mapping (Full)

---

### Part A: Problem Definitions

#### P1 — Max-Cut (Report pp. 1–2, §2 Table)
> *"QUBO: Q = −Σ(1−z_iz_j)/2. Direct Ising mapping. Depth p=1..4 swept. COBYLA vs SPSA compared."*

| Report Element | File | Section / Cell |
|---|---|---|
| QUBO formula & Ising builder from graph | `two_step_qaoa_qiskit_stage2.ipynb` | `§ 1 — QUBO → Ising Conversion` → Cell 3 |
| Problem QUBO definition | `two_step_qaoa_qiskit_stage2.ipynb` | `§ 2 — Problem Definitions` → Cell 4 |
| p=1..4 depth sweep + COBYLA vs SPSA | `QAOA_Project_2023311.ipynb` | Module 2 / `PennyLaneQAOA` class |
| MaxCut noiseless vs noisy experiment | `two_step_qaoa_qiskit_stage2.ipynb` | `§ 7 — Experiment 2: MaxCut (n=6, k=3)` → Cell 10 |

---

#### P2 — 0/1 Knapsack (Report pp. 1–2; Final Report Observation 4)
> *"QUBO: Q = −Σv_ix_i + λ(Σw_ix_i + s − C)². Slack variables vs Unbalanced Penalisation compared."*

| Report Element | File | Section / Cell |
|---|---|---|
| QUBO formulation (slack-variable method) | `QAOA_Project_2023311.ipynb` | Module 3 / Knapsack QUBO builder |
| Unbalanced Penalisation encoder | `QAOA_Project_2023311.ipynb` | Module 3 / `unbalanced_penalty()` function |
| λ penalty tuning / grid search | `QAOA_Project_2023311.ipynb` | §6 / λ sensitivity section |
| Qubit count comparison (11 vs 6 qubits) | `QAOA_Project_2023311.ipynb` | Summary table cell |
| Approximation ratio p=1,2 comparison | `QAOA_Project_2023311.ipynb` | Benchmark results section |

---

#### P3 — Portfolio Optimization (Report pp. 1–2; Final Report Methodology & Observations 2–3)
> *"Yahoo Finance data. Markowitz QUBO. X-mixer baseline and XY-mixer (Hamming-weight preserving) both implemented."*

| Report Element | File | Section / Cell |
|---|---|---|
| Markowitz QUBO formulation | `two_step_qaoa_qiskit_stage2.ipynb` | `§ 2 — Problem Definitions` → Cell 4 |
| Yahoo Finance data pipeline | `QAOA_Project_2023311.ipynb` | Module 3 / Portfolio section |
| X-mixer baseline | `QAOA_Project_2023311.ipynb` | Module 2 / `PennyLaneQAOA` class |
| XY-mixer (`XXPlusYYGate`, Hamming-weight preserving) | `two_step_qaoa_qiskit_stage2.ipynb` | `§ 4 — Stage 2: Qiskit Aer XY-Mixer QAOA` → Cell 7 |
| Portfolio n=5, k=2 full experiment | `two_step_qaoa_qiskit_stage2.ipynb` | `§ 6 — Experiment 1: Portfolio (n=5, k=2)` → Cell 9 |
| Brute-force optimal energy comparison | `two_step_qaoa_qiskit_stage2.ipynb` | `§ 11 — Final Summary` → Cell 14 |

---

### Part B §10: What Has Been Implemented

#### §10.1 — Infrastructure
> *"`qubo_to_ising(Q)` converter, `PennyLaneQAOA` and `QiskitQAOA` classes"*

| Report Element | File | Section / Cell |
|---|---|---|
| `qubo_to_ising()` with energy offset tracking | `two_step_qaoa_qiskit_stage2.ipynb` | `§ 1 — QUBO → Ising Conversion` → Cell 3 |
| `PennyLaneQAOA` class | `QAOA_Project_2023311.ipynb` | Module 2 / Infrastructure |
| `QiskitQAOA` / `Stage2QiskitQAOA` class | `two_step_qaoa_qiskit_stage2.ipynb` | `§ 4` → Cell 7 |

#### §10.5 — Noise Analysis
> *"`noise_sweep_pennylane()` sweeping η ∈ [0, 0.05]`"*

| Report Element | File | Section / Cell |
|---|---|---|
| Depolarising noise sweep (η: 0 → 0.05) | `two_step_qaoa_qiskit_stage2.ipynb` | `§ 8 — Noise Sensitivity Sweep` → Cell 11 |
| Feasibility vs η visualization | `two_step_qaoa_qiskit_stage2.ipynb` | `§ 10 — Visualisation` → Cell 13 |
| Feasibility floor derivation (thermalized limit) | `two_step_qaoa_corrected.ipynb` | `Cell 17 — Noise and feasibility floor analysis` |

#### §10.6 — Classical Benchmarking
> *"Brute-force optimal and Simulated Annealing vs QAOA"*

| Report Element | File | Section / Cell |
|---|---|---|
| Brute-force energy oracle | `two_step_qaoa_qiskit_stage2.ipynb` | `§ 6 — Experiment 1` → Cell 9 |
| Final energy vs optimal table | `two_step_qaoa_qiskit_stage2.ipynb` | `§ 11 — Final Summary` → Cell 14 |
| Classical vs Quantum comparison table | `QAOA_Project_2023311.ipynb` | Summary table cell |

---

### Final Report — Methodology: 5 Modules

#### Module 1 — QUBO/Ising Transformation Engine
> *"x_i = (1−Z_i)/2 substitution. Global energy offset tracked. Ising form: H = Σh_iZ_i + Σ J_{ij}Z_iZ_j."*

| Report Element | File | Section / Cell |
|---|---|---|
| `qubo_to_ising()` with global offset | `two_step_qaoa_qiskit_stage2.ipynb` | `§ 1 — QUBO → Ising Conversion` → Cell 3 |
| x_i = (1−Z_i)/2 substitution | `QAOA_Project_2023311.ipynb` | Module 1 / QUBO builder |

---

#### Module 2 — QAOA Simulators
> *"PennyLane for COBYLA/SPSA on statevectors. Qiskit Aer for depolarising noise + hardware transpilation."*

| Report Element | File | Section / Cell |
|---|---|---|
| PennyLane statevector QAOA circuit | `expanded_benchmark.ipynb` | `## Method Implementations` → Cells 6, 8, 10, 12 |
| COBYLA / SPSA gradient-free optimizers | `expanded_benchmark.ipynb` | `baseline()` → Cell 6 |
| Qiskit Aer noise backend | `two_step_qaoa_qiskit_stage2.ipynb` | `§ 4 — Stage 2: Qiskit Aer XY-Mixer QAOA` → Cell 7 |
| Hardware transpilation | `two_step_qaoa_qiskit_stage2.ipynb` | `Stage2QiskitQAOA` class → Cell 7 |

---

#### Module 3 — Problem Encodings
> *"Max-Cut (ZiZj edges), Knapsack (slack + unbalanced), Portfolio (Markowitz QUBO + real data)."*

| Report Element | File | Section / Cell |
|---|---|---|
| All three problem QUBO definitions | `two_step_qaoa_qiskit_stage2.ipynb` | `§ 2 — Problem Definitions` → Cell 4 |
| Slack-variable Knapsack encoding | `QAOA_Project_2023311.ipynb` | Module 3 |
| Unbalanced Penalisation Knapsack | `QAOA_Project_2023311.ipynb` | Module 3 |
| Markowitz QUBO with Yahoo Finance data | `QAOA_Project_2023311.ipynb` | Module 3 / Portfolio section |

---

#### Module 4 — Two-Step QAOA & Novel Loss Functions *(Main Contribution)*
> *"Stage-1: X-mixer minimizes HC1 = (Σx_i−k)² to approximate Dicke state. Four loss functions benchmarked."*

##### Stage-1 Loss Functions

| Loss Function | Report Formula | File | Section / Cell |
|---|---|---|---|
| **Baseline** | `⟨ψ\|(N−k)²\|ψ⟩` | `two_step_qaoa_corrected.ipynb` | `## Loss Functions: M5, M6, M7` → Cell 8 (`baseline()`) |
| **M5 Adaptive-α** | `⟨(N−k)²⟩ + α(p)·Var(N)` | `two_step_qaoa_corrected.ipynb` | `## Loss Functions` → Cell 5 (`m5_adaptive()`) |
| | | `expanded_benchmark.ipynb` | `### M5 — Adaptive-α Annealing` → Cell 12 |
| | | `expanded_benchmark - Copy.ipynb` | `### M5` → Cell 12 (`m5_adaptive_sigmoid()`) — sigmoid α variant |
| **M6 Projector** *(novel)* | `1 − ⟨ψ\|P_k\|ψ⟩²` | `two_step_qaoa_improved.ipynb` | `## M6 — Subspace Projector Loss` → Cell 7 |
| | | `two_step_qaoa_corrected.ipynb` | `## Loss Functions` → Cell 6 |
| **M7 Proj+KL** *(novel)* | `(1−⟨P_k\|ψ⟩²)+β·KL(q∥Uniform)` | `two_step_qaoa_improved.ipynb` | `## M7 — Subspace Projection + Uniformity` → Cell 9 |
| | | `two_step_qaoa_corrected.ipynb` | `## Loss Functions` → Cell 7 |

##### k-hot Subspace Helpers

| Report Element | File | Section / Cell |
|---|---|---|
| k-hot subspace index precomputation (`P_k`) | `two_step_qaoa_improved.ipynb` | `## New: Subspace Projection Helpers` → Cell 5 |
| `exact_dicke(n, k)` reference state | `two_step_qaoa_improved.ipynb` | `## Core Helpers` → Cell 3 |
| | `expanded_benchmark.ipynb` | Cell 2 |
| M6 vs M7 gap analysis vs C(n,k) | `two_step_qaoa_corrected.ipynb` | `## New Analysis Cells` → Cell 20 |
| M7 KL scaling argument: β auto-tuned to `log C(n,k)` | `two_step_qaoa_improved.ipynb` | `## Analysis: Why M6/M7 Should Outperform M5` → Cell 24 |

---

#### Module 5 — Stage-2 Qiskit Pipeline
> *"Inject Stage-1 statevector into Qiskit circuit. Apply Markowitz QUBO via XXPlusYYGate to preserve Hamming weight."*

| Report Element | File | Section / Cell |
|---|---|---|
| `Stage2QiskitQAOA` class | `two_step_qaoa_qiskit_stage2.ipynb` | `§ 4 — Stage 2: Qiskit Aer XY-Mixer QAOA` → Cell 7 |
| Custom statevector injection (replaces H gates) | `two_step_qaoa_qiskit_stage2.ipynb` | `§ 5 — Full Two-Step Pipeline` → Cell 8 |
| `XXPlusYYGate` for XY-mixer | `two_step_qaoa_qiskit_stage2.ipynb` | `Stage2QiskitQAOA` → Cell 7 |
| Circuit diagram visualization | `two_step_qaoa_qiskit_stage2.ipynb` | `§ 9 — Circuit Diagram` → Cell 12 |

---

### Final Report — Observation 1: Stage-1 Fidelity Benchmark (Table 1)

> *"15 Dicke states at p=6. Baseline mean fidelity 0.9772, min 0.8866. M6 mean 0.9981. M7 mean 0.9957. Gap degeneracy exposed for C(n,k) ≥ 35."*

| Report Element | File | Section / Cell |
|---|---|---|
| **Table 1** — Fidelity at p=6 (all 15 states, all methods) | `two_step_qaoa_corrected.ipynb` | `## Benchmark Configuration` → Cell 10 (summary stats) |
| | `two_step_qaoa_improved.ipynb` | `## Summary Statistics` → Cell 17 |
| 15-state benchmark configuration | `two_step_qaoa_corrected.ipynb` | `## Benchmark Configuration` → Cell 10 |
| | `expanded_benchmark.ipynb` | `## Benchmark Configuration — 15 Dicke States & p=6` → Cell 4 |
| Full benchmark run (15 states × p=1..6) | `two_step_qaoa_corrected.ipynb` | Cell 11 (benchmark loop) |
| | `two_step_qaoa_improved.ipynb` | `## Full Benchmark Run` → Cell 15 |
| | `expanded_benchmark.ipynb` | `## Full Benchmark — 15 States × p=1..6` → Cell 14 |
| **Figure 1** — Grouped bar chart (fidelity @ p=6) | `two_step_qaoa_corrected.ipynb` | `## Figures` → Cell 14 |
| | `two_step_qaoa_improved.ipynb` | `## Figures` → Cell 19 |
| | `expanded_benchmark.ipynb` | Cell 18 |
| **Figure 2** — Fidelity vs layers p (3×5 grid) | `two_step_qaoa_corrected.ipynb` | `## Figures` → Cell 15 |
| | `two_step_qaoa_improved.ipynb` | `## Figures` → Cell 20 |
| | `expanded_benchmark.ipynb` | Cell 19 |
| **Figure 3** — Δ improvement over Baseline | `two_step_qaoa_corrected.ipynb` | `## Figures` → Cell 16 |
| | `two_step_qaoa_improved.ipynb` | `## Figures` → Cell 21 |
| | `expanded_benchmark.ipynb` | Cell 20 |
| **Figure 4** — Loss landscape / α-schedule trajectory | `two_step_qaoa_corrected.ipynb` | `## Figures` → Cell 17 |
| | `two_step_qaoa_improved.ipynb` | `## Figures` → Cell 22 |
| | `expanded_benchmark.ipynb` | Cell 21 (linear α) |
| | `expanded_benchmark - Copy.ipynb` | Cell 21 (sigmoid α variant) |
| **Figure 5** — Fidelity heatmap (state × p) | `two_step_qaoa_corrected.ipynb` | `## Figures` → Cell 18 |
| | `two_step_qaoa_improved.ipynb` | `## Figures` → Cell 23 |
| | `expanded_benchmark.ipynb` | Cell 22 |
| Aggregate statistics (mean, min fidelity) | `expanded_benchmark.ipynb` | Cell 23 |
| | `expanded_benchmark - Copy.ipynb` | Cell 23 |
| **Gap Analysis** — M6 degeneracy for C(n,k) ≥ 35 | `two_step_qaoa_corrected.ipynb` | `## New Analysis Cells` → Cell 20 |
| **KL scaling** — M7 advantage ≈ `log C(n,k)` | `two_step_qaoa_improved.ipynb` | `## Analysis: Why M6/M7 Should Outperform M5` → Cell 24 |
| | `two_step_qaoa_corrected.ipynb` | `Cell 19 — Final recommendations` |

---

### Final Report — Observation 2: Full Two-Step Pipeline (Qiskit)

> *"n=5, k=2. Stage-1 → |D⁵₂⟩ with fidelity 0.9999. Stage-2 found exact global optimum (Adjusted Energy: −0.318430). Feasibility = 1.0000 (noiseless)."*

| Report Element | File | Section / Cell |
|---|---|---|
| Full Stage-1 → Stage-2 pipeline wrapper | `two_step_qaoa_qiskit_stage2.ipynb` | `§ 5 — Full Two-Step Pipeline` → Cell 8 |
| Portfolio n=5, k=2 experiment | `two_step_qaoa_qiskit_stage2.ipynb` | `§ 6 — Experiment 1: Portfolio (n=5, k=2)` → Cell 9 |
| Fidelity = 0.9999 output | `two_step_qaoa_qiskit_stage2.ipynb` | Cell 9 output |
| Adjusted Energy = −0.318430 | `two_step_qaoa_qiskit_stage2.ipynb` | `§ 11 — Final Summary` → Cell 14 |
| Feasibility = 1.0000 (noiseless) | `two_step_qaoa_qiskit_stage2.ipynb` | `§ 6` → Cell 9 |
| Portfolio energy verification (cross-check) | `two_step_qaoa_corrected.ipynb` | `Cell 18 — Portfolio energy verification` |

---

### Final Report — Observation 3: Noise Sensitivity Collapse

> *"η=0.000 → Feasibility=1.0000. η=0.001 → Feasibility=0.7036. η≥0.020 → Feasibility≈0.3125 = C(5,2)/2⁵ (thermalized limit)."*

| Report Element | File | Section / Cell |
|---|---|---|
| Full noise sweep (η: 0.000 → 0.050) | `two_step_qaoa_qiskit_stage2.ipynb` | `§ 8 — Noise Sensitivity Sweep` → Cell 11 |
| η=0.001 → feasibility 0.7036 | `two_step_qaoa_qiskit_stage2.ipynb` | Cell 11 output |
| Thermalization floor = C(n,k)/2ⁿ = 0.3125 proof | `two_step_qaoa_corrected.ipynb` | `Cell 17 — Noise and feasibility floor analysis` |
| Portfolio noise sweep (M6 vs M7) | `two_step_qaoa_qiskit_stage2.ipynb` | `§ 6 — Experiment 1` → Cell 9 (noise sub-section) |
| Feasibility vs η plot | `two_step_qaoa_qiskit_stage2.ipynb` | `§ 10 — Visualisation` → Cell 13 |
| XY circuit depth → noise vulnerability discussion | `two_step_qaoa_corrected.ipynb` | `Cell 19 — Final recommendations and parameter guidance` |

---

### Final Report — Observation 4: Knapsack Encoding Advantage

> *"6-item, W=25: Slack QUBO = 11 qubits; Unbalanced Penalisation = 6 qubits. 45% reduction. Higher approximation ratio at p=1,2."*

| Report Element | File | Section / Cell |
|---|---|---|
| Slack-variable encoder (6 + ⌊log₂W⌋ + 1 qubits) | `QAOA_Project_2023311.ipynb` | Module 3 |
| Unbalanced penalisation encoder (n qubits only) | `QAOA_Project_2023311.ipynb` | Module 3 |
| 11-qubit vs 6-qubit comparison table | `QAOA_Project_2023311.ipynb` | Summary table cell |
| Approximation ratio at p=1,2 (both methods) | `QAOA_Project_2023311.ipynb` | Benchmark results |
| λ sensitivity study | `QAOA_Project_2023311.ipynb` | §6 / λ tuning section |

---

## 4. Notebook-by-Notebook Guide

### `QAOA_Project_2023311.ipynb` — Main Project Deliverable
Self-contained notebook covering the full P1/P2/P3 pipeline: QUBO encoding, `qubo_to_ising()`, `PennyLaneQAOA`/`QiskitQAOA` classes, slack vs unbalanced Knapsack, Markowitz Portfolio, and classical benchmarking tables.

**Covers:** Part A §2 (all problems), Part B §10.1–10.6, Final Report Modules 1–3, Observation 4.

---

### `two_step_qaoa_corrected.ipynb` — Authoritative M5/M6/M7 Benchmark *(Table 1 source)*
The primary corrected benchmark notebook. Contains all four loss functions, the 15-state × p=1..6 sweep, all five report figures, and the two novel analysis cells (gap analysis Cell 20, feasibility floor Cell 17, portfolio verification Cell 18).

| Notebook Section | Report Section |
|---|---|
| `## Loss Functions: M5, M6, M7` (Cells 4–8) | Final Report: Methodology Module 4 |
| `## Benchmark Configuration` + Cell 11 run | Final Report: Observation 1 (Table 1) |
| `## Figures` (Cells 14–18) | Final Report: Observation 1 (Figures 1–5) |
| `## New Analysis Cells` → Cell 20 | Final Report: Observation 1 (Gap Analysis) |
| Cell 17 — Noise feasibility floor | Final Report: Observation 3 |
| Cell 18 — Portfolio energy verification | Final Report: Observation 2 |
| Cell 19 — Final recommendations | Final Report: Future Directions |

---

### `two_step_qaoa_improved.ipynb` — M6 & M7 First Implementation
First rigorous derivation of M6 (Projector) and M7 (Proj+KL). Contains Cell 24: the theoretical proof of why M7's KL term provides a logarithmically growing advantage as C(n,k) grows.

| Notebook Section | Report Section |
|---|---|
| `## New: Subspace Projection Helpers` (Cell 5) | Final Report: Module 4 — k-hot helpers |
| `## M6 — Subspace Projector Loss` (Cell 7) | Final Report: Module 4 (M6 formula) |
| `## M7 — Subspace Projection + Uniformity` (Cell 9) | Final Report: Module 4 (M7 formula) |
| `## Full Benchmark Run` (Cell 15) | Final Report: Observation 1 |
| `## Figures` (Cells 19–23) | Final Report: Observation 1 (Figures 1–5) |
| `## Analysis: Why M6/M7 Should Outperform M5` (Cell 24) | Final Report: Observation 1 (KL scaling) |

---

### `two_step_qaoa_qiskit_stage2.ipynb` — Full Pipeline (Qiskit Aer) *(Observations 2 & 3 source)*
End-to-end hardware-realistic notebook. Stage-1 PennyLane statevector optimizer feeds directly into Stage-2 Qiskit Aer with XY-mixer (`XXPlusYYGate`). Produces the n=5,k=2 portfolio result (Energy=−0.318430, Feasibility=1.0) and the full noise sweep.

| Notebook Section | Report Section |
|---|---|
| `§ 1 — QUBO → Ising Conversion` (Cell 3) | Final Report: Module 1 |
| `§ 2 — Problem Definitions` (Cell 4) | Final Report: Module 3 |
| `§ 3 — Stage 1: Classical Dicke Approximation` (Cells 5–6) | Final Report: Module 4 |
| `§ 4 — Stage 2: Qiskit Aer XY-Mixer QAOA` (Cell 7) | Final Report: Module 5 |
| `§ 5 — Full Two-Step Pipeline` (Cell 8) | Final Report: Module 5 |
| `§ 6 — Experiment 1: Portfolio (n=5, k=2)` (Cell 9) | Final Report: **Observation 2** |
| `§ 7 — Experiment 2: MaxCut (n=6, k=3)` (Cell 10) | Final Report: P1 results |
| `§ 8 — Noise Sensitivity Sweep` (Cell 11) | Final Report: **Observation 3** |
| `§ 9 — Circuit Diagram` (Cell 12) | Final Report: Module 5 |
| `§ 10 — Visualisation` (Cell 13) | Final Report: Observation 3 figures |
| `§ 11 — Final Summary` (Cell 14) | Final Report: Observation 2 (energy table) |

---

### `expanded_benchmark.ipynb` — Baseline / M1 / M4 / M5 Expanded Benchmark
Benchmarks the earlier-generation methods (Baseline, M1 INTERP warm-start, M4 Combo, M5 Adaptive-α) across all 15 Dicke states. Does **not** include M6/M7 — use `two_step_qaoa_corrected.ipynb` for those.

| Notebook Section | Report Section |
|---|---|
| `### M1 — INTERP Warm-Start` (Cell 8) | Part B §9.1 (parameter warm-starting) |
| `### M5 — Adaptive-α Annealing` (Cell 12) | Final Report: Module 4 (M5 baseline) |
| `## Full Benchmark` (Cell 14) | Final Report: Observation 1 (M5 rows of Table 1) |
| Figures 1–5 (Cells 18–22) | Final Report: Observation 1 figures (M5 lines) |
| Aggregate statistics (Cell 23) | Final Report: Observation 1 summary stats |

---

### `expanded_benchmark - Copy.ipynb` — M5 Sigmoid α-Schedule Variant
Identical structure to `expanded_benchmark.ipynb` but replaces linear α-annealing in M5 with a **sigmoid schedule** (`m5_adaptive_sigmoid()`). Used to test smoother annealing for harder Dicke states.

**Unique cells:** Cell 12 (`m5_adaptive_sigmoid()`), Cell 21 (sigmoid α-schedule figure).

---

## 5. Key Formulas Implemented

| Formula | File | Cell |
|---|---|---|
| `x_i = (1−Z_i)/2` (Ising mapping) | `two_step_qaoa_qiskit_stage2.ipynb` | Cell 3 |
| QAOA state `\|ψ(γ,β)⟩ = Π U(H_M,β_p)U(H_C,γ_p)\|ψ₀⟩` | All notebooks | PennyLane circuit |
| XY mixer `H_XY = Σ_{i,j}(X_iX_j + Y_iY_j)` | `two_step_qaoa_qiskit_stage2.ipynb` | Cell 7 |
| Dicke state `\|D^n_k⟩ = 1/√C(n,k) Σ_P P(\|1⟩^⊗k\|0⟩^⊗(n−k))` | All notebooks | Cell 2/3 (`exact_dicke()`) |
| M6 loss `1 − ⟨ψ\|P_k\|ψ⟩²` | `two_step_qaoa_improved.ipynb` | Cell 7 |
| M7 loss `(1−⟨P_k\|ψ⟩²) + β·KL(q∥Uniform)` | `two_step_qaoa_improved.ipynb` | Cell 9 |
| Unbalanced penalty `ζ(x) = −λ₁h(x) + λ₂h(x)²` | `QAOA_Project_2023311.ipynb` | Module 3 |
| Thermalization floor `C(n,k)/2^n` | `two_step_qaoa_corrected.ipynb` | Cell 17 |

---

## 6. Dependencies

```bash
pip install pennylane qiskit qiskit-aer numpy scipy matplotlib yfinance
```

| Package | Version | Used for |
|---|---|---|
| `pennylane` | ≥0.36 | Stage-1 statevector QAOA, all benchmarks |
| `qiskit` | ≥1.0 | Stage-2 XY-mixer circuit, transpilation |
| `qiskit-aer` | ≥0.13 | Depolarising noise simulation |
| `numpy` / `scipy` | latest | COBYLA/L-BFGS-B optimization, linear algebra |
| `matplotlib` | latest | All 5 report figures |
| `yfinance` | latest | Yahoo Finance data for Portfolio (P3) |

---

## 7. How to Run

**Recommended execution order:**

```
1. QAOA_Project_2023311.ipynb
   → Problems P1, P2, P3 baseline; infrastructure; Knapsack encoding comparison

2. two_step_qaoa_improved.ipynb
   → M6 and M7 first derivation and theory

3. two_step_qaoa_corrected.ipynb
   → Full corrected M5/M6/M7 benchmark → reproduces Table 1 of report

4. expanded_benchmark.ipynb
   → Baseline/M1/M4/M5 sweep → supplementary figures

5. expanded_benchmark - Copy.ipynb
   → Sigmoid α-schedule M5 variant (optional)

6. two_step_qaoa_qiskit_stage2.ipynb
   → Full Qiskit Aer pipeline → reproduces Observations 2 & 3
   (Run Cell 1 first to install packages, then restart kernel)
```

---

## 8. References

1. Farhi, Goldstone, Gutmann. *A Quantum Approximate Optimization Algorithm.* arXiv:1411.4028, 2014.
2. Lucas, A. *Ising Formulations of Many NP Problems.* Frontiers in Physics, 2:5, 2014.
3. Kandala et al. *Hardware-Efficient Variational Quantum Eigensolver.* Nature, 2017.
4. Montañez-Barrera et al. *Unbalanced Penalization: A New Approach to Encode Inequality Constraints.* QST, IOP Publishing, 2022.
5. Brandhofer et al. *Benchmarking QAOA for Portfolio Optimization.* Quantum Inf. Processing 22, Springer, 2022.
6. Qian et al. *Comparative Study of Variations in QAOA for TSP.* Entropy 25(8), 2023.
7. Blekos et al. *A Review on QAOA and Its Variants.* Physics Reports, 2024.
8. Minato, Y. *Two-Step QAOA: Enhancing Quantum Optimization by Decomposing K-hot Constraints.* arXiv:2408.05383, 2025.
