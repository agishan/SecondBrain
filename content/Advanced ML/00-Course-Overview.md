# MSCI 546: Advanced Machine Learning

**Instructor:** Sirisha Rambhatla
**University of Waterloo**

---

## Course Overview

This course covers the mathematical foundations and core algorithms for machine learning. The material progresses from fundamental math background to supervised learning methods including classification, regression, and neural networks.

```
                    Advanced ML Course Structure
                              │
         ┌────────────────────┼────────────────────┐
         │                    │                    │
   Math Background      Supervised Learning    Deep Learning
         │                    │                    │
    ┌────┴────┐          ┌────┴────┐              │
    │         │          │         │              │
[[Probability]]  [[Linear-Algebra]]  [[Logistic-Regression]]  [[Neural-Networks]]
[[Statistics-MLE]]  [[Optimization-for-ML]]  [[Linear-Regression]]
```

---

## Table of Contents by Lecture

### Lecture 2: Classification
> *Binary classification using logistic regression*

| Topic | File | Key Concepts |
|-------|------|--------------|
| Classification Setup | [[Logistic-Regression]] | Binary labels, decision boundaries |
| Loss Functions | [[Logistic-Regression#Loss Functions]] | 0-1 loss, perceptron, hinge, logistic |
| Empirical Risk Minimization | [[Logistic-Regression#Empirical Risk Minimization]] | Training objective |
| Sigmoid & MLE | [[Logistic-Regression#Probabilistic View and MLE]] | Maximum likelihood connection |

### Lecture 3: Math Background - Probability & Statistics
> *Foundational concepts for probabilistic ML*

| Topic | File | Key Concepts |
|-------|------|--------------|
| Probability Basics | [[Probability]] | Sample space, events, axioms |
| Random Variables | [[Probability#Random Variables]] | Discrete vs continuous, PMF, PDF, CDF |
| Expectation & Variance | [[Probability#Expectation and Variance]] | Moments of distributions |
| Common Distributions | [[Probability#Common Distributions]] | Bernoulli, Gaussian, etc. |
| Bayes' Rule | [[Probability#Bayes Rule]] | Posterior, prior, likelihood |
| Independence & Correlation | [[Probability#Independence and Correlation]] | IID, covariance |
| Statistics & MLE | [[Statistics-MLE]] | Point estimates, maximum likelihood |

### Lecture 4: Math Background - Linear Algebra & Optimization
> *Essential math for ML algorithms*

| Topic | File | Key Concepts |
|-------|------|--------------|
| Vectors & Matrices | [[Linear-Algebra]] | Basic operations, norms |
| Special Matrices | [[Linear-Algebra#Special Matrices]] | Symmetric, orthogonal, PSD |
| Matrix Decompositions | [[Linear-Algebra#Matrix Decompositions]] | EVD, SVD |
| Matrix Calculus | [[Linear-Algebra#Matrix Calculus]] | Gradients, Jacobian, Hessian |
| Optimization Basics | [[Optimization-for-ML]] | Loss functions, local/global optima |
| Convexity | [[Optimization-for-ML#Convex Optimization]] | Convex sets and functions |
| Gradient Descent | [[Optimization-for-ML#Gradient Descent]] | Iterative optimization |

### Lecture 5: Linear Regression
> *Predicting continuous outcomes*

| Topic | File | Key Concepts |
|-------|------|--------------|
| Regression Setup | [[Linear-Regression]] | Features, targets, linear model |
| Least Squares | [[Linear-Regression#Least Squares Solution]] | RSS, closed-form solution |
| Normal Equations | [[Linear-Regression#Normal Equations]] | $\boldsymbol{w}^* = (\boldsymbol{X}^\top\boldsymbol{X})^{-1}\boldsymbol{X}^\top\boldsymbol{y}$ |
| Ridge Regression | [[Linear-Regression#Ridge Regression]] | Regularization, non-invertible $\boldsymbol{X}^\top\boldsymbol{X}$ |

### Lecture 6: Neural Networks
> *Deep learning fundamentals*

| Topic | File | Key Concepts |
|-------|------|--------------|
| Motivation | [[Neural-Networks]] | Nonlinear mappings |
| Network Architecture | [[Neural-Networks#Network Architecture]] | Layers, neurons, activation functions |
| Backpropagation | [[Neural-Networks#Backpropagation]] | Chain rule, gradient computation |
| Training with SGD | [[Neural-Networks#Training Algorithm]] | Stochastic gradient descent |

---

## Core Concepts Map

### Learning Paradigm
```
Training Data D = {(x₁,y₁), ..., (xₙ,yₙ)}
                    │
                    ▼
            ┌───────────────┐
            │  Model f(x;θ) │
            └───────┬───────┘
                    │
         ┌──────────┴──────────┐
         │                     │
    Classification          Regression
    y ∈ {-1, +1}           y ∈ ℝ
         │                     │
         ▼                     ▼
[[Logistic-Regression]]  [[Linear-Regression]]
         │                     │
         └──────────┬──────────┘
                    │
                    ▼
            [[Neural-Networks]]
          (handles both tasks)
```

### Mathematical Dependencies
```
[[Probability]] ──────► [[Statistics-MLE]] ──────► [[Logistic-Regression]]
                              │                          │
                              │                          ▼
[[Linear-Algebra]] ──► [[Optimization-for-ML]] ──► [[Linear-Regression]]
        │                     │                          │
        │                     │                          ▼
        └─────────────────────┴─────────────────► [[Neural-Networks]]
```

---

## Key Definitions

| Term | Definition | First Appears |
|------|------------|---------------|
| **Empirical Risk** | Average loss over training data | [[Logistic-Regression]] |
| **Loss Function** | Measures prediction error | [[Logistic-Regression]] |
| **MLE** | Maximum Likelihood Estimation | [[Statistics-MLE]] |
| **Gradient** | Vector of partial derivatives | [[Linear-Algebra]] |
| **Convex Function** | $f(\lambda x + (1-\lambda)y) \leq \lambda f(x) + (1-\lambda)f(y)$ | [[Optimization-for-ML]] |
| **RSS** | Residual Sum of Squares | [[Linear-Regression]] |
| **Regularization** | Penalty on model complexity | [[Linear-Regression]] |
| **Activation Function** | Nonlinearity in neural networks | [[Neural-Networks]] |
| **Backpropagation** | Efficient gradient computation via chain rule | [[Neural-Networks]] |

---

## Algorithm Comparison

| Algorithm | Task | Model | Optimization |
|-----------|------|-------|--------------|
| **[[Logistic-Regression]]** | Classification | Linear | Gradient descent |
| **[[Linear-Regression]]** | Regression | Linear | Closed-form / GD |
| **[[Neural-Networks]]** | Both | Nonlinear | Backprop + SGD |

---

## Reading References

| Lecture | Reading |
|---------|---------|
| Lec 2 (Logistic Regression) | PiML1 Chapter 10 – 10.1, 10.2 |
| Lec 5 (Linear Regression) | PiML1 Chapter 11 – 11.1, 11.2, 11.3 |
| Lec 6 (Neural Networks) | Chapter 13 (relevant sections) |

---

## File Index

### Math Background
- [[Probability]] - Probability theory fundamentals
- [[Statistics-MLE]] - Statistics and maximum likelihood estimation
- [[Linear-Algebra]] - Linear algebra and matrix calculus
- [[Optimization-for-ML]] - Optimization for machine learning

### Supervised Learning
- [[Logistic-Regression]] - Binary classification
- [[Linear-Regression]] - Continuous prediction
- [[Neural-Networks]] - Feed-forward neural networks
