# [Not Done:] The Linear Model V: Mixed Effects Linear Models

Mixed-effects linear modelling / Multilevel modelling

lmer




```r
# Load the libraries we'll use in this chapter
library(ggplot2) # for plotting
library(lme4)
```

```
## Loading required package: Matrix
```


Deciding which is a fixed effect and which is a random effect

Do not put the same grouping *factor* as both a fixed and a random effect^[https://biologyforfun.wordpress.com/2015/08/31/two-little-annoying-stats-detail/]. (You could put a continuous variable as both a fixed and random effect if you have enough data)
