---
layout: default
title: Multivariate unbiased random walk models
permalink: /tutorial/multivar_URW/
---

# Multivariate models

Traits are rarely changing independently of each other due to shared genetics or development. Evolution is accordingly a multivariate phenomenon. The evoTS package includes functions to fit the following multivariate trait models:

* Multivariate unbiased random walks

* Multivariate decelerated evolution

* Multivariate accelerated evolution

* Multivariate Ornstein-Uhlenbeck processes

We will first go through the multivariate unbiased random walk models without (unbiased random walk) and with (decelerated and accelerated evolution) changes in the rate of evolution.


## Multivariate unbiased random walks with and without changes in the rate of evolution

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

The data set on phenotypic evolution in _Stephanodiscus yellowstonensis_ ([Theriot et al. 2006](https://www.cambridge.org/core/journals/paleobiology/article/abs/late-quaternary-rapid-morphological-evolution-of-an-endemic-diatom-in-yellowstone-lake-wyoming/B28737E2C2B8FBEC5D0577B5757401A5)) contains data on the number of ribs in addition to the size of the diameter we have analysed so far. Before combining the rib and diameter data into a multivariate data set, we first do an approximate log-transformation of the rib data:

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
fit.multivariate.URW(diam_ln_ribs_ln, R = "diag", r = "fixed")
```
```r
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

The returned parameters include the ancestral trait values for the two traits and the evolutionary rate matrix __R__. The diagonal in the __R__ matrix contains the step size (rate of evolution) parameters. The second trait (ribs) has about twice the rate of evolution as the first parameter (diameter). The off-diagonal elements are zero as this model is not estimating the covariance of the evolutionary changes in the two traits.

Next, we fit a model that allows the off-diagonal elements in the R matrix to be different from zero. We do this by setting `R = "symmetric"`. We are still keeping the rate of change fixed through time.

```r
fit.multivariate.URW(diam_ln_ribs_ln, R = "symmetric", r = "fixed")
```
```r
>
$converge
[1] "Model converged successfully"

$modelName
[1] "Multivariate Random walk (R matrix with non-zero off-diagonal elements)"

$logL
[1] 182.3777

$AICc
[1] -353.7027

$ancestral.values
[1] 3.717695 4.025925

$SE.anc
[1] NA

$R
          [,1]      [,2]
[1,] 0.2680092 0.3780642
[2,] 0.3780642 0.5524616

$SE.R
[1] NA

$method
[1] "Joint"

$K
[1] 5

$n
[1] 63

$iter
[1] NA

attr(,"class")
[1] "paleoTSfit"
```

A multivariate random walk with correlated changes has a much better fit compared to the model assuming uncorrelated changes in the traits according to AICc. This indicates that the traits are not evolving independently of each other. The estimated __R__ matrix indicates that the first trait has about half the rate of evolution as the second trait and that there is substantial covariance in the evolutionary changes of the two traits. How the two traits correlate in their changes can be computed by standardizing the covariance with the product of the standard deviations on the diagonal (this can also be done using the function `cov2cor` in the `stats` package):

```r
0.3780642/(sqrt(0.2680092)*sqrt(0.5524616))
```
```r
>
[1] 0.9825161
```

or alternatively:

```r
model1 <- fit.multivariate.URW(diam_ln_ribs_ln, R = "symmetric", r = "fixed")
```
```r
>
[1] "Model converged successfully."
```
```r
stats::cov2cor(model1$R)
```
```r
>
         [,1]     [,2]
[1,] 1.000000 0.982516
[2,] 0.982516 1.000000
```

A correlation of 0.98 basically means the two traits evolve as a single trait, since at least part of the deviation from a correlation of 1 is due to measurement error.

Note that the __R__ matrix is not describing the underlying genetic or phenotypic (co)variances of the traits. The __R__ matrix is therefore not the same as a __P__ (or __G__) matrix in quantitative genetics. However, the __R__ matrix is tightly connected to these matrices. For example, if the traits evolve only due to drift, the __R__ matrix is expected to be proportional to the additive genetic variance–covariance matrix (__G__) ([Lande 1979](https://www.jstor.org/stable/2407630); [Felsenstein 1988](https://www.annualreviews.org/content/journals/10.1146/annurev.es.19.110188.002305)). Estimating __R__ can therefore aid in evolutionary interpretations of the fossil record anchored in evolutionary quantitative genetics.

Standard errors of the elements in the __R__ matrix can be approximated by the square root of the diagonal elements of the [inverse](https://en.wikipedia.org/wiki/Invertible_matrix) of the negative of the [Hessian matrix](https://en.wikipedia.org/wiki/Hessian_matrix). These standard errors are automatically estimated and reported if the argument `hess = TRUE` is defined.

Parameterising multivariate models is demanding in terms of computational time ([Felsenstein 1973](https://pmc.ncbi.nlm.nih.gov/articles/PMC1762641/); [Hadfield and Nakagawa 2010](https://academic.oup.com/jeb/article/23/3/494/7412248); [Freckleton 2012](https://besjournals.onlinelibrary.wiley.com/doi/full/10.1111/j.2041-210X.2012.00220.x)). Be aware that fitting the multivariate models to several traits with many samples (populations) will take a much longer time than fitting univariate models to each trait separately. One way to somewhat follow the progress of the model fit is to set `trace = TRUE`. This allows the user to follow the progress of the optimisation routine to minimise the likelihood function.

The likelihood surface of multivariate models can contain several local peaks. It is therefore recommended to re-run the model from different starting points (i.e., different initial parameter values). The number of iterations can be defined by the iterations argument (e.g., iterations = 10). Initial values for the search algorithm are drawn from a normal distribution with a default standard deviation of 1. The user can set this standard deviation by the `iter.sd` argument (e.g., `iter.sd = 0.5`).

We can check if we find evidence for a change in the __R__ matrix along the time series. This involves estimating a separate __R__ matrix for two non-overlapping parts of the time series. This is done using the function `fit.multivariate.URW.shift`. We can define the shift point using the argument `shift.point` (e.g., `shift.point = 20`) or we can investigate all possible shift points in the time series by not defining a shift point (the default option). The length of the smallest number of samples (populations) in each of the two segments is controlled by the `minb` argument (the default is 10).

```r
fit.multivariate.URW.shift(diam_ln_ribs_ln, hess = TRUE)
```
```r
>
Total # hypotheses:  44 
1  2  3  4  5  6  7  8  9  10  11  12  13  14  15  16  17  18  19  20  21  22  23  24  25  26  27  28  29  30  31  32  33  34  35  36  37  38  39  40  41  42  43  44  
$converge
[1] "Model converged successfully"

$modelName
[1] "Multivariate Random walk with two R matrices (with non-zero off-diagonal elements)"

$logL
[1] 190.7035

$AICc
[1] -360.0108

$ancestral.values
[1] 3.730242 4.061121

$SE.anc
[1] 0.02942139 0.04554309

$R
$R$R.1
          [,1]      [,2]
[1,] 0.3712051 0.6509228
[2,] 0.6509228 1.1705596

$R$R.2
          [,1]      [,2]
[1,] 0.2216526 0.2628519
[2,] 0.2628519 0.3164376


$SE.R
$SE.R$SE.R.1
           [,1]       [,2]
[1,] 0.01712463 0.02980279
[2,] 0.02980279 0.06689237

$SE.R$SE.R.2
            [,1]        [,2]
[1,] 0.003596406 0.004424130
[2,] 0.004424130 0.006407946


$method
[1] "Joint"

$K
[1] 9

$n
[1] 63

$iter
[1] NA

$parameters
shift1 
    18 

$all.logl
 [1] 186.7757 185.1417 186.0625 189.5071 189.7518 187.6900 187.1944 190.7035 187.8904 187.7113 187.0241 186.5424 185.8391 185.3580 187.9486 185.8228 186.7893 186.8395
[19] 187.8938 186.4932 185.8709 186.7557 186.0485 184.9683 184.9777 184.3315 183.9433 183.9788 182.8478 182.2962 181.5945 181.9293 181.2754 180.8469 181.1119 180.5572
[37] 180.0302 179.8013 179.7917 180.0224 179.8181 179.8010 179.5718 179.7813

$GG
     [,1] [,2] [,3] [,4] [,5] [,6] [,7] [,8] [,9] [,10] [,11] [,12] [,13] [,14] [,15] [,16] [,17] [,18] [,19] [,20] [,21] [,22] [,23] [,24] [,25] [,26] [,27] [,28]
[1,]   11   12   13   14   15   16   17   18   19    20    21    22    23    24    25    26    27    28    29    30    31    32    33    34    35    36    37    38
     [,29] [,30] [,31] [,32] [,33] [,34] [,35] [,36] [,37] [,38] [,39] [,40] [,41] [,42] [,43] [,44]
[1,]    39    40    41    42    43    44    45    46    47    48    49    50    51    52    53    54

attr(,"class")
[1] "paleoTSfit"
```

Two __R__ matrices are returned (_R.1_ and _R.2_), along with their standard errors (since we used `hess = TRUE`). The estimated shift point is 18 (_shift1_). The rate of evolution is much larger in the second trait compared to the first trait (the diagonal elements in _R.1_) while the difference in the rate of evolution is much smaller after the shift point (the diagonal elements in _R.2_). The evolutionary correlation is 0.98 and 0.99 before and after the shift point. The number of parameters ($K$) for this model is 9 compared to 5 for the model where we estimated a single __R__ matrix for the multivariate data set. The model with two __R__ matrices has a lower (better) AICc score compared to the model with a single __R__ matrix. This difference in AICc scores is likely to be a result of the differences in rates of evolution in _R.1_ and _R.2_, since the evolutionary correlation is estimated to be very similar in both _R.1_ and _R.2_.

We now check if the multivariate accelerated and decelerated models have a better fit than the multivariate unbiased random walk models:

```r
multi_accel <- fit.multivariate.URW(diam_ln_ribs_ln, R = "symmetric", r = "accel")
multi_decel <- fit.multivariate.URW(diam_ln_ribs_ln, R = "symmetric", r = "decel")

multi_accel$AICc;multi_decel$AICc
```
```r
> 
[1] -351.2512
[1] -356.3287
```

The multivariate decelerated evolution model has a similar (but worse) fit to the data compared to the multivariate unbiased random walk model with a single __R__ matrix, but is out-competed by the model estimating two __R__ matrices.

---

Now that we have looked at the multivariate unbiased random walk models, let's learn more about the [multivariate Ornstein-Uhlenbeck models](({% link tutorial/multivar_OU.md %}).
