---
draft: false
date: 2023-10-03
slug: bv
categories:
  - machine learning
tags:
  - ai
  - ml
---

# Bias variance tradeoff 

The bias-variance tradeoff explains the relationship between a model's complexity and predictive capability vs it's generalisation capabilities. It provides us a framework to balance overfitting and underfitting.

<!-- more -->

**Bias** is a measure of underfitting and refers to the error introduced by an underfit model in explaining a complex real-world phenomenon. 

**Variance** on the other hand is a measure of overfitting and refers to the sensitivity of the model to changes in the training data.

The tradeoff refers to the objective of balancing the model in such a way that it is sufficiently complex to learn the underlying features well without memorising the training data. 

**Overfitting** can be overcome in deep neural networks by L2 regularisation, early stopping and using sufficient and diverse training data. **Transfer learning**, since it is pre-trained on large diverse datasets is more immune to overfitting on noise and minor feature patterns in the training data. 

Very large and deep neural networks although having a tremendous propensity to overfit, ironically avoid overfitting when trained with large and diverse training data with proper regularisation in what is called the **over parameterisation paradox**.