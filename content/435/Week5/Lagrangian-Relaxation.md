# Lagrangian Relaxation

**Course:** MSE 435 - Advanced Optimization Techniques
**Instructor:** S. Elhedhli, University of Waterloo

**Reference:** M. Fisher, "An Applications Oriented Guide to Lagrangian Relaxation", *Interfaces* vol 15 No 2 pp 10-21 (1985).

**Course Overview:** [[00-Course-Overview]]

---

## Related Topics
- **Alternative approach:** [[Branch-and-Bound]] - Uses LP relaxation for bounds
- **Cutting planes:** [[Cutting-Planes-and-Branch-and-Cut]] - Another way to tighten bounds
- **Formulations:** [[Week 2]] - Bin packing formulations
- **Set covering:** [[Set-Covering-Column-Generation]] - Related column generation

---

## Relaxation and Bounding

For many difficult large-scale optimization problems (including MIPs), the only practical approach is to solve a **relaxed version** of the problem.

### Types of Relaxation

| Relaxation Type | What it ignores | Bound provided |
|-----------------|-----------------|----------------|
| **LP relaxation** | Integer requirements | Upper (max) / Lower (min) | See [[Branch-and-Bound]] |
| **Constraint relaxation** | Removes some constraints entirely | Upper (max) / Lower (min) |
| **Lagrangean relaxation** | Removes constraints and penalizes violation in objective | Upper (max) / Lower (min) |

### Bounding Relationship

$$\boxed{\text{Lower bound (relaxation)} \leq \text{Optimal objective} \leq \text{Upper bound (heuristics)}}$$

For minimization problems. Roles reversed for maximization.

### Example: Bin Packing

Given items with sizes $D_l$ and bin capacity $V$:
- **LP lower bound:** $\sum D_l / V$
- **Better bound:** $\lceil \sum D_l / V \rceil$
- **Lagrangean bound:** Relax assignment constraints, penalize violations

---

## Lagrangean Relaxation: The Concept

### Example Problem

$$\max \quad 16x_1 + 10x_2 + 4x_4$$
$$\text{s.t.} \quad 8x_1 + 2x_2 + x_3 + 4x_4 \leq 10$$
$$\quad\quad x_1 + x_2 \leq 1 \quad \rightarrow u_1 \geq 0$$
$$\quad\quad x_3 + x_4 \leq 1 \quad \rightarrow u_2 \geq 0$$
$$\quad\quad x_i \in \{0, 1\}, \quad i = 1, 2, 3, 4$$

### Problem Structure

The problem has:
- A "complicating" constraint: $8x_1 + 2x_2 + x_3 + 4x_4 \leq 10$ (knapsack)
- "Easy" constraints: $x_1 + x_2 \leq 1$ and $x_3 + x_4 \leq 1$ (simple bounds)

**Idea:** Relax the easy constraints and move them to the objective with penalties.

---

## Forming the Lagrangean Relaxation

### Step 1: Add Penalty Terms

For constraints $x_1 + x_2 \leq 1$ and $x_3 + x_4 \leq 1$, add penalty terms:

$$u_1(1 - x_1 - x_2) + u_2(1 - x_3 - x_4)$$

where $u_1, u_2 \geq 0$ are **Lagrangean multipliers**.

### Step 2: Form the Subproblem

$$z_{SP} = \max \quad 16x_1 + 10x_2 + 4x_4 + u_1(1 - x_1 - x_2) + u_2(1 - x_3 - x_4)$$
$$\text{s.t.} \quad 8x_1 + 2x_2 + x_3 + 4x_4 \leq 10$$
$$\quad\quad x_i \in \{0, 1\}$$

Rearranging:

$$z_{SP} = \max \quad (16 - u_1)x_1 + (10 - u_1)x_2 + (-u_2)x_3 + (4 - u_2)x_4 + u_1 + u_2$$
$$\text{s.t.} \quad 8x_1 + 2x_2 + x_3 + 4x_4 \leq 10$$
$$\quad\quad x_i \in \{0, 1\}$$

This is a **binary knapsack problem** - much easier to solve!

---

## Lagrangean Bound

For any $u_1 \geq 0, u_2 \geq 0$:

$$\text{Lagrangean upper bound} = u_1 + u_2 + z_{SP}$$

The **best Lagrangean bound** is:

$$\min_{u_1 \geq 0, u_2 \geq 0} \{u_1 + u_2 + z_{SP}\}$$

This is called the **Lagrangean Dual Problem**.

---

## Example: Different Multiplier Values

| $u_1$ | $u_2$ | SP Objective | $x_1$ | $x_2$ | $x_3$ | $x_4$ | $z_{SP}$ | UB |
|-------|-------|--------------|-------|-------|-------|-------|----------|-----|
| 0 | 0 | $16x_1 + 10x_2 + 4x_4$ | 1 | 1 | 0 | 0 | 26 | **26** |
| 20 | 20 | $-4x_1 - 10x_2 - 20x_3 - 16x_4$ | 0 | 0 | 0 | 0 | 0 | **40** |
| 13 | 0 | $3x_1 - 3x_2 + 4x_4$ | 0 | 0 | 0 | 1 | 4 | **17** |

**Observations:**
- $u_1 = 13, u_2 = 0$ gives the best upper bound (17) among these
- The subproblem solution $(0, 0, 0, 1)$ is **feasible** to the original problem
- Its original objective value is $4$, providing a **lower bound**

$$\boxed{4 \leq \text{Optimal} \leq 17}$$

**Gap:** $\frac{|4 - 17|}{17} = 76.47\%$ (still far from optimal)

---

## Finding the Best Lagrangean Multipliers

### The Lagrangean Dual Problem

$$\min_{u_1 \geq 0, u_2 \geq 0} \{u_1 + u_2 + z_{SP}(u_1, u_2)\}$$

**Properties:**
- $LR(u_1, u_2) = u_1 + u_2 + z_{SP}$ is **convex** (piecewise linear)
- But **non-differentiable** at points where $z_{SP}$ has multiple optimal solutions

### Two Solution Methods

1. **Subgradient Optimization** - iterative gradient-like method
2. **Constraint Generation / Cutting Plane Method** - LP-based approach

---

## Method 1: Subgradient Optimization

### Algorithm

```
Start with initial solution u⁰
At iteration k:
    1. Solve SP to get solution xᵏ
    2. Compute subgradient: gᵏ = [1 - x₁ᵏ - x₂ᵏ]
                                  [1 - x₃ᵏ - x₄ᵏ]
    3. Choose step size tₖ
    4. Update: uᵏ⁺¹ = max{0, uᵏ - tₖgᵏ}
```

### Step Size Selection

A common choice:
$$t_k = \frac{\lambda_k (UB^k - \bar{z})}{\|b - Ax\|}$$

where:
- $UB^k$ = Lagrangean bound at iteration $k$
- $\bar{z}$ = best known feasible solution value
- $\lambda_k$ = scalar between 0 and 2

### Convergence Conditions (Held, Wolfe, Crowder 1974)

The method converges as long as:
$$t_k \to 0 \quad \text{and} \quad \sum_{i=1}^{k} t_i \to \infty$$

### MATLAB Implementation

```matlab
mu1=0; mu2=0;
Aineq=[8 2 1 4]; bineq=10;
iter=0;

options2 = optimoptions('intlinprog','display','off');

while iter<50
    iter=iter+1;
    f=[16-mu1; 10-mu1; -mu2; 4-mu2];

    [x,z]=intlinprog(-f,[1:4],Aineq,bineq,[],[],zeros(4,1),ones(4,1),[],options2);
    z=-z;

    UB=mu1+mu2+z;
    fprintf('iter= %g;  mu1 = %3.2f;  mu2 = %3.2f; UB = %3.2f \n', iter, mu1,mu2,UB);

    t=.3;
    mu1=max([0; mu1-t*(1-x(1)-x(2))]);
    mu2=max([0; mu2-t*(1-x(3)-x(4))]);
end;
```

---

## Method 2: Constraint Generation / Cutting Plane

### Reformulating the Dual

The subproblem finds the best $x$ from feasible set $X = \{x \in \{0,1\}^4 : 8x_1 + 2x_2 + x_3 + 4x_4 \leq 10\}$.

Let $x^1, x^2, \ldots, x^H$ be all feasible points in $X$. Then:

$$z_{SP} = \max_{h=1,\ldots,H} \{(16-u_1)x_1^h + (10-u_1)x_2^h + (-u_2)x_3^h + (4-u_2)x_4^h\}$$

### The Master Problem [MP]

Let $\theta = z_{SP}$. The Lagrangean dual becomes:

$$[MP]: \min \quad u_1 + u_2 + \theta$$
$$\text{s.t.} \quad \theta + (x_1^h + x_2^h)u_1 + (x_3^h + x_4^h)u_2 \geq 16x_1^h + 10x_2^h + 4x_4^h \quad h = 1, \ldots, H$$
$$\quad\quad u_1, u_2 \geq 0$$

### Algorithm

```
While LB ≠ UB:
    1. Start with any values of u ≥ 0
    2. Solve SP, get solution xʰ and upper bound UBₕ
    3. Update: UB = min(UB, UBₕ)
    4. Use xʰ to add one more cut to MP
    5. Solve new MP, get solution u and lower bound LB
End While
```

---

## Constraint Generation: Worked Example

### Iteration 1 ($h = 1$): Start with $u_1 = 20, u_2 = 20$

**Subproblem:**
$$\max \quad -4x_1 - 10x_2 - 20x_3 - 16x_4$$
$$\text{s.t.} \quad 8x_1 + 2x_2 + x_3 + 4x_4 \leq 10, \quad x_i \in \{0,1\}$$

**Solution:** $x^{h=1} = (0, 0, 0, 0)$, $z_{SP} = 0$, $UB = 40$

**Add constraint to MP:** $\theta \geq 0$

**Solve MP:** $u_1 = 0, u_2 = 0, \theta = 0$, $LB = 0$

### Iteration 2 ($h = 2$): $u_1 = 0, u_2 = 0$

**Subproblem:**
$$\max \quad 16x_1 + 10x_2 + 4x_4$$

**Solution:** $x^{h=2} = (1, 1, 0, 0)$, $z_{SP} = 26$, $UB = \min(40, 26) = 26$

**Add constraint:** $\theta + 2u_1 \geq 26$

**Solve MP:** $u_1 = 13, u_2 = 0, \theta = 0$, $LB = 13$

### Iteration 3 ($h = 3$): $u_1 = 13, u_2 = 0$

**Subproblem:**
$$\max \quad 3x_1 - 3x_2 + 4x_4$$

**Solution:** $x^{h=3} = (0, 0, 0, 1)$, $z_{SP} = 4$, $UB = \min(26, 17) = 17$

**Add constraint:** $\theta + u_2 \geq 4$

**Solve MP:** $u_1 = 11, u_2 = 0, \theta = 4$, $LB = 15$

### Iteration 4 ($h = 4$): $u_1 = 11, u_2 = 0$

**Subproblem:**
$$\max \quad 5x_1 - x_2 + 4x_4$$

**Solution:** $x^{h=4} = (1, 0, 0, 0)$, $z_{SP} = 5$, $UB = \min(17, 16) = 16$

**Add constraint:** $\theta + u_1 \geq 16$

**Solve MP:** $u_1 = 10.35, u_2 = 0, \theta = 5.65$, $LB = 16$

### Convergence

$UB = LB = 16$ → **STOP**

**Optimal Lagrange multipliers:** $u_1^* = 11, u_2^* = 0$

**Lagrangean bound:** 16

---

## MATLAB Implementation (Constraint Generation)

```matlab
f=[1;1;1];
A=[]; lb=[0;0;0]; ub=[inf;inf;inf]; b=[];

z_master=-inf; z_lag=inf;

options=optimoptions('linprog','display','off');
options2 = optimoptions('intlinprog','display','off');

% Initializations
u1=0; u2=0; iter=0;

while (abs(z_lag-z_master)>0.0001)
    iter=iter+1;

    % Solve SP
    obj=[16-u1;10-u1;-u2;4-u2]';
    weight=[8,2,1,4];
    x=zeros(4,1);
    x=intlinprog(-obj,[1:4],weight,10,[],[],zeros(4,1),ones(4,1),[],options2);

    z_lag=min(z_lag, u1+u2+obj*x);

    % Add new constraint to MP
    new_const=[x(1)+x(2), x(3)+x(4), 1];
    A=[A; new_const];
    b=[b; [16,10,0,4]*x];

    % Solve MP
    [uutheta, z_master]=linprog(f,-A,-b,[],[],lb,ub,[],options);
    u1=uutheta(1);
    u2=uutheta(2);

    fprintf('iter=%g: u1= %2.2f, u2 = %2.2f, x1 = %2g,x2 = %2g,x3 = %2g,x4 = %2g, z_master =%2.2f, z_lag=%2.2f\n', ...
            iter, u1,u2,x(1),x(2),x(3),x(4),z_master,z_lag);
end;
```

**Output:**
```
iter=1: u1= 13.00, u2 = 0.00, x1 = 1,x2 = 1,x3 = 0,x4 = 0, z_lower =13.00, z_upper=26.00
iter=2: u1= 11.00, u2 = 0.00, x1 = 0,x2 = 0,x3 = 0,x4 = 1, z_lower =15.00, z_upper=17.00
iter=3: u1= 12.00, u2 = 0.00, x1 = 1,x2 = 0,x3 = 0,x4 = 0, z_lower =16.00, z_upper=16.0
```

---

## Relationship to Dantzig-Wolfe Decomposition: Branch-and-Price

### Dantzig-Wolfe Reformulation

Express $x$ as convex combination of extreme points:
$$x = \sum_{h=1}^{H} \alpha_h x^h, \quad \sum_{h=1}^{H} \alpha_h = 1, \quad \alpha_h \in \{0, 1\}$$

The original problem becomes:

$$[DW]: \max \sum_{h=1}^{H} (16x_1^h + 10x_2^h + 4x_4^h)\alpha_h$$
$$\text{s.t.} \quad \sum_{h=1}^{H} (x_1^h + x_2^h)\alpha_h \leq 1$$
$$\quad\quad \sum_{h=1}^{H} (x_3^h + x_4^h)\alpha_h \leq 1$$
$$\quad\quad \sum_{h=1}^{H} \alpha_h = 1$$
$$\quad\quad \alpha_h \in \{0, 1\}$$

### LP Relaxation [DMP]

$$[DMP]: \max \sum_{h=1}^{H} (16x_1^h + 10x_2^h + 4x_4^h)\alpha_h$$
$$\text{s.t.} \quad \sum_{h=1}^{H} (x_1^h + x_2^h)\alpha_h \leq 1 \quad \rightarrow u_1$$
$$\quad\quad \sum_{h=1}^{H} (x_3^h + x_4^h)\alpha_h \leq 1 \quad \rightarrow u_2$$
$$\quad\quad \sum_{h=1}^{H} \alpha_h = 1 \quad \rightarrow \theta$$
$$\quad\quad \alpha_h \geq 0$$

**Key insight:** The dual of [DMP] is exactly the Master Problem [MP]!

### Branch-and-Price Algorithm

1. At node 0, apply Dantzig-Wolfe decomposition to get best upper bound and column set $\{x^h : h \in H^{node_0}\}$

2. If all $\alpha_h$ are 0 or 1: **STOP** (optimal found)

3. Otherwise, pick fractional $\alpha_{\bar{h}}$ and branch:
   - One branch: $\alpha_{\bar{h}} = 0$
   - Other branch: $\alpha_{\bar{h}} = 1$

4. **Better approach:** Branch on original variables $x_i$:
   - If $x_i = \sum_{h=1}^{H} \alpha_h x_i^h$ is fractional
   - Branch: $x_i = 0$ vs $x_i = 1$
   - This keeps subproblems as knapsacks

### Key Properties for Branch-and-Price

1. Subproblem structure doesn't change across nodes
2. Generated columns from parent nodes can warm-start child nodes
3. Columns satisfying branching conditions are reused

---

## Summary

### Lagrangian Relaxation provides:

1. **Subproblem** - easier optimization problem (e.g., knapsack)
2. **Master problem** - finds optimal multipliers
3. **Lagrangian dual** - best bound from relaxation

### Solution Methods:

| Method | Pros | Cons |
|--------|------|------|
| **Subgradient** | Simple, low memory | Slow convergence, step size tuning |
| **Constraint Generation** | Finite convergence, LP-based | More complex, needs LP solver |

### Connection to Other Methods:

- Lagrangian dual = LP relaxation of Dantzig-Wolfe reformulation
- Branch-and-price = [[Branch-and-Bound]] + column generation (Dantzig-Wolfe)
- Often provides tighter bounds than LP relaxation (see [[Week 2#1.2.1 Strength of Formulations|Strength of Formulations]])
- Column generation relates to [[Set-Covering-Column-Generation|Set Covering formulations]]
