# Hierarchical Bayes Models

---

## 1. General Setting: What Are Hierarchical Models?

Hierarchical (or multilevel) models are designed for **grouped or nested data** — where observations are clustered within higher-level units (e.g., patients within hospitals, students within schools).

- Observed data: predictors $x_{ij}$ and responses $y_{ij}$ for subject $i$ on trial $j$
- Data are grouped by some higher-level structure (subjects, schools, etc.)
- A key idea: **share information across groups**, while still modeling **within-group variability**

**Bayesian modeling** is especially effective here:
- Allows us to express group-level variation as distributions
- Automatically balances **overfitting (too much variation)** vs **underfitting (ignoring group structure)**

---

## 2. Motivating Example: Sleep Deprivation Study

Study setup:
- 18 subjects perform daily reaction time tasks under sleep restriction
- On day 0, subjects sleep normally; then restricted to 3 hours/night
- Reaction time $y_{ij}$ measured for subject $i$ on day $j$
- Goal: model how sleep deprivation affects reaction time **across and within individuals**

---

## 3. Modeling Options: Pooled vs. Unpooled vs. Partial Pooling

We compare three main strategies for modeling grouped data:

---

### A. Pooled Model

Single linear model across all subjects:

$$
y_{ij} = \alpha + \beta x_{ij} + \epsilon_{ij}, \quad \epsilon_{ij} \sim \mathcal{N}(0, \tau^{-1})
$$

#### Pros:
- Uses all data efficiently
- Simple to implement

#### Cons:
- Ignores individual variability
- Assumes all subjects share same baseline and slope

### Challenge Addressed:
- **None.** This model completely ignores the grouping structure.

---

### B. Unpooled Model (No Pooling)

Separate linear regression for each subject:

$$
y_{ij} = \alpha_i + \beta_i x_{ij} + \epsilon_{ij}, \quad \epsilon_{ij} \sim \mathcal{N}(0, \tau_i^{-1})
$$

Each subject gets their **own parameters**.

#### Pros:
- Captures individual behavior
- No shared assumptions across groups

#### Cons:
- Prone to **overfitting** (only 10 data points per subject!)
- Can't generalize to new subjects
- **Parameter explosion**: many parameters to estimate

### Challenge Addressed:
- **Similarity is context-dependent**: lets each subject differ completely
- But **sample size per group is too small** for reliable inference

---

### C. Partial Pooling (Hierarchical Bayes)

A compromise: share information **statistically** across groups using a hierarchical prior:

$$
y_{ij} = \alpha_i + \beta_i x_{ij} + \epsilon_{ij}, \quad \epsilon_{ij} \sim \mathcal{N}(0, \tau^{-1})
$$

where

$$
\alpha_i \sim \mathcal{N}(\mu_\alpha, \tau_\alpha^{-1}), \quad \beta_i \sim \mathcal{N}(\mu_\beta, \tau_\beta^{-1})
$$

Hyperparameters $(\mu_\alpha, \tau_\alpha), (\mu_\beta, \tau_\beta)$ have **hyperpriors**.

#### Interpretation:
- Subject-specific intercepts and slopes are **drawn from a common distribution**
- Balances between pooling and independence — the **core idea of hierarchical Bayes**

#### Pros:
- **Bayesian shrinkage**: extreme $\alpha_i, \beta_i$ values pulled toward population mean
- Allows **generalization** to new subjects
- **Regularizes** parameters for small-group data
- Captures both **group-level and population-level uncertainty**

#### Cons:
- More complex to specify
- Requires MCMC or variational inference

### Challenges Addressed:
- ✅ **Context-dependent similarity**: groups allowed to differ, but not too much
- ✅ **Small within-group sample size**: shared priors stabilize estimates
- ✅ **Regularization**: shrinkage prevents overfitting
- ✅ **Generalization**: can predict for unseen groups

---

## 4. Priors and Hyperpriors

In hierarchical models, **group-level parameters** themselves follow distributions with unknown parameters:

$$
\alpha_i \sim \mathcal{N}(\mu_\alpha, \tau_\alpha^{-1}), \quad \mu_\alpha \sim \mathcal{N}(0, 1000), \quad \tau_\alpha \sim \text{Gamma}(0.1, 0.1)
$$

This adds two more layers:
- **Priors** on group-level parameters (e.g., $\alpha_i$)
- **Hyperpriors** on hyperparameters (e.g., $\mu_\alpha$, $\tau_\alpha$)

This structure allows **information to flow up and down the hierarchy**:
- Data for each subject informs their parameters
- These inform the population distribution (and vice versa)

---

## 5. Inference: Posterior & Shrinkage

Once the data are observed:
- The posterior balances:
  - Matching individual subject data
  - Staying consistent with the population distribution

This gives rise to **Bayesian shrinkage**:
- Subjects with fewer data points (more uncertainty) get pulled more strongly toward the group average
- Subjects with more data have more individualized estimates
- Result: more stable estimates and better generalization

### Shrinkage is a form of regularization:
- Prevents overfitting when data are scarce
- Helps avoid extreme estimates due to noise

---

## 6. Comparing Model Behaviors

### Why pooled/unpooled results differ:
- **Unpooled** fits too tightly to individual data — overfits due to small $n$
- **Pooled** ignores differences — underfits real structure
- **Hierarchical (partial pooling)** balances both

### Visualization:
- Best-fit lines for unpooled vary wildly
- Best-fit lines for pooled are flat and identical
- Hierarchical model shows **reasonably varying lines**, pulled toward the population

---

## 7. When to Use Each Modeling Approach

| Model          | Use When...                                                | Avoid When...                                        |
|----------------|------------------------------------------------------------|------------------------------------------------------|
| Pooled         | Groups are truly similar; data is abundant and balanced    | Large between-group differences exist                |
| Unpooled       | Each group has many observations                           | Small group sizes; need for generalization           |
| Hierarchical   | Groups have few observations but share a common structure | There is *no* reason to assume any relationship      |

---

## 8. Summary of Bayesian Hierarchical Modeling

| Concept                     | Role / Importance                                               |
|-----------------------------|------------------------------------------------------------------|
| Hierarchy                   | Allows modeling of nested structure (subjects, schools, etc.)    |
| Partial pooling             | Balances within-group fit with between-group similarity          |
| Shrinkage                   | Regularizes extreme group parameters toward group mean           |
| Hyperpriors                 | Model uncertainty in group-level distributions                   |
| Bayesian inference          | Computes posteriors over all levels (individual and population)  |
| MCMC                        | Used for sampling from complex joint posterior                   |

---

## 9. Final Thoughts

- Bayesian hierarchical models offer a **principled and flexible** way to model grouped data.
- They:
  - Respect the data's structure
  - Share strength across groups
  - Generalize better to new data
- **Modeling assumptions matter** — garbage in, garbage out.
- Modern MCMC tools (like PyMC) make these models practical to fit, even when they're complex.

---