# Bayesian Statistics

## What is Bayesian Inference?

Bayesian inference is the process of updating our beliefs about an unknown quantity after observing data. It combines:

- **Prior distribution**: beliefs about the parameter before observing data.
- **Likelihood**: how likely the observed data is for different parameter values.
- **Posterior distribution**: updated beliefs about the parameter after observing data.

**Bayes’ Theorem** (for two events A and B):

$$
P(A \mid B) = \frac{P(B \mid A) \cdot P(A)}{P(B)}
$$

This allows us to update the probability of event $A$ after observing event $B$.

---

## Examples: Updating Beliefs

### Two Quarters Example

- 1 real coin (50/50), 1 fake coin (double heads).
- Toss a random coin and see heads.
- Use Bayes’ Rule to compute $P(\text{fake} \mid \text{heads})$.

Seeing heads makes it much more likely the coin is fake.

### Ten Quarters Example

- 9 real coins, 1 fake.
- Same observation: see heads.
- Now the posterior probability of being fake increases less dramatically — prior belief dominates more.

---

## Frequentist vs. Bayesian Probability

### Frequentist:
- Probability = long-run frequency.
- Parameters are fixed, data is random.
- Confidence intervals describe procedures over many repetitions.

### Bayesian:
- Probability = degree of belief.
- Parameters are random variables (representing uncertainty).
- Posterior intervals are statements about the parameter given the observed data.

---

## Estimating a Probability: Coffee Tasting Example

Suppose someone claims they can tell decaf from regular coffee by taste. Let $p$ be the probability they are correct.

We observe:
- $N = 10$ trials
- $Y \in \{0, 1, ..., 10\}$: number of correct guesses
- Sample proportion: $\hat{p} = Y / N$

### Frequentist CI:

By CLT:

$$
\hat{p} \sim \mathcal{N}\left(p, \frac{p(1 - p)}{N}\right)
$$

Approximate 95% CI:

$$
\hat{p} \pm 1.96 \cdot \sqrt{\frac{\hat{p}(1 - \hat{p})}{N}}
$$

This interval is random; the true $p$ is fixed.

---

## Bayesian Inference for Coffee Tasting

We assign a **prior** distribution to $p$, often a **Beta distribution**:

$$
p \sim \text{Beta}(\alpha, \beta)
$$

- $\alpha - 1$: prior successes
- $\beta - 1$: prior failures

Observed data likelihood is Binomial:

$$
Y \sim \text{Binomial}(N, p)
$$

Posterior:

$$
p \mid Y \sim \text{Beta}(\alpha + Y, \beta + N - Y)
$$

This is an example of a **conjugate prior**.

### Credible Interval:

A 95% credible interval contains 95% of the posterior probability mass. This is a direct probability statement about $p$, unlike a frequentist CI.

---

## Priors and Posteriors

- A **narrower** posterior indicates higher certainty.
- A **wider** posterior indicates more uncertainty.

Types of priors:
- **Uninformative**: expresses ignorance.
- **Weakly informative**: vague but provides some shape.
- **Informative**: strong beliefs about parameter values.

### As sample size increases:

- The influence of the prior decreases.
- Bayesian and frequentist results become similar (Bernstein–von Mises theorem).

---

## Bayesian Modeling Workflow

1. Choose data-generating model $p(Y \mid \theta)$
2. Choose prior $p(\theta)$
3. Observe data $y$
4. Compute posterior:

$$
p(\theta \mid y) \propto p(y \mid \theta) \cdot p(\theta)
$$

5. Summarize posterior: mean, variance, credible intervals, predictions, etc.

---

## Example: Poisson-Gamma Model (Lemonade Stand)

Lily sells lemonade each day. Her daily sales are modeled as:

$$
Y_i \sim \text{Poisson}(\lambda)
$$

### Prior:

$$
\lambda \sim \text{Gamma}(\alpha, \beta)
$$

- $\alpha$: prior count of lemonade sales
- $\beta$: prior number of days
- Prior mean = $\alpha / \beta$

### Likelihood (data $y_1, \dots, y_n$):

$$
p(y \mid \lambda) = \prod_{i=1}^n \frac{e^{-\lambda} \lambda^{y_i}}{y_i!}
$$

### Posterior:

$$
\lambda \mid y \sim \text{Gamma}\left(\alpha + \sum y_i, \beta + n\right)
$$

---

## Posterior Predictive Distribution

Goal: Predict future observation $Y_{\text{new}}$

$$
p(Y_{\text{new}} \mid y) = \int p(Y_{\text{new}} \mid \lambda) \cdot p(\lambda \mid y) \, d\lambda
$$

For Poisson-Gamma model, this integral has a closed form: **Negative Binomial** distribution.

---

## Monte Carlo Simulation

If we can't compute the posterior predictive analytically, use simulation.

### Version 1:

- Sample $\lambda^{(1)}, \dots, \lambda^{(M)} \sim p(\lambda \mid y)$
- For each $\lambda^{(i)}$, compute summaries or simulate outcomes

### Version 2 (Iterative):

- Sample $\lambda^{(i)} \sim p(\lambda \mid y)$
- Then sample $Y_{\text{new}}^{(i)} \sim \text{Poisson}(\lambda^{(i)})$
- Repeat to get a sample from the joint distribution $(\lambda, Y_{\text{new}})$

This allows flexible estimation even when analytic solutions are unavailable.

---

## Key Distributions to Know

| Distribution       | Use Case                                      | Parameters             |
|--------------------|-----------------------------------------------|------------------------|
| **Beta**           | Prior for probability in [0, 1]                | $\alpha, \beta$        |
| **Binomial**       | Number of successes in $n$ trials              | $n, p$                 |
| **Poisson**        | Count of events over fixed time                | $\lambda$              |
| **Gamma**          | Prior for Poisson rate                         | $\alpha, \beta$        |
| **Negative Binomial** | Posterior predictive for Poisson-Gamma     | $r, p$                 |

---