---
layout: post
title: "Budget MDPs and Sparse RL"
categories: general
author: Raghav Singal
excerpt_separator: <!--more-->
comments: true
---


We discussed the following two papers: Dantzig Selector with an approximately optimal denoising matrix and its application in sparse reinforcement learning [] and "Budget allocation using weakly coupled, constrained Markov decision processes" [] presented by Kumar Goutam. The two papers are not directly related to each other and can be read independently of each other. This document summarizes the papers and also lists the points discussed in our reading group

<!--more-->

# Dantzig Selector paper

## Problem

We are given a data matrix $$X \in \R^{n\times m} (m \gg n)$$ and a noisy observation vector $$y \in \R^n$$ satisfying $$y=X\beta^* + \epsilon$ where $\epsilon$$ is the noise vector following the Gaussian distribution $$N(0,\sigma^2 I)$$ and $$\beta^*$$ is the truth model which is a sparse vector. How to recover the sparse vector $\beta^*$ from this under-determined system?

## Existing solution

Dantzig Selector (DS) is a widely used approach to solve this problem. The model is


$$
\underset{\beta}{\mbox{minimize }}  \| \beta \|_1
$$

$$ 
\text{subject to}  \| X^T (X\beta - y)  \|_{\infty} \leq \lambda.
$$


Denote by $$\hat{\beta}_{\text{DS}}$$ the optimal solution to the DS model. Some comments on DS:

* Similar empirical and theoretical performance to LASSO (which is great!).
* Can be formulated as an LP (which is great!).
* We wondered about the rationale for including $$X^T$$ term in the constraint. Without the $X^T$, the infinity-norm will only consider the largest error and ignore all other errors (which is bad!). Therefore, having $$X^T$$ seems reaonable. But then, we argued that one can replace the infinity-norm with 1-norm (or 2-norm) and remove $X^T$. Moreover, the 1-norm will keep the optimization problem as an LP (the 2-norm will not). There might be some other rationale for the DS model (_denoising_?).

## Contribution

The authors argue that the $$X^T$$ term in the DS model is not optimal (according to some technical criterion which we skip here). Accordingly, their goal is to replace $$X^T$$ with $$Q^T$$ (defined later) and show $$Q^T$$ is "better" than $$X^T$$. Moreover, they show how to approximately estimate the optimal $$Q^T$$.




### References

[1] Boutilier, C. and T. Lu (2016). Budget allocation using weakly coupled, constrained markov decision processes. In Proceedings of the Thirty-Second Conference on Uncertainty in Artificial Intelligence, pp. 52–61. AUAI Press.

[2] Liu, B., L. Zhang, and J. Liu (2016). Dantzig selector with an approximately optimal denoising matrix and its application in sparse reinforcement learning. In Proceedings of the Thirty-Second Conference on Uncertainty in Artificial Intelligence, pp. 487–496. AUAI Press.