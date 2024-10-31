---
title: "[CS5446] Human-Guided AI Decision Making"
published: 2024-10-30
description: "#10 Appreticeship learning, Inverse RL"
tags: ["NUS", "AI Planning"]
category: CS5446
draft: false
---

> How can human guide AI the make decisions?

# Judgemental Decision Making
- complements evidence-based and utility-based approaches
- account for human heuristics and cognitive bias

:::Note 
Human is not always rational
:::

# Invorporation emothinal factors
- $EU(a) = \sum_{s'} p(R(a)=s') U(s') - D(a)$
    - $D(a)$: disappointment factor
- balancing rationality and emotion

# Human AI value alignment
- to handle uncertain preferences in a decision model
    - add a new latent variable to represent unknown preference
    - add an appropriatesensor model

# Appreticeship Learning

## Imitating learning
- learn $\pi(s)$ directly from $D$ (expert demonstrations)
- supervised learning: $\pi(s) = argmin_\pi \sum_i l(\pi(s_i), a_i)$
    - $l$: loss function between predicted action $\pi(s_i)$ and expert action $a_i$
- policy: mapping from state to action

### Behavior cloning
- train a stochastic policy $\pi_\theta(a|s)$ to imitate expert behavior
- $\theta^* = argmax_\theta \prod_{(s,a)} (a_i|s_i) = argmin_\theta \sum_{(s,a)} l(\pi_\theta(s_i) | a_i)$

### DAgger (Data Aggregation)
- chat with expert when encounter unfamiliar state

## Inverse Reinforcement Learning
- recover reward function $R^*$ where the expert's policy achieves a higher expected reward than other policies
- Bayesian approach
    - suppose data $d$ is observed, $h_R$ be the hypothesis that $R$ is the true reward function
    - $P(h_R|d) = \alpha P(d|h_R)p(h_R)$
- $R_\theta(s,a) = \sum_{i=1}^n \theta_i f_i(s,a)$ (feature vector)

### Maximum Margin IRL
- iteratively adjust $\theta$ to align learner's feater expectations with that of expert's

### Generative Adversarial Imiration Learning
- combine Generative Adversarial Networks (GAN) and IRL to learn policies without reward function estimation