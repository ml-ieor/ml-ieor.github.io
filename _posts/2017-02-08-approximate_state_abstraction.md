---
layout: post
title: "Dynamic programming: approximate state abstraction and Bayesian optimization"
categories: general
author: Francois Fagan
excerpt_separator: <!--more-->
comments: true
---


This week we discussed two Dynamic Programming (DP) papers: "Near optimal behavior via approximate state abstraction" and "Bayesian Optimization with a Finite Budget: An Approximate Dynamic Programming Approach" presented by Octavio Ruiz Lacedelli. The approximate state abstraction paper shows that compressing the state space of a Markov Decision Process (MDP) for faster optimization can be done without a significant decrease in accuracy; while the Bayesian Optimization paper uses DP as a lookahead strategy to avoid suboptimal greedy decisions.

<!--more-->

# Approximate State Abstraction and Bayesian Optimization

## Background and problem statement

Although there are efficient algorithms for optimizing MDPs of moderate size, MDPs are provably hard to solve as the state space grows large. Approximate state abstraction, also known as ``state aggregation'', is a classical idea for reducing the state space of an MDP to make optimization more efficient [2, p.305, Sec 6.3, Vol. 1 and p.46, Sec 1.3.3, Vol. 2] (curiously this older literature was not referenced in the paper). This paper gives the (first?) error bounds for approximate state abstraction.

Before we get into more detail, lets recap the standard MDP setup. An MDP is defined by a tuple $$(\mathcal{S}, \mathcal{A},\mathcal{T},\mathcal{R},\gamma)$$ where $$\mathcal{S}$$ is a finite state space; $$\mathcal{A}$$ is a finite set of actions available to the agent; $\mathcal{T}$ denotes $\mathcal{T}(s,a,s')$, the probability of an agent transitioning to state $s'\in\mathcal{S}$ after applying action $a \in \mathcal{A}$ in state $s\in \mathcal{S}$ ; $\mathcal{R}(s, a)$ denotes the reward received by the agent for executing action $a$ in state $s$; $\gamma\in [0, 1]$ is a discount factor that determines how much the agent prefers future rewards over immediate rewards. A solution to an MDP is called a policy, denoted $\pi : \mathcal{S}  \to \mathcal{A}$. The optimal policy $\pi^\ast$ maximizes the expected discounted reward from any state defined by the Bellman equation:

$$Q^{\pi}(s,a)=\mathcal{R}(s,a)+\gamma\sum_{s'}\mathcal{T}(s,a,s')Q^{\pi}('s,\pi(s')).$$
Finally, the value function at each state is defined to be $$V^\pi(s):=Q^{\pi}(s,\pi(s))$$.


Armed with the definition of an MDP, we can return to the question of state abstraction. Consider an MDP $$(\mathcal{S}_G,\mathcal{A},\mathcal{T},\mathcal{R},\gamma)$$ where the $G$ subscript denotes that this is the \emph{ground} MDP. The ground MDP is the MDP we want to solve. To simplify the ground MDP we can aggregate the ground states into a small number of ``abstract states'', such that similar ground states are mapped to the same abstract state. If we can define an MDP on the abstract states, then its solution might be useful in solving the ground MDP. This is the intuition behind approximate state abstraction.

Formally an aggregation is a map from the ground to an abstract state space $$\phi:\mathcal{S}_G\to\mathcal{S}_A$$ with weight function $$\omega:\mathcal{S}_G\to[0,1]$$ such that $$\sum_{g:\phi(g)=\phi(s)}\omega(g)=1$$ for all $$s\in\mathcal{S}_G$$. Together they define an \emph{approximate} MDP on the state space $$\mathcal{S}_A=\{\phi(s):s\in\mathcal{S}_G\}$$, reward $$\mathcal{R}_A(\phi(s),a)=\sum_{g:\phi(g)=\phi(s)}R(g,a)\omega(g)$$ and transitions $$\mathcal{T}(\phi(s),a,\phi(s')) = \sum_{g:\phi(g)=\phi(s)}\sum_{g':\phi(g')=\phi(s')} \mathcal{T}(g,a,g')\omega(g)$$.

Let a policy on the approximate MDP be denoted as $$\pi_A:\mathcal{S}_A\to \mathcal{A}$$ with the optimal policy being $$\pi^\ast_A$$. Note that this policy is over the \emph{same action space} $\mathcal{A}$ as the ground MDP. Thus it induces a policy in the ground MDP: $$\pi_{GA}(s) = \pi_A(\phi(s))$$. If $$\phi$$ is well defined then the induced policy will be close to the optimal policy, $$\pi_{GA}(s)\approx \pi^\ast(s)$$, and the approximate MDP will be small enough to enable efficient computation\footnote{This establishes a tradeoff of policy accuracy vs runtime that needs to be balanced.}.

## Contributions
The main result in this paper is stated in Lemma 1. It shows that if $\phi$ is defined to preserve the $$Q^{\pi^\ast}$$ function to within $$\epsilon>0$$ accuracy, i.e. 

$$
\phi(s)=\phi(s') \Longrightarrow \forall_a |Q^{\pi^\ast}(s,a) - Q^{\pi^\ast}(s',a)|\leq \epsilon,
$$

then 

$$
V^{\pi^\ast}(s) - V^{\pi_{GA}}(s)\leq \frac{2\epsilon}{(1-\gamma)^2}
$$

for all $$s\in\mathcal{S}_G$$. The theory behind this result is not particularly deep an follows from an induction type argument.

Practically the $$Q$$ function is unknown apriori and so the above result does not give a useful mechanism for defining $$\phi$$. A more practical corollary is provided in Lemma~2. It states that if $$\phi$$ preserves the rewards and transitions, i.e. 

$$
\phi(s_1)=\phi(s_2)\Longrightarrow &\forall_a | \mathcal{R}(s_1,a)-cR(s_2,a) | \leq \epsilon
$$

$$
\qquad \forall_s |\sum_{s':\phi(s')=\phi(s)} \mathcal{T}(s_1,a,s')-\mathcal{T}(s_2,a,s')|\leq \epsilon,
$$

then

$$
V^{\pi^\ast}(s) - V^{\pi_{GA}}(s)\leq \frac{2\epsilon}{(1-\gamma)^2}\frac{1+\gamma(|\mathcal{S}_G|-1)}{1-\gamma}
$$

for all $$s \in \mathcal{S}_G$$. Since we typically know $\mathcal{R}$ and $$\mathcal{T}$$ in advance, we can use this result to define $$\phi$$ given $$\epsilon$$ and have a guarantee on our accuracy. Unfortunately, the bound scales linearly with the size of the state space $$|\mathcal{S}_G|$$ and has an extra $$(1-\gamma)$$ factor in the denominator, so the bound will probably be too loose to be useful in practice. The paper did not give any experimental evidence of how tight this bound was.

## Experiments
The experiments have the bizarre setup of choosing $$\phi$$ by first solving the MDP, then greedily aggregating ground states into abstract states that satisfied the criteria in equation (\ref{eq:phi_Q}). Aggregating using the criteria in equation (\ref{eq:phi_R_T}) would be a more reasonable test of how $$\phi$$ would be chosen in practice. The experiments show that under their setup, abstraction can be beneficial, significantly decreasing the computational burden while maintaining a good approximation to the optimal policy. The benchmark policy in the experiments was very weak: just a random policy.

## Comments
* The choice of weights $$\omega$$ in the abstraction was not discussed in the paper. It would be interesting to know what its effect is.
* The abstraction model may be viewed as a Partially Observed MDP (POMDP). Perhaps the POMDP literature can add to this problem?
* Methods like dualizing and information relaxations may be useful for bounding the error of the approximation.



### References

[1] D. Abel, D. Hershkowitz, and M. Littman. Near optimal behavior via approximate state abstraction. In Proceedings of The 33rd International Conference on Machine Learning, pages 2915–2923, 2016.

[2] D. Bertsekas. Dynamic Programming and Optimal Control.

[3] N. Bhat, V. Farias, and C. Moallemi. Optimal ab testing. Technical report, Working paper, 2015.

[4] M. Ghavamzadeh, S. Mannor, J. Pineau, A. Tamar, et al. Bayesian reinforcement learning: A survey.
Foundations and Trends in Machine Learning, 8(5-6):359–483, 2015.

[5] R. Lam, K. Willcox, and D. H. Wolpert. Bayesian optimization with a finite budget: An approximate
dynamic programming approach. In Advances in Neural Information Processing Systems, pages 883–891, 2016.