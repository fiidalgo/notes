# Gradient Descent

---

## 1. Motivation: Why Do We Need Gradient Descent?

Let’s say we have a **single-neuron model** (either regression or classification):

$$
\hat{y} = f(W^T X) = f(w_0 + w_1 x_1 + w_2 x_2 + \dots + w_d x_d)
$$

- $X \in \mathbb{R}^d$: input vector
- $W \in \mathbb{R}^d$: weights
- $f$: activation function (identity for regression, sigmoid for classification)

---

## 2. The Problem: Our Model Performs Poorly Initially

We initialize weights **randomly**, which usually means:
- Predictions are inaccurate
- The output of the neuron is **far from the correct label**
- The total loss is **high**

We need to **adjust weights $W$** to reduce the prediction error — that’s where **gradient descent** comes in.

---

## 3. Goal: Minimize the Loss Function

We define a loss function $\mathcal{L}(W)$ that measures how far our predictions are from the true outputs.

### Example: Binary Cross-Entropy for Logistic Regression

$$
\mathcal{L}(W) = \sum_{i=1}^n \left[ -y_i \log(p_i) - (1 - y_i)\log(1 - p_i) \right]
\quad \text{where } p_i = \frac{1}{1 + e^{-W^T X_i}}
$$

Our goal is to **find $W$ that minimizes $\mathcal{L}(W)$**.

---

## 4. Analogy: Finding the Minimum of a 1D Function

Suppose we only have **one weight** $w_1$. Our loss becomes a function of a single variable: $\mathcal{L}(w_1)$.

We want:
$$
\frac{d\mathcal{L}}{dw_1} = 0 \quad \text{(find stationary points)}
$$

But this derivative might not have a closed-form solution — so instead we **approximate** it using an iterative algorithm:

---

## 5. Gradient Descent: The Key Idea

### Take a step in the **opposite direction** of the gradient.

1. Initialize $w_1^{(0)}$ randomly
2. Repeat:
   $$
   w_1^{(t+1)} = w_1^{(t)} - \eta \frac{d\mathcal{L}}{dw_1}
   $$
   where $\eta$ is the **learning rate**

This can be generalized to vector weights:

### For $W \in \mathbb{R}^d$:
$$
W^{(t+1)} = W^{(t)} - \eta \nabla_W \mathcal{L}(W)
$$

- $\nabla_W \mathcal{L}(W)$ is the **gradient vector**:
  $$
  \nabla_W \mathcal{L}(W) = \left[
    \frac{\partial \mathcal{L}}{\partial w_1},
    \frac{\partial \mathcal{L}}{\partial w_2},
    \dots,
    \frac{\partial \mathcal{L}}{\partial w_d}
  \right]^T
  $$

---

## 6. Why Negative Gradient?

The gradient $\nabla \mathcal{L}(W)$ points in the direction of **steepest increase** of the loss. So we move **against** it to **minimize** the loss.

---

## 7. Deriving the Gradient for Logistic Regression (Step-by-Step)

Suppose:

$$
p_i = \frac{1}{1 + e^{-W^T X_i}} \quad \text{(sigmoid activation)}
$$

### Loss for one data point:
$$
\mathcal{L}_i(W) = -y_i \log(p_i) - (1 - y_i)\log(1 - p_i)
$$

Let’s derive $\frac{d\mathcal{L}_i}{dW}$ by breaking into chain rule steps.

---

### Step-by-Step Chain Rule Breakdown

Let:
- $\xi_1 = -W^T X_i$ ⇒ $\frac{d\xi_1}{dW} = -X_i$
- $\xi_2 = e^{\xi_1}$ ⇒ $\frac{d\xi_2}{d\xi_1} = e^{\xi_1}$
- $\xi_3 = 1 + \xi_2$ ⇒ $\frac{d\xi_3}{d\xi_2} = 1$
- $\xi_4 = \frac{1}{\xi_3} = \frac{1}{1 + e^{\xi_1}}$ ⇒ $\frac{d\xi_4}{d\xi_3} = -\frac{1}{\xi_3^2}$
- $\xi_5 = \log(\xi_4)$ ⇒ $\frac{d\xi_5}{d\xi_4} = \frac{1}{\xi_4}$
- $\mathcal{L}_i^A = -y_i \xi_5$ ⇒ $\frac{d\mathcal{L}_i^A}{d\xi_5} = -y_i$

Use chain rule:
$$
\frac{d\mathcal{L}_i^A}{dW} = \frac{d\mathcal{L}_i^A}{d\xi_5}
\cdot \frac{d\xi_5}{d\xi_4} \cdot \frac{d\xi_4}{d\xi_3} \cdot \frac{d\xi_3}{d\xi_2} \cdot \frac{d\xi_2}{d\xi_1} \cdot \frac{d\xi_1}{dW}
$$

Substituting:
$$
\frac{d\mathcal{L}_i^A}{dW} = -y_i \cdot \frac{1}{\xi_4} \cdot \left(-\frac{1}{\xi_3^2}\right) \cdot 1 \cdot e^{\xi_1} \cdot (-X_i)
$$

With simplification, this becomes:
$$
\frac{d\mathcal{L}_i^A}{dW} = -y_i \cdot X_i \cdot \frac{e^{-W^T X_i}}{1 + e^{-W^T X_i}}
$$

---

### Now do the same for the second term:
$$
\mathcal{L}_i^B = -(1 - y_i) \log(1 - p_i)
$$

Repeat the chain rule steps to get:
$$
\frac{d\mathcal{L}_i^B}{dW} = (1 - y_i) \cdot X_i \cdot \frac{1}{1 + e^{-W^T X_i}}
$$

---

### Combine:
Total gradient for one example:
$$
\frac{d\mathcal{L}_i}{dW} = (p_i - y_i) X_i
$$

Then:
$$
\nabla_W \mathcal{L}(W) = \sum_{i=1}^n (p_i - y_i) X_i
$$

---

## 8. Final Gradient Descent Update Rule

Each iteration:
$$
W^{(t+1)} = W^{(t)} - \eta \sum_{i=1}^n (p_i - y_i) X_i
$$

---

## 9. Learning Rate $\eta$ Considerations

| Case          | Effect                                                                 |
|---------------|------------------------------------------------------------------------|
| $\eta$ too small | Very slow convergence                                                |
| $\eta$ too large | Divergence or oscillation                                            |
| $\eta$ just right | Smooth convergence to local minimum                                 |

We usually visualize the **loss vs iteration** (called a **trace plot**) to inspect convergence.

---

## 10. Local Minima

- Gradient descent may find a **local minimum**, not necessarily the **global minimum**
- This is especially true in **non-convex** problems (e.g., neural networks)
- Convex problems (e.g., linear regression) guarantee a global minimum

### Strategies:
- Try different **random initializations**
- Use **momentum**, **learning rate scheduling**, or **adaptive optimizers** (Adam, RMSprop)
- Add **noise** or regularization to escape shallow local minima

---

## 11. Summary

| Concept                  | Key Point                                                  |
|--------------------------|------------------------------------------------------------|
| Gradient                 | Vector of partial derivatives                              |
| Gradient Descent         | Move opposite to the gradient to minimize the loss         |
| Update Rule              | $W_{new} = W_{old} - \eta \nabla_W \mathcal{L}(W)$         |
| Logistic Regression Grad | $(p_i - y_i) X_i$                                          |
| Learning Rate            | Hyperparameter; must be tuned carefully                    |
| Local Minima             | A common challenge in non-convex optimization              |

---