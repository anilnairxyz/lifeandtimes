---
draft: false
date: 2025-05-18
slug: score_based_models
categories:
  - genai
tags:
  - ai
  - neural networks
  - score-based models
  - langevin dynamics
---

# Score-Based Generative Models

**Score-based models** represent a paradigm shift in generative AI. Instead of trying to estimate the probability density function (PDF) directly (like in flows) or implicitly (like in GANs), they focus on learning the **score function**: the geometric "shape" of the data distribution.

The score function is defined as the gradient of the log-probability density with respect to the input data:

$$
\mathbf{s}_\theta(\mathbf{x}) = \nabla_\mathbf{x} \log p_\theta(\mathbf{x})
$$

Intuitively, this vector field points in the direction where the data probability increases most rapidly.

<!-- more -->

## Denoising Score Matching

Training a model to match the true data score is challenging because we don't know the true $p(\mathbf{x})$ to begin with. However, **Denoising Score Matching** offers an elegant solution.

We can perturb the data with Gaussian noise $\mathbf{\tilde{x}} = \mathbf{x} + \sigma \mathbf{\epsilon}$. Interestingly, learning to remove this noise (denoising) is mathematically equivalent to estimating the score of the noisy distribution. The objective becomes:

$$
\mathbb{E}_{\mathbf{x}, \mathbf{\tilde{x}}} \left[ \left\| \mathbf{s}_\theta(\mathbf{\tilde{x}}) + \frac{\mathbf{\tilde{x}} - \mathbf{x}}{\sigma^2} \right\|^2 \right]
$$

This essentially trains the network to point back towards the clean data $\mathbf{x}$ given a noisy point $\mathbf{\tilde{x}}$. This is related to **Tweedie's Formula**, which connects optimal denoising to the score function.

## The Manifold Hypothesis and Sampling

Even with a trained score function, generating samples using **Langevin Dynamics** (iteratively following the gradient + adding noise) often fails. This is because real world data (like images) lies on a low-dimensional **manifold** within the high-dimensional pixel space.
- In regions far from the manifold (low density), the score is ill-defined and estimated poorly.
- The sampling process gets stuck or wanders in these empty regions.

## Noise Conditional Score Networks (NCSN)

The solution is to use multiple levels of noise.
1.  **Training**: We train a single network conditioned on the noise level $\sigma$, i.e., $s_\theta(\mathbf{x}, \sigma)$, using a weighted sum of denoising objectives across different $\sigma$ levels.
2.  **Sampling (Annealed Langevin Dynamics)**: We start with high noise (where density covers the whole space) and run Langevin dynamics. We then gradually lower the noise level, using the final sample of the previous step as the starting point for the next.

This process essentially guides the sample from a chaotic, simple distribution onto the complex, sharp data manifold. This architecture lays the direct foundation for modern **Diffusion Models**.
