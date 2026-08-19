---
layout: default
title: Adequacy
permalink: /day1/adequacy/
---

# Absolute fit

Measures of relative model fit, including AICc, do not address whether the best model among those tested actually describes the evolutionary changes in the time series in a statistically adequate way. In other words, even if for example the decelerated-evolution model is the best model according to AICc for a particular time series, it can be an imprecise statistical description of trait evolution in the data (e.g., [Pennell et al. 2015](https://www.journals.uchicago.edu/doi/10.1086/682022)). One way to assess absolute model fit is to investigate whether datasets clearly violate model assumptions. To assess this, we can apply a parametric bootstrap approach incorporated in the adePEM package v1.1.1 ([Voje 2018](https://besjournals.onlinelibrary.wiley.com/doi/abs/10.1111/2041-210X.13083)). The approach quantifies how likely it is that the estimated parameters from a specific model produce trait dynamics similar to the observed trait evolution.

![Adequacy](/assets/images/adequacy.png)
<small>Figure from [Voje (2018)](https://besjournals.onlinelibrary.wiley.com/doi/abs/10.1111/2041-210X.13083)</small>

The adequacy tests are intended to determine how likely it is that a model X, given parameters Y, can reproduce the trait dynamics seen in dataset Z ([Voje 2018](https://besjournals.onlinelibrary.wiley.com/doi/abs/10.1111/2041-210X.13083)). In the adequacy tests for stasis and strict stasis, four statistical characteristics of a white noise process are tested. (1) The amount of fluctuations around the fixed optimum does not increase or decrease over time; (2) deviations from the optimum are random (i.e., if there are many positive or negative deviations following each other it would indicate non-randomness); (3) the time series show low autocorrelation; and (4) net evolution is small ([Voje 2018](https://besjournals.onlinelibrary.wiley.com/doi/abs/10.1111/2041-210X.13083)). The first three statistics are also used for the unbiased random walk and the general random walk. To apply the same statistics on these models, the time series are detrended to behave like a white noise process ([Voje 2018](https://besjournals.onlinelibrary.wiley.com/doi/abs/10.1111/2041-210X.13083)). For decelerated evolution, statistic (2) and (3) are used together with a fifth statistic (5), evaluating if the magnitude of trait change decreases with time ([Voje 2020](https://academic.oup.com/evolut/article/74/1/188/6726926)). Statistics (2), (3), and (5) are also applied to accelerated evolution, but (5) is instead assessing increased, not decreased, exploration of morphospace with time. For the OU models, statistics (2) and (3) are used to investigate absolute fit. When detrended, the OU models also have the same expectation as a white noise process. Failure on one adequacy test does not predict failure on the others ([Voje 2020](https://academic.oup.com/evolut/article/74/1/188/6726926)). A model is deemed an adequate fit to a time series only if it passes all of the tests applied. For the mode-shift models, it is possible to assess absolute model fit for each of the two models separately. A a mode-shift model is adequate only if each of the two models pass all the applied adequacy tests.


## Adequacy tests with `adePEM`

The [adePEM package](http://github.com/klvoje/adePEM) developed in [Voje (2018)](https://besjournals.onlinelibrary.wiley.com/doi/abs/10.1111/2041-210X.13083) can be used to assess the absolute fit of phenotypic evolutionary models to time series. The package contains tests to assess the adequacy of all the univariate models mentioned above. 

To install and load the package in R, use the following code:

```r
install.packages("devtools")

devtools::install_github("klvoje/adePEM")

library(adePEM)
```


## How to use `adePEM`

An example data called `element.length` is available as part of the adePEM package. The data is a time series of the evolution of element length (measured in mm) in the conodont _Pterospathodus_ and was originally published by [Jones (2009)](https://www.cambridge.org/core/journals/paleobiology/article/directional-evolution-in-the-conodont-pterospathodus/19235BF6CAC0FD0F1A126F23BA9319B8). The data (element.length) is already a paleoTS object. We can first have a look at the data by plotting it:

```r
plot.paleoTS(element.length)
```

![Element length](/assets/images/element_length.png)

Time (the x-axis) is in millions of years and the trait is measured in millimeters. Error bars represent one standard error.

As an example, we then run the `fit3models` function from the paleoTS package to check the relative fit of the stasis, unbiased random walk and general random walk to the data:

```r
paleoTS::fit3models(element.length, pool=TRUE)
```

```r
>
Comparing 3 models [n = 31, method = Joint]

           logL K      AICc    dAICc Akaike.wt
GRW    25.38445 3 -43.88002 1.938802     0.262
URW    25.12370 2 -45.81882 0.000000     0.690
Stasis 22.47400 2 -40.51943 5.299396     0.049
```

The unbiased random walk (URW) model has the best fit to the data according to the AICc scores (-45.81882). However, the difference between the first and the second best AICc score (the GRW model) is small (<2 units).

Let's investigate if the URW model represents an adequate statistical description of the trait dynamics in the data. To do that, we run the function `fit3adequacy.RW` from the adePEM package. This is a wrapper function that runs 3 adequacy tests at the same time. Make sure you have the paleoTS package loaded, if not it will not run!

```r
fit3adequacy.RW(element.length)
```
```
>
$info
                   Value
replications     1000.00
confidence level    0.95

$summary
           estimate  min.sim max.sim p-value result
auto.corr    -0.318 -0.74014  0.4842   0.506 PASSED
runs.test   1.09003 -2.37144 3.55835   0.624 PASSED
slope.test  0.01199 -0.01997 0.04383   0.526 PASSED
```

The first part of the output summarizes the number of bootstrap replications (the number of simulated data sets) used for assessing model adequacy and the confidence level. 1000 replications and a confidence level of 0.95 are the default settings, but both can be defined by the user when running the `fit3adequacy.RW` function.

The second part of the output contains information on the results of the adequacy tests. The first column names the adequacy tests. The second column gives the test statistic computed on the real data. From the second column, we see that the autocorrelation is calculated to be negative and the slope test (which is the least-squares slope of how the (detrended) data changes with time) is positive.

The third and fourth columns reports the smallest and largest test statistics calculated on the simulated data sets. As we can see, all the three test statistics computed on the real data (second column) are not close to the extreme values reported in columns three and four.

The fifth column is not a real p-value, but is the fraction of simulated test statistics that is larger (or smaller) than the calculated test statistic on the observed data, divided by 0.5. A value of 1 means 50 percent of the test statistics on the simulated data are larger or smaller than the calculated statistic on the observed data, respectively. A value of 0.10 means 90 percent of the test statistics on the simulated data are larger (or smaller) than the test statistic on the observed time series.

The sixth column indicates whether our model passed the adequacy tests. Since we set our confidence level to 0.95 and all values in the fifth column is larger than 0.05, this means the random walk model passed all tests for our data set.

That the random walk model passed all tests can also be seen in the visual representation of the distributions of test statistics, where the test statistics computed for the real data is indicated with a broken (red) line. These plots are generated automatically if plot = TRUE (which is the default setting) when we run the `fit3adequacy.RW` function.

![Adequacy RW](/assets/images/adequacy_RW.png)




