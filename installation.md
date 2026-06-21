---
layout: default
title: Installation
permalink: /installation/
---

# Installing softwear

---

## R and Rstudio
If you don't have R and Rstudio on your laptop already, you can install the R software by following the instructions on [this page](https://mirrors.cicku.me/cran/). 

After installing R, you can install Rstudio by following the directions [here](https://docs.posit.co/ide/user/#rstudio-ide-oss-downloads). If you need an introduction on how to use Rstudio, you can find one [here](https://www.datacamp.com/tutorial/r-studio-tutorial?dc_referrer=https%3A%2F%2Fwww.google.com%2F).

## evoTS and paleoTS
Now that you have the basic software installed, open R studio and install the evoTS package.

Install from CRAN like this:

```r
install.packages("evoTS")
```

Or from GitHub like this:

```r
install.packages("devtools")
devtools::install_github("klvoje/evoTS")
```

Check if the package is installed by running:

```r
library(evoTS)
```

## Other packages

If not already installed, you can also install these packages that we will use during the workshop:

```r
install.packages("tidyverse")
```

If everything seems to work well, head over to the [introduction](/introduction/).


