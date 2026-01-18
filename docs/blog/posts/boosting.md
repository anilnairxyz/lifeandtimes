---
draft: false
date: 2023-10-06
slug: gnn
categories:
  - neural networks
tags:
  - ml
  - classification
  - ensemble
---


# Gradient boosting

[Gradient boosting](https://explained.ai/gradient-boosting/) is an ensemble technique that creates strong learning models by iteratively adding the predictions from weak learners. 

<!-- more -->

In the case of decision trees these weak learner are typically shallow decision trees and each new iteration attempts to add trees that correct the errors made by the previous combined ensemble guided by the gradient of a loss function.

Another way to look at these iterations are as additive models progressively developing an increasingly accurate composite model by adding simpler models in a greedy manner.

## Methodology ##

Lets take a concrete example to understand gradient boosting. Lets say we have a n-sample training set with 5 factors / inputs $(x_1, x_2, x_3, x_4, x_5)$ and we are solving a 3-class $(a, b, c)$ classification problem.

### Initial weak tree ###

Gradient boosting methods start with an initial weak model such as a binary split using a favourable feature or even a uniform probability distribution among all the classes - in our example case this would mean that we initially assign $(p_a, p_b, p_c) = (1/3, 1/3, 1/3)$ for all training samples.

### Loss Function ###

We then use a loss function to measure the discrepancy between predicted and actual values. For classification problems, a common loss function is **log loss** or cross-entropy loss. For a single training example with true labels $(y_a, y_b, y_c)$ and predicted probabilities $(p_a, p_b, p_c)$ the multi-class cross-entropy loss is given by

$$
\text{Loss} = -[y_a \log(p_a) + y_b \log(p_b) + y_c \log(p_c)]
$$

### Gradient calculation ###

The gradient of the loss function with respect to the predicted probabilities is then computed. For the cross-entropy loss above, the gradient of the loss reduces to the **residual** for a particular class

$$
{\partial L}_j = p_j - y_j
$$

### Next tree training###

For multi-class classification, gradient boosting typically builds one tree per class per iteration. Each tree is trained on the corresponding set of residuals (gradients) for that class, using the original input features. 

In our 3-class example, we would build three trees in parallel (or sequentially), one for each class’s residuals. Each tree tries to best fit the residuals for that specific class dimension. Effectively what we are training these new trees to do is to predict the negative residuals so that when added to the original ensembles outputs, the final predictions we get come closer to the true labels. 

The tree-building algorithm (e.g., greedy splitting) attempts to find splits on input features $(x_1, x_2, x_3, x_4, x_5)$ that lead to more homogeneous residual values within leaves. This means that leaves represent regions of the input space where the model needs similar corrections for that particular class’s predictions.

After these three new trees are built, they are added to the current ensemble. The model updates the predictions for each class by adding the outputs of the corresponding tree (multiplied by the learning rate) to the previous scores to obtain refined predictions for the individual classes.

### Subsequent iterations ###

The process of computing the loss function, it's gradient and generating 3 new trees continues until a desired number of iterations is reached or any of the other stopping criteria are met. Typical stopping criteria may include predefined loss threshold or non improvement of a validation score based on a validation set (taken out of the training set).
