# Convolutional Neural Networks II

---

## 1. CNN Recap: Layers and Kernels

### Convolutional Layers:
- Use small **filters (kernels)** that scan the input spatially.
- Apply the same weights (weight sharing) → outputs a **feature map**.
- Each feature map = one filter’s response at every spatial location.

---

## 2. Convolution Output Size Formula

Given:
- $I$ = input size (height or width)
- $K$ = kernel size
- $P$ = padding
- $S$ = stride

Then:
$$
O = \frac{I - K + 2P}{S} + 1
$$

### Example:
- Input = 32
- Kernel = 3
- Padding = 1
- Stride = 1

$$
O = \frac{32 - 3 + 2(1)}{1} + 1 = 32
$$

> This is **"same" padding**: output size = input size.

---

## 3. Parameter Count in a Convolutional Layer

Formula:
$$
\text{Params} = (K_h \cdot K_w \cdot C_{in} + 1) \cdot C_{out}
$$

- $K_h, K_w$: kernel height and width
- $C_{in}$: number of input channels
- $C_{out}$: number of filters (output channels)
- `+1` = one bias per filter

### Example:
- 16 filters of size 5×5×8 → input = 8 channels
- $C_{in} = 8$, $C_{out} = 16$, $K = 5$

$$
(5 \cdot 5 \cdot 8 + 1) \cdot 16 = (200 + 1) \cdot 16 = 3216 \text{ parameters}
$$

---

## 4. Flatten Layer: Why We Use It

CNN layers output a 3D volume (H×W×C). Dense layers expect a **1D vector**.

So we **flatten**:
$$
32 \times 32 \times 8 \Rightarrow 8192 \text{ features}
$$

This allows the dense layer to connect to **all features** learned by convolution layers.

---

## 5. Building a CNN: Components

| Layer                | What It Does                                        |
|----------------------|-----------------------------------------------------|
| **Conv Layer**       | Learns local filters (kernels), outputs feature maps|
| **Pooling Layer**    | Reduces spatial dimensions, keeps salient features  |
| **Flatten Layer**    | Converts 3D volume to 1D for dense input            |
| **Dense Layer**      | Learns combinations for classification/regression   |

---

## 6. Dropout in CNNs

### In Fully Connected Networks:
Dropout works by applying a **mask to the activations**, randomly turning off neurons.

We can write:
$$
\tilde{h} = R \cdot h, \quad \text{where } R \text{ is a diagonal matrix with Bernoulli entries}
$$

### In CNNs:
- Dropout still applies Bernoulli noise, **but not at the weight level**.
- Instead, it **multiplies entire feature maps by 0 or 1**.
- This is less effective than in MLPs, but sometimes useful before the final dense layers.

> **Key takeaway**: Dropout in CNNs masks **features**, not weights.

---

## 7. Backprop Through Max Pooling

In max pooling:
- Forward pass: pick the **maximum** value in each window.
- Backward pass: only propagate gradients **through the max**.

### Example:

Input:
```
2 4 8 3
9 3 4 2
5 4 6 3
2 3 1 3
```

With 2×2 max pooling (stride = 2), output:
```
9 8
5 6
```

### Backward pass:
- Keep a **mask** of where the maxes occurred during forward pass.
- Only those positions get nonzero gradients.

> This is a form of sparse gradient flow.

---

## 8. Receptive Field: What It Is and Why It Matters

### Definition:
The **receptive field** of a neuron is the region in the input that affects its output.

### Why it matters:
- Determines **how much context** each unit "sees"
- Higher layers have **larger receptive fields**
- Important in tasks like object detection or segmentation

---

### 1D Example (stride 1, no padding):

Layer 1: 3×1 kernel → receptive field = 3

Layer 2: applies 3×1 kernel to Layer 1 → receptive field:
$$
r_2 = r_1 + (k - 1) = 3 + 2 = 5
$$

### General Recursive Formula:
Let $r_l$ be receptive field at layer $l$, $k_l$ kernel size, $s_l$ stride:
$$
r_l = r_{l-1} + (k_l - 1) \cdot \prod_{j=1}^{l-1} s_j
$$

---

## 9. Dilated Convolutions

### Problem:
Standard convolutions only expand receptive field **slowly** with more layers.

### Solution:
Insert **"holes"** between kernel elements — this is **dilation**.

- Dilation rate = 1 → regular convolution
- Dilation rate = 2 → skip 1 input element between kernel points

### Benefit:
- Enlarges receptive field **without increasing number of parameters**
- Used in **semantic segmentation**, **WaveNet**, etc.

---

## 10. Summary Table

| Concept                | Explanation                                                                 |
|------------------------|-----------------------------------------------------------------------------|
| Output size formula    | $O = \frac{I - K + 2P}{S} + 1$                                               |
| Conv layer parameters  | $(K_h \cdot K_w \cdot C_{in} + 1) \cdot C_{out}$                            |
| Flatten                | Reshapes tensor for dense layer input                                       |
| Dropout (CNN)          | Bernoulli mask on feature maps                                              |
| MaxPool Backprop       | Only gradients for max-selected locations                                   |
| Receptive Field        | Input region affecting each neuron                                          |
| Dilation               | Expands receptive field using spaced-out kernels                            |

---