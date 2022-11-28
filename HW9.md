HW 9
================

For this homework, recall the Garmin dataset from the midterm exam.

``` r
Garmin <- read_csv("https://raw.githubusercontent.com/STAT505/2022midterm/main/Garmin_clean.csv") 
```

    ## Rows: 797 Columns: 5
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (2): slope, walk
    ## dbl (3): heart_rate, speed_past_60_seconds, avg_temp
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

#### 1. (4 points)

Use the following model and conduct a posterior predictive check of your
model / prior.

``` r
bayes_1 <- stan_glm(heart_rate ~ speed_past_60_seconds * walk, data = Garmin)
```

Create a series of figures and comment on the differences between your
predicted datasets and the data.

#### 2. (4 points)

Using `AIC()`, and `lm()`, compare the following models:

-   `heart_rate ~ speed_past_60_seconds * walk`
-   `heart_rate ~ speed_past_60_seconds + walk`
-   `heart_rate ~ speed_past_60_seconds`
-   `heart_rate ~ walk`

Which model do you prefer and why?

#### 3. (4 points)

Using `loo()`, and `stan_glm()`, compare the following models:

-   `heart_rate ~ speed_past_60_seconds * walk`
-   `heart_rate ~ speed_past_60_seconds + walk`
-   `heart_rate ~ speed_past_60_seconds`
-   `heart_rate ~ walk`

Which model do you prefer and why?

#### 4. (4 points)

Critique the figure below and create a better way to illustrate how
heart rate can be used to predict whether a runner was walking or
running.

``` r
Garmin_binary <- Garmin %>% mutate(walked = as.numeric(walk == "walk"), 
                                   heart_rate_diff150 = heart_rate - 150)

Garmin_binary %>% ggplot(aes(y = walked, x = heart_rate)) +
  geom_smooth(method ='loess', formula = 'y~x', color = 'red') +
  geom_smooth(method ='lm', formula = 'y~x', color = 'purple') + geom_point(alpha = .2)
```

![](HW9_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

#### 5. (4 points)

Summarize the model output below - feel free to choose whether you
summarize the `glm` or `stan_glm` output.

``` r
Garmin_binary %>% stan_glm(walked ~ heart_rate_diff150, family = 'binomial', data = ., refresh = 0) %>% print(digits = 2)
```

    ## stan_glm
    ##  family:       binomial [logit]
    ##  formula:      walked ~ heart_rate_diff150
    ##  observations: 797
    ##  predictors:   2
    ## ------
    ##                    Median MAD_SD
    ## (Intercept)        -3.02   0.19 
    ## heart_rate_diff150 -0.08   0.01 
    ## 
    ## ------
    ## * For help interpreting the printed output see ?print.stanreg
    ## * For info on the priors used see ?prior_summary.stanreg

``` r
Garmin_binary %>% glm(walked ~ heart_rate_diff150, family = 'binomial', data = .) %>% summary()
```

    ## 
    ## Call:
    ## glm(formula = walked ~ heart_rate_diff150, family = "binomial", 
    ##     data = .)
    ## 
    ## Deviance Residuals: 
    ##     Min       1Q   Median       3Q      Max  
    ## -1.1977  -0.4338  -0.2963  -0.1859   3.1876  
    ## 
    ## Coefficients:
    ##                     Estimate Std. Error z value Pr(>|z|)    
    ## (Intercept)        -3.025421   0.189765 -15.943   <2e-16 ***
    ## heart_rate_diff150 -0.078793   0.009275  -8.495   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## (Dispersion parameter for binomial family taken to be 1)
    ## 
    ##     Null deviance: 497.21  on 796  degrees of freedom
    ## Residual deviance: 395.62  on 795  degrees of freedom
    ## AIC: 399.62
    ## 
    ## Number of Fisher Scoring iterations: 6
