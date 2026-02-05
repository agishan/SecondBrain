# Practical Large Scale Optimization with illustrations in Matlab

**Author:** S. Elhedhli

**Last updated:** Jan. 2025

**Note:** Book Draft. Do not distribute without permission from the author.

**Course Overview:** [[00-Course-Overview]]

---

## Related Topics

- **Next:** [[Week 2]] - Detailed examples and formulation comparison
- **Solving MIPs:** [[Branch-and-Bound]], [[Branch-and-Cut-Detailed]]
- **Alternative relaxation:** [[Lagrangian-Relaxation]]

---

## Contents


| Chapter/Section | Title                                                                          | Page  |
| --------------- | ------------------------------------------------------------------------------ | ----- |
| **1**           | **Mixed Integer Programming Formulations and The Bin Packing Problem**         | **2** |
| 1.1             | The Bin-Packing Problem.                                                       | 2     |
| 1.2             | Example.                                                                       | 3     |
| 1.2.1           | Strength of Formulations                                                       | 4     |
| 1.3             | The Bin Packing Problem with Identical Items                                   | 7     |
| 1.4             | A Set Partitioning/Covering Formulation                                        | 8     |
| 1.5             | A Formulation with a large number of constraints (based on cover inequalities) | 10    |


---

## Chapter 1: Mixed Integer Programming Formulations and The Bin Packing Problem

### 1.1 The Bin-Packing Problem

Given a set of items $I = 1, 2, \ldots, n$ of sizes $s_i$ and a set of available bins $B = 1, 2, \ldots, m$ of identical capacity $C$, the bin-packing problem seeks to find the minimum number of bins to pack the $n$ items.

**Decision Variables:**

- $y_j$: Binary variable; 1 if bin $j$ is used, 0 otherwise.
- $x_{ij}$: Binary variable; 1 if item $i$ is assigned to bin $j$, 0 otherwise.

**Disaggregated Formulation [BP]:**

$$\min \sum_{j \in B} y_j$$

Subject to:

$$\sum_{j \in B} x_{ij} = 1 \quad \forall i \in I \tag{1.1 - Assignment}$$

$$\sum_{i \in I} s_i x_{ij} \leq C y_j \quad \forall j \in B \tag{1.2 - Capacity}$$

$$x_{ij} \leq y_j \quad \forall i \in I, \forall j \in B \tag{1.3 - Open bin}$$

$$x_{ij}, y_j \in 0, 1 \tag{1.4}$$

This formulation has $O(n + m + nm)$ constraints and $O(nm)$ variables.

**Aggregated Formulation [BPa]:**

By aggregating constraints (1.2) and (1.3), the capacity constraint becomes:

$$\sum_{i \in I} s_i x_{ij} \leq C y_j \quad \forall j \in B$$

This version has $O(n + m)$ constraints and $O(nm)$ variables.

### 1.2 Example

Consider items of sizes 20, 50, 50, 50, and 70 to be packed in bins of size 100. Assuming 5 bins are available, the optimal solution requires 3 bins.

The coefficient matrix for the disaggregated formulation exhibits a specific structure illustrated in the text, showing the relationship between item assignments and bin usage.

---

### 1.2.1 Strength of Formulations

The "goodness" of a formulation often depends on the tightness of its Linear Programming (LP) relaxation. Mixed Integer Programs (MIP) are typically solved via [[Branch-and-Bound]]; tighter feasible regions generally lead to fewer nodes explored.

- **Convex Hull:** The tightest feasible region where every extreme point is an integer solution.
- **Comparison:** If the LP feasible region of formulation A is contained within formulation B, A is considered tighter (better). Formally, if $P_A \subseteq P_B$, then formulation A is at least as tight as B.
- **[BP] vs [BPa]:** Neither formulation is strictly tighter than the other; their LP relaxation feasible regions intersect but neither contains the other.

A stronger formulation, $[BP^+]$, can be created by using the intersection of the two:

$$P_{BP^+} = P_{BP} \cap P_{BPa}$$

Though the constraint $x_{ij} \leq y_j$ is redundant for the integer problem, it is not redundant for the LP relaxation.

---

### 1.3 The Bin Packing Problem with Identical Items

If there are $d_i$ identical copies of item type $i$, the formulation can be improved by defining $x_{ij}$ as the number of copies of item $i$ assigned to bin $j$.

**Stronger Formulation for Identical Items:**

$$\min \sum_{j \in B} y_j$$

Subject to:

$$\sum_{j \in B} x_{ij} = d_i \quad \forall i \in I \tag{1.5}$$

$$\sum_{i \in I} s_i x_{ij} \leq C y_j \quad \forall j \in B \tag{1.6}$$

$$x_{ij} \leq d_i \cdot y_j \quad \forall i \in I, \forall j \in B \tag{1.7}$$

$$x_{ij} \in \mathbb{Z}^+, \quad y_j \in 0, 1 \tag{1.8}$$

---

### 1.4 A Set Partitioning/Covering Formulation

This approach considers all possible feasible packing patterns $P$ for a single bin. Let $a_{ip}$ denote the number of times item $i$ appears in pattern $p$.

**Set Covering Formulation [BPsc]:**

$$\min \sum_{p \in P} y_p$$

Subject to:

$$\sum_{p \in P} a_{ip} y_p \geq 1 \quad \forall i \in I \tag{1.9}$$

$$y_p \in 0, 1 \quad \forall p \in P \tag{1.10}$$

Because the number of patterns can be as large as $2^n$, they are often generated as needed using a **Binary Knapsack Problem** as the pricing subproblem (column generation). See [[Set-Covering-Column-Generation]] for more details on this approach.

---

### 1.5 A Formulation with a large number of constraints

This formulation uses **cover inequalities**. If a set of items $S \subseteq I$ cannot fit in a single bin (i.e., $\sum_{i \in S} s_i > C$), then at most $|S| - 1$ of those items can be assigned to that bin:

$$\sum_{i \in S} x_{ij} \leq |S| - 1 \quad \forall j \in B$$

Because there are exponentially many possible covers, a **constraint generation method** (cutting plane) is used. This is related to the [[Cutting-Planes-and-Branch-and-Cut|cutting plane method]] used in Branch-and-Cut:

1. Solve a relaxed version of the problem (without cover inequalities).
2. If the solution violates capacity, identify a violated cover $S$ where the current solution assigns too many items.
3. Add the corresponding cover inequality and resolve.
4. Repeat until no violated covers exist.

