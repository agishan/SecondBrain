# Optimization for Machine Learning

**Course:** MSCI 546 - Advanced Machine Learning
**Instructor:** Sirisha Rambhatla, University of Waterloo

**Course Overview:** [[00-Course-Overview]]

---

## Related Topics
- **Prerequisite:** [[Linear-Algebra#Matrix Calculus]]
- **Applications:** [[Linear-Regression]], [[Logistic-Regression]], [[Neural-Networks]]
- **Probability:** [[Statistics-MLE]] - MLE as optimization

---

## Motivation

The core problem in machine learning is **parameter estimation**: finding the values of $\boldsymbol{\theta} \in \Theta$ that minimize a scalar-valued **loss function** $\mathcal{L}: \Theta \to \mathbb{R}$.

$$\hat{\boldsymbol{\theta}} = \arg\min_{\boldsymbol{\theta} \in \Theta} \mathcal{L}(\boldsymbol{\theta})$$

The parameter space $\Theta \subset \mathbb{R}^D$ is typically continuous, so we study **continuous optimization**.

---

## General Optimization Problem

$$\begin{aligned}
\text{minimize} \quad & \mathcal{L}(\boldsymbol{\theta}) \\
\text{subject to} \quad & f_i(\boldsymbol{\theta}) \leq 0, \quad i = 1, \ldots, m \\
& h_i(\boldsymbol{\theta}) = 0, \quad i = 1, \ldots, p
\end{aligned}$$

### Difficulties

1. **Local vs global optimum** - Which did we find?
2. **Feasibility** - Finding a valid starting point
3. **Stopping criteria** - When to terminate
4. **Convergence rate** - How fast do we approach optimum
5. **Numerical issues** - Floating point precision

---

## Local and Global Optimization

### Definitions

- **Global optimum:** $\hat{\boldsymbol{\theta}} = \arg\min_{\boldsymbol{\theta} \in \Theta} \mathcal{L}(\boldsymbol{\theta})$
- **Local optimum:** $\boldsymbol{\theta}^*$ where $\mathcal{L}(\boldsymbol{\theta}^*) \leq \mathcal{L}(\boldsymbol{\theta})$ for all $\boldsymbol{\theta}$ "nearby"

```
    ┌────────────────────────────────────────┐
    │       ╱╲                               │
    │      ╱  ╲     ╱╲                       │
    │     ╱    ╲   ╱  ╲                      │
    │    ╱      ╲ ╱    ╲        ╱            │
    │   ╱        ▼      ╲      ╱             │
    │  ╱     local       ╲    ╱              │
    │ ╱      minimum      ╲  ╱               │
    │╱                     ▼                 │
    │                   global               │
    │                   minimum              │
    └────────────────────────────────────────┘
```

### Types of Local Minima

- **Strict local minimum:** $\mathcal{L}(\boldsymbol{\theta}^*)$ is strictly lower than all neighbors
- **Flat local minimum:** Surrounded by points with equal value
- **Saddle point:** Minimum in some directions, maximum in others

---

## Optimality Conditions

For continuous, twice-differentiable functions $\mathcal{L}$.

### Notation

- $\boldsymbol{g}(\boldsymbol{\theta}) = \nabla \mathcal{L}(\boldsymbol{\theta})$ : gradient vector
- $\boldsymbol{H}(\boldsymbol{\theta}) = \nabla^2 \mathcal{L}(\boldsymbol{\theta})$ : Hessian matrix

See [[Linear-Algebra#Matrix Calculus]] for details.

### First-Order Necessary Condition

If $\boldsymbol{\theta}^*$ is a local minimum, then:
$$\boldsymbol{g}^* = \nabla \mathcal{L}(\boldsymbol{\theta}^*) = \boldsymbol{0}$$

Points where $\nabla \mathcal{L} = \boldsymbol{0}$ are called **stationary points**.

### Second-Order Conditions

At a stationary point $\boldsymbol{\theta}^*$:

| Condition | $\boldsymbol{H}^*$ | Conclusion |
|-----------|---------------------|------------|
| Necessary | $\boldsymbol{H}^* \succeq 0$ (PSD) | Local min possible |
| Sufficient | $\boldsymbol{H}^* \succ 0$ (PD) | Guaranteed local min |

---

## Convex Optimization

### Convex Sets

A set $\mathcal{S}$ is **convex** if for any $\boldsymbol{x}, \boldsymbol{y} \in \mathcal{S}$ and $\lambda \in [0, 1]$:
$$\lambda \boldsymbol{x} + (1 - \lambda)\boldsymbol{y} \in \mathcal{S}$$

Geometrically: the line segment between any two points lies entirely in the set.

```
    Convex               Non-Convex
   ┌───────┐            ┌───────┐
   │  •    │            │  •    │
   │   ╲   │            │   ╲   │
   │    ╲  │            │   ╱╲  │  ← line exits set
   │     • │            │  ╱  • │
   └───────┘            └───────┘
```

### Convex Functions

A function $f: \mathcal{S} \to \mathbb{R}$ is **convex** if for any $\boldsymbol{x}, \boldsymbol{y} \in \mathcal{S}$ and $\lambda \in [0, 1]$:

$$f(\lambda \boldsymbol{x} + (1-\lambda)\boldsymbol{y}) \leq \lambda f(\boldsymbol{x}) + (1-\lambda) f(\boldsymbol{y})$$

**Interpretation:** The function lies below the chord connecting any two points.

```
f(x)
  │         •
  │        ╱╲  chord
  │       ╱  ╲
  │      •────•
  │     ╱      ╲
  │    ╱   f(x) ╲   ← function below chord
  │   ╱          ╲
  └──────────────────── x
```

### Related Definitions

- **Concave:** $-f$ is convex
- **Strictly convex:** Inequality is strict for $\lambda \in (0, 1)$ and $\boldsymbol{x} \neq \boldsymbol{y}$
- **Non-convex:** Neither convex nor concave

### Why Convexity Matters

> **Key theorem:** For convex functions, **every local minimum is a global minimum**.

This means gradient-based methods are guaranteed to find the global optimum!

### Testing Convexity

For twice-differentiable $f$:
$$f \text{ is convex} \iff \boldsymbol{H}(\boldsymbol{x}) \succeq 0 \text{ for all } \boldsymbol{x}$$

---

## Gradient Descent

**Gradient descent** is the workhorse optimization algorithm in ML.

### Basic Idea

1. Start at some point $\boldsymbol{\theta}^{(0)}$
2. Move in the direction of **steepest descent** (negative gradient)
3. Repeat until convergence

### Update Rule

$$\boldsymbol{\theta}^{(t+1)} = \boldsymbol{\theta}^{(t)} - \eta \nabla \mathcal{L}(\boldsymbol{\theta}^{(t)})$$

where $\eta > 0$ is the **learning rate** (step size).

### Algorithm

```
Input: Initial θ⁽⁰⁾, learning rate η, tolerance ε
Repeat:
    g = ∇L(θ)           # compute gradient
    θ = θ - η·g         # update parameters
Until ‖g‖ < ε           # convergence check
Return θ
```

### Learning Rate Selection

| $\eta$ too small | $\eta$ too large |
|------------------|------------------|
| Slow convergence | Oscillation/divergence |
| Many iterations needed | May miss minimum |

```
          Too small η           Good η            Too large η
               │                  │                    │
     ↘         │         ↘       │          ↙         │    ↗
      ↘        │          ↘      │         ↙          │   ↗
       ↘       │           ↘     │        ↙           │  ↗
        ↘      │            ↘    │       ↙            │ ↗
         ↘     │             ↘   │      ↙             │↗
          ↘    │              ↘  │     ↙              ↗
           ▼   │               ▼ │    ▼           diverges
```

---

## Stochastic Gradient Descent (SGD)

For large datasets, computing the full gradient is expensive:

$$\nabla \mathcal{L}(\boldsymbol{\theta}) = \frac{1}{N} \sum_{n=1}^{N} \nabla \ell_n(\boldsymbol{\theta})$$

### SGD Idea

Approximate the gradient using a **single random sample**:

$$\nabla \mathcal{L}(\boldsymbol{\theta}) \approx \nabla \ell_n(\boldsymbol{\theta})$$

### SGD Update

$$\boldsymbol{\theta}^{(t+1)} = \boldsymbol{\theta}^{(t)} - \eta \nabla \ell_n(\boldsymbol{\theta}^{(t)})$$

where $n$ is randomly chosen at each step.

### Properties

| Aspect | Full GD | SGD |
|--------|---------|-----|
| Cost per iteration | $O(N)$ | $O(1)$ |
| Gradient estimate | Exact | Noisy (unbiased) |
| Convergence | Smooth | Noisy |
| Can escape local minima | No | Sometimes |

### Mini-batch SGD

Compromise: use a **batch** of $B$ samples:

$$\nabla \mathcal{L}(\boldsymbol{\theta}) \approx \frac{1}{B} \sum_{n \in \text{batch}} \nabla \ell_n(\boldsymbol{\theta})$$

Typical batch sizes: 32, 64, 128, 256

---

## Variants and Extensions

### Momentum

Add "velocity" to smooth updates:

$$\boldsymbol{v}^{(t+1)} = \gamma \boldsymbol{v}^{(t)} + \eta \nabla \mathcal{L}(\boldsymbol{\theta}^{(t)})$$
$$\boldsymbol{\theta}^{(t+1)} = \boldsymbol{\theta}^{(t)} - \boldsymbol{v}^{(t+1)}$$

where $\gamma \in [0, 1)$ is the momentum coefficient.

### Adaptive Learning Rates

- **AdaGrad:** Adapts learning rate per parameter
- **RMSprop:** Running average of squared gradients
- **Adam:** Combines momentum and adaptive rates (most popular)

---

## Convexity of Common ML Objectives

| Model | Loss | Convex? |
|-------|------|---------|
| Linear regression | MSE | Yes |
| Logistic regression | Logistic | Yes |
| Neural networks | Various | **No** |

For non-convex problems (neural networks), gradient descent finds local minima. In practice, this often works well due to the structure of these problems.

---

## Summary

| Concept | Description |
|---------|-------------|
| **Optimization** | Finding parameters that minimize loss |
| **Convex function** | Local minima = global minima |
| **Gradient descent** | $\boldsymbol{\theta} \leftarrow \boldsymbol{\theta} - \eta \nabla \mathcal{L}$ |
| **SGD** | Use random samples for gradient |
| **Learning rate** | Controls step size |

---

## Key Takeaways for ML

1. **Convexity guarantees** global optimum for linear/logistic regression
2. **SGD** makes training on large datasets feasible
3. **Learning rate** is a critical hyperparameter
4. **Neural networks** are non-convex but still trainable
5. **Momentum and Adam** improve convergence in practice
