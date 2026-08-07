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


where $Z_{i}$ is a vector containing the expected trait values for sample $i$, $z_{0}$ is a vector containing the ancestral trait means, $\theta$ is a vector containing the optima, and $t_{i}$ is the time interval from the ancestral population mean (the start of the time series, which has a time of 0) to the $i\text{th}$ population mean.

The variance and covariance of sample/population means are given by the following expression:

$$
\begin{aligned}
\mathrm{Cov}(z_i, z_j)
&=
\Biggl[
P
\left(
\left[
\frac{1-e^{-(\lambda_k+\lambda_l)t_a}}
{\lambda_k+\lambda_l}
\right]_{1\le k,l\le m}
\odot
P^{-1}\Sigma\Sigma^{T}(P^{-1})^{T}
\right)
P^{T}
\Biggr]
e^{-A^{T}t_{ij}}
\end{aligned}
$$

where __P__ is the orthogonal matrix of eigenvectors of __A__, $\sum\sum^{T}$, is the [Cholesky decomposition](https://en.wikipedia.org/wiki/Cholesky_decomposition) of the __R__ matrix, $\lambda_i$ is the $i\text{th}$ eigenvalue of __A__, $t_a$ is the time interval from the ancestral population to the oldest of the two populations $z_i$ and $z_j$, and $t_{ij}$ is the time separating two samples $z_i$ and $z_j$, while $\odot$ represents the [Hadamard (element-wise) product](https://en.wikipedia.org/wiki/Hadamard_product_(matrices)).

Different ways to parameterize the __A__ matrix allows testing a range of evolutionary hypotheses using the multivariate Ornstein-Uhlenbeck (OU) process. While the __R__ matrix in the multivariate version of the unbiased random walk (and in the multivariate OU model) either needs to be diagonal (only elements on the diagonal while off-diagonal elements are zero) or complete (in the sense that both the diagonal and off-diagonal elements are non-zero), this is not the case for the __A__ matrix. Generally, we can divide up the hypotheses being tested using the multivariate OU into four different types:

* Independent evolution: only diagonal elements in both the __A__ and the __R__ matrix (i.e., equivalent to fitting univariate models to each trait separately).
* Independent adaptation: only diagonal elements in the __A__ matrix while the __R__ matrix is completely parameterized. In such a model, the traits are adapting independently toward their optima, but the stochastic changes in the traits are correlated.
* At least one trait affects the optimum of the other trait. The diagonal and at least one of the off-diagonal elements in the __A__ matrix is non-zero. It is the trait in the column of the non-zero off-diagonal element that affects the optimum of the trait in the row of the non-zero off-diagonal element. A negative off-diagonal element means the trait evolves toward the optimum determined by the other trait, while a positive off-diagonal element means the trait evolve away from the optimum determined by the other trait. The stochastic changes in the trait can be either correlated or non-correlated.
* The same traits affect each others optimum, but to different degrees. The same off-diagonal elements on both sides of the diagonal are parameterized in the __A__ matrix. The stochastic changes in the trait can be either correlated or non-correlated.

For a data set consisting of two traits, there are four possible parameterizations of the __A__ matrix (see panels below): 

1. Independent adaptation of both traits: panel a)
2. Trait Y affecting the optimum of trait X: panel b)
3. Trait X affecting the optimum of trait Y: panel c)
4. Trait X affecting the optimum of trait Y, and the traits X and Y affecting each others optima: panel d)

Each of these four ways of parameterizing the __A__ matrix can be combined with an __R__ matrix with elements only on the diagonal (the stochastic changes in the traits are uncorrelated) or a completely parameterized __R__ matrix (the stochastic changes in the traits are correlated).

![A-matrices](/assets/images/A_matrices.png)

An __A__ matrix with off-diagonal elements investigates [Granger causality](https://en.wikipedia.org/wiki/Granger_causality) between the two traits/variables ([Granger 1969](https://www.jstor.org/stable/1912791); [Schweder 1970](https://www.cambridge.org/core/journals/journal-of-applied-probability/article/abs/composable-markov-processes/785465016063EBC975689240EDBFE0BA). Simply speaking, we have evidence for Granger causality if observations in one time series is useful for forecasting observations in one or several other time series, which would be the case if one trait affects the optimum that another trait tracks with a lag. Multivariate OU models therefore enable us to move beyond interpreting correlations among variables. While correlation is a measure of linear dependence between two random variables, the time dimension (i.e., the lag/evolutionary inertia in the tracking of the changing optimum) of the potential relationship between two variables is important in Granger causality. And while a correlation is symmetric (the correlation between X and Y is the same as the correlation between Y and X), this is not necessarily the case for Granger causality. X can Granger-cause Y, without Y Granger-causing X.

It is also possible to implement a model where the last trait in the data set evolves as an unbiased random walk which affects the optima for all other traits in the data set. This model can be fitted by defining `A.matrix = "OUBM"`, which sets the last diagonal element in the __A__ matrix to zero. A zero diagonal element in the __A__ matrix means there is no tendency for this trait to evolve towards an optimal value (which is the case for an unbiased random walk). Setting `A.matrix = "OUBM"` will automatically define the __R__ matrix to only have diagonal elements (i.e., this option for the __A__ matrix will override how the __R__ matrix is defined by the user). The reason for this is that the stochastic changes in the variable evolving as an unbiased random walk will affect the optimum of the other traits, which means the stochastic trait dynamics of the traits evolving according to an OU model should be independent of the changes in the optimum.

Multivariate OU models can be fitted using two different functions in evoTS. The function `fit.multivariate.OU` allows the user to use pre-defined arguments for the __A__ and __R__ matrices to parameterise these matrices. The `A.matrix` can either be defined as `“diag”` (panel a) above), `“upper.tri”` (panel b) above), `“lower.tri”` (panel c) above), and `“full”` (panel d) above). The default is `“diag”`. The `R.matrix` can be defined as `“diag”` or `“symmetric”`. The function `fit.multivariate.OU` allows the user to test all (sensible) hypotheses for multivariate data that consists of two traits.

If the data consists of more than two traits, it is recommended to use the function `fit.multivariate.OU.user.defined`. This function lets the user define which elements in the __A__ and __R__ matrices that will be parameterised. This function therefore allows full flexibility for the type of hypotheses that are being tested as the parameterisation is not constrained to follow the pre-defined options for the __A__ matrix available in the function `fit.multivariate.OU`.

We will investigate whether the two traits from the _S. yellowstonensis_ lineage show evidence of independent evolution (only diagonal elements in the __A__ and __R__ matrices) or whether only the adaptation part of the trait dynamics is independent in the two traits (diagonal __A__ matrix and symmetric __R__ matrix). We will test these hypotheses using the `fit.multivariate.OU` function.

Note that the multivariate OU model demands much more computational time compared to univariate models and simple multivariate models (like the multivariate unbiased random walk). The computational time grows exponentially with the dimension of the variance–covariance matrix (e.g., [Felsenstein 1973](https://pmc.ncbi.nlm.nih.gov/articles/PMC1762641/); [Hadﬁeld and Nakagawa 2010](https://academic.oup.com/jeb/article-abstract/23/3/494/7412248); [Freckleton 2012](https://besjournals.onlinelibrary.wiley.com/doi/full/10.1111/j.2041-210X.2012.00220.x). The time it takes to fit the multivariate OU models therefore both depends on the number of traits and the length of the time series. Setting `trace = TRUE` allows the user to keep an eye on how the optimisation proceeds.

First, run the two different models:

```r
OUOU_model1 <- fit.multivariate.OU(diam_ln_ribs_ln, A.matrix = "diag", R.matrix = "diag")
OUOU_model2 <- fit.multivariate.OU(diam_ln_ribs_ln, A.matrix = "diag", R.matrix = "symmetric")
```

And look at the AICc results: 

```r
OUOU_model1$AICc;OUOU_model2$AICc
```
```r
>
[1] -267.6676
[1] -352.0637
```
The model with independent adaptation and correlated stochastic changes (`OUOU_model2`) is much better than the independent evolution model. 

Then, look at the output from the model:

```r
OUOU_model2
```
```r
>
$converge
[1] "Model converged successfully"

$logL
[1] 186.7299

$AICc
[1] -352.0637

$ancestral.values
[1] 3.718630 4.006437

$SE.anc
[1] NA

$optima
[1] 3.876449 4.282549

$SE.optima
[1] NA

$A
         [,1]     [,2]
[1,] 12.70187  0.00000
[2,]  0.00000 14.60702

$SE.A
[1] NA

$half.life
[1] 0.05457047 0.04745301

$R
           [,1]       [,2]
[1,] 0.02233866 0.07823089
[2,] 0.07823089 0.85571347

$SE.R
[1] NA

$method
[1] "Joint"

$K
[1] 9

$n
[1] 63

$iter
[1] NA

attr(,"class")
[1] "paleoTSfit"
```

And the correlation:

```r
stats::cov2cor(OUOU_model2$R)
```
```r
>
          [,1]      [,2]
[1,] 1.0000000 0.5576264
[2,] 0.5576264 1.0000000
```

The half-life for the log diameter and log number of ribs are 6.1% and 4.9% of the length of the time series, which translates into $13728 \times 0.061 = 837$ and $13728 \times 0.049 = 673$ years, respectively. The correlation of the stochastic changes is substantial, but much reduced compared to the estimate of the correlation from the multivariate unbiased random walk. This is because a substantial part of the trait dynamics in a multivariate OU model is due to the deterministic approach of the traits toward the optima. The model has an almost identical relative fit compared to the multivariate unbiased random walk according to AICc, but is out-competed by the unbiased random walk with a mode shift.

We now test if a more complex parameterization of the __A__ matrix gives us a better relative model fit according to AICc:

```r
OUOU_model3 <- fit.multivariate.OU(diam_ln_ribs_ln, A.matrix = "upper.tri", R.matrix = "symmetric")
OUOU_model4 <- fit.multivariate.OU(diam_ln_ribs_ln, A.matrix = "full", R.matrix = "symmetric")
OUOU_model5 <- fit.multivariate.OU(diam_ln_ribs_ln, A.matrix = "OUBM")
```

And look at the AICc results:

```r
OUOU_model3$AICc;OUOU_model4$AICc;OUOU_model5$AICc
```
```r
>
[1] -350.0442
[1] -380.0126
[1] -305.4217
```

The best model is a model where each trait affects the optimum of the other trait (`OUOU_model4`).

However, before we trust this result, we should make sure to run the multivariate models from different initial starting values to increase our chances of finding a potentially higher peak on the log-likelihood surface. The number of iterations can be defined by the `iterations` argument. The starting values are drawn from a normal distribution with a standard deviation of one. The user can define a larger or smaller standard deviation using the argument `iter.sd`.

All models possible to investigate using the `fit.multivariate.OU` function can also be investigated using the `fit.multivariate.OU.user.defined` function, as the latter lets the user define which elements in the __A__ and __R__ matrices that are parameterised. Which elements in __A__ and __R__ that should be parameterised or set to zero are given by 1 and 0 respectively. We can for example fit a model with a lower triangle __A__ matrix and a symmetric __R__ matrix. A lower triangle __A__ matrix means the diameter is affecting the optimum of the number of ribs.

First, make the __A__ and __R__ matrices:

```r
A <- matrix(c(1,0,1,1), nrow = 2, byrow = TRUE)
R <- matrix(c(1,1,1,1), nrow=2, byrow = TRUE)
```

Then, run the model with the user defined matrices:

```r
OUOU_model6 <- fit.multivariate.OU.user.defined(diam_ln_ribs_ln, A.user = A, R.user = R)
```

And look at the AICc result:

```r
OUOU_model6$AICc
```
```r
>
[1] -350.146
```

The `fit.multivariate.OU.user.defined` function is especially handy if we have a data set consisting of more than two traits. Let’s for example assume that we have a multivariate data set consisting of two phenotypic traits and a climatic variable, which we refer to as variables M, N and O, respectively. Assume we have an hypothesis that the climatic variable (O) is changing as a random walk and is affecting the optimum of trait N. We also hypothesise that trait N affects the optimum of trait M, but that trait M is not affecting the optimum of trait N. We assume the stochastic changes in traits M and N are correlated, while changes in O are uncorrelated with M and N. This hypothesis is parameterised by defining the __A__ and __R__ matrices the following way:

```r
A <- matrix(c(1,1,0,0,1,1,0,0,0), nrow = 3, byrow = TRUE)
A
```
```r
>
     [,1] [,2] [,3]
[1,]    1    1    0
[2,]    0    1    1
[3,]    0    0    0
```
```r
R <- matrix(c(1,1,0,1,1,0,0,0,1), nrow = 3, byrow = TRUE)
R
```
```r
> 
     [,1] [,2] [,3]
[1,]    1    1    0
[2,]    1    1    0
[3,]    0    0    1
```

Note that it is possible to define nonsensical biological hypotheses given the full flexibility of how to parameterise __A__ and __R__ using the `fit.multivariate.OU.user.defined` function. For example, an __A__ matrix with only zeros on the diagonal and non-zero off-diagonal elements would parameterise a model where the variables change according to an unbiased random walk (since the diagonal elements are zero), but where all variables are assumed to affect the optima for all variables in the data set (all off-diagonal elements are non-zero). This is a non-sense model, as a variable changing according to an unbiased random walk has no tendency (or ability) to evolve towards an optimum.


### Initial parameter values

All model fitting procedures to find maximum likelihood solutions need initial starting values for the model parameters. Most of the starting values when fitting multivariate OU models are based on maximum likelihood parameter estimates of the univariate versions of the OU model fitted to each trait separately. The starting values for the off-diagonal elements in the __A__ and __R__ matrices are set to 0 and 0.5, respectively. This way to set the initial parameter values seems to work fine for most data sets, but there is no guarantee that the provided initial parameters will always work as this depends on the nature of the data being analysed. If an error message is returned saying “function cannot be evaluated at initial parameters”, it is recommended that the user sets the iteration option to 1 (`iteration = 1`). The model fitting algorithm will then produce a set of random initial starting values and retry to start the optimisation procedure 100,000 times.

Another option is to define which initial parameter values the optimisation procedure should start from. These values can be provided by setting the `user.init...` (e.g., `user.init.off.diag.R = 1`) to something else than `NULL` (which is the default setting). Note that the length of the vector of user-specified initial values need to match the number of parameters to be estimated (e.g., `user.init.off.diag.R = 1` for a multivariate data set consisting of two traits, `user.init.off.diag.R = c(1,1,1)` if the data set contains three traits, etc.).

---

Now, let's move over to [Exercise 1]({% link exercise1.md %}).
