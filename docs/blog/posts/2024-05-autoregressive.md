---
draft: false
date: 2024-05-08
slug: autoregressive 
categories:
  - genai
tags:
  - ai
  - neural networks
---

# Autoregressive models

Autoregressive models as the name implies, generates data by predicting each element sequentially based on the elements previously generated. They are naturally aligned to tasks involving sequential dependence like natural language, audio and time-series data. However, autoregressive models have also been successfully applied to image generation.

<!-- more -->

- **GPT** (Generative Pre-trained Transformer) is a language model that predicts the next word or token based on previously generated text.
- **PixelCNN and PixelRNN** generate images pixel by pixel, with each pixel depending on the pixels generated before it.
- **WaveNet** produces audio samples one at a time, with each sample conditioned on prior samples, making it suitable for realistic speech and audio synthesis.

## Factorisation of a probability distribution

In the autoregressive approach we are either dealing with an ordered sequence (language) or unordered vector samples (images) and in general the data can be thought of as multi-dimensional vectors and their probability distributions are best represented as joint distributions over all these dimensions. A joint distribution over an n-dimensional vector can be stated accurately using the chain rule of probabilities as

$$
p(x_1, x_2, \dots, x_n) = p(x_n | x_{n-1}, \dots, x_2, x_1) \dots p(x_3 | x_2, x_1) \cdot p(x_2 | x_1) \cdot p(x_1)
$$

The principle behind autoregression is to compute this factorisation sequentially. For large dimensions this factorisation requires an exponentially large number of parameters (number of possible states that the distribution can take, also known as the support of the distribution). A **Bayesian network** is a graphical representation of such a probability distribution and in the unsimplified case above, a fully connected **directed acyclic graph (DAG)** with nodes representing the elements of the vector and the directed edges representing the dependence.

### Conditional independence in a Bayesian network

One way to simplify the computation of the joint distribution is to assume conditional independence - equivalent to removing certain edges from the Bayesian network. The simplest example of this would be a Markov model where an element (or node in a DAG) is independent of every other (historical) element given the immediate previous one

$$
p(x_{i} \perp x_{i-2},\dots,x_2,x_1 | x_{i-1})
$$

and the chain rule factorisation simplifies to

$$
p(x_1, x_2, \dots, x_n) = p(x_n | x_{n-1}) \dots p(x_3 | x_2) \cdot p(x_2 | x_1) \cdot p(x_1)
$$

A general Bayesian network would be one where every value is conditionally dependent on a few others ($\ll n$) thereby simplifying the joint distribution to

$$
p(x_1, x_2, \dots, x_n) = \prod_{i=1}^n p(x_i | \hat{\mathbf{x}_i})
$$

where $\hat{\mathbf{x}_i}$ denotes the subset of elements of $\mathbf{x}$ on which $x_i$ is conditionally dependent.

## Chain rule based autoregressive generators

Another way to simplify the computation is to assume that there exists a computable function that can closely approximate the conditional probabilities. If we are dealing with discrete variables, then this function is a probability mass function (PMF), while if we are dealing with continuous variables then it is the probability density function (PDF). The structure remains the same and for the case of a fully connected network (chain rule factorisation) the implementation can be represented by the figure below where the $N_i$ blocks represent the individual functional approximations of the conditional probabilities with each model (increasing $i$) being progressively more complex than the previous one.

```mermaid
graph TD
    %% Input Nodes
    x0((x.)) --> N1
    x1((x₁)) --> N2
    x1((x₁)) --> N3
    x1((x₁)) --> N4
    x2((x₂)) --> N3
    x2((x₂)) --> N4
    x3((x₃)) --> N4

    %% Process Blocks
    N1[N₁] --> y1(("p(x₁)"))
    N2[N₂] --> y2(("p(x₂|x₁)"))
    N3[N₃] --> y3(("p(x₃|x₂,x₁)"))
    N4[N₄] --> y4(("p(x₄|x₃,x₂,x₁)"))

    %% Additional Inputs
    x4((x₄))
```

**Fully visible sigmoid belief networks (FVSBN)** use logistic regression for each of the individual models. Results however are not good with this technique for image generation, since logistic regression is not complex enough to capture relationships in the images.

Using neural networks instead, works better as is the case with **neural autoregressive distribution estimator (NADE)** which uses single layer neural networks for each of the models. Unlike logistic regression, a neural network can introduce non-linearities and is therefore much more flexible in learning features and the results are much more impressive. Parameter sharing (tying weights) reduces parameters, speeds up training and generation. If we use a similar architecture to model a general Bayesian network with conditional independence simplifications it reduces to

$$
p(x_1, x_2, \dots, x_n) = \prod_{i=1}^n p_{neural}(x_i | \hat{\mathbf{x}_i})
$$

where $\hat{\mathbf{x}_i}$ are the nodes of the DAG on which $x_i$ is conditionally dependent. Additionally this Bayesian network needs to satisfy the "Markov" ordering constraints imposed by the chain rule factorisation (so that generation is possible), which means

$$
\hat{\mathbf{x}_i} \subset \{x_1, x_2, \dots x_j\} \implies j < i
$$

???+ info "Universal Approximation Theorem"

    A feedforward neural network with at least one hidden layer and a
    sufficiently large number of neurons can approximate any continuous function
    to any desired degree of accuracy, provided the network uses a non-linear
    activation function (like a sigmoid or ReLU)

Generative models using the chain rule factorisation however require as many neural networks as there are input dimensions - each modelling a single conditional probability.

### Generating from the autoregressive model

Generating from the chain rule based autoregressive model has a similar architecture to the one used for training.

- $x_1$ is sampled from the marginal prior distribution $p(x_1)$.
- This is input to the first neural network $N_2$ to obtain $p(x_2|x_1)$ and $x_2$ is sampled from this distribution.
- This process is repeated sequentially for all $x_{i \le n}$ to obtain the complete generation.

As is obvious from the steps above, the generative process from an autoregressive model is sequential with each $x_i$ from the generated vector, generated sequentially from the previously generated values.

## Autoencoders as autoregressive generators

Autoencoders are generally used to obtain a compressed representation of the inputs. Their structure being that of a generalised Bayesian network can be used to modify them to function as an autoregressive model under certain constraints. The conditional probabilities are

$$
p(x_1, x_2, \dots, x_n) = \prod_{i=1}^n p_{neural}(x_i | \hat{\mathbf{x}_i})
$$

where $\hat{\mathbf{x}_i}$ are the nodes of the DAG on which $x_i$ is conditionally dependent.

```mermaid
graph TD
    %% Input Nodes
    x1((x₁)) --> N
    x2((x₂)) --> N
    x3((x₃)) --> N
    x4((x₄)) --> N

    %% Process Blocks
    N[Autoencoder] --> y4(("p(xᵢ|x̂ᵢ)"))
```

A vanilla autoencoder however, is not a generative model. Since the DAG modelled by the neural network does not have any inherent ordering, it does not provide us with a meaningful way to generate samples from the model because the model is non-causal with respect to the input sequence.

If we are able to constrain the DAG so as to force some kind of sequential ordering on the dependencies similar to what we achieve using chain rule factorisation, we can use the autoencoder as an autoregressive generative model. This is precisely what is done in a **masked autoencoder for distributed estimation (MADE)** where masks are used to disallow certain paths in the DAG so as to follow an ordered dependency sequence. The advantage of such a model over the chain rule factorisation is that a single neural network (deep) can model the joint probability distribution.

The generative process is however exactly the same as before and is sequential and time consuming.


