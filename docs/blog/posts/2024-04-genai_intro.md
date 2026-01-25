---
draft: false
date: 2024-04-10
slug: genai_intro 
categories:
  - genai
tags:
  - ai
  - neural networks
---

# Goals of a generative model

The primary objective of a generative model (with parameters $\theta$) is to approximate via $P_\theta(\mathbf{x})$ the data distribution $P_{data}(\mathbf{x})$ given a sufficiently large set of training samples from an independent and identically distributed training distribution $P_{train}(\mathbf{x})$. 

<!-- more -->

However an effective generative model would also be able to capture patterns, dependencies and features present in the data. And there is a trade-off between maximising likelihood (overfitting to $P_{train}(\mathbf{x})$) vs extracting the latent space structure so that features can be learnt and modelled.

Along with these primary objectives, a good generative model should also support efficient sampling from $P_\theta(\mathbf{x})$ laying emphasis on diversity and fidelity of generated samples.

## Model parameter estimation

Most machine learning techniques have to make a choice on how they estimate parameters of the model, generally based on the phenomenon they try to model. 

### Maximum a posteriori (MAP)

If there exists some kind of a-priori knowledge of the phenomenon (e.g. physical laws or financial laws), then these could be used as a start to the modelling process. Further fine-tuning of the parameters are then made based on the training samples. MAP incorporates prior knowledge through a prior distribution $P(\theta)$. It finds the parameter that maximises the posterior distribution $P(\theta | \mathbf{x})$. Using Bayes theorem, the posterior distribution is given by

$$
P(\theta | \mathbf{x}) = \frac{P(\mathbf{x} | \theta) P(\theta)}{P(\mathbf{x})}
$$

Since $P(\mathbf{x})$ does not depend on $\theta$, it can be ignored during optimisation. Thus the MAP estimate $\hat{\theta}_{\text{MAP}}$​ is the parameter that maximises the posterior (log for simplifying the computations)

$$
\hat{\theta}_{\text{MAP}} = \arg \max_{\theta} \big( \log P(\mathbf{x} | \theta) + \log P(\theta) \big)
$$

### Maximum  likelihood estimation (MLE)

In generative models typically, there is no prior distribution available to begin the parameter optimisation. Therefore frequentist methods of estimation like maximum likelihood are employed. The objective here reduces to finding the parameters $\hat{\theta}_{\text{MLE}}$ that maximises the likelihood of the observed data $\mathbf{x}$. 

$$
\hat{\theta}_{\text{MLE}} = \arg \max_{\theta} \big(\log P(\mathbf{x} | \theta) \big)
$$

???+ info "Frequentist statistics"

    Frequentist statistics is a framework for statistical inference based on the
    idea that probabilities are long-run frequencies of events. Frequentist 
    methods do not incorporate prior information about the parameters. Instead,
    they rely solely on the observed data to make inferences.

## Analysing probability distributions

Since we aim to model probability distributions, we need some means of analysing different distributions as well as metrics for comparing distributions. Shannon's information theory gives us a good basis for analysing probability distributions in terms of their information content and compressibility.

???+ info "Shannon's Information Theory"

    In [A Mathematical Theory of Communication](https://people.math.harvard.edu/~ctm/home/text/others/shannon/entropy/entropy.pdf), Shannon laid the groundwork for 
    information theory by introducing concepts like entropy, channel capacity,
    and coding theory. His work showed how information can be transmitted
    reliably over noisy channels and how data can be compressed efficiently.

### Entropy of a distribution

The idea of entropy was introduced to understand the information content of a distribution. Information theory defines information in terms of the uncertainty or surprise associated with random variable. Entropy quantifies the expected amount of "surprise" in observing outcomes from a distribution. For a random variable $\mathbf{x}$ with possible outcomes $x$ the entropy is given by

$$
H(\mathbf{x}) = -\sum_x P(x) \log P(x)
$$

A high entropy indicates a distribution with high uncertainty (e.g., a uniform distribution where all outcomes are equally likely), while a low entropy indicates a more predictable distribution (e.g., a delta distribution concentrated on a single outcome). The Shannon entropy, when calculated with a base-2 logarithm, measures information in bits, providing a direct interpretation of information content that is both intuitive and mathematically optimal for digital communication. It effectively gives us the minimum bits needed to optimally compress a random variable given its probability distribution.

In generative AI, the concept of entropy when extended to cross-entropy between two probabilities and its associated distance metrics gives us the necessary tools to compare probability distributions.

### Cross entropy of two distributions

Cross-entropy is a measure of the difference between two probability distributions and is widely used as a loss function in machine learning (ML) models. When used for example in generative AI, cross-entropy quantifies the "distance" between the true distribution (the actual samples from $P_{data}$) and the model distribution (generated from $P_\theta$). The lower the cross-entropy, the closer the model's generation mimics the data distribution. 

For discrete probability distributions $P$ and $Q$ over the same set of events, the cross-entropy $H(P,Q)$ is given by 

$$
H(P, Q) = -\sum_{x}P(x)\log Q(x)
$$

Cross-entropy can be decomposed into two parts: the entropy of the true distribution and an additional term known as the KL divergence (or relative entropy) between the true distribution and the model distribution. This decomposition highlights how cross-entropy combines information from the true distribution’s inherent uncertainty and the "extra cost" of using the model distribution 

$$
H(P, Q) = H(P) + D_{KL}(P||Q)
$$

Intuitively, in Information Theory parlance, the cross entropy decomposition can be thought of as representing the number of bits needed to compress the true distribution optimally and the KL divergence being the additional bits needed if information is compressed according to the model distribution.

### Distance measure (KL divergence)

This leads us to a possible distance measure between distributions $P$ and $Q$ when using Shannon's entropy and cross-entropy which is the Kullback-Leibler (KL) divergence given by

$$
D_{KL}​(P||Q)=\sum_x​P(x)log\frac{P(x)}{Q(x)}​
$$

The divergence $D_{KL}(P||Q) \ge 0$ for all $P$, $Q$ with equality if and only if $P = Q$ and therefore it can be used as a reasonable measure to compare distributions although it is not a true metric.

???+ info "Metric space theory and true distance metrics"

    The notion of a metric comes from metric space theory in mathematics,
    particularly within topology and geometry. The metric concept provides a
    formal way to measure "distance" between elements in a space. A **metric** on
     a set $X$ is a function $d: X \times X \rightarrow \mathbb{R}$ that
     satisfies the following properties:
     
     1. Non-negativity: $d(x, y) \geq 0$ 
     2. Identity of indiscernibles: $d(x, y) = 0$ if and only if $x=y$
     3. Symmetry: $d(x,y)=d(y,x)$ 
     4. Triangle inequality: $d(x, z) \leq d(x, y) + d(y, z)$

The KL divergence is not a true distance metric because it is not symmetric and it does not obey the triangle inequality. However there are other measures of distance (Wasserstein, Jensen-Shannon etc.) based on other "divergences" and "entropy" definitions (generalised as f-divergences), some of which are true metrics.

### KL divergence in generative modelling

For a generative model $P_\theta(\mathbf{x})$ with the corresponding data distribution $P_{data}(\mathbf{x})$, the KL divergence can be stated as

$$
\begin{split} D_{KL}​(P_{data}||P_\theta) &= \mathbb{E}_{\mathbf{x}\sim P_{data}} ​\left[\log \left( \frac{P_{data}(\mathbf{x})}{P_\theta(\mathbf{x})} \right) \right] \\ &= \mathbb{E}_{\mathbf{x}\sim P_{data}} ​\left[\log P_{data}(\mathbf{x}) \right] - \mathbb{E}_{\mathbf{x}\sim P_{data}} ​\left[\log {P_\theta(\mathbf{x})} \right] \end{split}​
$$

And since the first terms does not depend on $P_\theta$, minimising KL divergence in generative modelling reduces to maximising the expected log-likelihood (MLE)

$$
\arg \min_{\theta} D_{KL}​(P_{data}||P_\theta) = \arg \max_{\theta}\mathbb{E}_{\mathbf{x}\sim P_{data}} ​\left[\log {P_\theta(\mathbf{x})} \right]
$$

This also shows us that although we can compare models $P_{\theta_1}$ and $P_{\theta_2}$ in which of them more closely models the true data distribution $P_{data}$, we cannot know how close we are to the $P_{data}$ itself with either of them.

In practise we approximate the expected log-likelihood with the empirical log-likelihood over training samples $D$. This follows from Monte Carlo estimation.

$$
\hat{\theta}_{\text{MLE}} = \max_{\theta} \frac {1}{|D|}\sum_{x \in D}\log P_\theta(x)
$$

???+ info "Monte Carlo estimation"

    Monte Carlo Estimation is a method for approximating an unknown quantity,
    typically an expectation, integral, or sum, using random sampling. It is 
    particularly useful when direct analytical computation is intractable due to 
    high dimensionality or complexity.

