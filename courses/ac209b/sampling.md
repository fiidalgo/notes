# Sampling Methods

## Lemonade Example (Revisited)

- In the last lecture, we used **Bayesian modeling** to estimate the **posterior distribution** of Lily's average lemonade sales/day.
- We assumed:
  - **Prior**: $\text{Gamma}$
  - **Likelihood**: $\text{Poisson}$
  - Which gave us a **posterior** that was again a **Gamma**.
- We then computed the **posterior predictive distribution** $p(y_{\text{new}} \mid y)$, which turned out to be a **Negative Binomial**.

### But What If...?
- What if:
  1. We **don’t recognize** the posterior predictive as a known distribution?
  2. Or worse—what if we **can’t even sample from the posterior** because **conjugacy is lost**?

In these cases, we turn to **approximate methods**:  
Goal: Generate samples from a distribution we **cannot sample from directly**.

---

## Rejection Sampling

### Basic Idea:
- Useful when we cannot sample from the posterior or posterior predictive directly.
- We **approximate** the target distribution $f(x)$ using an **easier-to-sample** proposal distribution $g(x)$.

### Steps:
1. **Target Distribution**: Define $f(x)$, the distribution we want samples from.
2. **Proposal Distribution**: Pick $g(x)$ such that:
   - $f(x) \leq M g(x)$ for all $x$, for some constant $M > 1$
   - $g(x)$ is easy to sample from
3. **Sample**:
   - Draw $x \sim g(x)$
   - Draw $u \sim \text{Uniform}(0,1)$
   - Accept $x$ if:  
     $$ u \leq \frac{f(x)}{M g(x)} $$
   - Otherwise, reject $x$
4. **Repeat** until enough samples are collected.

### Cookie Monster Example:
- Cookie Monster wants to sample **cookie diameters**, which follow a complicated PDF.
- We apply rejection sampling with:
  - Known target distribution: the diameter PDF
  - Chosen proposal distribution: a convenient one that upper bounds the PDF
- Results improve with more samples:
  - 100 samples → noisy estimate
  - 10,000 samples → close to true stats
  - 100,000 samples → nearly perfect

---

## Bayesian Rejection Sampling

- Let $h(\theta) = p(\theta)L(\theta \mid y)$ be proportional to the posterior.
- Choose:
  - Proposal: $g(\theta) = p(\theta)$ (the prior)
  - Let $M$ be such that:  
    $$ \frac{h(\theta)}{g(\theta)} \leq M $$
- Choose $M = \max_\theta L(\theta \mid y)$ (i.e. likelihood at the MLE)
- This gives us a way to sample from the **posterior**, which is needed to compute the **posterior predictive**.

---

## Importance Sampling

- Instead of drawing from the distribution, we estimate expectations using **weighted samples**.

### Why?
- Sometimes we only need **summary statistics** (e.g. mean, variance), not full samples.

### Steps:
1. Choose a proposal distribution $q(x)$ that is easy to sample from.
2. Sample $x_1, ..., x_n \sim q(x)$
3. Assign **importance weights**:
   $$ w_i = \frac{p(x_i)}{q(x_i)} $$
4. Estimate expectation of function $h(x)$:
   $$ \mathbb{E}_p[h(x)] \approx \frac{\sum_{i=1}^n w_i h(x_i)}{\sum_{i=1}^n w_i} $$

### Limitations:
- **Efficiency depends heavily on the proposal distribution**.
- Poor proposals → very high variance in weights → unreliable estimates.
- Naive Monte Carlo with unweighted samples is **worse** when $p(x)$ is hard to sample from.

---

## Limits of Rejection and Importance Sampling

- **Rejection Sampling**:
  - May reject many candidates before accepting.
  - Very inefficient if $f(x)$ and $g(x)$ are not well-aligned.
- **Importance Sampling**:
  - May need many samples before the weighted average stabilizes.
  - Sensitive to outliers in weight ratios.
- In both, **choice of proposal distribution is crucial**.

---

## MCMC: Markov Chain Monte Carlo

- A solution when rejection and importance sampling are inefficient.

### Key Ideas:
- **Markov Chain**: A memoryless process where the future state depends only on the current state.
- **Monte Carlo**: Use of random sampling to estimate values.
- Combine them: **Construct a chain whose stationary distribution is the posterior**.

---

## Markov Chain Fundamentals

- Let $x^{(t)}$ be the state at time $t$.
- The chain evolves as:
  $$ x^{(t)} \to x^{(t+1)} $$
- **Markov Property**:  
  $$ p(x^{(t+1)} \mid x^{(t)}, x^{(t-1)}, ..., x^{(0)}) = p(x^{(t+1)} \mid x^{(t)}) $$

---

## Metropolis-Hastings Algorithm

- Goal: Sample from a distribution $\pi(x)$ (e.g. the posterior).
- Given a **proposal distribution** $q(y \mid x)$:

### Algorithm:
1. At current state $x$:
   - Propose $y \sim q(y \mid x)$
2. Compute:
   $$ a = \min\left(1, \frac{\pi(y) q(x \mid y)}{\pi(x) q(y \mid x)}\right) $$
3. Accept with probability $a$:
   - If accepted: $x^{(t+1)} = y$
   - Else: $x^{(t+1)} = x$

### Interpretation:
- **Uphill moves** (toward higher probability): always accepted.
- **Downhill moves**: accepted with probability proportional to how bad the move is.

---

## Skittles Example

### Problem Setup:
- Company is testing mango Skittles.
- Given 8 binary taste test responses.

### Model:
- Let $x_i \sim \text{Bernoulli}(\theta)$
- Prior:
  - $\alpha \sim \text{Normal}(0, 100)$
  - $\beta \sim \text{Normal}(0, 100)$
- Posterior: proportional to $p(\alpha, \beta) L(\alpha, \beta \mid \text{data})$

---

## Using pymc for MCMC

### Benefits:
- Abstracts away MCMC details.
- Runs multiple chains in parallel.
- Discards a **burn-in period** to allow convergence.
- Outputs samples for inference.

### Interpreting Results:
- pymc gives posterior samples for parameters.
- **$\hat{R}$ (r_hat)** statistic:
  - Measures convergence
  - $\hat{R} \approx 1$ → convergence
  - $\hat{R} > 1.3$ → concern (bad mixing or autocorrelation)

---