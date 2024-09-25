---
title: "[CS5242] Linear Regression"
published: 2024-08-13
description: "#1 Univariate & Multivariate Linear Regression"
tags: ["NUS", "Neural Network & Deep Learning"]
category: CS5242
draft: false
---

# Univariate Linear Regression
- Maps from **input** to **output**
- $\tilde y = wx + b$
    - $\tilde y$: prediction output
    - $w$: weight
    - $x$: input
    - $b$: bias
- Loss funciton
    - to calculate the loss between $\tilde y$ and $y$
    - L1: $L(x, y| w, b) = |\tilde y - y|$
    - L2: $L(x, y| w, b) = \frac{1}{2} |\tilde y - y|^2$
    - Global loss $J(w,b) = \frac{1}{m} \sum_{i=1}^m L(x^i, y^i | w, b)$ (data points are independent to each other)
    - $min J(w,b) = min_{w,b} \frac{1}{2m} \sum_{i=1}^m (wx^i + b - y^i)^2$
        - the $2$ in $\frac{1}{2m}$ is for calculation convenience
    - Gradient descent
        - optimization
        - for each sample, compute $\tilde y = wx + b$
        - compute average loss $J(w,b)$
        - compute $\frac{\partial J}{\partial w}$
        - update $w = w - α \frac{\partial J}{\partial w}$, $α$ is learning rate
        - update $w, b$ repeatedly

# Multivariate Linear Regression
- features as column vector $X = \begin{bmatrix} x_1 \\ x_2 \\ ... \\ x_n \end{bmatrix}$, $x \in R^{n \times 1}$
- $\tilde y = w^T x + b, w \in R^{n \times 1}, b \in R$
    - $= \sum_{i=1}^m w_ix_i+b = (w_1, w_2, ..., w_n) \times \begin{bmatrix} x_1 \\ x_2 \\ ... \\ x_n \end{bmatrix} + b$
- Gradient of vector and metrix
    - $J(w) = L(x, y|w) = \frac{1}{2} (w^Tx-y)^2$
    - let $Z = w^Tx-y$, $J(w)=\frac{1}{2}Z^2$
    - $\frac{\partial J(w)}{\partial w} = \frac{\partial J}{\partial Z} \frac{\partial Z}{\partial w} = Zx = (w^Tx-y)x$
