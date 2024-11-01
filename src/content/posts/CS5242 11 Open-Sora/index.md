---
title: "[CS5242] Open-Sora"
published: 2024-10-29
description: "#11 DiT"
tags: ["NUS", "Neural Network & Deep Learning"]
category: CS5242
draft: false
---

# Diffusion Model before DiT: U-Net
![before](before.png)
- but U-Net is not designed for long sequence data i.e video

# Diffusion Transformers (DiT)
![DiT](dit.png)
- input: latent representation of image or video

## adaLN-Zero
- LayerNorm with parameters generated by input

## Cross Attention
- conditions are encoded in Keys and Values
- latent in Queries
## In-context Conditioning
- conditions are concatenated to the latent tokens

# MovieGen
- a cast of foundation models that generates videos & synchronized audio

- training scheme
![training scheme](training_scheme.png)

# Open-Sora
![Open Sora](open_sora.png)
- utilizes DiT architecture
- reduce training & inference cost
- large-scale image pre-training $\rightarrow$ high quality video data find tuning