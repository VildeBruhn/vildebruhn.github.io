---
layout: default
title: Multivariate models
permalink: /tutorial/multivar_models/
---

# Multivariate models

Traits are rarely changing independently of each other due to shared genetics or development. Evolution is accordingly a multivariate phenomenon. The evoTS package includes functions to fit the following multivariate trait models:

* Multivariate unbiased random walks

* Multivariate decelerated evolution

* Multivariate accelerated evolution

* Multivariate Ornstein-Uhlenbeck processes


## 1. Multivariate unbiased random walks with and without changes in the rate of evolution

Evolution as a multivariate unbiased random walk is modeled using an evolutionary rate matrix __R__ ([Felsenstein 1988](https://www.annualreviews.org/content/journals/10.1146/annurev.es.19.110188.002305); [Revell and Harmon 2008](https://faculty.umb.edu/liam.revell/pdfs/Revell_and_Harmon_2008.EER.pdf)). The diagonal elements in __R__ represent the rate of evolution for the individual traits, while the off-diagonal elements represent the extent to which different traits co-evolve.

The multivariate variance-covariance matrix for the unbiased random walk model (__V__) is computed using the [Kronecker product](https://en.wikipedia.org/wiki/Kronecker_product) of the __R__ matrix and a “distance matrix” __C__, describing the distance in time between the different samples/populations in the time series.


$$
V = \sum_{i=1}^{m} R \otimes C
$$


Sampling error of the trait mean (calculated as the sample variance divided by the sample size) is – as for all models in evoTS – added to the diagonal of the __V__ matrix. To ensure symmetric positive definiteness of the __V__ matrix during log-likelihood optimization, __R__ is parameterized by its [Cholesky decomposition](https://en.wikipedia.org/wiki/Cholesky_decomposition) as the cross-product of upper triangular matrices. In cases where different parts of the evolutionary time series are described by $R_{m}$ matrices, __V__ is computed as the sum of the different $R_{m}$ and $C_{m}$ matrices:


$$
V = \sum_{i=1}^{m} R_{m} \otimes C_{m}
$$


The current implementation of the multivariate unbiased random walk model allows testing six variants of the model. All variants of the model can be fitted using different specifications of `R` and `r` arguments in the `fit.multivariate.URW` function.

There are two options for the structure of the __R__ matrix. Setting `R = "diagonal"` means only the diagonal elements of the __R__ matrix will be estimated while off-diagonal elements are set to 0 (see panel a) below). This parameterization of the __R__ matrix means the changes in the traits are assumed to be uncorrelated. Setting `R = "symmetric"` means all (both diagonal and off-diagonal) elements in the __R__ matrix are estimated (see panel b) below). This parameterization estimates how changes in the traits are correlated.
