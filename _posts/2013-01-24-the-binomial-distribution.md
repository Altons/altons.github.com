---
layout: post
title: "The Binomial Distribution"
description: ""
category: R
tags: [R, rstats, statistics, probability]
---
{% include JB/setup %}




This is the first of a series of articles I'll be posting here about some of the most common probability distributions.



## Bernoulli random variable

It is a variable that has 2 possible outcomes: *"success"*, or *"failure"*. Success occurs with probability <span>\(p\)</span> and failure with probability <span>\(q=1-p\)</span>

The distribution of heads and tails in coin tossing is an example of a Bernoulli distribution with <span>\(p=q=1/2\)</span>. The Bernoulli distribution is the simplest discrete distribution, and it the building block for other more complicated discrete distributions. The distributions of a number of variate types defined based on sequences of independent Bernoulli trials that are curtailed in some way are summarized in the following list:

- **binomial distribution**: number of successes in n trials
- **geometric distribution**:	number of failures before the first success
- **negative binomial distribution**:	number of failures before the xth success

## Binomial probability distribution

Suppose that $n$ independent Bernoulli trials each one having probability of success p are to be performed. Let X be the
number of successes among the n trials. 

We say that X follows the binomial probability distribution with parameters $$B(n,p)$$.

In plain english, the binomial distribution describes the outcome of n independent trials in an experiment. Each trial is assumed to have only two outcomes, either success or failure.

* Probability mass function of X:

$$P(X=x)=\binom{n}{x}p^x(1-p)^{n-x}, x=0,1,2,3,...,n$$

where $$\binom{n}{x}=\frac{n!}{(n-x)!x!}$$

### Binomial Distribution Properties

- **Mean:** <span> \(\mu = np \) </span>
- **Variance:** <span>\(\sigma^2=npq\)</span>
- **Standard deviation:** <span>\(\sigma =\sqrt{npq}\)</span>
- **Skewness:** <span>\(\alpha_3=\frac{q-p}{\sqrt{npq}}\)</span>
- **Kurtosis:** <span>\(\alpha_4= 3 + \frac{1-6pq}{npq}\)</span>
- **Moment generating function:** <span>\(M(t) =(q+pe^t)^n\)</span>
- **Characteristic function:** <span>\(\phi(\omega)=(q+pe^{i\omega})^n\)</span>

>Note: The special case of a binomial distribution with <span>\(n=1\)</span> is also called the *Bernoulli distribution*.

### Binomial Distribution in R

- Generate Binomial random numbers:

In R we use the function **rbinom(e,n,p)** to generate binomial random numbers.
Parameters:
  - **e:** number of experiments you want to simulate
  - **n:** number of independent Bernoulli trials in each experiment
  - **p:** define the probability of success
  
**Example1**: A fair coin is tossed. Let the variable x take values 1 and 0 according to as the toss results in "Head"" or "Tail". Then, X is a Bernoulli variable with parameter p=1/2. Here, X denotes the number of heads obtained in the toss. Probability of success 1/2 and the probability of failure 1/2


{% highlight r %}
set.seed(0)  # always fix the seed to make results reproducibles.
e = 1  # simulate 1 experiment only
n = 1  # define 1 trial
p = 0.5  # define the probability of success p=1/2

rbinom(e, n, p)  ### conduct the random simulation
{% endhighlight %}



{% highlight text %}
## [1] 1
{% endhighlight %}


**Example2:** Run the above experiment 10 times and calculated the expected number of heads. Well, we don't really need to do the simulation as we already know the **E[X]** for a binomial distribution is np. So the expected number of heads in 10 experiments is:

{% highlight r %}
e = 10  # simulate 10 experiments
n = 1  # define 1 trial
p = 0.5  # define the probability of success p=1/2

e * p
{% endhighlight %}



{% highlight text %}
## [1] 5
{% endhighlight %}


let's run the simulation and calculate the average:


{% highlight r %}
sim = rbinom(e, n, p)
# Results
sim
{% endhighlight %}



{% highlight text %}
##  [1] 0 0 1 1 0 1 1 1 1 0
{% endhighlight %}



{% highlight r %}
# Total heads in 10 experiments
sum(sim)
{% endhighlight %}



{% highlight text %}
## [1] 6
{% endhighlight %}


Close enough. To understand why we got 6 instead 5 we need to understand *"Law of Large Numbers"*. This law explains that in the long run it becomes extremely likely that the proportion of success, X/n, will be as close as you like to the probability of success in a single trial, p.

Let's run again the same example but 10000 times


{% highlight r %}
e = 10000  # simulate 10000 experiments
n = 1  # define 1 trial
p = 0.5  # define the probability of success p=1/2

e * p
{% endhighlight %}



{% highlight text %}
## [1] 5000
{% endhighlight %}


let's run the simulation and calculate the average:


{% highlight r %}
sim = rbinom(e, n, p)

# Total heads in 10 experiments
sum(sim)
{% endhighlight %}



{% highlight text %}
## [1] 4959
{% endhighlight %}



{% highlight r %}
# Proportion of success
sum(sim)/e
{% endhighlight %}



{% highlight text %}
## [1] 0.4959
{% endhighlight %}


So, 0.4959 is very close to our original p=0.5

**Example3:** Generate 100 samples of binomial(20,.5). Print the first 3 experiments


{% highlight r %}
set.seed(0)  #let's define a new seed
e = 100  # simulate 100 experiments
n = 20  # define 20 trials
p = 0.5  # define the probability of success p=1/2

sim = rbinom(e, n, p)
sim[1:3]
{% endhighlight %}



{% highlight text %}
## [1] 13  9  9
{% endhighlight %}


The interpretation of the above result is:
  - In our first experiment we got 13 heads out 20
  - In our second experiment we got 9 heads out 20
  - In our third experiment we got 9 heads out 20

- Using probability distribution function pbinom() density function dbinom()

Let's illustrate the above functions with an example:

**Example4:** What's the probability of getting exactly 2 heads in 6 tosses of a fair coin?

Let's do it the hard way: manually

{% highlight r %}
set.seed(1244)
e = 1e+05  # create a large number of simulations
n = 6  # tosses
p = 0.5

result = rbinom(e, n, p)

# table with frequencies
freq.table = table(result)
freq.table
{% endhighlight %}



{% highlight text %}
## result
##     0     1     2     3     4     5     6 
##  1545  9444 23461 31168 23431  9377  1574
{% endhighlight %}



{% highlight r %}

# table with densities
t = freq.table/e
t
{% endhighlight %}



{% highlight text %}
## result
##       0       1       2       3       4       5       6 
## 0.01545 0.09444 0.23461 0.31168 0.23431 0.09377 0.01574
{% endhighlight %}

The probability of getting exactly 2 heads in 6 tosses is: 0.23461

Using dbinom() is much simpler: dbinom() calculates the exact probability of success for every x.

{% highlight r %}
# dbinom(x, size, prob);

## x: number of successes

## size: number of trials

## prob: probability of success.

dbinom(2, 6, 0.5)
{% endhighlight %}



{% highlight text %}
## [1] 0.2344
{% endhighlight %}


Alternatively, we can use the cumulative probability function for binomial distribution pbinom() 

{% highlight r %}
# This is the probability of P(X=0)+P(X=1)+P(X=2)
pbinom(2, 6, 0.5)
{% endhighlight %}



{% highlight text %}
## [1] 0.3437
{% endhighlight %}



{% highlight r %}
# We just need to remove P(X=0)+P(X=1) in order to get the probability of
# 2 heads in 6 tosses.
pbinom(2, 6, 0.5) - pbinom(1, 6, 0.5)
{% endhighlight %}



{% highlight text %}
## [1] 0.2344
{% endhighlight %}


**Example5:** Find the probability that in five tosses of a fair die, a 3 will appear

Let X the number of times a 3 appear in five tosses of a fair die

a) twice: <span>\(P(X=2)\)</span>

{% highlight r %}
dbinom(2, 5, 1/6)
{% endhighlight %}



{% highlight text %}
## [1] 0.1608
{% endhighlight %}

b) at most once: <span>\(P(X\leqslant{1})\)</span>

{% highlight r %}
# At most once = P(X=0) + P(X=1) we need the cumulative probability
pbinom(1, 5, 1/6)
{% endhighlight %}



{% highlight text %}
## [1] 0.8038
{% endhighlight %}

c) at least two times: <span>\(P(X\geqslant{2})=1 -P(X\leqslant{2})\)</span>

{% highlight r %}
1 - pbinom(2, 5, 1/6)
{% endhighlight %}



{% highlight text %}
## [1] 0.03549
{% endhighlight %}


**Example6**: if 20% of the bolts produced by a machine are defective, determine the probability that out of 4 bolts chosen at random:

a) 1 defective: <span>\(P(X=1)\)</span>

{% highlight r %}
dbinom(1, 4, 0.2)
{% endhighlight %}



{% highlight text %}
## [1] 0.4096
{% endhighlight %}

b) 0 defectives <span>\(P(X=0)\)</span>

{% highlight r %}
dbinom(0, 4, 0.2)
{% endhighlight %}



{% highlight text %}
## [1] 0.4096
{% endhighlight %}

c) Less than 2 defective:  <span>\(P(X \lt 2) = P(X\leqslant{1})\)</span>

{% highlight r %}
pbinom(1, 4, 0.2)
{% endhighlight %}



{% highlight text %}
## [1] 0.8192
{% endhighlight %}




