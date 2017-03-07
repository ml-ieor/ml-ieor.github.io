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




### References

[1] D. Abel, D. Hershkowitz, and M. Littman. Near optimal behavior via approximate state abstraction. In Proceedings of The 33rd International Conference on Machine Learning, pages 2915–2923, 2016.

[2] D. Bertsekas. Dynamic Programming and Optimal Control.

[3] N. Bhat, V. Farias, and C. Moallemi. Optimal ab testing. Technical report, Working paper, 2015.

[4] M. Ghavamzadeh, S. Mannor, J. Pineau, A. Tamar, et al. Bayesian reinforcement learning: A survey.
Foundations and Trends in Machine Learning, 8(5-6):359–483, 2015.

[5] R. Lam, K. Willcox, and D. H. Wolpert. Bayesian optimization with a finite budget: An approximate
dynamic programming approach. In Advances in Neural Information Processing Systems, pages 883–891, 2016.