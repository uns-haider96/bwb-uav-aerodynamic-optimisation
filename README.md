# BWB UAV Aerodynamic Optimisation — AI-Driven Surrogate Modelling and CFD

[![Publication](https://img.shields.io/badge/Published-Journal_of_Aerospace_Engineering-1B3A6B?style=flat)](https://doi.org/10.1177/09544100261447561)
[![DOI](https://img.shields.io/badge/DOI-10.1177/09544100261447561-blue?style=flat)](https://doi.org/10.1177/09544100261447561)
[![Conference](https://img.shields.io/badge/Presented-IBCAST_2025-orange?style=flat)]()
[![L/D Improvement](https://img.shields.io/badge/L/D_Improvement-23%25-success?style=flat)]()

> **Published research: AI-driven aerodynamic shape optimisation of a Blended-Wing-Body UAV using Gaussian Process Regression surrogate modelling and iterative CFD, achieving a 23% improvement in simulated lift-to-drag ratio.**
>
> *Proceedings of the Institution of Mechanical Engineers, Part G: Journal of Aerospace Engineering (SAGE/IMechE)*
> DOI: [10.1177/09544100261447561](https://doi.org/10.1177/09544100261447561)

---

## Overview

This repository documents the research methodology, computational tools, and fabrication process for a Blended-Wing-Body (BWB) UAV designed for long-endurance, low-power ISR (Intelligence, Surveillance, and Reconnaissance) missions.

The core contribution is a **GPR-CFD integrated design optimisation pipeline** that dramatically reduces the computational cost of aerodynamic shape optimisation while improving design quality — a methodology applicable to any engineering system where simulation is expensive and design space exploration is required.

**The fundamental problem this research solves:** Iterative CFD simulation across a large design space is computationally prohibitive. Running a full CFD evaluation for every candidate design is impractical. This work implements a Gaussian Process Regression surrogate model as a fast, differentiable approximator of the aerodynamic response surface — allowing the design space to be searched efficiently.

---

## Publication

**Full citation:**

> Shah, M.U.H., Naseem, M.S., et al. (2026). Design and Optimization of a Blended Wing Body UAV Using AI-Driven Surrogate Modeling and CFD Analysis. *Proceedings of the Institution of Mechanical Engineers, Part G: Journal of Aerospace Engineering*. SAGE/IMechE. DOI: 10.1177/09544100261447561

**Conference presentation:**

> IBCAST 2025 — International Bhurban Conference on Applied Sciences and Technology  
> Fluid Dynamics Track · Oral Presentation · August 19-22, 2025 · Bhurban, Pakistan  
> Camera-ready paper submitted for IEEE Xplore conference proceedings

---

## UAV Specifications

| Parameter | Value |
|---|---|
| Configuration | Blended-Wing-Body (BWB) |
| Wingspan | 1 metre |
| Mission profile | Long-endurance, low-power ISR |
| Primary material | Carbon Fibre Reinforced Polymer (CFRP) |
| Fabrication method | Vacuum-bagging infusion |
| Validation | Wind tunnel testing |

---

## Research Methodology

### Stage 1 — Design Space Definition

Design variables selected for optimisation:
- Wing sweep angle
- Aerofoil section (NACA family)
- Blending ratio (wing-to-body transition)
- Twist distribution along span

Constraints:
- Structural feasibility
- Minimum payload volume
- Static stability margin

### Stage 2 — CFD Simulation and DOE Sampling

A structured **Design of Experiments (DOE)** sampling strategy was used to select an initial set of candidate designs covering the design space efficiently. Each candidate design was evaluated using CFD simulation:

- Computational mesh generated with structured blocking approach
- Mesh convergence study conducted — grid independence verified across three refinement levels
- Flow solver run at target cruise conditions (angle of attack sweep)
- Outputs extracted: lift coefficient (CL), drag coefficient (CD), lift-to-drag ratio (L/D)

### Stage 3 — GPR Surrogate Model Construction

A **Gaussian Process Regression (GPR)** model was trained on the CFD-evaluated design sample:

```python
from sklearn.gaussian_process import GaussianProcessRegressor
from sklearn.gaussian_process.kernels import RBF, Matern

kernel = Matern(length_scale=1.0, nu=2.5)
gpr = GaussianProcessRegressor(kernel=kernel, alpha=1e-6, normalize_y=True)
gpr.fit(X_sample, y_ld_ratio)
```

**Why GPR specifically:**
- Provides **uncertainty estimates** alongside predictions — high uncertainty regions guide where to invest additional CFD evaluations
- Smooth, differentiable response surface — compatible with gradient-based optimisation
- Effective with small training datasets — important when CFD evaluations are expensive

### Stage 4 — Surrogate-Based Optimisation

With the fast GPR model available, a systematic design space search was conducted:
- Evaluated thousands of candidate designs using GPR predictions (milliseconds each)
- Identified high-performance regions guided by both predicted performance and GPR uncertainty
- Refined promising designs with targeted additional CFD evaluations
- Selected optimal configuration based on maximum predicted L/D ratio

**Result: 23% improvement in simulated lift-to-drag ratio over the baseline configuration.**

### Stage 5 — Physical Fabrication

The optimised airframe geometry was fabricated using:

1. **3D-printed split moulds** — designed in SolidWorks for each airframe section
2. **Carbon fibre layup** — CFRP fabric positioned in split moulds with target fibre orientation
3. **Vacuum-bagging infusion** — vacuum applied to consolidate layup and achieve target:
   - Skin thickness uniformity
   - Fibre volume fraction
   - Surface finish quality

### Stage 6 — Wind Tunnel Validation

The fabricated airframe was tested in a controlled wind tunnel environment:
- Lift and drag measurements taken across the design angle-of-attack range
- Experimental CL, CD, and L/D data compared against CFD predictions
- Correlation confirmed aerodynamic model validity

---

## Key Results

| Metric | Baseline | Optimised | Improvement |
|---|---|---|---|
| Lift-to-Drag Ratio (L/D) | — | — | **+23%** |
| Computational approach | Full CFD per design | GPR surrogate | Orders of magnitude faster |
| Physical validation | — | Wind tunnel confirmed | CFD model validated |

---

## Why GPR over Other Surrogate Methods

| Method | Uncertainty Estimate | Small Data | Interpretability |
|---|---|---|---|
| GPR | Yes | Excellent | High |
| Neural Network | No (standard) | Poor | Low |
| Polynomial Response Surface | No | Good | High |
| Kriging | Yes (equivalent) | Good | Medium |

GPR was selected because the combination of uncertainty quantification and effectiveness with small DOE datasets made it ideal for this engineering optimisation context.

---

## Broader Applicability of the GPR-CFD Pipeline

The surrogate modelling approach developed here is not limited to UAV aerodynamics. The same pipeline — DOE sampling, expensive simulation, GPR surrogate, efficient space exploration — applies to:

- **Gas turbine blade cooling optimisation**
- **Heat exchanger geometry design**
- **Automotive aerodynamic development**
- **Wind turbine blade profile optimisation**
- **Chemical reactor design**

Any engineering design problem where simulation is expensive and design space is large is a candidate for this methodology.

---

## Repository Structure

```
bwb-uav-aerodynamic-optimisation/
├── docs/
│   ├── research_methodology.md          # Detailed methodology documentation
│   └── fabrication_process.md           # CFRP fabrication step-by-step
├── surrogate_modelling/
│   ├── doe_sampling.py                   # Design of Experiments sampling
│   ├── gpr_model.py                      # GPR training and prediction
│   ├── optimisation_loop.py             # Surrogate-based design search
│   └── uncertainty_visualisation.py     # GPR uncertainty surface plots
├── cfd_post_processing/
│   ├── extract_aero_coefficients.py     # CL, CD, L/D extraction from CFD output
│   └── convergence_monitor.py           # Mesh convergence tracking
├── results/
│   ├── design_space_exploration.png     # GPR prediction surface
│   ├── uncertainty_map.png              # GPR uncertainty across design space
│   ├── optimisation_history.png         # L/D improvement over iterations
│   └── wind_tunnel_vs_cfd.png          # Experimental validation comparison
├── fabrication/
│   └── mould_design_notes.md            # 3D-printed mould specifications
└── README.md
```

---

## Tools and Software

- **Python** — GPR surrogate modelling, DOE sampling, post-processing
- **Scikit-Learn** — GaussianProcessRegressor
- **SolidWorks** — Airframe geometry and mould design
- **CFD Solver** — Flow simulation and mesh generation
- **MATLAB** — Data processing and visualisation
- **Vacuum infusion equipment** — Physical fabrication

---

## Supervisor

This work was conducted under the supervision of **Dr. Muhammad Shoaib Naseem**, COMSATS University Islamabad, Wah Campus.

---

## Author

**Muhammad Uns Haider Shah**  
PEC Registered Mechanical Engineer · MECH/059916  
B.Sc. Mechanical Engineering, COMSATS University Islamabad (2021-2025)  
[LinkedIn](https://www.linkedin.com/in/muhammad-uns-haider-shah) · [Email](mailto:shahuns963@gmail.com) · [DOI](https://doi.org/10.1177/09544100261447561)
