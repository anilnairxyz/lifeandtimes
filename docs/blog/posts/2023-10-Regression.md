---
draft: false
date: 2023-10-02
slug: regression
categories:
  - machine learning
tags:
  - ai
  - ml
---


# Linear and logistic regression

Linear and logistic regression are the simplest models used in supervised learning tasks like modelling a dependent variable and classification.

<!-- more -->

## Modelling a dependent variable ##

**Linear regression** is used to model a dependent variable by fitting a straight line as close as possible to the data points

$$
y = w_0 + w_1 x_1 + w_2 x_2 + \dots + w_n x_n
$$

by minimising the mean square error 

$$
MSE = \frac{1}{n}\sum_{​i=1}^{n}​(y_i​ − \hat{y}_i​)^2
$$

## Linear binary classification ##

Linear regression can also be used for binary classification, however in classification problems the goal is to find the probability of a data point belonging to a certain class. Linear regression being unbounded is unsuitable for this purpose. **Logistic regression** simply adds a non-linear sigmoid function to the output of a linear regression (the logit) to bound the values to legitimate probabilities.

$$
p = \sigma(z) = \frac{1}{1 + e^{−z}​}
$$

Since we are trying to model probability distributions here, the loss we try to minimise here is the cross-entropy between the sample distribution and the predicted distribution

$$
Loss  = -\frac{1}{N}\sum_{i=1}^{N}​[y_i​\log(p_i​)+(1−y_i​)\log(1−p_i​)]
$$

Minimising the cross-entropy is equivalent to maximising log likelihood of the training samples (MLE).

## Categorical classification ##

**Multinomial logistic regression** is an extension of logistic regression to cover categorical classifications. For $K$ classes the model computes the linear decision boundary (logit) for each class 

$$
z_k ​= w_{0,k}​+w_{1,k​}x_1​+w_{2,k}​x_2​+\dots+w_{n,k}​x_n​
$$

and then converts these into probabilities using the softmax function

$$
P(y=k∣x)=\frac{e^{z_k}}{\sum_{j=1}^{K}​e^{z_j}}​​​
$$

Multinomial logistic regression minimises the **cross-entropy loss**, which generalises the log-loss for multiple classes. 

## Regularisation ##

The main aim of regularisation is to prevent overfitting and improve generalisation. In logistic regression models there are several levels of regularisation

1. **L1 regularisation (Lasso)** for feature selection (driving certain coefficients to zero) of the most important features encouraging sparsity of the model
3. **L2 regularisation (Ridge)** prevents overfitting by constraining the weights (shrinking the coefficients) thereby reducing the variance of the model

## Limitations ##

There is a limited subset of problems that lend itself to a ** linear decision boundary**. For example the simple XOR problem cannot be solved by logistic regression.

Another limitation is the sensitivity to feature scaling. Normalisation techniques can help here, but at the end of the day we are tampering with the input data thereby introducing inefficiencies.

