# Logistic Regression

**Course:** MSCI 546 - Advanced Machine Learning
**Instructor:** Sirisha Rambhatla, University of Waterloo

**Course Overview:** [[00-Course-Overview]]

---

## Related Topics
- **Prerequisite:** [[Probability]], [[Statistics-MLE]]
- **Math background:** [[Linear-Algebra]], [[Optimization-for-ML]]
- **Extension:** [[Neural-Networks]] - Nonlinear classification
- **Comparison:** [[Linear-Regression]] - Continuous outcomes

---

## Machine Learning Overview

Machine learning is a branch of AI that aims to develop algorithms that can **learn from experience**. Rather than hand-coding rules, ML algorithms analyze data to make predictions or decisions.

### The Learning Framework

Given:
- **Training data:** $\mathcal{D} = \{(\boldsymbol{x}_1, y_1), \ldots, (\boldsymbol{x}_n, y_n)\}$
- **Input space:** $\mathcal{X}$ (features)
- **Output space:** $\mathcal{Y}$ (labels/targets)

Goal: Learn a function $f: \mathcal{X} \to \mathcal{Y}$ that generalizes to unseen data.

### Types of Learning Problems

| Type | Output $\mathcal{Y}$ | Example |
|------|---------------------|---------|
| **Classification** | Discrete labels | Spam detection, image recognition |
| **Regression** | Continuous values | House price prediction |

---

## Classification

**Goal:** Predict a discrete label $y$ given input features $\boldsymbol{x}$.

### Binary Classification Setup

- **Input:** $\boldsymbol{x} \in \mathbb{R}^D$ (feature vector)
- **Output:** $y \in \{-1, +1\}$ (binary label)
- **Training data:** $\mathcal{D} = \{(\boldsymbol{x}_n, y_n)\}_{n=1}^{N}$

**Examples:**
- Email spam detection: $\boldsymbol{x}$ = word frequencies, $y$ = spam/not spam
- Medical diagnosis: $\boldsymbol{x}$ = patient features, $y$ = disease/healthy
- Credit approval: $\boldsymbol{x}$ = financial data, $y$ = approve/deny

---

## Linear Classifiers

A **linear classifier** uses a linear function of the input to make predictions:

$$f(\boldsymbol{x}) = \text{sign}(\boldsymbol{w}^\top \boldsymbol{x})$$

where:
- $\boldsymbol{w} \in \mathbb{R}^D$ is the **weight vector** (parameters to learn)
- $\boldsymbol{w}^\top \boldsymbol{x} = \sum_{d=1}^{D} w_d x_d$ is the linear combination

### Decision Boundary

The **decision boundary** is the hyperplane where $\boldsymbol{w}^\top \boldsymbol{x} = 0$:

```
        w^T x > 0  →  predict +1
              │
    ──────────┼──────────  (decision boundary: w^T x = 0)
              │
        w^T x < 0  →  predict -1
```

### Including a Bias Term

To allow the decision boundary to not pass through the origin:

$$f(\boldsymbol{x}) = \text{sign}(\boldsymbol{w}^\top \boldsymbol{x} + b)$$

**Notation trick:** Append 1 to $\boldsymbol{x}$ and $b$ to $\boldsymbol{w}$:
- $\tilde{\boldsymbol{x}} = [1, x_1, \ldots, x_D]^\top$
- $\tilde{\boldsymbol{w}} = [b, w_1, \ldots, w_D]^\top$

Then $\tilde{\boldsymbol{w}}^\top \tilde{\boldsymbol{x}} = b + \boldsymbol{w}^\top \boldsymbol{x}$.

---

## Loss Functions

To learn the weights $\boldsymbol{w}$, we need to measure how well our predictions match the true labels. This is done via a **loss function** $\ell(y, \hat{y})$.

### 0-1 Loss (Ideal but Impractical)

$$\ell_{0\text{-}1}(y, \hat{y}) = \mathbb{1}[y \neq \hat{y}] = \begin{cases} 0 & \text{if } y = \hat{y} \\ 1 & \text{if } y \neq \hat{y} \end{cases}$$

**Problem:** Not differentiable, NP-hard to optimize directly.

### Surrogate Loss Functions

Since 0-1 loss is intractable, we use differentiable **surrogate losses**. All are functions of the **margin** $m = y \cdot (\boldsymbol{w}^\top \boldsymbol{x})$:

| Loss | Formula | Properties |
|------|---------|------------|
| **Perceptron** | $\max(0, -m)$ | Piecewise linear, not smooth |
| **Hinge** (SVM) | $\max(0, 1-m)$ | Piecewise linear, margin-based |
| **Logistic** | $\log(1 + e^{-m})$ | Smooth, probabilistic interpretation |

### Visualizing Loss Functions

```
Loss
  │    ╲
  │     ╲  0-1 loss (step function)
  │      ╲____
  │       ╲
  │        ╲  Hinge loss
  │         ╲____
  │          ╲
  │           ╲  Logistic loss (smooth curve)
  │            ╲___
  └────────────────────── margin (y·w^T x)
         0    1
```

All surrogate losses are **upper bounds** on the 0-1 loss.

---

## Empirical Risk Minimization

**Empirical Risk Minimization (ERM)** is the principle of choosing parameters that minimize the average loss on training data.

### Empirical Risk

$$F(\boldsymbol{w}) = \frac{1}{N} \sum_{n=1}^{N} \ell(y_n, \boldsymbol{w}^\top \boldsymbol{x}_n)$$

### ERM Objective

$$\boldsymbol{w}^* = \arg\min_{\boldsymbol{w}} F(\boldsymbol{w}) = \arg\min_{\boldsymbol{w}} \frac{1}{N} \sum_{n=1}^{N} \ell(y_n, \boldsymbol{w}^\top \boldsymbol{x}_n)$$

### Optimization via Gradient Descent

See [[Optimization-for-ML#Gradient Descent]] for details. The update rule:

$$\boldsymbol{w}^{(t+1)} = \boldsymbol{w}^{(t)} - \eta \nabla F(\boldsymbol{w}^{(t)})$$

where $\eta > 0$ is the **learning rate**.

---

## The Logistic Loss

The **logistic loss** (also called log loss or cross-entropy loss) is:

$$\ell_{\text{logistic}}(y, \boldsymbol{w}^\top \boldsymbol{x}) = \log(1 + e^{-y \boldsymbol{w}^\top \boldsymbol{x}})$$

### Properties

1. **Smooth:** Differentiable everywhere
2. **Convex:** Guarantees global optimum (see [[Optimization-for-ML#Convex Optimization]])
3. **Probabilistic interpretation:** Connected to maximum likelihood

### Gradient

$$\nabla_{\boldsymbol{w}} \ell_{\text{logistic}} = \frac{-y \boldsymbol{x}}{1 + e^{y \boldsymbol{w}^\top \boldsymbol{x}}} = -y \boldsymbol{x} \cdot \sigma(-y \boldsymbol{w}^\top \boldsymbol{x})$$

where $\sigma(z) = \frac{1}{1+e^{-z}}$ is the **sigmoid function**.

---

## The Sigmoid Function

The **sigmoid** (logistic) function maps any real number to $(0, 1)$:

$$\sigma(z) = \frac{1}{1 + e^{-z}}$$

### Properties

| Property | Value |
|----------|-------|
| Range | $(0, 1)$ |
| $\sigma(0)$ | $0.5$ |
| $\lim_{z \to \infty} \sigma(z)$ | $1$ |
| $\lim_{z \to -\infty} \sigma(z)$ | $0$ |
| Derivative | $\sigma'(z) = \sigma(z)(1 - \sigma(z))$ |
| Symmetry | $\sigma(-z) = 1 - \sigma(z)$ |

### Visualization

```
σ(z)
  1 │                    ___________
    │                 __/
    │              __/
0.5 │           __/
    │        __/
    │     __/
  0 │____/
    └─────────────────────────────── z
          -4  -2   0   2   4
```

---

## Probabilistic View and MLE

### From Labels to Probabilities

Instead of predicting hard labels, we can model the **probability** of each class:

$$P(y = +1 \mid \boldsymbol{x}; \boldsymbol{w}) = \sigma(\boldsymbol{w}^\top \boldsymbol{x}) = \frac{1}{1 + e^{-\boldsymbol{w}^\top \boldsymbol{x}}}$$

$$P(y = -1 \mid \boldsymbol{x}; \boldsymbol{w}) = 1 - \sigma(\boldsymbol{w}^\top \boldsymbol{x}) = \sigma(-\boldsymbol{w}^\top \boldsymbol{x})$$

Unified form:
$$\mathbb{P}(y \mid \boldsymbol{x}; \boldsymbol{w}) = \sigma(y \boldsymbol{w}^\top \boldsymbol{x})$$

### Maximum Likelihood Estimation

See [[Statistics-MLE]] for MLE background.

**Likelihood:** Probability of observing the training labels given the model:

$$P(\boldsymbol{w}) = \prod_{n=1}^{N} \mathbb{P}(y_n \mid \boldsymbol{x}_n; \boldsymbol{w})$$

**MLE:** Find $\boldsymbol{w}^*$ that maximizes the likelihood:

$$\boldsymbol{w}^* = \arg\max_{\boldsymbol{w}} P(\boldsymbol{w}) = \arg\max_{\boldsymbol{w}} \prod_{n=1}^{N} \mathbb{P}(y_n \mid \boldsymbol{x}_n; \boldsymbol{w})$$

### Connection to Logistic Loss

Taking the log and negating (to convert max → min):

$$\boldsymbol{w}^* = \arg\max_{\boldsymbol{w}} \sum_{n=1}^{N} \log \mathbb{P}(y_n \mid \boldsymbol{x}_n; \boldsymbol{w})$$

$$= \arg\min_{\boldsymbol{w}} \sum_{n=1}^{N} -\log \mathbb{P}(y_n \mid \boldsymbol{x}_n; \boldsymbol{w})$$

$$= \arg\min_{\boldsymbol{w}} \sum_{n=1}^{N} -\log \sigma(y_n \boldsymbol{w}^\top \boldsymbol{x}_n)$$

$$= \arg\min_{\boldsymbol{w}} \sum_{n=1}^{N} \log(1 + e^{-y_n \boldsymbol{w}^\top \boldsymbol{x}_n})$$

$$= \arg\min_{\boldsymbol{w}} \sum_{n=1}^{N} \ell_{\text{logistic}}(y_n \boldsymbol{w}^\top \boldsymbol{x}_n)$$

> **Key insight:** *Minimizing logistic loss is exactly doing MLE for the sigmoid model!*

---

## Summary

| Concept | Description |
|---------|-------------|
| **Linear classifier** | $f(\boldsymbol{x}) = \text{sign}(\boldsymbol{w}^\top \boldsymbol{x})$ |
| **Logistic loss** | $\ell(m) = \log(1 + e^{-m})$ where $m = y \boldsymbol{w}^\top \boldsymbol{x}$ |
| **Sigmoid** | $\sigma(z) = 1/(1 + e^{-z})$ |
| **Probabilistic model** | $P(y \mid \boldsymbol{x}) = \sigma(y \boldsymbol{w}^\top \boldsymbol{x})$ |
| **Optimization** | ERM via gradient descent |
| **MLE connection** | Logistic loss = negative log-likelihood |

---

## Reading

**PiML1:** Chapter 10 – 10.1, 10.2 (intro and 10.2.1, 10.2.3, 10.2.4)
