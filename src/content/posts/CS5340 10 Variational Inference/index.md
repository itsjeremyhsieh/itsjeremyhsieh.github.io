---
title: "[CS5340] Variational Inference"
published: 2024-10-28
description: "#10 Mean-Field Approximation"
tags: ["NUS", "Uncertainty Modelling in AI"]
category: CS5340
draft: false
---

# Approximate Inference
- Goal: find the posterior $p(Z|X)$ (intractable)
- Stochastic approximation: MCMC
- Deterministic approximation: **Variational approach**

# Variational Approach
- Given $p(X,Z)$, find approximation $q(Z)$ for $p(Z|X)$
- minimize KL-divergence
    - $KL(z||p) = - \int q(z) ln \frac {p(Z|X} {q(Z)} dZ \geq 0$
    - but $p(Z|X)$ is intractable
- $\Rightarrow$ we maximize lower-bound of log likelihood
    - $argmax L(q) = \int q(Z) ln \frac {p(X,Z)}{q(Z)} dZ$

# Mean-Field Approximation
- $q(Z) = \prod_{i=1}^M q_i(Z_i)$
- $L(q) = \inf \prod_i q_i (ln p(X,Z) - \sum_i ln q_i) dZ \ = ... = -KL(q_j || \tilde p(x, z_j)) + constant$
- $\Rightarrow ln Q^*_j(Z_j) = E_{i \neq j} [ln p(X,Z)] + constant$
- **KL(q||p) $\neq$ KL(p||q)**
- **See the examples in the slides**

# Loopy Belief Propogation
- belief propagation in graphs with cycles
- initialized all messages and beliefs to $1$
    - $m_{s \rightarrow t} (x_t) =1$, $bel_S(x_s) = 1$
- every iteration update belief for each node
- send message on each node
    - asychronous: define a hierachical order given by the tree
    - synchronous: all current messages are updated with messages from previous step
