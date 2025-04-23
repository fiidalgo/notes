# Nesterov Momentum

---

## 1. What is Nesterov Momentum?

Nesterov Accelerated Gradient (NAG) is a **modified momentum method**. It introduces a clever twist: instead of computing the gradient at the current weights, we compute it at a **"look-ahead" point**.

---

## 2. Standard Momentum Recap

In regular momentum:
$$
v_t = \alpha v_{t-1} + (1 - \alpha) g_t \\
W_{t+1} = W_t - \eta v_t
$$

- $g_t$ = gradient at current weight $W_t$
- $\alpha$ = momentum parameter (typically 0.9–0.99)
- $\eta$ = learning rate

This smooths the updates by incorporating prior gradient directions.

---

## 3. Nesterov Momentum: Key Idea

Rather than computing the gradient at $W_t$, Nesterov computes it at a **look-ahead point**:
$$
\tilde{W}_t = W_t - \eta \alpha v_{t-1}
$$

Then:
1. Evaluate gradient at $\tilde{W}_t$
2. Update momentum:
   $$
   v_t = \alpha v_{t-1} + (1 - \alpha) \nabla \mathcal{L}(\tilde{W}_t)
   $$
3. Update weights:
   $$
   W_{t+1} = W_t - \eta v_t
   $$

> You "peek" into the future to decide the direction to move.

---

## 4. Algorithm (Step-by-Step)

Let:
- $\alpha$: momentum decay factor
- $\eta$: learning rate
- $v_t$: velocity vector (momentum)

### Initialization:
- $v_0 = 0$
- $W_0$ = random init

### For each iteration $t$:
1. Compute look-ahead weight:
   $$
   \tilde{W}_t = W_t - \eta \alpha v_{t-1}
   $$
2. Compute gradient at this lookahead:
   $$
   g_t = \nabla_W \mathcal{L}(\tilde{W}_t)
   $$
3. Update momentum:
   $$
   v_t = \alpha v_{t-1} + (1 - \alpha) g_t
   $$
4. Update weight:
   $$
   W_{t+1} = W_t - \eta v_t
   $$

---

## 5. Visual Intuition

- In regular momentum, you compute the gradient and then "apply" momentum.
- In **Nesterov**, you **apply momentum first**, then compute the gradient at the **look-ahead** position.

This anticipatory update can lead to **faster convergence** and **smoother updates**, especially in high-curvature directions (valleys).

---

## 6. Why It Works Better

| Advantage              | Why It Matters                                        |
|------------------------|--------------------------------------------------------|
| **More informed updates** | Gradient is computed at a look-ahead position        |
| **Better convergence** | Especially for convex functions                        |
| **Escapes saddle points** | By including momentum + gradient from a future point |
| **Faster in practice** | Used in many deep learning frameworks by default       |

---

## 7. Summary of Notation (from the slides)

| Symbol            | Meaning                                |
|------------------|----------------------------------------|
| $W_t$            | Current weight                         |
| $\tilde{W}_t$    | Interim "lookahead" weight              |
| $g_t$            | Gradient at lookahead point            |
| $v_t$            | Smoothed gradient (momentum)           |
| $\alpha$         | Momentum coefficient (e.g. 0.9)        |
| $\eta$           | Learning rate                          |

---

## 8. Summary

| Optimizer        | Gradient Location         | Comment                               |
|------------------|---------------------------|----------------------------------------|
| **Momentum**     | At current weights $W_t$  | Updates might "overshoot" or oscillate |
| **Nesterov**     | At lookahead $\tilde{W}_t$| "Look ahead" leads to smoother path    |

---