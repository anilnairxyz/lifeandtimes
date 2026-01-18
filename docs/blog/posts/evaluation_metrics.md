---
draft: false
date: 2025-06-30
slug: evaluation_metrics
categories:
  - genai
tags:
  - ai
  - neural networks
  - evaluation
  - fid
  - inception score
---

# Evaluating Generative Models

Evaluating generative models is notoriously difficult. Unlike discriminative tasks (classification) where accuracy is a clear metric, "generation quality" is subjective and multidimensional. How do we define if a generated image is "good"? It needs to be realistic (fidelity) but the model also needs to generate a wide variety of images (diversity) and not just copy the training set (novelty).

<!-- more -->

## Density Estimation Metrics

For exact likelihood models (Flows, Autoregressive) or approximate ones (VAEs), we can evaluate the model based on how well it fits the data distribution.

- **Log-Likelihood**: We calculate the average log-likelihood assigned to a held-out test set $E_{p_{data}}[\log p_\theta(\mathbf{x})]$. Higher is better.
- **Perplexity**: Common in Likelihood Models (LLMs), it is the exponential of the negative log-likelihood. Lower is better.
- **Compression Rate**: Measured in bits-per-dimension. Since a good generative model is an efficient compressor, lower BPD indicates better modeling of the data structure.

## Sample Quality Metrics

For implicit models like GANs, we cannot calculate likelihoods. We must evaluate the samples directly.

### Inception Score (IS)
One of the first widely used metrics for images. It uses a pre-trained Inception classifier to measure two things:
1.  **Sharpness**: The classifier should be confident about what object is in the image (low entropy of $p(y|\mathbf{x})$).
2.  **Diversity**: The distribution of predicted classes across all generated images should be uniform (high entropy of marginal $p(y)$).
$$
IS = \exp(\mathbb{E}_{\mathbf{x}} [D_{KL}(p(y|\mathbf{x}) || p(y))])
$$

### Fréchet Inception Distance (FID)
FID improves upon IS by comparing the statistics of the generated images against the *real* images, rather than just looking at the generated images in isolation.
- It extracts feature vectors from an intermediate layer of the Inception network.
- It assumes these features follow a Gaussian distribution.
- It calculates the Fréchet distance (Wasserstein-2 distance) between the Gaussian of real features and the Gaussian of fake features.
- **Lower FID is better**, as it indicates the generated distribution is statistically close to the real distribution.

## Human Evaluation
Ultimately, for perceptual tasks, human evaluation remains the gold standard. Methods like **HYPE** (Human eYe Perceptual Evaluation) formalize this testing (e.g., measuring the time it takes for a human to distinguish real from fake).

However, human evals are expensive and slow, which keeps automated metrics like FID and Log-Likelihood as the standard workhorses for model development.
