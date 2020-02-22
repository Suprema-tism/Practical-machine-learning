---
title: "PML"
author: "Konstantin Zuev"
date: "22 02 2020"
output: 
  html_document: 
    keep_md: yes
---




```r
options(knitr.duplicate.label = 'allow')
```

## Cleaning data

It might be not the most efficient way, however, I definitely need to remove columns with NAs and empty values:


```
## Warning: package 'caret' was built under R version 3.6.2
```

```
## Loading required package: lattice
```

```
## Loading required package: ggplot2
```

```
## Warning: package 'ggplot2' was built under R version 3.6.2
```

```
## [1] 56
```

```
## [1] 56
```

## Preprocess 

Using principal component analysis, one can decrease a number of variables (that is no the only advantage, of course). In my case, it is extremely essential due to the fact that I do not have a powerful machine. I have decided to use thresh = 0,9.



## Fitting a model 

After preprocessing I can fit my model. I have used CV, setting 8 folds. That should be quick enough as well as effective.


```
## Random Forest 
## 
## 13737 samples
##    20 predictor
##     5 classes: 'A', 'B', 'C', 'D', 'E' 
## 
## No pre-processing
## Resampling: Cross-Validated (8 fold) 
## Summary of sample sizes: 12020, 12020, 12020, 12020, 12020, 12021, ... 
## Resampling results across tuning parameters:
## 
##   mtry  Accuracy   Kappa    
##   1     0.9745936  0.9678620
##   2     0.9754677  0.9689676
##   3     0.9752492  0.9686895
##   4     0.9745940  0.9678620
##   5     0.9732840  0.9662067
## 
## Accuracy was used to select the optimal model using the largest value.
## The final value used for the model was mtry = 2.
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 1654   11    2    7    0
##          B   14 1100   21    3    1
##          C    2   12 1002   10    0
##          D    1    2   34  923    4
##          E    0    8    0    8 1066
## 
## Overall Statistics
##                                        
##                Accuracy : 0.9762       
##                  95% CI : (0.972, 0.98)
##     No Information Rate : 0.2839       
##     P-Value [Acc > NIR] : < 2.2e-16    
##                                        
##                   Kappa : 0.9699       
##                                        
##  Mcnemar's Test P-Value : NA           
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            0.9898   0.9709   0.9462   0.9706   0.9953
## Specificity            0.9953   0.9918   0.9950   0.9917   0.9967
## Pos Pred Value         0.9881   0.9658   0.9766   0.9575   0.9852
## Neg Pred Value         0.9960   0.9930   0.9883   0.9943   0.9990
## Prevalence             0.2839   0.1925   0.1799   0.1616   0.1820
## Detection Rate         0.2811   0.1869   0.1703   0.1568   0.1811
## Detection Prevalence   0.2845   0.1935   0.1743   0.1638   0.1839
## Balanced Accuracy      0.9925   0.9813   0.9706   0.9811   0.9960
```

We have got quite a good accuracy and stable cross validation performance.

## Predicting with the model 

First of all, I have to "clean" the new data and apply PCA preprocess. After that I can try my model, predicting results.


```
##  [1] B A A A A E D B A A B C B A E E A B B B
## Levels: A B C D E
```

I have choosed Random forest due to several factors:
 1) you can get feature importance;
 1) it is a robust method;
 2) you need not care about distribution;
 3) if you alter your RF well, it will not be very   time-consuming.
 
 Thanks for reading!
