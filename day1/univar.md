---
layout: default
title: Univariate models
permalink: /day1/univar/
---

# Univariate models in evoTS

The evoTS package contains a range of univariate models that expand and supplement the models available in paleoTS.

The paleoTS package contains functions to fit biased (GRW) and unbiased random walks (URW), stasis (modeled as a white noise process, i.e., uncorrelated variation around a constant mean), strict stasis (no real evolutionary change) and an Ornstein-Uhlenbeck (OU) processes assuming a fixed optimum ([Hunt 2006](https://www.cambridge.org/core/journals/paleobiology/article/abs/fitting-and-comparing-models-of-phyletic-evolution-random-walks-and-beyond/1B03F54C04ED6174FDE9672E63DAD0D2); [Hunt et al. 2008](https://academic.oup.com/evolut/article-abstract/62/3/700/6853647)). The paleoTS package also contains models of a punctuated mode of evolution where punctuations (jumps in phenotype space) separate different parameterizations of the stasis model ([Hunt 2008](https://pubs.geoscienceworld.org/paleosoc/paleobiol/article-abstract/34/3/360/86481/Gradual-or-pulsed-evolution-when-should). A few other mode shift models (where the model of evolution shifts at some point during the evolutionary sequence) has also been implemented in paleoTS ([Hunt 2008](https://pubs.geoscienceworld.org/paleosoc/paleobiol/article-abstract/34/3/360/86481/Gradual-or-pulsed-evolution-when-should); [Hunt et al. 2015](https://www.pnas.org/doi/abs/10.1073/pnas.1403662111)).

The following additional univariate models have been implemented in the evoTS package:

* A decelerated-evolution model (an unbiased random walk with an exponential decrease in the rate of change over time)
* A accelerated-evolution model (an unbiased random walk with an exponential increase in the rate of change over time)
* Ornstein-Uhlenbeck processes where the optimum changes according to an unbiased random walk.


## Stasis and strict stasis

The stasis model is a white noise process composed of two parameters, the optimal phenotype ($\theta$) and the variance ($\omega$). The trait means are normally distributed with the mean equal to $\theta$ and the variance equal to $\omega$ ([Hunt 2006](https://www.cambridge.org/core/journals/paleobiology/article/abs/fitting-and-comparing-models-of-phyletic-evolution-random-walks-and-beyond/1B03F54C04ED6174FDE9672E63DAD0D2)). When the variance is zero, there are no true fluctuations around the optimum (except those explained by sampling error alone), and the model collapses to strict stasis with only one parameter ($\theta$; [Hunt et al. 2015](https://www.pnas.org/doi/abs/10.1073/pnas.1403662111)). 


## Unbiased random walk and general random walk

In an unbiased random walk, trait changes are drawn randomly from a normal distribution of evolutionary “steps” (instead of a normal distribution of trait means across time). The distribution has a mean ($\mu_{step}$) that decides the average direction of the trait changes over time and a variance ($\sigma^2_{step}$) determining the magnitude of the fluctuations around the mean. When the mean is zero, we have an unbiased random walk with no specific direction ([Hunt 2006](https://www.cambridge.org/core/journals/paleobiology/article/abs/fitting-and-comparing-models-of-phyletic-evolution-random-walks-and-beyond/1B03F54C04ED6174FDE9672E63DAD0D2)). When the mean is not equal to zero, the changes have an average direction and are biased towards an increase (positive $\mu_{step}$) or decrease (negative $\mu_{step}$) of the trait mean over time. This biased version of the model is what is often referred to as a general random walk model, trend model, or directional evolution. Here, we will use the term general random walk. Both the unbiased and general random walk are originally composed of the two parameters $\mu_{step}$ and $\sigma^2_{step}$, with a third parameter, the ancestral state ($z_0$), which is part of the calculation of the expected mean trait at time $t_i$.


## Decelerated and accelerated evolution

In the models described above, the evolutionary rate is constant. To allow for change in the rate through time, it is possible to extend the unbiased random walk model to include a decline or increase in the rate through time ([Voje 2020](https://academic.oup.com/evolut/article-abstract/74/1/188/6726926), [2023](https://www.cambridge.org/core/journals/paleobiology/article/fitting-and-evaluating-univariate-and-multivariate-models-of-withinlineage-evolution/8F16773B4F432B702D030675ABE5BAFD)). The additional parameter, $r$, describes the exponential change in rate. If $r$ is larger than zero, the rate will accelerate through time. If $r$ is less than zero, the rate will decelerate. We refer to these models as accelerated and decelerated evolution ([Voje 2023](https://www.cambridge.org/core/journals/paleobiology/article/fitting-and-evaluating-univariate-and-multivariate-models-of-withinlineage-evolution/8F16773B4F432B702D030675ABE5BAFD)). These models are closely related to the early and late burst models developed for phylogenetic comparative data (e.g., [Harmon et al. 2010](https://onlinelibrary.wiley.com/doi/abs/10.1111/j.1558-5646.2010.01025.x); [Cooper and Purvis 2010](https://www.journals.uchicago.edu/doi/abs/10.1086/652466); [Blomberg et al. 2003](https://academic.oup.com/evolut/article/57/4/717/6756141)), but describes a reduced or increased rate of evolution with time within a lineage and not within a clade. As for the random walk model ([Hunt 2006](https://www.cambridge.org/core/journals/paleobiology/article/abs/fitting-and-comparing-models-of-phyletic-evolution-random-walks-and-beyond/1B03F54C04ED6174FDE9672E63DAD0D2)), the expected evolutionary divergence between ancestor and descendant populations is always zero in the model of decelerated  and accelerated evolution. The expected trait mean and its variance and covariance are given by the following expressions:

$$
E[z_i] = z_0
$$

$$
Var[z_i] = \sigma^2_{step_0}\left[\frac{e^{rt_i}-1}{r}\right] + \epsilon_i
$$

$$
Cov[z_i, z_j] = \sigma^2_{step_0}\left[\frac{e^{rt_{min}} - 1}{r}\right]
$$

where 
- $z_i$ is the expected trait value for population i in the time series
- $z_i$ is the ancestral trait mean
- $\sigma^2_{step_0}$ is the step distribution
- $r$ describes the exponential decay or increase in the net rate of change through time and is constrained to be $0$ or smaller for the decelerated evolution model, and $0$ or larger for the accelerated evolution model
- $t_i$ is the time interval from the ancestral population mean (the start of the fossil sequence, which has a time of 0) to the $i\text{th}$ population
- $t_{min}$ is the time interval from the ancestral population to the oldest of the two populations $z_i$ and $z_j$.

The decelerated model of evolution can be fitted to a time series using the `opt.joint.decel` function, and the accelerated model by using the `opt.joint.accel` function.

## Ornstein-Uhlenbeck processes

The last three single-mode models are different versions of an Ornstein-Uhlenbeck (OU) process. The OU process consists of a stochastic and a deterministic part. The stochastic part is similar to the unbiased random walk, while the deterministic part allows the trait to evolve towards an optimal trait value ($\theta$). The strength of attraction towards the optimum is determined by the $\alpha$ parameter ([Hansen 1997](https://onlinelibrary.wiley.com/doi/abs/10.1111/j.1558-5646.1997.tb01457.x); Hunt et al. 2008). In the simplest OU model, the optimum is fixed ([Hunt et al. 2008](https://academic.oup.com/evolut/article-abstract/62/3/700/6853647)). The OU model with a moving optimum is defined when allowing for the optimum to move according to an unbiased random walk with the variance $\sigma^2_{\theta}$ [Hansen et al. (2008)](https://academic.oup.com/evolut/article-abstract/62/8/1965/6853095); [Voje 2023](https://www.cambridge.org/core/journals/paleobiology/article/fitting-and-evaluating-univariate-and-multivariate-models-of-withinlineage-evolution/8F16773B4F432B702D030675ABE5BAFD)). A special case of this latter model is when the ancestral trait state has the same value as the ancestral optimum ([Voje 2023](https://www.cambridge.org/core/journals/paleobiology/article/fitting-and-evaluating-univariate-and-multivariate-models-of-withinlineage-evolution/8F16773B4F432B702D030675ABE5BAFD)). 


An Ornstein-Uhlenbeck model adjusted to describe phenotypic evolution within a single lineage has the expected trait mean and its variance and covariance are given by the following expressions:

$$
E[z_i] = e^{(-\alpha t_i)}z_0 + (1-e^{-\alpha t_i})\theta
$$

$$
Var[z_i] = \left[\frac{\sigma^2_{step} + \sigma^2_{\theta}}{2\alpha}\right] (1-e^{(-2\alpha t_i)}) + \sigma^2_{\theta} t_i \left[\frac{1-2(1-e^{-\alpha t_i})}{\alpha t_i} \right] + \epsilon_i
$$

$$
Cov[z_i, z_j] = \left[\frac{\sigma^2_{step} + \sigma^2_{\theta}}{2\alpha}\right] (1-e^{(-2\alpha t_a)}) e^{-\alpha t_{ij}} + \sigma^2_{\theta} t_a \left[\frac{1-(1+e^{-\alpha t_{ij}})(1-e^{-\alpha t_a})}{\alpha t_i} \right]
$$

where 
- $z_i$ is the expected trait value for the $i\text{th}$ sample
- $z_0$ is the ancestral trait mean
- $t_i$ is the time interval from the ancestral population mean (the start of the time series, which has a time of 0) to the $i\text{th}$ sample
- $\theta$ is the optimum
- $\alpha$ measures the rate of adaptation to the optimum
- $\sigma^2_{step}$ is the variance of the stochastic perturbations of $z$
- $\sigma^2_{\theta}$ is the variance of the stochastic perturbations of the optimum
- $t_a$ is the time interval from the ancestral population to the oldest of the two populations $z_i$ and $z_j$
- $t_{ij}$ is the time separating two samples $z_i$ and $z_j$
- The estimation (sampling) error $\epsilon_i$ of the population means contribute to the expected variance between two population means

The OU model with a moving optimum can be fitted using the `opt.joint.OUBM function`.

```r
> opt.joint.OUBM(ln.diameter)
$logL
[1] 78.5667

$AICc
[1] -148.4437

$parameters
anc/theta.0 vstep.trait       alpha   vstep.opt 
 3.71050957  0.25577371  4.45009756  0.00000001 

$modelName
[1] "OU model with moving optimum (ancestral state at optimum)"

$method
[1] "Joint"

$K
[1] 4

$n
[1] 63

$iter
[1] NA

$se
NULL

attr(,"class")
[1] "paleoTSfit"
```

The _vstep.opt_ parameter describes the rate of change in the optimum. This is extremely small (virtually zero) in the example above, which means the optimum is essentially fixed. The alpha in the OU model represents the strength of the pull towards the optimum ([Hansen 1997](https://onlinelibrary.wiley.com/doi/abs/10.1111/j.1558-5646.1997.tb01457.x)). A parameter that is easier to interpret compared to the alpha is the half-life, $\frac{ln(2)}{\alpha}$, which is the time it takes for the trait to move half-way from the ancestral state to the optimum. The half life is therefore a quantification of the speed of adaptation towards the optimal state. As for the decelerated and accelerated models of evolution, the interpretation of the half-life depends on the time interval covered by the time series. Since the time interval of the time series we analyse is scaled to unit length (i.e., the time from the start to the end of the time series is 1), this means the half-life can be interpreted as the percent of the total length of the time series. The half-life in our example is $\frac{ln(2)}{\alpha}=0.16$. According to this point estimate, it takes the trait 16% of the total length of the time series to evolve half-way towards the optimum, which is about $13728 years \times 0.16 = 2197 years$.

Note that the name of the first reported parameter is _anc/theta.0_. This parameter represents the ancestral trait value, but also the value of the “ancestral” optimum. The default option in the `opt.joint.OUBM` function is to assume that the trait was perfectly adapted at the start of the time series (the argument `anc.opt = TRUE`), but this can be changed by setting `anc.opt = FALSE`, like this:

```r
> opt.joint.OUBM(ln.diameter, opt.anc  = FALSE)
$logL
[1] 80.71298

$AICc
[1] -150.3733

$parameters
        anc vstep.trait     theta.0       alpha   vstep.opt 
 3.70316688  0.27295686  3.89044533 11.89309009  0.00000001 

$modelName
[1] "OU model with moving optimum"

$method
[1] "Joint"

$K
[1] 5

$n
[1] 63

$iter
[1] NA

$se
NULL

attr(,"class")
[1] "paleoTSfit"
```

Setting `opt.anc = FALSE` estimates a separate “ancestral” value for the optimum (_theta.0_). The rate of change in the optimum (_vstep.opt_) is still negligible, which means this model is virtually identical to a model where the optimum is fixed. This can be shown by fitting an OU model where the optimum is fixed, which is the model included in the paleoTS package:

```r
> paleoTS::opt.joint.OU(ln.diameter)
$logL
[1] 80.71298

$AICc
[1] -152.7363

$parameters
       anc      vstep      theta      alpha 
 3.7031735  0.2729604  3.8904516 11.8941703 

$modelName
[1] "OU"

$method
[1] "Joint"

$se
NULL

$K
[1] 4

$n
[1] 63

attr(,"class")
[1] "paleoTSfit"
```

The fixed optimum model gives the same log-likelihood value as the model where the optimum was allowed to change (but actually did not). The fixed optimum model has a better AICc score as this model contains one less parameter (the parameter describing the rate of change in the optimum).

It is good practice to repeat any numerical optimization procedure from different starting points. This is especially important when the model has several parameters, as parameter-rich models may contain more than one peak in the log-likelihood surface. The OUBM model is a type of model that may have several local peaks in the likelihood space.

The user can choose the number of iterations of the numerical optimization of the OUBM model using the argument `iterations`. The function will return the parameter values from the run with the highest log-likelihood. The starting values in each iteration are drawn from a normal distribution with mean zero and a standard deviation set by the user (default is 1). The initial values for the _vstep_ and _alpha_ parameters are constrained to be equal or larger than 0.

Here, we run the `opt.joint.OUBM` function (assuming the trait value is perfectly adapted at the start of the sequence) from 100 different starting points (i.e., 100 different initial parameter values):

```r
> opt.joint.OUBM(ln.diameter, opt.anc = TRUE, iterations = 100)
$logL
[1] 78.5667

$AICc
[1] -148.4437

$parameters
anc/theta.0 vstep.trait       alpha   vstep.opt 
 3.71050949  0.25577331  4.45009314  0.00000001 

$modelName
[1] "OU model with moving optimum (ancestral state at optimum)"

$method
[1] "Joint"

$K
[1] 4

$n
[1] 63

$iter
[1] 100

$se
NULL

attr(,"class")
[1] "paleoTSfit"
```

From the output, we see that the likelihood score of the best model among the 100 model runs is identical to the score when we ran the model without any iterations. However, the maximum likelihood parameter estimates are slightly different (e.g., a difference in the sixth decimal for the _vstep_ parameter), but not to an extent that changes our interpretation of the trait dynamics.

The evoTS package contains functions to estimate likelihood surfaces for the different versions of the OU models (`loglik.surface.OU` and `loglik.surface.OUBM`). In these functions, the likelihood surface is not estimated as a function of the step variance and alpha parameter directly, but rather as a function of two related parameters that are easier to give a biological interpretation. The stationary variance, $\frac{v_{step}}{2\alpha}$, represents the equilibrium variance of the OU process ([Hansen et al. 2008](https://academic.oup.com/evolut/article-abstract/62/8/1965/6853095)) and describes the variance expected in the trait after the trait has reached the optimum. The half-life, $\frac{log(2)}{\alpha}$, is the amount of time it takes for the trait to move half-way from the ancestral state to the optimum. The half-life is informative regarding the speed of adaptation toward the optimal state. To get an idea for which candidate values to investigate for the likelihood-surface, we first need to calculate the maximum likelihood values of the stationary variance and half-life parameters from the model output.

The OU model with a fixed optimum had the best relative model fit according to AICc among the three versions of the OU model we investigated. The maximum likelihood estimate of the half-life from this OU model is $\frac{log(2)}{11.8941} = 0.0583$. The maximum likelihood estimate of the stationary variance is $\frac{0.2730}{2 \times 11.8941} = 0.0115$. But these are only point-estimates. We can explore the support interval around these point estimates of the half-life and the stationary variance using the `loglik.surface.OU` function:

```r
> loglik.surface.OU(ln.diameter, stat.var.vec=seq(0,0.1,0.001), h.vec=seq(0,0.4,0.001))
      lower upper
stationary variance 0.007 0.053
half-life           0.029 0.305
```

![OU_logl_surface](/assets/images/OU_logl_surface.png)

Half-life values up to 30% of the total length of the time series are within two log-likelihood units from the best estimate. This indicates that substantially slower evolution than the point estimate of a 6% half-life cannot be ruled out as a possibility.


## Fitting all univariate models in evoTS and paleoTS

A quick way to evaluate the relative fit of all univariate models in the evoTS and paleoTSpackages (excluding models with mode shifts) is to use the `fit.all.univariate` function:

```r
> fit.all.univariate(ln.diameter, pool = TRUE)

Comparing 9 models [n = 63, method = Joint]

                                                                logL K       AICc       dAICc Akaike.wt
GRW                                                         77.64073 3 -148.87469    3.861618     0.058
URW                                                         77.57018 2 -150.94035    1.795953     0.162
Stasis                                                      39.84019 2  -75.48039   77.255917     0.000
StrictStasis                                              -707.46411 1 1416.99379 1569.730094     0.000
Decel                                                       78.67217 3 -150.93756    1.798746     0.162
Accel                                                       77.57017 3 -148.73357    4.002737     0.054
OU                                                          80.71298 4 -152.73631    0.000000     0.397
OU model with moving optimum (ancestral state at optimum)   78.56670 4 -148.44374    4.292567     0.046
OU model with moving optimum                                80.71298 5 -150.37333    2.362978     0.122
```


## Fitting combinations of univariate models to a time series (mode shift)

There is no a priori reason why a lineage should follow only one mode of evolution. The evoTS package allows for investigating all pairwise model combinations of the models stasis, unbiased random walk (URW), trend (GRW) and an Ornstein-Uhlenbeck (OU) process with a fixed optimum using the function `fit.mode.shift`. It is possible to either investigate specific shift points using the argument `shift.point` or investigate all possible shift points, like below:

```r
> fit.mode.shift(ln.diameter, model1 = "URW", model2 = "URW", minb = 10)
[1] "Searching all possible shift points in the evolutionary sequence"
Total # hypotheses:  44 
1  2  3  4  5  6  7  8  9  10  11  12  13  14  15  16  17  18  19  20  21  22  23  24  25  26  27  28  29  30  31  32  33  34  35  36  37  38  39  40  41  42  43  44  
$logL
[1] 79.27473

$AICc
[1] -149.8598

$parameters
       anc      vstep      vstep     shift1 
 3.7304865  0.2432606  0.2494830 52.0000000 

$modelName
[1] "URW-URW"

$method
[1] "Joint"

$se
NULL

$K
[1] 4

$n
[1] 63

$all.logl
 [1] 70.86731 68.59747 62.65156 65.41455 53.23718 60.25449 56.19608 45.50594 42.55632 44.70092 46.05836 46.58797 48.14004 44.19647 67.63474 57.82010 64.69928 69.73926
[19] 58.45089 55.26295 53.40897 64.95625 66.97684 66.65108 72.98220 65.42928 71.12637 76.39216 74.96141 74.07654 74.06923 77.98640 75.27489 73.96364 78.18181 74.92223
[37] 75.10229 77.07127 77.28518 79.12256 75.99980 79.27473 76.45725 70.37393

$GG
shift1 
    52 

attr(,"class")
[1] "paleoTSfit"
```

The function `fit.mode.shift` can also be used to fit all pairwise combinations of the four models by setting the `fit.all` argument as `TRUE`. If a shift point is not defined (using the `shift.point` argument), all possible shift points are investigated for all models:

```r
> fit.mode.shift(ln.diameter, fit.all = TRUE, minb = 10)
[1] "Searching all possible shift points in the evolutionary sequence"
1  2  3  4  5  6  7  8  9  10  11  12  13  14  15  16  17  18  19  20  21  22  23  24  25  26  27  28  29  30  31  32  33  34  35  36  37  38  39  40  41  42  43  44  
Comparing 16 models [n = 63, method = Joint]

                  logL K       AICc      dAICc Akaike.wt
Stasis-Stasis 53.24711 4  -97.80456 64.3140156     0.000
Stasis-URW    72.84306 4 -136.99646 25.1221145     0.000
Stasis-GRW    72.84414 5 -134.63564 27.4829349     0.000
Stasis-OU     74.97027 6 -136.44053 25.6780478     0.000
URW-URW       79.27473 4 -149.85981 12.2587671     0.001
URW-GRW       79.54430 5 -148.03597 14.0826065     0.000
URW-OU        85.34081 6 -157.18163  4.9369506     0.026
GRW-GRW       84.03615 6 -154.57229  7.5462836     0.007
GRW-OU        88.87002 7 -161.70368  0.4148986     0.254
OU-OU         90.26555 8 -161.86443  0.2541469     0.275
OU-GRW        84.14765 7 -152.25894  9.8596410     0.002
OU-URW        83.90670 6 -154.31340  7.8051743     0.006
OU-Stasis     87.80929 6 -162.11858  0.0000000     0.312
GRW-URW       83.79338 5 -156.53412  5.5844543     0.019
GRW-Stasis    86.49922 6 -159.49844  2.6201411     0.084
URW-Stasis    83.36672 5 -155.68080  6.4377793     0.012
[[1]]
$logL
[1] 87.80929

$AICc
[1] -162.1186

$parameters
         anc        vstep     theta_OU        alpha        omega       shift1 
 3.705695031  0.327387632  3.817864883  5.226471406  0.001870449 38.000000000 

$modelName
[1] "OU-Stasis"

$method
[1] "Joint"

$se
NULL

$K
[1] 6

$n
[1] 63

$GG
shift1 
    38 

attr(,"class")
[1] "paleoTSfit"
```

The function returns a list of the highest log-likelihood found for each investigated model. A detailed output from the model with the lowest AICc value among the 16 candidate models is also given. An OU-Stasis model with a shift point at sample (population) 38 has the best relative fit according to AICc. Note, however, that the model-combination GRW-OU has an almost equal AICc score relative to the best model. Also the combination of two OU models (each with their own fixed optimum) shows a good relative fit to the data.


## Simulating data

It is possible to simulate data for all implemented models in evoTS. Standard R-package documentation can be seen by entering `?sim.OUBM` and `?sim.accel.decel`.


