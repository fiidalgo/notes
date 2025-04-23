# Clock Auctions Augmented with Unreliable Advice
*Gkatzelis et al.*

---
## Problem
* Clock auctions are great in practice but have poor worst-case performance
* Without knowing anything about bidder values, clock auctions can only guarantee a $O(\log n)$ approximation to optimal social welfare. This means that in the worst case, the sum of values of winners achieved by a clock auction is at most $\frac{1}{O(\log n)} \cdot (\text{Optimal Social Welfare})$ i.e., clock auctions can be exponentially worse than the optimal outcome but only by a logarithmic factor in the number of bidders. Suppose the auctioneer has no prior knowledge of bidders' values, bidders drop out only based on prices offered in each round, and bidders' values and the feasibility constraints are structured adversarially. In this case, the auction may fail to serve high-value agents early on because they drop out before the auction figures out who they are. So, an adversary can construct a bidding environment where a small number of bidders have very high value (optimal to select them) and the auction gradually loses these high-value bidders early due to uniform or incremental pricing. The remaining feasible set yields only a logarithmic fraction of the optimal value. The goal of the paper is construct a feasibility constraint where there are many feasible subsets and only one of them gives very high total value. Then, set bidder values such that the high-value set is not easily discoverable by price signals and the auction can't distinguish between good and bad bidders until it's too late. Then, show that any deterministic price increase rule will lose the high-value agents before reaching the correct set, and lastly, prove that in this scenario, the mechanism gets at most $O(1 / \log n)$ of the optimal welfare.
* The limitation is not computational, it's informational: the auction doesn't know enough without bids or predictions to safely identify high-value winners early enough.
* In summary, for example, imagine there is 1 bidder with value 1000 and 10 with value 10 and the constraint is that only winner can be chosen. If the clock auction raises prices too fast, the high-value bidder may drop out at 999 anticipating large increments, so the mechanism might instead select one of the 10 bidders as the winner. The problem in short is that the auctioneer doesn't know who's valuable until they're gone, so they could force them to drop out accidentally and achieve a worse welfare.
## Clock Auction Background
* Clock auctions are dynamic, iterative auctions where prices increase over time and bidders drop out when prices exceed their value
* Benefits: obvious strategy-proofness (dominant strategy is to stay while price $\le$ value), unconditional winner privacy (winners never have to report their true values), transparency
* In standard clock auctions, without extra information, it's shown that no deterministic clock auction can achieve better than a $O(\log n)$ approximation in general settings.
## Augmented Clock Auctions
* The goal is to design clock auctions that leverage machine-learned predictions about the optimal winner set to perform near-optimally when the prediction is good and still guarantee non-trivial welfare when the prediction is bad
* Mechanism 1: Follow-The-Unpredicted-Leader
	* Receive a predicted winner set $\hat{OPT}$ from an external source (e.g., ML model)
	* Alternate between pushing unpredicted bidders (those not in $\hat{OPT}$) to raise prices and pushing predicted bidders (from $\hat{OPT}$) if welfare threshold isn't reached
	* Stop when feasible set with target welfare is reached
	* Parameters: $\gamma$: how aggressively to target predicted bidders, $\epsilon$: desired closeness to optimality
	* Error-tolerant variant: modifies this mechanism to tolerate cases where $\hat{OPT}$ isn't exactly optimal but still good enough by introducing parameter $\eta$ to bound how far off $\hat{OPT}$ can be from true optimum.
	* Inputs: 
		* Feasibility system $\mathcal{F}$: defines valid subsets of winners
		* Predicted good set of bidders $\hat{OPT} \subseteq N$ 
		* $\gamma > 0$: inflation factor for welfare threshold, $\epsilon > 0$: how close we want to be to optimal (consistency)
	* Step 1: initialize all prices at 0
	* Step 2: in each phase, do:
		* Run a water-filling clock auction (WFCA) pass over unpredicted bidders $N \setminus \hat{OPT}$ and increase their prices until total welfare of current feasible set exceeds $\gamma \cdot v(\hat{OPT})$ where $v(\hat{OPT})$ is the total welfare of the predicted winner set $\hat{OPT}$ from our ML model or everyone drops out and if not successful, run WFCA on predicted bidders $\hat{OPT}$ 
		* WFCA is a clock auction subroutine introduced in Christodoulou et al., 2016
			* Raise prices uniformly for all bidders still in the auction until one of two things happens:
				* A feasible set of bidders emerges that satisfies constraints (e.g., size $\le k$)
				* A bidder drops out (their value is below the current price)
	* Step 3: terminate when a feasible set exceeds welfare target or all bidders drop out
	* Step 4: return the last feasible set and the prices at which bidders dropped out
* Mechanism 2: Follow-The-Binding-Benchmark
	* Second design that achieves stronger consistency (consistency$\infty$)
	* Still strategy-proof but robustness becomes polynomial rather than logarithmic
## Example: 4 bidders, 2 can win
* Setup: bidders A, B, C, D, each with private value: $A \rightarrow 8, B \rightarrow 6, C \rightarrow 2, D \rightarrow 1$, feasibility constraint: can choose 2 bidders max, predicted optimal set $\hat{OPT}=\{ C,D \}$ (this is an incorrect prediction! they're low value)
* Run Mechanism 1: FTUL
	* Try WFCA on $N \setminus \hat{OPT} = \{ A, B \}$. Start at price 0 and increase uniformly:
	* Price: 0, Active bidders: A, B
	* Price: 1, Active bidders: A, B
	* ...
	* Price: 6, A, B
	* 7, A (B drops)
	* 8, - (A drops)
	* Suppose we set $\gamma=2$ and $v(\hat{OPT})=3$ as the values of C + D is 3. That means our welfare target is $2 \cdot 3 = 6$. Before B drops, the active set is $\{ A, B \}$, welfare = $14 \ge 6$ so our target is met. So, the winners are A and B and the mechanism succeeds even though the advice as bad
* Run Mechanism 2: FTBB
	* Binds to value of $\hat{OPT}=\{ C, D \} = 3$
	* Suppose $\alpha=1.5$, then our target is $\alpha \cdot v(\hat{OPT}) = 1.5 \cdot 3 = 4.5$
	* Try WFCA  on $\hat{OPT} = \{ C, D \}$
	* Start price at 0, C drops at 2, D drops at 1, welfare never exceeds 3, so this fails
	* Now try WFCA on $N \setminus \hat{OPT} = \{ A,B \}$
	* This results in a total value of 14 which meets our target
	* So FTBB also succeeds in this case