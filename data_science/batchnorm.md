# Batch Normalization

---

## 1. Motivation: Why Normalize?

Neural networks **learn more efficiently** when the data (inputs and internal activations) are:
- **Centered** (mean ≈ 0)
- **Scaled** (variance ≈ 1)

Just like we normalize input features (standardization), we can normalize **hidden layer activations**.

---

## 2. Problem: Internal Covariate Shift (ICS)

### ICS = The distribution of activations in hidden layers **shifts** during training.

- As the weights change, each layer receives different distributions over time.
- This makes training slower and less stable.
- Especially problematic in deep networks (can cause vanishing gradients or saturation).

---

### Example: Hidden Layer Activations Over Time

Let $H^{(l)}$ be the activation of hidden layer $l$:
- Batch 1 → $\mathcal{N}(0, 1)$
- Batch 2 → $\mathcal{N}(1, 2)$
- Batch 3 → $\mathcal{N}(3, 5)$

This makes optimization harder — we keep “chasing” a moving target.

---

## 3. The Fix: Normalize Activations per Layer

For a hidden layer:
- Given $K$ neurons and a batch of $N$ data points
- Let $H_{ik}$ be the activation of the $k$-th neuron on the $i$-th example

---

### Step 1: Compute Batch Mean for each neuron $k$
$$
\mu_k = \frac{1}{N} \sum_{i=1}^N H_{ik}
$$

### Step 2: Compute Batch Variance
$$
\sigma_k^2 = \frac{1}{N} \sum_{i=1}^N (H_{ik} - \mu_k)^2
$$

(Add small constant $\delta$ for numerical stability)

---

### Step 3: Normalize the Activations
$$
\hat{H}_{ik} = \frac{H_{ik} - \mu_k}{\sqrt{\sigma_k^2 + \delta}}
$$

Now $\hat{H}_{ik}$ has mean 0 and variance 1 — per feature/neuron.

---

## 4. Problem: Normalizing Kills Representational Power

If all hidden activations are squashed to mean 0 and var 1, how can the model learn?

### Solution: Add Learnable Parameters
$$
H''_{ik} = \gamma_k \hat{H}_{ik} + \beta_k
$$

- $\gamma_k$: scale parameter
- $\beta_k$: shift parameter

This allows the model to **learn back any distribution it wants**, while still getting the benefits of normalization.

---

## 5. Where Do We Apply BatchNorm?

Usually:
- **Before activation**

Why?
- At that point, $W a^{(l-1)} + b$ is still a linear transformation, with nice (symmetric) distribution.
- Normalizing it leads to **stable activations** downstream.

---

### Notation Summary:
- $\hat{H}_{ik}$: normalized activation
- $H''_{ik}$: transformed activation with learnable scale/shift
- $H_{ik}$: original activation (pre-norm)

---

## 6. Evaluation (Test Time)

During inference, you no longer have a batch of activations — so you can't compute $\mu_k$ and $\sigma_k^2$ on the fly.

### Solution:
- Track **running averages** during training:
  $$
  \mu_k^{\text{global}} = \rho \mu_k^{\text{global}} + (1 - \rho)\mu_k^{\text{batch}}
  $$
  $$
  \sigma_k^{\text{global}} = \rho \sigma_k^{\text{global}} + (1 - \rho)\sigma_k^{\text{batch}}
  $$

Use these global statistics during test time:
$$
\hat{H}_{k}^{\text{test}} = \frac{H_k - \mu_k^{\text{global}}}{\sqrt{\sigma_k^{\text{global}} + \delta}}
$$

---

## 7. Summary of Training vs. Inference

| Step               | Training Time                        | Inference Time                          |
|--------------------|---------------------------------------|------------------------------------------|
| Compute mean/var   | Per mini-batch                        | Use running average                      |
| Normalize          | Batch stats                           | Global stats                             |
| Learnable params   | $\gamma_k, \beta_k$ updated via backprop | Used as-is to transform activations     |

---

## 8. Benefits of BatchNorm

| Benefit                        | Why It Helps                              |
|-------------------------------|--------------------------------------------|
| **Stabilizes learning**       | Keeps gradients from vanishing/exploding   |
| **Reduces internal covariate shift** | Makes learning smoother                 |
| **Enables higher learning rates** | Training becomes more robust             |
| **Acts as regularizer**       | Adds noise through batch estimates         |
| **Improves generalization**   | Even without dropout                       |

---

## 9. TL;DR Equations

### During Training:

Given mini-batch $\{H_{ik}\}$:

1. $\mu_k = \frac{1}{N} \sum_i H_{ik}$  
2. $\sigma_k^2 = \frac{1}{N} \sum_i (H_{ik} - \mu_k)^2$  
3. $\hat{H}_{ik} = \frac{H_{ik} - \mu_k}{\sqrt{\sigma_k^2 + \delta}}$  
4. $H''_{ik} = \gamma_k \hat{H}_{ik} + \beta_k$

---