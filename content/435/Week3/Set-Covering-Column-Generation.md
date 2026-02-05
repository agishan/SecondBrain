# Set Packing, Set Covering, and Column Generation Formulations

**Course:** MSE 435 - Advanced Optimization Techniques
**Instructor:** S. Elhedhli, University of Waterloo

**Course Overview:** [[00-Course-Overview]]

---

## Related Topics
- **Basic formulations:** [[Week1_Review]], [[Week 2]]
- **Solving methods:** [[Branch-and-Bound]], [[Cutting-Planes-and-Branch-and-Cut]]
- **Dantzig-Wolfe connection:** [[Lagrangian-Relaxation#Relationship to Dantzig-Wolfe Decomposition Branch-and-Price|Branch-and-Price]]

---

## Assignment-Type Formulations (Review)

For the Bin Packing Problem:

**Decision Variables**:
- $z_k = 1$ if bin $k$ is used, 0 otherwise ($k = 1, \ldots, K$)
- $y_{kl} = 1$ if item $l$ is assigned to bin $k$, 0 otherwise

### MIP Formulation [BP_ad]

$$\min \sum_{k=1}^{K} z_k$$

Subject to:
$$\sum_{k=1}^{K} y_{kl} = 1 \quad l = 1, \ldots, L \quad \text{(each item assigned once)}$$
$$\sum_{l=1}^{L} D_l y_{kl} \leq V z_k \quad k = 1, \ldots, K \quad \text{(capacity)}$$
$$y_{kl} \leq z_k \quad k = 1, \ldots, K, l = 1, \ldots, L \quad \text{(linking)}$$
$$y_{kl}, z_k \in \{0, 1\}$$

Where $D_l$ = size of item $l$, $V$ = bin capacity.

---

## Strength of Formulations

**Key Insight**: The tightest feasible region is given by the **convex hull** of integer points. See [[Week 2#1.2.1 Strength of Formulations|Strength of Formulations]] for detailed analysis.

- A formulation corresponding to the convex hull solves the MIP at the root node
- Every extreme point of the convex hull is an integer solution
- Tighter formulations → fewer branch-and-bound nodes

---

## Column Generation / Set Covering Formulation

Instead of deciding which item goes to which bin, we consider **packing patterns**.

**Decision Variable**:
$$\alpha_h = \begin{cases} 1 & \text{if packing pattern } h \text{ is used} \\ 0 & \text{otherwise} \end{cases}$$

### Set Covering Formulation [BP_sc]

$$\min \sum_{h=1}^{H} \alpha_h$$

Subject to:
$$\sum_{h=1}^{H} a_l^h \alpha_h \geq 1 \quad l = 1, \ldots, L$$
$$\alpha_h \in \{0, 1\} \quad h = 1, \ldots, H$$

Where $a_l^h = 1$ if item $l$ is in pattern $h$, 0 otherwise.

### Example Patterns (5 items)
Each column represents a valid packing pattern:

| Pattern | Item 1 | Item 2 | Item 3 | Item 4 | Item 5 |
|---------|--------|--------|--------|--------|--------|
| 1 | 1 | 0 | 0 | 0 | 0 |
| 2 | 0 | 1 | 0 | 0 | 0 |
| 3 | 0 | 0 | 1 | 0 | 0 |
| 4 | 0 | 0 | 0 | 1 | 0 |
| 5 | 0 | 0 | 0 | 0 | 1 |
| 6 | 1 | 1 | 0 | 0 | 0 |
| 7 | 1 | 0 | 1 | 0 | 0 |
| ... | ... | ... | ... | ... | ... |

The number of patterns $H$ can be exponentially large ($\leq 2^L$).

---

## Cover-Inequality Formulation

Replace capacity constraints with **cover inequalities**.

**Original capacity constraint**:
$$\sum_{l=1}^{L} D_l y_{kl} \leq V$$

**Cover inequality**: For any set $C \subseteq \{1, 2, \ldots, L\}$ where $\sum_{l \in C} D_l > V$:
$$\sum_{l \in C} y_{kl} \leq |C| - 1$$

This says: at most $|C| - 1$ items from cover $C$ can fit in one bin.

### Formulation [BP_ci]

$$\min \sum_{k=1}^{K} z_k$$

Subject to:
$$\sum_{k=1}^{K} y_{kl} = 1 \quad l = 1, \ldots, L$$
$$\sum_{l \in C} y_{kl} \leq |C| - 1 \quad \forall C: \sum_{l \in C} D_l > V$$
$$y_{kl} \leq z_k$$
$$y_{kl}, z_k \in \{0, 1\}$$

---

## Constraint Generation

Since there are exponentially many cover inequalities, we generate them **iteratively**:

### Algorithm
1. Solve relaxed problem (without cover inequalities)
2. Check if solution violates any cover inequality
3. If violated: add the inequality and re-solve
4. If not violated: solution is optimal

### Example
**Initial solution**: All items assigned to bin 1 ($y_{11} = y_{12} = y_{13} = y_{14} = y_{15} = 1$)

**Violated cover**: All 5 items exceed capacity

**Add constraint**: $y_{k1} + y_{k2} + y_{k3} + y_{k4} + y_{k5} \leq 4$ for $k = 1, \ldots, 5$

Continue iterating until no violations...

---

## Example 1: Fire Station Location

**Problem**: A city has 6 districts. Build minimum number of fire stations such that each district has a station within 15 minutes driving time.

**Driving times** (minutes):

| | Dist. 1 | Dist. 2 | Dist. 3 | Dist. 4 | Dist. 5 | Dist. 6 |
|---|---------|---------|---------|---------|---------|---------|
| Dist. 1 | 0 | 10 | 20 | 30 | 30 | 20 |
| Dist. 2 | | 0 | 25 | 35 | 20 | 10 |
| Dist. 3 | | | 0 | 15 | 30 | 20 |
| Dist. 4 | | | | 0 | 15 | 25 |
| Dist. 5 | | | | | 0 | 14 |
| Dist. 6 | | | | | | 0 |

### Coverage Sets (within 15 minutes)
- Station at Dist. 1: covers {1, 2}
- Station at Dist. 2: covers {1, 2, 6}
- Station at Dist. 3: covers {3, 4}
- Station at Dist. 4: covers {3, 4, 5}
- Station at Dist. 5: covers {4, 5, 6}
- Station at Dist. 6: covers {2, 5, 6}

### Assignment-Type Formulation

$$x_i = 1 \text{ if station built in district } i$$

$$\min \sum_{i=1}^{6} x_i$$

Subject to:
$$x_1 + x_2 \geq 1 \quad \text{(cover district 1)}$$
$$x_1 + x_2 + x_6 \geq 1 \quad \text{(cover district 2)}$$
$$x_3 + x_4 \geq 1 \quad \text{(cover district 3)}$$
$$x_3 + x_4 + x_5 \geq 1 \quad \text{(cover district 4)}$$
$$x_4 + x_5 + x_6 \geq 1 \quad \text{(cover district 5)}$$
$$x_2 + x_5 + x_6 \geq 1 \quad \text{(cover district 6)}$$
$$x_i \in \{0, 1\}$$

### Column Generation Formulation

Each column = a maximal set of districts covered by one station.

---

## Example 2: Airline Crew Scheduling

**Problem**: Air Canada wants to minimize crew costs on a subnetwork (London, Toronto, Windsor).

| Flight | Departure | Destination | Dep. Time | Arr. Time |
|--------|-----------|-------------|-----------|-----------|
| 1 | Toronto | London | 09:00 | 10:00 |
| 2 | Windsor | London | 10:00 | 10:30 |
| 3 | London | Toronto | 11:00 | 11:30 |
| 4 | London | Toronto | 11:30 | 12:30 |
| 5 | Toronto | London | 12:00 | 12:30 |
| 6 | London | Windsor | 01:00 | 01:30 |
| 7 | Toronto | Windsor | 01:00 | 02:00 |
| 8 | Windsor | Toronto | 03:00 | 04:00 |

**Rules**:
- One crew at each airport initially
- Crews must return to base at end of duty
- **Duty cost** = total elapsed time (first departure to last arrival)

**Objective**: Cover each flight exactly once, minimize total duty cost.

### Assignment-Type Formulation

Variables: $x_{cf}$ = 1 if crew $c$ covers flight $f$

### Column Generation Formulation

Each column = a valid duty (sequence of flights returning to base)

**Example duties from Toronto**:
- Duty A: Flight 1 → Flight 3 (cost: 2.5 hours)
- Duty B: Flight 1 → Flight 4 (cost: 3.5 hours)
- Duty C: Flight 1 → Flight 6 → Flight 8 (cost: 7 hours)
- ...

The column generation approach handles the combinatorial complexity by generating promising duties dynamically.

---

## Summary: Three Formulation Approaches

| Approach | Variables | Constraints | Characteristics |
|----------|-----------|-------------|-----------------|
| **Assignment** | $O(n \times m)$ | Polynomial | Weak LP relaxation |
| **Set Covering** | Exponential ($2^n$) | $O(n)$ | Strong LP, need column generation |
| **Cover Inequalities** | $O(n \times m)$ | Exponential | Need constraint generation |

**Trade-off**:
- Assignment formulations are compact but weak
- Set covering/column generation gives tighter bounds but requires sophisticated algorithms
- Cover inequalities can be added dynamically to strengthen formulations
