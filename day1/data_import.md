---
layout: default
title: Data import
permalink: /day1/data_import/
---

## Getting data into evoTS format

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

You can also see the [paleoTS vignette](https://cran.r-project.org/web/packages/paleoTS/vignettes/paleoTS_basics.html) for more info on how to import data and create a paleoTS object.


## Data included in evoTS

Two evolutionary sequences (time series) of phenotypic change are included in the evoTS package. The data are from the diatom lineage _Stephanodiscus yellowstonensis_ and were originally published in [Theriot _et al._ (2006)](https://www.cambridge.org/core/journals/paleobiology/article/abs/late-quaternary-rapid-morphological-evolution-of-an-endemic-diatom-in-yellowstone-lake-wyoming/B28737E2C2B8FBEC5D0577B5757401A5). Each trait (number of ribs and the diameter) consists of 63 samples spanning almost 14 000 years of phenotypic change.

You can access the data by typing ```ribs_S.yellowstonensis```or ```diameter_S.yellowstonensis```. 

We will use these data to illustrate many of the functions implemented in evoTS.

If we for example want to investigate proportional phenotypic change in the diameter of _S. yellowstonensis_ (measured in micrometers), we first do an approximate log-transformation of the data:

```r
ln_diameter <- paleoTS::ln.paleoTS(diameter_S.yellowstonensis)
```

We then convert the time vector in the data set to unit length (i.e., the length in time from the oldest to youngest sample/population in the data set becomes 1):

```r
ln_diameter$tt <- ln_diameter$tt/(max(ln_diameter$tt))
```

Such a linear transformation of the time vector does not change how the estimated parameters describe the evolutionary dynamics in the data, but ease parameter estimation and interpretation of certain model parameters. We can also plot the data to have a look at the phenotypic changes:

```r
plotevoTS(ln_diameter)
```

![plotevoTS](/assets/images/diameter.png)


## Simulating data

It is possible to simulate data for all implemented models in evoTS. Standard R-package documentation can be seen by entering `?sim.OUBM` and `?sim.accel.decel`.

---

Later, in the excersises, we will go through how to import data into R and transform the data to paleoTS/evoTS format. But for now, this example dataset is all you need for the introduction to [univariate models]({% link day1/univar.md %}).

