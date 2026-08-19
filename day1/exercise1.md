---
layout: default
title: Exercise 1
permalink: /day1/exercise1/
---

# Exercise 1

---

## Data

In this exercise, we will use data from the [Phenotypic Evolution Time Series (PETS) database](https://pets.nhm.uio.no/PETS/) to fit and assess different univariate models. PETS is a public database with time series of phenotypic change within lineages. The database is maintained by a group of international evolutionary biologists and paleontologists. You can first explore the data on the website linked above. It is possible to filter the data based on a range of options and you can find data from both modern and fossil lineages.

The phenotypic traits have been measured in units on a ratio scale, and include all measures of lengths, widths, areas, counts etc. The database also includes phenotypic traits on ratio scales that have been log-transformed (e.g., log length in cm). Computing means and averages are meaningful statistical operations of measurements on a ratio scale (or log-transformed ratio scale variables), which is not the case for all scale types (e.g., measures on nominal or ordinal scales). Principal components scores are not part of the database for various reasons that could make the evolutionary interpretation such scores difficult.

See the [PETS help page](https://pets.nhm.uio.no/PETS/) for an explanation of the content in the data tables you can download.

After exploring the webpage for a bit, download a time series you want to work with and place it in your working directory. You download the dataset by selecting the specific study un _View/download individual series_ and then clicking Download on the right side. For this exercise you can download the time series as a paleoTS object. Also check in the metadata wether or not the data is log-transformed. If not, log-transform the data before continuing with analyses. 

### Example of how to download and import data from PETS:

Here, we select a time series on teeth crown height in a lineage of extinct giant sharks (_Carcharocles megalodon_) from [Pimiento and Balk (2015)](https://www.cambridge.org/core/journals/paleobiology/article/bodysize-trends-of-the-extinct-giant-shark-carcharocles-megalodon-a-deeptime-perspective-on-marine-apex-predators/03A62B39329A8595DD129EEC9BE8A065).

![PETS data](/assets/images/PETS.png)


## Import and prepare data in R

Now, we are going to import the text files into R and get them into the correct format for analysis with evoTS. Make sure you have loaded the necessary packages (evoTS and tidyverse).
