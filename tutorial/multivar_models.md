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

![R-matrices](/assets/images/R_matrices.png)

The argument `r` in the `fit.multivariate.URW` function defines whether the rate of change is assumed constant (“fixed”), asymptotically decreasing (“decel”), or asymptotically increasing (“accel”) with time. Defining `r` as “fixed” means a regular multivariate unbiased random walk is fitted to the data. The “decel” and “accel” options fit multivariate versions of the decelerated and accelerated versions of the unbiased random walk, respectively. These latter two models deviate from the multivariate unbiased random walk in that the distance matrix __C__ is transformed by an exponential decay or acceleration parameter, $r$, that is jointly estimated during the maximum likelihood search for the __R__ matrix. A common $r$ parameter is assumed for all the traits.

To use the multivariate models, we first need multivariate data (i.e., at least two traits or variables).

The data set on phenotypic evolution in __Stephanodiscus yellowstonensis__ ([Theriot et al. 2006](https://www.cambridge.org/core/journals/paleobiology/article/abs/late-quaternary-rapid-morphological-evolution-of-an-endemic-diatom-in-yellowstone-lake-wyoming/B28737E2C2B8FBEC5D0577B5757401A5)) contains data on the number of ribs in addition to the size of the diameter we have analysed so far. Before combining the rib and diameter data into a multivariate data set, we first do an approximate log-transformation of the rib data:

```r
ln_ribs <- paleoTS::ln.paleoTS(ribs_S.yellowstonensis)
```

and convert the time vector to unit length:

```r
ln_ribs$tt <- ln_ribs$tt/(max(ln_ribs$tt))
```

We combine the two paleoTS objects into a multivariate evoTS object using the `make.multivar.evoTS` function to make a multivariate data set ready to be analyzed in evoTS:

```r
diam_ln_ribs_ln <- make.multivar.evoTS(ln_diameter, ln_ribs)
```

We can also use the `plotevoTS.multivariate` function to have a look at the combined data set:

```r

plotevoTS.multivariate(diam_ln_ribs_ln, y_min = 3.4, y_max = 4.8, x.label = "Relative time", pch = c(20,20))
```

![Diameter_ribs](/assets/images/diam_and_ribs.png)

Eye-balling the data seems to suggest that the traits change in a coordinated fashion.

We first fit a multivariate unbiased random walk model where the off-diagonal elements in the __R__ matrix are zero, and the rate of evolution is assumed constant. This is equivalent as fitting two separate univariate unbiased random walk models to each of the two time series.

```r
> fit.multivariate.URW(diam.ln_ribs.ln, R = "diag", r = "fixed")
[1] "Model converged successfully."

$modelName
[1] "Multivariate Random walk (R matrix with zero off-diagonal elements)"

$logL
[1] 136.1905

$AICc
[1] -263.6914

$ancestral.values
[1] 3.71049 4.00711

$SE.anc
[1] NA

$R
          [,1]      [,2]
[1,] 0.2387433 0.0000000
[2,] 0.0000000 0.4568377

$SE.R
[1] NA

$method
[1] "Joint"

$K
[1] 4

$n
[1] 63

$iter
[1] NA

attr(,"class")
[1] "paleoTSfit"
```
