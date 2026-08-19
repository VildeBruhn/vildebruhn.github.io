---
layout: default
title: Exercise 2
permalink: /day1/exercise2/
---

# Exercise 2

In this exercise, we will use three time series to assess relative and absolute fit. You can download the time series by clicking the links below:

- <a href="{{ '/assets/data_exercises/ts_1.txt' | relative_url }}" download>#Time series 1#</a>: mean teeth crown height in a lineage of extinct giant sharks (_Carcharocles megalodon_) from [Pimiento and Balk (2015)](https://www.cambridge.org/core/journals/paleobiology/article/bodysize-trends-of-the-extinct-giant-shark-carcharocles-megalodon-a-deeptime-perspective-on-marine-apex-predators/03A62B39329A8595DD129EEC9BE8A065)
- <a href="{{ '/assets/data_exercises/ts_2.txt' | relative_url }}" download>#Time series 2#</a>: mean number of chambers in a lineage of foraminifera (_Globorotalia sp._) from [Malmgren and Kennet (1981)](https://www.jstor.org/stable/2400475)
- <a href="{{ '/assets/data_exercises/ts_3.txt' | relative_url }}" download>Time series 3</a>: first lower molar occlusal surface in a lineage of Pliocene _Ogmodontomys_ (rodents) from [Marcolini and Martin (2008)](https://www.academia.edu/48202602/Mosaic_evolution_in_first_lower_molars_of_Pliocene_I_Ogmodontomys_I_Rodentia_Arvicolidae_from_the_Meade_Basin_of_southwestern_Kansas_USA_)

---

Import and prepare the data for analysis as we did in exercise 1, none of the time series are log-transformed. When all the time series are on the correct format, use what you have learned in the previous sections to:

- Fit different univariate models and assess relative fit (AICc)
- Use adequacy tests to evaluate absolute fit of the model that fitted the data best according to relative fit
- If the difference between the first and second best model according to AICc is small (e.g., <2), you can also try to see if the second best model is an adequate statistical description of the time series


### Questions to think about

- What are the different uses of relative and absolute fit tests?
- Is one type of test better than the other?

---

This was the last exercise for today. Tomorrow we will move on to [multivariate models]({% link day2/multivar_URW.md %})
