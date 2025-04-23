# HyperLogLogLog: Cardinality Estimation With One Log More
*Karppa and Pagh*

---
## Problem
* Improving cardinality estimation (process of estimating number of distinct elements in a dataset) with more efficient sketch compression. 
	* Storing every unique ID to count them is slow and memory-intensive in practice, so you want to use a sketch (data structure that gives you close estimate of number of unique elements (cardinality) using way less memory than storing all of the data)
## Sketch Background
* A sketch is a compact summary of data (not the full data) which allows you to estimate something (like count of unique elements)
* Goal is to get a good enough answer while using much less space
## HyperLogLog (HLL) Background
* HLL is one of the most famous sketches for cardinality estimation
* Invented by Flajolet et al. in 2007
* Requires just few KB to estimate billions of unique items
* Used in Google BigQuery, Redis, Apache DataSketches, and network traffic monitoring
* It works by hashing each element (like a user ID) into a bitstring of some width (suppose 32 for example), defining a number of bits $k$ we use to split the elements into buckets (suppose $k=2$ for example, then the 2 MSBs determine which of the $2^k=2^2=4$ buckets this value will go into), storing the maximum position of first 1-bit in the respective bucket (suppose bucket 00 currently has value 3, then you get another bitstring that belongs to bucket 00 and it is 00001, then bucket 00 gets updated to 5 as the position of first 1-bit is greater than the current maximum which was stored in that bucket), then using a formula (harmonic mean) to estimate how many unique values must have caused those observations
	* HLL estimates cardinality with $\hat{n}=\alpha_m\cdot m^2 \cdot \left( \sum_{i=1}^{m} 2^{-R[i]} \right)^{-1}$ where $R[i]$ is the value in bucket $i$, $m$ is the number of buckets, and $\alpha_m$ is a correction constant which depends on $m$.
	* We multiply by $m^2$ because if you have $m$ buckets, each is like an independent estimator of the cardinality. When you compute $Z = \sum_{i=1}^{m} 2^{-R[i]}$ you get an average signal across buckets. Then $m^2 / Z$ gives an estimate that scales with the number of buckets used and their combined behavior. 
	* Even with clever hashing and aggregation, this estimator is still slightly biased especially for small $m$. So, Flajolet et al. empirically derived $\alpha_m$ to correct this bias. $\alpha_m = \left( m \cdot \int_0^\infty \left( \log_2 \left( \frac{2+u}{1+u}\right) \right) ^m du \right) ^{-1}$ For practical purposes, they fit it numerically for various values of $m$: $m=16, \alpha_m=0.673$, $m=32, \alpha_m=0.697$, $m=64, \alpha_m=0.709$. An $m$ smaller than 16 (like the 4 used in my example) is rare due to the high estimation error that comes with it.
* The issue with HLL is that despite being compact, it still stores each bucket using $\log \log n$ bits (where $n$ is the num of unique elements as suppose you want to count up to $n=10^9$ elements, then you need to be able to store roughly $\log_2n \approx 30$ values as the $\rho$ values can range up to 30, meaning that each bucket (register) needs to store numbers ranging from 0 to 30 which requires $\log_2 30=5$ bits per register, so $\log \log n$) which is *okay* but we could do better. We can shave off another 1-2 bits per register with HLLL.
## HyperLogLogLog (HLLL)
* HLLL is a compressed version of HLL. Still used for cardinality estimation ($n$ elements in a data stream using $m$ registers)
* Compresses registers from $O(m \log \log n)$ to $m \log \log \log m + O(m + \log \log n)$ bits
* HLLL stores an array of $m$ registers where each one holds a position of the leftmost 1-bit ($\rho$ value from earlier) after hashing a stream element. It estimates how spread out the hashes are which indirectly tells us how many distinct elements we've seen
	* $M=[r_1, r_2, \ldots, r_m]$ where $r_i \approx \log_2(n)$ for that bucket
	* Each $r_i$ is a small integer (typically 5-6 bits) representing how far we had to go in the hash to find the first 1-bit. So for large $n$, you get large $\rho$ values
* The difference here is that in HLL $M$ is a fixed array of length $m$ with $\log \log n$ bits per value. HLLL $M$ stores an offset from a base value $B$ so it uses fewer bits. Suppose the HLL $M$ is $M= [5, 3, 0, 7, 6, 2, 4, 5]$, we can select a value of a hyperparameter $k$ to be the range. So in our HLLL $M$, we can store the range $[B, B+2^k - 1]$. Suppose we choose $k=3$ and $B=2$, then we can store the range $[2, 9]$ in our $M$ in HLLL. Fine-tuning these parameters depending on your $\rho$ values is extremely important. In this example, our HLLL $M$ would be $M=[3, 1, -, 5, 4, 0, 2, 3]$ and then we store the values that are outside that range (in this case, that's just the value 0) in our sparse dictionary $S$ where the key is the index and the value is the register value. So in this case, $S=\{ 2 \rightarrow 0 \}$. Therefore, in HLLL our final register value is $M[j] + B$ or $S[j]$ if $j \in S$.
* Step 1: Initialization
	* In HLL, we set $M$ to be an array of length $m$ where each value is 0.
	* In HLLL, we set $M$ to be an array of length $m$ where each value is 0 (using only $k$ bits per entry), $B=0$, and $S=\{ \}$. 
* Step 2: Update(y)
	* In HLL we hash $y \rightarrow 64$-bit hash $h(y)$, then split it into its top bits for the bucket index $j$ and its bottom bits and compute $r = \rho (h(y))$ and set $M[j]=\max (M[j], r)$. 
	* In HLLL we follow the same first steps where we hash y and split into its top and bottom bits and find $j$ and $r$ therefore. Now, we get current value at $j$ which is either $M[j] + B$ or $S[j]$. If $r >$ current value at $j$, then if $r \in [B, B+ 2^k - 1]$ we store $r - B$ in $M[j]$ using only $k$ bits. Otherwise, we store $S[j] = r$. 
	* There's also a compress() step done after each update in order to find the optimal $B$. It does this by searching for a new $B'$ that would allow as many values as possible be encoded into the dense $M$ array instead of the sparse $S$ dict. The goal is to min the total bit usage. For the dense part, we use $m \cdot k$ bits, and for the sparse part we use $|S| \cdot (\log m + \log w)$ bits as each entry in $S$ is a $(j,r)$ pair, and $j$ needs $\log_2 m$ bits to index into the $m$ registers, and $r$ needs $\log_2 w$ bits to hold the full register value ($\rho$ values go up to $w$ since they come from 64-bit hashes). So in the example where input data are 64-bits wide, $w=64$. It's the original bitwidth before hashing.
		* The authors created an HLLL* which is HLLL with added heuristics about when to call compress() instead of always choosing to compress after each update. Heuristics are approximate strategies used to make algorithms faster or more practical often at the expense of optimality. HLLL* triggers compress() only when a sparse insert is needed. Also, instead of trying all candidate $B' \in [0,w]$, HLLL* just tries the next higher $B'$ to fit new value. Also, HLLL* skips the compress() step if it obviously won't help
* Step 3: Estimate()
	* Both use the same estimation formula, except that with HLLL you have to do a lookup in two places ($M$ + offset or $S$) to get the value of a register
## Empirical Tests
* Experimental setup:
	* Register sizes ($m$): From $2^4$ to $2^{18}$
	* Stream size ($n$): From $2^4$ to $2^{30}$
	* Input data types: 64-bit random integers, 8-character ASCII strings, synthetic $(j,r)$ pairs (avoids hashing overhead)
	* 10 independent repetitions per setting
	* All experiments done in RAM to eliminate I/O bias
	* Compared HLL, HLLL, and some variants of both
* Findings:
	* Sketch size: HLLL reduces sketch size by ~40% on average vs HLL. Best compression is for large $m$ 
	* Update time: HLLL is slower than HLL but HLLL* (with heuristics) achieves amortized constant time for large $n$ competitive with HLL. HLLL* is the same as HLLL except that it implements some additional rules that shortcut or bypass finding the most optimal $B$ at the benefit of update time
	* Merge time: HLLL merges are slower than HLL due to post-merge compress() call but remain efficient.
	* Tradeoff: HLLL* is space-optimal at very little cost in runtime. It's preferable when saving memory is key.
## Theoretical Formulation
* HLLL is not only backed by experiments, but by theory to.
* For most registers you can use $\log \log \log m + O(1)$ bits to store their offset with high probability. The sparse representation is bounded by $O(m)$ bits because there are only $\frac{m}{\log m}$ outliers and each takes $\log m$ bits
* Given $n \gg m$, updates take constant time on average, even with the rebase operations
* Since it uses the same estimation formula as HLL, the accuracy is preserved.
## Strengths
* Clear theoretical formulation with derivation of compression bounds, adapts known probabilistic bounds from HLL and shows tight control over outliers
* Strong practical relevance
* Thorough implementation
* Insightful evaluation (tested over many parameters, fair comparisons to industry tools, demonstrates clear memory savings with tolerable runtime cost)
## Weaknesses
* This is an optimization, not a new estimation method. HLLL doesn't improve estimation accuracy, just space
* Compression routine is complex. Although amortized constant time, worst-case updates can be very expensive.
* Sparse map can grow very large and having a large $S$ will hurt performance
* Doesn't address non-mergeable sketches or alternative strategies like MinCount
## Verdict
* I score it a 4/5 after reading over my peers' comments. The paper presents a theoretically sound and practically valuable optimization of the widely used HLL algorithm. It compresses registers via relative offsets and sparse outlier handling, reducing memory footprint by up to 40% without sacrificing estimation accuracy or mergeability. Their implementation is reproducible and they evaluated against major industrial benchmarks. The reason I don't think it deserves a 5 is because it doesn't present a novel cardinality estimation technique or theory, instead it extends an existing solution but only in one direction: space, not in the accuracy direction. It's a good systems paper, but not the greatest theory paper. I'm also concerned about the tests using uniform input data. In practice, estimation often involves skewed or heavy-tailed distributions.