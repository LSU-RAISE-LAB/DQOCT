# Dynamic Quantum Optimal Topology for Consensus

![License](https://img.shields.io/badge/license-Academic-blue)
![Python](https://img.shields.io/badge/python-3.10%2B-blue)
![Qiskit](https://img.shields.io/badge/Qiskit-0.46.3-purple)
![Qiskit-Algorithms](https://img.shields.io/badge/qiskit--algorithms-0.3.1-purple)

> Official code and numerical experiments accompanying the paper:  
> *"Dynamic Quantum Optimal Communication Topology Design for Consensus Control in Linear Multi-Agent Systems"*

---

## 🔬 Overview

This repository contains a **quantum-assisted communication topology design framework** for consensus control in linear multi-agent systems (MASs).

At each topology update, we solve a **mixed-integer quadratic program (MIQP)** via a **three-block ADMM**:
- Block 1: convex quadratic program in relaxed edges and flow variables  
- Block 2: pure binary QUBO in edge indicators, solved by  
  - either **brute force** (for debugging and small graphs)  
  - or **Quantum Imaginary Time Evolution (QITE)** on a Qiskit simulator  
- Block 3: closed-form auxiliary update and dual update

The resulting time-varying Laplacians are used in **first-order** and **second-order** consensus dynamics, and the code reproduces all numerical examples and figures reported in the paper.

> Note: All quantum routines in this repository are executed on **Qiskit simulators** only. No real quantum hardware was used for the results in the paper.

---

## 🌟 Features

- Dynamic communication topology design via **three-block ADMM**
- Flow-based **exact connectivity** and **degree constraints** on each node
- Cost model combining:
  - communication cost  
  - distance-based penalties  
  - quadratic degree regularization
- Binary ADMM block mapped to a **QUBO** and then to an **Ising Hamiltonian**
- **QITE** solver (VarQITE + Imaginary McLachlan) vs **brute-force** QUBO oracle
- Support for:
  - **First-order consensus** (\(\dot{x} = -Lx\))  
  - **Second-order consensus** (\(\dot{x} = v, \dot{v} = -\alpha Lx - \beta Lv\))
- Automatic plotting of:
  - agent trajectories  
  - topology evolution (edge activity over time)  
  - consensus error over time

---

## 📁 Code Structure

The main script implements the complete **closed-loop online procedure**:

- Builds the candidate complete graph and incidence matrix
- Initializes ADMM variables \((z, r, s, \lambda)\)
- Repeats for each update step:
  1. Solves Block 1 (relaxed QP) via `cvxpy` + SCS  
  2. Builds the QUBO for Block 2 and solves it  
     - brute force if `USE_BRUTE_FORCE = True`  
     - QITE (VarQITE) if `USE_BRUTE_FORCE = False`  
  3. Updates the auxiliary variable and dual variables in closed form  
  4. Thresholds the relaxed edges, builds the Laplacian, and simulates consensus  
  5. Logs eigenvalues, algebraic connectivity, and consensus error
- Stores all trajectories and generates plots at the end of a run

By default, the script calls:

```python
if __name__ == "__main__":
    online_topology_control()
