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

In an unbiased random walk, trait changes are drawn randomly from a normal distribution of evolutionary “steps” (instead of a normal distribution of trait means across time). The distribution has a mean ($\mu_{step}$) that decides the average direction of the trait changes over time and a variance 
($\sigma^2_{step}$) 
determining the magnitude of the fluctuations around the mean. When the mean is zero, we have an unbiased random walk with no specific direction ([Hunt 2006](https://www.cambridge.org/core/journals/paleobiology/article/abs/fitting-and-comparing-models-of-phyletic-evolution-random-walks-and-beyond/1B03F54C04ED6174FDE9672E63DAD0D2)). 

![Unbiased random walk](/assets/images/URW.png)
<small>Figure from [Kinneberg and Voje (2026)](https://academic.oup.com/evolut/article/80/1/97/8280398)</small>

When the mean is not equal to zero, the changes have an average direction and are biased towards an increase (positive $\mu_{step}$) or decrease (negative $\mu_{step}$) of the trait mean over time. This biased version of the model is what is often referred to as a general random walk model, trend model, or directional evolution. Here, we will use the term general random walk. Both the unbiased and general random walk are originally composed of the two parameters $\mu_{step}$ and $\sigma^2_{step}$, with a third parameter, the ancestral state ($z_0$), which is part of the calculation of the expected mean trait at time $t_i$.


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

