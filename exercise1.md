---
layout: default
title: Exercise 1
permalink: /exercise1/
---

# Exercise 1

---

## Data

In this exercise, we will use parts of a dataset from [Jensen _et. al_ (2022)](https://www.pnas.org/doi/abs/10.1073/pnas.2207634119), looking into body size adaptations in a wild population of Atlantic salmon (_Salmo salar_) residing in a river impacted by hydropower development, affecting the waterflow in the river. The phenotypic time series used here can also be downloaded (in raw version) from the [PETS database]({% link other_resources/PETS.md %}). The body mass is measured as weight (grams). The waterflow (climatic variable) is measured as volume/time ($\frac{m^3}{s}$) in june to september. The measurements take place over 61 years (with 7 years missing) from 1955-2016, a total of 54 measured years. The data include number of samples (N), trait/waterflow mean, trait/waterflow variance, and the age (year of sampling) in million years (where the oldest sample has age = 0).

<small>Note: Where the sample size is 1, the variance is set to a number very close to zero because it is not possible to take the logarithm of zero.</small>

Download the <a href="{{ '/assets/data_exercises/body_size.txt' | relative_url }}" download>body size measurements</a> and <a href="{{ '/assets/data_exercises/waterflow_jun_sep.txt' | relative_url }}" download>waterflow</a> data, and place the files in your working directory.


## Import and prepare data in R

Now, we are going to import the text files into R and get them into the correct format for analysis with evoTS. Make sure you have loaded the necessary packages (evoTS and tidyverse).

First, set your working directory:

```r
setwd("PATH_TO_WORKING_DIRECTORY")
```

Then, import the data:

```r
body_size <- read_delim("./body_size.txt", col_names = TRUE, delim = "\t")
waterflow <- read_delim("./waterflow_jun_sep.txt", col_names = TRUE, delim = "\t")
```

It is easier to work with the time series if we store them in a list like this:

```r
data <- list()
data$body_size <- as.list(body_size)
data$waterflow <- as.list(waterflow)
```

And now we can transform the data into paleoTS objects:

```r
paleoTS_data <- lapply(data, function(x) {
  paleoTS::as.paleoTS(mm = x$trait_mean, vv = x$trait_var, nn = x$N, tt = x$age_MY, oldest = "first")
})
```

Next, we log-transform the data:

```r
ln_data <- lapply(paleoTS_data, paleoTS::ln.paleoTS)
```

We also convert the time vector to relative time (0 to 1):

```r
ln_data <- lapply(ln_data, function(x) {
  x$tt <- x$tt/(max(x$tt))
  x
})
```

The last thing to do is to convert the data into a multivariate evoTS object:

```r
evoTS_data <- make.multivar.evoTS(ln_data$body_size, ln_data$waterflow)
```

Now you are ready to analyse the data!


## Analyses and questions

Use what you have learned in the tutorial to explore the data. You can for example first plot the data to visualise it and then analyse it by fitting different multivariate models, both different types of unbiased random walks and OU models.

Suggestions for questions to think about:

* klklkl
* lklkl
* lklk

Have fun!

---

When you feel done working with this exercise, move along to [exercise 2]({% link exercise2.md %}).


 
