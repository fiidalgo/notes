# Backpropagation

---

## 1. Why Do We Need Backpropagation?

Backpropagation is how we **compute the gradient of the loss** with respect to every weight in a neural network, efficiently.

### Our Goal:
Given:
- Inputs $X$
- Target $y$
- Network with weights $W$

We want to:
$$
\frac{\partial \mathcal{L}(W)}{\partial W} \quad \text{so we can update } W \text{ using gradient descent}
$$

---

## 2. Recap: Chain Rule for Derivatives

To compute a derivative like $\frac{d\mathcal{L}}{dW}$ for a network like:
$$
x \to h = W^T x \to p = \sigma(h) \to \mathcal{L}(p, y)
$$

We apply the **chain rule**:
$$
\frac{d\mathcal{L}}{dW} =
\frac{d\mathcal{L}}{dp} \cdot \frac{dp}{dh} \cdot \frac{dh}{dW}
$$

If we break each layer into a function $\xi_i$, we can track how data and gradients flow **forward and backward**.

---

## 3. Worked Example: Logistic Regression

### Forward Pass

Let:
- $h = W^T x$
- $p = \sigma(h) = \frac{1}{1 + e^{-h}}$
- $\mathcal{L}(p, y) = -y \log(p) - (1 - y)\log(1 - p)$

---

### Step 1: Derivatives of BCE Loss w.r.t. $p$
$$
\frac{\partial \mathcal{L}}{\partial p} = -\frac{y}{p} + \frac{1 - y}{1 - p}
$$

---

### Step 2: Derivative of $p$ w.r.t. $h$
Recall:
$$
p = \sigma(h) = \frac{1}{1 + e^{-h}} \quad \Rightarrow \quad \frac{dp}{dh} = p(1 - p)
$$

---

### Step 3: Derivative of $h$ w.r.t. $W$
$$
h = W^T x \quad \Rightarrow \quad \frac{\partial h}{\partial W} = x
$$

---

### Full Gradient:
Putting it together:
$$
\frac{\partial \mathcal{L}}{\partial W} = \left( \frac{\partial \mathcal{L}}{\partial p} \cdot \frac{dp}{dh} \right) \cdot x
$$

---

### Simplified Gradient:
You’ll eventually find:
$$
\frac{\partial \mathcal{L}}{\partial W} = (p - y) x
$$

This is the same gradient used in SGD and is extremely efficient to compute.

---

## 4. Why Backprop?

Instead of manually deriving gradients for every model:
- Backprop builds a **computational graph**
- Applies **chain rule backward through the graph**
- Reuses partial derivatives via **dynamic programming**

---

## 5. Manual Forward + Backward Example (Evaluated)

Let:
- $x = 3$
- $W = 3$
- $y = 1$

We compute:
1. $\xi_1 = -W \cdot x = -9$
2. $\xi_2 = e^{\xi_1} = e^{-9}$
3. $\xi_3 = 1 + \xi_2$
4. $\xi_4 = 1/\xi_3 = p$
5. $\xi_5 = \log(\xi_4)$
6. $\mathcal{L}_i = -y \cdot \xi_5$

Then compute:
- $\frac{d\mathcal{L}_i}{dW}$ by applying the chain rule backwards through each step
- Final result: approx **–0.00037018** for this example

---

## 6. Forward Mode vs. Reverse Mode

| Mode         | Best When                     | Description                          |
|--------------|-------------------------------|--------------------------------------|
| Forward Mode | Few inputs, many outputs ($m > n$) | Computes derivatives one at a time  |
| Reverse Mode | Many inputs, few outputs ($m < n$) | Stores all intermediate results, then works backward (used in backprop) |

> For neural nets, we typically have **many weights (inputs)** and one scalar loss (output), so **reverse mode** is ideal.

---

## 7. Auto-Differentiation & Computational Graphs

In frameworks like PyTorch/TensorFlow:

- Each node = operation (e.g., $\exp$, $\log$)
- Each edge = variable (e.g., output of a neuron)
- Each node stores both:
  - Value during **forward pass**
  - Derivative function during **backward pass**

### Example Computational Graph:
```
x → h = W^T x → σ(h) = p → log(p) → L
```

Each node has:
- `forward()` = compute value
- `backward()` = compute gradient via chain rule

---

## 8. Python-style Pseudocode for Backprop (1 neuron)

```python
def sigmoid(x):
    return 1 / (1 + np.exp(-x))

def d_sigmoid(x):
    s = sigmoid(x)
    return s * (1 - s)

def forward(X, W):
    h = np.dot(W, X)
    p = sigmoid(h)
    return h, p

def loss(p, y):
    return -y * np.log(p) - (1 - y) * np.log(1 - p)

def grad(X, y, W):
    h, p = forward(X, W)
    dL_dp = -y / p + (1 - y) / (1 - p)
    dp_dh = p * (1 - p)
    dL_dh = dL_dp * dp_dh
    dL_dW = dL_dh * X
    return dL_dW
```

---

## 9. Summary

| Concept                 | Key Takeaways                                                   |
|-------------------------|------------------------------------------------------------------|
| Chain Rule              | Enables composition of gradients through layers                  |
| Backpropagation         | Efficient use of the chain rule across a computational graph     |
| Forward vs Reverse Mode | Reverse is used in practice due to many inputs, one output       |
| Computational Graph     | Tracks both value and derivative at each step                   |
| Autograd Libraries      | Automate backprop via dynamic graph construction (e.g., PyTorch) |

---