# Evolvability
*Leslie Valiant*

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
* 