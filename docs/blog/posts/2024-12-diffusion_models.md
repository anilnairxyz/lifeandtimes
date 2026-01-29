---
draft: false
date: 2024-12-11
slug: diffusion_models
categories:
  - genai
tags:
  - ai
  - neural networks
  - diffusion models
  - sde
---

# Score-Based Diffusion Models

Diffusion models are currently the state-of-the-art in generative AI, powering tools like DALL-E 2, Stable Diffusion, and Imagen. They unify the concepts of Iterative Refinement (from Score-Based Models) and Variational inference.

<!-- more -->

Conceptually, they consist of two processes:

1.  **Forward Process (Diffusion)**: Slowly destroying the data structure by adding noise until it becomes pure static.
2.  **Reverse Process (Generation)**: Learning to reverse this process to recover structure from noise.

## The Stochastic Differential Equation (SDE) View

We can model the forward diffusion process as a solution to a **Stochastic Differential Equation (SDE)**:

$$
d\mathbf{x} = f(\mathbf{x}, t)dt + g(t)d\mathbf{w}
$$

Here, $d\mathbf{w}$ represents infinitesimal white noise. Over time $t=0 \to T$, this process transforms the complex data distribution into a simple Gaussian prior.

Crucially, a remarkable result from Anderson (1982) shows that this process can be reversed if we know the **score function** of the distribution at every time step. The reverse SDE is given by:

$$
d\mathbf{x} = [f(\mathbf{x}, t) - g(t)^2 \nabla_\mathbf{x} \log p_t(\mathbf{x})] dt + g(t) d\mathbf{\bar{w}}
$$

This provides a direct link to **Score-Based Models**. To generate data, we just need to learn the score function $\nabla_\mathbf{x} \log p_t(\mathbf{x})$ for all $t$ (which we can do using denoising score matching) and then simulate the reverse SDE starting from random noise.

## The Discrete Denoising View (DDPM)

Another perspective, popularised by Denoising Diffusion Probabilistic Models (DDPM), treats time as discrete steps.

- **Forward**: 

$$
q(\mathbf{x}_t | \mathbf{x}_{t-1}) = \mathcal{N}(\mathbf{x}_t; \sqrt{1-\beta_t}\mathbf{x}_{t-1}, \beta_t \mathbf{I})
$$

- **Reverse**: We learn to approximate the posterior

$$
q(\mathbf{x}_{t-1} | \mathbf{x}_t)$ using a neural network $p_\theta(\mathbf{x}_{t-1} | \mathbf{x}_t)
$$

Training this model by maximising the Variational Lower Bound (ELBO) simplifies mathematically to the same objective as **Denoising Score Matching**: simply training a network to predict the noise $\epsilon$ that was added to the image.

## Sampling with Predictor-Corrector

Since we have both an SDE and a score function, we can use powerful numerical solvers for sampling.

- **Predictor**: Use a standard numerical SDE solver (like Euler-Maruyama) to take a step in the reverse direction.
- **Corrector**: Use Langevin Dynamics to "correct" the sample, moving it towards higher probability density at the current noise level before taking the next step.

This combination allows for high-fidelity generation that is more stable and easier to train than GANs.
