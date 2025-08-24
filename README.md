# Optimizing Surgical Schedules with GA & d-PSO

This repository implements and evaluates **two nature-inspired metaheuristics** for the **Surgical Scheduling Problem (SSP)**:

* a **Genetic Algorithm (GA)**
* a **Discrete Particle Swarm Optimization (d-PSO)**

Both algorithms are tested on stochastic hospital-like instances under strict **no-buffer constraints** across 300 **Monte Carlo simulations**, producing statistical comparisons, convergence plots, boxplots, histograms, and Gantt charts.

> This code reproduces the experiments described in:
> **“Optimizing Surgical Schedules: A Comparative Performance Analysis of Genetic Algorithms and Discrete Particle Swarm Optimization”**
> *J. Lara Arce, J. Sepúlveda, R. Santana, M. Becerra-Rozas, B. Gana, D. Villalobos (ARTIIS 2025)*

---

## Problem Overview

* **Jobs (15 surgeries)**, each with **3 sequential operations**:

  1. Preoperative anesthesia (APR)
  2. Surgery (OR)
  3. Recovery (ARR)
* **Resources:** 4 APRs, 4 ORs, 4 ARRs, plus mapped anesthesiologists, surgeons, and recovery physicians.
* **Constraints:**

  * **Setup and cleanup times** by surgery type.
  * **Blocking (no buffer):** patients cannot leave a stage until the next resource is free.
  * **Maximum wait times** enforced.
* **Objective:** minimize

  * the **makespan** (completion of last surgery) +
  * a weighted penalty for delayed start times (`α = 1e-6`).

---

## Algorithms

### Genetic Algorithm (GA)

* **Encoding:**

  * Surgery sequence ×3 (one copy per stage).
  * Room assignment for each operation.
* **Operators:**

  * Inverted roulette wheel selection.
  * Order Crossover (OX1) for sequences + single-point crossover for rooms.
  * Swap mutations and reassignment of rooms.
  * Elitism: top-2 individuals preserved.

### Discrete PSO (d-PSO)

* **Encoding:** discrete sequence of surgeries + room assignments.
* **Velocity:** continuous vectors (rank for sequences, normalized for rooms).
* **Update:** inertia + cognitive + social terms → passed through sigmoid → probability of swap/reassignment.
* **Exploration:** occasionally copies from *pBest* or *gBest*.

---

## Requirements

```txt
Python 3.9+ (local) or Google Colab
numpy
matplotlib
scipy
tqdm
```

Install locally:

```bash
pip install numpy matplotlib scipy tqdm
```

---

## Running the Experiments

### Option A: Google Colab

1. Open the notebook/script in Colab.
2. Run all cells — by default it performs **300 Monte Carlo simulations**.
3. Outputs are stored in `Actualizado/graphs/`:

```
graphs/
├─ boxplot/           # Makespan comparison
├─ convergence/       # Fitness evolution
├─ gantt/             # Gantt charts (best runs)
├─ histograms/        # Distribution histograms
└─ csv/               # Schedules, statistics, p-values
```

### Option B: Local

```bash
python main.py
```

---

## Parameter Settings

| Parameter                    | GA   | d-PSO |
| ---------------------------- | ---- | ----- |
| Population / Swarm Size      | 30   | 30    |
| Max Generations / Iterations | 1000 | 1000  |
| Crossover Probability        | 0.8  | –     |
| Mutation Probability         | 0.3  | –     |
| Elitism Count                | 2    | –     |
| Inertia (W)                  | –    | 0.7   |
| Cognitive coeff. (C1)        | –    | 1.5   |
| Social coeff. (C2)           | –    | 1.5   |
| Velocity limits              | –    | ±4.0  |
| Alpha (α)                    | 1e-6 | 1e-6  |

---

## Outputs

* **Statistics CSV:** summary of valid runs (`summary_statistics.csv`).
* **Paired test report:** Wilcoxon + bootstrap CI + effect size (TXT/CSV).
* **Convergence plots:** evolution of best & average fitness.
* **Boxplot & histograms:** performance distributions.
* **Gantt charts:** schedules for best GA and d-PSO runs.
* **Schedules CSVs:** detailed task logs, room strategies, and start timetables.

---

## Results Summary

* **GA**: more consistent, lower mean makespan (\~1180.1 ± 30.2 min).
* **d-PSO**: found the best single schedule overall (1095.28 min).
* **Gap**: typically <5 minutes (negligible in hospital context).
* **Interpretation:**

  * GA → robustness, day-to-day planning.
  * d-PSO → exploratory power, occasionally discovers superior schedules.

---

## Citation

If you use this repository, please cite the paper:

```bibtex
@inproceedings{lara2025optimizing,
  title     = {Optimizing Surgical Schedules: A Comparative Performance Analysis of Genetic Algorithms and Discrete Particle Swarm Optimization},
  author    = {Lara Arce, José and Sepúlveda, Javier and Santana, Ricardo and Becerra-Rozas, Marcelo and Gana, Bady and Villalobos, Daniel},
  booktitle = {Proceedings of ARTIIS 2025},
  year      = {2025}
}
```

---

## License

Distributed under the **MIT License**.

---

## Acknowledgements

Supported by:

* **DI Iniciación PUCV**, project *2025/PUCV/039.725/2025*.
* **ANID**: National Doctorate Scholarship 2024–21240115.
* Collaboration with **Hospital Puerto Montt** (Anesthesia & OR Service).
