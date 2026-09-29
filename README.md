# Modular cost-to-go functions for MPC via anchored ANOVA - A proof-of-concept

Dinesh Krishnamoorthy, Department of Engineering Cybernetics, NTNU

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.XXXXXXX.svg)](https://doi.org/10.5281/zenodo.XXXXXXX)
[![License: all rights reserved](https://img.shields.io/badge/license-all%20rights%20reserved-lightgrey.svg)](LICENSE)

These four notebooks illustrate, on small inventory-control problems, the central idea of synthesizing modular cost-to-go functions. The idea is that the cost-to-go of a long-horizon MPC can be split *exactly* into low-dimensional modules by an anchored ANOVA decomposition. The modules can then be learned separately and composed into the terminal cost of a short-horizon MPC.

They are proofs of concept, not a software package. 

---

## The idea

A long-horizon MPC (the *expert*) is replaced by a one-step MPC with a learned terminal cost $V$. Instead of learning $V$ as one monolithic function, we anchor it at a reference point $\mathbf a$ and decompose it with anchored ANOVA (a.k.a. cut-HDMR):

$$V(\mathbf{z}) = V(\mathbf{a}) + \dots $$


The identity is exact, and every module vanishes on the anchor cross. First-order modules need expert data only along 1-D lines, and each interaction only in the space of its own variables. The central hypothesis is that the significant modules follow the structure of the problem: its couplings, its sources of uncertainty, and its integer decisions. The notebooks test this hypothesis in three settings:
- distributed MPC with a shared resource constraint
- MPC with uncertainty
- MPC with integer actions

## Notebooks

| # | Notebook | Setting  |
|---|---|---|
| 1 | [`anchored_ANOVA_introduction.ipynb`](anchored_ANOVA_introduction.ipynb) | Anchored ANOVA on the Rosenbrock function |
| 2 | [`WP2.1_ANOVA_for_coupled_agents.ipynb`](WP2.1_ANOVA_for_coupled_agents.ipynb) | Two inventories sharing a limited supply (distributed MPC) | 
| 3 | [`WP2.2_ANOVA_uncertainty.ipynb`](WP2.2_ANOVA_uncertainty.ipynb) | One inventory with uncertain, time-varying demand (multistage scenario MPC) | 
| 4 | [`WP2.3_ANOVA_mixed_integer.ipynb`](WP2.3_ANOVA_mixed_integer.ipynb) | One inventory with orders in lots of 5 units (mixed-integer MPC) |

**1. Introduction to anchored ANOVA.** A tutorial introduction to anchored ANOVA decomposition. 

**2. Coordination.** Two inventories are coupled only through a shared capacity.
- _Baseline module_: The first-order modules equal each agent's own uncoupled cost-to-go, that agents can learn independently. 
- _Correction module_: The interaction module is the price of sharing. It is non-zero only where both inventories are short.

**3. Uncertainty.** Future demand is unknown, and the expert is a multistage scenario MPC with non-anticipativity constraints.
- _Baseline module_: Represents the nominal cost-to-go. 
- _Correction module_: All decision-relevant demand information sits in the state–demand interaction module.
  
**4. Integer decisions.** Inventory problem, but $u \in {0,5}$. 
- _Baseline module_: cost-to-go of the relaxed problem ignoring the integer requirement. Ignoring integrality costs about 5 % in closed loop (up to 22 % from some states).
- _Correction module_: accounts for the integrality effect.
  - With 72 mixed-integer expert solves, the modular one-step MPC comes within 0.3 % of the expert's closed-loop cost, as close as the one-step MPC with the exact cost-to-go.
  - A monolithic fit on the same kind of data needs 862 solves (12×) to come within 1 %.

## Running the notebooks

The notebooks are committed with their outputs, so they can be read on GitHub without running anything.

To run them locally:

```bash
git clone https://github.com/<user>/<repo>.git
cd <repo>
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter lab
```

- **Tested with:** Python 3.11, NumPy 2.4, SciPy 1.17, Matplotlib 3.10, CVXPY 1.9 with the Clarabel solver.
- **Solvers:** the experts are solved with CVXPY/Clarabel, or exactly by dynamic programming (the mixed-integer expert in notebook 4).
- **Regression:** all modules are fitted with RBF networks (`scipy.interpolate.RBFInterpolator`). No neural networks are used.
- **Reproducibility:** random seeds are fixed, so re-running reproduces the reported numbers up to solver tolerance.

## Purpose and citation

These notebooks are shared mainly for the reviewers, as supporting material for the preliminary results/proof-of-concept. They are research prototypes on small problems, not a finished method or a software package.

**If you would like to use these notebooks, or the approach they demonstrate, in your own work, please contact the author first** (dinesh.krishnamoorthy@ntnu.no). The method is under active development, and I am happy to discuss it and point you to the latest results.


## License

© 2026 Dinesh Krishnamoorthy. All rights reserved.

The notebooks may be viewed, downloaded and run for the purpose of evaluating them, in particular by the reviewers of the proposal. Any other use, including copying, modifying or redistributing the code, text or figures, or incorporating them in other work, requires prior written permission from the author. See [LICENSE](LICENSE).

## Contact

Dinesh Krishnamoorthy, dinesh.krishnamoorthy@ntnu.no
