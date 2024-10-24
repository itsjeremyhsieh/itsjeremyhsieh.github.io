---
title: "[CS5242] Stable Diffusion"
published: 2024-10-22
description: "#9 Stable Diffusion"
tags: ["NUS", "Neural Network & Deep Learning"]
category: CS5242
draft: false
---

# Image Generation
![Image Generation](image_generation.png)

# Diffusion Model
![Process](process.png)
1. Forward Process:
    - build a random Gaussian noise image
    - generate training data & labels for reverse process
2. Reverse Process:
    - train noise predictor (Unet) to generate denoised image
    - input: noise image & step number (to indicate the amount of noise per image step)
    - output: denoised image
    - Why need T steps?
        - break down a hard problem
        - easier to inject conditions (i.e text prompt) gradually
    - Noise predictor loss function: distance between predicted image and ground truth (from forward process)

- Text-to-Image: in reverse process, add text prompt as an input.
    - 3 inputs: noise image, step number, text prompt

# Stable (Latent) Diffusion
> Why need?
- DM often operate on high dimension pixel space $\Rightarrow$ costly computation
![stable diffusion](stable_diffusion.png)

## AutoEncoder
- implemented as Variational Autoencoder (VAE)
- handle perceptual image compression (reduces the dimension of input data)
![autoencoder](autoencoder.png)

## Denoiser
- latent diffusion models
![denoiser](denoiser.png)

## Condition Encoder
- arbutrary encoder that produces a sequence of tokens to generate embeddings for denoiser

# Dall-E 2
![dalle](dalle.png)
- 4 parts:
    - text encoder & image encoder (pretrained from CLIP)
    - prior training (transform text embedding to image embedding)
    - decoder training (produces image conditioned on CLIP image embeddings and text captions)
    - inference (delete CLIP image encoder, save CLIP text encoder)
