---
draft: false
date: 2023-10-05
slug: trees
categories:
  - machine learning
tags:
  - ml
  - classification
  - ensemble
---


# Decision trees and forests

Decision trees are hierarchical rule based models that provided an advancement over logistic regression in classification problems - for example in solving the XOR problem.

<!-- more -->

## Decision trees ##

In many problems like the Titanic competition on Kaggle, it is observed that a single binary decision is much more accurate in predicting outcomes than more complicated methods like regressions. In the Titanic case for instance whether a person survives or not is rather well predicted by whether they were male or female or which class their ticket belonged to. Now if we build a sequence of decision as a decision tree, the leaf nodes would give us the probability of the outcome for an unknown input by simply traversing the tree.

The advantages of decision trees are that 

- They require no transformation of the input variables because they do not combine the variables in building or traversing the tree. 
- They can handle categorical variable without encoding
- Since this is a multilayer function with a non-linear step function as activation, it ultimately results in a non-linear decision boundary and can handle XOR like problems. 
- Since the decisions are based on input features, the predictions are easily interpretable compared to neural networks or logistic regression

The feature to be used as the conditional at every node in partitioning the data (order of decisions) is determined based on criteria like the **Gini impurity** (the probability of misclassifying a randomly chosen sample) or **information gain** (the reduction in entropy after a dataset is split on a feature).

A comprehensive decision tree exactly mimics the training data with each leaf node being a **pure subset** and is the very definition of overfitted. To avoid this the tree is pruned after a certain depth using a stopping condition e.g., maximum tree depth, minimum samples per leaf, or pure subsets. However single decision trees are still prone to overfitting, but ironically they also suffer from high sensitivity to changes in training data, thereby leading to high variance.

## Bagging - Random forests ##

Bagging is an **ensemble technique** to reduce variability and overfitting by creating different trees and taking a majority vote of the predictions. 

In **random forests** the different trees are trained using bootstrapped samples (repeatedly sampling from the training set with *replacement*). Additionally each tree also uses a random subset of features at each split. These randomisations and sampling methods further de-correlate the trees, typically leading to better performance.