---
layout: post
title: "Detecting Language in Twitter"
description: ""
category: R
tags: [R, rstats, twitter, NLP]
---
{% include JB/setup %}

This is a quick and dirty way of determining language of twitter posts. Ideally one would have a naive bayes model scoring every tweet against a Bag of Words in order to detect languages but unfortunately package e1071 doesn't come with a multinomial boolean version of naive bayes which is the one I feel comfortable using when doing NLP.

Instead, I will use the tm package which comes with serveral stopwords list to quickly determine languages.

Let's load the packages required for this task:


{% highlight r %}
require(twitteR)
require(tm)
library(plyr)
{% endhighlight %}


According with the tm package documentation the supported languages are: danish, dutch, english, finnish, french, german, hungarian, italian, norwegian, portuguese, russian, spanish, and swedish.

I am going to create a list of supported languages and then iterate through this list in order to extract all stopwords.

So here it is my list with supported languages:


{% highlight r %}
langList <- list("danish", "dutch", "english", "finnish", "french", "german", 
    "hungarian", "italian", "norwegian", "portuguese", "russian", "spanish", 
    "swedish")
{% endhighlight %}


and my empty list where I'll store languages along with their stopwords (similar to a Python dictionary):


{% highlight r %}
langStopwords <- list()
{% endhighlight %}


and now let's iterate over langList:


{% highlight r %}
for (lang in langList) langStopwords[[lang]] <- stopwords(kind = lang)
{% endhighlight %}


done!

Let's do a quick check:

english:


{% highlight r %}
langStopwords[["english"]][c(1:10)]
{% endhighlight %}



{% highlight text %}
##  [1] "a"       "about"   "above"   "across"  "after"   "again"   "against"
##  [8] "all"     "almost"  "alone"
{% endhighlight %}


german:


{% highlight r %}
langStopwords[["german"]][c(1:10)]
{% endhighlight %}



{% highlight text %}
##  [1] "aber"  "alle"  "allem" "allen" "aller" "alles" "als"   "also" 
##  [9] "am"    "an"
{% endhighlight %}


total stopwords by language:


{% highlight r %}
for (lang in langList) {
    cat(length(langStopwords[[lang]]), "-", lang, "\n")
}
{% endhighlight %}



{% highlight text %}
## 145 - danish 
## 106 - dutch 
## 488 - english 
## 235 - finnish 
## 236 - french 
## 264 - german 
## 207 - hungarian 
## 361 - italian 
## 228 - norwegian 
## 295 - portuguese 
## 159 - russian 
## 428 - spanish 
## 114 - swedish
{% endhighlight %}


Ok, now we need a function that scores a given piece of text of any length so here it is my function:


{% highlight r %}
langScore <- function(post) {
    results <- list()
    toList <- strsplit(post, " ")
    lowerList <- lapply(toList, tolower)
    for (word in lowerList) {
        for (lang in names(langStopwords)) {
            results[[lang]] <- is.element(word, strsplit(langStopwords[[lang]], 
                " "))
        }
        
    }
    scores <- as.vector(lapply(results, sum))
    # In case language is not supported or anything else like links or
    # hashtags
    if (Reduce("+", scores) == 0) {
        return("unknown")
    }
    max_score <- which.max(scores)
    return(names(scores[max_score]))
}
{% endhighlight %}


Let's do some examples:


{% highlight r %}
ex1 <- "Heavy snow is falling across parts of the UK, as forecasters warn that large swathes of England could see up to 4in (10cm)\nfall on Monday"
label <- langScore(ex1)
label
{% endhighlight %}



{% highlight text %}
## [1] "english"
{% endhighlight %}



{% highlight r %}

ex2 <- "Obama pide subir el techo de la deuda porque EEUU no es una nación morosa"
label <- langScore(ex2)
label
{% endhighlight %}



{% highlight text %}
## [1] "spanish"
{% endhighlight %}



Ok my function seems to be working fine so now let's get some tweets with **#bigdata** hashtag.


{% highlight r %}
# Search tweets with '#datamining'

# search a maximum of 10 tweets in french
fr_tweets = ldply(searchTwitter("#bigdata", lang = "fr", n = 10), statusText)
fr_tweets$lang = "french"

# search a maximum of 10 tweets in german
de_tweets = ldply(searchTwitter("#bigdata", lang = "de", n = 10), statusText)
de_tweets$lang = "german"
# search a maximum of 10 tweets in spanish
es_tweets = ldply(searchTwitter("#bigdata", lang = "es", n = 10), statusText)
es_tweets$lang = "spanish"
# search a maximum of 10 tweets in english
en_tweets = ldply(searchTwitter("#bigdata", lang = "en", n = 10), statusText)
en_tweets$lang = "english"
{% endhighlight %}


I will need a data frame with all tweets  (I'll use this df to score posts):

{% highlight r %}
tweets = rbind(fr_tweets, de_tweets, es_tweets, en_tweets)
{% endhighlight %}


Let's look at post counts by language in another as we want to check accuracy later on...

{% highlight r %}
table(tweets$lang)
{% endhighlight %}



{% highlight text %}
## 
## english  french  german spanish 
##      10      10      10      10
{% endhighlight %}


The first tweet in every language is:


{% highlight r %}
ddply(tweets, .(lang), function(x) x[nrow(x), 1])
{% endhighlight %}



{% highlight text %}
##      lang
## 1 english
## 2  french
## 3  german
## 4 spanish
##                                                                                                                                          V1
## 1                                RT @donloden “@KarimaZannotti: #BigData Technology Does Not Replace a Data Warehouse http://t.co/MPVH6SIM”
## 2 RT @sunysky: Analytics for offline retail, bientôt Google Analytics pour les magasins physiques? #BigData #Analytics http://t.co/c9MqCrXn
## 3                RT @CIOredaktion Obwohl Firmen Potenzial sehen - Kein Geld für #BigData. Studie von #Fraunhofer IAIS: http://t.co/ObOAEsV8
## 4                                                    Todos los #datasets de @GuardianData  http://t.co/qJDEmp7s #bigdata  cc: @albertocairo
{% endhighlight %}



Let's score the list of tweets:


{% highlight r %}
test <- NULL

for (i in 1:nrow(tweets)) {
    post <- tweets[i, 1]
    label <- tweets[i, 2]
    prediction <- langScore(post)
    if (!exists("test")) {
        test <- as.data.frame(cbind(post = post, label = label, prediction = prediction))
    } else if (exists("test")) {
        temp <- as.data.frame(cbind(post = post, label = label, prediction = prediction))
        test <- rbind(test, temp)
        rm(temp)
        
    }
}
{% endhighlight %}


And finally our crosstab table looks like:

{% highlight r %}
table(test$label, test$prediction)
{% endhighlight %}



{% highlight text %}
##          
##           french unknown german dutch spanish english
##   french       9       1      0     0       0       0
##   german       0       0      9     1       0       0
##   spanish      0       0      0     0       9       1
##   english      0       1      0     0       0       9
{% endhighlight %}


There are some posts labeled incorrectly some of the reasons behind are:

- Even though we have searched by language, doesn't mean the post is written in a specific language - it only means the default language used by the twitter account is german, english etc.
- It's harder to predict correctly when message is too short.
- Simply error in prediction

I hope you've found this post useful.

{% highlight r %}
sessionInfo()
{% endhighlight %}



{% highlight text %}
## R version 2.15.2 (2012-10-26)
## Platform: x86_64-apple-darwin9.8.0/x86_64 (64-bit)
## 
## locale:
## [1] en_GB.UTF-8/en_GB.UTF-8/en_GB.UTF-8/C/en_GB.UTF-8/en_GB.UTF-8
## 
## attached base packages:
## [1] stats     graphics  grDevices utils     datasets  methods   base     
## 
## other attached packages:
##  [1] e1071_1.6-1     class_7.3-5     caret_5.15-048  foreach_1.4.0  
##  [5] cluster_1.14.3  reshape2_1.2.1  lattice_0.20-10 plyr_1.7.1     
##  [9] twitteR_0.99.19 rjson_0.2.11    RCurl_1.95-3    bitops_1.0-4.2 
## [13] tm_0.5-8.1      knitr_0.9      
## 
## loaded via a namespace (and not attached):
##  [1] codetools_0.2-8  digest_0.6.0     evaluate_0.4.3   formatR_0.7     
##  [5] grid_2.15.2      iterators_1.0.6  rstudio_0.97.248 slam_0.1-26     
##  [9] stringr_0.6.1    tools_2.15.2
{% endhighlight %}

