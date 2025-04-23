# Bias Corrections in Adam

---

## 1. Problem: Initialization Bias in Momentum Estimate

Adam uses **exponentially weighted moving averages** to track:

- The **first moment** (mean):  
  $$
  v_t = \alpha v_{t-1} + (1 - \alpha) g_t
  $$

But at $t = 1$, we initialize:
$$
v_0 = 0
$$

So:
$$
v_1 = (1 - \alpha) g_1
$$

This is **biased toward 0** — we are **underestimating** the true first moment at early steps.

---

## 2. Step-by-Step Derivation of the Bias

We want to understand how this bias emerges and how to correct for it.

---

### Step 1: What is the "stationary value" of $v_t$?

Assume $g_t$ is a stationary process (same distribution over time), then:
$$
v_t = \alpha v_{t-1} + (1 - \alpha) g_t
$$

Take expectation:
$$
\mathbb{E}[v_t] = \alpha \mathbb{E}[v_{t-1}] + (1 - \alpha) \mathbb{E}[g_t]
$$

Solve for $\mathbb{E}[v_t]$ in steady state:
$$
\mathbb{E}[v] = \alpha \mathbb{E}[v] + (1 - \alpha)\mathbb{E}[g]
\quad \Rightarrow \quad
(1 - \alpha)\mathbb{E}[v] = (1 - \alpha)\mathbb{E}[g]
$$

So:
$$
\boxed{\mathbb{E}[v] = \mathbb{E}[g]}
$$

This tells us: **in expectation, the exponential moving average tracks the true mean** — but only **after convergence**.

---

### Step 2: Why is there bias at early iterations?

At $t = 1$:
$$
v_1 = \alpha \cdot 0 + (1 - \alpha) g_1 = (1 - \alpha) g_1
$$

So:
$$
\mathbb{E}[v_1] = (1 - \alpha) \mathbb{E}[g_1] \neq \mathbb{E}[g]
$$

The expected value of $v_1$ is too small — **it underestimates the true gradient mean**.

---

### Step 3: Generalizing the Bias

We assume:
$$
\mathbb{E}[v_t] = (1 - \alpha^t) \mathbb{E}[g_t]
$$

We verify by induction:

#### Base case ($t = 1$):
$$
\mathbb{E}[v_1] = (1 - \alpha) \mathbb{E}[g_1] = (1 - \alpha^1)\mathbb{E}[g_1]
\quad \text{✓}
$$

#### Inductive step:
Assume:
$$
\mathbb{E}[v_t] = (1 - \alpha^t) \mathbb{E}[g_t]
$$

Then:
$$
v_{t+1} = \alpha v_t + (1 - \alpha) g_{t+1}
$$
Take expectation:
$$
\mathbb{E}[v_{t+1}] = \alpha \mathbb{E}[v_t] + (1 - \alpha) \mathbb{E}[g]
= \alpha (1 - \alpha^t)\mathbb{E}[g] + (1 - \alpha)\mathbb{E}[g]
= \left[ \alpha (1 - \alpha^t) + (1 - \alpha) \right]\mathbb{E}[g]
= (1 - \alpha^{t+1}) \mathbb{E}[g]
$$

So by induction:
$$
\boxed{
\mathbb{E}[v_t] = (1 - \alpha^t) \mathbb{E}[g]
}
$$

---

## 3. The Fix: Bias Correction

Since we know the bias is:
$$
\mathbb{E}[v_t] = (1 - \alpha^t) \mathbb{E}[g]
$$

We correct it by dividing out the bias term:
$$
\boxed{
\hat{v}_t = \frac{v_t}{1 - \alpha^t}
}
$$

This gives us an **unbiased estimate** of the first moment.

> This is called **bias correction** and is standard in Adam and similar optimizers.

---

## 4. Summary: What You Do in Adam

For each parameter $j$:

### Without correction:
- First moment estimate:  
  $v_j^{(t)} = \rho_1 v_j^{(t-1)} + (1 - \rho_1) g_j^{(t)}$

- Second moment estimate:
  $r_j^{(t)} = \rho_2 r_j^{(t-1)} + (1 - \rho_2) (g_j^{(t)})^2$

### With bias correction:
- $\hat{v}_j^{(t)} = \dfrac{v_j^{(t)}}{1 - \rho_1^t}$
- $\hat{r}_j^{(t)} = \dfrac{r_j^{(t)}}{1 - \rho_2^t}$

### Final update:
$$
W_j^{(t+1)} = W_j^{(t)} - \frac{\epsilon}{\sqrt{\hat{r}_j^{(t)} + \delta}} \hat{v}_j^{(t)}
$$

---

## 5. Why This Matters

- Early updates in Adam without correction **underestimate momentum**
- Fixing this ensures that Adam behaves well **even in early training**
- **PyTorch and TensorFlow both implement this bias correction**

---
