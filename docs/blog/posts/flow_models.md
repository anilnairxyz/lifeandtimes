---
draft: false
date: 2024-08-07
slug: flow_models
categories:
  - genai
tags:
  - ai
  - neural networks
  - normalizing flows
---

# Normalizing Flows

Variational Auto-Encoders (VAEs) approximate the data distribution using a lower bound (ELBO) because the true likelihood is intractable. **Normalizing Flows** take a different path: they define a model where the exact log-likelihood is tractable and can be optimized directly. They achieve this by using a sequence of invertible transformations to map a simple distribution (like a Gaussian) to the complex data distribution.

<!-- more -->

## The Change of Variables Formula

The core mathematical principle behind flow models is the change of variables formula for probability density functions. If we transform a random variable $\mathbf{z}$ with distribution $p_Z(\mathbf{z})$ via an invertible function $\mathbf{x} = f(\mathbf{z})$, the density of the transformed variable $\mathbf{x}$ is given by:

$$
p_X(\mathbf{x}) = p_Z(f^{-1}(\mathbf{x})) \left| \det \frac{\partial f^{-1}(\mathbf{x})}{\partial \mathbf{x}} \right|
$$

Here, the second term is the absolute value of the determinant of the **Jacobian matrix** of the inverse transformation. It measures how the transformation expands or contracts the volume of the space.

## Normalizing Flows in Practice

A normalizing flow consists of a sequence of $K$ such invertible transformations. We start with a simple base distribution $\mathbf{z}_0 \sim \mathcal{N}(0, I)$ and apply the chain of functions:

$$
\mathbf{z}_K = f_K \circ \dots \circ f_1(\mathbf{z}_0) = \mathbf{x}
$$

The log-likelihood of a data point $\mathbf{x}$ can then be computed exactly by summing the log-likelihood of the base distribution and the log-determinants of the Jacobians of each step.

### The Computational Challenge

For this to be practical, we need transformations where:

1.  **Invertibility** is easy to compute.
2.  **Jacobian Determinant** is computationally cheap (specifically $O(D)$ rather than $O(D^3)$).

A common strategy is to design transformations with **triangular Jacobian matrices**, as their determinant is simply the product of the diagonal elements.

## Popular Flow Architectures

### NICE (Non-linear Independent Components Estimation)
NICE uses **additive coupling layers**. It splits the input vector into two parts. The first part stays the same, while the second part is transformed by adding a function of the first part (modeled by a neural network). This transformation is volume-preserving (determinant is 1) and trivially invertible.

### Real-NVP (Real-valued Non-Volume Preserving)
Real-NVP extends NICE by using scaling and translation.

$$
\begin{aligned}
\mathbf{y}_{1:d} &= \mathbf{x}_{1:d} \\
\mathbf{y}_{d+1:D} &= \mathbf{x}_{d+1:D} \odot \exp(s(\mathbf{x}_{1:d})) + t(\mathbf{x}_{1:d})
\end{aligned}
$$

This allows for more complex deformations of the space while maintaining a triangular Jacobian.

### Autoregressive Flows (MAF and IAF)

Autoregressive models can also be interpreted as flows.

- **Masked Autoregressive Flow (MAF)**: Fast likelihood evaluation (parallel), but slow sequential sampling. Good for density estimation.
- **Inverse Autoregressive Flow (IAF)**: Fast parallel sampling, but slow likelihood evaluation. Good for real-time generation.

Normalizing flows offer the powerful benefit of **exact likelihood estimation** and efficient latent variable manipulation, though they often require high-dimensional latent spaces (same dimension as input) compared to the compressed latent spaces of VAEs.
