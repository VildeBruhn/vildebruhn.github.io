---
layout: default
title: Exercise 1
permalink: /exercise1/
---

# Exercise 1

---

## Data

In this exercise, we will use parts of a dataset from [Jensen _et. al_ (2022)](https://www.pnas.org/doi/abs/10.1073/pnas.2207634119), looking into body size adaptations in a wild population of Atlantic salmon (_Salmo salar_) residing in a river impacted by hydropower development, affecting the waterflow in the river. The phenotypic time series used here can also be downloaded (in raw version) from the [PETS database]({% link /other_resources/PETS.md %}). The body mass is measured as weight (grams). The waterflow (climatic variable) is measured as volume/time ($\frac{m^3}{s}$) in june to september. The measurements take place over 61 years (with 7 years missing) from 1955-2016, a total of 54 measured years. The data include number of samples (N), trait/waterflow mean, trait/waterflow variance, and the age of the sample in million years (where the oldest sample has age = 0).

<small>Note: Where the sample size is 1, the variance is set to a number very close to zero because it is not possible to take the logarithm of zero.</small>

Download the <a href="{{ '/assets/data_exercises/body_size.txt' | relative_url }}" download>body size measurements</a> and <a href="{{ '/assets/data_exercises/waterflow_jun_sep.txt' | relative_url }}" download>waterflow</a> data, and place the files in your working directory.


## Import and prepare data in R

Now, we are going to import the text files into R and get them into the correct format for analysis with evoTS. Make sure you have loaded the necessary packages (evoTS and tidyverse).

First, set your working directory:

```r
PATH = "PATH_TO_WORKING_DIRECTORY"
setwd(PATH)
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
paleo_data <- lapply(data, function(x) {
  as.paleoTS(mm = x$trait_mean, vv = x$trait_var, nn = x$N, tt = x$age_MY, oldest = "first")
})
 
