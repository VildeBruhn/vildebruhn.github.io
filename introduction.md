---
layout: default
title: Overview
permalink: /introduction/
---

# evoTS - Analyses of evolutionary time-series

---

The evoTS package facilitates univariate and multivariate analyses of phenotypic change within lineages.

The evoTS package extends the univariate modeling framework implemented in the [paleoTS](https://github.com/cran/paleoTS) package (Hunt 2006; 2008a; 2008b; Hunt et al. 2008; 2010; 2015) and has been developed to mirror the user experience from paleoTS as much as possible. For example, all univariate models implemented in evoTS are fitted to a paleoTS object (i.e., the data format used in paleoTS). The fit of all univariate models available in paleoTS and evoTS are directly comparable.

evoTS contains a range of multivariate models, including different versions of multivariate unbiased random walks and Ornstein-Uhlenbeck processes. Together, these models allow the user to test various hypotheses of trait evolution. That is, whether traits change in a correlated or uncorrelated manner, whether one trait/variable affects the optimum of a second trait (Granger causality), whether adaptation in different traits happen independently toward fixed optima, etc.

evoTS also contains functions for calculating the topology of the likelihood surfaces of fitted models, a useful feature to investigate the range of parameter values with approximately equal likelihood as the best parameter estimates.

In this workshop we will focues on multivariate analysis of several traits and abiotic factors. If you want to learn more about the univariate analysis in evoTS, a walk-through is available at the [evoTS webpage](https://klvoje.github.io/evoTS/articles/evoTS_vignette.html#univariate-models-in-evots).

Let's get the software [installed](/installation/).




