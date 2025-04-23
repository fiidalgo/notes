# Stochastic Gradient Descent (SGD)

---

## 1. Recap: Gradient Descent Limitations

From last lecture:

- To minimize the total loss:
  $$
  \mathcal{L}(W) = \sum_{i=1}^n \mathcal{L}_i(W)
  $$

- Update rule:
  $$
  W^{(t+1)} = W^{(t)} - \eta \nabla_W \mathcal{L}(W)
  $$

### Problem:
Computing the **full gradient** $\nabla_W \mathcal{L}(W)$ requires a pass through **all $n$ training examples**, which is:
- **Slow** for large datasets
- **Memory-intensive**
- Can get stuck in **local minima**

---

## 2. Stochastic Gradient Descent (SGD): The Key Idea

Instead of computing the gradient over the full dataset, we update using **just one example** $(X_i, y_i)$:

### SGD update rule:
$$
W^{(t+1)} = W^{(t)} - \eta \nabla_W \mathcal{L}_i(W)
$$

Where:
- $\mathcal{L}_i(W)$ is the **loss for one training example**
- $\eta$ is the **learning rate**

This gives a **noisy but fast estimate** of the true gradient.

---

## 3. Why Is It Called "Stochastic"?

Because each update is based on a **randomly selected** training point.

Even though each step is noisy, over many iterations it **converges** toward a minimum.

---

## 4. The SGD Workflow

Let’s break SGD into precise steps:

1. **Sample** one training example $(X_i, y_i)$
2. **Forward pass**: compute prediction $\hat{y}_i = f_W(X_i)$
3. **Compute loss**: $\mathcal{L}_i(W) = \text{BCE/MSE/etc}$
4. **Backpropagate**: compute gradient $\nabla_W \mathcal{L}_i$
5. **Update weights**:
   $$
   W \leftarrow W - \eta \nabla_W \mathcal{L}_i
   $$

6. **Repeat** for next training example

---

## 5. Mini-Batch SGD

Instead of using **1 example** or **all $n$ examples**, mini-batch SGD uses a **subset (batch)** of size $B$:

### Mini-batch update rule:
Let $B_k = \{i_1, i_2, ..., i_B\}$ be a mini-batch.

Then:
$$
W \leftarrow W - \eta \cdot \frac{1}{B} \sum_{j \in B_k} \nabla_W \mathcal{L}_j(W)
$$

This provides a **balance** between:
- The speed of SGD
- The stability of full batch gradient descent

---

## 6. Example Derivation: Binary Cross-Entropy Loss

Suppose $\hat{y}_i = \sigma(W^T X_i)$ and $y_i \in \{0, 1\}$.

The BCE loss:
$$
\mathcal{L}_i(W) = -y_i \log \hat{y}_i - (1 - y_i) \log(1 - \hat{y}_i)
$$

Its gradient:
$$
\nabla_W \mathcal{L}_i = (\hat{y}_i - y_i) X_i
$$

So the SGD update becomes:
$$
W \leftarrow W - \eta (\hat{y}_i - y_i) X_i
$$

---

## 7. Benefits of SGD

| Benefit                     | Explanation                                                                 |
|-----------------------------|-----------------------------------------------------------------------------|
| **Faster per step**         | Each update uses just one (or a few) data points                           |
| **Online learning**         | Can process streaming data                                                  |
| **Explores loss surface**   | Noise in updates can help escape local minima                               |
| **Low memory footprint**    | Only stores mini-batch, not full dataset                                    |

---

## 8. Drawbacks of SGD

| Drawback                   | Explanation                                                                 |
|----------------------------|-----------------------------------------------------------------------------|
| **Noisy updates**          | May bounce around the minimum                                               |
| **Needs tuning**           | Learning rate $\eta$ is critical                                            |
| **Slower convergence**     | May require more epochs to reach minimum                                    |

---

## 9. Visualizing Full vs Mini-Batch vs SGD

Imagine a 2D loss surface $L(W_1, W_2)$:

- **Full GD**: smooth path toward minimum
- **SGD**: jagged, stochastic path
- **Mini-batch**: a compromise — less noisy, still fast

Sometimes, **SGD helps avoid local minima** thanks to randomness.

---

## 10. Epochs and Batching

- **Epoch**: one full pass through the dataset
- One epoch in SGD = $n$ parameter updates (1 per example)
- One epoch in mini-batch SGD = $\frac{n}{B}$ updates

> Always **shuffle data** between epochs to improve generalization

---

## 11. Choosing Batch Size

- Typical sizes: 32, 64, 128, 256
- **Powers of 2** are preferred for GPU memory efficiency
- Tradeoff:
  - **Smaller batches**: more noise, more updates
  - **Larger batches**: smoother, but more compute per update

---

## 12. Summary Table

| Method      | Update Based On        | Memory Use | Speed  | Noise     | Escapes Local Minima? |
|-------------|-------------------------|------------|--------|-----------|------------------------|
| **Full GD** | All $n$ examples        | High       | Slow   | None      | No                     |
| **Mini-batch** | Batch of size $B$     | Medium     | Medium | Low–Medium | Maybe                  |
| **SGD**     | One example             | Low        | Fast   | High      | Often                  |

---

## 13. Final Thought

- **Mini-batch SGD** is the **default choice in deep learning**.
- It strikes a balance between noisy but fast learning and stable convergence.
- The randomness helps **generalization** and **escaping poor local minima**.
- We can further improve SGD with tricks like:
  - Momentum
  - Learning rate schedules
  - Adaptive methods (e.g., Adam)

---