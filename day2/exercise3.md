---
layout: default
title: Exercise 3
permalink: /day2/exercise3/
---

# Exercise 3

---

## Data

In this exercise, we will use parts of a dataset from [Jensen _et al._ (2022)](https://www.pnas.org/doi/abs/10.1073/pnas.2207634119), looking into body size adaptations in a wild population of Atlantic salmon (_Salmo salar_). The salomon resides in a river impacted by hydropower development, affecting the waterflow in the river. The phenotypic time series used here can also be downloaded (in raw version) from the [PETS database](https://pets.nhm.uio.no/PETS/). The body size is measured as weight (grams). The waterflow (climatic variable) is measured as volume/time ($\frac{m^3}{s}$) from june to september. The measurements take place over 61 years from 1955-2016,with 7 years missing, a total of 54 measured years. The data include number of samples (N), trait/waterflow mean, trait/waterflow variance, and the age (year of sampling) in million years (where the oldest sample has age = 0).

<small>_Note: Where the sample size is 1 for the waterflow dataset, the variance is set to $\text{mean} \times 0.05$._</small>

Download the <a href="{{ '/assets/data_exercises/body_size.txt' | relative_url }}" download>body size measurements</a> and <a href="{{ '/assets/data_exercises/waterflow_jun_sep.txt' | relative_url }}" download>waterflow</a> data (click the links), and place the files in your working directory.


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
  paleoTS::as.paleoTS(mm = x$trait_mean, vv = x$trait_var, nn = x$N, tt = x$year, oldest = "first")
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

* Which model fit the data best?
* What does it mean that a model fit the data best?
* What are the biological interpretations of your results?

---

When you feel done working with this exercise, move along to [exercise 4]({% link day2/exercise4.md %}).


 
