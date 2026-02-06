# Review: Linear Programs and the Revised Simplex Method

**Author:** S. Elhedhli

**Last updated:** Jan. 2025

**Note:** Book Draft. Do not distribute without permission from the author.

**Course Overview:** [[00-Course-Overview]]

---

## Contents

| Section | Title                                        |
| ------- | -------------------------------------------- |
| 0.1     | The Simplex Method                           |
| 0.2     | The Simplex Method: Steps                    |
| 0.3     | The Simplex Method: Example                  |
| 0.4     | The Revised Simplex Method                   |
| 0.5     | Initializing the Simplex Method              |
| 0.6     | Example: Unbounded Problem                   |
| 0.7     | Example (Unbounded continued)                |
| 0.8     | Duality in LP: Primal-Dual Relationship      |
| 0.9     | Duality in LP: Primal-Dual Theory            |
| 0.10    | Sensitivity Analysis in LP                   |

---

## 0.1 The Simplex Method

Solves an LP in **standard form**:

$$\min \; c_1 x_1 + c_2 x_2 + \ldots + c_n x_n$$

Subject to:

$$a_{11}x_1 + a_{12}x_2 + \ldots + a_{1n}x_n = b_1$$
$$a_{21}x_1 + a_{22}x_2 + \ldots + a_{2n}x_n = b_2$$
$$\vdots$$
$$a_{m1}x_1 + a_{m2}x_2 + \ldots + a_{mn}x_n = b_m$$
$$x_1, x_2, \ldots, x_n \geq 0$$

**Converting to standard form:**

- $\leq$ constraints: add a non-negative slack variable
- $\geq$ constraints: subtract a non-negative surplus variable
- Free variables: replace $x$ free by $x = x' - x''$ where $x', x'' \geq 0$

**Example:**

$$\min \; 2x_1 - 3x_2$$

$$\text{s.t.} \quad x_1 - 3x_2 + 2x_3 \leq 3$$
$$-x_1 + 2x_2 \geq 2$$
$$x_1 \text{ free}, \; x_2 \geq 0, \; x_3 \geq 0$$

Converted to standard form:

$$\min \; 2x_1' - 2x_1'' - 3x_2$$

$$\text{s.t.} \quad x_1' - x_1'' - 3x_2 + 2x_3 + s_1 = 3$$
$$-x_1' + x_1'' + 2x_2 - s_2 = 2$$
$$x_1', x_1'', x_2, x_3, s_1, s_2 \geq 0$$

---

## 0.2 The Simplex Method: Steps

1. Find an initial basic feasible solution (Two-phase or the Big M methods)
2. Repeat until optimal solution is found:
   1. Test for optimality (using the reduced costs)
   2. If not optimal, change the basis
   3. Choose an entering variable
   4. Choose a leaving variable using the Minimum Ratio Test
   5. Update the solution

**Example problem:**

$$\max \; 5x_1 + 6x_2 + 2x_3$$

$$\text{s.t.} \quad 4x_1 + x_2 \leq 3$$
$$x_1 - x_2 + x_3 \leq 2$$
$$x_1, x_2, x_3 \geq 0$$

---

## 0.3 The Simplex Method: Example

Put in standard form and set up the initial tableau:

**Initial Tableau:**

|       | $z$ | $x_1$ | $x_2$ | $x_3$ | $s_2$ | $s_3$ | rhs |
| ----- | --- | ------ | ------ | ------ | ------ | ------ | --- |
| Row 0 | 1   | $-5$   | $-6$   | $-2$   | 0      | 0      | 0   |
| Row 1 | 0   | 4      | 1      | 0      | 1      | 0      | 3   |
| Row 2 | 0   | 1      | $-1$   | 1      | 0      | 1      | 2   |

- **Optimality check:** Are all reduced costs non-negative? **No**
- **Entering variable:** Most negative reduced cost $\Rightarrow x_2$ enters
- **Leaving variable:** Ratio test over positive coefficients only: coefficient of $s_2$ is 1, coefficient of $s_3$ is $-1$ $\Rightarrow$ $s_2$ leaves
- Perform Gauss-Jordan elimination on the $x_2$ column

**Second Tableau:**

|   | $z$ | $x_1$ | $x_2$ | $x_3$ | $s_2$ | $s_3$ | rhs |
| - | --- | ------ | ------ | ------ | ------ | ------ | --- |
|   | 1   | 19     | 0      | $-2$   | 6      | 0      | 18  |
|   | 0   | 4      | 1      | 0      | 1      | 0      | 3   |
|   | 0   | 5      | 0      | 1      | 1      | 1      | 5   |

- **Current BFS:** $x_2 = 3$, $s_3 = 5$, $z = 18$
- **Optimality check:** Reduced cost of $x_3$ is negative
- **Entering variable:** $x_3$
- **Leaving variable:** Ratio test: coefficient of $x_2$ is 0, coefficient of $s_3$ is 1 $\Rightarrow$ $s_3$ leaves
- Perform Gauss-Jordan elimination on the $x_3$ column

**Final Tableau:**

|   | $z$ | $x_1$ | $x_2$ | $x_3$ | $s_2$ | $s_3$ | rhs |
| - | --- | ------ | ------ | ------ | ------ | ------ | --- |
|   | 1   | 29     | 0      | 0      | 8      | 2      | 28  |
|   | 0   | 4      | 1      | 0      | 1      | 0      | 3   |
|   | 0   | 5      | 0      | 1      | 1      | 1      | 5   |

- **Current BFS:** $x_2 = 3$, $x_3 = 5$, $z = 28$
- **Optimality check:** All reduced costs are non-negative $\Rightarrow$ **optimal**

**Optimal solution:** $x_1^* = 0$, $x_2^* = 3$, $x_3^* = 5$, $s_2^* = 0$, $s_3^* = 0$ with objective value **28**.

---

## 0.4 The Revised Simplex Method

$$\min \; z = c_B x_B + c_N x_N$$

$$\text{s.t.} \quad Bx_B + Nx_N = b$$
$$x_N, x_B \geq 0$$

Expressing in terms of the basis:

$$x_B = B^{-1}b - B^{-1}Nx_N$$

$$z = c_B B^{-1}b + [c_N - c_B B^{-1}N] x_N$$

**Basic Feasible Solution:** $x_B = B^{-1}b$, $x_N = 0$, $z = c_B B^{-1}b$

**Algorithm:**

- If reduced costs $c_N - c_B B^{-1}N \geq 0$, then **stop** — the current basis is optimal
- Else choose an entering variable with a negative reduced cost, say $x_{\bar{j}}$
- Choose the leaving variable through a ratio test:
  - Setting $x_{\bar{j}} = t$, we get $x_B = B^{-1}b - t B^{-1} a_{\bar{j}}$
  - To keep $x_B \geq 0$:

$$t = \min \left\{ \frac{(B^{-1}b)_i}{(B^{-1}a_{\bar{j}})_i} \;,\; (B^{-1}a_{\bar{j}})_i > 0 \right\}$$

- The leaving variable $x_s$ is the one corresponding to the minimum ratio:

$$s = \arg\min \left\{ \frac{(B^{-1}b)_i}{(B^{-1}a_j)_i} \;,\; (B^{-1}a_j)_i > 0 \right\}$$

---

## 0.5 Initializing the Simplex Method

Given:

$$\min \; z = c^T x$$
$$\text{s.t.} \quad Ax = b$$
$$x \geq 0$$

We need an initial basic feasible solution. Add artificial variables $x^a$:

$$\text{s.t.} \quad Ax + x^a = b$$
$$x, x^a \geq 0$$

### The Two-Phase Method

**Phase I:**

$$\min \sum_i x_i^a$$
$$\text{s.t.} \quad Ax + x^a = b$$
$$x, x^a \geq 0$$

- If at optimality $x^{a*} \neq 0$, the problem is **infeasible**
- Else $Ax^* = b$, and $x^*$ is used to start Phase II (the original simplex)

### The Big M Method

$$\min \; c^T x + M \sum_i x_i^a$$
$$\text{s.t.} \quad Ax + x^a = b$$
$$x, x^a \geq 0$$

- $M$ is a very large positive number
- If at optimality $x^{a*} \neq 0$, the problem is **infeasible**
- Else $x^{a*} = 0$, and $x^*$ is **optimal**

---

## 0.6 Example: Unbounded Problem

$$\max \; x_1 + x_2$$

$$\text{s.t.} \quad -2x_1 + x_2 \leq 2$$
$$x_1 - 2x_2 \leq 2$$
$$x_1, x_2 \geq 0$$

Standard form:

$$\min \; -x_1 - x_2$$

$$\text{s.t.} \quad -2x_1 + x_2 + x_3 = 2$$
$$x_1 - 2x_2 + x_4 = 2$$
$$x_1, x_2, x_3, x_4 \geq 0$$

**Iteration 1:**

- Initial basis: $\{x_3, x_4\}$; Nonbasic: $\{x_1, x_2\}$

$$B = \begin{bmatrix} 1 & 0 \\ 0 & 1 \end{bmatrix}, \quad N = \begin{bmatrix} -2 & 1 \\ 1 & -2 \end{bmatrix}, \quad B^{-1} = B$$

- **Optimality check — reduced costs:**

$$c_N - c_B B^{-1}N = [-1, -1] - [0, 0]\begin{bmatrix} 1 & 0 \\ 0 & 1 \end{bmatrix}\begin{bmatrix} -2 & 1 \\ 1 & -2 \end{bmatrix} = [-1, -1]$$

- **Entering variable:** $x_1$
- **Leaving variable — ratio test:**

$$B^{-1}a_1 = \begin{bmatrix} -2 \\ 1 \end{bmatrix}, \quad B^{-1}b = \begin{bmatrix} 2 \\ 2 \end{bmatrix}$$

$$\min\left\{\frac{2}{1}\right\} = 2 \Rightarrow x_4 \text{ leaves}$$

(Only positive entries considered; $-2$ is skipped)

---

## 0.7 Example (Unbounded continued)

**Iteration 2:**

- New basis: $\{x_1, x_3\}$; Nonbasic: $\{x_2, x_4\}$

$$B = \begin{bmatrix} -2 & 1 \\ 1 & 0 \end{bmatrix}, \quad N = \begin{bmatrix} 1 & 0 \\ -2 & 1 \end{bmatrix}, \quad B^{-1} = \begin{bmatrix} 0 & 1 \\ 1 & 2 \end{bmatrix}$$

$$c_B = [-1, 0], \quad c_N = [-1, 0]$$

- **Optimality check — reduced costs:**

$$c_N - c_B B^{-1}N = [-1, 0] - [-1, 0]\begin{bmatrix} 0 & 1 \\ 1 & 2 \end{bmatrix}\begin{bmatrix} -2 & 1 \\ 1 & 0 \end{bmatrix} = [-1, 1]$$

- **Entering variable:** $x_2$
- **Leaving variable — ratio test:**

$$B^{-1}a_2 = \begin{bmatrix} 0 & 1 \\ 1 & 2 \end{bmatrix}\begin{bmatrix} 1 \\ -2 \end{bmatrix} = \begin{bmatrix} -2 \\ -3 \end{bmatrix}$$

All entries are $< 0$. The ratio test fails $\Rightarrow$ **problem is unbounded**.

---

## 0.8 Duality in LP: Primal-Dual Relationship

| Primal          | Minimization LP | Maximization LP | Dual            |
| --------------- | --------------- | --------------- | --------------- |
|                 | $\geq$          | $\geq 0$        |                 |
| **Constraints** | $\leq$          | $\leq 0$        | **Variables**   |
|                 | $=$             | free            |                 |
|                 | $\geq 0$        | $\leq$          |                 |
| **Variables**   | $\leq 0$        | $\geq$          | **Constraints** |
|                 | free            | $=$             |                 |

**Example:**

$$\text{[Primal]:} \quad \min \; c^T x \qquad \text{[Dual]:} \quad \max \; b^T y$$
$$\text{s.t.} \quad Ax = b \qquad\qquad \text{s.t.} \quad A^T y \leq c$$
$$x \geq 0 \qquad\qquad\qquad y \text{ free}$$

---

## 0.9 Duality in LP: Primal-Dual Theory

Consider the primal-dual pair $[P]$ and $[D]$:

$$[P]: \quad \min \; c^T x \qquad [D]: \quad \max \; b^T y$$
$$\text{s.t.} \quad Ax = b \qquad\qquad \text{s.t.} \quad A^T y \leq c$$
$$x \geq 0 \qquad\qquad\qquad y \text{ free}$$

For any $\bar{x}$ feasible to $[P]$ and $\bar{y}$ feasible to $[D]$:

$$b^T \bar{y} \leq c^T \bar{x}$$

**Optimality conditions** — $\bar{x}$ and $\bar{y}$ are optimal if either:

1. **Strong duality:** $c^T \bar{x} = b^T \bar{y}$
2. **Complementary slackness:** $\bar{x}_i(a_i^T \bar{y} - c_i) = 0$ for all $i$
   - (Also $\bar{y}_j(a_j^T \bar{x} - b_j) = 0$, but redundant in this case)

---

## 0.10 Sensitivity Analysis in LP

- **Change in $c$:** Check if the current basis remains optimal by checking optimality (the reduced costs)
  - Solve for $c_N - c_B B^{-1}N \geq 0$

- **Change in $b$:** Check if the current basis remains optimal by checking feasibility (rhs)
  - Solve for $B^{-1}b \geq 0$

**References:**

- Winston W.L. and M. Venkataramanan. *Introduction to Mathematical Programming*, Duxbury Press, 2003.
- Hillier F.S. and G.J. Lieberman. *Introduction to Operations Research*, McGraw Hill, 2005.
