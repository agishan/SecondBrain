# Statistics and Maximum Likelihood Estimation

**Course:** MSCI 546 - Advanced Machine Learning
**Instructor:** Sirisha Rambhatla, University of Waterloo

**Course Overview:** [[00-Course-Overview]]

---

## Related Topics
- **Prerequisite:** [[Probability]]
- **Applications:** [[Logistic-Regression#Probabilistic View and MLE]], [[Linear-Regression]]
- **Optimization:** [[Optimization-for-ML]]

---

## Motivation: Model Fitting from Data

The process of estimating parameters $\theta$ from data $\mathcal{D}$ is called **model fitting** or **training**, and is at the heart of machine learning.

### The Central Questions

1. **How do we know which model to fit?** (Model selection)
2. **How do we learn the parameters of these models?** (Parameter estimation)

```
    Data D                     Model f(x; θ)
  ┌─────────┐                 ┌─────────────┐
  │ (x₁,y₁) │                 │             │
  │ (x₂,y₂) │  ──────────►    │  θ = ???    │
  │   ...   │   Estimation    │             │
  │ (xₙ,yₙ) │                 └─────────────┘
  └─────────┘
```

---

## Point Estimates

A **point estimate** $\hat{\theta}$ is a single "best guess" for the true parameter $\theta$ based on observed data.

### Notation

| Symbol | Meaning |
|--------|---------|
| $\theta$ | True (unknown) parameter |
| $\hat{\theta}$ | Estimate of $\theta$ |
| $\mathcal{D}$ | Observed data |

### Desirable Properties

1. **Consistency:** $\hat{\theta} \to \theta$ as sample size $n \to \infty$
2. **Unbiasedness:** $\mathbb{E}[\hat{\theta}] = \theta$
3. **Efficiency:** Low variance among unbiased estimators

---

## Maximum Likelihood Estimation (MLE)

**MLE** is the most common method for parameter estimation. The idea: find the parameter value that makes the observed data most probable.

### Setup

- **Data:** $\mathcal{D} = \{x_1, x_2, \ldots, x_n\}$ (assume IID)
- **Model:** Probability distribution $p(x; \theta)$ parameterized by $\theta$
- **Goal:** Find $\theta$ that "best explains" the data

### Likelihood Function

The **likelihood** is the probability of observing the data as a function of $\theta$:

$$\mathcal{L}(\theta) = p(\mathcal{D}; \theta) = \prod_{i=1}^{n} p(x_i; \theta)$$

> **Key insight:** Same formula as joint probability, but viewed as a function of $\theta$ (with data fixed) rather than a function of data (with $\theta$ fixed).

### MLE Objective

$$\hat{\theta}_{\text{MLE}} = \arg\max_{\theta} \mathcal{L}(\theta) = \arg\max_{\theta} \prod_{i=1}^{n} p(x_i; \theta)$$

### Log-Likelihood

Taking the log converts products to sums (easier to optimize):

$$\ell(\theta) = \log \mathcal{L}(\theta) = \sum_{i=1}^{n} \log p(x_i; \theta)$$

Since $\log$ is monotonic:
$$\hat{\theta}_{\text{MLE}} = \arg\max_{\theta} \ell(\theta)$$

### Negative Log-Likelihood (NLL)

To convert to a minimization problem:

$$\hat{\theta}_{\text{MLE}} = \arg\min_{\theta} \left[ -\ell(\theta) \right] = \arg\min_{\theta} \sum_{i=1}^{n} -\log p(x_i; \theta)$$

---

## MLE Examples

### Example 1: Bernoulli Distribution

**Data:** $n$ coin flips, $k$ heads observed.

**Model:** $X \sim \text{Bernoulli}(\theta)$, so $p(x; \theta) = \theta^x (1-\theta)^{1-x}$

**Likelihood:**
$$\mathcal{L}(\theta) = \prod_{i=1}^{n} \theta^{x_i}(1-\theta)^{1-x_i} = \theta^k (1-\theta)^{n-k}$$

**Log-likelihood:**
$$\ell(\theta) = k \log \theta + (n-k) \log(1-\theta)$$

**Finding MLE:** Set derivative to zero:
$$\frac{d\ell}{d\theta} = \frac{k}{\theta} - \frac{n-k}{1-\theta} = 0$$

**Solution:**
$$\boxed{\hat{\theta}_{\text{MLE}} = \frac{k}{n}}$$

The MLE is simply the fraction of heads observed!

### Example 2: Gaussian Distribution

**Data:** $\{x_1, \ldots, x_n\}$ from $\mathcal{N}(\mu, \sigma^2)$

**Log-likelihood:**
$$\ell(\mu, \sigma^2) = -\frac{n}{2}\log(2\pi) - \frac{n}{2}\log(\sigma^2) - \frac{1}{2\sigma^2}\sum_{i=1}^{n}(x_i - \mu)^2$$

**MLE solutions:**
$$\hat{\mu}_{\text{MLE}} = \frac{1}{n}\sum_{i=1}^{n} x_i = \bar{x}$$

$$\hat{\sigma}^2_{\text{MLE}} = \frac{1}{n}\sum_{i=1}^{n}(x_i - \bar{x})^2$$

The MLE for the mean is the sample mean, and for variance is the sample variance.

---

## MLE for Supervised Learning

In supervised learning, we have input-output pairs $\mathcal{D} = \{(\boldsymbol{x}_n, y_n)\}_{n=1}^{N}$.

### Conditional Likelihood

We model the conditional distribution $p(y \mid \boldsymbol{x}; \boldsymbol{w})$:

$$\mathcal{L}(\boldsymbol{w}) = \prod_{n=1}^{N} p(y_n \mid \boldsymbol{x}_n; \boldsymbol{w})$$

### MLE Objective

$$\hat{\boldsymbol{w}}_{\text{MLE}} = \arg\max_{\boldsymbol{w}} \sum_{n=1}^{N} \log p(y_n \mid \boldsymbol{x}_n; \boldsymbol{w})$$

Or equivalently (minimizing negative log-likelihood):

$$\hat{\boldsymbol{w}}_{\text{MLE}} = \arg\min_{\boldsymbol{w}} \sum_{n=1}^{N} -\log p(y_n \mid \boldsymbol{x}_n; \boldsymbol{w})$$

---

## Connection to Loss Functions

The negative log-likelihood is equivalent to common loss functions!

### Classification: Logistic Loss

For $p(y \mid \boldsymbol{x}; \boldsymbol{w}) = \sigma(y \boldsymbol{w}^\top \boldsymbol{x})$ (see [[Logistic-Regression]]):

$$-\log p(y \mid \boldsymbol{x}; \boldsymbol{w}) = -\log \sigma(y \boldsymbol{w}^\top \boldsymbol{x}) = \log(1 + e^{-y \boldsymbol{w}^\top \boldsymbol{x}})$$

This is exactly the **logistic loss**!

### Regression: Squared Loss

For $p(y \mid \boldsymbol{x}; \boldsymbol{w}) = \mathcal{N}(y; \boldsymbol{w}^\top \boldsymbol{x}, \sigma^2)$ (see [[Linear-Regression]]):

$$-\log p(y \mid \boldsymbol{x}; \boldsymbol{w}) = \frac{(y - \boldsymbol{w}^\top \boldsymbol{x})^2}{2\sigma^2} + \text{const}$$

Minimizing NLL is equivalent to minimizing **squared error**!

---

## Summary: MLE and Loss Functions

| Model | Likelihood | Loss Function |
|-------|------------|---------------|
| Logistic regression | $\sigma(y \boldsymbol{w}^\top \boldsymbol{x})$ | Logistic/Cross-entropy |
| Linear regression | $\mathcal{N}(y; \boldsymbol{w}^\top \boldsymbol{x}, \sigma^2)$ | Mean Squared Error |

> **Key insight:** Many loss functions in ML are derived from probabilistic models via MLE!

---

## Properties of MLE

### Advantages

1. **Asymptotically optimal:** Achieves lowest variance among consistent estimators as $n \to \infty$
2. **Asymptotically normal:** $\hat{\theta}_{\text{MLE}} \approx \mathcal{N}(\theta, I(\theta)^{-1}/n)$
3. **Invariant:** If $\hat{\theta}$ is MLE of $\theta$, then $g(\hat{\theta})$ is MLE of $g(\theta)$

### Limitations

1. **Can overfit** with small data
2. **May not exist** (unbounded likelihood)
3. **May not be unique** (multiple maxima)

---

## Beyond MLE: Regularization

To prevent overfitting, we can add a **regularization** term:

$$\hat{\theta} = \arg\min_{\theta} \left[ -\ell(\theta) + \lambda R(\theta) \right]$$

where $R(\theta)$ penalizes complex models.

**Example:** Ridge regression (see [[Linear-Regression#Ridge Regression]]) uses $R(\boldsymbol{w}) = \|\boldsymbol{w}\|_2^2$.

From a Bayesian perspective, regularization corresponds to placing a **prior** on $\theta$.

---

## Key Takeaways

1. **MLE** finds parameters that maximize the probability of observed data
2. **Log-likelihood** converts products to sums for easier optimization
3. **Common loss functions** (logistic, MSE) arise from MLE
4. **Regularization** helps prevent overfitting
