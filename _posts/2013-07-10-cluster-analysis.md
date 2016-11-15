---
layout: post
title: "Cluster Analysis"
description: ""
category: rstats
tags: [rstats,R,tutorial]
---


Cluster analysis is the art of mathematically split objects into groups or clusters whose members are similar in some way and dissimilar to objects belonging to other groups.

There is a plethora of clustering algorithms available with plenty supporters and detractors of all flavours. I am not going to cover all of them.

This post is about how I normally conduct cluster analyses. The way I use cluster analysis as a previous step for predictive modelling - this mean I create cluster and when new objects are available I try to classify them into groups using multiple techniques.

When possible, I'll try to provide different approaches to achieve the same result using R.

For this example I'm going to use the water-treatment data set from [UCI](http://archive.ics.uci.edu/ml/datasets/Water+Treatment+Plant)

This dataset comes from the daily measures of sensors in a urban waste water treatment plant. The objective is to classify the operational state of the plant in order to predict faults through the state variables of the plant at each of the stages of the treatment process.

Data Set Description
-

* Number of instances: 527
* Number of Attributes: 38
* All attributes are numeric and continuous

There are some missing values, all are unknown information.

Comments to the data file:

The first element of each line is the day of the data, the rest are the attribute values

###Attributes

```
N.  Attrib.    
 1  Q-E        (input flow to plant)  
 2  ZN-E       (input Zinc to plant)
 3  PH-E       (input pH to plant)
 4  DBO-E      (input Biological demand of oxygen to plant)
 5  DQO-E      (input chemical demand of oxygen to plant)
 6  SS-E       (input suspended solids to plant)  
 7  SSV-E      (input volatile supended solids to plant)
 8  SED-E      (input sediments to plant)
 9  COND-E     (input conductivity to plant)
10  PH-P       (input pH to primary settler)
11  DBO-P      (input Biological demand of oxygen to primary settler)
12  SS-P       (input suspended solids to primary settler)
13  SSV-P      (input volatile supended solids to primary settler)
14  SED-P      (input sediments to primary settler)
15  COND-P     (input conductivity to primary settler)
16  PH-D       (input pH to secondary settler)
17  DBO-D      (input Biological demand of oxygen to secondary settler)
18  DQO-D      (input chemical demand of oxygen to secondary settler)
19  SS-D       (input suspended solids to secondary settler)
20  SSV-D      (input volatile supended solids to secondary settler)
21  SED-D      (input sediments to secondary settler)  
22  COND-D     (input conductivity to secondary settler)
23  PH-S       (output pH)   
24  DBO-S      (output Biological demand of oxygen)
25  DQO-S      (output chemical demand of oxygen)
26  SS-S       (output suspended solids)
27  SSV-S      (output volatile supended solids)
28  SED-S      (output sediments)
29  COND-S     (output conductivity)
30  RD-DBO-P   (performance input Biological demand of oxygen in primary settler)
31  RD-SS-P    (performance input suspended solids to primary settler)
32  RD-SED-P   (performance input sediments to primary settler)
33  RD-DBO-S   (performance input Biological demand of oxygen to secondary settler)
34  RD-DQO-S   (performance input chemical demand of oxygen to secondary settler)
35  RD-DBO-G   (global performance input Biological demand of oxygen)
36  RD-DQO-G   (global performance input chemical demand of oxygen)
37  RD-SS-G    (global performance input suspended solids)
38  RD-SED-G   (global performance input sediments)
```

Data Preparation
-


{% highlight r %}
water.treatment = read.csv("../data/water-treatment.data", header = F)
# Remove the first column (day of the data)
water.treatment$V1 = NULL
{% endhighlight %}



{% highlight r %}
# rename variables to something more friendly
colnames(water.treatment) = c("Q_E", "ZN_E", "PH_E", "DBO_E", "DQO_E", "SS_E",
    "SSV_E", "SED_E", "COND_E", "PH_P", "DBO_P", "SS_P", "SSV_P", "SED_P", "COND_P",
    "PH_D", "DBO_D", "DQO_D", "SS_D", "SSV_D", "SED_D", "COND_D", "PH_S", "DBO_S",
    "DQO_S", "SS_S", "SSV_S", "SED_S", "COND_S", "RD_DBO_P", "RD_SS_P", "RD_SED_P",
    "RD_DBO_S", "RD_DQO_S", "RD_DBO_G", "RD_DQO_G", "RD_SS_G", "RD_SED_G")
{% endhighlight %}



{% highlight r %}
apply(water.treatment, 2, class)
{% endhighlight %}



{% highlight text %}
##         Q_E        ZN_E        PH_E       DBO_E       DQO_E        SS_E
## "character" "character" "character" "character" "character" "character"
##       SSV_E       SED_E      COND_E        PH_P       DBO_P        SS_P
## "character" "character" "character" "character" "character" "character"
##       SSV_P       SED_P      COND_P        PH_D       DBO_D       DQO_D
## "character" "character" "character" "character" "character" "character"
##        SS_D       SSV_D       SED_D      COND_D        PH_S       DBO_S
## "character" "character" "character" "character" "character" "character"
##       DQO_S        SS_S       SSV_S       SED_S      COND_S    RD_DBO_P
## "character" "character" "character" "character" "character" "character"
##     RD_SS_P    RD_SED_P    RD_DBO_S    RD_DQO_S    RD_DBO_G    RD_DQO_G
## "character" "character" "character" "character" "character" "character"
##     RD_SS_G    RD_SED_G
## "character" "character"
{% endhighlight %}



{% highlight r %}
# attributes are numeric and continuous so convert 'character variable to
# numeric
water.treatment.num = apply(water.treatment, 2, as.numeric)
{% endhighlight %}


Now we have a bunch of missing values due to the above conversion so we need to fill these gaps.


{% highlight r %}
library(impute)
rseed = 2394  # for reproducibility.
# Note: impute.knn requires a matrix as input data
water.treatment.knn = impute.knn(water.treatment.num, rng.seed = rseed)
# Retrieve imputed matrix from list object
water.treatment.imputed = as.data.frame(water.treatment.knn$data)
{% endhighlight %}


Exploratory Analysis
-

It is always recommendable  to remove variables with very low variability as they will not contribute to the formation of clusters.


{% highlight r %}
library(caret)
nearZeroVar(water.treatment.imputed)
{% endhighlight %}



{% highlight text %}
## integer(0)
{% endhighlight %}


Some algorithms like kmeans are susceptible to outliers, badly skewed distribution etc. So checking these things and fixing or improving skewness when possible is a most-do before applying clustering (especially kmeans)


{% highlight r %}
summary(water.treatment.imputed)
{% endhighlight %}



{% highlight text %}
##       Q_E             ZN_E            PH_E          DBO_E    
##  Min.   :10050   Min.   : 0.10   Min.   :6.90   Min.   : 31  
##  1st Qu.:32964   1st Qu.: 0.90   1st Qu.:7.60   1st Qu.:148  
##  Median :36010   Median : 1.50   Median :7.80   Median :182  
##  Mean   :37221   Mean   : 2.36   Mean   :7.81   Mean   :188  
##  3rd Qu.:41182   3rd Qu.: 3.00   3rd Qu.:8.00   3rd Qu.:223  
##  Max.   :60081   Max.   :33.50   Max.   :8.70   Max.   :438  
##      DQO_E          SS_E          SSV_E          SED_E      
##  Min.   : 81   Min.   :  98   Min.   :13.2   Min.   : 0.40  
##  1st Qu.:326   1st Qu.: 170   1st Qu.:55.9   1st Qu.: 3.20  
##  Median :400   Median : 196   Median :64.3   Median : 4.41  
##  Mean   :407   Mean   : 228   Mean   :61.5   Mean   : 4.58  
##  3rd Qu.:474   3rd Qu.: 242   3rd Qu.:69.6   3rd Qu.: 5.50  
##  Max.   :941   Max.   :2008   Max.   :85.0   Max.   :36.00  
##      COND_E          PH_P          DBO_P          SS_P          SSV_P     
##  Min.   : 651   Min.   :7.30   Min.   : 32   Min.   : 104   Min.   : 7.1  
##  1st Qu.:1201   1st Qu.:7.70   1st Qu.:156   1st Qu.: 184   1st Qu.:54.2  
##  Median :1406   Median :7.80   Median :194   Median : 220   Median :62.9  
##  Mean   :1479   Mean   :7.83   Mean   :205   Mean   : 254   Mean   :60.4  
##  3rd Qu.:1672   3rd Qu.:8.00   3rd Qu.:238   3rd Qu.: 272   3rd Qu.:68.8  
##  Max.   :3230   Max.   :8.50   Max.   :517   Max.   :1692   Max.   :93.5  
##      SED_P           COND_P          PH_D          DBO_D         DQO_D    
##  Min.   : 1.00   Min.   : 646   Min.   :7.10   Min.   : 26   Min.   : 80  
##  1st Qu.: 3.05   1st Qu.:1217   1st Qu.:7.70   1st Qu.: 98   1st Qu.:222  
##  Median : 4.50   Median :1420   Median :7.80   Median :119   Median :274  
##  Mean   : 5.01   Mean   :1496   Mean   :7.81   Mean   :122   Mean   :274  
##  3rd Qu.: 6.00   3rd Qu.:1714   3rd Qu.:7.90   3rd Qu.:147   3rd Qu.:325  
##  Max.   :46.00   Max.   :3170   Max.   :8.40   Max.   :285   Max.   :511  
##       SS_D           SSV_D           SED_D          COND_D    
##  Min.   : 49.0   Min.   : 20.2   Min.   :0.00   Min.   :  85  
##  1st Qu.: 78.0   1st Qu.: 68.0   1st Qu.:0.20   1st Qu.:1226  
##  Median : 92.0   Median : 74.5   Median :0.30   Median :1428  
##  Mean   : 94.2   Mean   : 73.0   Mean   :0.42   Mean   :1491  
##  3rd Qu.:106.0   3rd Qu.: 79.3   3rd Qu.:0.50   3rd Qu.:1701  
##  Max.   :244.0   Max.   :100.0   Max.   :3.50   Max.   :3690  
##       PH_S          DBO_S           DQO_S            SS_S      
##  Min.   :7.00   Min.   :  3.0   Min.   :  9.0   Min.   :  6.0  
##  1st Qu.:7.60   1st Qu.: 14.0   1st Qu.: 65.0   1st Qu.: 14.0  
##  Median :7.70   Median : 18.0   Median : 85.0   Median : 19.0  
##  Mean   :7.71   Mean   : 20.3   Mean   : 88.5   Mean   : 22.3  
##  3rd Qu.:7.80   3rd Qu.: 23.0   3rd Qu.:103.0   3rd Qu.: 24.0  
##  Max.   :9.70   Max.   :320.0   Max.   :350.0   Max.   :238.0  
##      SSV_S           SED_S           COND_S        RD_DBO_P   
##  Min.   : 29.2   Min.   :0.000   Min.   : 683   Min.   : 0.6  
##  1st Qu.: 75.2   1st Qu.:0.000   1st Qu.:1235   1st Qu.:30.5  
##  Median : 81.3   Median :0.010   Median :1433   Median :37.8  
##  Mean   : 80.2   Mean   :0.036   Mean   :1496   Mean   :38.6  
##  3rd Qu.: 85.7   3rd Qu.:0.020   3rd Qu.:1694   3rd Qu.:47.6  
##  Max.   :100.0   Max.   :3.500   Max.   :3950   Max.   :79.1  
##     RD_SS_P        RD_SED_P        RD_DBO_S       RD_DQO_S   
##  Min.   : 5.3   Min.   :  7.7   Min.   : 8.2   Min.   : 1.4  
##  1st Qu.:50.6   1st Qu.: 88.6   1st Qu.:80.5   1st Qu.:62.9  
##  Median :59.3   Median : 93.3   Median :85.0   Median :69.3  
##  Mean   :58.5   Mean   : 90.5   Mean   :82.9   Mean   :67.5  
##  3rd Qu.:66.8   3rd Qu.: 95.7   3rd Qu.:87.7   3rd Qu.:75.0  
##  Max.   :96.1   Max.   :100.0   Max.   :94.7   Max.   :96.8  
##     RD_DBO_G       RD_DQO_G       RD_SS_G        RD_SED_G    
##  Min.   :19.6   Min.   :19.2   Min.   :10.3   Min.   : 36.4  
##  1st Qu.:87.2   1st Qu.:73.3   1st Qu.:87.5   1st Qu.: 99.3  
##  Median :89.9   Median :78.8   Median :90.7   Median : 99.7  
##  Mean   :88.7   Mean   :77.5   Mean   :88.9   Mean   : 99.1  
##  3rd Qu.:92.3   3rd Qu.:83.2   3rd Qu.:93.0   3rd Qu.:100.0  
##  Max.   :97.0   Max.   :98.1   Max.   :99.4   Max.   :100.0
{% endhighlight %}



{% highlight r %}
# check for skewness
library(e1071)
sk_ind = apply(water.treatment.imputed, 2, skewness)
sort(sk_ind)
{% endhighlight %}



{% highlight text %}
##  RD_SED_G  RD_DBO_G   RD_SS_G  RD_DBO_S  RD_SED_P  RD_DQO_G  RD_DQO_S
## -11.86161  -5.24580  -4.54156  -4.21696  -3.32192  -1.67071  -1.38834
##     SSV_E     SSV_P     SSV_D     SSV_S   RD_SS_P  RD_DBO_P      PH_D
##  -1.14369  -1.04507  -1.03271  -0.91374  -0.35996  -0.01697   0.03737
##     DQO_D      PH_E      PH_P     DBO_D       Q_E     DQO_E     DBO_E
##   0.03915   0.14153   0.24740   0.30907   0.47877   0.57821   0.80853
##    COND_D    COND_P    COND_E     DBO_P    COND_S      SS_D      PH_S
##   0.85505   0.89315   0.89893   0.98658   1.21396   1.81187   2.01946
##     DQO_S     SED_D      ZN_E      SS_P     SED_E     SED_P      SS_S
##   2.27587   3.40077   4.69888   5.37324   5.49156   5.79264   6.29553
##      SS_E     DBO_S     SED_S
##   6.61849  11.36129  14.20156
{% endhighlight %}


Some variables are badly skewed (positively and negatively). Let's plot some of them.


{% highlight r %}
par(mfrow = c(2, 2))
hist(water.treatment.imputed$RD_SED_G)
hist(water.treatment.imputed$RD_DBO_S)
hist(water.treatment.imputed$RD_DBO_P)
hist(water.treatment.imputed$SED_S)
{% endhighlight %}

![center](/figs/2013-07-10-cluster-analysis/check_skewness.png)


We can use Box-Cox transformations to try to fix/improve skeweness.


{% highlight r %}
# from caret use BoxCoxTrans()
RD_DBO_S.trans = BoxCoxTrans(water.treatment.imputed$RD_DBO_S)
RD_DBO_S.trans
{% endhighlight %}



{% highlight text %}
## Box-Cox Transformation
##
## 527 data points used to estimate Lambda
##
## Input data summary:
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.
##     8.2    80.5    85.0    82.9    87.7    94.7
##
## Largest/Smallest: 11.5
## Sample Skewness: -4.22
##
## Estimated Lambda: 2
{% endhighlight %}



{% highlight r %}
par(mfrow = c(1, 2))
# raw variable
hist(water.treatment.imputed$RD_DBO_S)
# After Applying Cox-Box
hist(predict(RD_DBO_S.trans, water.treatment.imputed$RD_DBO_S))
{% endhighlight %}

![center](/figs/2013-07-10-cluster-analysis/Box-Cox.png)


This is only for one variable so we need to do the above for all other variables with ill-skewness. Whilst we could wrap up the above piece of code in a function there is another way I call the **FastTrack** using the caret package.

The **FastTrack** uses the preProcess() function in the caret package to apply BoxCox, center & scale and imputation all in one go. It is pretty neat!


{% highlight r %}
trans = preProcess(as.data.frame(water.treatment.num), method = c("BoxCox",
    "center", "scale", "knnImpute"), k = 10)
# Apply transformations
water.treatment.transf = predict(trans, as.data.frame(water.treatment.num))
{% endhighlight %}


The only thing you need to do before applying the fastTrack approach is to ensure all your variables are continuous.

K-means
-

I always start with k-means as it is easy to implement and understand. I set k to a high value and run k-means from 2 clusters to k clusters and plot the well known elbow graph to have an idea of how many clusters I will need.

For this example I choose k=50.


{% highlight r %}
library(useful)
source("../multiKmeans.R")
source("../elbowGraph.R")
cl = multiKmeans(water.treatment.transf, 50, 200)
elbow = elbowGraph(cl$css)
elbow
{% endhighlight %}

![center](/figs/2013-07-10-cluster-analysis/kmeans.png)


> Note: You may get a slightly different graph as k-means always return different setups due to initial random selection.

Looking at the above graph I'd say **k** is between 3 and 9 clusters

Hierarchical Clustering
-

Initial k-means is giving `3<=k<=9` clusters and I think 4 or 5 clusters seems right but I'll use hierarchical clustering to gain more insight on the number of clusters. Please notice that I use other clustering algorithms to gain insight on how many clusters I will choose for my previous k-means.


{% highlight r %}
# Ward Hierarchical Clustering
d = dist(water.treatment.transf, method = "euclidean")  # distance matrix
fit1 = hclust(d, method = "ward")
plot(fit1, labels = FALSE)  # display dendogram
{% endhighlight %}

![center](/figs/2013-07-10-cluster-analysis/hc.png)

{% highlight r %}

{% endhighlight %}


In the above tree graph, what we are looking for is for large stems and it seems like 3,4 or 5 clusters are big candidates.

Let's see how these different options of clusters looks like.


{% highlight r %}
par(mfrow = c(2, 2))
plot(fit1, labels = FALSE)
rect.hclust(fit1, k = 3, border = "red")
plot(fit1, labels = FALSE)
rect.hclust(fit1, k = 4, border = "green")
plot(fit1, labels = FALSE)
rect.hclust(fit1, k = 5, border = "blue")
{% endhighlight %}

![center](/figs/2013-07-10-cluster-analysis/dendograms.png)


3 clusters seem like a potential solution. Let's see how looks like 3 clusters


{% highlight r %}
clus3 = cl$cluster[[3]]
source("../plot.kmeans2.R")
plot.kmeans2(clus3, data = water.treatment.transf)
{% endhighlight %}

![center](/figs/2013-07-10-cluster-analysis/3clusters.png)



Validating cluster solutions
-

Another way of validating cluster is using the **clValid** are package. Google it for further information.

{% highlight r %}
library(clValid)
intern = clValid(water.treatment.transf, 3:8, clMethods = c("hierarchical",
    "kmeans", "pam"), validation = "internal")
{% endhighlight %}



{% highlight text %}
## Warning: rownames for data not specified, using 1:nrow(data)
{% endhighlight %}



{% highlight r %}
summary(intern)
{% endhighlight %}



{% highlight text %}
##
## Clustering Methods:
##  hierarchical kmeans pam
##
## Cluster sizes:
##  3 4 5 6 7 8
##
## Validation Measures:
##                                  3       4       5       6       7       8
##                                                                           
## hierarchical Connectivity    5.858   9.716  48.670  64.571  66.575  72.167
##              Dunn            0.633   0.361   0.251   0.253   0.253   0.264
##              Silhouette      0.580   0.417   0.304   0.252   0.233   0.177
## kmeans       Connectivity    5.858 183.667 242.506 293.722 367.299 391.783
##              Dunn            0.633   0.076   0.089   0.048   0.093   0.077
##              Silhouette      0.580   0.152   0.121   0.112   0.099   0.098
## pam          Connectivity  272.549 325.534 373.498 404.961 456.599 475.980
##              Dunn            0.035   0.041   0.041   0.033   0.033   0.032
##              Silhouette      0.115   0.104   0.088   0.077   0.074   0.061
##
## Optimal Scores:
##
##              Score Method       Clusters
## Connectivity 5.858 hierarchical 3       
## Dunn         0.633 hierarchical 3       
## Silhouette   0.580 hierarchical 3
{% endhighlight %}



{% highlight r %}

stab = clValid(water.treatment.transf, 3:8, clMethods = c("hierarchical", "kmeans",
    "pam"), validation = "stability")
{% endhighlight %}



{% highlight text %}
## Warning: rownames for data not specified, using 1:nrow(data)
{% endhighlight %}



{% highlight r %}

summary(stab)
{% endhighlight %}



{% highlight text %}
##
## Clustering Methods:
##  hierarchical kmeans pam
##
## Cluster sizes:
##  3 4 5 6 7 8
##
## Validation Measures:
##                       3     4     5     6     7     8
##                                                      
## hierarchical APN  0.000 0.003 0.010 0.018 0.021 0.028
##              AD   7.113 7.070 6.985 6.866 6.825 6.807
##              ADM  0.002 0.043 0.343 0.250 0.249 0.285
##              FOM  0.849 0.846 0.834 0.820 0.814 0.810
## kmeans       APN  0.025 0.072 0.084 0.116 0.352 0.456
##              AD   7.114 6.567 6.254 6.064 6.155 6.153
##              ADM  0.109 0.330 0.392 0.536 1.588 1.875
##              FOM  0.849 0.793 0.762 0.739 0.735 0.727
## pam          APN  0.067 0.095 0.185 0.163 0.255 0.239
##              AD   6.441 6.225 6.165 6.050 6.025 5.914
##              ADM  0.323 0.431 0.820 0.682 1.013 0.909
##              FOM  0.802 0.775 0.774 0.762 0.757 0.743
##
## Optimal Scores:
##
##     Score Method       Clusters
## APN 0.000 hierarchical 3       
## AD  5.914 pam          8       
## ADM 0.002 hierarchical 3       
## FOM 0.727 kmeans       8
{% endhighlight %}


Using the stability algorithm it seems like 3 and 8 are good options for the number of clusters - however with 8 clusters there is a lot of overlapping among clusters.


{% highlight r %}
clus8 = cl$cluster[[8]]
source("../plot.kmeans2.R")
plot.kmeans2(clus8, data = water.treatment.transf)
{% endhighlight %}

![center](/figs/2013-07-10-cluster-analysis/8clusters.png)


So final solution at this stage will be 3 clusters. One also can isolate each cluster and try to find if there are meaningful sub-clusters by repeating the above process.
