---
layout: default
title: Data import
permalink: /tutorial/data_import
---

# Getting data into evoTS format

---

An object of class paleoTS is the required input for most of the functions in evoTS. To create a paleoTS object, you need vectors of trait means, sample/population variances, sample sizes and ages of the samples/populations.

One easy way to create a paleoTS object is to use the `as.paleoTS` function from the paleoTS package.

First, create a data example:

```r
trait_means <- rnorm(20)
trait_variance <- rep(0.5,20)
sample_size <- rep(30,20)
time_vector <- seq(0,19,1)
```

Then, create a paleoTS object:
```r
indata_evoTS <- paleoTS::as.paleoTS(mm = trait_means, vv = trait_variance, nn = sample_size, tt = time_vector)
```

Another way to get data into evoTS is to use the function `read.paleoTS` from the paleoTS package. This function imports data from a text file with four columns corresponding to sample sizes, trait means, sample/population variances, and ages of the samples/populations (in that order) and converts the input to a paleoTS object.

See also the [paleoTS vignette](https://cran.r-project.org/web/packages/paleoTS/vignettes/paleoTS_basics.html) for more info on how to import data and create a paleoTS object.

