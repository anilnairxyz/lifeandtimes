---
draft: false
date: 2023-10-08
slug: gnn
categories:
  - learning
tags:
  - classification
  - ml
---


# Support vector machines

Support vector machines are the most popular application of kernel methods which are a class of algorithms used to simplify non-linear classification problems by projecting the data onto higher dimensions so as to be able to apply linear regressions.

<!-- more -->

## Feature space transformation ##

Kernel methods use the concept of feature space transformation to project the input data onto one or more higher dimensions. The non-linear classification problem in the input vector space consequently gets modified to a linear classification problem in the higher dimensional space and one can find a hyperplane to classify the data.

## Kernel trick##

Normally when one does a feature space transformation, it requires computing the transformed space for the input data sets and then computing the cosine distance (dot product) of the samples in the the transformed space. The kernel trick uses a kernel function which directly gives this dot product without having to transform the data into the higher dimensional space.
## Kernel functions ##

The most commonly used kernel functions are: 
**Polynomial Kernel of different degrees**

$$
K(x,y)=(x⋅y+c)^d
$$
where d is the degree of the the polynomial. 
**Radial basis function kernel**

$$
K(x,y)=e^{(−γ∥x−y∥^2)}
$$

is the default choice for non-linear problems due to its flexibility and general applicability.
## Support vector classifier ##

Support Vector Machines (SVMs) aim to classify data points by finding the optimal **hyperplane** that separates two classes of data with the **maximum margin**. A hyperplane in $\mathbb{R}^n$ is defined as:

$$
f(\mathbf{x}) = \mathbf{w} \cdot \mathbf{x} + b = 0
$$

where $\mathbf{w}$ is the normal vector to the hyperplane and $b$ is the bias term (intercept)
The hyperplane separates the two classes:

- If $f(\mathbf{x}) > 0: \mathbf{x}$ belongs to Class $+1$
- If $f(\mathbf{x}) < 0: \mathbf{x}$ belongs to Class $−1$

Initially, the hyperplane can be randomly assigned or set using a heuristic method (e.g., least-squares approximation). The **margin** is defined as the distance between the hyperplane and the closest points from either class (called **support vectors**). The **goal of the SVM** is to find the hyperplane that maximises the margin between the two classes.

**Soft Margin SVM** allow some errors where perfect separation may not be possible such as in the case of most real-world data.

## Comparison of classification methods ##

| **Aspect**                         | **SVMs**                 | **Gradient Boosting** | **Neural Networks**            |
| ---------------------------------- | ------------------------ | --------------------- | ------------------------------ |
| **Dataset Size**                   | Small to Medium          | Medium to Large       | Large                          |
| **Scalability**                    | Poor                     | Good                  | Excellent                      |
| **Data Type**                      | High-dimensional, Sparse | Tabular               | Unstructured (images, text)    |
| **Interpretability**               | Moderate                 | High                  | Low                            |
| **Ease of Tuning**                 | Moderate                 | Moderate              | Complex                        |
| **Computational Cost**             | Moderate                 | Moderate              | High                           |
| **Performance on Non-Linear Data** | Excellent (with kernels) | Excellent             | Excellent                      |
| **Output**                         | Class Labels or Margins  | Probabilities         | Probabilities                  |
| **Domain Examples**                | Text, Bioinformatics     | Finance, Healthcare   | Images, Text, Complex Patterns |