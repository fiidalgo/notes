# Clustering

---

## 1. What is Clustering?

**Goal**: Group similar data points into clusters without knowing labels (unsupervised learning).

- A **cluster** is a group where:
  - Points inside are similar.
  - Points outside are dissimilar.

**Examples**:
- Market segmentation
- Tissue classification in bioinformatics
- Image object separation

---

## 2. Challenges in Clustering

- "Similarity" is context dependent
- Number of clusters is unknown
- High-dimensional data is hard to visualize
- Distance metric matters

---

## 3. Data Notation

Let $X \in \mathbb{R}^{N \times d}$:
- $N$: number of samples (rows)
- $d$: number of features (columns)
- $\mathbf{x}_i$: row vector = $i^{th}$ sample

### Sample Mean:
$$
\bar{\mathbf{x}} = \frac{1}{N} \sum_{i=1}^N \mathbf{x}_i
$$

The sample mean represents the center (average) of the data along that feature dimension. The mean is a vector, so visualize it as being the center of mass of each column.

### Sample Covariance:
$$
\hat{\Sigma} = \frac{1}{N-1} \sum_{i=1}^{N} (\mathbf{x}_i - \bar{\mathbf{x}})^T (\mathbf{x}_i - \bar{\mathbf{x}})
$$

The sample covariance matrix defined above is a $d \times d$ matrix where the diagonal entries $\hat{\Sigma}_{jj}$ is the variance of feature $j$. The off-diagonal entries $\hat{\Sigma}_{jk}$ is the covariance between features $j$ and $k$. For example, suppose you measured height and weight. If taller people tend to weigh more, then the covariance between those two features is $> 0$, if there is no relation, then the covariance is $\approx 0$, and if taller people generally weigh less, then the covariance is $< 0$. The interpretation is that a positive covariance means the features are directly proportional, a negative covariance means they are inversely proportional, and a near zero covariance means they are not related.

---

## 4. Distance / Dissimilarity Metrics

Choosing a distance or dissimilarity metric is one of the **most critical decisions** in clustering. The "right" metric depends on:

- The shape of your clusters
- The scale and type of your features
- Whether features are correlated
- The presence of outliers
- The interpretability or goal of the analysis

Below are common metrics, followed by guidance on when and why to use each.

---

### L1 Norm (Manhattan Distance)
$$
d_{\text{Man}}(\mathbf{x}_j, \mathbf{x}_k) = \sum_{l=1}^{d} |x_{jl} - x_{kl}|
$$

- **Interpretation**: Total "city block" distance between points (sum of absolute differences).
- **When to use**:
  - When you want a metric that is **less sensitive to outliers** than L2.
  - When features are on similar scales or have been standardized.
- **Drawbacks**:
  - Doesn't emphasize large deviations as much as L2.
- **Best for**: Data with sparse features (e.g., text or categorical embeddings), or when robustness is needed.

---

### L2 Norm (Euclidean Distance)
$$
d_{\text{Euc}}(\mathbf{x}_j, \mathbf{x}_k) = \sqrt{ \sum_{l=1}^{d} (x_{jl} - x_{kl})^2 }
$$

- **Interpretation**: Straight-line distance in feature space.
- **When to use**:
  - When clusters are roughly spherical.
  - When magnitudes of differences matter.
- **Drawbacks**:
  - Highly sensitive to outliers and unscaled features.
- **Best for**: Well-behaved numeric data where clustering should reflect compactness.

---

### Mahalanobis Distance
$$
d_{\text{Mah}}(\mathbf{x}_j, \mathbf{x}_k) = \sqrt{ (\mathbf{x}_j - \mathbf{x}_k)^T \hat{\Sigma}^{-1} (\mathbf{x}_j - \mathbf{x}_k) }
$$

- **Interpretation**: Distance that accounts for scale and correlations between features.
- **When to use**:
  - When features are correlated.
  - When clusters are elliptical, not spherical.
- **Drawbacks**:
  - Requires good estimates of the covariance matrix (not ideal for small $N$ or high $d$).
- **Best for**: Statistical applications, anomaly detection, and Gaussian-based models.

---

### Cosine Dissimilarity
$$
d_{\text{cos}}(\mathbf{x}_j, \mathbf{x}_k) = 1 - \frac{\mathbf{x}_j \cdot \mathbf{x}_k}{\|\mathbf{x}_j\|_2 \|\mathbf{x}_k\|_2}
$$

- **Interpretation**: Measures the angle between vectors.
- **When to use**:
  - When the **direction** of the vector matters more than its magnitude.
  - When data are sparse or represent frequency counts (e.g., TF-IDF in text).
- **Drawbacks**:
  - Ignores magnitude, so two very different-sized vectors pointing the same way look similar.
- **Best for**: Text, gene expression, or image pixels.

---

### Connecting to Clustering Challenges

#### Challenge: **Similarity is context-dependent**
- This section directly addresses this challenge by showing that different metrics encode **different notions of similarity**:
  - Euclidean = geometric closeness
  - Cosine = orientation
  - Mahalanobis = statistical similarity
  - Manhattan = robust component-wise distance
- The choice affects what clusters are discovered.

#### Challenge: **Number of clusters is unknown**
- Some metrics (like Mahalanobis) emphasize local density or structure, which can **bias algorithms like K-means** into finding more or fewer clusters depending on how dissimilarity is measured.

#### Challenge: **High-dimensional data is hard to visualize**
- In high dimensions, Euclidean distances tend to concentrate (i.e., all distances become similar).
- Cosine distance is often **better for high-dimensional sparse data** because it measures angular separation.
- Mahalanobis helps when features are correlated — which is common in high-dimensional biological data.

#### Challenge: **Distance metric matters**
- This section shows **why metric choice affects clustering outcome**.
- Using the wrong metric (e.g., Euclidean on highly skewed or correlated features) can distort results.
- Preprocessing (standardization or PCA) may be needed before applying certain metrics.

---

## 5. Preprocessing and Scale

Clustering results can be **heavily influenced by the scale, skewness, or variance** of features. Distance metrics like Euclidean and Manhattan operate on raw numerical differences so unprocessed features can distort dissimilarity calculations and bias clustering.

There are three common preprocessing strategies:

---

### A. Standardization (Z-score normalization)

$$
x_{ij} \rightarrow \frac{x_{ij} - \bar{x}_j}{\sqrt{\hat{\Sigma}_{jj}}}
$$

This transformation centers each feature around 0 and scales to unit variance.

#### Effects:
- Makes all features equally weighted (regardless of original scale)
- Eliminates units (dimensionless features)
- Makes Euclidean and Manhattan distances fair and comparable across features

#### When to use:
- When features have different units (e.g., height in cm vs. weight in kg)
- When using distance metrics sensitive to scale (like L2, L1)
- When the distribution is roughly symmetric or bell-shaped

#### When *not* to use:
- When features are already unitless and comparable
- If using a model that can internally learn feature weights (e.g., tree-based methods)
- When data are highly skewed — use a transformation first

---

### B. Min-Max Normalization

$$
x_{ij} \rightarrow \frac{x_{ij} - \min(x_j)}{\max(x_j) - \min(x_j)}
$$

Scales each feature to the $[0, 1]$ range.

#### Effects:
- Preserves the shape of the original distribution (does not change spread)
- Makes all features bounded to the same range
- Sensitive to outliers (min and max values can dominate)

#### When to use:
- When features are on vastly different scales but all bounded (e.g., pixel intensities, percentages)
- When clustering with algorithms that assume features are bounded (e.g., neural nets)

#### When *not* to use:
- When data contain significant outliers (use robust scaling or standardization instead)

---

### C. Nonlinear Transformations

Common ones:
- $\log(x)$
- $\log(1 + x)$ (avoids issues with $x=0$)
- $\text{arcsinh}(x)$ (handles both signs and zero)
- Box-Cox or Yeo-Johnson transformations

#### Effects:
- Reduce **skewness** and **compress large values**
- Make long-tailed distributions more symmetric
- Help distance metrics treat spread-out values more fairly

#### When to use:
- When data are **positively skewed** or follow a power-law distribution
- When large values dominate dissimilarity (e.g., income, population)
- Before standardization or normalization to stabilize variance

#### When *not* to use:
- When features are already symmetric
- When the scale of the variable is meaningful (e.g., geographic distance)

---

### Why Preprocessing Matters in Clustering

#### Relating to Distance Metrics:
- L2 and L1 distances are **scale-sensitive**, so unprocessed data can bias clustering toward features with large variance or large units
- Mahalanobis distance incorporates covariance, so it **partially accounts for scaling**, but still benefits from preprocessing
- Cosine distance is **scale-invariant**, but can still be affected by skewed distributions — sometimes it’s better to normalize vectors to unit norm

#### Relating to Challenges:
- **Similarity is context-dependent**: preprocessing aligns features to the same scale so distance truly reflects similarity
- **High-dimensional data**: standardized and transformed features make comparisons more meaningful when we can't visualize the space
- **Distance metric matters**: proper preprocessing ensures your chosen distance metric behaves as expected

---

## 6. K-Means Clustering

K-Means is one of the most popular clustering algorithms. It assumes that data can be grouped into $K$ **compact, spherical** clusters using Euclidean distance.

### Objective:
Minimize total **within-cluster variance**:
$$
\sum_{k=1}^K \sum_{i \in C_k} \| \mathbf{x}_i - \mathbf{m}_k \|_2^2
$$
where $\mathbf{m}_k$ is the centroid (mean) of cluster $C_k$.

### Algorithm (Lloyd's Algorithm):
1. Initialize $K$ cluster centers (randomly or using K-means++)
2. Assign each point to the nearest center
3. Recompute cluster centers
4. Repeat 2 amd 3 until convergence (no reassignment or max iterations)

---

### Strengths:
- **Fast and scalable** (linear in $N$ under most conditions)
- Cluster centers are **interpretable**
- **Incremental updating** possible for new data
- Easy to implement and often a good baseline

### Weaknesses:
- **Must specify $K$ in advance**
- Sensitive to **initialization** (bad seeds → poor local optima)
- Biased toward **spherical clusters**
- Sensitive to **outliers** and **scale**
- No notion of "outlier" or "noise"

### Optimizations:
- Use **K-means++** for better seeding
- Run **multiple initializations** and keep the best
- Combine with **preprocessing** (e.g., PCA, standardization)

---

### Addresses which clustering challenges?
- **Similarity is context-dependent**: K-means uses **Euclidean distance**, so choice of metric and preprocessing is critical
- **Unknown $K$**: Requires estimating $K$ using tools like the **elbow method**, **gap statistic**, or **silhouette score**
- **High-dimensionality**: Can work poorly without dimensionality reduction like PCA
- **Distance metric**: Hardcoded to Euclidean; not robust to correlated or skewed features

---

### When to use:
- Data has **compact, spherical** clusters
- You need **fast** clustering for large $N$
- Clusters are **roughly equal in size**

### When *not* to use:
- Non-spherical, varying-density clusters
- Outlier-heavy data
- When the correct number of clusters $K$ is completely unknown and difficult to estimate

---

## 7. Choosing the Number of Clusters

### A. Elbow Method
Plot total within-cluster sum of squares vs. $K$. Look for the **"elbow"**: the point beyond which adding clusters yields diminishing returns.

- **Pros**: Fast, simple
- **Cons**: Often no clear elbow; subjective

---

### B. Gap Statistic (Tibshirani et al., 2001)

Compares clustering performance to null distribution (randomly distributed reference data):
$$
\text{Gap}(K) = \mathbb{E}_{\text{null}}[\log(W_K)] - \log(W_K)
$$

Where $W_K$ is the within-cluster variation for $K$ clusters.

#### Steps:
1. Compute $W_K$ on actual data
2. Generate $B$ random datasets
3. Compute $W_K^{(b)}$ for each
4. Compare log-average to original

#### Pros:
- Statistically grounded
- Can suggest **whether any clustering structure exists at all**

#### Cons:
- **Tends to underestimate $K$** (conservative)
- Results vary with sampling
- **Computationally expensive**

---

### C. Silhouette Score

For each point:
$$
s_i = \frac{b_i - a_i}{\max(a_i, b_i)}
$$

Where:
- $a_i$ = average distance to same cluster
- $b_i$ = average distance to **nearest other cluster**

Average silhouette $\bar{s}$ measures overall clustering quality.

#### Pros:
- Applicable with any dissimilarity
- Diagnostic per point or per cluster

#### Cons:
- Requires pairwise distance computation (slow for large $N$)
- Can be misleading when clusters are imbalanced

---

## 8. DBSCAN (Density-Based Spatial Clustering of Applications with Noise)

Unlike K-means, DBSCAN finds **arbitrarily shaped** clusters by detecting **dense regions** in the data.

### Key Parameters:
- $\varepsilon$ (eps): radius for local neighborhood
- MinPts: minimum number of points within $\varepsilon$ for a core point

### Point Types:
- **Core**: ≥ MinPts neighbors within $\varepsilon$
- **Border**: near a core but not dense
- **Noise**: not in any cluster

---

### Strengths:
- **No need to specify $K$**
- Detects **arbitrary shapes**
- Identifies **outliers** as noise
- Works well with **spatial data**, **image blobs**, etc.

### Weaknesses:
- **Highly sensitive** to eps and MinPts
- **Struggles with variable-density clusters**
- Not effective in **high dimensions** (distance concentration)

---

### Addresses which clustering challenges?
- **Unknown $K$**: No need to choose $K$
- **Noise/outliers**: Explicitly labeled
- **Similarity is context-dependent**: You can define your own distance function (not restricted to Euclidean)

### When to use:
- Data has **non-spherical clusters**
- You expect **noise or outliers**
- Data has **spatial or geographic structure**

### When *not* to use:
- Data with **strongly varying densities**
- **Very high-dimensional** data (distance loses meaning)
- When $\varepsilon$ and MinPts are hard to tune

---

## 9. t-SNE (t-Distributed Stochastic Neighbor Embedding)

t-SNE is a **nonlinear dimensionality reduction** technique used for **visualizing high-dimensional data** in 2D or 3D.

- Preserves **local structure** (neighborhoods)
- Useful for **visual inspection** of clustering structure

---

### Strengths:
- Reveals **cluster-like structure** in high-dim space
- Preserves **local similarities** well
- Helpful for **debugging preprocessing/clustering**

### Weaknesses:
- Not a clustering method itself
- **No interpretability** in axes
- Sensitive to hyperparameters (perplexity, learning rate)
- Cannot embed new points easily (non-parametric)

---

### Addresses which clustering challenges?
- **High-dimensionality**: Provides a 2D/3D view for inspection
- **Visual evaluation**: Reveals separation and overlap between clusters
- **Distance metric matters**: Works well with many dissimilarity inputs

### When to use:
- **Visualizing high-D data**
- Diagnosing clustering quality
- Before or after applying clustering (like K-means or DBSCAN)

### When *not* to use:
- When quantitative clustering output is needed
- As a standalone method for identifying clusters

---

## 10. Clustered Regression

Clustering can be used to improve supervised learning by allowing separate models for different groups.

### Workflow:
1. **Cluster** the data into $K$ subgroups
2. Fit **separate regression models** to each subgroup

### Benefits:
- **Captures heterogeneity** in relationships
- Improves predictive performance
- Reveals **group-specific dynamics**

### Example:
In the Boston housing dataset:
- Global model $R^2 = 0.65$
- Clustered regression: $R^2 = 0.82$

---

### When *not* to use clustering:
- Small sample sizes (overfitting risk)
- When clusters are not meaningful for prediction