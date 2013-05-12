---
layout: post
title: "ARIMA models in a Nutshell"
description: ""
category: R
tags: [R, time-series, statistics]
---
{% include JB/setup %}


This post is a how-to guide to apply ARIMA models to time series. I'll use it as a quick reference/reminder for myself and hope somebody out there find it useful too.

if you are looking for a thorough manual I recomend this online [book](http://a-little-book-of-r-for-time-series.readthedocs.org/en/latest/src/timeseries.html#forecasting-using-an-arima-model)

The data I am using for this post (**Age of Death of Successive Kings of England**) has been made available by Rob Hyndman in his Time Series Data Library at http://robjhyndman.com/TSDL/.


Data Preparation
-

* Get your data into a **ts** object first


{% highlight r %}
kings <- scan("http://robjhyndman.com/tsdldata/misc/kings.dat", skip = 3)
# Convert kings data set to ts object
kingsts = ts(kings)

kingsts
{% endhighlight %}



{% highlight text %}
## Time Series:
## Start = 1 
## End = 42 
## Frequency = 1 
##  [1] 60 43 67 50 56 42 50 65 68 43 65 34 47 34 49 41 13 35 53 56 16 43 69
## [24] 59 48 59 86 55 68 51 33 49 67 77 81 67 71 81 68 70 77 56
{% endhighlight %}


Once data is in the right format then we start our analysis.

####  1 Plot data and examine. Do a visual inspection to determine if your series is non-stationary.



{% highlight r %}
plot.ts(kingsts)
{% endhighlight %}

![center](/figs/2013-05-12-arima-models-in-a-nutshell/tsPlot.png) 



#### 2 Examine Autocorrelation Function (ACF) for stationarity.  The ACF for a non-stationary series will show large autocorrelations that diminish only very  slowly at large lags.

> Note: Don't even bother looking at the Partial Autocorrelation Function if the time series is non-stationary.


{% highlight r %}
acf(kingsts, lag.max = 20)
{% endhighlight %}

![center](/figs/2013-05-12-arima-models-in-a-nutshell/ACF.png) 


At first glance we could say it is  non-stationary but let's run a unit-root test.

In Kwiatkowski-Phillips-Schmidt-Shin (KPSS) test, the null hypothesis is that the data is stationary. In this case small p-values (less than 0.05) suggest that differencing is required.


{% highlight r %}
library(tseries)
{% endhighlight %}



{% highlight text %}
## 'tseries' version: 0.10-30
## 
## 'tseries' is a package for time series analysis and computational finance.
## 
## See 'library(help="tseries")' for details.
{% endhighlight %}



{% highlight r %}
kpss.test(kingsts)
{% endhighlight %}



{% highlight text %}
## 
##  KPSS Test for Level Stationarity
## 
## data:  kingsts 
## KPSS Level = 0.6906, Truncation lag parameter = 1, p-value =
## 0.0144
{% endhighlight %}


With a KPSS p-value of 0.0144 we reject the null hypothesis of stationarity.

#### 3 If not stationary, take first differences

> **Note**: If variance appears non-constant, take logarithm before first differencing


{% highlight r %}
kings1diff = diff(kingsts, differences = 1)
plot.ts(kings1diff)
{% endhighlight %}

![center](/figs/2013-05-12-arima-models-in-a-nutshell/firstDiff1.png) 

{% highlight r %}
acf(kings1diff, lag.max = 20)
{% endhighlight %}

![center](/figs/2013-05-12-arima-models-in-a-nutshell/firstDiff2.png) 

{% highlight r %}
kpss.test(kings1diff)
{% endhighlight %}



{% highlight text %}
## Warning: p-value greater than printed p-value
{% endhighlight %}



{% highlight text %}
## 
##  KPSS Test for Level Stationarity
## 
## data:  kings1diff 
## KPSS Level = 0.0347, Truncation lag parameter = 1, p-value = 0.1
{% endhighlight %}


Now both the ACF plot and the KPSS test (p-value = 0.1) suggest that the time series is stationary.

Before going into the Model Identification and Estimation it is advisable to check for white noise on your stationary series, because if your series is white noise there is nothing to model and there is no point in carrying out any estimation or forecasting.

For this we can carry out a Ljung-Box test. This can be done in R using the “Box.test()”, function.


{% highlight r %}
Box.test(kings1diff, lag = 20, type = "Ljung-Box")
{% endhighlight %}



{% highlight text %}
## 
##  Box-Ljung test
## 
## data:  kings1diff 
## X-squared = 25.1, df = 20, p-value = 0.1975
{% endhighlight %}


Here the Ljung-Box test statistic  is X-squared = 25.1,(p-values shown for the Ljung-Box statistic plot are incorrect see [here](http://stats.stackexchange.com/questions/6455/how-many-lags-to-use-in-the-ljung-box-test-of-a-time-series)) so we need to compare the observed X-aquared versus a X-squared with  20 d.f. and  critical value at the 0.05.


{% highlight r %}
# Chi-squared 20 d.f. and critical value at the 0.05
qchisq(0.05, 20, lower.tail = F)
{% endhighlight %}



{% highlight text %}
## [1] 31.41
{% endhighlight %}


 Observed Chi-squared 25.1 < 31.41 so there is no evidence of non-zero autocorrelations (white noise) at lags 1-20.

Model Identification and Estimation
-
#### 1. Examine the ACF and PACF

Examine the Autocorrelation Function and Partial Autocorrelation Function of your stationary series to understand what ARIMA(p,d,q) models to estimate.  The **d** in ARIMA stands for the number of times the data have been differenced to become to stationary.  In this case d=1.

The **p** in ARIMA(p,d,q) measures the order of the autoregressive component. To get an idea of what orders to consider, examine the partial autocorrelation function (PACF).  If the time-series has an autoregressive order of 1, called AR(1), then we should see only the first partial autocorrelation coefficient as significant. If it has an AR(2), then we should see only the first and second partial autocorrelation coefficients as significant. (Note, that they could be positive and/or negative; what matters is the statistical significance.)  Generally, the partial autocorrelation function PACF will have significant correlations up to lag p, and will quickly drop to near zero values after lag p.


{% highlight r %}
pacf(kings1diff, lag.max = 20)
{% endhighlight %}

![center](/figs/2013-05-12-arima-models-in-a-nutshell/pacf.png) 


The above PACF plot suggests an AR(2) or AR(3) could be adecuated in this case.

The **q** measures the order of the moving average component.  To get an idea of what orders to consider, we examine the ACF.  If the time series is a moving average of order 1, called a MA(1), we should see only one significant autocorrelation coefficient at lag 1. This is because a MA(1) process has a memory of only one period.  If the time series is a MA(2), we should see only two significant autocorrelation coefficients, at lag 1 and 2, because a MA(2) process has a memory of only two periods.  Generally, for a time series that is a MA(q), the autocorrelation function will have significant correlations up to lag q, and will quickly drop to near zero values after lag q.


{% highlight r %}
acf(kings1diff, lag.max = 20)
{% endhighlight %}

![center](/figs/2013-05-12-arima-models-in-a-nutshell/acf.png) 


The ACF suggests a MA(1) as only lag 1 exceeds the significance bounds.

We have identified p,d,q so our potential models can be:

* ARIMA(3,1,0) since the PACF is zero after lag 3
* ARIMA(0,1,1) since the ACF is zero after lag 1
* ARIMA(3,1,1) a combined model of previous models.

From the point of view of parsimony we should pick the model with the least number of paramater in this case the ARIMA(0,1,1) but personally I'd like to try all models and select the model with the lower AIC (AIC penalizes the number of paramaters in the model).



{% highlight r %}
arima(kingsts, order = c(3, 1, 0))  # ARIMA(3,1,0)
{% endhighlight %}



{% highlight text %}
## 
## Call:
## arima(x = kingsts, order = c(3, 1, 0))
## 
## Coefficients:
##          ar1     ar2     ar3
##       -0.606  -0.490  -0.328
## s.e.   0.149   0.155   0.148
## 
## sigma^2 estimated as 222:  log likelihood = -169.3,  aic = 346.6
{% endhighlight %}



{% highlight r %}
arima(kingsts, order = c(0, 1, 1))  # ARIMA(0,1,1)
{% endhighlight %}



{% highlight text %}
## 
## Call:
## arima(x = kingsts, order = c(0, 1, 1))
## 
## Coefficients:
##          ma1
##       -0.722
## s.e.   0.121
## 
## sigma^2 estimated as 230:  log likelihood = -170.1,  aic = 344.1
{% endhighlight %}



{% highlight r %}
arima(kingsts, order = c(3, 1, 1))  # ARIMA(3,1,1)
{% endhighlight %}



{% highlight text %}
## 
## Call:
## arima(x = kingsts, order = c(3, 1, 1))
## 
## Coefficients:
##          ar1     ar2     ar3     ma1
##       -0.581  -0.478  -0.320  -0.029
## s.e.   0.465   0.267   0.214   0.499
## 
## sigma^2 estimated as 222:  log likelihood = -169.3,  aic = 348.6
{% endhighlight %}


Results are:

Model | AIC
----- | ----
ARIMA(3,1,0) | 346.1 
ARIMA(0,1,1) | **344.1**
ARIMA(3,1,1) | 348.6

In this case ARIMA(0,1,1) seems a good candidate for our **Age of Death of Successive Kings of England** time series.

Forecasting
=

> Note: This part of the post is literally a copy & paste from Avril Coghlan's online book (with minor changes) I mentioned at the begining of this post. 

Let's use our ARIMA(0,1,1) to make forecasts for future values using the **forecast.Arima()** function in the “forecast” R package. 



{% highlight r %}
library(forecast)
{% endhighlight %}



{% highlight text %}
## This is forecast 4.03
{% endhighlight %}



{% highlight r %}
arimaKings = arima(kingsts, order = c(0, 1, 1))  # ARIMA(0,1,1)
kingForecast = forecast.Arima(arimaKings, h = 5)  # predict the next 5 kings
kingForecast
{% endhighlight %}



{% highlight text %}
##    Point Forecast Lo 80 Hi 80 Lo 95  Hi 95
## 43          67.75 48.30 87.20 38.00  97.50
## 44          67.75 47.56 87.94 36.87  98.63
## 45          67.75 46.84 88.66 35.78  99.72
## 46          67.75 46.16 89.35 34.72 100.78
## 47          67.75 45.49 90.01 33.70 101.80
{% endhighlight %}



{% highlight r %}
plot.forecast(kingForecast)
{% endhighlight %}

![center](/figs/2013-05-12-arima-models-in-a-nutshell/forecast.png) 


The ARIMA model gives the forecasted age at death of the next five kings as 67.8 years.

It is a good idea to investigate whether the forecast errors of an ARIMA model are normally distributed with mean zero and constant variance, and whether the are correlations between successive forecast errors.


{% highlight r %}
acf(kingForecast$residuals, lag.max = 20)
{% endhighlight %}

![center](/figs/2013-05-12-arima-models-in-a-nutshell/resd.png) 

{% highlight r %}
Box.test(kingForecast$residuals, lag = 20, type = "Ljung-Box")
{% endhighlight %}



{% highlight text %}
## 
##  Box-Ljung test
## 
## data:  kingForecast$residuals 
## X-squared = 13.58, df = 20, p-value = 0.8509
{% endhighlight %}


Since the correlogram shows that none of the sample autocorrelations for lags 1-20 exceed the significance bounds, and the X-squared for the Ljung-Box test is 13.58, we can conclude that there is very little evidence for non-zero autocorrelations in the forecast errors at lags 1-20.

To investigate whether the forecast errors are normally distributed with mean zero and constant variance, we can make a time plot and histogram (with overlaid normal curve) of the forecast errors:


{% highlight r %}
# Avril Coghlan's function
plotForecastErrors <- function(forecasterrors) {
    # make a histogram of the forecast errors:
    mybinsize <- IQR(forecasterrors)/4
    mysd <- sd(forecasterrors)
    mymin <- min(forecasterrors) - mysd * 5
    mymax <- max(forecasterrors) + mysd * 3
    # generate normally distributed data with mean 0 and standard deviation
    # mysd
    mynorm <- rnorm(10000, mean = 0, sd = mysd)
    mymin2 <- min(mynorm)
    mymax2 <- max(mynorm)
    if (mymin2 < mymin) {
        mymin <- mymin2
    }
    if (mymax2 > mymax) {
        mymax <- mymax2
    }
    # make a red histogram of the forecast errors, with the normally
    # distributed data overlaid:
    mybins <- seq(mymin, mymax, mybinsize)
    hist(forecasterrors, col = "red", freq = FALSE, breaks = mybins)
    # freq=FALSE ensures the area under the histogram = 1 generate normally
    # distributed data with mean 0 and standard deviation mysd
    myhist <- hist(mynorm, plot = FALSE, breaks = mybins)
    # plot the normal curve as a blue line on top of the histogram of forecast
    # errors:
    points(myhist$mids, myhist$density, type = "l", col = "blue", lwd = 2)
}


plot.ts(kingForecast$residuals)  # make time plot of forecast errors
{% endhighlight %}

![center](/figs/2013-05-12-arima-models-in-a-nutshell/ferrs1.png) 

{% highlight r %}
plotForecastErrors(kingForecast$residuals)  # make a histogram
{% endhighlight %}

![center](/figs/2013-05-12-arima-models-in-a-nutshell/ferrs2.png) 


