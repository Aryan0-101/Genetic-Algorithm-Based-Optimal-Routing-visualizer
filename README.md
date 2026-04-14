# Optimal Routing AI: GA vs Dijkstra

[![Python Version](https://img.shields.io/badge/python-3.8%2B-blue.svg)](https://www.python.org/downloads/)
[![Streamlit App](https://static.streamlit.io/badges/streamlit_badge_black_white.svg)](https://streamlit.io)

A robust, interactive demonstration of Multi-Objective Evolutionary Algorithms solving NP-Hard QoS-constrained network routing problems across a deeply complex multi-attribute ISP backbone.

## 🌟 Overview

Routing within modern ISP backbones is rarely a single-objective problem. While algorithms like Dijkstra are exceptional at finding the absolute cheapest path, they often fail when introduced to multi-dimensional Quality of Service (QoS) constraints (e.g., maximizing reliability while maintaining strict latency ceilings).

This project contrasts standard shortest-path algorithms against a custom **Non-dominated Sorting Genetic Algorithm II (NSGA-II)**. It visually and quantitatively demonstrates how evolutionary algorithms can successfully navigate constrained 3D manifolds where traditional greedy algorithms fall into "traps" or violate strict SLAs.

## ✨ Key Features

- **NSGA-II Multi-Objective Engine**: Instead of naive fitness scalarization, the core solver implements Pareto Front sorting, Crowding Distance diversity maintenance, and Constrained Domination.
- **Constrained Domination Tunneling**: Routes that violate SLAs (like a max latency constraint) aren't instantly discarded. They are graded on their margin of violation, allowing the algorithm to safely traverse temporarily 'invalid' regions to uncover optimal subpaths.
- **Complex Topologies**: Features a dense Indian ISP backbone network with over 40 nodes/links, deliberately injected with "Satellite Traps" (ultra-cheap but high-latency links) to test algorithm resilience.
- **Interactive Streamlit Dashboard**: A full GUI to tweak GA hyperparameters (Mutation Rate, Population Size, Constraints), visualize convergence via Plotly, and compare the AI routes against Baseline Dijkstra.
- **Educational Visuals**: Includes an interactive HTML module (`about.html` - available locally) showcasing real-time packet routing animations that highlight the algorithmic differences.

## 🚀 Quick Start

### Installation

1. **Clone the repository** (if applicable):
   ```bash
   git clone <repository-url>
   cd Routing_GA
   ```

2. **Install requirements**:
   Ensure you have Python 3.8+ installed. Then install the necessary dependencies:
   ```bash
   pip install streamlit networkx plotly numpy pandas
   ```

### Running the App

Launch the interactive dashboard locally:

```bash
streamlit run start.py
```
*(Or simply run `python start.py` which executes the Streamlit process).*

The application will automatically spin up on your `localhost` (usually `http://localhost:8501`).

## 🧠 Technical Architecture

- `app.py`: The frontend Streamlit UI layout, sidebar controls, event handling, and Plotly graph generation.
- `routing_engine.py`: The computational core containing the Graph architecture, Edge attributes, Dijkstra implementations, and the deep NSGA-II GA mechanics (`_fast_non_dominated_sort`, `_crowding_distance`, etc.).
- `start.py`: A lightweight entry point script.

### The Algorithm Breakdown

1. **Initialization**: Stochastic depth-capped random walks generate the initial population of valid source-to-destination paths.
2. **Evaluation (`_eval_path`)**: Paths are assessed for Cost (minimize), Latency (minimize), and Unreliability (minimize). Constraint violations (CV) are calculated natively.
3. **Sorting**: The population is sorted into Pareto fronts using `_fast_non_dominated_sort`, heavily biased by Constrained Domination (feasible paths always dominate infeasible ones).
4. **Selection**: To preserve diverse genetic material and prevent premature convergence, tournament selection relies on Front Rank, then broken by Crowding Distance.
5. **Reproduction**: Shared-node crossover with bridge repair logic mixes traits, while subpath mutation injects new random walks dynamically.

## 🎯 The "Satellite Trap" Demo

To perfectly illustrate the engine's power, setup a route from **DEL** to **CHN** with a tight **Latency Constraint (e.g., Max 100ms)**.

- **Dijkstra** will eagerly try to utilize the direct DEL-CHN Satellite link because it only costs `3`. However, the latency is `140ms`, causing it to immediately violate the QoS constraints.
- **The GA** will evaluate the Pareto front, recognize the constraint boundary, and intelligently route the packet through a slightly more expensive but infinitely safer premium Fiber mesh, successfully delivering the payload under 100ms.

## 📝 License

This project is open-source and available under the [MIT License](LICENSE).
