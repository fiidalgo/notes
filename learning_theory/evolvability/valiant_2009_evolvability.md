# Evolvability
*Leslie Valiant, Feb 3, 2009*

## Introduction
* If evolution was just a random search, it would require exponential time (too long)
* A *necessary* condition for evolution to a target is the *existence* of an evolutionary path towards it consisting of small steps
* Computational learning theory can be viewed as delineating limits on the complexity of functions that can be acquired with feasible resources without an explicit designer or programmer
* Goal of the paper is to give a quantitative theory of the evolution of mechanisms
* Four notions:
    * Motivated by the biology of cells consisting of thousands of proteins and operating with circuits with complex mechanisms, the paper concerns mechanisms that can evaluate many-argument functions
    * Any many-argument function has measurable performance determined by values of function on inputs from a probability distribution over the conditions that arise
    * For any function, only a limited number of variants can be explored per generation (through mutations or recombination) since there is a limitation to population size for organisms
    * Requirement that mechanisms with significant improvements in performance evolve in a limited number of generations
* Evolvability is a restricted case of PAC learnability
* Distinguishing between effects of nature and nurture on behavior is hard, so this paper's framework will help
* In PAC learning, an update to a hypothesis may depend on the particular examples presented, on the values computed on them by the hypothesis, and on the values of the functions to be learned on those examples (permitted by polynomial time computation) [Valiant 1984]
* Slightly more restricted than that, in Statistical Query (SQ) model [Kearns 1998] the updates depend only on the result of statistical queries on the distribution of examples
    * These queries can ask about the percentage of examples that have certain syntactic (relating to syntax) descriptions
* In evolution we assume updates depend *only* on the aggregate performance of the competing hypotheses on a distribution of examples or experiences and in no additional way on the syntactic descriptions of the examples (cannot look at individual examples nor their syntax, makes it harder to know why a trait worked)
    * This reflects the idea that the relationship between genotype and phenotype may be complicated and the evolution algorithm doesn't understand it
    * The function classes that are evolvable are therefore a subset of the those that are SQ learnable
* Results of the evolvability of specific classes of functions:
    * Paper shows that the classes of monotone Boolean conjunctions and disjunctions are evolvable over the unifrom distribution of inputs
    * Class of Boolean parity functions is not evolvable over the uniform distribution as it is not SQ learnable, and since this class is known to be learnable, we can conclude that evolvability is more constrained than learnability
* **Goal**: Quantitative explanations of which mechanisms can evolve using only feasible resources and which cannot

## Many-Argument Functions
* Structures or circuits of living cells have to respond to variations in internal and external conditions. Represent the conditions as the values of a number of variables $x_1, \ldots, x_n$, each of which may correspond, for example, to the output of some previously existing circuit
* Responses that are desirable for an organism under various combinations of values of the variables $x_1, \ldots, x_n$ are viewed as values of an ideal function $f(x_1, \ldots, x_n)$
* $f$ has low value in circumstances when such a low value is most beneficial and high in circumstances when a high value is most beneficial
* A parity function is odd or even. An odd (even) parity function $f$ over $x_1, \ldots, x_n$ has a value $+1$ if and only if an odd (even) number of the variables in a subset $S$ of the variables have value $+1$
    * Example: An odd parity function over $\{ x_1, x_2, x_3, x_4 \}$ with $S=\{ x_1, x_3, x_4 \}$ has value $+1$ if $x_1 = x_2 = x_3 = x_4 = +1$ and value $-1$ if $x_1=x_2=x_3=+1$ and $x_4=-1$
* Evolving parity functions over $n$ variables (for an arbitrary unknown subset $S$) either the number of generations or the population size of the generatiosn need t obe exponentially large in terms of $n$
* Contrastly, there are classes with similar substantial structure that are evolvable, such as monotone conjunctions, defined as conjunctions of a subset $S$ of the literals $\{ x_1, x_2, \ldots, x_n \}$. An example of a conjunction is the function that is true if and only if $x_1=x_4=+1$, then we abbreviate the function as $x_1x_4$
* $X_n$ is the set of all $2^n$ combinations of values that the variables $x_1, \ldots, x_n$ can take and the set $S$ is unknown to the evolution algorithm and the challenge for it is to approximate it from among the more than polynomially many possibilties
* $D_n$ is a probability distribution over $X_n$ that describes relative frequency with which the various combinations of variable values for $x_1, \ldots, x_n$ occur in the context of the organism

*Definition 2.1.* The performance of function $r : X_n \rightarrow \{ -1, 1 \}$ with respect to ideal function $f : X_n \rightarrow \{-1, 1 \}$ for probability distribution $D_n$ over $X_n$ is $$\text{Perf}_f(r, D_n)=\sum_{x \in X_n}f(x)r(x)D_n(x)$$

* The performance is a measure of the correlation between ideal function $f$ and a hypothesis $r$ we have at hand which is always a real number in the range $\left[ -1, 1 \right]$
* Performance is $1$ if $f$ is identical to $r$ on points with nonzero probability in $D_n$. Performance is $-1$ if there is perfect anti-correlation on these points
* Every time the organism has an experience in the form of a set of values $x \in X_n$ it will undergo a benefit amounting to $+1$ if its circuit $r$ agrees with the ideal $f$ on that $x$ or a penalty $-1$ if $r$ disagrees
* Over a sequence of life experiences (i.e., different points in $X_n$) the total of all benefits and penalties will be accumulated
* Organisms or groups for which this total is high will be selected preferentially to survive over organisms or groups with lower totals
* Paper elects to discuss which ideal functions give rise to landscapes which allow them to evolve
* An organism or group will be able to test the performance of a function $r$ by sampling a limited set $Y \subseteq X_n$ of size $s(n)$ of inputs or experiences from $D_n$ where for simplicity we assume the $Y$ are chosen independently for the various $r$

*Definition 2.2.* For a positive integer $s$, ideal function $f : X_n \rightarrow \{-1, 1 \}$ and probability distribution $D_n$ over $X_n$ the empirical performance $\text{Perf}_f(r, D_n, s)$ of function $r : X_n \rightarrow \{ -1, 1 \} is a random variable that makes $s$ selections independently with replacement according to $D_n$ and for the multiset $Y$ so obstained takes value $$s^{-1}\sum_{x \in Y}f(x)r(x)$$

* Evolution is able to proceed from any starting point as otherwise if some reinitialization process were permitted then proceeding to reinitialized state from another state might incur a large decrease in performance

## Definition of Evolvability
* Given the existence of $f$, we ask whether it is possible to evolve a circuit for a function $r$ that closely approximates $f$
* Class $C$ of ideal functions $f$ is evolvable if any $f$ in the class $C$ satisfies two conditions
    * (i) From any starting function $r_0$ sequence of functions $r_0 \Rightarrow r_1 \Rightarrow r_2 \Rightarrow r_3 \Rightarrow \ldots$ will be such that $r_i$ will follow from $r_{i-1}$ as a result of a single step of mutation and selection in a moderate size population
    * (ii) After moderate number $i$ of steps $r_i$ will have a performance value significantly higher than the performance of $r_0$ so that it is detectable after a moderate number of experiences
* Conditions are sufficient for evolution to start from any function and progress towards $f$
* $C$ is a class of ideal functions and $R$ is a class of representations of functions from which the organism will choose an $r$ to approximate the $f$ from $C$
* We assume the representation from $R$ is polynomial evaluatable (there is a polynomial $u(n)$ such that given the description of an $r$ in $R$ and an input $x$ from $X_n$, the value of $r(x)$ can be computed in $u(|r|)$ steps)
* $\epsilon$ is the error of the evolution, describes how close to optimality the performance of the evolved representation has to be

*Definition 3.1.* For a polynomial $p(\cdot, \cdot)$ and a representation class $R$ a p-neighborhood $N$ on $R$ is a pair $M_1, M_2$ of randomied polynomial time Turing machines such that on input the numbers $n$ and $\lceil 1/\epsilon \rceil$ and a representation $r \in R_n$ act as follows: $M_1$ outputs all members of a set $\text{Neigh}_N(r,\epsilon) \subseteq R_n$ that contains $r$ and may depend on random coin tosses of $M_1$ and has size at most $p(n, 1/\epsilon)$. If $M_2$ is then run on this output of $M_1$, it in turn outputs one member of $\text{Neigh}_N(r,\epsilon)$ with member $r_1$ being output with a probability $\text{Pr}_N(r,r_1) \ge 1/p(n, 1/\epsilon)$.

* For each genome, the number of variants determined by $M_1$ that can be searched effectively is not unlimited because the population at any time is not unlimited but is polynomial bounded
* Significant number of experiences with each variant, generated by $M_2$, must be available so that differences in performance can be detected reliably
* One implementation is to call $R$ the set of possible genomes, restrict mutations to a fixed constant number of base pairs, and call genome length as polynomial in the relevant $n$
* Consider exponentially many such variants to be impractical while modest polynomial bounds like $n$ or $n^2$ are feasible
* Goal is to show that population sizes, number of generations, and numbers of computational steps all have to be upper bounded by a polynomial in the number of variables on which a circuit depends and in the inverse of the error

*Definition 3.2.* For error parameter $\epsilon$, positive integers $n$ and $s$, an ideal function $f \in C_n$, a representation class $R$ with $p(n, 1/\epsilon)$-neighborhood $N$ on $R$, a distribution $D$, a representation $r \in R_n$ and a real number $t$, the mutator $\text{Mu}(f, p(n, 1/\epsilon), R, N, D, s, r, t)$ is a random variable that on input $r \in R_n$ takes a value $r_1 \in R_n$ determined as follows: For each $r_1 \in \text{Neigh}_N(r, \epsilon)$ it first computes an empirical value of $v(r_1) = \text{Perf}_f(r_1, D_n, s)$. Let $\text{Bene}$ be the set $\{ r_1 \mid v(r_1) \ge v(r) + t \}$ and $\text{Neut}$ be the set difference $\{ r_1 \mid v(r_1) \ge v(r) - t\} - \text{Bene}$. Then

(i) if $\text{Bene} \neq \phi$ then output $r_1 \in \text{Bene}$ with probability $\text{Pr}_N(r,r_1)/\sum_{r_1 \in \text{Bene}}\text{Pr}_N(r,r_1)$

(ii) if $\text{Bene} = \phi$ then output $r_1 \in \text{Neut}$, the probability of a specific $r_1$ being $\text{Pr}_N(r,r_1)/\sum_{r_1 \in \text{Neut}}\text{Pr}_N(r,r_1)$

* This definition makes distinction between beneficial and neutral mutations as revealed by a set of $s$ experiences
* Beneficial if empirical performance after mutation exceeds performance of current representation $r$ by an additive tolerance of at least $t$, a quantity which will be large enough (inverse polynomial at least) to be reliably distinguished from a zero increase in performance
* Neutral if no significant increase in performance is expected but it is expected that the performance is not worse than that of the current $r$ by more than $t$
* If some beneficial mutations are available, one is chosen in accordance with relative probabilities of their generation by $N$ as allowed by machine $M_2$ from Definition 3.1
* If none are available, then one of the neutral mutations is taken in accordance with relative probabilities of their generation by $N$

*Definition 3.3.* For a mutator $\text{Mu}(f, p(n, 1/\epsilon), R, N, D, s, r, t)$ a $t$-evolution step on input $r_1 \in R_n$ is the random variable $r_2 = \text{Mu}(f, p(n, 1/\epsilon), R, N, D, s, r_1, t)$. We then say $r_1 \rightarrow r_2$ or $r_2 \leftarrow \text{Evolve}(f, p(n, 1/\epsilon), R, N, D_n, s, r_1, t)$

* Polynomials $\text{tl}(x,y)$ and $\text{tu}(x,y)$ are polynomially related if for some $\eta > 1$ for all $x,y$ $(0 < x, y < 1)(\text{tu}(x,y))^{\eta} \le \text{tl}(x,y) \le \text{tu}(x,y)$
* An evolution sequence is a sequence of $t$-evolution steps where the $t$ at each step is bounded between two polynomially related quantities $\text{tl}(1/n, \epsilon), \text{tu}(1/n, \epsilon)$ and computable in polynomial time by a Turing machine $T$ that takes $r \in R, n$ and $\epsilon$ as inputs

*Definition 3.4.* For a mutator $\text{Mu}(f,p(n,1/\epsilon),R,N,D,s,r,t)$ a $(tl, tu)$-evolution sequence for $r_1 \in R_n$ is a random variable that takes as values sequences $r_1,r_2,r_3,\ldots$ such that for all $i$ $r_i \leftarrow \text{Evolve}(f,p(n,1/\epsilon),R,N,D,s,r_{i-1},t_i)$ where $tl(1/n, \epsilon) \le t_i \le tu(1/n, \epsilon)$, $tl$ and $tu$ are polynomially related polynomials and $t_i$ is the output of a Turing machine $T$ on input $r_{i-1},n$ and $\epsilon$

* If we want to evolve to performance very close to $1$ like $1-\epsilon$, we need numbers of experiences $s$ or numbers of generations $g$ that grow inversely with $\epsilon$ and tolerances $t$ that diminish with $\epsilon$
* Therefore $s$ and $g$ are functions of $n$ and $\epsilon$ denoted by $s(n,1/\epsilon), g(n,1/\epsilon), tl(1/n,\epsilon)$ and $tu(1/n,\epsilon)$

*Definition 3.5.* For polynomials $p(n, 1/\epsilon), s(n, 1/\epsilon), tl(1/n, \epsilon)$ and $tu(1/n, \epsilon)$, a representation class $R$ and $p(n, 1/\epsilon)$-neighborhood $N$ on $R$, the class $C$ is $(tl, tu)$-evolvable by $(p(n,1/\epsilon), R,N,s(n,1/\epsilon))$ over distribution $D$ if there is a polynomial $g(n,1/\epsilon)$ and a Turing machine $T$ which computes a tolerance bounded between $tl$ and $tu$ such that for every positive integer $n$, ever $f \in C_n$ every $\epsilon > 0$ and every $r_0 \in R_n$ it is the case that with probability greater than $1-\epsilon$ a $(tl, tu)$-evolution sequence $r_0,r_1,r_2,\ldots$ where $r_i \leftarrow \text{Evolve}(f,p(n,1/\epsilon), R,N,D_n,s(n,1/\epsilon),r_{i-1},T(r_{i-1},n,\epsilon))$ will have $\text{Perf}_f(r_{g(n,1/\epsilon)},D_n) > 1-\epsilon$

* The polynomial $g(n,1/\epsilon)$, which is the generation polynomial, upper bounds number of generations needed for evolution process

*Definition 3.6.* A class $C$ is evolvable by $(p(n,1/\epsilon), R,N,s(n,1/\epsilon))$ over $D$ if and only if for some pair of polynomially related polynomials $tl, tu, C$ is $(tl, tu)$-evolvable by $(p(n,1/\epsilon),R,N,s(n,1/\epsilon))$ over $D$

*Definition 3.7.* Class $C$ is evolvable by $R$ over $D$ iff for some polynomials $p(n,1/\epsilon)$ and $s(n,1/\epsilon)$ and some $p(n,1/\epsilon)$-neighborhood $N$ on $R$, $C$ is evolvable by $(p(n,1/\epsilon),R,N,s(n,1/\epsilon))$ over $D$

*Definition 3.8.* $C$ is evolvable over $D$ if for some $R$ it is evolvable by $R$ over $D$

*Definition 3.9.* $C$ is evolvable if it is evolvable over all $D$

* Definition of evolvability similar to definition of learnability [Valiant 1984] but includes that each step of learning chooses from a polynomial size set of hypotheses, tolerates at most a small decrease in performance, and the choice of the next hypothesis from the candidate hypotheses is made on the basis of their aggregate performance on inputs and not according to teh values of the various inputs

*Proposition 3.10.* If $C$ is evolvable by $R$ over $D$ then $C$ is learnable by $R$ over $D$. If $C$ is evolvable by $R$, $C$ is learnable by $R$.

*Proof.* If $C$ is evolvable over $D$ then for some polynomials $p(n,1/\epsilon), s(n,1/\epsilon), g(n,1/\epsilon), tl(1/n,\epsilon)$ and $tu(1/n,\epsilon)$ some representation $R$ adn some $p(n,1/\epsilon)$-neighborhood $N$ on $R$, $C$ is $(tl, tu)$-evolvable by $(p(n,1/\epsilon), R, N, s(n,1/\epsilon))$ over $D$ with generation polynomial $g(n,1/\epsilon)$. We can replicate this evolution algorithm in terms of PAC learning. At each stage, evolution algorithm takes fixed size samples of $s(n,1/\epsilon)$ labeled examples from distribution, computes empirical performance for its current hypothesis, and then generates teh next hypothesis in polynomially bounded way. Computing this performance is equivalent to computing the fraction of examples on which the hypothesis predicts correctly. Therefore, the access required to examples is the same as random labeled examples from $D$ and every step is a polynomial-time computation, all of which is allowed in PAC learning. Final hypothesis of the evolution model satisfies the requirements of learning model since it ensures performance is at least $1-\epsilon$ and accuracte on at least $1-\epsilon /2$ of $D$.

*Proposition 3.11.* If $C$ is evolvable by $R$ over $D$ then it is efficiently learnable from statistical queries using $R$ over $D$.

* Evolvability over all $D$ would be great. This would guarantee evolution independent of any assumptions about the distribution. It also means evolution can continue even if $D$ changes. Of course this would reduce the value of $\text{Perf}$ for any one $r$ and thus would set back evolutionary progress, but the process of finding improvements with respect to whatever the current $D$ is will continue.
* Open problem to whether distribution-free evolution is possible for a significant class of functions
* This definition of evolvability handles various restrictions robustly. E.g., evolvability with initialization, instead of requiring convergence from any starting point $r_0 \in R_n$ in Definition 3.5, you only require there is convergence from one fixed starting point $r_0$ for all targets $f \ in C_n$

*Proposition 3.12.* If $C$ is evolvable with optimization over $D$ then $C$ is evolvable over $D$. If $C$ is evolvable with initialization and optimization over $D$, then $C$ is evolvable with initialization over $D$

## Limits to Evolvability
* Does learnability imply evolvability? No.
* The set of odd parity functions is not evolvable for uniform distribution over $\{-1, 1 \}^n$ by any representation $R$
* If $C$ is the class of boolean threshold functions and $R$ is the given representation of it, then $C$ is not evolvable by $R$ unless $NP=RP$
* Four impediments to evolvability. First three are impediments to learnability, last is unique to evolvability
    * (i) Complexity of the mechanism that is evolving exceeds number of experiences in the context of information theory
    * (ii) Learnability by a fixed representation would imply solving a computational problem that is believed to be hard
    * (iii) Function class is so extensive that learning it by any representation would imply an efficient algorithm for a problem believed to be hard to compute
    * (iv) In information theory, evolvability cannot proceed because no empirical test of a polynomial number of hypotheses in a neighborhood can guarantee sufficient convergence in performance

## Some Provably Evolvable Structures
* A conjunction or disjunction is monotone if it contains no negated literals
* Disjunction is Boolean or and denoted by $+$
* Conjunction is Boolean and and denoted by multiplication
* Negation of variable $x_i$ is $x_i'$
* Uniform distribution over $\{-1,+1\}$ denoted by $U$
* Previous work such as [Ros 1997] and [Ros 1993] have analyzed evolutionary algorithms for learning conjunctions and disjunctions but allowed to depend on input information like number of bits on which the hypothesis and input differed

*Fact 1.* Over $U$ any conjunction's probability of a particular combination is $2^{-k}$ and a disjunction is $1-2^{-k}$

*Fact 2.* Probability that mean of $s$ independent random variables taking values in range $\left[a, b\right]$ is greater than or less than the mean of their expectations by more than $\delta$ is at most $\exp(-2s\delta^2/(b-a)^2)$

* Monotone conjunctions and disjunctions are evolvable over uniform distribution for their natural representations
* Conjunctions and disjunctions are evolvable with initialization over uniform distribution

## Discussion