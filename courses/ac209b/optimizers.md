# Optimizers

---

## 1. Challenges in Optimization

Before we get to optimizers, here’s what makes training neural networks hard:

### A. Local Minima & Saddle Points
- We want to reach the **global minimum**, but we often get stuck in:
  - **Local minima**: acceptable if close enough
  - **Saddle points**: very common in high-dimensional spaces

At saddle points, the **gradient is close to zero**, so gradient descent slows dramatically.

---

### B. Exploding Gradients
- Gradient values become very large, often during backpropagation through deep networks
- Solution: **Gradient clipping**
  $$
  \nabla \mathcal{L}(W) \gets \text{sign}(\nabla \mathcal{L}(W)) \cdot u
  $$
  where $u$ is a predefined threshold.

---

### C. Vanishing Gradients
- Opposite of exploding gradients: derivatives shrink as we go backward
- Especially bad with **sigmoid/tanh activations**
- Gradients vanish → earlier layers stop learning

---

### D. Poor Conditioning
- In some directions, the loss is steep; in others, it’s flat (a narrow valley)
- Gradient descent oscillates in steep directions and barely progresses in flat ones
- We need **direction-sensitive learning rates**

---

## 2. Momentum

### Core Idea:
Use the **past gradients** to smooth out the updates.

Instead of:
$$
W_{t+1} = W_t - \eta \nabla \mathcal{L}_t
$$

Use:
$$
v_t = \alpha v_{t-1} + (1 - \alpha) \nabla \mathcal{L}_t \\
W_{t+1} = W_t - \eta v_t
$$

Where:
- $v_t$: velocity vector (smoothed gradient)
- $\alpha \in [0.9, 0.99]$: how much momentum to retain
- $\eta$: learning rate

---

### Why Momentum Works:
- Dampens **oscillations** in sharp directions (e.g., vertical)
- Accelerates movement in **flat directions**
- Helps escape **saddle points** by adding inertia

> Analogy: a ball rolling downhill gains speed, rather than constantly restarting.

---

## 3. Adaptive Learning Rates

We might want **different learning rates for each parameter**, especially if:
- Some weights learn faster than others
- Some gradients are consistently small or large

---

## 4. AdaGrad

Tracks **cumulative squared gradients** for each parameter:
$$
r_j^{(t)} = r_j^{(t-1)} + [g_j^{(t)}]^2
$$

Then update:
$$
W_j^{(t+1)} = W_j^{(t)} - \frac{\epsilon}{\sqrt{\delta + r_j^{(t)}}} g_j^{(t)}
$$

- $\epsilon$: base learning rate
- $\delta$: small constant to prevent divide-by-zero

### Effect:
- If $r_j$ grows large, the effective learning rate **shrinks**
- Adapts step size for each parameter

### Problem:
- Learning rate may **decay too aggressively**
- Slows down too much over time

---

## 5. RMSProp (Root Mean Square Propagation)

A refinement of AdaGrad:
- Instead of summing squared gradients, take **exponentially weighted average**:
  $$
  r_j^{(t)} = \rho \cdot r_j^{(t-1)} + (1 - \rho) \cdot [g_j^{(t)}]^2
  $$
- Then update:
  $$
  W_j^{(t+1)} = W_j^{(t)} - \frac{\epsilon}{\sqrt{\delta + r_j^{(t)}}} g_j^{(t)}
  $$

Typical values:
- $\rho = 0.9$
- $\delta = 10^{-8}$

> RMSProp prevents the learning rate from collapsing too early, unlike AdaGrad.

---

## 6. Adam: Adaptive Moment Estimation

Adam combines **Momentum + RMSProp**:

- **First moment estimate** (like momentum):
  $$
  v_j^{(t)} = \rho_1 \cdot v_j^{(t-1)} + (1 - \rho_1) \cdot g_j^{(t)}
  $$
- **Second moment estimate** (like RMSProp):
  $$
  r_j^{(t)} = \rho_2 \cdot r_j^{(t-1)} + (1 - \rho_2) \cdot [g_j^{(t)}]^2
  $$

Then correct for bias:
$$
\hat{v}_j^{(t)} = \frac{v_j^{(t)}}{1 - \rho_1^t}, \quad \hat{r}_j^{(t)} = \frac{r_j^{(t)}}{1 - \rho_2^t}
$$

Update rule:
$$
W_j^{(t+1)} = W_j^{(t)} - \frac{\epsilon}{\sqrt{\hat{r}_j^{(t)} + \delta}} \hat{v}_j^{(t)}
$$

### Typical values:
- $\rho_1 = 0.9$ (PyTorch: $\beta_1$)
- $\rho_2 = 0.999$ (PyTorch: $\beta_2$)
- $\epsilon = 10^{-3}$ or $10^{-4}$

---

## 7. Summary Table of Optimizers

| Optimizer     | Adaptive? | Momentum? | Good For                         | Downsides                      |
|---------------|-----------|-----------|----------------------------------|--------------------------------|
| **SGD**       | ❌        | ❌        | Simplicity                       | Can be slow / oscillatory     |
| **Momentum**  | ❌        | ✅        | Faster convergence               | Needs tuning                  |
| **AdaGrad**   | ✅        | ❌        | Sparse data, fast start          | Learning rate shrinks too much|
| **RMSProp**   | ✅        | ❌        | Non-stationary problems          | Needs decay tuning            |
| **Adam**      | ✅        | ✅        | Works well out-of-the-box        | Slightly more compute/memory  |

---

## 8. Visual Summary

| Phenomenon                 | Solved By         |
|----------------------------|-------------------|
| Saddle points              | Momentum, Adam    |
| Sharp ravines / valleys    | RMSProp, Adam     |
| Vanishing gradients        | Careful activations + Optimizers |
| Poor conditioning          | Adaptive learning rate methods   |

---