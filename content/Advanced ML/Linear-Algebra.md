# Linear Algebra and Matrix Calculus for ML

**Course:** MSCI 546 - Advanced Machine Learning
**Instructor:** Sirisha Rambhatla, University of Waterloo

**Course Overview:** [[00-Course-Overview]]

---

## Related Topics
- **Applications:** [[Linear-Regression]], [[Neural-Networks]]
- **Optimization:** [[Optimization-for-ML]]
- **Probability:** [[Probability]] - Covariance matrices

---

## Vectors

### Notation

A **vector** $\boldsymbol{x} \in \mathbb{R}^n$ is an ordered list of $n$ real numbers:

$$\boldsymbol{x} = \begin{pmatrix} x_1 \\ x_2 \\ \vdots \\ x_n \end{pmatrix}$$

By convention, vectors are **column vectors**. Row vectors are written as $\boldsymbol{x}^\top$.

### Vector Operations

| Operation | Notation | Result |
|-----------|----------|--------|
| Addition | $\boldsymbol{x} + \boldsymbol{y}$ | $(x_1 + y_1, \ldots, x_n + y_n)^\top$ |
| Scalar mult. | $c\boldsymbol{x}$ | $(cx_1, \ldots, cx_n)^\top$ |
| Dot product | $\boldsymbol{x}^\top \boldsymbol{y}$ | $\sum_{i=1}^n x_i y_i$ |

### Inner Product (Dot Product)

$$\boldsymbol{x}^\top \boldsymbol{y} = \langle \boldsymbol{x}, \boldsymbol{y} \rangle = \sum_{i=1}^{n} x_i y_i$$

**Geometric interpretation:** $\boldsymbol{x}^\top \boldsymbol{y} = \|\boldsymbol{x}\| \|\boldsymbol{y}\| \cos\theta$

---

## Vector Norms

A **norm** measures the "size" or "length" of a vector.

### Common Norms

| Norm | Notation | Formula |
|------|----------|---------|
| $L^1$ (Manhattan) | $\|\boldsymbol{x}\|_1$ | $\sum_{i=1}^n |x_i|$ |
| $L^2$ (Euclidean) | $\|\boldsymbol{x}\|_2$ | $\sqrt{\sum_{i=1}^n x_i^2}$ |
| $L^\infty$ (Max) | $\|\boldsymbol{x}\|_\infty$ | $\max_i |x_i|$ |
| $L^p$ (General) | $\|\boldsymbol{x}\|_p$ | $\left(\sum_{i=1}^n |x_i|^p\right)^{1/p}$ |

### Properties of Norms

1. **Non-negativity:** $\|\boldsymbol{x}\| \geq 0$ with equality iff $\boldsymbol{x} = \boldsymbol{0}$
2. **Scaling:** $\|c\boldsymbol{x}\| = |c| \|\boldsymbol{x}\|$
3. **Triangle inequality:** $\|\boldsymbol{x} + \boldsymbol{y}\| \leq \|\boldsymbol{x}\| + \|\boldsymbol{y}\|$

---

## Matrices

### Notation

A **matrix** $\boldsymbol{A} \in \mathbb{R}^{m \times n}$ has $m$ rows and $n$ columns:

$$\boldsymbol{A} = \begin{pmatrix} a_{11} & a_{12} & \cdots & a_{1n} \\ a_{21} & a_{22} & \cdots & a_{2n} \\ \vdots & \vdots & \ddots & \vdots \\ a_{m1} & a_{m2} & \cdots & a_{mn} \end{pmatrix}$$

### Matrix-Vector Product

For $\boldsymbol{A} \in \mathbb{R}^{m \times n}$ and $\boldsymbol{x} \in \mathbb{R}^n$:

$$\boldsymbol{A}\boldsymbol{x} = \begin{pmatrix} \boldsymbol{a}_1^\top \boldsymbol{x} \\ \vdots \\ \boldsymbol{a}_m^\top \boldsymbol{x} \end{pmatrix} \in \mathbb{R}^m$$

where $\boldsymbol{a}_i^\top$ is the $i$-th row of $\boldsymbol{A}$.

### Matrix-Matrix Product

For $\boldsymbol{A} \in \mathbb{R}^{m \times n}$ and $\boldsymbol{B} \in \mathbb{R}^{n \times p}$:

$$(\boldsymbol{A}\boldsymbol{B})_{ij} = \sum_{k=1}^{n} a_{ik} b_{kj}$$

Result: $\boldsymbol{A}\boldsymbol{B} \in \mathbb{R}^{m \times p}$

### Properties of Matrix Multiplication

| Property | Formula |
|----------|---------|
| Associative | $(\boldsymbol{A}\boldsymbol{B})\boldsymbol{C} = \boldsymbol{A}(\boldsymbol{B}\boldsymbol{C})$ |
| Distributive | $\boldsymbol{A}(\boldsymbol{B} + \boldsymbol{C}) = \boldsymbol{A}\boldsymbol{B} + \boldsymbol{A}\boldsymbol{C}$ |
| **NOT** Commutative | $\boldsymbol{A}\boldsymbol{B} \neq \boldsymbol{B}\boldsymbol{A}$ in general |

---

## Special Matrices

### Identity Matrix

$$\boldsymbol{I}_n = \begin{pmatrix} 1 & 0 & \cdots & 0 \\ 0 & 1 & \cdots & 0 \\ \vdots & & \ddots & \vdots \\ 0 & 0 & \cdots & 1 \end{pmatrix}$$

Property: $\boldsymbol{A}\boldsymbol{I} = \boldsymbol{I}\boldsymbol{A} = \boldsymbol{A}$

### Diagonal Matrix

$$\boldsymbol{D} = \text{diag}(d_1, \ldots, d_n) = \begin{pmatrix} d_1 & & 0 \\ & \ddots & \\ 0 & & d_n \end{pmatrix}$$

### Symmetric Matrix

$\boldsymbol{A}$ is **symmetric** if $\boldsymbol{A}^\top = \boldsymbol{A}$, i.e., $a_{ij} = a_{ji}$.

### Orthogonal Matrix

$\boldsymbol{U}$ is **orthogonal** if $\boldsymbol{U}^\top \boldsymbol{U} = \boldsymbol{U}\boldsymbol{U}^\top = \boldsymbol{I}$.

Properties:
- Columns are orthonormal: $\boldsymbol{u}_i^\top \boldsymbol{u}_j = \delta_{ij}$
- $\boldsymbol{U}^{-1} = \boldsymbol{U}^\top$
- Preserves lengths: $\|\boldsymbol{U}\boldsymbol{x}\|_2 = \|\boldsymbol{x}\|_2$

### Positive Definite/Semidefinite

A symmetric matrix $\boldsymbol{A}$ is:
- **Positive definite (PD):** $\boldsymbol{x}^\top \boldsymbol{A} \boldsymbol{x} > 0$ for all $\boldsymbol{x} \neq \boldsymbol{0}$
- **Positive semidefinite (PSD):** $\boldsymbol{x}^\top \boldsymbol{A} \boldsymbol{x} \geq 0$ for all $\boldsymbol{x}$

Notation: $\boldsymbol{A} \succ 0$ (PD), $\boldsymbol{A} \succeq 0$ (PSD)

---

## Matrix Inverse

The **inverse** of a square matrix $\boldsymbol{A} \in \mathbb{R}^{n \times n}$ is denoted $\boldsymbol{A}^{-1}$:

$$\boldsymbol{A}^{-1}\boldsymbol{A} = \boldsymbol{A}\boldsymbol{A}^{-1} = \boldsymbol{I}$$

### Existence

$\boldsymbol{A}^{-1}$ exists iff $\det(\boldsymbol{A}) \neq 0$ (non-singular matrix).

### Properties

| Property | Formula |
|----------|---------|
| Inverse of inverse | $(\boldsymbol{A}^{-1})^{-1} = \boldsymbol{A}$ |
| Product | $(\boldsymbol{A}\boldsymbol{B})^{-1} = \boldsymbol{B}^{-1}\boldsymbol{A}^{-1}$ |
| Transpose | $(\boldsymbol{A}^{-1})^\top = (\boldsymbol{A}^\top)^{-1} \triangleq \boldsymbol{A}^{-\top}$ |

---

## Matrix Decompositions

### Eigenvalue Decomposition (EVD)

For a square matrix $\boldsymbol{A} \in \mathbb{R}^{n \times n}$, $\lambda$ is an **eigenvalue** and $\boldsymbol{u}$ is the corresponding **eigenvector** if:

$$\boldsymbol{A}\boldsymbol{u} = \lambda\boldsymbol{u}, \quad \boldsymbol{u} \neq \boldsymbol{0}$$

The **characteristic equation:** $\det(\lambda\boldsymbol{I} - \boldsymbol{A}) = 0$

### Diagonalization

If eigenvectors are linearly independent:

$$\boldsymbol{A} = \boldsymbol{U}\boldsymbol{\Lambda}\boldsymbol{U}^{-1}$$

where:
- $\boldsymbol{U} = [\boldsymbol{u}_1, \ldots, \boldsymbol{u}_n]$ (eigenvectors as columns)
- $\boldsymbol{\Lambda} = \text{diag}(\lambda_1, \ldots, \lambda_n)$ (eigenvalues)

For **symmetric** matrices: $\boldsymbol{A} = \boldsymbol{U}\boldsymbol{\Lambda}\boldsymbol{U}^\top$ (orthogonal $\boldsymbol{U}$)

### Singular Value Decomposition (SVD)

SVD generalizes EVD to **rectangular** matrices. Any $\boldsymbol{A} \in \mathbb{R}^{m \times n}$:

$$\boldsymbol{A} = \boldsymbol{U}\boldsymbol{S}\boldsymbol{V}^\top$$

where:
- $\boldsymbol{U} \in \mathbb{R}^{m \times m}$ is orthonormal ($\boldsymbol{U}^\top\boldsymbol{U} = \boldsymbol{I}_m$)
- $\boldsymbol{V} \in \mathbb{R}^{n \times n}$ is orthonormal ($\boldsymbol{V}^\top\boldsymbol{V} = \boldsymbol{I}_n$)
- $\boldsymbol{S} \in \mathbb{R}^{m \times n}$ has **singular values** $\sigma_1 \geq \sigma_2 \geq \cdots \geq 0$ on diagonal

**Expanded form:**
$$\boldsymbol{A} = \sigma_1 \boldsymbol{u}_1 \boldsymbol{v}_1^\top + \sigma_2 \boldsymbol{u}_2 \boldsymbol{v}_2^\top + \cdots + \sigma_r \boldsymbol{u}_r \boldsymbol{v}_r^\top$$

where $r = \min(m, n)$.

---

## Matrix Calculus

### Gradients

For a function $f: \mathbb{R}^n \to \mathbb{R}$, the **gradient** is:

$$\boldsymbol{g} = \frac{\partial f}{\partial \boldsymbol{x}} = \nabla f = \begin{pmatrix} \frac{\partial f}{\partial x_1} \\ \vdots \\ \frac{\partial f}{\partial x_n} \end{pmatrix}$$

The gradient points in the direction of steepest ascent.

### Directional Derivative

The rate of change of $f$ along direction $\boldsymbol{v}$:

$$D_{\boldsymbol{v}} f(\boldsymbol{x}) = \nabla f(\boldsymbol{x}) \cdot \boldsymbol{v}$$

### Jacobian

For $\boldsymbol{f}: \mathbb{R}^n \to \mathbb{R}^m$, the **Jacobian** is an $m \times n$ matrix:

$$\boldsymbol{J}_f(\boldsymbol{x}) = \frac{\partial \boldsymbol{f}}{\partial \boldsymbol{x}^\top} = \begin{pmatrix} \frac{\partial f_1}{\partial x_1} & \cdots & \frac{\partial f_1}{\partial x_n} \\ \vdots & \ddots & \vdots \\ \frac{\partial f_m}{\partial x_1} & \cdots & \frac{\partial f_m}{\partial x_n} \end{pmatrix} = \begin{pmatrix} \nabla f_1(\boldsymbol{x})^\top \\ \vdots \\ \nabla f_m(\boldsymbol{x})^\top \end{pmatrix}$$

### Hessian

For $f: \mathbb{R}^n \to \mathbb{R}$, the **Hessian** is the matrix of second derivatives:

$$\boldsymbol{H}_f = \frac{\partial^2 f}{\partial \boldsymbol{x}^2} = \nabla^2 f = \begin{pmatrix} \frac{\partial^2 f}{\partial x_1^2} & \cdots & \frac{\partial^2 f}{\partial x_1 \partial x_n} \\ \vdots & \ddots & \vdots \\ \frac{\partial^2 f}{\partial x_n \partial x_1} & \cdots & \frac{\partial^2 f}{\partial x_n^2} \end{pmatrix}$$

The Hessian is the **Jacobian of the gradient**.

---

## Useful Derivative Identities

For differentiable $f: \mathbb{R}^n \to \mathbb{R}$:

### Scalar-to-Scalar Chain Rule

$$\frac{d}{dx}[f(u(x))] = \frac{df}{du} \cdot \frac{du}{dx}$$

This is crucial for [[Neural-Networks#Backpropagation|backpropagation]]!

### Vector Derivatives

| Function | Derivative |
|----------|------------|
| $\boldsymbol{a}^\top \boldsymbol{x}$ | $\frac{\partial}{\partial \boldsymbol{x}}(\boldsymbol{a}^\top \boldsymbol{x}) = \boldsymbol{a}$ |
| $\boldsymbol{b}^\top \boldsymbol{A} \boldsymbol{x}$ | $\frac{\partial}{\partial \boldsymbol{x}}(\boldsymbol{b}^\top \boldsymbol{A} \boldsymbol{x}) = \boldsymbol{A}^\top \boldsymbol{b}$ |
| $\boldsymbol{x}^\top \boldsymbol{A} \boldsymbol{x}$ | $\frac{\partial}{\partial \boldsymbol{x}}(\boldsymbol{x}^\top \boldsymbol{A} \boldsymbol{x}) = (\boldsymbol{A} + \boldsymbol{A}^\top)\boldsymbol{x}$ |

If $\boldsymbol{A}$ is symmetric: $\frac{\partial}{\partial \boldsymbol{x}}(\boldsymbol{x}^\top \boldsymbol{A} \boldsymbol{x}) = 2\boldsymbol{A}\boldsymbol{x}$

### Product Rule for Vectors

$$\frac{\partial}{\partial \boldsymbol{x}}[\boldsymbol{u}(\boldsymbol{x})^\top \boldsymbol{v}(\boldsymbol{x})] = \left[\frac{\partial \boldsymbol{v}(\boldsymbol{x})}{\partial \boldsymbol{x}}\right] \boldsymbol{u}(\boldsymbol{x}) + \left[\frac{\partial \boldsymbol{u}(\boldsymbol{x})}{\partial \boldsymbol{x}}\right] \boldsymbol{v}(\boldsymbol{x})$$

### Matrix Derivatives

For $f: \mathbb{R}^{m \times n} \to \mathbb{R}$:

$$\frac{\partial f}{\partial \boldsymbol{X}} = \begin{pmatrix} \frac{\partial f}{\partial x_{11}} & \cdots & \frac{\partial f}{\partial x_{1n}} \\ \vdots & & \vdots \\ \frac{\partial f}{\partial x_{m1}} & \cdots & \frac{\partial f}{\partial x_{mn}} \end{pmatrix}$$

---

## Key Takeaways for ML

1. **Matrix-vector products** are the core operation in linear models
2. **SVD** is fundamental for dimensionality reduction (PCA)
3. **Positive definiteness** relates to convexity (see [[Optimization-for-ML]])
4. **Gradients and Hessians** are essential for optimization
5. **Chain rule** enables backpropagation in [[Neural-Networks]]
