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
