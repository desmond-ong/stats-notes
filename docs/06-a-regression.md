# The Linear Model I: Linear Regression

In the next few Chapters, we will learning about a workhorse tool of analytics: the linear model, which allows us to run linear regression models that we can use to estimate simple trends and look at how some variables in our data may affect other variables. The linear model is a key tool used in many fields, from psychology, economics, linguistics, business, as well as in some physical sciences like ecology. Thus, it is an essential part of the data scientist's toolkit.


In this Chapter, we will be going over linear regression with one or more independent variables, to predict a continuous dependent variable. We will also discuss how to interpret the output of a simple regression model, and discuss the case of categorical independent variables.




The learning objectives for this chapter are:

- Readers should be able to understand and be able to use both simple and multiple regression to estimate simple trends.

- Readers should be able to interpret the output of a regression model, including regression coefficients, confidence intervals, hypothesis testing about coefficients, and goodness-of-fit statistics.

- Readers should be able to interpret the meaning of dummy-coded variables used to model categorical independent variables.




```r
# Load the libraries we'll use in this chapter
library(tidyverse) 
```

I've also made up some simulated data just for the next few sections that we will use to illustrate linear regression. Below is the code I used to generate `df1`: $X$ is just a vector from 0 to 10, and $Y$ is an affine transformation of $X$ with some random noise added.


```r
set.seed(1)
df1 = data.frame(X=c(seq(0,10)))
df1$Y = -2 + df1$X*2 + rnorm(n=nrow(df1), mean=0, sd=1)
```

## Basics of Linear Regression

Linear Regression really boils down to this simple equation. 

$$ Y = b_0 + b_1 X $$

We want to predict $Y$, our outcome variable or **dependent variable (DV)**, using $X$, which can have several names, like **independent variable (IV)**, predictor or regressor.

$b_0$ and $b_1$ are *parameters* that we have to estimate in the model: $b_0$ is the Constant or *Intercept* term, and $b_1$ is generally called a regression coefficient, or the slope.

Below, we have a simple graphical illustration. Let's say I have a dataset of $X$ and $Y$ values, and I plot a scatterplot of $Y$ on the vertical axis and $X$ on the horizontal. It's convention that the dependent variable is always on the vertical axis. 

On the right I've also drawn the "best-fit" line to the data. Graphically, $b_0$ is where the line crosses the vertical axis (i.e., when $X$ is 0), and $b_1$ is the slope of the line. Some readers may have learnt this in high school, where the slope of the line is the "rise" over the "run", so how many units of "Y" do we increase ('rise') as we increase "X".




<img src="06-a-regression_files/figure-html/lmi-plot1-1.png" width="336" style="display: block; margin: auto;" /><img src="06-a-regression_files/figure-html/lmi-plot1-2.png" width="336" style="display: block; margin: auto;" />

And that's the basic idea. Our linear model is one way of trying to explain $Y$ using $X$, which is by multiplying $X$ by the regression coefficient $b_1$ and adding a constant $b_0$. It's so simple, yet, it is a very powerful and widely used tool, and we shall see more over the rest of this chapter and the next few chapters.


Our dependent variable $Y$ is always the quantity that we are interested in and that we are trying to predict. Our independent variables $X$ are variables that we use to predict $Y$.

Here are some examples:

Dependent Variable <br> Y | Independent Variable <br> X
--- | ---
Income | Years of Education
Quartly Revenue | Expenditure on Marketing
Quantity of Products Sold | Month of Year
Click-through rate | Colour of advertisement <br>(Note that this is an experiment!)

Perhaps we are interested in predicting an individual’s income based on their years of education. Or for a company, we might want to predict Quarterly Revenue using the amount we spent on Marketing, or predict sales at in different months of the year.

We can also use regression to model the results of experiments. For example, we might be interested in whether changing the colour of an advertisement will affect how effective it is, measured by how many people click on the advertisement (called the click-through rate, measuring how many people click on our ad). So we may show some of our participants the ad in blue, some in green, etc, and then see how that affects our click-through rate. So linear modelling can and is frequently used to model experiments as well.


Independent Variables can be **continuous** (e.g., Years; Expenditure), or they can also be **discrete**/**categorical** (e.g., Month, Colour, Ethnicity). And we shall see examples of both in this chapter

Similarly, Dependent Variables can either be **continuous** or **categorical**. In this Chapter we shall only cover continuous DVs, and we shall learn about categorical DVs in the next Chapter on Logistic Regression.




## Running a regression


The first step to running a regression is to be clear about what is your dependent variable of interest, and what are your independent variables. Often, this is clear from the context: As a researcher we have an objective to model or predict a certain variable --- that will be the dependent variable, $Y$. And we have variables that we think would predict that, and those will be our $X$'s. (Later we'll discuss the differences between predictors, covariates, and confounders, which could all statistically affect the depenent variable.)

### Structure your dataset

The second step is to structure your data. 
For most Linear Regression (at least for the examples in this Chapter), we almost always want `wide`-form data, discussed in the earlier Chapter on data handling, where you have each row of the data frame be one observation, and you have one column for $Y$ and one column for $X$. (In later Chapters we shall see when we may need `long`-form data for other types of regression.)


For example, the `mtcars` dataset that comes with R is an example of wide-form data. Each row is one observation (i.e., one car), and each column is an attribute/variable associated with that observation (e.g., fuel economy, number of cylinders, horsepower, etc). 


```r
head(mtcars,3)
```

```
##                mpg cyl disp  hp drat    wt  qsec vs am gear carb
## Mazda RX4     21.0   6  160 110 3.90 2.620 16.46  0  1    4    4
## Mazda RX4 Wag 21.0   6  160 110 3.90 2.875 17.02  0  1    4    4
## Datsun 710    22.8   4  108  93 3.85 2.320 18.61  1  1    4    1
```

### Visualize

The third step is to visualize your data, discussed also in the earlier Chapter on Data Visualization. For regression analyses, visualization of your data allows you to see whether there may be linear trends or non-linear trends (or no trends).

<img src="06-a-regression_files/figure-html/lmi-plot2a-1.png" width="336" style="display: block; margin: auto;" /><img src="06-a-regression_files/figure-html/lmi-plot2a-2.png" width="336" style="display: block; margin: auto;" /><img src="06-a-regression_files/figure-html/lmi-plot2a-3.png" width="336" style="display: block; margin: auto;" /><img src="06-a-regression_files/figure-html/lmi-plot2a-4.png" width="336" style="display: block; margin: auto;" />

Linear models assume that there exists a linear trend between $Y$ and $X$. If you have a non-linear trend, like the quadratic and exponential ones shown here, you may want to think about transforming some of your variables to see if you can get a linear trend before running the linear model. If you visually no trend, like the last plot above, you can confirm this lack of trend just by running a linear model.

<img src="06-a-regression_files/figure-html/lmi-plot2b-1.png" width="336" style="display: block; margin: auto;" /><img src="06-a-regression_files/figure-html/lmi-plot2b-2.png" width="336" style="display: block; margin: auto;" />


Plotting can also help with troubleshooting. For example, you’ll be able to immediately see if you accidentally have a factor instead of a numeric variable, or if you have possible outliers (like the graph on the left) or possibly missing data (like the graph on the right).

Let's plot the two variables in our toy dataset `df1`:

<img src="06-a-regression_files/figure-html/lmi-plot2c-1.png" width="336" style="display: block; margin: auto;" />

Looks very linear! So we should expect to see a strong linear relationship between `df$X` and `df$Y`.

###### Additional plotting tips {-}

If you are using `ggplot()` to plot your data, you can ask it to plot the best fit __linear__ line using `geom_smooth(method="lm", se=FALSE)`. 

> (If you don't specify the method, it defaults to a non-linear smoothing, e.g. LOESS smoothing using stats::loess() for N<1000 observations. 
> "se=FALSE" will remove the standard error ribbon -- try setting it to true to see what it gives you).

For example, if I wanted to plot the best-fitting linear line to the graph above with the exponential trend, I can use the following chunk of code, which produces:


```r
ggplot(df0, aes(x=x, y=y_exp)) + geom_point() + 
  geom_smooth(method="lm", se=FALSE) +
  xlab("X \n Exponential Trend") + ylab("Y") + theme_bw() + 
  theme(panel.grid = element_blank(), 
        panel.background = element_blank(),
        axis.ticks = element_blank(),
        axis.text = element_blank())
```

<img src="06-a-regression_files/figure-html/lmi-plot2d-example-of-smooth-1.png" width="336" style="display: block; margin: auto;" />

Now you can see above that the line does have a significant slope (and indeed if you actually run the linear model using the code in the next section, you'll see that there is a significant linear relationship). However, the linear line is not really the best description of the data, is it? This is why it is helpful to always plot our variables, so that we can see if we're trying to fit a linear line to data that is better described by a non-linear relationship.

Consider this other case, where we now have discrete X variables. 

<img src="06-a-regression_files/figure-html/lmi-plot2e-example-of-jitter1-1.png" width="336" style="display: block; margin: auto;" />

It might be hard to see any linear trend. And in fact a lot of the data are overlapping, so it's hard to see where the data are! This is where we have two more plotting tricks up our sleeve.

First, is to jitter each point. Jittering means slightly shifting each datapoint a little to the left/right (jitter in the "width" direction), or up and down ("height"). This is mainly for visualization, we are not actually changing our data values. We do this giving the option `position=position_jitter(width= XXX , height= YYY)` to `geom_point()`, where XXX is how much is our maximum jitter on the horizontal/x direction, and YYY is how much we want to jitter on the vertical/y axis. If say we have an integer valued variable ("How happy are you" on a scale from 1-5), then I recommend a jitter of about 0.1 to 0.2. This is big enough to be seen, but small enough to not be confusing.

Second tip, is to make your points slightly transparent, using the `alpha = ZZZ` option in `geom_point()`. This will help you see dense clusters of points vs. non-dense clusters. 

And of course, we can use `geom_smooth()`. So here we have:


```r
ggplot(df0, aes(x=x_discrete, y=y_discrete)) + 
  geom_point(position=position_jitter(width=0.2, height= 0.2), alpha=0.3) + 
  geom_smooth(method="lm", se=FALSE) +
  xlab("X \n Discrete points, with jitter, alpha, and best-fit") + ylab("Y") +
  theme_bw() + 
  theme(panel.grid = element_blank(), 
        panel.background = element_blank(),
        axis.ticks = element_blank(),
        axis.text = element_blank())
```

<img src="06-a-regression_files/figure-html/lmi-plot2e-example-of-jitter2-1.png" width="336" style="display: block; margin: auto;" />

And it's much easier to see the trend!

### Running the linear model


Finally, we're ready to run the model. And in fact, it's one line of code. `lm` for linear model, and then you provide an "equation", `y~x`, which is R syntax for "Y depends upon X". The final argument is the dataframe in which the data is stored.


```r
# running a Linear Model
fit1 <- lm(Y~X, df1)
```


> <span style='color:blue; font-size:150%'>TIP!</span> 
> Best practice tip: If you store all your Y and X variables in your dataframe as wide form data, you can just write `lm(y~x, df1)`, which is very neat syntax.
> <br> It's not as clean to write `lm(df1$y~df1$x)`, and I discourage this.


After fitting the model, we can just call `summary()` on our lm object, in order to view the output of the model.



```r
# examining the output
summary(fit1)
```

```
## 
## Call:
## lm(formula = Y ~ X, data = df1)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -1.0781 -0.5736  0.1260  0.3071  1.5452 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept) -2.26117    0.46171  -4.897 0.000851 ***
## X            2.10376    0.07804  26.956 6.44e-10 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.8185 on 9 degrees of freedom
## Multiple R-squared:  0.9878,	Adjusted R-squared:  0.9864 
## F-statistic: 726.6 on 1 and 9 DF,  p-value: 6.442e-10
```


There's a lot of information here, which we will break down in the next few sections, after a short digression to discuss how (Ordinary Least Squares) regression is solved.





## Ordinary Least Squares Regression

In this section we'll be taking a quick peek behind what the model is doing, and we'll discuss the formulation of **Ordinary Least Squares** regression. 


In linear regression, we assume that the "true" model is such that $Y$ is $b_0$ + $b_1$\*$X$ plus some "errors", which could be due to other, unmeasured factors (omitted variables), or maybe just random noise.

$$ ``\text{True" model: } Y = b_0 + b_1 X + \epsilon $$ 

In fact, within regression, we make the further assumption that the errors are normally distributed around zero with some variance. (So $\epsilon \sim \mathscr{N}(0, \sigma)$).

Since we don't know the "true" $b_0$ and $b_1$, we can only choose $\hat{b_0}$ and $\hat{b_1}$; using this we can compute the prediction of our model, $\hat{Y}$. We want our $\hat{b_0}$ and $\hat{b_1}$ to be as close to the "true" $b_0$ and $b_1$, which will also make our predictions $\hat{Y}$ as close to the actual $Y$.

To do this, we define the Residual or **Residual Error** of our model. For the $i$-th data point, the residual is the difference between the actual $Y_i$ and our model predicted, $\hat{Y_i}$. 

$$ \text{Residual Error: } e_i = Y_i - \hat{Y_i} $$


Here's an illustration. Let's say I start off just by drawing a green line through the origin with some upward slope. 

<img src="06-a-regression_files/figure-html/lmi-ols1-1.png" width="336" style="display: block; margin: auto;" />


Here, the red lines illustrate the residual error; the difference between the actual value and our prediction. And to make our model better, we want to minimise the red bars.
Some red bars are lower, some are higher, so let’s pivot the slope upwards.


<img src="06-a-regression_files/figure-html/lmi-ols2-1.png" width="336" style="display: block; margin: auto;" />


Now, we have this yellow line. It looks better, overall the bars are smaller. Now we note that all the red bars are below, so instead of pivoting, let’s move the whole line down.



<img src="06-a-regression_files/figure-html/lmi-ols3-1.png" width="336" style="display: block; margin: auto;" />

And finally we get the blue line here, which is the best solution to minimising the red bars. We want to minimise the residuals. How is this done?



### Ordinary Least Squares Derivation

The residuals can be positive or negative, so if we simply add the residuals up we might be cancelling out some of them. So instead of minimising the sum of the residuals, we usually choose to square the residuals and minimise the sum of squares of the residuals. (Mathematically it becomes easier to work with the square than the absolute value). 

So here, we have the Ordinary Least Squares Regression, where the goal is to choose  $\hat{b_0}$ and $\hat{b_1}$ to minimise the sum of squares of the residuals $\sum_{i} e_i^2 = \sum_i \left( Y_i - \hat{Y_i} \right)^2$.


We can do this by taking the partial derivative with respect to $\hat{b_0}$ and $\hat{b_1}$, and setting them both to 0. First, we define the following variables to simplify notation:

\begin{align}
\text{Define } \bar{Y} &\equiv \frac{1}{n}\sum_i^n Y_i \\
\text{Define } \bar{X} &\equiv \frac{1}{n}\sum_i^n X_i \\
\text{Define } Z &\equiv \sum_i \left( Y_i - \hat{Y_i} \right)^2 \\
&= \left( Y_i - \hat{b_0} - \hat{b_1} X \right)^2 \\
\end{align}

Then we take the partial derivative with respect to $\hat{b_0}$, solve for this $\hat{b_0}$, then substitute it into the partial deriative with respect to $\hat{b_1}$:
\begin{align}
\text{Partial deriative w.r.t. } \hat{b_0} : \; \; 
\frac{\partial Z}{\partial \hat{b_0}} &= \sum_i^n -2 \left(Y_i - \hat{b_0} - \hat{b_1}X_i \right) \\
\text{Setting the derivative to 0 and solving, we have: } \; \;
\hat{b_0} &= \frac{1}{n}\sum_i^n Y_i - \frac{1}{n}\sum_i^n\hat{b_1}X_i \\
\implies \hat{b_0} &= \bar{Y} - \hat{b_1} \bar{X} \\
\text{Partial deriative w.r.t. } \hat{b_1} : \; \;\frac{\partial Z}{\partial \hat{b_1}} &= \sum_i^n  -2X_i \left( Y_i - \hat{b_0} - \hat{b_1}X_i \right) 
\end{align}
\begin{align}
\text{Setting the derivative to 0 and substituting $\hat{b_1}$, we have: } & \\
\sum_i^n X_i Y_i - \sum_i^n (\bar{Y}-\hat{b_1}\bar{X})X_i - \sum_i^n\hat{b_1}X_i^2 &= 0 \\
\sum_i^n X_i Y_i - \bar{Y} \sum_i^n X_i + \hat{b_1} \left(\bar{X} \sum_i^n X_i - \sum_i^n X_i^2 \right) &= 0 \\
\hat{b_1} &= \frac{\sum_i^n X_i Y_i - \bar{Y}\sum_i^n X_i }{ \sum_i^n X_i^2 - \bar{X} \sum_i^n X_i } \\ &= \frac{\sum_i^n X_i Y_i - n\bar{X}\bar{Y}}{ \sum_i^n X_i^2 - n\bar{X}^2} \\
\text{simplifying: } \; \; \hat{b_1} &= \frac{\sum_i^n (X_i - \bar{X})(Y_i - \bar{Y})}{ \sum_i^n (X_i - \bar{X})^2 }
\end{align}

And we end up with the final OLS solution:

\begin{align}
\hat{b_0} &= \bar{Y} - \hat{b_1} \bar{X} \\
\hat{b_1} &= \frac{\sum_i^n (X_i - \bar{X})(Y_i - \bar{Y})}{ \sum_i^n (X_i - \bar{X})^2 } = \frac{Cov(X,Y)}{Var(X)}
\end{align}

The good news is that $R$ already does this for you. Let's check this solution with the `lm()` model we fit on the previous page!

Let's do $\hat{b_1}$ first: in R, we can calculate the covariance of $X$ and $Y$, and divide that by the variance of $X$, and save that into b1-hat: for this dataset we get 2.10. 



```r
b1hat = cov(df1$X, df1$Y) / var(df1$X)
b1hat
```

```
## [1] 2.103757
```

Following the equation for $\hat{b_0}$, we can take the mean of $Y$, and subtract $\hat{b_1}$ times the mean of $X$, and we get -2.26.


```r
b0hat = mean(df1$Y) - b1hat * mean(df1$X)
b0hat
```

```
## [1] -2.261167
```


Finally let's go back to our regression output table, which we can summon using `summary(...)$coeff`. 



```r
fit1 <- lm(Y~X, df1)
# verifying the OLS solution
summary(fit1)$coeff
```

```
##              Estimate Std. Error   t value    Pr(>|t|)
## (Intercept) -2.261167 0.46170984 -4.897376 8.50721e-04
## X            2.103757 0.07804321 26.956313 6.44250e-10
```

We can see that the Estimate of the Intercept, i.e., $\hat{b_0}$, is -2.26, and the Estimate of the Coefficient on $X$, i.e., $\hat{b_1}$, is 2.10. They agree exactly! Excellent. So our `lm()` is really doing OLS regression.


Again, since $R$ does all the calculations for you, it's not necessary to know how to derive the OLS solutions (especially with more than one independent variable $X$), but it is handy to know the intuition behind it, especially when we get to more complicated regression.



## Interpreting the output of a regression model

In this section we'll be going over the different parts of the linear model output. First, we'll talk about the coefficient table, then we'll talk about goodness-of-fit statistics. 


Let's re-run the same model from before:


```r
fit1 <- lm(Y~X, df1)
summary(fit1)
```

```
## 
## Call:
## lm(formula = Y ~ X, data = df1)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -1.0781 -0.5736  0.1260  0.3071  1.5452 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept) -2.26117    0.46171  -4.897 0.000851 ***
## X            2.10376    0.07804  26.956 6.44e-10 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.8185 on 9 degrees of freedom
## Multiple R-squared:  0.9878,	Adjusted R-squared:  0.9864 
## F-statistic: 726.6 on 1 and 9 DF,  p-value: 6.442e-10
```

First, `summary()` helpfully reiterates the formula that you put in. This is useful to check that it's running what you thought it ran.

```
Call:
lm(formula = Y ~ X, data = df1)
```

It also tells you the minimum, 1st quantile (25\%-ile), median, 3rd quantile (75\%-ile), and maximum of the residuals ($e_i = Y_i - \hat{Y_i}$). That is, the minimum residual error of this model is -1.0781, the median residual error is 0.1260, and the maximum is 1.5452. 

```
Residuals:
    Min      1Q  Median      3Q     Max 
-1.0781 -0.5736  0.1260  0.3071  1.5452 
```





### The coefficient table

Let's turn next to the coefficient table.


```r
summary(fit1)$coeff
```

```
##              Estimate Std. Error   t value    Pr(>|t|)
## (Intercept) -2.261167 0.46170984 -4.897376 8.50721e-04
## X            2.103757 0.07804321 26.956313 6.44250e-10
```


Let’s focus on the "Estimate" column. These are the point estimate of $b_0$ and $b_1$, for the equation $$Y= b_0 + b_1 X$$ 

What do these numbers mean?


> $b_0$: The mean value of $Y$ when $X$ is zero

The meaning of the intercept, $b_0$, is pretty straightforward. It is the average value of the dependent variable $Y$ when the independent variable $X$ is set to 0. (Graphically, it is the vertical intercept: the point at which the line crosses the vertical axis.)


> $b_1$: According to the model, a one-unit change in $X$ results in a $b_1$-unit change in $Y$


The coefficient on $X$, $b_1$, captures the magnitude of change in $Y$, per unit-change in $X$. Graphically, this is the slope of the regression line; if $b_1$ is larger, the line will have a steeper slope. Conversely, if $b_1$ is smaller in magnitude, the line will have a more shallow slope. If $b_1$ is positive, the slope will slope upwards `/`, otherwise if $b_1$ is negative, the slope will go downwards `\`.


#### Example: Interpreting Simple Regression Coefficients {-}

Let's go through an example. Let's say we fit a model to predict our monthly profit given the amount that we spent on advertising. Both Profit and Expenditure are measured in \$.

$$\text{Profit} = -2500 + 3.21* \text{ExpenditureOnAdvertising}$$

Coefficient | Interpretation
--- | ---
($b_0$) | Monthly profit is -\$2500 without any money spent on advertising.
($b_1$) | For every dollar spent on Advertising, Profit increases by \$3.21

> Q: Why could profit be negative here?

Negative (or otherwise unusual) intercepts arise all the time in linear regression. In this example, this just means that, if we spent \$0 on advertising, we would still incur a negative profit of \$2,500, which could be due to omitted variables such as the amount we have to spent on rent, wages, and other upkeep.

> Note that it is very important to be aware of the **units** that each of the variables, both $Y$ and $X$, are measured in. This will ensure accurate interpretation of the coefficients!



#### The rest of the coefficient table {-}

The estimated value of $b_0$ and $b_1$ are given in the first column (`Estimate`) of the coefficient table. Next to the estimates, we have the standard error of $b_0$ and $b_1$, which gives us a sense of the error associated with our estimate.


In the third column, we have the `t value`. This is the t-statistic for a one-sample t-test comparing this coefficient to zero. That is, it is the one-sample t-test for the null hypothesis that the coefficient is zero, against the alternative, two-sided hypothesis that it is not zero:
$$ H_0: b_j = 0 \\ H_1: b_j \neq 0 $$

In fact, the t value here, is simply the Estimate divided by the Standard Error. (You can check it yourself!) 
So with this t value, and the degrees of freedom of the model, we can actually calculate the `p value` for such a t test. R helpfully does this for you, and this is given in the fourth column, `Pr(>|t|)`. We can see that these numbers in this example are quite small, so both $b_0$ and $b_1$ are statistically different from zero.

To the right of the `Pr(>|t|)` column, R will helpfully print out certain significance codes. 

- If $p$ is between 0.1 and 0.05, R will print a `.`. 
- If $p$ is less than 0.05 ($\alpha$=5\% level of significance) but greater than 0.01 (1\%), R will print a single `*`. 
- If $p$ is less than 0.01 but greater than 0.001, R will print out two asteriks, `**`. 
- Finally, if $p$ is less than 0.001, R will print out three asterisks, `***`.

### Goodness-of-fit statistics

Finally we'll look at the last part of the summary output.

```
## Residual standard error: 0.8185 on 9 degrees of freedom
##   (1 observation deleted due to missingness)
## Multiple R-squared:  0.9878, Adjusted R-squared:  0.9864 
## F-statistic: 726.6 on 1 and 9 DF,  p-value: 6.442e-10
```

First, note that R will helpfully print out whether or not there were observations missing in our data. 

> (1 observation deleted due to missingness)

If, for any data point, either the $X$ value, or the $Y$ value (or both) are missing, then R will remove that observation from the linear model, and report it in the output. This is always something useful to check: do we have an abnormally large number of missing observations that we not expect? For example, perhaps one of the variables has a large number of missing observations? Or maybe when we were calculating new variables, we did not consider certain situations, and so end up with a lot of missing variables. (Or maybe we made a typo in our code!). This is always a good safety check before proceeding further. (Note that if there are no missing observations, R will omit this line).

Next, we'll discuss a very important statistic, called the coefficient of determination, or $R^2$ ("R-squared"), which is a measure of the **proportion of variance explained by the model**. $R^2$ is a number that always lies between 0 and 1. An $R^2$ of 1 means it's a perfect model, it explains all of the variance (all the data points lie on the line. Alternatively, all the residuals are 0). 

The total amount of variability of the data is captured in something called the Total Sum of Squares, which is the sum of the difference between each data point $Y_i$ and the mean $\bar{Y}$ (this is also related to the variance of $Y$):
\begin{align}
\text{Total Sum of Squares} \equiv \sum_i \left(Y_i - \bar{Y} \right)^2
\end{align}

The amount of variability that is explained by our model (which predicts $\hat{Y}$) is given by the Regression Sum of Squares, which is the sum of the squared error between our model predictions and the mean $\bar{Y}$:

\begin{align}
\text{Regression Sum of Squares} \equiv \sum_i \left(\hat{Y_i} - \bar{Y} \right)^2
\end{align}


And finally, the leftover amount of variability, also called the Residual Sum of Squares, is basically the difference between our model predictions $\hat{Y}$ and the actual data points $Y$. This was the term that Ordinary Least Squares regression tries to minimize, which we saw in the last Section.

\begin{align}
\text{Residual Sum of Squares} \equiv \sum_i \left(Y_i - \hat{Y_i} \right)^2
\end{align}

As it turns out, the Total Sum of Squares is made up of these two parts: the Regression Sum of Squares (or "*Explained*" Sum of Squares), and the Residual Sum of Squares (or the "*Unexplained*" Sum of Squares).

\begin{align}
\text{Total Sum of Squares} \equiv \text{Regression Sum of Squares} + \text{Residual Sum of Squares}
\end{align}


$R^2$ basically measures the proportion of explained variance over the total variance. In other words:

\begin{align}
R^2 &\equiv \frac{\text{Regression Sum of Squares}}{\text{Total Sum of Squares}} \\
&\equiv 1 - \frac{\text{Residual Sum of Squares}}{\text{Total Sum of Squares}} 
\end{align}


You can read off the $R^2$ value from the field indicated by "Multiple R-squared", i.e., 

> \#\# Multiple R-squared:  0.9878, ...

In the output above, the $R^2$ is 0.9878; this means that this model explains 98.8\% of the variance. That's really high!



Now, how good is a good $R^2$? Unfortunately there's no good answer, because it really depends on contexts. In some fields and in some contexts, even an $R^2$ of .10 to .20 could be really good. In other fields, maybe we would expect $R^2$s of .80 or .90!



## Examples: Simple Regression {#regression_example1}

Here’s a simple example to illustrate what we've discussed so far, by using a dataset that comes bundled with R, the `mtcars` dataset.  We can load the dataset using `data(mtcars)`.

First, let's see what the data looks like, using `head(mtcars)`, which prints out the first 6 rows of `mtcars`.


```r
data(mtcars)
head(mtcars)
```

```
##                    mpg cyl disp  hp drat    wt  qsec vs am gear carb
## Mazda RX4         21.0   6  160 110 3.90 2.620 16.46  0  1    4    4
## Mazda RX4 Wag     21.0   6  160 110 3.90 2.875 17.02  0  1    4    4
## Datsun 710        22.8   4  108  93 3.85 2.320 18.61  1  1    4    1
## Hornet 4 Drive    21.4   6  258 110 3.08 3.215 19.44  1  0    3    1
## Hornet Sportabout 18.7   8  360 175 3.15 3.440 17.02  0  0    3    2
## Valiant           18.1   6  225 105 2.76 3.460 20.22  1  0    3    1
```


We're interested in particular in predicting the fuel economy (`mtcars$mpg`, measured in miles per gallon) of certain vehicles using its horsepower (`mtcars$hp`).

Let's plot what they look like on a scatterplot.


```r
ggplot(mtcars, aes(x=hp, y=mpg)) + geom_point() + theme_bw()
```

<img src="06-a-regression_files/figure-html/lmi-mtcarsexample2-1.png" width="336" style="display: block; margin: auto;" />

It looks like there is a linear trend! We can see visually that as horsepower increases, fuel economy drops; large cars tend to guzzle more fuel than smaller ones. And finally, we can run the linear model using using `lm(mpg ~ hp, mtcars)`:



```r
summary(lm(mpg ~ hp, mtcars))
```

```
## 
## Call:
## lm(formula = mpg ~ hp, data = mtcars)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -5.7121 -2.1122 -0.8854  1.5819  8.2360 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept) 30.09886    1.63392  18.421  < 2e-16 ***
## hp          -0.06823    0.01012  -6.742 1.79e-07 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 3.863 on 30 degrees of freedom
## Multiple R-squared:  0.6024,	Adjusted R-squared:  0.5892 
## F-statistic: 45.46 on 1 and 30 DF,  p-value: 1.788e-07
```



Looking at the coefficient table, we can see that $b_0$, the intercept term, suggests suggests that, according to our model, a car with zero horsepower (if such a vehicle exists) would have a mean fuel economy of 30 mpg.

We can look at the coefficient on $X$, which is $b_1 = -0.068$. Now this means that every unit-increase in horsepower would be associated with a decrease in fuel efficiency by -0.068 miles per gallon. Now, these are quite strange units to think about: for one, we do not think about horsepower in 1's and 2's; we think of horsepower in the tens or hundreds, and typical cars have horsepowers around 120 (the median in our dataset is 123). Secondly, -0.068 miles per gallon sounds pretty little, given that we see in the data that the mean fuel consumption is about 20 mpg.

What we can do to help our interpretation is to multiply our coefficient to get more human-understandable values. This means that, according to the model, an increase of horsepower by 100 units, will be associated with a decrease in fuel efficiency by 6.8 miles per gallon. Ahh these numbers make more sense now. 


Finally, we note that the $R^2$ of this model is 0.6024. This means that the model explains about 60\% of the variance in the data. Again, it's hard to objectively say whether this is amazing or still needs more work, because this really depends on our desired context.



One takeaway from this example is that the linear model just calculates the coefficients based upon the numbers that we, as the analyst, provide it. It is up to us to then read the numbers that are output from the model, and **make sense** of those numbers, and interpret the meaning of those numbers. So this means being aware of units, and also not hesitating to wonder, "hmm this isn't right, did I code everything correctly?"






## Multiple Linear Regression



We’ve covered Simple Linear Regression, whereby "Simple" means just one independent variable. Next we’ll talk about Multiple Linear Regression, where "Multiple" just means multiple independent variables.

$$Y = b_0 + b_1X_1 + b_2X_2 + b_3X_3 + \ldots$$ 

This is actually quite a straightforward extension, once we get the hang of the interpretation. We can simply extend our linear model to also include $X_2$, $X_3$ and so forth, and now our $b_1$ is called the coefficient on $X_1$ or the partial coefficient on $X_1$.

Here’s the most difficult part.

> When we interpret each partial coefficient, the value is interpreted *holding all the other IVs constant*. So $b_1$ represents the expected change in Y when $X_1$ increases by one unit, holding constant all the other variables.

This is so important that I'll say it twice more. If you really understand this point, then you know how to do multiple regression.


>The partial regression coefficients represent the expected change in the dependent variable when the associated independent variable is increased by one unit while the values of all other independent variables are held constant. 


And a third time, in different words:


>Each coefficient $b_i$ estimates the mean change in the dependent variable ($Y$) per unit increase in $X_i$, when all other predictors are held constant.





Here's an example

$$\text{Profit} = -2000 + 2.5* \text{ExpenditureOnAdvertising} +32*\text{NumberOfProductsSold}$$

Coefficient | Intepretation:
--- | ---
(b0) | Monthly profit is -\$2000 without any money spent on advertising and with zero products sold.
(b1) | Holding the number of products sold constant, every dollar spent on advertising increases profit by \$2.50
(b2) | Keeping advertising expenditure constant, every product sold increases profit by \$32



## Standardized Coefficients



Let's take a short digression to discuss standardised coefficients. In all the examples in this Chapter, we've seen that it's very important to be clear about what the units of measurement are, as this affects how we interpret the numbers. 

For example, in $\text{Income} = b_0 + b_1 \text{YearsOfEducation}$, we can say for a 1 unit increase in $X_1$, so for one additional year of education, there is a $b_1$ unit increase in $Y$; so there is a \$$b_1$ increase in income.


Unfortunately, sometimes we may have difficulty comparing $X$'s. Perhaps I have a dataset of American students and their standardized test scores on the SATs, and a corresponding dataset of Singaporean students and their standardized test scores on the O-levels^[ignore the fact that the SAT and O-levels are at different levels]. I want to compare the datasets to predict how test scores ($X$) affect income ($Y$), but my $X$s here are on different scales. How should we compare them?

One way is by using standardised coefficients^[Confusingly, when we use standardised coefficients, it is the variables that get standardised, not the coefficients.]. 

To do so, we "standardize" each variable by subtracting its mean and dividing by its standard deviation. Then we just re-run the regression. Now, notice that I’ve replaced $b$'s with $\beta$s, and now these $\beta$s are unit-less. Or, to put it another way, they are in "standardized" units. By convention (although not everyone follows this), $b$ are used to refer to unstandardized regression coefficients while $\beta$s are used to refer to standardized regression coefficients.

$$\left[ \frac{Y-\bar{Y}}{\sigma_{Y}} \right] = \beta_0 + \beta_1 \left[ \frac{X_1 - \bar{X_1}}{\sigma_{X_1}} \right] + \beta_2 \left[ \frac{X_2 - \bar{X_2}}{\sigma_{X_2}} \right] + \ldots$$

* Note: We can choose to standardise only the IVs, or only some of the IVs. The usual convention is that all the IVs and sometimes the DV are standardised.


Now, the interpretation is:

> When $X_i$ increases by one standard deviation, there is a change in $Y$ of $\beta_i$ standard deviations

Example: if we run: $$\text{Income} = \beta_0 + \beta_1 \text{Education} + \beta_2 \text{Working Experience}$$ as a standardized regression, and we find that $\beta_1$ = 0.5, then the interpretation is:
“For every increase in education level by one standard deviation, holding working experience constant, there is an average increase in income by 0.5 standard deviations.”

With standardised coefficients, the interpretation changes, everything is now in standard deviations.



Sometimes, standardised coefficients makes it easier to interpret when the underlying unit is quite difficult to interpret, for example: IQ, the intelligence quotient, is actually a standardised variable itself such that 100 is the mean of the population and 15 is the standard deviation. It's hard to come up with units of "absolute" intelligence, and so IQ is actually measured relative to others in the population.

Coming back to the example of comparing the results in an American sample with the SAT and a Singaporean sample with the O-levels; even if I cannot compare 1 point on the SAT with 1 point on the O-levels, with standardized coefficients I can still ask: does a 1 standard deviation increase in SAT score have the same effect (on whatever $Y$), as a 1 standard deviation increase in O-level score?


Here is some R code to 'manually' standardize variables and use them in a model

```r
# unstandardized
lm(Y ~ X, df1)

# standardized
df1$X_standardized = scale(df1$X, center=TRUE, scale=TRUE)
df1$Y_standardized = scale(df1$Y, center=TRUE, scale=TRUE)

lm(Y_standardized ~ X_standardized, df1)
```


## Categorical Independent Variables

So far we have been dealing with continuous independent variables ($X$), (e.g. Expenditure, Years, Age, Numbers, ...). In this section, we consider categorical independent variables (e.g., Gender, Ethnicity, MaritalStatus, Color-Of-Search-Button, ...).

Let's consider an example modelling how Umbrella Sales depend upon Weather. 

$$\text{UmbrellaSales} = b_0 + b_1 \text{Weather}$$

These categorical variables take on one of a small set of fixed values. Let's assume in this simple world that weather is only Sunny or Rainy. 

### Dummy Coding

Dummy Coding (the default method in R) is a method by which we create and use dummy variables in our regression model^[Aside from dummy coding (R’s default), there are other coding schemes which can be used to test more specific hypothesis, e.g. effect coding, difference coding, etc. But here we shall focus on Dummy Coding.].


In this example, we can define a variable: `Rainy` that is 1 if `Weather==Rainy`, and 0 if `Weather==Sunny`.


`Rainy` is called a **dummy variable** (sometimes called an indicator variable)

We can replace `Weather` with the dummy variable `Rainy`:

$$\text{UmbrellaSales} = b_0 + b_1 \text{Weather} \; \rightarrow \; \color{brown}{\text{UmbrellaSales} = b_0 + b_1 \text{Rainy}}$$

Thus, this breaks down into two equations (technically, a piecewise equation):

- If Sunny, $\text{UmbrellaSales} = b_0 + b_1(0) = b_0$
- If Rainy, $\text{UmbrellaSales} = b_0 + b_1(1) = b_0 + b_1$


Now we can interpret the value of these coefficients. 
Looking at the first equation, we can see that $b_0$ is simply the average umbrella sales when it is sunny. And similarly, from the second equation, we see that $b_0$ **PLUS** $b_1$ is the average umbrella sales when it is rainy.


This means that $b_1$ is the **difference** between these equations: It is the average difference in umbrella sales when it is rainy, compared to when it is sunny.

The following table summarizes these interpretations:


Coefficient | Intepretation:
--- | ---
($b_0$) | Average Umbrella sales when it is Sunny
($b_0+b_1$) | Average Umbrella sales when it is Rainy
($b_1$) | Average difference in Umbrella sales when it is Rainy, **compared to** when it is Sunny <br> (Sales when Rainy - Sales when Sunny)


```
Q: Would you predict $b_1$ to be greater than 0 or less than 0?
```

### Dummy Coding with 3 levels


Let's now consider a more complicated world in which `Weather` can take one of three values: Sunny, Rainy, or Cloudy.

Then we can define two dummy variables, `Rainy` and `Cloudy`,

- `Rainy` = 1 if weather is rainy, 0 otherwise
- `Cloudy` = 1 if weather is cloudy, 0 otherwise

We say that Sunny is the **Reference Group** for the categorical variable `Weather`. 

$$\text{UmbrellaSales} = b_0 + b_1 \text{Rainy} + b_2\text{Cloudy}$$

This breaks down into three equations:

- If Sunny, $\text{UmbrellaSales} = b_0 + b_1(0) + b_2(0) = b_0$
- If Rainy, $\text{UmbrellaSales} = b_0 + b_1(1) + b_2(0) = b_0 + b_1$
- If Cloudy, $\text{UmbrellaSales} = b_0 + b_1(0) + b_2(1) = b_0 + b_2$

Just like above, we can interpret the meaning of the coefficients in the following table:

Coefficient | Intepretation:
--- | ---
($b_0$) | Average Umbrella sales when it is Sunny
($b_0+b_1$) | Average Umbrella sales when it is Rainy
($b_1$) | Average difference in Umbrella sales when it is Rainy, **compared to** when it is Sunny <br> (Sales when Rainy - Sales when Sunny)
($b_0+b_2$) | Average Umbrella sales when it is Cloudy
($b_2$) | Average difference in Umbrella sales when it is Cloudy, **compared to** when it is Sunny <br> (Sales when Cloudy - Sales when Sunny)


Thus, in general, a categorical variable with $n$ levels will have $(n-1)$ dummy variables. And the general interpretation of these $i$ dummy variables are:

Coefficient | Intepretation:
--- | ---
($b_0$) | Average Value of Y for the reference group.
($b_i$) | Average Difference in Y for Dummy Group i compared to the reference group.



### The Reference Group

Now, when we do dummy coding, one of the groups will automatically be the reference group. The choice of reference group is not fixed. And choosing your reference group well (depending on your goals) will make your analyses more convenient and interpretable.


For example, for the Umbrella Sales example (Sunny, Rainy, Cloudy), I think "Sunny" is a good reference group. Why?



Or let’s say I want to see how well people react to the color of the button on my webpage. So I run an experiment with the following four buttons^[This may seem frivolous, but Google actually ran a lot of these tests back in the day, with different shades of blue/green/red to settle on their current "Google colors".]:

- <span style="color:blue">Current Button</span>
- <span style="color:green">Button A</span>
- <span style="color:magenta">Button B</span>
- <span style="color:brown">Button C</span> 

Which should I choose to be my reference group? I think that "Current Button" should be the reference group, since that is the status quo and I am interesting in how changing the buttons would affect click-through, relative to my current button.

```
The good news: R handles dummy coding for you using factors.
You do not need to create your own dummy variables. Just run:
lm(sales ~ weather, df)
and if weather is a factor with n levels,
R will default to creating n-1 dummy variables 


The bad news: R does not know your hypotheses, so it uses a heuristic for 
choosing the reference group.
If you do not specify, R defaults to ranking the groups by alphabetical order.

Thus, in the weather example, it would choose "Cloudy", 
and in the button example, "Button A" as the reference group.

If your variable (df$var) is a factor, you can check by using
levels(df$var). 
The first level will be the reference group.

Use relevel(df$var, "desiredReferenceLevel") to adjust the reference group.

(if df$var is a character string, levels() will return NULL,
but if you put it into a lm(), R will treat it as categorical variable,
with the alphabetically smallest string as the reference group)
```


### Interpreting categorical and continuous independent variables

Whenever we have categorical independent variables in a model, interpreting the coefficients has to be done with respect to the reference group, even for other continuous independent variables.



Let's add a continuous variable to our 3-weather model.

- `UmbrellaSales` is a continuous variable measured in $.
- `Rainy` and `Cloudy` are dummy variables ("Sunny" is the reference group)
- `ExpenditureOnAdvertising` is also a continuous variable measured in $.

Let's say we fit the following model and obtain the following coefficients:

$$\text{UmbrellaSales} = 10 + 50 \text{Rainy} + 20 \text{Cloudy} + 2.5\text{ExpenditureOnAdvertising}$$

Here's how we interpret each of these coefficients:


Coefficient | Intepretation:
--- | ---
10 | Average umbrella sales when it is sunny and $0 spent on advertising.
50 | Average umbrella sales when it is rainy compared to sunny *and* $0 spent on advertising.
20 | Average umbrella sales when it is cloudy compared to sunny *and* $0 spent on advertising.
2.5 | When it is sunny, every dollar spent on advertising increases sales by $2.50








## Assumptions behind Linear Regression










Linear Regression is a powerful tool, but also makes a lot of assumptions about the data. These assumptions are necessary in order for the mathematical derivations to work out nicely (e.g., we saw the nice solution to the the Ordinary Least Squares minimization problem). Thus, we have to be mindful of these assumptions, as statistical tools become less valid when there are violations of these assumptions.

The following table summarizes some of the assumptions behind linear regression, and how we can check that these assumptions are met (or at least, not violated). Note that in some of these cases there are formal ways to check (e.g. Breusch-Pagan test for homoscedasticity), but for right now our goal is to build intuition, so we will explore graphical methods. 

Assumption | How to (informally) check
--- | ---
There exists a linear relationship between dependent variable and the independent variable | Examine X-Y scatterplot -- there should be a linear trend.
There are no outliers | Examine X-Y scatterplot and the univariate histograms for outliers
Errors are normally-distributed about zero | Examine the residual plot: Residuals should be normally distributed about 0
There is no _heteroscedasticity_ / there IS _homoscedasticity_. <br>i.e., Errors are independent of the independent variable | Examine the residual plot: Residuals should not depend on X
No or little _multicollinearity_ | Check correlation between independent variables / <br> calculate variance inflation factor in linear model
Errors are independent of each other; they are not correlated. | Check for autocorrelation between errors.




We already saw the first two assumptions in the previous few sections: that we expect there to be a linear relationship between the DV and the IV(s), without any outliers. The way to visually eyeball this is to simply plot the X-Y scatterplot, also known as a bivariate ("two-variable") scatterplot.



<img src="06-a-regression_files/figure-html/lmi-residuals-2-1.png" width="336" style="display: block; margin: auto;" /><img src="06-a-regression_files/figure-html/lmi-residuals-2-2.png" width="336" style="display: block; margin: auto;" />




### Residual plots

We saw earlier that the objective of Ordinary Least Squares regression is to find the line that minimizes the sum of the (squares of the) residuals $e_i = Y_i - \hat{Y_i}$. Below, we have the best-fit linear line to a (purposely) non-linear dataset. The residual errors are shown in red, going from the actual data points ($Y_i$'s) to the predicted line ($\hat{Y_i}$'s).


<img src="06-a-regression_files/figure-html/lmi-residuals-3-1.png" width="336" style="display: block; margin: auto;" />

Now let's instead plot the residuals $e_i$ against $X$. That is, instead of plotting $Y$ on the vertical axis, we plot the length of the red bars $e_i$ against X:

<img src="06-a-regression_files/figure-html/lmi-residuals-4-1.png" width="336" style="display: block; margin: auto;" />


Ideally, residuals should be normally distributed around 0, and not depend on X. 



<img src="06-a-regression_files/figure-html/lmi-residuals-5-1.png" width="240" style="display: block; margin: auto;" />

But we clearly see from the red dots above that it is not the case here that the residuals are normally distributed around 0. In fact, there seems to be some non-linearity in the data.


One solution might be to do a non-linear transformation to one of the variables, e.g. $\log(...)$, $exp(...)$. Let's try taking the logarithm of the dependent variable, e.g. $\log(Y) \sim X$

> One thing to note here is that if you just write `log()` in R you’ll get the natural logarithm (log base e); you’ll need to specify `log10()` if you want a base10 logarithm. They both achieve the same thing in terms of the purpose of the transformation here. The main difference is in interpretability; if your variable is something like GDP, then a base10 log will be more interpretable, because a base10 log of 3 is a thousand, 4 is ten thousand, and so forth.


<img src="06-a-regression_files/figure-html/lmi-residuals-6-1.png" width="336" style="display: block; margin: auto;" /><img src="06-a-regression_files/figure-html/lmi-residuals-6-2.png" width="336" style="display: block; margin: auto;" />


The residuals of the transformed plot look a lot better. But actually here we see a slightly different problem: notice that the errors seem to be larger for smaller values of $X$:
This is called _heteroscedasticity_, which by definition is when the residual error depends on the value of the independent variable. Having heteroscedasticity often implies that your model is missing some variables that can explain this dependence.

Linear regression often assumes no or little heteroscedasticity; in other words, linear regression assumes homoscedasticity. While there are formal tests for heteroscedasticity, for now we leave it as something that we might be able to eyeball from the data.


In summary, residual plots should look distributed with no discernable pattern, with respect to the independent variables (first graph below). There are many possibilities, such as having non-normal distributions (second graph below) or heteroscedastic errors (third graph below).

<img src="06-a-regression_files/figure-html/lmi-residuals-7-1.png" width="336" style="display: block; margin: auto;" /><img src="06-a-regression_files/figure-html/lmi-residuals-7-2.png" width="336" style="display: block; margin: auto;" /><img src="06-a-regression_files/figure-html/lmi-residuals-7-3.png" width="336" style="display: block; margin: auto;" />


If you run a linear model, you can easily pull the residuals using `residuals(lm_object)`. For example, let's take the `mtcars` example predicting fuel economy (`mpg`) using horsepower (`hp`).



```r
# Use mtcars, run simple lm()
data(mtcars)
fit1 <- lm(mpg ~ hp, mtcars)
mtcars$predicted <- predict(fit1) # get predictions if needed
mtcars$residuals <- residuals(fit1) # get residuals

# plotting DV against IV
ggplot(mtcars, aes(x=hp, y=mpg)) + geom_point(color="blue") + 
  geom_smooth(method="lm", se=FALSE) + theme_bw()
```

<img src="06-a-regression_files/figure-html/lmi-residuals-8-1.png" width="336" style="display: block; margin: auto;" />

```r
# plotting the residuals against IV
ggplot(mtcars, aes(x=hp, y=residuals)) + geom_point(color="red") + 
  geom_hline(yintercept=0, linetype="dashed") + theme_bw()
```

<img src="06-a-regression_files/figure-html/lmi-residuals-8-2.png" width="336" style="display: block; margin: auto;" />


We see what looks like maybe one outlier right on the right edge of the graph, but aside from that the residuals look fine.





## Exercises: Linear Model I

1a) For this simple linear regression,
$$ \text{Income} = b_0 + b_1 \text{Years of Education} $$
We find $b_0$=-4500, $b_1$=500 (in units of \$/year). What does this mean? [Negative income?!]


1b) 
For this multiple linear regression,
$$\text{Income} = b_0 + b_1 \text{Years of Education} + b_2 \text{Years of Experience}$$
We find $b_0$=-3700, $b_1$=450, $b_2$=350 (in $/year). What do these mean?


1c) 
For this simple linear regression,
$$\text{Income} = b_0 + b_1 \text{Gender}$$
Let’s say Gender = 1 if Female, 0 if Male, and 
we find that $b_0$=3500, $b_1$=5 (in \$). What does this mean?








