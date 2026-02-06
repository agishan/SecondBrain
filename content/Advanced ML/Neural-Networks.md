# Feed-Forward Neural Networks

**Course:** MSCI 546 - Advanced Machine Learning
**Instructor:** Sirisha Rambhatla, University of Waterloo

**Course Overview:** [[00-Course-Overview]]

---

## Related Topics
- **Prerequisites:** [[Linear-Algebra#Matrix Calculus]], [[Optimization-for-ML#Stochastic Gradient Descent (SGD)|SGD]]
- **Linear models:** [[Linear-Regression]], [[Logistic-Regression]]
- **Probabilistic view:** [[Statistics-MLE]]

---

## Motivation: Why Neural Networks?

### Linear Models Are Not Always Adequate

Linear models ($f(\boldsymbol{x}) = \boldsymbol{w}^\top \boldsymbol{x}$) cannot capture nonlinear patterns:

```
    Linearly Separable          NOT Linearly Separable (XOR)

         • •                           •       •
        • • •                            ╲   ╱
       ─────────                          ╲ ╱
        ○ ○                                ╳
         ○ ○ ○                            ╱ ╲
                                        ○       ○
```

### The Solution: Nonlinear Mappings

Transform features using a nonlinear mapping:

$$\phi(\boldsymbol{x}): \mathbb{R}^D \to \mathbb{R}^M$$

Then apply a linear model in the transformed space:

$$f(\boldsymbol{x}) = \boldsymbol{w}^\top \phi(\boldsymbol{x})$$

**Key question:** What nonlinear mapping $\phi$ should we use?

**Neural networks:** Learn $\phi$ from data!

---

## From Linear Models to Neural Networks

### Linear Model as a One-Layer Network

```
    x₁ ───w₁───╲
                ╲
    x₂ ───w₂────○──── o = h(w^T x)
                ╱
    x₃ ───w₃───╱
```

With $h(a) = a$ (identity), this is linear regression.

### Adding Nonlinearity

Replace the identity with a nonlinear **activation function** $h$:

| Activation | Formula | Range |
|------------|---------|-------|
| **ReLU** | $h(a) = \max(0, a)$ | $[0, \infty)$ |
| **Sigmoid** | $h(a) = \frac{1}{1+e^{-a}}$ | $(0, 1)$ |
| **Tanh** | $h(a) = \frac{e^a - e^{-a}}{e^a + e^{-a}}$ | $(-1, 1)$ |

---

## Network Architecture

### Multiple Output Nodes

```
         x₁ ──────╲
                   ○ ── o₁
         x₂ ──────╳○ ── o₂
                   ○ ── o₃
         x₃ ──────╱○ ── o₄

              W ∈ ℝ^{4×3}
```

$$\boldsymbol{o} = h(\boldsymbol{W}\boldsymbol{x})$$

where $h$ is applied element-wise.

This creates a **nonlinear basis**: $\Phi(\boldsymbol{x}) = h(\boldsymbol{W}\boldsymbol{x})$

### Multiple Layers

```
    Input      Hidden 1    Hidden 2    Output
    Layer      Layer       Layer       Layer

     ○ ─────── ○ ───────── ○ ───────── ○
     ○ ─────── ○ ───────── ○
     ○ ─────── ○ ───────── ○
     ○ ─────── ○
```

**Terminology:**
- Each node is called a **neuron**
- $h$ is the **activation function**
- One neuron per layer can have $h(a) = 1$ to incorporate **bias**
- Output layer often uses $h(a) = a$ (identity)
- **#layers** refers to hidden layers (plus 1 or 2 for input/output)
- **Deep neural nets** have many layers and millions of parameters
- This is a **feedforward, fully connected** architecture

---

## Mathematical Formulation

### Recursive Definition

An L-layer neural net:

$$f(\boldsymbol{x}) = h_L(\boldsymbol{W}_L h_{L-1}(\boldsymbol{W}_{L-1} \cdots h_1(\boldsymbol{W}_1 \boldsymbol{x})))$$

### Layer-by-Layer Notation

For input $\boldsymbol{x}$, define recursively:

$$\boldsymbol{o}_0 = \boldsymbol{x}$$
$$\boldsymbol{a}_\ell = \boldsymbol{W}_\ell \boldsymbol{o}_{\ell-1}$$
$$\boldsymbol{o}_\ell = h_\ell(\boldsymbol{a}_\ell)$$

for $\ell = 1, \ldots, L$.

### Notation Summary

| Symbol | Meaning |
|--------|---------|
| $\boldsymbol{W}_\ell \in \mathbb{R}^{D_\ell \times D_{\ell-1}}$ | Weights between layer $\ell-1$ and $\ell$ |
| $D_0 = D, D_1, \ldots, D_L$ | Number of neurons at each layer |
| $\boldsymbol{a}_\ell \in \mathbb{R}^{D_\ell}$ | Input (pre-activation) to layer $\ell$ |
| $\boldsymbol{o}_\ell \in \mathbb{R}^{D_\ell}$ | Output of layer $\ell$ |
| $h_\ell: \mathbb{R}^{D_\ell} \to \mathbb{R}^{D_\ell}$ | Activation function at layer $\ell$ |

---

## Learning the Model

### Objective

No matter how complicated the model, the goal is the same: minimize average loss.

$$\mathcal{E}(\boldsymbol{W}_1, \ldots, \boldsymbol{W}_L) = \frac{1}{N} \sum_{n=1}^{N} \mathcal{E}_n(\boldsymbol{W}_1, \ldots, \boldsymbol{W}_L)$$

### Loss Functions

| Task | Loss Function |
|------|---------------|
| Regression | $\mathcal{E}_n = \|f(\boldsymbol{x}_n) - y_n\|_2^2$ |
| Classification | $\mathcal{E}_n = \ln\left(1 + \sum_{k \neq y_n} e^{f(\boldsymbol{x}_n)_k - f(\boldsymbol{x}_n)_{y_n}}\right)$ |

### The Challenge

The function $f(\cdot)$ is a composition of nonlinear functions → generally **non-convex**!

We use **Stochastic Gradient Descent (SGD)** (see [[Optimization-for-ML#Stochastic Gradient Descent (SGD)]]).

---

## Backpropagation

**Backpropagation** (backprop) is an efficient algorithm to compute gradients in neural networks using the **chain rule**.

### The Problem

We need gradients with respect to all weights:
$$\frac{\partial \mathcal{E}_n}{\partial \boldsymbol{W}_\ell} \quad \text{for } \ell = 1, \ldots, L$$

### Key Insight: Chain Rule

Recall from [[Linear-Algebra#Matrix Calculus]]:
$$\frac{d}{dx} f(u(x)) = \frac{df}{du} \cdot \frac{du}{dx}$$

For neural networks, we chain through all the layers.

### Forward Propagation

Compute outputs layer by layer:

```
x → a₁ → o₁ → a₂ → o₂ → ... → aL → oL → Loss
```

1. $\boldsymbol{o}_0 = \boldsymbol{x}_n$
2. For $\ell = 1, \ldots, L$:
   - $\boldsymbol{a}_\ell = \boldsymbol{W}_\ell \boldsymbol{o}_{\ell-1}$
   - $\boldsymbol{o}_\ell = h_\ell(\boldsymbol{a}_\ell)$

### Backward Propagation

Compute gradients from output to input:

```
∂E/∂oL → ∂E/∂aL → ∂E/∂oL-1 → ... → ∂E/∂a1 → ∂E/∂W1
```

### Gradient Computation

For weight $w_{\ell,ij}$ (connecting node $j$ in layer $\ell-1$ to node $i$ in layer $\ell$):

$$\frac{\partial \mathcal{E}_n}{\partial w_{\ell,ij}} = \frac{\partial \mathcal{E}_n}{\partial a_{\ell,i}} \cdot \frac{\partial a_{\ell,i}}{\partial w_{\ell,ij}} = \frac{\partial \mathcal{E}_n}{\partial a_{\ell,i}} \cdot o_{\ell-1,j}$$

### The Key Recursion

```
         o_{ℓ-1,j}
            │
           w_{ℓ,ij}
            │
            ▼
    ○ ──── (i) ──── ○
   j        │       k
           w_{ℓ+1,ki}
            │
            ▼
```

$$\frac{\partial \mathcal{E}_n}{\partial a_{\ell,i}} = \left( \sum_k \frac{\partial \mathcal{E}_n}{\partial a_{\ell+1,k}} w_{\ell+1,ki} \right) h'_\ell(a_{\ell,i})$$

### Matrix Form

$$\frac{\partial \mathcal{E}_n}{\partial \boldsymbol{W}_\ell} = \frac{\partial \mathcal{E}_n}{\partial \boldsymbol{a}_\ell} \boldsymbol{o}_{\ell-1}^\top$$

$$\frac{\partial \mathcal{E}_n}{\partial \boldsymbol{a}_\ell} = \begin{cases} \left( \boldsymbol{W}_{\ell+1}^\top \frac{\partial \mathcal{E}_n}{\partial \boldsymbol{a}_{\ell+1}} \right) \circ h'_\ell(\boldsymbol{a}_\ell) & \text{if } \ell < L \\ 2(h_L(\boldsymbol{a}_L) - y_n) \circ h'_L(\boldsymbol{a}_L) & \text{if } \ell = L \text{ (square loss)} \end{cases}$$

where $\circ$ denotes element-wise (Hadamard) product.

---

## Training Algorithm

### The Backpropagation Algorithm

**Initialize** $\boldsymbol{W}_1, \ldots, \boldsymbol{W}_L$ randomly.

**Repeat:**

1. **Pick** random data point $n \in [N]$

2. **Forward propagation:** For $\ell = 1, \ldots, L$:
   - Compute $\boldsymbol{a}_\ell = \boldsymbol{W}_\ell \boldsymbol{o}_{\ell-1}$
   - Compute $\boldsymbol{o}_\ell = h_\ell(\boldsymbol{a}_\ell)$

   (where $\boldsymbol{o}_0 = \boldsymbol{x}_n$)

3. **Backward propagation:** For $\ell = L, \ldots, 1$:
   - Compute $\frac{\partial \mathcal{E}_n}{\partial \boldsymbol{a}_\ell}$ using the recursion

4. **Update weights:** For $\ell = 1, \ldots, L$:
   $$\boldsymbol{W}_\ell \leftarrow \boldsymbol{W}_\ell - \eta \frac{\partial \mathcal{E}_n}{\partial \boldsymbol{W}_\ell} = \boldsymbol{W}_\ell - \eta \frac{\partial \mathcal{E}_n}{\partial \boldsymbol{a}_\ell} \boldsymbol{o}_{\ell-1}^\top$$

**Until** convergence.

---

## Practical Considerations

### Variants of SGD

| Method | Description |
|--------|-------------|
| **Mini-batch SGD** | Average gradient over a batch of examples |
| **SGD with momentum** | Add "velocity" to smooth updates |
| **Adam** | Adaptive learning rates per parameter |

See [[Optimization-for-ML#Variants and Extensions]] for details.

### Activation Function Choice

| Function | Pros | Cons |
|----------|------|------|
| **ReLU** | Fast, avoids vanishing gradient | "Dead neurons" (output always 0) |
| **Sigmoid** | Smooth, bounded | Vanishing gradient for large $|a|$ |
| **Tanh** | Zero-centered | Vanishing gradient |

**ReLU** is the most common choice for hidden layers.

### Weight Initialization

Poor initialization can lead to:
- Vanishing gradients (weights too small)
- Exploding gradients (weights too large)

Common strategies: Xavier initialization, He initialization.

---

## Summary

| Concept | Description |
|---------|-------------|
| **Neural network** | Composition of linear + nonlinear transformations |
| **Activation function** | Introduces nonlinearity (ReLU, sigmoid, tanh) |
| **Forward propagation** | Compute outputs layer by layer |
| **Backpropagation** | Compute gradients using chain rule |
| **SGD** | Update weights using stochastic gradients |
| **Loss function** | MSE (regression) or cross-entropy (classification) |

### Key Equations

| Step | Formula |
|------|---------|
| Forward | $\boldsymbol{a}_\ell = \boldsymbol{W}_\ell \boldsymbol{o}_{\ell-1}$, $\boldsymbol{o}_\ell = h_\ell(\boldsymbol{a}_\ell)$ |
| Backward | $\frac{\partial \mathcal{E}}{\partial \boldsymbol{a}_\ell} = (\boldsymbol{W}_{\ell+1}^\top \frac{\partial \mathcal{E}}{\partial \boldsymbol{a}_{\ell+1}}) \circ h'_\ell(\boldsymbol{a}_\ell)$ |
| Weight gradient | $\frac{\partial \mathcal{E}}{\partial \boldsymbol{W}_\ell} = \frac{\partial \mathcal{E}}{\partial \boldsymbol{a}_\ell} \boldsymbol{o}_{\ell-1}^\top$ |
| Update | $\boldsymbol{W}_\ell \leftarrow \boldsymbol{W}_\ell - \eta \frac{\partial \mathcal{E}}{\partial \boldsymbol{W}_\ell}$ |

---

## Reading

**PiML1:** Relevant sections of Chapter 13
