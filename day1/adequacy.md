---
layout: default
title: Adequacy
permalink: /day1/adequacy/
---

# Absolute fit

Measures of relative model fit, including AICc, do not address whether the best model among those tested actually describes the evolutionary changes in the time series in a statistically adequate way. In other words, even if for example the decelerated-evolution model is the best model according to AICc for a particular time series, it can be an imprecise statistical description of trait evolution in the data (e.g., [Pennell et al., 2015](https://www.journals.uchicago.edu/doi/10.1086/682022)). One way to assess absolute model fit is to investigate whether datasets clearly violate model assumptions. To assess this, we can apply a parametric bootstrap approach incorporated in the adePEM package v1.1.1 ([Voje, 2018](https://besjournals.onlinelibrary.wiley.com/doi/abs/10.1111/2041-210X.13083)). The approach quantifies how likely it is that the estimated parameters from a specific model produce trait dynamics similar to the observed trait evolution. 
