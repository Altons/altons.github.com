---
layout: post
title: "The Geometric Distribution"
description: ""
category: R
tags: [R, rstats, statistics, probability]
---
{% include JB/setup %}

According to Wikipedia the geometric distribution is either of two discrete probability distributions:

- The probability distribution of the number of <span>\(X\)</span> Bernoulli trials needed to get one success, supported on the set <span>\(\{ 1, 2, 3, ...\}\)</span>
- The probability distribution of the number <span>\(Y = X − 1\)</span> of failures before the first success, supported on the set <span>\(\{ 0, 1, 2, 3, ... \}\)</span>


Which of these one calls "the" geometric distribution is a matter of convention and convenience **if** is an old school longhand calculation. However if you are using statistical packages such like SAS, R, SPSS etc you should really know what definition is being used.

**R** *uses the number of failures (Y).*

Thus,  the form of geometric distribution for modeling number of failures until the first success:

$$P(Y=k)=(1-p)^{k}p$$

for <span>\(k=0,1,2,3...\)</span>


![center](/figs/Geometric-Distribution/unnamed-chunk-1.png) 



## Properties

- **Mean:** <span>\(\frac{1-p}{p}\)</span>
- **Variance:** <span>\(\frac{1-p}{p^2}\)</span>
- **Standard deviation:** <span>\(\frac{\sqrt{1-p}}{p}\)</span>
- **Skewness:**  <span>\(\frac{2-p}{\sqrt{1-p}}\)</span>
- **Kurtosis:**  <span>\(6+\frac{p^2}{1-p}\)</span>
- **Moment generating function:** <span>\(\frac{p}{1-(1-p)e^t}\)</span>
- **Characteristic function:** <span>\(\frac{p}{1-(1-p)e^{it}}\)</span>

## Geometric Distribution in R

### Generate Geometric random numbers

In R we use the function **rgeom(N,p)** to generate geometric random numbers.
Parameters:

- **N:** number of geometric(p) counts.
- **p:** probability of success in each trial

**Example1:** if we roll a fair die, and count the number of rolls before the first **6** appears, we have a geometric distribution with <span>\(p = 1/6\)</span>

So in R if roll the die 3 times we have:

{% highlight r %}
set.seed(1)

rgeom(3, 1/6)
{% endhighlight %}



{% highlight text %}
## [1]  3  2 14
{% endhighlight %}


The above result means:

- We had 2 failures before the first 6 appeared.
- We had 1 failure before the first 6 appeared.
- We had 13 failures before the first 6 appeared.

### Probability distribution function dgeom() density function pgeom()

**Example2:** Veronica is rolling a die. Calculate the probability of getting a 3 on the 8th roll. This statement is traduced in R as the probability of 7 failures before the first 3 appears.

For answering this question we use *dgeom()*.


{% highlight r %}
set.seed(9)
dgeom(7, 1/6)
{% endhighlight %}



{% highlight text %}
## [1] 0.04651
{% endhighlight %}


The probability of getting a 3 on the 8th roll is 0.04651 which is very low. Let's see what is the expected number of rolls before a 3 comes out. For this we'll use the mean of a geometric distribution <span>\(\frac{1-p}{p}\)</span>


{% highlight r %}
p = 1/6  # probability of success
q = 1 - p  # probaility of failure

# Mean of geometric distribution
q/p
{% endhighlight %}



{% highlight text %}
## [1] 5
{% endhighlight %}

The expected number of rolls before a 3 come out is 5

**Example3:** Lionel Messi scores 28% of his shots (2012/13 Spanish La Liga).

a) What is the probability that Messi will not score a goal until his 7th try?

{% highlight r %}
p = 0.28  # probability of success
# What's the probability of not scoring in the first 6 shots?
dgeom(6, p)
{% endhighlight %}



{% highlight text %}
## [1] 0.03901
{% endhighlight %}

The probability of not scoring until his 7th try is 0.03901

b) What is the expected number of shots before he scores?


{% highlight r %}
p = 0.28  # probability of success
q = 1 - p

q/p
{% endhighlight %}



{% highlight text %}
## [1] 2.571
{% endhighlight %}

Lionel Messi scores a goal every 2.571 shots on average.

c) What is the probability that the ﬁrst goal occurs in the ﬁrst 5 shots?

The above probability is the probability of:
- scoring on the first shot (no failure) +
- missing 1 shot +
- missing 2 shots +
- missing 3 shots +
- missing 4 shots

or

<span>\(P(X \leqslant 5)=P(X=0)+P(X=1)+P(X=2)+P(X=3)+P(X=4)\)</span>


{% highlight r %}
P0 = dgeom(0, 0.28)  # scoring on the first shot (no failure)
P1 = dgeom(1, 0.28)  # missing 1 shot
P2 = dgeom(2, 0.28)  # missing 2 shots
P3 = dgeom(3, 0.28)  # missing 3 shots
P4 = dgeom(4, 0.28)  # missing 4 shots
cat(P0, "+", P1, "+", P2, "+", P3, "+", P4)
{% endhighlight %}



{% highlight text %}
## 0.28 + 0.2016 + 0.1452 + 0.1045 + 0.07525
{% endhighlight %}



{% highlight r %}

P0 + P1 + P2 + P3 + P4
{% endhighlight %}



{% highlight text %}
## [1] 0.8065
{% endhighlight %}


or simply using the cumulative probability function *pgeom()*:


{% highlight r %}
pgeom(4, 0.28)
{% endhighlight %}



{% highlight text %}
## [1] 0.8065
{% endhighlight %}




