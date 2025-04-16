# Convolutional Neural Networks I

---

## 1. Motivation: Why Do We Need CNNs?

### Problem with Fully Connected Networks (MLPs):
- For a 28×28 grayscale image (like MNIST), you need 784 weights **per neuron**.
- For a color image of size 224×224×3 (ImageNet), each neuron would require 150K weights.
- MLPs **don’t scale** well with high-dimensional input like images.

### Additional issues:
- MLPs treat **each pixel as independent**, ignoring spatial relationships.
- MLPs are **not translation invariant**: a shifted version of an image gives a very different output.

---

## 2. CNNs to the Rescue: Key Ideas

CNNs address these issues with three core ideas:

### A. **Local Connectivity**
Instead of connecting every pixel to every neuron, CNNs connect **each neuron to a small local patch** (e.g., 3×3).

### B. **Weight Sharing**
All patches use the **same weights** — called a **filter** or **kernel**.
- One filter slides across the image and detects the same feature at every location.
- This greatly reduces the number of learnable parameters.

### C. **Translation Invariance**
If a feature (e.g. edge, texture) appears in a different place, the same filter will still detect it.
- Makes CNNs robust to shifts and distortions.

---

## 3. Convolution: The Operation Itself

Let:
- $X$ = input patch (e.g., 3×3 pixels)
- $K$ = kernel (e.g., 3×3 weights)

Then:
$$
\text{Convolution Output} = (K * X)[i, j] = \sum_{u=1}^{k} \sum_{v=1}^{k} K[u, v] \cdot X[i+u, j+v]
$$

> Technically this is **cross-correlation**, but we call it convolution.

### Example:
If $K$ is:
```
[[-1,  0,  1],
 [-2,  0,  2],
 [-1,  0,  1]]
```
This is a **Sobel filter** — it detects **horizontal edges**.

You slide it across the image and compute the sum of products for each position.

---

## 4. Feature Maps and Filters

- Each filter produces one **feature map** — a 2D grid showing where that feature (e.g., edge) appears in the image.
- A CNN learns **multiple filters** per layer — each detecting a different kind of pattern.

If we apply $F$ filters to an input image, we get $F$ feature maps.

---

## 5. CNN Architecture: Overview

| Layer Type      | What It Does                                   |
|------------------|------------------------------------------------|
| **Conv Layer**   | Applies filters to extract local features      |
| **Activation**   | Applies nonlinearity (e.g., ReLU)              |
| **Pooling**      | Downsamples to reduce spatial resolution       |
| **Fully Connected** | Makes final prediction (e.g., softmax)      |

---

## 6. Pooling: What & Why

### Max Pooling:
Reduces spatial size while keeping important features.

- For a 2×2 window with stride 2:
  - Output is the **maximum** value in that window

### Benefits:
- **Downsamples** feature maps → fewer parameters
- Provides **local translation invariance**
- No parameters to learn

### Example:
If input is:
```
[[1, 1, 2, 5],
 [5, 7, 7, 8],
 [3, 1, 1, 0],
 [1, 2, 3, 4]]
```
Max pooling with 2×2 windows → output:
```
[[7, 8],
 [3, 4]]
```

---

## 7. Padding and Stride

### Padding:
Used to **control the spatial size** of the output.

- **Same padding**: Pad with zeros so output size = input size
- **Valid padding**: No padding → output is smaller

### Stride:
Controls **how much the filter "jumps"** between positions.

- Larger stride → fewer output values (more aggressive downsampling)

### Output size formula:
$$
O = \frac{W - K + 2P}{S} + 1
$$

Where:
- $W$ = input width
- $K$ = kernel size
- $P$ = padding
- $S$ = stride

---

## 8. Activation Function: ReLU

CNNs use **ReLU (Rectified Linear Unit)**:
$$
f(x) = \max(0, x)
$$

Why?
- Fast to compute
- Avoids saturation (like sigmoid/tanh)
- Introduces **sparsity** (many outputs are 0)
- Helps prevent vanishing gradients

---

## 9. From Kernel to Feature Map (RGB Case)

### RGB Input = 3 Channels

Let’s say we have:
- An RGB image of shape 7×7×3
- A filter of shape 3×3×3 (one 3×3 kernel per channel)

We perform element-wise multiplication on each channel, then sum across channels:
$$
\text{Output}[i,j] = \sum_{c=1}^3 \sum_{u=1}^3 \sum_{v=1}^3 K[c,u,v] \cdot X[c,i+u,j+v]
$$

Output is a **single channel feature map**, size depends on stride and padding.

Apply multiple filters → multiple feature maps.

---

## 10. Why Multiple Filters and Layers?

### Multiple Filters:
- Each detects a different **low-level pattern**: vertical edge, horizontal edge, texture, etc.

### Multiple Layers:
- **Layer 1**: learns edges
- **Layer 2**: combines edges into corners, textures
- **Layer 3+**: combines parts into object shapes

CNNs build **hierarchical features**, just like humans.

---

## 11. Summary Table: Key Concepts

| Concept              | Explanation                                                      |
|----------------------|------------------------------------------------------------------|
| **Convolution**      | Applies a filter to local patches, computes dot product          |
| **Filter**           | Set of learnable weights shared across the image                 |
| **Feature Map**      | Output of convolving input with one filter                       |
| **Weight Sharing**   | Reduces number of parameters                                     |
| **Local Connectivity** | Detects spatially local patterns                               |
| **Pooling**          | Downsamples, provides spatial invariance                         |
| **Padding**          | Controls output size                                             |
| **Stride**           | Controls step size of the convolution                            |
| **ReLU**             | Fast, sparse, avoids vanishing gradient                          |

---