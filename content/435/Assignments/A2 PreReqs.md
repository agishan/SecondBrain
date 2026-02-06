# Prerequisites for HW2 - Two Levels of Explanation

## 🎈 **ELI5 Version** (Explain Like I'm 5)

### What You're Learning To Do

Imagine you have a puzzle that's really hard to solve. Instead of trying every single piece, you learn **smart tricks** to:

1. Make the puzzle easier first (relaxation)
2. Cut away bad pieces that won't work (cutting planes)
3. Split the puzzle into smaller puzzles (branching)
4. Know when to give up on a piece (fathoming)

---

## 📚 **Masters Student Version**

Here's what you need to know, organized by problem:

---

## **PROBLEM 1: Core MIP Solution Techniques**

### 1. **LP Relaxation**

**What it is:** Drop the "integer" requirement and solve as a regular linear program.

**ELI5:** If you need exactly 3 or 4 cookies, but I say "you can have 3.5 cookies", that's relaxation - I made the rule easier.

**Masters:** The LP relaxation provides:

- An **upper bound** for maximization problems (lower bound for minimization)
- The starting point for branch-and-bound
- A way to measure formulation strength

**Key formula:** $$z_{LP} \geq z^* \text{ (for max problems)}$$

**From course:** See [[Branch-and-Bound#Bounding]]

---

### 2. **Valid Cuts / Valid Inequalities**

**What it is:** A constraint that:

- ✅ Satisfied by ALL integer feasible solutions
- ✅ Eliminates the current fractional LP solution

**ELI5:** It's like adding a rule "you must have at least 2 red blocks" that all valid tower designs already follow, but your current broken design doesn't.

**Masters:**

- A cut is **valid** if: $ax \leq b$ for all $x$ in the integer feasible set
- A cut is **useful** if: it cuts off the current LP optimum
- Valid cuts tighten the LP relaxation toward the **convex hull**

**How to check if $2x_1 + x_2 \leq 7$ is valid:**

1. List all integer feasible points
2. Check if each point satisfies the inequality
3. Check if LP solution $(x_1^{LP}, x_2^{LP})$ violates it

**From course:** See [[Cutting-Planes-and-Branch-and-Cut#Main Concepts]]

---

### 3. **Gomory Cuts**

**What it is:** A systematic way to generate valid cuts from the simplex tableau.

**ELI5:** Look at your answer sheet (tableau) and use a magic formula to create a new rule that kicks out the wrong answer but keeps all right answers.

**Masters:**

**Step-by-step algorithm:**

1. **Pick a row** with fractional basic variable (must be integer in final solution)
    
2. **Rewrite the row** separating integer and fractional parts: $$x_i + \sum_j (\lfloor a_{ij} \rfloor + f_{ij})s_j = \lfloor b_i \rfloor + f_i$$ where $f_{ij}, f_i$ are fractional parts
    
3. **The Gomory cut is:** $$\sum_j f_{ij} s_j \geq f_i$$
    
4. **Convert back** to original variables using $s_j = b_j - \sum_k A_{jk}x_k$
    

**Example from your problem:** If you get $x_1 = 3.75 - 1.25s_1 + 0.25s_2$:

- Integer part: $x_1 = 3 - 2s_1 + 0s_2$
- Fractional: $0.75s_1 + 0.25s_2 = 0.75$
- **Cut:** $0.75s_1 + 0.25s_2 \geq 0.75$ → $3s_1 + s_2 \geq 3$

**From course:** See [[Cutting-Planes-and-Branch-and-Cut#Gomory's Cutting Plane Algorithm]]

---

### 4. **Revised Simplex Method**

**What it is:** Build the optimal tableau without actually doing all the simplex pivots.

**ELI5:** Instead of solving the puzzle step-by-step, you use shortcuts to jump straight to the answer sheet.

**Masters:**

**Key formula:** $$\text{Optimal tableau} = B^{-1}[b \mid A]$$

where:

- $B$ = basis matrix (columns of basic variables)
- $A$ = full constraint matrix
- $b$ = RHS vector

**Steps to build tableau:**

1. From graphical solution, identify which variables are basic (non-zero) and non-basic (zero)
2. Form basis matrix $B$ from columns of basic variables
3. Compute $B^{-1}$
4. Compute tableau rows: $B^{-1}b$ (RHS), $B^{-1}A$ (constraint coefficients)
5. Compute objective row: $c_B^T B^{-1}A - c^T$ (reduced costs)

**From course:** Standard simplex method background

---

### 5. **Branch and Bound**

**What it is:** Solve the problem by creating a tree where each node is a subproblem.

**ELI5:** Split your toy box into "red toys" and "blue toys" boxes. If the blue box still has too many toys, split it again into "small blue" and "big blue". Keep splitting until each box is sorted.

**Masters:**

**The algorithm:**

```
1. Solve LP relaxation (Node 0)
2. If integer → DONE
3. Otherwise, pick fractional variable x_j = value
4. Create two branches:
   - Left: x_j ≤ ⌊value⌋
   - Right: x_j ≥ ⌈value⌉
5. Solve LP at each child node
6. Fathom nodes if:
   - LP infeasible
   - LP solution is integer (update incumbent)
   - LP bound ≤ incumbent (for max)
7. Repeat until no unexplored nodes
```

**Key concepts:**

- **Incumbent:** Best known integer solution so far
- **Fathom:** Stop exploring a branch (prune it)
- **Bound:** LP solution gives upper bound (for max problems)

**Fathoming conditions for MAXIMIZATION:**

1. **All-integer solution** → update incumbent, fathom
2. **Infeasible LP** → fathom
3. **Bound ≤ incumbent** → fathom (can't improve)

**From course:** See [[Branch-and-Bound#The Algorithm in Action]]

---

### 6. **Valid Branching Rules**

**What it is:** When you branch, you must **partition** the feasible set AND eliminate the current LP solution.

**ELI5:** When you split your toys into two groups, every toy must go in exactly one group, and your current "wrong" answer can't be in either group.

**Masters:**

**A branching rule is valid if:**

1. $S = S_1 \cup S_2$ (covers all integer points)
2. $S_1 \cap S_2 = \emptyset$ (no overlap)
3. $x^{LP} \notin S_1$ and $x^{LP} \notin S_2$ (eliminates LP solution)

**Standard branching on variable:** $x_j \leq \lfloor x_j^{LP} \rfloor$ vs $x_j \geq \lceil x_j^{LP} \rceil$

**Alternative branching on constraint:** Pick any constraint $ax \leq b$ where:

- At LP solution: $ax^{LP} = \beta$ (fractional value)
- Branch: $ax \leq \lfloor \beta \rfloor$ vs $ax \geq \lceil \beta \rceil$

**Example:** If $x_2 = 3.6$ at LP optimum:

- **Valid:** $x_2 = 3$ vs $x_2 = 4$ vs $x_2 \geq 5$ is **NOT valid** (doesn't partition)
- **Valid:** $x_2 \leq 3$ vs $x_2 \geq 4$ ✓

**From course:** See [[Branch-and-Bound#Alternative Branching Rules]]

---

## **PROBLEM 2: Facility Location Formulations**

### 7. **Assignment-Type Formulation**

**What it is:** Use binary variables for both facility locations AND customer assignments.

**ELI5:** You have stickers for "pizza shop open/closed" and separate stickers for "kid 1 goes to shop A, kid 2 goes to shop B", etc.

**Masters:**

**Variables:**

- $z_j \in {0,1}$: facility $j$ is open
- $x_{ij} \in {0,1}$: customer $i$ is assigned to facility $j$

**Standard formulation:** $$\min \sum_j f_j z_j + \sum_i \sum_j c_{ij} x_{ij}$$ $$\text{s.t. } \sum_j x_{ij} = 1 \quad \forall i \quad \text{(each customer served once)}$$ $$\quad x_{ij} \leq z_j \quad \forall i,j \quad \text{(can't use closed facility)}$$ $$\quad x_{ij}, z_j \in {0,1}$$

where:

- $f_j$ = fixed cost to open facility $j$
- $c_{ij}$ = cost to serve customer $i$ from facility $j$

**For uncapacitated:** No capacity constraints!

**From course:** See [[Week 2#1.1 The Bin-Packing Problem]] (same structure)

---

### 8. **Set Covering Formulation**

**What it is:** Instead of assigning customers individually, choose from pre-defined "service patterns."

**ELI5:** Instead of saying "kid 1 → shop A, kid 2 → shop A", you say "Pattern 3: shop A serves kids 1 and 2."

**Masters:**

**Key idea:** Enumerate all possible ways one facility can serve a subset of customers.

**Variables:**

- $\alpha_h \in {0,1}$: pattern $h$ is selected

**Formulation:** $$\min \sum_h \text{cost}_h \cdot \alpha_h$$ $$\text{s.t. } \sum_h a_{ih} \alpha_h \geq 1 \quad \forall i \quad \text{(each customer covered)}$$ $$\quad \alpha_h \in {0,1}$$

where:

- $a_{ih} = 1$ if customer $i$ is served in pattern $h$
- $\text{cost}_h$ includes both fixed cost (if facility opens) and service costs

**Example patterns** for 3 facilities, 5 customers:

- Pattern 1: Facility 1 open, serves customers {1, 2}
- Pattern 2: Facility 1 open, serves customers {1, 3, 5}
- Pattern 3: Facility 2 open, serves customers {2, 4}
- ...

**From course:** See [[Set-Covering-Column-Generation#Column Generation / Set Covering Formulation]]

---

## **PROBLEM 3: Formulation Strength**

### 9. **Comparing LP Relaxation Feasible Regions**

**What it is:** Proving one formulation is "tighter" (stronger) than another.

**ELI5:** If one rule book allows fewer "kinda right" answers than another rule book, the stricter one is better.

**Masters:**

**Definition:** Formulation A is **at least as tight as** B if: $$F_A \subseteq F_B$$

where $F_A$ is the LP relaxation feasible region of A.

**How to prove $F_3 \subseteq F_1$:**

**Method 1: Direct implication**

- Take any point $(x, z) \in F_3$
- Show it satisfies all constraints of $F_1$
- Done!

**Method 2: Show constraint redundancy**

- Show that constraints in $F_1$ can be derived from constraints in $F_3$

**Example from your problem:**

To prove $F_3 \subseteq F_1$:

- $F_3$ has: $\sum_i D_i x_{ij} \leq V z_j$ AND $x_{ij} \leq z_j$
- $F_1$ has: $\sum_i D_i x_{ij} \leq V z_j$ (only this)
- Since $F_3$ has MORE constraints, any solution feasible to $F_3$ automatically satisfies $F_1$'s constraints
- Therefore $F_3 \subseteq F_1$ ✓

**To prove $F_3 \subseteq F_2$:**

- $F_3$ has: $\sum_i D_i x_{ij} \leq V z_j$
- $F_2$ has: $\sum_i D_i x_{ij} \leq V$ AND $x_{ij} \leq z_j$
- Need to show: if $(x,z) \in F_3$, then it satisfies $F_2$'s constraints

**From course:** See [[Week 2#1.2.1 Strength of Formulations]]

---

## 🛠️ **Implementation Skills Needed**

### For Matlab/Python:

1. **Setting up MIP in Matlab:**

```matlab
f = [objective coefficients];
A = [inequality constraint matrix];
b = [inequality RHS];
Aeq = [equality constraint matrix];
beq = [equality RHS];
intcon = [1:n]; % all variables integer

[x, fval] = intlinprog(f, intcon, A, b, Aeq, beq, lb, ub);
```

2. **Setting up MIP in Python (PuLP):**

```python
from pulp import *

prob = LpProblem("FacilityLocation", LpMinimize)
x = LpVariable.dicts("x", (I, J), cat='Binary')
z = LpVariable.dicts("z", J, cat='Binary')

prob += lpSum([f[j]*z[j] for j in J]) + lpSum([c[i][j]*x[i][j] for i in I for j in J])

for i in I:
    prob += lpSum([x[i][j] for j in J]) == 1
    
prob.solve()
```

---

## 📋 **Quick Reference: Problem-by-Problem Needs**

|Problem|Key Concepts|Course References|
|---|---|---|
|**1a**|LP relaxation, graphical method|[[Branch-and-Bound]]|
|**1b**|Valid cuts, checking validity|[[Cutting-Planes-and-Branch-and-Cut#Valid Inequalities vs. Valid Cuts]]|
|**1c**|Revised simplex, Gomory cuts|[[Cutting-Planes-and-Branch-and-Cut#Gomory's Cutting Plane Algorithm]]|
|**1d**|Branch-and-bound algorithm|[[Branch-and-Bound#The Algorithm in Action]]|
|**1e**|Valid branching rules|[[Branch-and-Bound#Alternative Branching Rules]]|
|**2.1**|Assignment formulation|[[Week 2#1.1]]|
|**2.2**|Set covering formulation|[[Set-Covering-Column-Generation]]|
|**2.3**|Matlab/Python implementation|N/A|
|**3**|Formulation strength, LP feasible regions|[[Week 2#1.2.1 Strength of Formulations]]|

---

## 🎯 **Before Starting the HW:**

**Make sure you can:**

1. ✅ Solve an LP graphically and identify the optimal basis
2. ✅ Check if an inequality is satisfied by all integer points
3. ✅ Generate a Gomory cut from a tableau row
4. ✅ Execute branch-and-bound (track incumbent, fathom nodes)
5. ✅ Write assignment-type and set covering formulations
6. ✅ Prove $F_A \subseteq F_B$ by showing constraint implications

**Review these documents in order:**

1. [[Week 2]] - Formulations and strength
2. [[Branch-and-Bound]] - B&B algorithm
3. [[Cutting-Planes-and-Branch-and-Cut]] - Valid cuts and Gomory cuts
4. [[Branch-and-Cut-Detailed]] - Worked example with tableaux

Good luck! 🚀