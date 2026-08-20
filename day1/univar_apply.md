---
layout: default
title: Univariate models application
permalink: /day1/univar_apply/
---


# Applying the univariate models in paleoTS/evoTS

The different univariate models can be applied to datasets in paleoTS/evoTS using the `opt.joint...` functions. Here's an overview:

- Stasis: `paleoTS::opt.joint.Stasis`
- Strict stasis: `paleoTS::opt.joint.StrictStasis`
- Unbiased random walk: `paleoTS::opt.joint.URW`
- General random walk: `paleoTS::opt.joint.GRW`
- Decelerated evolution: `evoTS::opt.joint.decel`
- Accelerated evolution: `evoTS::opt.joint.accel`
- OU model with a fixed optimum: `paleoTS::opt.joint.OU`
- OU model with a moving optimum: `evoTS::opt.joint.OUBM`
- OU model with a moving optimum and with the ancestral state at optimum: `evoTS::opt.joint.OUBM` with the argument `opt.anc = TRUE`.


Below, we will look at outputs from fitting the decelerated evolution, accelerated evolution and OU models to the example dataset in evoTS. We will also show you how to compare the fit of different models, including how to use evoTS to assess the uncertainty of the estimated parameters by exploring the log-likelihood surface. In the last part, we will go through how to fit all univariate models at once and how to fit combinations of univariate models to a time series (mode-shift models).

We will use the time series with the diameter of _Stephanodiscus yellowstonensis_. Get the data into the right paleoTS format like this:

```r
ln_diameter <- paleoTS::ln.paleoTS(diameter_S.yellowstonensis)
```

We then convert the time vector in the dataset to unit length (i.e., the length in time from the oldest to youngest sample/population in the data set becomes 1):

```r
ln_diameter$tt <- ln_diameter$tt/(max(ln_diameter$tt))
```

We will start with the decelerated-evolution model.


## Decelerated-evolution model


Fit the decelerated model of evolution to the time series by running:

```r
opt.joint.decel(ln_diameter)
```

You will get an output like this:

```r
>
paleoTSfit object [n = 63 , K = 3 ]

Model:  Decel 
Method:  Joint 
log-likelihood =  78.67217 
AICc =  -150.9376 

Parameter estimates: 
       anc      vstep          r 
 3.7195270  0.4309393 -1.3116638 

Additional elements not printed:  convergence logLFunction 
```

The output returns the log-likelihood of the model parameters (_log-likelihood_), the AICc score (_AICc_), the number (_K_) of estimated parameters, the length of the analysed time series (_n_), the model name (_Model_) and the method used to parameterise the model (_Method_). Under _Parameter estimates_, _anc_ is the estimated ancestral trait value, _vstep_ is the initial value for the step distribution, and _r_ describes the exponential decay in the $v_{step}$ parameter through time.

The time it takes to half the net rate of evolution can be calculated based on the value of $r$ using $\frac{−𝑙𝑛(2)}{𝑟}$. The half-life parameter is interpreted based on the time-scale used when analysing the data. Since time from start to end in our data has been scaled to unit length, the estimated half-life represents the percent of the total length of the time series it takes for the rate of evolution to half. The half-life is $\frac{-ln(2)}{-1.3116638} = 0.53$ in this example. The total length of the analysed time series is 13 728 years, which means it takes $13728 \times 0.53 = 7276$ years for the net rate of evolution to be reduced by 50%.

What are the uncertainty of the estimated parameters in this model? Standard errors of the parameters are returned by setting `hess = TRUE` when fitting a model. The standard errors are calculated based on the [Hessian matrix](https://en.wikipedia.org/wiki/Hessian_matrix), which is a square matrix of partial second order derivatives.

Another way to assess the uncertainty of the estimated parameters is to explore the log-likelihood surface of the fitted model.

Investigating the log-likelihood surface can be helpful for several reasons.

1. Computing the log-likelihood surface is a great way to explore which parameter combinations that have an almost identical log-likelihood compared to the maximum log-likelihood values. Investigating the log-likelihood surface is also an approach to assess uncertainty in the estimated parameters. A large range of parameter values that have almost the same log-likelihood is an indication that we should be careful putting too much emphasis on only the maximum log-likelihood (best) estimates of the parameters. The functions in evoTS calculating log-likelihood surfaces report the upper and lower parameter estimates that are within two support units of the best estimate as a way to assess uncertainty in parameters ([Edwards 1992](https://www.press.jhu.edu/books/title/1877/likelihood)). While standard errors computed from the [Hessian matrix](https://en.wikipedia.org/wiki/Hessian_matrix) are always symmetric around the point estimate, the log-likelihood surface might not be (multivariate) normal. The reported upper and lower parameter estimates are therefore often not symmetrical around the maximum log-likelihood parameter estimates.
2. Estimating parameters in a model using maximum log-likelihood always run the risk of returning parameters from a local and not a global optimum in the log-likelihood landscape. Investigating the support surface for combinations of parameters is one way to explore the topology of the log-likelihood surface.
3. Ridges in the log-likelihood surface can make it challenging to identify maximum log-likelihood estimates of the model parameters in certain cases. Flat ridges may for example cause identifiability issues and problems for the model to converge. Investigating the log-likelihood surface can therefore help diagnose challenges related to failures of model convergence.

The evoTS package contains functions to create log-likelihood surfaces for univariate models in evoTS and paleoTS (e.g., `loglik.surface.stasis`, `loglik.surface.URW`, `loglik.surface.GRW`, `loglik.surface.OU`). These functions need a paleoTS object and vectors containing candidate values for the parameters to be evaluated. Which candidate values to investigate is trial-and-error, but the maximum log-likelihood estimate of the parameter should always be in the interval.

For the decelerated model of evolution, the vectors given to the arguments `vstep.vec` and `r.vec` define the pairwise combinations of parameters for which the function will estimate the log-likelihood. The resolution of the input vectors therefore determines how accurate the visual representation of the support surface is, including the returned upper and lower estimates printed in the console. A higher resolution gives better precision, but demands more computation time. Note that the computed support surface is conditional on the best estimates of the other model parameters that are not part of the support surface (e.g., the estimated ancestral trait value is assumed to be 3.7195270 in the example below).

One way to define the candidate values is to use the `seq` function:

```r
loglik.surface.decel(ln_diameter, vstep.vec = seq(0,1.3,0.01), r.vec = seq(-5,0,0.01))
```
```r
>
      lower upper
vstep  0.18  1.26
r     -3.23 -0.21
```

![Decel_logl_surface](/assets/images/loglik_decel.png)

From the log-likelihood surface and from the printed confidence intervals, we see that $r$ values between -3.23 and -0.21 are within 2 log-likelihood units from the best estimate for this parameter. This suggests we should be careful to exclude the possibility that the half-life of the decay in the rate of evolution is as much as 330% (45 312 years) or as low as 21% (2 946 years) of the investigated time interval.


## Accelerated-evolution model

The accelerated evolution model is identical to the decelerated model except that the $r$ parameter is constrained to be 0 or larger, which means the rate of evolution is accelerating with time.

Fit the accelerated evolution model like this:

```r
opt.joint.accel(ln_diameter)
```

And you will get an output like this:

```r
> 
paleoTSfit object [n = 63 , K = 3 ]

Model:  Accel 
Method:  Joint 
log-likelihood =  77.57017 
AICc =  -148.7336 

Parameter estimates: 
      anc     vstep         r 
3.7104897 0.2387415 0.0000010 

Additional elements not printed:  convergence logLFunction 
```

The accelerated evolution model has a lower (worse) log-likelihood and higher (worse) AICc score compared to the decelerated model of evolution.

A support surface can be produced using the `loglik.surface.accel` function:

```r
loglik.surface.accel(ln_diameter, vstep = seq(0,5,0.01), r.vec = seq(0,1.5, 0.005))
```
```r
>
      lower upper
vstep 0.090  5.00
r     0.035  1.35
```

![Accel_logl_surface](/assets/images/loglik_accel.png)

The 3D plot can be rotated vertically and horizontally to get a better overview of the log-likelihood surface, which is why the observation angle is different for this 3D plot compared to the 3D plot for the decelerated model.


## Ornstein-Uhlenbeck models

To assess the fit of an OU model with a moving optimum, you use this function:

```r
opt.joint.OUBM(ln_diameter)
```

Which will give you an output like this:

```r
>
paleoTSfit object [n = 63 , K = 4 ]

Model:  OU model with moving optimum (ancestral state at optimum) 
Method:  Joint 
log-likelihood =  78.5667 
AICc =  -148.4437 

Parameter estimates: 
anc/theta.0 vstep.trait       alpha   vstep.opt 
 3.71050957  0.25577371  4.45009756  0.00000001 
```

The _vstep.opt_ parameter describes the rate of change in the optimum. This is extremely small (virtually zero) in the example above, which means the optimum is essentially fixed. The $\alpha$ in the OU model represents the strength of the pull towards the optimum ([Hansen 1997](https://onlinelibrary.wiley.com/doi/abs/10.1111/j.1558-5646.1997.tb01457.x)). A parameter that is easier to interpret compared to $\alpha$ is the half-life, $\frac{ln(2)}{\alpha}$, which is the time it takes for the trait to move half-way from the ancestral state to the optimum. The half-life is therefore a quantification of the speed of adaptation towards the optimal state. As for the decelerated and accelerated models of evolution, the interpretation of the half-life depends on the time interval covered by the time series. Since the time interval of the time series we analyse is scaled to unit length (i.e., the time from the start to the end of the time series is 1), this means the half-life can be interpreted as the percent of the total length of the time series. The half-life in our example is $\frac{ln(2)}{4.45009756} = 0.16$. According to this point estimate, it takes the trait 16% of the total length of the time series to evolve half-way towards the optimum, which is about $13728 \text{ years} \times 0.16 = 2197 \text{ years}$.

Note that the name of the first reported parameter is _anc/theta.0_. This parameter represents the ancestral trait value, but also the value of the “ancestral” optimum. The default option in the `opt.joint.OUBM` function is to assume that the trait was perfectly adapted at the start of the time series (the argument `anc.opt = TRUE`), but this can be changed by setting `anc.opt = FALSE`, like this:

```r
opt.joint.OUBM(ln.diameter, opt.anc  = FALSE)
```

Which gives an output like this:

```r
>
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
paleoTS::opt.joint.OU(ln.diameter)
```

```r
> 
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
opt.joint.OUBM(ln.diameter, opt.anc = TRUE, iterations = 100)
```

```r
>
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
loglik.surface.OU(ln.diameter, stat.var.vec=seq(0,0.1,0.001), h.vec=seq(0,0.4,0.001))
```
```r
>
      lower upper
stationary variance 0.007 0.053
half-life           0.029 0.305
```

![OU_logl_surface](/assets/images/OU_logl_surface.png)

Half-life values up to 30% of the total length of the time series are within two log-likelihood units from the best estimate. This indicates that substantially slower evolution than the point estimate of a 6% half-life cannot be ruled out as a possibility.


## Fitting all univariate models in evoTS and paleoTS

A quick way to evaluate the relative fit of all univariate models in the evoTS and paleoTSpackages (excluding models with mode shifts) is to use the `fit.all.univariate` function:

```r
fit.all.univariate(ln.diameter, pool = TRUE)
```
```r
>

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
fit.mode.shift(ln.diameter, model1 = "URW", model2 = "URW", minb = 10)
```
```r
>
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
fit.mode.shift(ln.diameter, fit.all = TRUE, minb = 10)
```
```r
>
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

Evaluating relativ fit of a model to a dataset using AICc gives no guarantee that the best model among those tested represents a good statistical description of the empirical data (e.g., [Pennell et al. 2015](https://www.journals.uchicago.edu/doi/10.1086/682022)). In the [next section]({% link day1/adequacy.md %}), we will give an introduction to absolute model fit, using adequacy testing.





