# The Branch-and-Bound Algorithm

**Course:** MSE 435 - Advanced Optimization Techniques
**Instructor:** S. Elhedhli, University of Waterloo

**Course Overview:** [[00-Course-Overview]]

---

## Related Topics
- **Formulations:** [[Week1_Review]], [[Week 2]] - How you formulate affects B&B performance
- **Enhancement:** [[Cutting-Planes-and-Branch-and-Cut]] - Add cuts to tighten LP bounds
- **Detailed example:** [[Branch-and-Cut-Detailed]]
- **Alternative bounding:** [[Lagrangian-Relaxation]] - Often tighter than LP relaxation

---

## Overview

The Branch-and-Bound algorithm is the fundamental method for solving Mixed Integer Programs (MIPs). It works by:
1. **Branching**: Partitioning the feasible region into smaller subproblems
2. **Bounding**: Calculating bounds on the best solution obtainable from each subproblem

---

## Bounding

For a **minimization** problem, we need:
- **Lower bound**: Achieved through **relaxation** (ignoring some constraints)
  - LP relaxation: ignores integer requirements
  - Constraint (Combinatorial) relaxation: ignores some constraints
  - Lagrangean relaxation
- **Upper bound**: Achieved through **feasible solutions** (using heuristics)

$$\text{Lower bound} \leq z^* \leq \text{Upper bound}$$

For **maximization** problems, the roles are reversed.

---

## Example Problem

$$\max \quad 2x_1 + 5x_2$$
$$\text{s.t.} \quad 5x_1 + 2x_2 \leq 81$$
$$\quad\quad 2x_1 + 8x_2 \leq 80$$
$$\quad\quad x_1, x_2 \geq 0, \text{ integer}$$

### LP Relaxation Solution
- $x_1 = 13.55$, $x_2 = 6.61$
- Objective value = 60.167

### Optimal Integer Solution
- $x_1 = 12$, $x_2 = 7$
- Objective value = 59

---

## The Algorithm in Action

### Step 1: Solve LP Relaxation (Node 0)
Solve the LP relaxation to get initial bounds.
- Solution: $x_1 = 13.55$, $x_2 = 6.61$, OV = 60.167
- This gives an **upper bound** of 60.167 (for maximization)

### Rounding Heuristic
- Rounding to nearest integer ($x_1=14$, $x_2=7$) is **infeasible**
- Rounding down to ($x_1=13$, $x_2=6$) is feasible with OV = 56
- This becomes the **incumbent** (best known feasible solution)

$$56 \leq \text{Optimal objective} \leq 60.167$$

### Step 2: Branching
Since $x_1 = 13.55$ is fractional, branch on $x_1$:
- **Left branch**: $x_1 \leq 13$
- **Right branch**: $x_1 \geq 14$

This eliminates the fractional LP solution.

---

## Branch-and-Bound Tree

```
                    Node 0
            max 2x₁ + 5x₂
            x₁=13.56, x₂=6.61
                UB=60.167
              Incumbent=56
                   |
        __________|__________
       |                     |
    x₁ ≤ 13              x₁ ≥ 14
       |                     |
    Node 1                Node 4
  x₁=13, x₂=6.75        x₁=14, x₂=5.5
    UB=59.75              UB=55.5
       |                     |
   ____|____              FATHOM
  |         |          (UB < Incumbent)
x₂ ≤ 6   x₂ ≥ 7
  |         |
Node 2    Node 3
x₁=13     x₁=12
x₂=6      x₂=7
OV=56     OV=59
INTEGER   INTEGER
FATHOM    FATHOM
          Update Incumbent=59
```

---

## Terminology

| Term | Definition |
|------|------------|
| **LP relaxation** | The LP obtained by dropping integer requirements |
| **Node** | A subproblem in the search tree |
| **Incumbent** | Best known feasible integer solution |
| **Fathom** | Pruning a node (no further branching needed) |
| **Branching** | Splitting a node into child subproblems |

---

## When to Fathom a Branch

A branch is **fathomed** (pruned) when one of these conditions occurs:

1. **All-integer solution**: Found a feasible integer solution (update incumbent if better)
2. **Infeasible LP**: The subproblem has no feasible solution
3. **Bound worse than incumbent**:
   - For max: OV ≤ incumbent
   - For min: OV ≥ incumbent

---

## The Classical Branch-and-Bound Algorithm

1. **Start at Node 0**: Solve LP relaxation, get solution $x^{LP}$ with objective $z^{LP}$
2. **If** $x^{LP}$ satisfies integer requirements → **STOP** (optimal found)
3. **If not**:
   - a. **Branch** on a fractional variable (e.g., if $x_1^{LP} = 2.3$, create $x_1 \leq 2$ and $x_1 \geq 3$)
   - b. Add new nodes to the list of unexplored nodes
   - c. (Optional) Generate initial incumbent by rounding

4. **While** unexplored nodes exist:
   - a. Choose an unexplored node
   - b. Solve its LP
   - c. If all-integer solution → update incumbent, fathom
   - d. If LP infeasible → fathom
   - e. If objective ≤ incumbent (max problem) → fathom
   - f. Else → branch, create two new nodes

5. **Termination**: When no unexplored nodes remain
   - If incumbent exists → incumbent is optimal
   - Else → problem is infeasible

---

## Main Components

### I. Bounding
$$\text{Lower bound} \leq z \leq \text{Upper bound}$$

### II. Branching
Partition the feasible set $S$ into subsets $S_1, S_2, \ldots, S_k$

$$z^k = \max\{c^T x : x \in S_k\}$$

Then: $z = \max_k z^k$

---

## Alternative Branching Rules

Standard branching: $x_1 \leq 13$ vs $x_1 \geq 14$

**Alternative**: Branch on any constraint that:
- Covers the entire feasible set of integer points
- Eliminates the current fractional LP solution

**Example**: Branch on $x_1 + 2x_2 \leq 26$ vs $x_1 + 2x_2 \geq 27$
- At Node 0: $x_1 + 2x_2 = 13.56 + 2(6.61) = 26.78$
- This cut eliminates the LP solution and may lead to faster convergence

---

## Key Insights

1. The LP relaxation provides bounds at each node
2. Tighter formulations → fewer nodes to explore (see [[Week 2#1.2.1 Strength of Formulations|Strength of Formulations]])
3. Good incumbent solutions enable more pruning
4. Branching strategy affects tree size significantly
5. Node selection strategy (best-first, depth-first) impacts performance
6. Can be enhanced with [[Cutting-Planes-and-Branch-and-Cut|cutting planes]] → Branch-and-Cut
7. [[Lagrangian-Relaxation]] can provide tighter bounds than LP relaxation
