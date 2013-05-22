---
layout: post
title: "Cross Validation Using SAS"
description: ""
category: SAS 
tags: [statistics, machine learning, cross validation]
---
{% include JB/setup %}



Cross validation is a model evaluation method that is better than residuals.


 The problem with residual evaluations is that they do not give an indication of how well the machine learning algo will do when it is asked to make new predictions for data it has not already seen. One way to overcome this problem is to not use the entire data set when training a machine learning.


Some of the data is removed before training begins. Then when training is done, the data that was removed can be used to test the performance of the learned model on "new" data. 

This is the basic idea for a whole class of model evaluation methods called cross validation.  The holdout method is the simplest kind of cross validation. The data set is separated into two sets, called the training set and the testing set.  


However, its evaluation can have a high variance. The evaluation may depend heavily on which data points end up in the training set and which end up in the test set, and thus the evaluation may be significantly different depending on how the division is made. 

**K-fold** cross validation is one way to improve over the holdout method.  The data set is divided into *k subsets*, and the holdout method is repeated *k* times.


Each time, one of the *k subsets* is used as the test set and the other *k-1 subsets* are put together to form a training set. Then the average error across all k trials is computed (or any other measure of accuracy/error). The advantage of this method is that it matters less how the data gets divided. 

Every data point gets to be in a test set exactly once, and gets to be in a training set k-1 times. The variance of the resulting estimate is reduced as k is increased. The disadvantage of this method is that the training algorithm has to be rerun from scratch k times, which means it takes k times as much computation to make an evaluation. 

**Leave-one-out cross** validation is *K-fold* cross validation taken to its logical extreme, with K equal to N, the number of data points in the set. 


 That means that N separate times, the machine learning algo is trained on all the data except for one point and a prediction is made for that point. As before the average error is computed and used to evaluate the model.  

The evaluation given by *leave-one-out* LOO cross validation error is good, but at first pass it seems very expensive to compute.

Fortunately, locally weighted learners can make LOO predictions just as easily as they make regular predictions. 

###Code


Cross-validation typically takes K replicate samples of the data, each one using <span>\\(\frac{(K-1)}{K}\\)</span> of the data to build the model and the remaining <span>\\(\frac{1}{K}\\)</span> of the data to test the model in some way.


 This is called a K-fold cross-validation.  In addition, the random K-fold cross-validation does not split the data into a partition of K subsets, but takes K independent samples of size <span>\\(\frac{N*(K- 1)}{K}\\)</span> instead.


Below there is a sample of how to do it SAS:

```

  %let K=5;
  %let rate=%sysevalf((&K-1)/&K);
  
  *Build model with all data;
  
  proc reg data=xv.sample1;
  model y=x1 x2 x3 x4 x5 x6 x7;
  run;

  *Generate the cross validation sample;
  proc surveyselect data=xv.sample1 out=cv seed=231258
  samprate=&rate outall reps=10;
  run;

  /* the variable selected is an automatic variable generatic by surveyselect.If selected is true then then new_y will get the value of y otherwise is missing */
   
  data cv;
  set cv;
   if selected then new y=y;
  run;

/* get predicted values for the missing new_y in each replicate */

 ods output ParameterEstimates=ParamEst;
  proc reg data=cv;
    model new_y=x1 x2 x3 x4 x5 x6 x7;
   by replicate;
   output out=out1(where=(new_y=.)) predicted=y_hat;
  run;

 /* summarise the results of the cross-validations */ 
  data out2;
  set out1;
   d=y-y_hat;
   absd=abs(d);
  run;

  proc summary data=out2;
  var d absd;
  output out=out3 std(d)=rmse mean(absd)=mae;
  run;

 /* Calculate the R2 */ 
 proc corr data=out2 pearson out=corr(where=( type ='CORR'));
  var y ;
  with y hat;
 run;

 data corr;
  set corr;
  Rsqrd=y**2;
 run;
```


The *%LET* statements at the top provide a computation of the sampling rate: for a K-fold cross-validation, we always want <span>\\(\frac{(K-1)}{K}\\)</span> of the data points in each replicate sample. 

The **%SYSEVALF()** macro function lets us perform foating point calculations within the macro code.

The key feature of the **PROC SURVEYSELECT** statement is the OUTALL option. OUTALL tells the procedure to output all of the records from the input data set, but to mark the selected sample records. 

The way it does this is with a new variable, SELECTED. SELECTED is 1 for the chosen records and 0 for the rest. So in each of the K replicates, all N records appear, with <span>\\(\frac{(K-1)}{K}\\)</span>  of them with SELECTED.

The subsequent data step uses SELECTED to prepare for the model validation steps. We will keep our real response value Y, and perform the modeling on NEW Y, which will be missing for all the values we want to use as our hold-out group in each replicate. 

The PROC REG step then fits our model, and predicts a Y-hat for each value where NEW Y is missing.  We keep only these records, since they are all we need for our model evaluations.  

Two of the popular measures of model performance in cross-validation are the Root Mean Square Error (RMSE) and the mean absolute error (MAE).  

The RMSE is just the standard deviation of the differences Y-YHAT , while the MAE is just the average of their absolute values. 

The differences D and their absolute values ABSD are computed in the following data step, and then passed to PROC SUMMARY to get the RMSE and MAE in a straightforward manner.  

Also the <span>\\(R^2\\)</span> in the test set can be calculated by the following formula: <span>\\((corr(y-yhat))\\)</span>

This implementation can be easy translated to other programming languages.