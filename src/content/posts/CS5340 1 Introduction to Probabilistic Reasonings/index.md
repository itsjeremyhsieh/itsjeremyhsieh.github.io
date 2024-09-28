---
title: "[CS5340] Introduction to Probabilistic Reasonings"
published: 2024-08-12
description: "#1 Random variables, Joint probabilities, Conjugate distributions"
tags: ["NUS", "Uncertainty Modelling in AI"]
category: CS5340
draft: false
---

# Probability

## Probability space $(Ω, E, P)$
- $Ω$: sample space, set of all possible outcomes
- $E$: event space, $⊆ 2^Ω$
    - must contain $∅$ and $Ω$
    - closed under **countable union**: $\alpha_i ∈ E → \cup \alpha_i ∈ E$
    - closed under **complements**: $\alpha ∈ E → Ω - \alpha ∈ E$
- $P(\alpha) ≥ 0, P(Ω) = 1$
- if $\alpha \cap \beta = ∅ → P(\alpha \cup \beta) = P(\alpha) + P(\beta)$

## Probability distribution
- Discrete: Probability mass function $\sum_{i=1}^K P(x=x^i) = 1$
- Continuous: Probability density function $\int_{val(x)} p(x)dx = 1$ 

# Marginalization (sum rule)

- given $\int \int p(x, y) dx dy =1$
    - Eliminate = sum over
    - Continuous case: $\int p(x, y)dy = p(x)$, $\int p(x, y) dx = p(y)$
    - Discrete case: $\sum_y p(x, y) = p(x)$, $\sum_x p(x, y) = p(y)$

# Conditional probability (product rule)
- $\boldsymbol {p(x | y) = \frac {p(x, y)} {p(y)}}$
- $p(x,y) = p(x|y)p(y)$
- can be extended to multiple random variable
    - $p(w,x,y,z) = p(w,x,y|z)p(z) $
                 $= p(w,x | y,z)p(y|z)p(z)$ 
                 $= p(w|x,y,z)p(x|y,z)p(y|z)p(z)$

# Baye's rule
- since $p(x, y) = p(x|y)p(y) = p(y|x)p(x)$,  
- $\boldsymbol {p(y|x) = \frac {p(x|y)p(y)}{p(x)}}$
    - $p(y|x)$: posterior
    - $p(x|y)$: likelihood
    - $p(y)$: prior
    - $p(x)$: evidence

## Independence
- $p(x|y) = p(x)$, knowing $y$ doesn't give any information on $x$
- therefore, $p(x,y) = p(x|y)p(y) = p(x)p(y)$

## Expectation
- $E[f[x]] = \int f[x]p(x) dx$
- R1: $E[k] = k$ (expected value of a constant is a constant)
- R2: $E[k \times f[x]] = k \times E[f[x]]$
- R3: $E[f[x]+g[x]] = E[f[x]] + E[g[x]]$
- R4: $E[f[x]g[x]] = E[f[x]] \times E[g[x]]$ if $X, Y$ are independent

# **Conjugate distribution (Conjugate prior)**
- used for modelling parameter of probability distribution
- **prior** and **posterior** are in the same distribution family
    - if $p(x| \theta)$ is Normal distribution, then $p( \theta | x)$ will also be in the same family
    - 如果後驗與先驗是同一個指數族，那就稱那個先驗為共軛先驗
