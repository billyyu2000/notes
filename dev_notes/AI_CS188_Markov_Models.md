[](...menustart)

- [Markov Models](#d656a155bed68a7dec83cd56ff973bbc)
    - [Probability Recap](#e9171aa010f3fa5d166ecf151b1de19c)
    - [Reasoning over Time or Space](#b8edf9c2f97f0adf550e21090016b328)
    - [Markov Models](#d656a155bed68a7dec83cd56ff973bbc)
        - [Conditional Independence](#0f1513d04ac32269de73d0f17465488e)
        - [Joint Distribution of a Markov Model](#2549029b268b93144235df84effeb97d)
        - [Chain Rule and Markov Models](#1584c0069936b81fd7e2d00d4dc7186a)
        - [Implied Conditional Independencies](#5627b13e1756dc92c82a9b3998e04960)
    - [Example Markov Chain : Weather](#a3e9d92d013e8bd559c093cbca5a7684)
    - [Mini-Forward Algorithm](#cd0df25e7ecc8f5591d125ef5318fae1)
    - [Exmaple Run of Mini-Forward Algorithm](#ae8a34b505e071da4ab4c6a36da4f594)
    - [Stationary Distributions](#cff3dc4ffa629a6c5051471a4665a6c7)
        - [Still the Weather Example](#9ee0b726bfd7b5e9fbe84c468ace55c1)
    - [Application of Stationary Distribution: Web Link Analysis](#485984b095c6416cdcac20510c1c3a37)
    - [Application of Stationary Distribution: Gibbs Sampling](#a80042cc44b2a1b2b7d6193bb4083c58)
    - [Hidden Markov Models](#94d2b6fed9dd768fe2edec7e6c85546f)

[](...menuend)


<h2 id="d656a155bed68a7dec83cd56ff973bbc"></h2>

# Markov Models

Markov Model, a special kind of Bayes net, that sees a particularly large amount of use compared to most Bayes nets.

<h2 id="e9171aa010f3fa5d166ecf151b1de19c"></h2>

## Probability Recap

- ![](../imgs/cs188_prob_recap.png)


<h2 id="b8edf9c2f97f0adf550e21090016b328"></h2>

## Reasoning over Time or Space

- Often, we want to reason about a sequence of observations
    - Speech recognition
    - Robot localization
    - User attention
    - Medical monitoring
- Need to introduce time ( or space ) into our models



<h2 id="d656a155bed68a7dec83cd56ff973bbc"></h2>

## Markov Models

- A **Markov Model** is a chain-structured Bayes' net (BN)
    - Each node is identically distributed (stationarity)
    - Value of X at a given time is called the **state**
        - ![](../imgs/cs188_markov_model_state_x.png)
        - **so you have some variable X that replicates at every time step**
    - As a Bayes' net
        - P(X₁)
        - P(X<sub>t</sub> | X<sub>t-1</sub>)
    - Parameters: called **transition probability** or dynamics, specify how the state evolves over time (also, initial state probabilities)
        - this is the model how the world changes
    - Stationarity assumption: transition probabilities the same at all times
        - this means transition probabilities P(x<sub>t</sub> | x<sub>t-1</sub> ) don't depend on time, they are always the same. 
    - Same as MDP transition model , but no choice of action
        - here is no action, you are just interested in watching how its value evovles probabilistically over time, for example, what's the probability of rain 5 days after it was sunny ?


<h2 id="0f1513d04ac32269de73d0f17465488e"></h2>

### Conditional Independence

- Basic conditional independence:
    - Past and future independent given the present
        - Bayes Net D-separation
    - Each time step only depends on the previous
        - to predict what happens at the next time, just knowing the current time is the best thing. Knowing more things about the past is not going to help you.
    - This is called the (first order) Markov property
        - You might say, well, what if it doesn't apply in my situation? What if my situation, the state of the next time, depends on the state of the current time and the state of the previous time?
        - Well, to still be able to fit in this format and to really fit the notion of state, you should then combine the state of the current time and the state of the previous time in one bigger state variable that you now call your state.
- Note that the chain is just a (growable) BN
    - We can always use generic BN reasoning on it if we truncate the chain at a fixed length. But in this chapter we will use some variations of this algorithm that are easy to derive from first principles for Markov models, and that have equivalance simplifications of the sampling and variable elimination algorithms.


<h2 id="2549029b268b93144235df84effeb97d"></h2>

### Joint Distribution of a Markov Model

This case let's look at just 4 variables. 

![](../imgs/cs188_markov_4state_joint_distribution.png)

- Joint distribution:
    - P(X₁, X₂, X₃, X₄) = P(X₁)·P(X₂|X₁)·P(X₃|X₂)·P(X₄|X₃)
- More generally
    - P(X₁, X₂, ..., X<sub>T</sub>) = P(X₁)·P(X₂|X₁)·P(X₃|X₂)...P(X<sub>T</sub>|X<sub>T-1</sub>)
    - ![](../imgs/cs188_markov_4state_joint_distribution_generally.png)
- 简化版 Chain Rule

<h2 id="1584c0069936b81fd7e2d00d4dc7186a"></h2>

### Chain Rule and Markov Models

- From the chain rule, every joint distribution over X₁, X₂, X₃, X₄ , can can be written as:
    - P(X₁, X₂, X₃, X₄) = P(X₁)·P(X₂|X₁)·P(X₃|X₁,X₂)·P(X₄|X₁,X₂,X₃)  
- Assuming that
    - X₃ ⫫ X₁|X₂  
        - What dose that mean ?  That means that once somebody tells you the value of X₂,  you write the distribution of X₃ ; somebody then tells you what X₁ is and the distribution of X₃ doesn't change -- stays the same. 
    - X₄ ⫫ X₁,X₂ | X₃ 
- results in the expression posited on the previous slide: 
    - P(X₁, X₂, X₃, X₄) = P(X₁)·P(X₂|X₁)·P(X₃|X₂)·P(X₄|X₃)

--- 

- generally
    - ![](../imgs/cs188_markov_chain_rule_generally.png)


<h2 id="5627b13e1756dc92c82a9b3998e04960"></h2>

### Implied Conditional Independencies 

- We assumed : `X₃ ⫫ X₁|X₂`  , and `X₄ ⫫ X₁,X₂ | X₃` 
- We also have :  `X₁ ⫫ X₃,X₄ |X₂`
- proof:
    - ![](../imgs/cs188_markov_implied_conditional_probability.png)
- Additional explicit assumption
    - P(X<sub>t</sub>|X<sub>t-1</sub>) is the same for all t




<h2 id="a3e9d92d013e8bd559c093cbca5a7684"></h2>

## Example Markov Chain : Weather

![](../imgs/cs188_markov_chain_example_weather.png)

- States: X = {rain,sun}
- Initial distribution: 1.0 sun
- CPT P(X<sub>t</sub> | X<sub>t-1</sub>)

X<sub>t-1</sub> | X<sub>t</sub> | P(X<sub>t</sub> \| X<sub>t-1</sub>)
--- | --- | ---
sun  | sun  | 0.9
sun  | rain | 0.1
rain | sun  | 0.3
rain | rain | 0.7

- Two new ways of representing the same CPT
    - ![](../imgs/cs188_HMM_2_representation.png)
- What is the probability distribution after one step : P(X₂=sun) ?
    ```python
    P(X₂=sun) = P(X₂=sun,X₁=sun) + P(X₂=sun,X₁=rain)
              = P(X₂=sun|X₁=sun)·P(X₁=sun) + P(X₂=sun|X₁=rain)·P(X₁=rain)
              = 0.9·1 + 0.3·0 = 0.9
    ```


<h2 id="cd0df25e7ecc8f5591d125ef5318fae1"></h2>

## Mini-Forward Algorithm

- Question: What’s P(X) on some day t?
- ![](../imgs/cs188_markov_forward_simulation.png)
- What is actually going on here, think about the Markov model.
    - You have X₁,X₂. It's a Bayes net with two variables. You have all the distributions, all the tables. You have instantiated a distribution for the initial time X₁. And now you're running variable elimination to get the distribution for X₂. So you're trying to compute P(X₂) in this Bayes net.


<h2 id="ae8a34b505e071da4ab4c6a36da4f594"></h2>

## Exmaple Run of Mini-Forward Algorithm

- From initial observation of sun

·  | (X₁) | (X₂) | (X₃) | (X₄) | ... | (X<sub>∞</sub>)
--- | --- | --- | --- | --- | --- | ---
sun  | 1.0 | 0.9 | 0.84 | 0.804 | ... | 0.75
rain | 0.0 | 0.1 | 0.16 | 0.196 | ... | 0.25

- If we run it long enough, it will converge onto 0.75,0.25.
- From initial observation of ran

·  | (X₁) | (X₂) | (X₃) | (X₄) | ... | (X<sub>∞</sub>)
--- | --- | --- | --- | --- | --- | ---
sun  | 0.0 | 0.3 | 0.48 | 0.588 | ... | 0.75
rain | 1.0 | 0.7 | 0.52 | 0.412 | ... | 0.25

- We actually end up with the same distribution at time *t* equals infinity.
    - Just like value iteration, policy iteration, it doesn't matter how you initialize your values, because it decays over time by the discount factor.
- From yet another initial distribution P(X₁)


·  | (X₁) | ... | (X<sub>∞</sub>)
--- | --- | --- | ---
sun  | p   | ... | 0.75
rain | 1-p | ... | 0.25


<h2 id="cff3dc4ffa629a6c5051471a4665a6c7"></h2>

## Stationary Distributions

- For most chains:
    - Influence of the initial distribution gets less and less over time.
    - The distribution we end up in is independent of the initial distribution
- Stationary distribution:
    - The distribution we end up with is called the **stationary distribution P<sub>∞</sub>** of the chain
    - It satisfies  ![](../imgs/cs188_markov_stationary_distributions.png)

- As the property of markov matrix , it will converge to 0.94868/0.31623 = 3:1, that means:
    - P<sub>∞</sub>(sun) = 3/4
    - P<sub>∞</sub>(rain) = 1/4 

<h2 id="9ee0b726bfd7b5e9fbe84c468ace55c1"></h2>

### Still the Weather Example 

X<sub>t-1</sub> | X<sub>t</sub> | P(X<sub>t</sub> \| X<sub>t-1</sub>)
--- | --- | ---
sun  | sun  | 0.9
sun  | rain | 0.1
rain | sun  | 0.3
rain | rain | 0.7

- Question: What's P(X) at time t=infinity?

```octave
octave:6> a = [ 0.9 0.3 ; 0.1 0.7 ]
a =

   0.90000   0.30000
   0.10000   0.70000

octave:7> [V,LAMBDA] = eig(a)
V =

   0.94868  -0.70711
   0.31623   0.70711

LAMBDA =

Diagonal Matrix

   1.00000         0
         0   0.60000

```

---

<h2 id="485984b095c6416cdcac20510c1c3a37"></h2>

## Application of Stationary Distribution: Web Link Analysis

- PageRank over a web graph
    - Each web page is a state
    - Initial distribution: uniform over pages
    - Transitions:
        - With prob. c, uniform jump to a random page
        - With prob. 1-c, follow a random outlink
    - So page rank is a number assigned to each node in the web graph. Google came up with a way for a computer to just crawl the internet and assign the page ranks automatically. The computer will start anywhere on the internet, at random. And then when you're on a page with a bunch of out links, you click one of them at random. You follow that one. And then you repeat this process. And as you repeat this, you're actually following a Markov model. Google compute the stationary distribution.
- Stationary distribution
    - Will spend more time on highly reachable pages
    - E.g. many ways to get to the Acrobat Reader download page
    - Somewhat robust to link spam
    - Google 1.0 returned the set of pages containing all your keywords in decreasing rank, now all search engines use link analysis along with many other factors (rank actually getting less important over time)


<h2 id="a80042cc44b2a1b2b7d6193bb4083c58"></h2>

## Application of Stationary Distribution: Gibbs Sampling

- Each joint instantiation over all hidden and query varialbes is a state: { X₁, ..., X<sub>n</sub> } = H ∪ Q

- Transitions:
    - with probability 1/n resample variable Xⱼ according to
    - P( Xⱼ | X₁,X₂,...,X<sub>j-1</sub>, X<sub>j+1</sub>, ..., X<sub>n</sub>, e₁,...e<sub>m</sub> )

- Stationary distribution:
    - Conditional distribution  P( X₁,X₂,..., X<sub>n</sub> | e₁,...e<sub>m</sub> )
    - Means that when running Gibbs sampling long enough we get a sample from the desired distribution
    - Requires some proof to show this is true!

---

<h2 id="94d2b6fed9dd768fe2edec7e6c85546f"></h2>

## Hidden Markov Models

Typically, you need more than just a Markov model, you need something called hidden Markov model.



