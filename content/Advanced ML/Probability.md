# Probability Theory for Machine Learning

**Course:** MSCI 546 - Advanced Machine Learning
**Instructor:** Sirisha Rambhatla, University of Waterloo

**Course Overview:** [[00-Course-Overview]]

---

## Related Topics
- **Next:** [[Statistics-MLE]] - Using probability for estimation
- **Applications:** [[Logistic-Regression#Probabilistic View and MLE]]
- **Math background:** [[Linear-Algebra]]

---

## Why Probability in ML?

Probability provides the language for:
1. **Modeling uncertainty** in predictions
2. **Quantifying confidence** in estimates
3. **Principled learning** via maximum likelihood
4. **Generative models** that describe how data is produced

---

## Sample Space and Events

### Sample Space

The **sample space** $\Omega$ is the set of all possible outcomes of an experiment.

**Examples:**
| Experiment | Sample Space $\Omega$ |
|------------|----------------------|
| Coin flip | $\{H, T\}$ |
| Die roll | $\{1, 2, 3, 4, 5, 6\}$ |
| Real measurement | $\mathbb{R}$ |

### Events

An **event** $A$ is a subset of the sample space: $A \subseteq \Omega$.

**Examples:**
- "Rolling an even number": $A = \{2, 4, 6\}$
- "Coin shows heads": $A = \{H\}$

---

## Probability Axioms

A **probability measure** $P$ assigns a number to each event, satisfying:

### Kolmogorov Axioms

1. **Non-negativity:** $P(A) \geq 0$ for all events $A$
2. **Normalization:** $P(\Omega) = 1$
3. **Additivity:** For disjoint events $A$ and $B$ (i.e., $A \cap B = \emptyset$):
   $$P(A \cup B) = P(A) + P(B)$$

### Derived Properties

From these axioms, we can derive:

| Property | Formula |
|----------|---------|
| Complement | $P(A^c) = 1 - P(A)$ |
| Union | $P(A \cup B) = P(A) + P(B) - P(A \cap B)$ |
| Subset | If $A \subseteq B$, then $P(A) \leq P(B)$ |
| Bounds | $0 \leq P(A) \leq 1$ |

---

## Conditional Probability

The **conditional probability** of $A$ given $B$ is:

$$P(A \mid B) = \frac{P(A \cap B)}{P(B)}, \quad \text{provided } P(B) > 0$$

**Interpretation:** The probability of $A$ occurring, knowing that $B$ has occurred.

### Example

Rolling a die:
- $A$ = "roll is 6" → $P(A) = 1/6$
- $B$ = "roll is even" → $P(B) = 1/2$
- $P(A \mid B) = P(\{6\})/P(\{2,4,6\}) = (1/6)/(1/2) = 1/3$

### Chain Rule (Product Rule)

$$P(A \cap B) = P(A \mid B) \cdot P(B) = P(B \mid A) \cdot P(A)$$

For multiple events:
$$P(A_1 \cap A_2 \cap \cdots \cap A_n) = P(A_1) \cdot P(A_2 \mid A_1) \cdot P(A_3 \mid A_1, A_2) \cdots$$

---

## Bayes' Rule

**Bayes' theorem** relates conditional probabilities:

$$P(A \mid B) = \frac{P(B \mid A) \cdot P(A)}{P(B)}$$

### Components in ML Context

| Term | Name | Interpretation |
|------|------|----------------|
| $P(A \mid B)$ | **Posterior** | Updated belief after seeing data |
| $P(B \mid A)$ | **Likelihood** | How likely is data given hypothesis |
| $P(A)$ | **Prior** | Initial belief before seeing data |
| $P(B)$ | **Evidence** | Normalizing constant |

### Law of Total Probability

If $\{A_1, A_2, \ldots, A_k\}$ partition $\Omega$:

$$P(B) = \sum_{i=1}^{k} P(B \mid A_i) P(A_i)$$

This is useful for computing the evidence $P(B)$ in Bayes' rule.

---

## Random Variables

A **random variable** $X$ is a function that maps outcomes to numbers:
$$X: \Omega \to \mathbb{R}$$

### Types

| Type | Values | Example |
|------|--------|---------|
| **Discrete** | Countable set | Number of heads in 10 flips |
| **Continuous** | Uncountable (intervals) | Height, temperature |

---

## Discrete Random Variables

### Probability Mass Function (PMF)

For discrete $X$, the **PMF** gives the probability of each value:

$$p_X(x) = P(X = x)$$

**Properties:**
- $p_X(x) \geq 0$ for all $x$
- $\sum_x p_X(x) = 1$

### Common Discrete Distributions

#### Bernoulli Distribution

Single binary trial with success probability $\theta$:

$$X \sim \text{Bernoulli}(\theta)$$
$$P(X = 1) = \theta, \quad P(X = 0) = 1 - \theta$$

**PMF:** $p(x) = \theta^x (1-\theta)^{1-x}$ for $x \in \{0, 1\}$

#### Binomial Distribution

Number of successes in $n$ independent Bernoulli trials:

$$X \sim \text{Binomial}(n, \theta)$$
$$P(X = k) = \binom{n}{k} \theta^k (1-\theta)^{n-k}$$

#### Categorical Distribution

Generalization of Bernoulli to $K$ outcomes:

$$X \sim \text{Categorical}(\theta_1, \ldots, \theta_K)$$
$$P(X = k) = \theta_k, \quad \sum_{k=1}^{K} \theta_k = 1$$

---

## Continuous Random Variables

### Probability Density Function (PDF)

For continuous $X$, the **PDF** $f_X(x)$ satisfies:

$$P(a \leq X \leq b) = \int_a^b f_X(x) \, dx$$

**Properties:**
- $f_X(x) \geq 0$ for all $x$
- $\int_{-\infty}^{\infty} f_X(x) \, dx = 1$

> **Note:** $f_X(x)$ is NOT a probability! It can be $> 1$. Only areas under the curve are probabilities.

### Cumulative Distribution Function (CDF)

The **CDF** gives cumulative probability:

$$F_X(x) = P(X \leq x) = \int_{-\infty}^{x} f_X(t) \, dt$$

**Properties:**
- $F_X(-\infty) = 0$, $F_X(\infty) = 1$
- Non-decreasing
- $f_X(x) = \frac{d}{dx} F_X(x)$

### Common Continuous Distributions

#### Uniform Distribution

$$X \sim \text{Uniform}(a, b)$$
$$f(x) = \frac{1}{b-a} \quad \text{for } x \in [a, b]$$

#### Gaussian (Normal) Distribution

$$X \sim \mathcal{N}(\mu, \sigma^2)$$
$$f(x) = \frac{1}{\sqrt{2\pi\sigma^2}} \exp\left(-\frac{(x-\mu)^2}{2\sigma^2}\right)$$

**Parameters:**
- $\mu$ = mean (location)
- $\sigma^2$ = variance (spread)

**Standard normal:** $Z \sim \mathcal{N}(0, 1)$

---

## Expectation and Variance

### Expectation (Mean)

The **expected value** is the weighted average:

**Discrete:**
$$\mathbb{E}[X] = \sum_x x \cdot p_X(x)$$

**Continuous:**
$$\mathbb{E}[X] = \int_{-\infty}^{\infty} x \cdot f_X(x) \, dx$$

### Properties of Expectation

| Property | Formula |
|----------|---------|
| Linearity | $\mathbb{E}[aX + bY] = a\mathbb{E}[X] + b\mathbb{E}[Y]$ |
| Constant | $\mathbb{E}[c] = c$ |
| Function | $\mathbb{E}[g(X)] = \sum_x g(x) p_X(x)$ |

### Variance

**Variance** measures spread around the mean:

$$\text{Var}(X) = \mathbb{E}[(X - \mu)^2] = \mathbb{E}[X^2] - (\mathbb{E}[X])^2$$

where $\mu = \mathbb{E}[X]$.

**Standard deviation:** $\sigma = \sqrt{\text{Var}(X)}$

### Properties of Variance

| Property | Formula |
|----------|---------|
| Scaling | $\text{Var}(aX) = a^2 \text{Var}(X)$ |
| Constant | $\text{Var}(c) = 0$ |
| Shift | $\text{Var}(X + c) = \text{Var}(X)$ |
| Sum (indep.) | $\text{Var}(X + Y) = \text{Var}(X) + \text{Var}(Y)$ |

---

## Joint and Marginal Distributions

### Joint Distribution

For two random variables $X$ and $Y$:

**Discrete:** Joint PMF $p_{X,Y}(x, y) = P(X = x, Y = y)$

**Continuous:** Joint PDF $f_{X,Y}(x, y)$ where
$$P(X \in A, Y \in B) = \int_A \int_B f_{X,Y}(x, y) \, dy \, dx$$

### Marginal Distribution

Obtained by "summing out" the other variable:

**Discrete:**
$$p_X(x) = \sum_y p_{X,Y}(x, y)$$

**Continuous:**
$$f_X(x) = \int_{-\infty}^{\infty} f_{X,Y}(x, y) \, dy$$

---

## Independence and Correlation

### Independence

Random variables $X$ and $Y$ are **independent** if and only if:

$$P(X = x, Y = y) = P(X = x) \cdot P(Y = y)$$

or equivalently: $f_{X,Y}(x, y) = f_X(x) \cdot f_Y(y)$ for all $x, y$.

### IID Variables

**Independent and Identically Distributed (IID):** Random variables are drawn from the same distribution and are mutually independent.

> This is a common assumption for training data in ML.

### Covariance

**Covariance** measures linear relationship:

$$\text{Cov}(X, Y) = \mathbb{E}[(X - \mu_X)(Y - \mu_Y)] = \mathbb{E}[XY] - \mathbb{E}[X]\mathbb{E}[Y]$$

### Correlation Coefficient

**Correlation** is normalized covariance:

$$\text{Corr}(X, Y) = \frac{\text{Cov}(X, Y)}{\sigma_X \sigma_Y}$$

**Properties:**
- $-1 \leq \text{Corr}(X, Y) \leq 1$
- $\text{Corr}(X, Y) = 0$ means uncorrelated

### Independence vs. Uncorrelated

$$\text{Independent} \Rightarrow \text{Uncorrelated} \quad (\text{Corr}(X, Y) = 0)$$

> **However, the reverse is NOT generally true!** Uncorrelated variables can still be dependent.

---

## Summary Table: Common Distributions

| Distribution | Notation | Mean | Variance |
|-------------|----------|------|----------|
| Bernoulli | $\text{Ber}(\theta)$ | $\theta$ | $\theta(1-\theta)$ |
| Binomial | $\text{Bin}(n, \theta)$ | $n\theta$ | $n\theta(1-\theta)$ |
| Uniform | $\text{Unif}(a, b)$ | $\frac{a+b}{2}$ | $\frac{(b-a)^2}{12}$ |
| Gaussian | $\mathcal{N}(\mu, \sigma^2)$ | $\mu$ | $\sigma^2$ |

---

## Key Takeaways for ML

1. **Random variables** model uncertainty in data and predictions
2. **Bayes' rule** is the foundation for probabilistic inference
3. **IID assumption** simplifies learning theory
4. **Gaussian distribution** appears everywhere (central limit theorem)
5. **Independence ≠ uncorrelated** (a common pitfall!)
