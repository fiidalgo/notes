# Weight Initialization

---

## 1. Why Initialization Matters

Neural networks are **sensitive to the scale** and structure of their weight initialization.

### Goals of Initialization:
- **Break symmetry**: Units must start differently to learn different features
- **Ensure good signal propagation**: Avoid exploding or vanishing gradients
- **Preserve variance**: Inputs and gradients should not shrink or explode through layers

---

### The Problem with Bad Initialization:

| Initialization Scale | Effect                           |
|----------------------|----------------------------------|
| Too small            | Gradients vanish → no learning   |
| Too large            | Activations or gradients explode |
| All weights same     | All units learn the same thing   |

---

## 2. Symmetry Breaking

All units in a layer must start with **different random weights**. Otherwise:
- They compute identical outputs
- Gradients are identical
- No learning → redundancy

### Recommendation:
- Randomize **weights**
- Initialize **biases separately** (often zero or small positive values)

---

## 3. Xavier (Glorot) Initialization

### Goal:
Ensure that **variance of activations** and **variance of gradients** stay constant across layers.

Let a fully connected layer have:
- $m$ inputs (fan-in)
- $n$ outputs (fan-out)

### Xavier Initialization (for tanh/sigmoid activations):

- If weights $W_{ij} \sim \mathcal{N}\left(0, \frac{1}{m}\right)$
  then:
  $$
  \text{Var}[W_{ij} x_j] = \frac{1}{m}
  $$

### Why this works:
- Input variance preserved: prevents signals from shrinking
- Gradient variance preserved: helps learning proceed layer by layer

---

## 4. Kaiming (He) Initialization

### For ReLU or ReLU-like activations

ReLU is not symmetric and "kills" half the activations ($\max(0, x)$), so we need **larger variance** to compensate.

### He Initialization:
- $W_{ij} \sim \mathcal{N}\left(0, \frac{2}{m}\right)$

If using a uniform distribution:
- $W_{ij} \sim \mathcal{U}\left[-\sqrt{\frac{6}{m}}, \sqrt{\frac{6}{m}}\right]$

### Why $\frac{2}{m}$?
- ReLU discards ~50% of values (negative ones), so we scale up the remaining ones to maintain the variance.

---

## 5. Generalized Xavier/He (Fan-In/Fan-Out)

Let:
- $m$: fan-in (inputs)
- $n$: fan-out (outputs)

Then a general uniform initializer:
$$
W_{ij} \sim \mathcal{U}\left[-\sqrt{\frac{6}{m+n}}, \sqrt{\frac{6}{m+n}}\right]
$$

This balances **forward and backward variance**.

---

## 6. Bias Initialization

### Output Layer Bias:
- Set using **marginal statistics of the output** (e.g., log-odds)

### Hidden Layer Bias:
- Set to **small positive number** (e.g., 0.001) to:
  - Prevent **dead ReLU units**
  - Ensure some gradient flows initially

> For ReLU: if bias = 0, and $W^T x < 0$, the neuron never fires → gradient is zero forever.

---

## 7. Empirical Example (from the slides)

Task: Fit synthetic data $y = x \sin(x) + \epsilon$ using a 3-layer network with 100 units/layer (activation = tanh)

### Scenario A: $W \sim \mathcal{U}[-1, 1]$
- Works well — training proceeds

### Scenario B: $W \sim \mathcal{U}[-5, 5]$
- Network outputs saturate
- Gradients vanish beyond $x = 1$
- Model fails to learn properly in that region

---

## 8. Summary Table

| Method              | Use Case               | Distribution                  | Variance     |
|---------------------|------------------------|--------------------------------|--------------|
| Xavier (Glorot)     | tanh/sigmoid networks  | $\mathcal{N}(0, \frac{1}{m})$  | $\frac{1}{m}$|
| He (Kaiming)        | ReLU networks          | $\mathcal{N}(0, \frac{2}{m})$  | $\frac{2}{m}$|
| Uniform Generalized | Any activation         | $\mathcal{U}[-\sqrt{6/(m+n)}, \sqrt{6/(m+n)}]$ | $\sim \frac{2}{m+n}$ |

---