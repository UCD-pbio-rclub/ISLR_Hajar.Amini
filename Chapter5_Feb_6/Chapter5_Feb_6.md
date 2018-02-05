# Chapter5_Feb_6


#5.3.1


```r
library (ISLR)
```

```
## Warning: package 'ISLR' was built under R version 3.4.2
```

```r
set.seed (1)
train=sample (392 ,196) #training set, subset 196 samples out of originoal 392 observations.1
#?sample #Random Samples and Permutations
```


```r
lm.fit <- lm(mpg~horsepower, data = Auto, subset = train)
attach (Auto)
mean((mpg -predict (lm.fit ,Auto))[-train ]^2) # mean() calculate the MSE of the 196 in the validation, -train means all observations that are not in the training set
```

```
## [1] 26.14142
```

```r
#MSE for the linear regression is 26.14 
```

#poly() function to estimate the test error for the quadratic and cubic regressions.

```r
lm.fit2=lm(mpg~poly(horsepower ,2) ,data=Auto ,subset =train )
mean((mpg -predict (lm.fit2 ,Auto))[-train ]^2)
```

```
## [1] 19.82259
```

```r
#19.82
lm.fit3=lm(mpg~poly(horsepower ,3) ,data=Auto ,subset =train )
mean((mpg -predict (lm.fit3 ,Auto))[-train ]^2)
```

```
## [1] 19.78252
```

```r
#19.78
```

#If we choose a different training set instead, then we will obtain somewhat different errors on the validation set.


```r
set.seed (2)
train=sample (392 ,196)
lm.fit =lm(mpg~horsepower ,subset =train)
mean((mpg -predict (lm.fit ,Auto))[-train ]^2)
```

```
## [1] 23.29559
```

```r
#23.30
lm.fit2=lm(mpg~poly(horsepower ,2) ,data=Auto ,subset =train )
mean((mpg -predict (lm.fit2 ,Auto))[-train ]^2)
```

```
## [1] 18.90124
```

```r
#18.90
lm.fit3=lm(mpg~poly(horsepower ,3) ,data=Auto ,subset =train )
mean((mpg -predict (lm.fit3 ,Auto))[-train ]^2)
```

```
## [1] 19.2574
```

```r
#19.26
```

#5.3.2


```r
#The LOOCV estimate can be automatically computed for any generalized linear model using the glm() and cv.glm() functions.
glm.fit=glm(mpg~horsepower ,data=Auto)
coef(glm.fit)
```

```
## (Intercept)  horsepower 
##  39.9358610  -0.1578447
```

```r
#(Intercept ) horsepower
#39.936 -0.158

lm.fit =lm(mpg~horsepower ,data=Auto)
coef(lm.fit)
```

```
## (Intercept)  horsepower 
##  39.9358610  -0.1578447
```

```r
#(Intercept ) horsepower
#39.936 -0.158
#we will perform linear regression using the glm() function rather than the lm() function because the former can be used together with cv.glm(). 
```


```r
library (boot) #The cv.glm() function is part of the boot library.
glm.fit=glm(mpg~horsepower ,data=Auto)
cv.err =cv.glm(Auto ,glm.fit) #This function calculates the estimated K-fold cross-validation prediction error for generalized linear models.
cv.err$delta
```

```
## [1] 24.23151 24.23114
```

```r
#1 1
#24.23 24.23
#The cv.glm() function produces a list with several components. The two numbers in the delta vector contain the cross-validation results.
```


```r
cv.error=rep (0,5)
for (i in 1:5){
glm.fit=glm(mpg~poly(horsepower ,i),data=Auto)
cv.error[i]=cv.glm (Auto ,glm.fit)$delta [1]
}
cv.error
```

```
## [1] 24.23151 19.24821 19.33498 19.42443 19.03321
```

```r
#[1] 24.23 19.25 19.33 19.42 19.03
```

#5.3.3 k-Fold Cross-Validation


```r
set.seed (17)
cv.error.10= rep (0 ,10)
for (i in 1:10) {
glm.fit=glm(mpg~poly(horsepower ,i),data=Auto)
cv.error.10[i]=cv.glm (Auto ,glm.fit ,K=10) $delta [1]
}
cv.error.10
```

```
##  [1] 24.20520 19.18924 19.30662 19.33799 18.87911 19.02103 18.89609
##  [8] 19.71201 18.95140 19.50196
```

```r
#[1] 24.21 19.19 19.31 19.34 18.88 19.02 18.90 19.71 18.95 19.50
```


