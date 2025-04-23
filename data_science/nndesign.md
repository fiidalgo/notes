# Anatomy of Neural Networks & Design Choices

---

## 1. Overview & Motivation

Neural networks are **modular function approximators** built by composing simple units:
$$
h(x) = f(XW + b)
$$
- $X$ = inputs
- $W$ = weights
- $b$ = bias
- $f$ = activation function

Each **edge in the network** corresponds to a weight. The final output is computed layer-by-layer from input to output.

---

## 2. Neural Network Architectures: The "Zoo"

Many variations of network architecture exist:

- Feedforward (Perceptron, MLP)
- Recurrent (RNN, LSTM, GRU)
- Autoencoders (VAE, Denoising AE, Sparse AE)
- Energy-Based Models (Boltzmann Machines, Hopfield Nets)
- Deep Belief Networks, Restricted Boltzmann Machines (RBMs)

Each has different properties and use cases, especially for different data types (images, text, sequences).

---

## 3. Anatomy of an Artificial Neural Network (ANN)

### Components:
- **Input layer**: Number of input features
- **Hidden layers**: Where computation happens
- **Output layer**: Depends on task (binary, multiclass, regression)

### Key Design Questions:
- What **activation function** to use?
- How many **nodes per layer**?
- How many **hidden layers**?
- What kind of **output unit**?
- What **loss function** is appropriate?

---

## 4. Activation Functions

### Role:
- Provide **non-linearity**
- Let neural networks model complex functions
- Determine if a neuron "fires"

### Desirable Properties:
- **Non-linear**
- **Simple to compute**
- Maintain **non-vanishing gradients** (especially in deep networks)
- Optionally restrict output range

### Common Choices:

| Function       | Formula | Properties |
|----------------|---------|------------|
| **Sigmoid**    | $\sigma(x) = \frac{1}{1 + e^{-x}}$ | Smooth, squashes to [0,1], suffers from vanishing gradients |
| **Tanh**       | $\tanh(x) = \frac{e^x - e^{-x}}{e^x + e^{-x}}$ | Squashes to [-1, 1], but still suffers from vanishing gradients |
| **ReLU**       | $f(x) = \max(0, x)$ | Sparse activations, avoids vanishing gradient for $x > 0$ |
| **Leaky ReLU** | $f(x) = \max(0.01x, x)$ | Allows small gradients for $x < 0$ |
| **ELU**        | $f(x) = \max(x, \alpha(e^x - 1))$ | Smooth variant of ReLU |
| **Softplus**   | $f(x) = \log(1 + e^x)$ | Smooth approximation of ReLU |
| **Swish (SiLU)** | $f(x) = x \cdot \sigma(x)$ | Empirically outperforms ReLU on deeper models |

> Swish is the **default in PyTorch** under the name `SiLU`.

---

## 5. Loss Functions

Loss functions measure how well the model's predictions match the targets.

### Based on Output Type:

| Task                  | Distribution | Output Layer       | Loss Function         |
|-----------------------|--------------|---------------------|------------------------|
| **Regression**        | Gaussian     | Linear              | MSE                    |
| **Binary Classification** | Bernoulli | Sigmoid             | Binary Cross-Entropy   |
| **Multiclass Classification** | Multinoulli | Softmax        | Cross-Entropy          |

### Probabilistic Interpretation:

- Use the **likelihood** of the data under the model’s prediction
- Then **minimize negative log-likelihood** (equivalent to the losses above)

Examples:

- **MSE** from normal distribution likelihood:
  $$
  \mathcal{L}(W; X, y) = \sum_i (y_i - \hat{y}_i)^2
  $$

- **Binary Cross-Entropy** from Bernoulli:
  $$
  \mathcal{L}(W; X, y) = -\sum_i y_i \log \hat{y}_i + (1 - y_i) \log(1 - \hat{y}_i)
  $$

---

## 6. Output Units

Output units translate the neural network's intermediate values into predictions appropriate for the task.

### Binary Classification:
- Output: $P(y=1)$
- Use: **Sigmoid**
- Output is constrained to $[0, 1]$
- Loss: Binary Cross-Entropy

### Multiclass Classification:
- Outputs scores (logits) for each class
- Use: **Softmax**
  $$
  P(y = k) = \frac{e^{s_k}}{\sum_{j=1}^K e^{s_j}}
  $$
- Loss: Cross-Entropy

### Regression:
- Output: real-valued $\hat{y}$
- Use: **Linear** output layer
- Loss: Mean Squared Error (MSE)

---

## 7. Putting It All Together: Design Flow

1. **Determine task**: Binary classification? Regression?
2. **Choose architecture**:
   - How many layers?
   - How many units per layer?
3. **Select activation** (e.g., ReLU in hidden layers)
4. **Pick output unit** (sigmoid, softmax, linear)
5. **Choose loss function** to match output
6. **Select optimizer** (SGD, Adam, etc.)

---

## 8. Universal Approximation Theorem

**Theorem**: Any continuous function on a bounded domain can be approximated by a neural network with **one hidden layer** of sufficient size.

### So why go deeper?
- Depth = **learn hierarchical representations**
- Shallow networks require **exponentially more neurons**
- Deep networks are more **parameter efficient** and **generalize better**

---

## 9. Depth & Representation Learning

More layers enable the network to:
- **Learn features at increasing levels of abstraction**
- Capture hierarchical structure (e.g., pixels → edges → shapes → objects)

### Empirical evidence (Goodfellow et al. 2017):
- **11-layer networks** generalize better than 3-layer ones even with the **same number of parameters**
- Depth helps beyond just adding capacity

---

## 10. Summary

| Design Component     | Considerations                                                                      |
|----------------------|--------------------------------------------------------------------------------------|
| **Activation**        | ReLU is default; sigmoid/tanh only for outputs or shallow nets                     |
| **Loss Function**     | Must match task and output distribution                                             |
| **Output Unit**       | Sigmoid, softmax, or linear depending on classification/regression                 |
| **Architecture**      | Start simple, deepen as needed — deeper networks are more expressive               |
| **Optimizer**         | Choose based on convergence (SGD, Adam, RMSProp, etc.)                             |

---