# Practical Large Scale Optimization with illustrations in Matlab

**Author:** S. Elhedhli

**Last updated:** Jan. 2025 

**Note:** Book Draft. Do not distribute without permission from the author. 

---

## Contents

| Chapter/Section | Title | Page |
| --- | --- | --- |
| **1** | **Mixed Integer Programming Formulations and The Bin Packing Problem** | **2** |
| 1.1 | The Bin-Packing Problem. | 2 |
| 1.2 | Example. | 3 |
| 1.2.1 | Strength of Formulations | 4 |
| 1.3 | The Bin Packing Problem with Identical Items | 7 |
| 1.4 | A Set Partitioning/Covering Formulation | 8 |
| 1.5 | A Formulation with a large number of constraints (based on cover inequalities) | 10 |



---

## Chapter 1: Mixed Integer Programming Formulations and The Bin Packing Problem

### 1.1 The Bin-Packing Problem

Given a set of items  of sizes  and a set of available bins  of identical size , the bin-packing problem seeks to find the minimum number of bins to pack the  items.

**Decision Variables:**

* : Binary variable; 1 if bin  is used, 0 otherwise.


* : Binary variable; 1 if item  is assigned to bin , 0 otherwise.



**Disaggregated Formulation [BP]:**



Subject to:

* 
 for  (Assignment constraints) 


* 
 for  (Capacity constraints) 


* 
 for  (Open bin constraints) 


* 
 



This formulation has  constraints and  variables.

**Aggregated Formulation [BPa]:**
By aggregating constraints (1.3) and (1.4), the capacity constraint becomes:


This version has  constraints and  variables.

### 1.2 Example

Consider items of sizes 20, 50, 50, 50, and 70 to be packed in bins of size 100. Assuming 5 bins are available, the optimal solution requires 3 bins.

The coefficient matrix for the disaggregated formulation exhibits a specific structure illustrated in the text, showing the relationship between item assignments and bin usage.

---

### 1.2.1 Strength of Formulations

The "goodness" of a formulation often depends on the tightness of its Linear Programming (LP) relaxation. Mixed Integer Programs (MIP) are typically solved via branch-and-bound; tighter feasible regions generally lead to fewer nodes explored.

* 
**Convex Hull:** The tightest feasible region where every extreme point is an integer solution.


* 
**Comparison:** If the LP feasible region of formulation A is contained within formulation B, A is considered tighter (better).


* 
** vs :** Neither formulation is tighter than the other; their LP relaxation feasible regions intersect.



A stronger formulation, , can be created by using the intersection of the two, though the constraint  is redundant for the integer problem, it is not redundant for the LP relaxation.

---

### 1.3 The Bin Packing Problem with Identical Items

If there are  identical copies of item , the formulation can be improved by defining  as the number of copies of item  assigned to bin .

**Stronger Formulation for Identical Items:**



Subject to:

* 
 


* 
 


* 
 


* 
 integer,  



---

### 1.4 A Set Partitioning/Covering Formulation

This approach considers all possible feasible packing patterns () for a single bin.

**Set Partitioning Formulation [BPsc]:**



Subject to:

* 
 for  


* 
 



Because the number of patterns can be as large as , they are often generated as needed using a Binary Knapsack Problem.

---

### 1.5 A Formulation with a large number of constraints

This formulation uses **cover inequalities**. If a set of items  cannot fit in a bin (), then at most  of those items can be assigned to that bin:




Because there are many possible covers, a **constraint generation method** is used:

1. Solve a relaxed version of the problem.


2. If the solution violates capacity, identify the items causing the violation.


3. Add the corresponding cover inequality and resolve.



