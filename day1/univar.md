---
layout: default
title: Univariate models
permalink: /day1/univar/
---

# Univariate models in evoTS

The evoTS package contains a range of univariate models that expand and supplement the models available in paleoTS.

The paleoTS package contains functions to fit biased (GRW) and unbiased random walks (URW), stasis (modeled as a white noise process, i.e., uncorrelated variation around a constant mean), strict stasis (no real evolutionary change) and an Ornstein-Uhlenbeck (OU) processes assuming a fixed optimum (see [Hunt 2006](https://www.cambridge.org/core/journals/paleobiology/article/abs/fitting-and-comparing-models-of-phyletic-evolution-random-walks-and-beyond/1B03F54C04ED6174FDE9672E63DAD0D2) and [Hunt et al. 2008](https://academic.oup.com/evolut/article-abstract/62/3/700/6853647) for info on these models). The paleoTS package also contains models of a punctuated mode of evolution where punctuations (jumps in phenotype space) separate different parameterizations of the stasis model ([Hunt 2008](https://pubs.geoscienceworld.org/paleosoc/paleobiol/article-abstract/34/3/360/86481/Gradual-or-pulsed-evolution-when-should). A few other mode shift models (where the model of evolution shifts at some point during the evolutionary sequence) has also been implemented in paleoTS ([Hunt 2008](https://pubs.geoscienceworld.org/paleosoc/paleobiol/article-abstract/34/3/360/86481/Gradual-or-pulsed-evolution-when-should); [Hunt et al. 2015](https://www.pnas.org/doi/abs/10.1073/pnas.1403662111)).

The following additional univariate models have been implemented in the evoTS package:

* A decelerated-evolution model (an unbiased random walk with an exponential decrease in the rate of change over time)
* A accelerated-evolution model (an unbiased random walk with an exponential increase in the rate of change over time)
* Ornstein-Uhlenbeck processes where the optimum changes according to an unbiased random walk.


## Decelerated-evolution model

The unbiased random walk model in the paleoTS package model evolutionary changes as random draws from a normal distribution with a mean of zero ([Hunt 2006](https://www.cambridge.org/core/journals/paleobiology/article/abs/fitting-and-comparing-models-of-phyletic-evolution-random-walks-and-beyond/1B03F54C04ED6174FDE9672E63DAD0D2)). Each draw from the normal distribution represents a discrete evolutionary “step” and the variance of the normal distribution is called the step variance. The decelerated model of phyletic evolution is an unbiased random walk where the step variance is reduced exponentially through time ([Voje 2020](https://academic.oup.com/evolut/article-abstract/74/1/188/6726926)). This model is closely related to the early burst model developed for phylogenetic comparative data (e.g., [Harmon et al. 2010](https://onlinelibrary.wiley.com/doi/abs/10.1111/j.1558-5646.2010.01025.x), [Cooper and Purvis 2010](https://www.journals.uchicago.edu/doi/abs/10.1086/652466)), but describes a reduced rate of evolution with time within a lineage and not within a clade. As for the random walk model ([Hunt 2006](https://www.cambridge.org/core/journals/paleobiology/article/abs/fitting-and-comparing-models-of-phyletic-evolution-random-walks-and-beyond/1B03F54C04ED6174FDE9672E63DAD0D2)), the expected evolutionary divergence between ancestor and descendant populations is always zero in the model of decelerated evolution. The expected trait mean and its variance and covariance are given by the following expressions:

$$
E[z_i] = z_0
$$

$$
Var[z_i] = \sigma^2_{step.0}\left[\frac{e^{rt_i}-1}{r}\right] + \epsilon_i
$$

$$
Cov[z_i, z_j] = \sigma^2_{step.0}\left[\frac{e^{rt_{min}} - 1}{r}\right]
$$

where $z_i$ is the expected trait value for population i in the time series, $z_i$ is the ancestral trait mean, $\sigma^2_{step.0}$ is the step distribution, $r$ describes the exponential decay in the net rate of change through time and is constrained to be $0$ or smaller, $t_i$ is the time interval from the ancestral population mean (the start of the fossil sequence, which has a time of $0$) to the $i\text{th}$ population, and $t_{min}$ is the time interval from the ancestral population to the oldest of the two populations $z_i$ and $z_j$.

The decelerated model of evolution can be fitted to a time series using the `opt.joint.decel` function, like this:

```r
> opt.joint.decel(ln.diameter)
$logL
[1] 78.67217

$AICc
[1] -150.9376

$parameters
       anc      vstep          r 
 3.7195253  0.4308633 -1.3114667 

$modelName
[1] "Decel"

$method
[1] "Joint"

$se
NULL

$K
[1] 3

$n
[1] 63

attr(,"class")
[1] "paleoTSfit"
```

The output returns the log-likelihood of the model parameters (logl), the AICc score (AICc), the number (K) of estimated parameters (parameters), the length of the analysed time series (n), the model name (modelName) and the method used to parameterise the model (method). Under parameters, _anc_ is the estimated ancestral trait value, _vstep_ is the initial value for the step distribution, and _r_ describes the exponential decay in the $v_{step}$ parameter through time.

The time it takes to half the net rate of evolution can be calculated based on the value of $r$ using $\frac{−𝑙𝑛(2)}{𝑟}$. The half-life parameter is interpreted based on the time-scale used when analysing the data. Since time from start to end in our data has been scaled to unit length, the estimated half-life represent the percent of the total length of the time series it takes for the rate of evolution to half. The half-life is $\frac{-ln(2)}{-1.3114667} = 0.53$ in this example. The total length of the analyzed time series is 13 728 years, which means it takes $13728 * 0.53 = 7276$ years for the net rate of evolution to be reduced by 50%.

What are the uncertainty of the estimated parameters in this model? Standard errors of the parameters are returned by setting `hess = TRUE` when fitting a model. The standard errors are calculated based on the [Hessian matrix](https://en.wikipedia.org/wiki/Hessian_matrix), which is a square matrix of partial second order derivatives.

Another way to assess the uncertainty of the estimated parameters is to explore the likelihood-surface of the fitted model.

Investigating the likelihood surface can be helpful for several reasons.

1. Computing the likelihood surface is a great way to explore which parameter combinations that have an almost identical likelihood compared to the maximum likelihood values. Investigating the log-likelihood surface is also an approach to assess uncertainty in the estimated parameters. A large range of parameter values that have almost the same log-likelihood is an indication that we should be careful putting too much emphasis on only the maximum-likelihood (best) estimates of the parameters. The functions in evoTS calculating log-likelihood surfaces report the upper and lower parameter estimates that are within two support units of the best estimate as a way to assess uncertainty in parameters ([Edwards 1992](https://www.press.jhu.edu/books/title/1877/likelihood)). While standard errors computed from the [Hessian matrix](https://en.wikipedia.org/wiki/Hessian_matrix) are always symmetric around the point estimate, the log-likelihood surface might not be (multivariate) normal. The reported upper and lower parameter estimates are therefore often not symmetrical around the maximum likelihood parameter estimates.
2. Estimating parameters in a model using maximum likelihood always run the risk of returning parameters from a local and not a global optimum in the likelihood landscape. Investigating the support surface for combinations of parameters is one way to explore the topology of the likelihood-surface.
3. Ridges in the log-likelihood surface can make it challenging to identify maximum likelihood estimates of the model parameters in certain cases. Flat ridges may for example cause identifiability issues and problems for the model to converge. Investigating the log-likelihood surface can therefore help diagnose challenges related to failures of model convergence.

The evoTS package contain functions to create likelihood surfaces for univariate models in evoTS and paleoTS (e.g., `loglik.surface.stasis`, `loglik.surface.URW`, `loglik.surface.GRW`, `loglik.surface.OU`). These functions need a paleoTS object and vectors containing candidate values for the parameters to be evaluated. Which candidate values to investigate is trial-and-error, but the maximum likelihood estimate of the parameter should always be in the interval.

For the decelerated model of evolution, the vectors given to the arguments `vstep.vec` and `r.vec` define the pairwise combinations of parameters for which the function will estimate the log-likelihood. The resolution of the input vectors therefore determines how accurate the visual representation of the support surface is, including the returned upper and lower estimates printed in the console. A higher resolution gives better precision, but demands more computation time. Note that the computed support surface is conditional on the best estimates of the other model parameters that are not part of the support surface (e.g., the estimated ancestral trait value is assumed to be 3.7195253 in the example below).

One way to define the candidate values is to use the `seq` function:

```r
> loglik.surface.decel(ln.diameter, vstep.vec = seq(0,1.3,0.01), r.vec = seq(-5,0,0.01))
      lower upper
vstep  0.18  1.26
r     -3.23 -0.21
```

![Decel_logl_surface](/assets/images/decel_logl_surface.png)

From the likelihood surface and from the printed confidence intervals, we see that $r$ values between -3.23 and -0.21 are within 2 log-likelihood units from the best estimate for this parameter. This suggests we should be careful to exclude the possibility that the half-life of the decay in the rate of evolution is as much as 330% (45 312 years) or as low as 21% (2 946 years) of the investigated time interval.


## Accelerated-evolution model

The accelerated evolution model is identical to the decelerated model except that the $r$ parameter is constrained to be 0 or larger, which means the rate of evolution is accelerating with time.

The accelerated evolution model can be fitted using the `opt.joint.accel` function:

```r


$method
[1] "Joint"

$se
NULL

$K
[1] 3

$n
[1] 63

attr(,"class")
[1] "paleoTSfit"
```

The accelerated evolution model has a lower (worse) log-likelihood and higher (worse) AICc score compared to the decelerated model of evolution.

A support surface can be produced using the `loglik.surface.accel` function:

```r
> loglik.surface.accel(ln.diameter, vstep = seq(0,5,0.01), r.vec = seq(0,1.5, 0.005))
      lower upper
vstep 0.090  5.00
r     0.035  1.35
```

![Accel_logl_surface](/assets/images/accel_logl_surface.png)
