# Dropout

---

## 1. Motivation: What Problem is Dropout Solving?

### Overfitting via Co-Adaptation

Even with L1/L2 regularization, deep neural networks can **overfit** by developing **co-adapted units**:
- Neurons "collaborate" tightly to memorize the training data
- Each weight might remain small (so norm penalties don’t help), but the **combined behavior** overfits

---

### Co-Adaptation Defined:

> Co-adaptation occurs when multiple hidden units **rely on each other’s outputs** to make predictions, reducing robustness to variation.

---

## 2. Dropout: The Core Idea

### Dropout randomly deactivates neurons during training.

At each training iteration:
- For each hidden (and optionally input) neuron:
  - With probability $p$, **keep** the neuron
  - With probability $1-p$, **drop** the neuron (set output = 0)
- This results in **different network substructures** being trained at each step

#### Analogy:
- Like training a **large ensemble** of subnetworks that all share weights

---

## 3. Dropout in Training

Let:
- $h_i$ be the activation of neuron $i$
- $\mu_i \sim \text{Bernoulli}(p)$

Then during training:
$$
\tilde{h}_i = \mu_i \cdot h_i
$$

Only neurons with $\mu_i = 1$ contribute to the forward and backward pass.

### Typical dropout probabilities:
- **Hidden layers**: $p = 0.5$
- **Input layers**: $p = 0.8$ (used less frequently)

> These values are **hyperparameters** — tune with validation set!

---

## 4. Dropout at Test Time

During inference, we no longer want randomness. Instead, we use the **expected output**:

### Option 1: Scale weights at test time
If $p = 0.5$, then multiply each output by $p$:
$$
\tilde{h}_i = p \cdot h_i
$$

This compensates for the fact that neurons were only active half the time during training.

### Option 2: Scale at training time (inverted dropout)
Instead of scaling at test time, **scale up the active neurons during training**:
$$
\tilde{h}_i = \frac{1}{p} \cdot \mu_i \cdot h_i
$$

Then no rescaling is needed during test time. This is the default in **PyTorch** and **TensorFlow**.

---

## 5. Theoretical & Practical Benefits

| Benefit                     | Description                                                                 |
|-----------------------------|-----------------------------------------------------------------------------|
| **Reduces overfitting**     | Less co-adaptation → more robust model                                      |
| **Acts as model averaging** | Trains many sub-networks, combines them at test time                        |
| **Efficient alternative**   | To full ensembles (training 10+ networks)                                   |
| **Improves generalization** | Dropout improves validation/test performance in deep nets                   |

---

## 6. Summary: Training vs. Testing

| Phase        | Neurons Dropped?         | Outputs Scaled?            |
|--------------|---------------------------|-----------------------------|
| Training     | Yes (random mask)         | Optional: scale by $1/p$   |
| Testing      | No                        | Optional: scale by $p$     |

---

## 7. Practical Notes

- Dropout is most effective on:
  - Fully connected layers
  - Smaller datasets
  - Mid-sized networks

- Dropout is **not typically used** on:
  - BatchNorm layers
  - Convolutional layers (other regularizers like data augmentation preferred)

---

## 8. Original Dropout Paper

Srivastava et al., 2014  
**"Dropout: A Simple Way to Prevent Neural Networks from Overfitting"**  
Journal of Machine Learning Research  
https://jmlr.org/papers/volume15/srivastava14a/srivastava14a.pdf

- Dropout reduces **test error** significantly in deep nets with 2–4 layers and 1000+ units
- Became a **default component** in most deep learning libraries

---