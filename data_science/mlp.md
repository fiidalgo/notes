# Multi-Layer Perceptron (MLP)

---

## 1. Recap: What We've Seen So Far

### Single Neuron Recap
A **neuron** is:
- An **affine transformation**: $h = XW + b$
- Followed by an **activation**: $\hat{y} = \sigma(h)$

#### Depending on activation:
- **Sigmoid** → Logistic regression
- **Identity** → Linear regression

So far, we’ve just **rebranded regression** with a neuron interpretation.

---

## 2. Motivation for Going Deeper: Why Neural Networks?

### Limitations of Linear Models

Linear/logistic regression:
- Can only separate data with **linear boundaries**
- Can’t capture **complex regions or nonlinear patterns**

#### Visual Example:
- Data separable into **curved or irregular** regions (e.g., circles, XOR)
- Polynomial regression also fails in many cases (overfits or underfits)

---

## 3. Intuition: What’s the Big Deal with Neural Networks?

Neural networks can:
- Represent **nonlinear functions**
- Model **nonlinear decision boundaries**
- Combine neurons to represent complex patterns
- Learn **hierarchical representations**

---

## 4. Example: Heart Disease Data

Using a binary dataset with attributes like:
- Age, Chest Pain, Cholesterol, etc.
- Target: `AHD` = has heart disease (Yes/No)

This is the **same example as before**, but now we want to go beyond fitting one linear decision surface.

---

## 5. Limitation of One Neuron (Logistic Regression)

Depending on how we choose the weight vector $W$, we can:
- Fit the **left part** of the data well, or
- Fit the **right part** of the data well
- But **not both simultaneously**

---

## 6. Key Idea: Combine Multiple Neurons

### Step-by-step:

1. Use **two neurons** (each with their own weights)
   - One fits the left part of the data
   - One fits the right

2. Combine their outputs:
   - Let $z_1$ and $z_2$ be outputs of the two neurons
   - Compute:
     $$
     q = W_{31} z_1 + W_{32} z_2 + W_{30}
     $$
   - Apply **sigmoid**: $p = \sigma(q)$

3. Final prediction:
   $$
   \hat{y} = \sigma(W_3^T z + b_3)
   $$

This is a **two-layer neural network** — the first layer learns representations, and the second layer makes predictions.

---

## 7. Forward Pass Structure

### First layer:
- Multiple neurons compute:
  $$
  z_k = \sigma(W_k^T x + b_k)
  $$

### Second layer:
- Combine those outputs with another affine + activation:
  $$
  \hat{y} = \sigma(W_3^T z + b_3)
  $$

This is your **classic multi-layer perceptron**.

---

## 8. Why Do This? What We Gain

By combining neurons:
- We can **model complicated, nonlinear functions**
- Add **flexibility** without hardcoding features
- Can build **nonlinear decision boundaries** that adapt to the data

---

## 9. Visualization: Composing Neurons

Each neuron can carve out a **region** of space. Their combination lets us:
- Add decision boundaries together
- Learn unions/intersections of features
- Capture **hierarchical structure** in data

Even for **2D input**, this stacking works the same:
- Each layer learns increasingly abstract features

---

## 10. Summary

| Concept                | Description                                                                 |
|------------------------|-----------------------------------------------------------------------------|
| Neuron                 | Affine transformation + activation                                           |
| Single neuron          | Linear or logistic regression                                                |
| Neural network         | Composed of multiple neurons                                                 |
| MLP                    | Multi-Layer Perceptron: multiple layers of neurons                           |
| Benefit of MLP         | Can model nonlinear functions, separate complex regions, and generalize well |

---

## 11. What’s Next?

Next lectures will explore:
- **Which activation functions** to use (e.g., ReLU vs. sigmoid)
- **How many neurons and layers** to add
- **How to choose loss functions**
- **How to design output layers** (binary, multiclass, regression)

---