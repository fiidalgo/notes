# Neural Network Regularization

---

## 1. Why Do We Need Regularization?

**Regularization** refers to modifications made to the learning algorithm to **reduce generalization error** (test error) — even if training error stays the same.

---

### Problem: Overfitting

Neural networks, especially deep ones with many layers and parameters, can perfectly memorize training data — but perform poorly on unseen data.

Example:
- A 5-layer network with 100 neurons/layer might get **near-zero training loss**, but **high validation loss**.

---

## 2. Norm Penalties: L1 and L2 Regularization

### Basic Idea:
We modify the loss function by adding a **penalty term**:
$$
\mathcal{L}'(W) = \mathcal{L}(W; X, y) + \alpha \cdot \Omega(W)
$$

Where:
- $\alpha$ is the **regularization strength** (hyperparameter)
- $\Omega(W)$ is a function of the weights

---

### A. L2 Regularization (Weight Decay)
$$
\Omega(W) = \frac{1}{2} \|W\|_2^2 = \frac{1}{2} \sum_j W_j^2
$$

New loss:
$$
\mathcal{L}' = \mathcal{L} + \frac{\alpha}{2} \|W\|_2^2
$$

#### Gradient update:
If original SGD update was:
$$
W_{t+1} = W_t - \eta \nabla_W \mathcal{L}
$$

Then with L2:
$$
W_{t+1} = W_t - \eta \left( \nabla_W \mathcal{L} + \alpha W_t \right)
$$

> We shrink the weights toward zero — hence the name "weight decay".

---

### B. L1 Regularization (Sparsity-inducing)
$$
\Omega(W) = \|W\|_1 = \sum_j |W_j|
$$

- Encourages **sparse weights** (some weights = 0)
- Leads to **feature selection** in shallow models

---

### Choosing $\alpha$ (the penalty strength)

| $\alpha$ Value | Effect on Model               |
|----------------|-------------------------------|
| $0$            | No regularization              |
| Small          | Minor complexity control       |
| Large          | Aggressively penalizes weights |

- **Too small**: won’t prevent overfitting
- **Too large**: may underfit the data

---

## 3. Early Stopping

Instead of relying only on the loss function, we **monitor validation performance during training**:

### Key Idea:
Stop training **before the network begins to overfit**.

#### Typical behavior:
- Training loss keeps going down
- Validation loss starts increasing after a point

We stop at the point where **validation loss is minimal**.

---

### Patience:
Sometimes performance bounces around before improving again.

- **Patience = number of epochs with no improvement** before stopping
- Example:
  - If patience = 10: training will stop if no val_loss improvement for 10 consecutive epochs

---

### TensorFlow Code:

```python
from tensorflow.keras.callbacks import EarlyStopping

early_stop = EarlyStopping(
    monitor='val_loss',
    min_delta=0.001,
    patience=10,
    mode='min',
    restore_best_weights=True
)
```

- `min_delta`: smallest change considered an improvement
- `patience`: how many epochs to wait before stopping
- `restore_best_weights`: revert to best model after training stops

---

## 4. Data Augmentation

Used to improve generalization by **artificially increasing the dataset size** through transformations.

---

### Common in Image Tasks:
- Rotation, zoom, flipping, cropping, etc.
- Example:
  - Original: image of a cat
  - Augmented: rotated, flipped, shifted versions of the same image

---

### Warning:
- You **must not change the class** via augmentation!
- Example: rotating a “6” by 180° may look like a “9” — incorrect label!

---

### Not great for:
- **Tabular data** (e.g., spreadsheets)
- **Time-series data** (can violate sequence integrity)

---

## 5. Summary Table

| Method             | Key Idea                                    | Pros                             | Cons                             |
|--------------------|---------------------------------------------|----------------------------------|----------------------------------|
| L2 (weight decay)  | Penalize large weights                      | Smooth regularization            | Doesn’t encourage sparsity       |
| L1                 | Penalize absolute values                    | Promotes sparsity                | Can zero out weights prematurely |
| Early Stopping     | Halt when val_loss worsens                  | Simple, widely used              | Needs tuning (patience, metric)  |
| Data Augmentation  | Increase dataset diversity via transformation | Boosts generalization            | Not always valid or available    |

---