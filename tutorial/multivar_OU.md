---
layout: default
title: Multivariate Ornstein-Uhlenbeck models
permalink: /tutorial/multivar_OU/
---


## Multivariate Ornstein-Uhlenbeck models

Multivariate Ornstein-Uhlenbeck models allow for testing a range of different adaptive hypotheses of trait evolution, as detailed below. Adapted to describe evolution of traits within the same lineage, the multivariate Ornstein-Uhlenbeck process is described by the following differential equation ([Bartoszek et al. 2012](https://www.sciencedirect.com/science/article/pii/S0022519312003918); [Reitan et al. 2012](https://www.jstor.org/stable/41713485); [Clavel et al. 2015](https://besjournals.onlinelibrary.wiley.com/doi/full/10.1111/2041-210X.12420)):

$$
dY = A(\theta(t) - Y(t))dt + RdW(t)
$$

where __A__ is a square matrix that describes the rate of evolution toward the optimal trait values and with dimensions equal to the number of investigated traits (__A__ is often called the pull matrix), $\theta$ is a vector containing the optimum for each trait, __R__ is a square matrix describing the stochastic changes in the traits and with dimensions equal to the number of investigated traits (__R__ is often called the drift matrix), and $W$ is the diffusion parameter. Under the assumption that we only have one selective regime (optimum) per trait, the expected trait mean of the Ornstein-Uhlenbeck process is the weighted sum of the optimum and the root value ([Hansen 1997](https://onlinelibrary.wiley.com/doi/abs/10.1111/j.1558-5646.1997.tb01457.x)):

$$
E[Z_{i}] = e^{(-At_{i})}z_{0} + (1 - e^{(-At_{i})})\theta
$$


where 𝑍𝑖
 is a vector containing the expected trait values for sample i, 𝑧0
 is a vector containing the ancestral trait means, 𝜃
 is a vector containing the optima, and 𝑡𝑖
 is the time interval from the ancestral population mean (the start of the time-series, which has a time of 0) to the ith population mean.

The variance and covariance of sample/population means are given by the following expression:

𝐶𝑜𝑣(𝑧𝑖,𝑧𝑗)=[𝑃([1𝜆𝑘+𝜆𝑙(1−𝑒−(𝜆𝑘+𝜆𝑙)𝑡𝑎)]1≤𝑘𝑙≤𝑚⊙𝑃−1ΣΣ𝑇(𝑃−1)𝑇)𝑃𝑇]𝑒−𝐴𝑇𝑡𝑖𝑗

where P is the orthogonal matrix of eigenvectors of A, ΣΣ𝑇
 is the Cholesky decomposition of the R matrix, 𝜆𝑖
 is the ith eigenvalue of A, 𝑡𝑎
 is the time interval from the ancestral population to the oldest of the two populations 𝑧𝑖
 and 𝑧𝑗
, and 𝑡𝑖𝑗
 is the time separating two samples 𝑧𝑖
 and 𝑧𝑗
, while ⊙
 represents the Hadamard (element-wise) product.

Different ways to parameterize the A matrix allows testing a range of evolutionary hypotheses using the multivariate OU process. While the R matrix in the multivariate version of the unbiased random walk (and in the multivariate OU model) either needs to be diagonal (only elements on the diagonal while off-diagonal elements are zero) or complete (in the sense that both the diagonal and off-diagonal elements are non-zero), this is not the case for the A matrix. Generally, we can divide up the hypotheses being tested using the multivariate OU into four different types:

Independent evolution (only diagonal elements in both the A and the R matrix, i.e., equivalent to fitting univariate models to each trait separately.)

Independent adaptation (only diagonal elements in the A matrix while the R matrix is completely parameterized. In such a model, the traits are adapting independently toward their optima, but the stochastic changes in the traits are correlated.)

At least one trait affects the optimum of the other trait. The diagonal and at least one of the off-diagonal elements in the A matrix is non-zero. It is the trait in the column of the non-zero off-diagonal element that affects the optimum of the trait in the row of the non-zero off-diagonal element. A negative off-diagonal element means the trait evolves toward the optimum determined by the other trait, while a positive off-diagonal element means the trait evolve away from the optimum determined by the other trait. The stochastic changes in the trait can be either correlated or non-correlated.

The same traits affect each others optimum (but to different degrees). The same off-diagonal elements on both sides of the diagonal are parameterized in the A matrix. The stochastic changes in the trait can be either correlated or non-correlated.

For a data set consisting of two traits, there are four possible parameterizations of the A matrix (see panels below). Independent adaptation of both traits (panel a), trait Y affecting the optimum of trait X (panel b), trait X affecting the optimum of trait Y, and the traits X and Y affecting each others optima (panel d). Each of these four ways of parameterizing the A matrix can be combined with an R matrix with elements only on the diagonal (the stochastic changes in the traits are uncorrelated) or a completely parameterized R matrix (the stochastic changes in the traits are correlated).
