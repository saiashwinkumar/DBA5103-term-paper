# Optimizing Urban Traffic Flow (Static vs. Simulation-Guided Scheduling)

This repository contains the code and experiments from an operations research project on **traffic signal scheduling**. We compare **static, demand-aware optimization** against **simulation-guided heuristics** to maximize network throughput and reduce congestion under realistic queueing and signal constraints.

## Objective
Design traffic-light schedules at intersections (one incoming street green per second) to **maximize total simulation score**, where each car earns:
- a fixed bonus for finishing within the horizon, plus
- a time bonus for finishing earlier.

## Datasets & Test Environments
1) [**Hash Code 2021 – Traffic Signaling dataset (Kaggle):**](https://www.kaggle.com/competitions/hashcode-2021-oqr-extension/data)  
  Large synthetic city instance used as the primary benchmark.

2) [**Real-city case study: Bengaluru sub-network**](https://figshare.com/articles/dataset/Urban_Road_Network_Data/2061897/1)
   - Road network clustered into regions; synthetic peak-hour trips generated to stress-test scalability.
   - Outputs include congestion hotspots, wasted-green diagnostics, and completion metrics.

3) **Synthetic “Organic City” stress test**
   - Random geometric graph (hub-and-spoke topology) + commuter-hub demand to evaluate robustness under irregular networks.

## Methodology (Models Implemented)
We implemented and compared four scheduling paradigms:

- **Model 1: Greedy (Baseline Round-Robin / traffic-count heuristic)**  
  Simple cyclic schedules; robust but can waste green time on low-demand streets.

- **Model 2: Demand-Weighted (MIP heuristic using street demand)**  
  Compute street demand from car paths and allocate green time proportionally using a compact MILP (Gurobi), improving efficiency by prioritizing busy approaches.

- **Model 3: Demand-Weighted Local Search (Simulation-guided hill-climbing)**  
  Initialize using demand-proportional timings, then iteratively improve schedule via swaps/±1 duration moves using simulation feedback (black-box optimization).

- **Model 4: Time-Ordered (Arrival-time synchronization)**  
  Attempts “green-wave” style scheduling based on predicted arrivals; shown to be brittle in high-variance, organic topologies.

## Key Results (Hash Code Benchmark)
| Model | Total Score |
|------|-------------|
| Model 1: Greedy | 4,006,847 |
| Model 2: Demand-Weighted (MIP) | 4,106,799 |
| Model 3: Local Search | 4,065,548 |
| Model 4: Time-Ordered | 4,015,598 |

## Organic City Stress Test (Controlled Comparison)
All models achieved **100% completion**, but demand-aware approaches reduced congestion significantly:
- Models **2 & 3** achieved the **best scores** and **lowest average wait time (~191.6s/car)** vs baseline (~226.8s/car),
showing that **demand allocation** beats naive cycling and brittle arrival-time synchronization in hub-and-spoke networks.

## Main Takeaways
- Traffic scheduling is primarily a **demand allocation problem**, not just timing synchronization.
- **Demand-aware methods (Models 2 & 3)** consistently outperform naive/static approaches.
- **Simulation-guided optimization (Model 3)** yields the best (or near-best) outcomes but at higher compute cost.
- Network **topology matters**: irregular hub-and-spoke structures benefit most from demand-prioritized scheduling.

## Tech Stack
- Python (data parsing, schedule generation, simulation)
- Gurobi (MILP for demand-weighted scheduling)
- Visualization for congestion heatmaps / diagnostics (optional in repo)

## Reproducibility
Run the pipeline end-to-end to reproduce:
- schedule generation for each model,
- simulation scoring,
- stress tests across environments (Hash Code instance, Organic City, Bengaluru case study),
- summary tables and plots.
