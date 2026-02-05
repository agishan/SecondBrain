# Branch-and-Cut: Detailed Example

**Course:** MSCI 435 - Advanced Optimization Techniques
**Instructor:** S. Elhedhli, University of Waterloo

**Course Overview:** [[00-Course-Overview]]

---

## Related Topics
- **Background:** [[Branch-and-Bound]] - The enumeration framework
- **Cutting planes:** [[Cutting-Planes-and-Branch-and-Cut]] - Theory and concepts
- **Formulations:** [[Week 2]] - Why tighter formulations help
- **Alternative:** [[Lagrangian-Relaxation]] - Different bounding approach

---

## Readings

- Mitchell J.E. *Integer programming: Branch-and-cut algorithms*. Encyclopedia of Optimization, Volume II, Kluwer Academic Press, August 2001.
- Winston W.L. and M. Venkataramanan. *Introduction to mathematical programming*, Duxbury Press, 2003. Pages 545-549 (cutting plane methods); pages 329-334 (dual simplex).

---

## Branch-and-Cut: Combining Branch-and-Bound and Cutting Planes

### Problem Statement

$$\min \quad -5x_1 - 6x_2$$
$$\text{s.t.} \quad x_1 + 2x_2 \leq 7$$
$$\quad\quad 2x_1 - x_2 \leq 3$$
$$\quad\quad x_1, x_2 \geq 0, \text{ integer}$$

---

## Step 1: Solve LP Relaxation at Node 0

Convert to standard form:
$$\min \quad -5x_1 - 6x_2$$
$$\text{s.t.} \quad x_1 + 2x_2 + s_1 = 7$$
$$\quad\quad 2x_1 - x_2 + s_2 = 3$$
$$\quad\quad x_1, x_2 \geq 0$$

### Initial Tableau

| $z$ | $x_1$ | $x_2$ | $s_1$ | $s_2$ | RHS |
|-----|-------|-------|-------|-------|-----|
| 1 | -5 | -6 | 0 | 0 | 0 |
| 0 | 1 | 2 | 1 | 0 | 7 |
| 0 | 2 | -1 | 0 | 1 | 3 |

### After Simplex Iterations

**Second tableau:**
| $z$ | $x_1$ | $x_2$ | $s_1$ | $s_2$ | RHS |
|-----|-------|-------|-------|-------|-----|
| 1 | -2 | 0 | 3 | 0 | 21 |
| 0 | 0.5 | 1 | 0.5 | 0 | 3.5 |
| 0 | 2.5 | 0 | 0.5 | 1 | 6.5 |

**Optimal tableau:**
| $z$ | $x_1$ | $x_2$ | $s_1$ | $s_2$ | RHS |
|-----|-------|-------|-------|-------|-----|
| 1 | 0 | 0 | 3.4 | 0.8 | 26.2 |
| 0 | 0 | 1 | 0.4 | -0.2 | 2.2 |
| 0 | 1 | 0 | 0.2 | 0.4 | 2.6 |

**LP Solution:** $x_1^* = 2.6$, $x_2^* = 2.2$, $z^* = -26.2$

Both variables are fractional → we can either branch or add Gomory cuts.

---

## Step 2: Generate Gomory Cut

Let's add a Gomory cut based on **row 1** (the $x_2$ row):

$$x_2 + \frac{2}{5}s_1 - \frac{1}{5}s_2 = 2.2$$

Rearranging to isolate fractional parts:
$$x_2 - s_2 - 2 = 0.2 - \frac{2}{5}s_1 - \frac{4}{5}s_2$$

**Gomory cut:** $\frac{2}{5}s_1 + \frac{4}{5}s_2 \geq 0.2$

Multiplying by 5: $2s_1 + 4s_2 \geq 1$

Converting back to $x_1, x_2$:
$$2(7 - x_1 - 2x_2) + 4(3 - 2x_1 + x_2) \geq 1$$
$$14 - 2x_1 - 4x_2 + 12 - 8x_1 + 4x_2 \geq 1$$
$$26 - 10x_1 \geq 1$$
$$x_1 \leq 2.5$$

This can be **strengthened** to: $\boxed{x_1 \leq 2}$

---

## Step 3: Add Cut and Re-optimize

Transform the cut to $x_1 + s_3 = 2$ and add to the tableau:

| $z$ | $x_1$ | $x_2$ | $s_1$ | $s_2$ | $s_3$ | RHS |
|-----|-------|-------|-------|-------|-------|-----|
| 1 | 0 | 0 | 3.4 | 0.8 | 0 | 26.2 |
| 0 | 0 | 1 | 0.4 | -0.2 | 0 | 2.2 |
| 0 | 1 | 0 | 0.2 | 0.4 | 0 | 2.6 |
| 0 | 1 | 0 | 0 | 0 | 1 | 2 |

After row operations to maintain basic solution:

| $z$ | $x_1$ | $x_2$ | $s_1$ | $s_2$ | $s_3$ | RHS |
|-----|-------|-------|-------|-------|-------|-----|
| 1 | 0 | 0 | 3.4 | 0.8 | 0 | 26.2 |
| 0 | 0 | 1 | 0.4 | -0.2 | 0 | 2.2 |
| 0 | 1 | 0 | 0.2 | 0.4 | 0 | 2.6 |
| 0 | 0 | 0 | -0.2 | -0.4 | 1 | -0.6 |

This is **dual feasible** (reduced costs ≥ 0) but **primal infeasible** (negative RHS).

### Apply Dual Simplex Method

After one dual simplex iteration:

| $z$ | $x_1$ | $x_2$ | $s_1$ | $s_2$ | $s_3$ | RHS |
|-----|-------|-------|-------|-------|-------|-----|
| 1 | 0 | 0 | 3 | 0 | 2 | 25 |
| 0 | 1 | 0 | 0 | 0 | 1 | 2 |
| 0 | 0 | 1 | 0.5 | 0 | -0.5 | 2.5 |
| 0 | 0 | 0 | 0.5 | 1 | -2.5 | 1.5 |

**New solution:** $x_1^* = 2$, $x_2^* = 2.5$, $z^* = -25$

$x_2$ is still non-integer → we should proceed (add another cut or branch).

---

## Step 4: Branch on $x_2$

Since $x_2 = 2.5$, create two nodes:
- **Node 1:** $x_2 \leq 2$
- **Node 2:** $x_2 \geq 3$

### Node 1: Add constraint $x_2 + s_4 = 2$

After dual simplex:

| $z$ | $x_1$ | $x_2$ | $s_1$ | $s_2$ | $s_3$ | $s_4$ | RHS |
|-----|-------|-------|-------|-------|-------|-------|-----|
| 1 | 0 | 0 | 0 | 0 | 5 | 6 | 22 |
| 0 | 1 | 0 | 0 | 0 | 1 | 0 | 2 |
| 0 | 0 | 1 | 0 | 0 | 0 | 1 | 2 |
| 0 | 0 | 0 | 1 | 0 | -1 | -2 | 1 |
| 0 | 0 | 0 | 0 | 1 | -2 | 1 | 1 |

**Solution:** $x_1^* = 2$, $x_2^* = 2$, $z^* = -22$ ✓ **INTEGER**

### Node 2: Add constraint $x_2 - s_5 = 3$ (i.e., $-x_2 + s_5 = -3$)

After dual simplex:

| $z$ | $x_1$ | $x_2$ | $s_1$ | $s_2$ | $s_3$ | $s_5$ | RHS |
|-----|-------|-------|-------|-------|-------|-------|-----|
| 1 | 0 | 0 | 5 | 0 | 0 | 4 | 23 |
| 0 | 1 | 0 | 1 | 0 | 0 | 2 | 1 |
| 0 | 0 | 1 | 0 | 0 | 0 | -1 | 3 |
| 0 | 0 | 0 | -2 | 1 | 0 | -5 | 4 |
| 0 | 0 | 0 | -1 | 0 | 1 | -2 | 1 |

**Solution:** $x_1^* = 1$, $x_2^* = 3$, $z^* = -23$ ✓ **INTEGER**

---

## Solution

Both nodes give integer solutions:
- **Node 1:** $x_1 = 2$, $x_2 = 2$, $z = -22$
- **Node 2:** $x_1 = 1$, $x_2 = 3$, $z = -23$

Since we're minimizing, the **optimal solution** is from Node 2:

$$\boxed{x_1^* = 1, \quad x_2^* = 3, \quad z^* = -23}$$

---

## Branch-and-Cut Tree

```
                         Node 0
                    LP: x₁=2.6, x₂=2.2
                        z = -26.2
                           |
                    Add cut: x₁ ≤ 2
                           |
                    LP: x₁=2, x₂=2.5
                        z = -25
                           |
            _______________|_______________
           |                               |
      x₂ ≤ 2                          x₂ ≥ 3
           |                               |
        Node 1                          Node 2
    x₁=2, x₂=2                      x₁=1, x₂=3
      z = -22                         z = -23
      INTEGER                         INTEGER
       FATHOM                    OPTIMAL SOLUTION
```

---

## Key Notes on Branch-and-Cut

See also [[Cutting-Planes-and-Branch-and-Cut]] for more theory on cutting planes.

1. **At every node**, we either add cuts or decide to branch

2. **If cuts are only added at the root node**, then it's branch-and-bound with a preprocessing step where cuts enhance the formulation

3. **Two types of cuts at child nodes:**
   - **Local cuts**: Valid only for that node and its children
   - **Global cuts**: Valid for all nodes in the tree, including the root

4. **The dual Simplex method** is very efficient for finding the optimal solution after a cut or branching constraint is added (maintains dual feasibility, restores primal feasibility)

---

## Why Dual Simplex?

When we add a constraint (cut or branching):
- The current solution remains **dual feasible** (reduced costs unchanged)
- But becomes **primal infeasible** (may violate new constraint)

Dual simplex efficiently restores primal feasibility while maintaining optimality conditions.
