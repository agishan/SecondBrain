# Cutting Planes and Branch-and-Cut

**Course:** MSE 435 - Advanced Optimization Techniques
**Instructor:** S. Elhedhli, University of Waterloo

**Course Overview:** [[00-Course-Overview]]

---

## Related Topics
- **Foundation:** [[Branch-and-Bound]] - The enumeration framework
- **Formulation tightness:** [[Week 2#1.2.1 Strength of Formulations|Strength of Formulations]]
- **Cover inequalities:** [[Week 2#1.5 A Formulation with a large number of constraints|Cover Inequalities in Bin Packing]]
- **Detailed example:** [[Branch-and-Cut-Detailed]]
- **Alternative: ** [[Lagrangian-Relaxation]] - Different relaxation approach

---

## The Concept of Cutting Planes

**Key Idea**: Instead of branching when the LP solution is fractional, add constraints (cuts) that:
- Cut off the current fractional solution
- Do not cut off any integer feasible points

This tightens the LP relaxation toward the **convex hull** of integer points.

---

## Main Concepts

| Term | Definition |
|------|------------|
| **Valid Inequality** | A constraint satisfied by all feasible integer points |
| **Cut (Cutting Plane)** | A valid inequality that "cuts off" a non-integer point (usually the LP optimum) |
| **Convex Hull** | The tightest possible LP relaxation where every extreme point is integer |

---

## Cutting Plane Method

**Algorithm**:
1. Solve LP relaxation
2. If solution is integer → **STOP** (optimal)
3. If solution is fractional:
   - Find a valid inequality (cut) violated by the current solution
   - Add the cut to the LP
   - Re-solve and repeat

The method iteratively approximates the convex hull until an integer solution is found.

---

## Example Problem

$$\max \quad 8x_1 + 5x_2$$
$$\text{s.t.} \quad x_1 + x_2 \leq 6$$
$$\quad\quad 9x_1 + 5x_2 \leq 45$$
$$\quad\quad x_1, x_2 \geq 0, \text{ integer}$$

### LP Relaxation Solution
| $z$ | $x_1$ | $x_2$ | $s_1$ | $s_2$ | RHS |
|-----|-------|-------|-------|-------|-----|
| 1 | 0 | 0 | 1.25 | 0.75 | 41.25 |
| 0 | 0 | 1 | 2.25 | -0.25 | 2.25 |
| 0 | 1 | 0 | -1.25 | 0.25 | 3.75 |

Solution: $x_1 = 3.75$, $x_2 = 2.25$, $z = 41.25$

Both variables are fractional → need to add cuts or branch.

---

## Gomory's Cutting Plane Algorithm

### Procedure
1. Pick a row with a fractional RHS (basic variable must be integer)
2. Rewrite the row isolating fractional parts
3. Generate the Gomory cut

### Derivation (from row 2 where $x_1 = 3.75$)

Row: $x_1 - 1.25s_1 + 0.25s_2 = 3.75$

Rewrite separating integer and fractional parts:
$$x_1 - 2s_1 + 0s_2 = 3 + 0.75s_1 + 0.25s_2 + 0.75$$

Rearranging:
$$x_1 - 2s_1 - 3 = 0.75s_1 + 0.25s_2 + 0.75$$

**Gomory Cut**: The fractional parts must sum to at least the fractional RHS:
$$0.75s_1 + 0.25s_2 \geq 0.75$$

Multiply by 4: $3s_1 + s_2 \geq 3$

### Convert to Original Variables
Using $s_1 = 6 - x_1 - x_2$ and $s_2 = 45 - 9x_1 - 5x_2$:

$$3(6 - x_1 - x_2) + (45 - 9x_1 - 5x_2) \geq 3$$
$$18 - 3x_1 - 3x_2 + 45 - 9x_1 - 5x_2 \geq 3$$
$$63 - 12x_1 - 8x_2 \geq 3$$
$$\boxed{3x_1 + 2x_2 \leq 15}$$

---

## After Adding the Cut

New problem:
$$\max \quad 8x_1 + 5x_2$$
$$\text{s.t.} \quad x_1 + x_2 \leq 6$$
$$\quad\quad 9x_1 + 5x_2 \leq 45$$
$$\quad\quad 3x_1 + 2x_2 \leq 15 \quad \leftarrow \text{cut}$$
$$\quad\quad x_1, x_2 \geq 0$$

Re-solve and continue adding cuts until integer solution is found.

### After Re-optimization
| $z$ | $x_1$ | $x_2$ | $s_1$ | $s_2$ | $s_3$ | RHS |
|-----|-------|-------|-------|-------|-------|-----|
| 1 | 0 | 0 | 0.33 | 1.67 | | 40 |
| 0 | 0 | 1 | 0 | -1 | 3 | 0 |
| 0 | 1 | 0 | 0.67 | -1.67 | | 5 |
| 0 | 0 | 0 | 1 | 0.33 | -1.33 | 1 |

Solution: $x_1 = 5$, $x_2 = 0$, $z = 40$

This is now integer! (Though may need more cuts in general)

---

## Valid Inequalities vs. Valid Cuts

| | Valid Inequality | Valid Cut | Neither |
|---|-----------------|-----------|---------|
| Satisfied by all integer points? | Yes | Yes | No |
| Cuts off LP solution? | Not necessarily | Yes | - |

A cut must be both valid AND eliminate the current fractional solution.

---

## The Branch-and-Cut Algorithm

**Key Idea**: Combine cutting planes with branch-and-bound.

At each node:
1. Solve LP relaxation
2. **Try to add cuts** to tighten the formulation
3. If still fractional and no more useful cuts → **branch**

### Characteristics

1. At every node, we either add cuts or decide to branch
2. If cuts are only added at the **root node** → this is branch-and-bound with preprocessing
3. At child nodes, there are two types of cuts:
   - **Local cuts**: Valid only for that node and its descendants
   - **Global cuts**: Valid for all nodes in the tree
4. The **dual Simplex method** efficiently re-optimizes after adding cuts or branching constraints

---

## Branch-and-Cut Example

$$\min \quad -5x_1 - 6x_2$$
$$\text{s.t.} \quad x_1 + 2x_2 \leq 7$$
$$\quad\quad 2x_1 - x_2 \leq 3$$
$$\quad\quad x_1, x_2 \geq 0, \text{ integer}$$

### LP Solution (Node 0)
| $z$ | $x_1$ | $x_2$ | $s_1$ | $s_2$ | RHS |
|-----|-------|-------|-------|-------|-----|
| 1 | 0 | 0 | 3.4 | 0.8 | 26.2 |
| 0 | 0 | 1 | 0.4 | -0.2 | 2.2 |
| 0 | 1 | 0 | 0.2 | 0.4 | 2.6 |

Solution: $x_1 = 2.6$, $x_2 = 2.2$, $z = -26.2$

### Generate Gomory Cut (from row 1)
$$x_2 + \frac{2}{5}s_1 - \frac{1}{5}s_2 = 2.2$$
$$x_2 - s_2 - 2 = 0.2 - \frac{2}{5}s_1 - \frac{4}{5}s_2$$

Gomory cut: $\frac{2}{5}s_1 + \frac{4}{5}s_2 \geq 0.2$

Simplifies to: $2s_1 + 4s_2 \geq 1$

This implies: $x_1 \leq 2.5$ → $\boxed{x_1 \leq 2}$

### After Adding Cut
New solution: $x_1 = 2$, $x_2 = 2.5$, $z = -25$

Still fractional in $x_2$ → either add another cut or branch.

### Branch on $x_2$
- **Node 1**: $x_2 \leq 2$ → Solution: $x_1 = 2$, $x_2 = 2$, $z = -22$ (INTEGER)
- **Node 2**: $x_2 \geq 3$ → Solution: $x_1 = 1$, $x_2 = 3$, $z = -23$ (INTEGER)

**Optimal**: $x_1 = 1$, $x_2 = 3$, $z = -23$

---

## Branch-and-Cut Tree

```
                    Node 0
            min -5x₁ - 6x₂
            x₁=2, x₂=2.5, z=-25
            x₁ ≤ 2 ← Valid cut
                   |
        __________|__________
       |                     |
    x₂ ≤ 2               x₂ ≥ 3
       |                     |
    Node 1                Node 2
    x₁=2, x₂=2            x₁=1, x₂=3
    z=-22                 z=-23
    INTEGER               INTEGER
    Incumbent=-22         Incumbent=-23 ✓
```

---

## Summary

**Branch-and-Cut** combines:
- The **cutting plane method** (tightens LP relaxation)
- The **branch-and-bound method** (systematic enumeration)

This is the **leading algorithm** for solving Mixed Integer Programs in modern solvers (CPLEX, Gurobi, etc.). See [[Branch-and-Cut-Detailed]] for a complete worked example with tableau operations.

### Key Advantages
1. Cuts reduce the gap between LP bound and integer optimum
2. Fewer nodes need to be explored compared to pure B&B
3. Problem-specific cuts (cover inequalities, flow cuts, etc.) can be very effective
