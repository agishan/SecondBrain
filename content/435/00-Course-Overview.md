# MSE 435: Practical Large Scale Optimization


## Course Overview

This course covers advanced techniques for solving **Mixed Integer Programs (MIPs)** at scale. The key insight throughout is that MIPs are NP-hard, so we need clever algorithms that combine **relaxation** (to get bounds) with **enumeration** (to find optimal solutions).

```
                        MIP Solving Framework
                              │
         ┌────────────────────┼────────────────────┐
         │                    │                    │
    Formulation          Relaxation           Enumeration
         │                    │                    │
    ┌────┴────┐          ┌────┴────┐          ┌────┴────┐
    │         │          │         │          │         │
 Compact   Extended     LP     Lagrangian   Branch   Branch
 (Week 2)  (Week 2-3)  (all)   (Week 5)    & Bound  & Price
                                           (Week 3) (Week 5)
```

---

## Table of Contents by Week

### Week 1: LP Review (Prerequisites)
> *Foundation: Review of Linear Programming methods needed for the course*

| Topic | File | Key Concepts |
|-------|------|--------------|
| The Simplex Method | [[Week1_Review]] | Standard form, reduced costs, tableau operations |
| The Revised Simplex Method | [[Week1_Review#0.4 The Revised Simplex Method]] | Basis inverse, entering/leaving variables |
| Initializing Simplex | [[Week1_Review#0.5 Initializing the Simplex Method]] | Two-Phase method, Big M method |
| Duality in LP | [[Week1_Review#0.8 Duality in LP Primal-Dual Relationship]] | Primal-dual pairs, complementary slackness |
| Sensitivity Analysis | [[Week1_Review#0.10 Sensitivity Analysis in LP]] | Changes in $c$ and $b$, basis optimality |

### Week 2: MIP Formulations
> *Foundation: How you model a problem affects how fast you can solve it*

| Topic | File | Key Concepts |
|-------|------|--------------|
| Disaggregated vs Aggregated | [[Week 2]] | $[BP_d]$ vs $[BP_a]$ formulations |
| [[Strength of Formulations]] | [[Week 2#1.2.1 Strength of Formulations]] | LP relaxation tightness, convex hull |
| [[Set Covering Formulation]] | [[Week 2#1.4 A Set Partitioning/Covering Formulation]] | Packing patterns, exponential variables |
| [[Cover Inequalities]] | [[Week 2#1.5 A Formulation with a large number of constraints]] | Constraint generation |

### Week 3: Branch-and-Bound & Cutting Planes
> *Core algorithms: Systematic search + tightening relaxations*

| Topic | File | Key Concepts |
|-------|------|--------------|
| [[Branch-and-Bound]] | [[Branch-and-Bound]] | Bounding, branching, fathoming, incumbent |
| [[Cutting Planes]] | [[Cutting-Planes-and-Branch-and-Cut]] | Valid inequalities, Gomory cuts |
| [[Branch-and-Cut]] | [[Cutting-Planes-and-Branch-and-Cut#The Branch-and-Cut Algorithm]] | Combining B&B with cuts |
| Column Generation Formulations | [[Set-Covering-Column-Generation]] | Set covering, constraint generation |

### Week 4: Branch-and-Cut (Detailed)
> *Putting it together with worked examples*

| Topic | File | Key Concepts |
|-------|------|--------------|
| [[Branch-and-Cut Detailed Example]] | [[Branch-and-Cut-Detailed]] | Full tableau operations, dual simplex |

### Week 5: Lagrangian Relaxation
> *Alternative relaxation: Penalize constraint violations in the objective*

| Topic | File | Key Concepts |
|-------|------|--------------|
| [[Lagrangian Relaxation]] | [[Lagrangian-Relaxation]] | Multipliers, subproblem, dual |
| [[Subgradient Optimization]] | [[Lagrangian-Relaxation#Method 1 Subgradient Optimization]] | Iterative multiplier updates |
| [[Constraint Generation (Lagrangian)]] | [[Lagrangian-Relaxation#Method 2 Constraint Generation / Cutting Plane]] | Master problem, cutting planes |
| [[Branch-and-Price]] | [[Lagrangian-Relaxation#Relationship to Dantzig-Wolfe Decomposition Branch-and-Price]] | Dantzig-Wolfe + branching |

---

## Core Concepts Map

### Formulation Strength Hierarchy
```
Weakest                                              Tightest
   │                                                    │
   ▼                                                    ▼
[BP_a] ←──intersect──→ [BP_d]  →  [BP_ad]  →  [BP_sc]  →  Convex Hull
Aggregated           Disaggregated  Combined   Set Covering   (Ideal)
```

See: [[Week 2#1.2.1 Strength of Formulations]]

### Algorithm Relationships
```
                    ┌─────────────────┐
                    │  LP Relaxation  │
                    └────────┬────────┘
                             │
         ┌───────────────────┼───────────────────┐
         │                   │                   │
         ▼                   ▼                   ▼
┌─────────────────┐  ┌───────────────┐  ┌─────────────────────┐
│ Branch-and-Bound│  │ Cutting Planes │  │ Lagrangian Relaxation│
│   [[Branch-and-Bound]]   │  │ [[Cutting-Planes-and-Branch-and-Cut]] │  │    [[Lagrangian-Relaxation]]     │
└────────┬────────┘  └───────┬───────┘  └──────────┬──────────┘
         │                   │                     │
         └─────────┬─────────┘                     │
                   ▼                               ▼
          ┌────────────────┐              ┌────────────────┐
          │ Branch-and-Cut │              │ Branch-and-Price│
          │[[Branch-and-Cut-Detailed]]│              │[[Lagrangian-Relaxation#Branch-and-Price]]│
          └────────────────┘              └────────────────┘
```

---

## Key Definitions

| Term | Definition | First Appears |
|------|------------|---------------|
| **LP Relaxation** | Drop integer constraints to get easier LP | [[Week 2]] |
| **Convex Hull** | Tightest LP relaxation where all extreme points are integer | [[Week 2]] |
| **Incumbent** | Best known feasible integer solution | [[Branch-and-Bound]] |
| **Fathom** | Prune a branch (no need to explore further) | [[Branch-and-Bound]] |
| **Valid Inequality** | Constraint satisfied by all integer feasible points | [[Cutting-Planes-and-Branch-and-Cut]] |
| **Cut** | Valid inequality that eliminates current LP solution | [[Cutting-Planes-and-Branch-and-Cut]] |
| **Cover Inequality** | Cut based on items that exceed capacity | [[Week 2]], [[Set-Covering-Column-Generation]] |
| **Lagrangian Multiplier** | Penalty for constraint violation | [[Lagrangian-Relaxation]] |
| **Subgradient** | Generalized gradient for non-smooth functions | [[Lagrangian-Relaxation]] |
| **Column Generation** | Generate variables (columns) as needed | [[Week 2]], [[Lagrangian-Relaxation]] |
| **Constraint Generation** | Generate constraints (rows) as needed | [[Week 2]], [[Set-Covering-Column-Generation]] |

---

## Formulation Comparison

| Formulation | Variables | Constraints | LP Bound | Notes |
|-------------|-----------|-------------|----------|-------|
| **Aggregated** $[BP_a]$ | $O(KL)$ | $O(K+L)$ | Weak | Fewer constraints |
| **Disaggregated** $[BP_d]$ | $O(KL)$ | $O(KL)$ | Weak | More constraints, different structure |
| **Combined** $[BP_{ad}]$ | $O(KL)$ | $O(KL)$ | Tighter | Intersection of above |
| **Set Covering** $[BP_{sc}]$ | $O(2^L)$ | $O(L)$ | Tightest | Needs column generation |
| **Cover Inequalities** $[BP_{ci}]$ | $O(KL)$ | $O(2^L)$ | Tight | Needs constraint generation |

See: [[Week 2]], [[Set-Covering-Column-Generation]]

---

## Algorithm Comparison

| Algorithm | Mechanism | Bound Source | Key Advantage |
|-----------|-----------|--------------|---------------|
| **[[Branch-and-Bound]]** | Tree search + LP at each node | LP relaxation | Conceptually simple |
| **[[Cutting Planes]]** | Add cuts to tighten LP | LP relaxation | No enumeration needed |
| **[[Branch-and-Cut]]** | B&B + cuts at nodes | LP + cuts | Industry standard |
| **[[Lagrangian Relaxation]]** | Penalize constraints | Lagrangian dual | Often tighter than LP |
| **[[Branch-and-Price]]** | B&B + column generation | LP of DW reformulation | For set covering structure |

---

## When to Use What

| Problem Structure | Recommended Approach |
|-------------------|---------------------|
| Small MIP (< 1000 vars) | Standard [[Branch-and-Cut]] (CPLEX/Gurobi) |
| Many similar substructures | [[Set Covering Formulation]] + column generation |
| Decomposable constraints | [[Lagrangian Relaxation]] |
| Set packing/covering | [[Branch-and-Price]] |
| Known strong cuts | Add to [[Branch-and-Cut]] |

---

## Running Example: Bin Packing

The **Bin Packing Problem** is used throughout the course:
- Items: sizes $D_1, ..., D_L$
- Bins: capacity $V$
- Goal: minimize bins used

| Week | Formulation/Algorithm | Notes |
|------|----------------------|-------|
| 2 | $[BP_d]$, $[BP_a]$, $[BP_{ad}]$ | Basic MIP formulations |
| 2 | $[BP_{sc}]$ | Set covering with patterns |
| 2 | $[BP_{ci}]$ | Cover inequality formulation |
| 3 | B&B, B&C | Solving the MIP |
| 5 | Lagrangian relaxation | Alternative bounds |

---

## Assignments

| Assignment | Topics Covered | Link |
|------------|----------------|------|
| HW1 | MIP Formulations, LP Relaxation | `Assignments/HW1_MSE435W2026.pdf` |
| HW2 | Branch-and-Bound, Cutting Planes | `Assignments/HW2_MSE435W2026.pdf` |

---

## Quick Reference: Bounding

For **minimization**:
$$\text{Relaxation bound (lower)} \leq z^* \leq \text{Heuristic bound (upper)}$$

For **maximization**:
$$\text{Heuristic bound (lower)} \leq z^* \leq \text{Relaxation bound (upper)}$$

**Relaxation options:**
1. [[LP Relaxation]] - drop integrality
2. [[Lagrangian Relaxation]] - penalize complicating constraints
3. Constraint relaxation - remove constraints entirely

---

## File Index

### Week 1
- [[Week1_Review]] - LP Review: Simplex Method, Revised Simplex, Duality, Sensitivity Analysis

### Week 2
- [[Week 2]] - Detailed formulations, strength comparison, set covering

### Week 3
- [[Branch-and-Bound]] - B&B algorithm
- [[Cutting-Planes-and-Branch-and-Cut]] - Cutting planes and B&C
- [[Set-Covering-Column-Generation]] - Column generation formulations

### Week 4
- [[Branch-and-Cut-Detailed]] - Detailed B&C example with tableaux

### Week 5
- [[Lagrangian-Relaxation]] - Lagrangian methods, subgradient, branch-and-price
