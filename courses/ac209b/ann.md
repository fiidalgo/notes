# Introduction to ANN and Perceptron

---

## 1. Introduction & Motivation

### Why Neural Networks?

Neural networks (NNs) are a powerful class of models for:
- **Classification** (e.g. predicting if a patient has a disease)
- **Regression** (e.g. forecasting numerical outcomes)
- **Representation learning** (e.g. image, text, speech)

Recent successes:
- **AlphaFold**: solved protein folding
- **YOLOv5**: advanced object detection
- **Text-to-image**: DALL·E, Stable Diffusion
- **Black hole image reconstruction**: CHIRP (Katie Bouman)
- **Chatbots, code generation, autonomous driving**

### Challenges:
- **Bias** in data (gender, racial bias in ML models)
- Requires **structured modeling** and ethical awareness

---

## 2. Basic Concepts: Supervised vs Unsupervised Learning

| Paradigm        | Description                                       | Example                        |
|----------------|---------------------------------------------------|--------------------------------|
| Supervised      | Uses labeled data to predict outcomes             | Classification, Regression     |
| Unsupervised    | Finds structure in unlabeled data                 | Clustering, Dimensionality Red.|

---

## 3. Supervised Learning Workflow

1. **Get labeled data**: Pairs of inputs $X$ and outputs $y$
2. **Select model**: E.g. linear regression, logistic regression, neural network
3. **Define loss function**:
   - Regression: MSE
   - Classification: Binary cross-entropy, categorical cross-entropy, hinge loss
4. **Minimize loss**:
   - Analytical (for linear regression)
   - Gradient descent (for complex models)

---

## 4. Linear to Logistic Regression

### Linear Regression:

Predicts a **continuous** outcome.

Example: Predict basketball player’s points scored.

$$
y = \beta_0 + \beta_1 x + \epsilon
$$

### Logistic Regression:

Predicts a **binary outcome** (classification).

- Outputs a **probability** via the **sigmoid function**:
  $$
  \sigma(h) = \frac{1}{1 + e^{-h}}, \quad h = \beta_0 + \beta_1 x
  $$
- Example: Predict whether player will score more than a certain number of points (yes/no)

---

## 5. Classification Example: Heart Disease

Data on 303 patients includes:
- Features like Age, Chest Pain, Cholesterol, etc.
- Target: `AHD` (Yes/No — has heart disease)

Goal: Use features $X$ to predict binary target $y$

---

## 6. Why Not Use Linear Regression for Classification?

Linear regression doesn’t constrain predictions:
- Output can be < 0 or > 1 — not valid for probabilities
- Doesn’t model decision boundaries effectively

---

## 7. Logistic Regression Recap

- Uses **sigmoid function** to produce valid probabilities:
  $$
  p = \frac{1}{1 + e^{-h}}, \quad \text{where } h = \beta_0 + \beta_1 x
  $$

- **Loss function**: Binary cross-entropy
  $$
  \mathcal{L}(\beta) = -[y \log p + (1 - y) \log (1 - p)]
  $$

---

## 8. From Logistic Regression to Neural Networks

Let’s generalize logistic regression into a **neural network view**:

Each prediction is:
- **Affine transformation** (linear step): $h_i = \beta_0 + \beta_1 x_i$
- **Activation**: $p_i = \sigma(h_i)$
- **Loss**: $\mathcal{L}_i(\beta) = -y_i \log p_i - (1 - y_i)\log(1 - p_i)$

Total loss:
$$
\mathcal{L}(\beta) = \sum_{i=1}^n \mathcal{L}_i(\beta)
$$

This is **logistic regression**, but the formulation matches what we'll soon generalize into a neural network.

---

## 9. Building Our First Neural Network (Single Neuron)

### Mathematical structure:

Let:
- $X \in \mathbb{R}^{n \times p}$ = input matrix (observations × features)
- $W \in \mathbb{R}^{p}$ = weights
- $b \in \mathbb{R}$ = bias

Then:
1. Affine step: $h = XW + b$
2. Activation: $\hat{y} = \sigma(h)$
3. Loss: Binary cross-entropy

### Vectorized computation:
$$
\hat{y} = \sigma(XW + b)
$$

This structure:
- Is just **logistic regression**
- Becomes a **single neuron** in an artificial neural network (ANN)

---

## 10. Visualizing the Neuron

A **neuron** performs:
1. Weighted sum (affine): $h = XW + b$
2. Activation function: $\hat{y} = \sigma(h)$

This **modular view** allows us to:
- Build more complex networks
- Replace activations (ReLU, tanh, etc.)
- Chain multiple neurons → **multi-layer perceptron**

---

## 11. Linear Regression as a Neuron

If we remove the activation function:
- Output is $y = h = XW + b$
- Loss is Mean Squared Error (MSE)

Then the neuron becomes **equivalent to linear regression**.

---

## 12. Summary

| Model Type              | Architecture                             | Loss Function         |
|------------------------|-------------------------------------------|-----------------------|
| Linear Regression       | Affine only                               | MSE                   |
| Logistic Regression     | Affine + sigmoid                          | Binary Cross Entropy  |
| Single Neuron (ANN)     | Affine + any activation                   | Depends on task       |

### Key Insight:
- **A neuron is just an affine transformation + nonlinearity**
- Logistic regression = single neuron with sigmoid
- Linear regression = single neuron with identity activation

---
