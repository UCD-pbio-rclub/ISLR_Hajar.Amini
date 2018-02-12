# Chapter5_Feb_13
page 201



#5.3.4 The Bootstrap


```r
library (ISLR)
```

```
## Warning: package 'ISLR' was built under R version 3.4.2
```

```r
alpha.fn=function (data ,index){
X=data$X [index]
Y=data$Y [index]
return ((var(Y)-cov (X,Y))/(var(X)+var(Y) -2* cov(X,Y)))
}
```


```r
alpha.fn(Portfolio ,1:100)
```

```
## [1] 0.5758321
```

```r
# 0.576
set.seed (1)
alpha.fn(Portfolio ,sample (100 ,100 , replace =T)) #The command uses the sample() function to randomly select 100 observations from the range 1 to 100, with replacement
```

```
## [1] 0.5963833
```

```r
# 0.596
```


```r
library (boot)
boot(data = Portfolio , statistic = alpha.fn, R = 1000)
```

```
## 
## ORDINARY NONPARAMETRIC BOOTSTRAP
## 
## 
## Call:
## boot(data = Portfolio, statistic = alpha.fn, R = 1000)
## 
## 
## Bootstrap Statistics :
##      original        bias    std. error
## t1* 0.5758321 -7.315422e-05  0.08861826
```


```r
boot.fn=function (data ,index ) # to see the intercept and slope
+ return (coef(lm(mpg~horsepower ,data=data ,subset =index)))
boot.fn(Auto ,1:392)
```

```
## (Intercept)  horsepower 
##  39.9358610  -0.1578447
```


```r
set.seed (1)
boot.fn(Auto ,sample (392 ,392 , replace =T))
```

```
## (Intercept)  horsepower 
##  38.7387134  -0.1481952
```

```r
boot.fn(Auto ,sample (392 ,392 , replace =T))
```

```
## (Intercept)  horsepower 
##  40.0383086  -0.1596104
```


```r
boot(Auto ,boot.fn ,1000)
```

```
## 
## ORDINARY NONPARAMETRIC BOOTSTRAP
## 
## 
## Call:
## boot(data = Auto, statistic = boot.fn, R = 1000)
## 
## 
## Bootstrap Statistics :
##       original      bias    std. error
## t1* 39.9358610  0.02972191 0.860007896
## t2* -0.1578447 -0.00030823 0.007404467
```

```r
summary (lm(mpg~horsepower ,data=Auto))$coef
```

```
##               Estimate  Std. Error   t value      Pr(>|t|)
## (Intercept) 39.9358610 0.717498656  55.65984 1.220362e-187
## horsepower  -0.1578447 0.006445501 -24.48914  7.031989e-81
```


```r
boot.fn=function (data ,index)
+ coefficients(lm(mpg~horsepower +I(horsepower ^2) ,data=data ,
subset =index))
set.seed (1)
boot(Auto ,boot.fn ,1000)
```

```
## 
## ORDINARY NONPARAMETRIC BOOTSTRAP
## 
## 
## Call:
## boot(data = Auto, statistic = boot.fn, R = 1000)
## 
## 
## Bootstrap Statistics :
##         original        bias     std. error
## t1* 56.900099702  6.098115e-03 2.0944855842
## t2* -0.466189630 -1.777108e-04 0.0334123802
## t3*  0.001230536  1.324315e-06 0.0001208339
```

```r
boot(data = Auto , statistic = boot.fn, R = 1000)
```

```
## 
## ORDINARY NONPARAMETRIC BOOTSTRAP
## 
## 
## Call:
## boot(data = Auto, statistic = boot.fn, R = 1000)
## 
## 
## Bootstrap Statistics :
##         original        bias     std. error
## t1* 56.900099702 -6.863131e-02 2.1133492036
## t2* -0.466189630  9.942586e-04 0.0336689369
## t3*  0.001230536 -3.381623e-06 0.0001218251
```

```r
summary (lm(mpg~horsepower +I(horsepower ^2) ,data=Auto))$coef
```

```
##                     Estimate   Std. Error   t value      Pr(>|t|)
## (Intercept)     56.900099702 1.8004268063  31.60367 1.740911e-109
## horsepower      -0.466189630 0.0311246171 -14.97816  2.289429e-40
## I(horsepower^2)  0.001230536 0.0001220759  10.08009  2.196340e-21
```

#Probs

4. Suppose that we use some statistical learning method to make a prediction
for the response Y for a particular value of the predictor X.
Carefully describe how we might estimate the standard deviation of
our prediction.

For making the method to predict the response Y for a particular value of the predictor X we could estimate the standard deviation of our prediction by using the bootstrap approach. The bootstrap method acts by repeatedly sampling observations (with replacement) from the original data set n times, for some large value of n, each time fitting a new model and subsequently obtaining the RMSE of the estimates for all n models.

6. We continue to consider the use of a logistic regression model to
predict the probability of default using income and balance on the
Default data set. In particular, we will now compute estimates for
the standard errors of the income and balance logistic regression coefficients
in two different ways: (1) using the bootstrap, and (2) using
the standard formula for computing the standard errors in the glm()
function. Do not forget to set a random seed before beginning your
analysis.

(a) Using the summary() and glm() functions, determine the estimated
standard errors for the coefficients associated with income
and balance in a multiple logistic regression model that uses
both predictors.


```r
library(ISLR)
summary(Default)
```

```
##  default    student       balance           income     
##  No :9667   No :7056   Min.   :   0.0   Min.   :  772  
##  Yes: 333   Yes:2944   1st Qu.: 481.7   1st Qu.:21340  
##                        Median : 823.6   Median :34553  
##                        Mean   : 835.4   Mean   :33517  
##                        3rd Qu.:1166.3   3rd Qu.:43808  
##                        Max.   :2654.3   Max.   :73554
```

```r
attach(Default)
set.seed(1)
glm.fit = glm(default~income+balance, data=Default, family=binomial)
summary(glm.fit)
```

```
## 
## Call:
## glm(formula = default ~ income + balance, family = binomial, 
##     data = Default)
## 
## Deviance Residuals: 
##     Min       1Q   Median       3Q      Max  
## -2.4725  -0.1444  -0.0574  -0.0211   3.7245  
## 
## Coefficients:
##               Estimate Std. Error z value Pr(>|z|)    
## (Intercept) -1.154e+01  4.348e-01 -26.545  < 2e-16 ***
## income       2.081e-05  4.985e-06   4.174 2.99e-05 ***
## balance      5.647e-03  2.274e-04  24.836  < 2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## (Dispersion parameter for binomial family taken to be 1)
## 
##     Null deviance: 2920.6  on 9999  degrees of freedom
## Residual deviance: 1579.0  on 9997  degrees of freedom
## AIC: 1585
## 
## Number of Fisher Scoring iterations: 8
```

(b) Write a function, boot.fn(), that takes as input the Default data
set as well as an index of the observations, and that outputs
the coefficient estimates for income and balance in the multiple
logistic regression model.


```r
boot.fn = function(data, index)
  return(coef(glm(default~income+balance, data=data, family=binomial,
                  subset=index)))
```

(c) Use the boot() function together with your boot.fn() function to
estimate the standard errors of the logistic regression coefficients
for income and balance.


```r
library(boot)
boot(Default, boot.fn, 50)
```

```
## 
## ORDINARY NONPARAMETRIC BOOTSTRAP
## 
## 
## Call:
## boot(data = Default, statistic = boot.fn, R = 50)
## 
## 
## Bootstrap Statistics :
##          original        bias     std. error
## t1* -1.154047e+01  1.181200e-01 4.202402e-01
## t2*  2.080898e-05 -5.466926e-08 4.542214e-06
## t3*  5.647103e-03 -6.974834e-05 2.282819e-04
```

(d) Comment on the estimated standard errors obtained using the
glm() function and using your bootstrap function.
Similar answers to the second and third significant digits.

9. We will now consider the Boston housing data set, from the MASS
library.

(a) Based on this data set, provide an estimate for the population
mean of medv. Call this estimate ˆμ.


```r
library(MASS)
summary(Boston)
```

```
##       crim                zn             indus            chas        
##  Min.   : 0.00632   Min.   :  0.00   Min.   : 0.46   Min.   :0.00000  
##  1st Qu.: 0.08204   1st Qu.:  0.00   1st Qu.: 5.19   1st Qu.:0.00000  
##  Median : 0.25651   Median :  0.00   Median : 9.69   Median :0.00000  
##  Mean   : 3.61352   Mean   : 11.36   Mean   :11.14   Mean   :0.06917  
##  3rd Qu.: 3.67708   3rd Qu.: 12.50   3rd Qu.:18.10   3rd Qu.:0.00000  
##  Max.   :88.97620   Max.   :100.00   Max.   :27.74   Max.   :1.00000  
##       nox               rm             age              dis        
##  Min.   :0.3850   Min.   :3.561   Min.   :  2.90   Min.   : 1.130  
##  1st Qu.:0.4490   1st Qu.:5.886   1st Qu.: 45.02   1st Qu.: 2.100  
##  Median :0.5380   Median :6.208   Median : 77.50   Median : 3.207  
##  Mean   :0.5547   Mean   :6.285   Mean   : 68.57   Mean   : 3.795  
##  3rd Qu.:0.6240   3rd Qu.:6.623   3rd Qu.: 94.08   3rd Qu.: 5.188  
##  Max.   :0.8710   Max.   :8.780   Max.   :100.00   Max.   :12.127  
##       rad              tax           ptratio          black       
##  Min.   : 1.000   Min.   :187.0   Min.   :12.60   Min.   :  0.32  
##  1st Qu.: 4.000   1st Qu.:279.0   1st Qu.:17.40   1st Qu.:375.38  
##  Median : 5.000   Median :330.0   Median :19.05   Median :391.44  
##  Mean   : 9.549   Mean   :408.2   Mean   :18.46   Mean   :356.67  
##  3rd Qu.:24.000   3rd Qu.:666.0   3rd Qu.:20.20   3rd Qu.:396.23  
##  Max.   :24.000   Max.   :711.0   Max.   :22.00   Max.   :396.90  
##      lstat            medv      
##  Min.   : 1.73   Min.   : 5.00  
##  1st Qu.: 6.95   1st Qu.:17.02  
##  Median :11.36   Median :21.20  
##  Mean   :12.65   Mean   :22.53  
##  3rd Qu.:16.95   3rd Qu.:25.00  
##  Max.   :37.97   Max.   :50.00
```

```r
set.seed(1)
attach(Boston)
medv_mean = mean(medv)
medv_mean
```

```
## [1] 22.53281
```

(b) Provide an estimate of the standard error of ˆμ. Interpret this
result.
Hint: We can compute the standard error of the sample mean by
dividing the sample standard deviation by the square root of the
number of observation


```r
medv_err = sd(medv) / sqrt(length(medv))
medv_err
```

```
## [1] 0.4088611
```

```r
#0.40
```

(c) Now estimate the standard error of ˆμ using the bootstrap. How
does this compare to your answer from (b)?


```r
boot.fn = function(data, index) return(mean(data[index]))
library(boot)
bootstrap = boot(medv, boot.fn, 1000)
bootstrap
```

```
## 
## ORDINARY NONPARAMETRIC BOOTSTRAP
## 
## 
## Call:
## boot(data = medv, statistic = boot.fn, R = 1000)
## 
## 
## Bootstrap Statistics :
##     original      bias    std. error
## t1* 22.53281 0.008517589   0.4119374
```

```r
#0.4119374
#Similar to answer from (b)(0.4119 vs 0.4089)
```

(d) Based on your bootstrap estimate from (c), provide a 95% confidence
interval for the mean of medv. Compare it to the results
obtained using t.test(Boston$medv).
Hint: You can approximate a 95% confidence interval using the
formula [ˆμ − 2SE(ˆμ), ˆμ + 2SE(ˆμ)].


```r
t.test(medv)
```

```
## 
## 	One Sample t-test
## 
## data:  medv
## t = 55.111, df = 505, p-value < 2.2e-16
## alternative hypothesis: true mean is not equal to 0
## 95 percent confidence interval:
##  21.72953 23.33608
## sample estimates:
## mean of x 
##  22.53281
```

```r
#21.72953 23.33608
c(bootstrap$t0 - 2*0.4119, bootstrap$t0 + 2*0.4119)
```

```
## [1] 21.70901 23.35661
```

```r
#[1] 21.70901 23.35661
#Bootstrap estimate only 0.02 differs from t.test estimate.
```

(e) Based on this data set, provide an estimate, ˆμmed, for the median
value of medv in the population.
Bootstrap estimate only 0.02 away for t.test estimate.

e
medv.med = median(medv)
medv.med

```r
median(medv)
```

```
## [1] 21.2
```

```r
#21.2
```

(f) We now would like to estimate the standard error of ˆμmed. Unfortunately,
there is no simple formula for computing the standard
error of the median. Instead, estimate the standard error of the
median using the bootstrap. Comment on your findings.


```r
boot.fn = function(data, index) return(median(data[index]))
boot(medv, boot.fn, 1000)
```

```
## 
## ORDINARY NONPARAMETRIC BOOTSTRAP
## 
## 
## Call:
## boot(data = medv, statistic = boot.fn, R = 1000)
## 
## 
## Bootstrap Statistics :
##     original  bias    std. error
## t1*     21.2 -0.0098   0.3874004
```

```r
#t1* std.error 0.374358
#Median of 21.2 with SE of 0.380. Small standard.
```

(g) Based on this data set, provide an estimate for the tenth percentile
of medv in Boston suburbs. Call this quantity ˆμ0.1. (You
can use the quantile() function.)


```r
medv_tenth = quantile(medv, c(0.1))
medv_tenth
```

```
##   10% 
## 12.75
```

```r
#  10% 
#12.75 
```

(h) Use the bootstrap to estimate the standard error of ˆμ0.1. Comment
on your findings.


```r
boot.fn = function(data, index) return(quantile(data[index], c(0.1)))
boot(medv, boot.fn, 1000)
```

```
## 
## ORDINARY NONPARAMETRIC BOOTSTRAP
## 
## 
## Call:
## boot(data = medv, statistic = boot.fn, R = 1000)
## 
## 
## Bootstrap Statistics :
##     original  bias    std. error
## t1*    12.75 0.00515   0.5113487
```

```r
#std.error 0.4912231
#Tenth-percentile of 12.75 (original) with std.error of 0.49. Small standard error.
```

