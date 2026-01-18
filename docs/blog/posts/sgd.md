---
draft: false
date: 2023-11-04
slug: sgd
categories:
  - learning
tags:
  - ai
  - ml
  - neural-net
---

# Stochastic gradient descent

Stochastic gradient descent is an iterative optimisation technique used to optimise model parameters of a neural network model during training by minimising the error function. 

<!-- more -->

## Neural network training ##

A training cycle for a neural network involves the following steps:

1. **Forward pass** for a single training sample to compute the loss vector
2. Accumulate losses over a set of samples and compute the sum / mean of the errors
3. **Back propagation** to compute the gradient of the aggregated loss with respect to all the neural network parameters (weights and biases)
4. Update the parameters using the computed gradients throttled by a learning rate

## Gradient descent ##

In the early machine learning problems (eg Titanic problem in Kaggle), with limited training data, the whole training data could be passed through the neural network during training due to limited memory requirements. The **standard method of gradient descent** then was to use the complete batch of training data in a single training cycle. This would then be repeated over multiple passes of the entire batch (called epochs).

## Stochastic gradient descent (SGD)##

As training data became massive with the advent of deep learning and large neural networks, storing the entire batch in memory for gradient computation became impossible. Standard gradient descent updated the parameters too infrequently slowing down learning and convergence. With the advent of GPUs, more frequent computations of the gradients became practical. 

SGD uses stochastic mini batches (or even single samples) per training cycle, thereby leveraging the computational advancements to make the training process more scalable for deep learning. A collateral benefit of SGD was that it was found to perform an implicit regularisation due to the slight variability of the mini batches. This "noise" helps the model avoid overfitting and improves generalisation.

SGD has now become the de-facto optimisation method in deep learning.

