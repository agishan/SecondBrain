# Linear Regression

**Course:** MSCI 546 - Advanced Machine Learning
**Instructor:** Sirisha Rambhatla, University of Waterloo

**Course Overview:** [[00-Course-Overview]]

---

## Related Topics
- **Math background:** [[Linear-Algebra]], [[Optimization-for-ML]]
- **Probabilistic view:** [[Statistics-MLE]]
- **Comparison:** [[Logistic-Regression]] - Classification instead of regression
- **Extension:** [[Neural-Networks]] - Nonlinear models

---

## Motivation: Predicting Continuous Outcomes

**Regression** predicts a continuous outcome variable using past observations.

**Examples:**
- Predicting future temperature
- Predicting the amount of rainfall
- Predicting the demand of a product
- Predicting the sale price of a house

### Key Difference from Classification

| Aspect | Classification | Regression |
|--------|----------------|------------|
| Output | Discrete labels | Continuous values |
| Error measure | Right or wrong | Distance from target |
| Loss function | 0-1, logistic, hinge | Squared error |

**Linear Regression:** Regression with linear models.

---

## Problem Setup

### Notation

| Symbol | Meaning |
|--------|---------|
| $\boldsymbol{x} \in \mathbb{R}^D$ | Input features (covariates, predictors) |
| $y \in \mathbb{R}$ | Output (response, target) |
| $\mathcal{D} = \{(\boldsymbol{x}_n, y_n)\}_{n=1}^{N}$ | Training data |
| $\boldsymbol{w} \in \mathbb{R}^D$ | Weight vector (parameters) |
| $w_0$ | Bias term (intercept) |

### Linear Model

$$f(\boldsymbol{x}) = w_0 + \sum_{d=1}^{D} w_d x_d = w_0 + \boldsymbol{w}^\top \boldsymbol{x}$$

This defines a **hyperplane** parameterized by:
- $\boldsymbol{w}$ = weight vector (slope)
- $w_0$ = bias (intercept)

### Notation Simplification

Append 1 to each $\boldsymbol{x}$ to absorb the bias:

$$\tilde{\boldsymbol{x}} = [1, x_1, x_2, \ldots, x_D]^\top \in \mathbb{R}^{D+1}$$
$$\tilde{\boldsymbol{w}} = [w_0, w_1, w_2, \ldots, w_D]^\top \in \mathbb{R}^{D+1}$$

Then: $f(\boldsymbol{x}) = \boldsymbol{w}^\top \boldsymbol{x}$ (bias included in $\boldsymbol{w}$)

---

## Measuring Prediction Error

### Why Not 0-1 Loss?

The classification error (0-1 loss) is inappropriate for continuous outcomes - being "close" should be rewarded.

### Error Metrics

| Error | Formula |
|-------|---------|
| Absolute error | $|f(\boldsymbol{x}) - y|$ |
| Squared error | $(f(\boldsymbol{x}) - y)^2$ |

**Squared error** is most common because it's:
- Differentiable everywhere
- Penalizes large errors more

---

## Least Squares Solution

### Goal

Find $\boldsymbol{w}^*$ that minimizes total squared error on training data.

### Residual Sum of Squares (RSS)

$$\text{RSS}(\boldsymbol{w}) = \sum_{n=1}^{N} (f(\boldsymbol{x}_n) - y_n)^2 = \sum_{n=1}^{N} (\boldsymbol{x}_n^\top \boldsymbol{w} - y_n)^2$$

Note: $\boldsymbol{x}_n^\top \boldsymbol{w} = \boldsymbol{w}^\top \boldsymbol{x}_n$ (scalar, order doesn't matter).

### Matrix Form

Define the **design matrix** and **target vector**:

$$\boldsymbol{X} = \begin{pmatrix} \boldsymbol{x}_1^\top \\ \boldsymbol{x}_2^\top \\ \vdots \\ \boldsymbol{x}_N^\top \end{pmatrix} \in \mathbb{R}^{N \times D}, \quad \boldsymbol{y} = \begin{pmatrix} y_1 \\ y_2 \\ \vdots \\ y_N \end{pmatrix} \in \mathbb{R}^N$$

Then:
$$\text{RSS}(\boldsymbol{w}) = \|\boldsymbol{X}\boldsymbol{w} - \boldsymbol{y}\|_2^2$$

### Objective

$$\boldsymbol{w}^* = \arg\min_{\boldsymbol{w} \in \mathbb{R}^D} \text{RSS}(\boldsymbol{w}) = \arg\min_{\boldsymbol{w}} \|\boldsymbol{X}\boldsymbol{w} - \boldsymbol{y}\|_2^2$$

This is called the **least (mean) squares solution** or **empirical risk minimizer**.

---

## Closed-Form Solution

Linear regression admits a **closed-form solution** (no iterative optimization needed).

### Derivation

Expand RSS:
$$\text{RSS}(\boldsymbol{w}) = (\boldsymbol{X}\boldsymbol{w} - \boldsymbol{y})^\top (\boldsymbol{X}\boldsymbol{w} - \boldsymbol{y})$$
$$= \boldsymbol{w}^\top \boldsymbol{X}^\top \boldsymbol{X} \boldsymbol{w} - \boldsymbol{y}^\top \boldsymbol{X} \boldsymbol{w} - \boldsymbol{w}^\top \boldsymbol{X}^\top \boldsymbol{y} + \boldsymbol{y}^\top \boldsymbol{y}$$
$$= \boldsymbol{w}^\top \boldsymbol{X}^\top \boldsymbol{X} \boldsymbol{w} - 2\boldsymbol{w}^\top \boldsymbol{X}^\top \boldsymbol{y} + \boldsymbol{y}^\top \boldsymbol{y}$$

### Taking the Gradient

Using [[Linear-Algebra#Matrix Calculus|matrix calculus]]:

$$\nabla_{\boldsymbol{w}} \text{RSS}(\boldsymbol{w}) = \frac{\partial}{\partial \boldsymbol{w}} \left( \boldsymbol{w}^\top \boldsymbol{X}^\top \boldsymbol{X} \boldsymbol{w} - 2\boldsymbol{w}^\top \boldsymbol{X}^\top \boldsymbol{y} + \boldsymbol{y}^\top \boldsymbol{y} \right)$$
$$= 2\boldsymbol{X}^\top \boldsymbol{X} \boldsymbol{w} - 2\boldsymbol{X}^\top \boldsymbol{y}$$

### Normal Equations

Setting the gradient to zero:

$$2\boldsymbol{X}^\top \boldsymbol{X} \boldsymbol{w} - 2\boldsymbol{X}^\top \boldsymbol{y} = \boldsymbol{0}$$
$$\boldsymbol{X}^\top \boldsymbol{X} \boldsymbol{w} = \boldsymbol{X}^\top \boldsymbol{y}$$

This system is called the **Normal Equations**.

### Solution

If $\boldsymbol{X}^\top \boldsymbol{X}$ is invertible:

$$\boxed{\boldsymbol{w}^* = (\boldsymbol{X}^\top \boldsymbol{X})^{-1} \boldsymbol{X}^\top \boldsymbol{y}}$$

### Pseudo-inverse

The matrix $\boldsymbol{X}^\dagger = (\boldsymbol{X}^\top \boldsymbol{X})^{-1} \boldsymbol{X}^\top$ is called the **Moore-Penrose pseudo-inverse**.

So: $\boldsymbol{w}^* = \boldsymbol{X}^\dagger \boldsymbol{y}$

---

## Computational Complexity

### Bottleneck

Computing $\boldsymbol{w}^* = (\boldsymbol{X}^\top \boldsymbol{X})^{-1} \boldsymbol{X}^\top \boldsymbol{y}$ requires:
1. $\boldsymbol{X}^\top \boldsymbol{X} \in \mathbb{R}^{D \times D}$ : $O(ND^2)$
2. Inverting $\boldsymbol{X}^\top \boldsymbol{X}$ : $O(D^3)$

**Total:** $O(ND^2 + D^3)$

For large $D$, faster methods exist (QR decomposition, iterative methods).

---

## When $\boldsymbol{X}^\top \boldsymbol{X}$ is Not Invertible

### What Happens?

If $\boldsymbol{X}^\top \boldsymbol{X}$ is singular (not invertible):
- The normal equations have **infinitely many solutions**
- All are valid minimizers of RSS

### When Does This Occur?

$\boldsymbol{X}^\top \boldsymbol{X}$ is singular when:
- $N < D$ (fewer samples than features)
- Columns of $\boldsymbol{X}$ are linearly dependent
- Features are perfectly correlated

### Eigenvalue Perspective

From [[Linear-Algebra#Matrix Decompositions|eigendecomposition]]:

$$\boldsymbol{X}^\top \boldsymbol{X} = \boldsymbol{U}^\top \begin{pmatrix} \lambda_1 & & \\ & \ddots & \\ & & \lambda_D \end{pmatrix} \boldsymbol{U}$$

If any $\lambda_i = 0$, the matrix is singular.

**Inverse:**
$$(\boldsymbol{X}^\top \boldsymbol{X})^{-1} = \boldsymbol{U}^\top \begin{pmatrix} 1/\lambda_1 & & \\ & \ddots & \\ & & 1/\lambda_D \end{pmatrix} \boldsymbol{U}$$

Division by zero if $\lambda_i = 0$!

---

## Ridge Regression

### Solution: Add Regularization

Add a small positive value $\lambda$ to the diagonal:

$$\boldsymbol{X}^\top \boldsymbol{X} + \lambda \boldsymbol{I} = \boldsymbol{U}^\top \begin{pmatrix} \lambda_1 + \lambda & & \\ & \ddots & \\ & & \lambda_D + \lambda \end{pmatrix} \boldsymbol{U}$$

Now all eigenvalues are positive → matrix is invertible!

### Ridge Regression Solution

$$\boxed{\boldsymbol{w}^* = (\boldsymbol{X}^\top \boldsymbol{X} + \lambda \boldsymbol{I})^{-1} \boldsymbol{X}^\top \boldsymbol{y}}$$

### Optimization Perspective

Ridge regression minimizes **regularized RSS**:

$$\boldsymbol{w}^* = \arg\min_{\boldsymbol{w}} \left[ \|\boldsymbol{X}\boldsymbol{w} - \boldsymbol{y}\|_2^2 + \lambda \|\boldsymbol{w}\|_2^2 \right]$$

The term $\lambda \|\boldsymbol{w}\|_2^2$ penalizes large weights.

### Properties of Ridge Regression

| Aspect | Effect |
|--------|--------|
| $\lambda = 0$ | Ordinary least squares |
| $\lambda$ small | Slight regularization |
| $\lambda$ large | Weights shrink toward zero |
| $\lambda \to \infty$ | $\boldsymbol{w}^* \to \boldsymbol{0}$ |

### Choosing $\lambda$

$\lambda$ is a **hyperparameter** - typically chosen via **cross-validation**:

1. Split data into train/validation
2. Try different $\lambda$ values
3. Select $\lambda$ with best validation performance

---

## Probabilistic Interpretation

### Gaussian Noise Model

Assume: $y = \boldsymbol{w}^\top \boldsymbol{x} + \epsilon$ where $\epsilon \sim \mathcal{N}(0, \sigma^2)$

Then: $p(y \mid \boldsymbol{x}; \boldsymbol{w}) = \mathcal{N}(y; \boldsymbol{w}^\top \boldsymbol{x}, \sigma^2)$

### MLE = Least Squares

From [[Statistics-MLE]]:

$$-\log p(y \mid \boldsymbol{x}; \boldsymbol{w}) = \frac{(y - \boldsymbol{w}^\top \boldsymbol{x})^2}{2\sigma^2} + \text{const}$$

Minimizing negative log-likelihood = minimizing squared error!

### Ridge = Gaussian Prior

If we place a prior $\boldsymbol{w} \sim \mathcal{N}(\boldsymbol{0}, \tau^2 \boldsymbol{I})$, the MAP estimate gives ridge regression with $\lambda = \sigma^2/\tau^2$.

---

## Summary

| Concept | Formula |
|---------|---------|
| **Linear model** | $f(\boldsymbol{x}) = \boldsymbol{w}^\top \boldsymbol{x}$ |
| **RSS** | $\|\boldsymbol{X}\boldsymbol{w} - \boldsymbol{y}\|_2^2$ |
| **Normal equations** | $\boldsymbol{X}^\top \boldsymbol{X} \boldsymbol{w} = \boldsymbol{X}^\top \boldsymbol{y}$ |
| **OLS solution** | $\boldsymbol{w}^* = (\boldsymbol{X}^\top \boldsymbol{X})^{-1} \boldsymbol{X}^\top \boldsymbol{y}$ |
| **Ridge solution** | $\boldsymbol{w}^* = (\boldsymbol{X}^\top \boldsymbol{X} + \lambda \boldsymbol{I})^{-1} \boldsymbol{X}^\top \boldsymbol{y}$ |
| **Ridge objective** | $\|\boldsymbol{X}\boldsymbol{w} - \boldsymbol{y}\|_2^2 + \lambda \|\boldsymbol{w}\|_2^2$ |

---

## Reading

**PiML1:** Chapter 11 – 11.1, 11.2 (11.2.1, 11.2.2), 11.3 (11.3.1)
