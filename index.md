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

I have removed columns with NAs and empty values, thus the amount of remained vriables (y and all x) in both training and testing sets is 56 (I have also eliminated time).

```
## [1] 56
```

```
## [1] 56
```

## Preprocess 

Using principal component analysis, one can decrease a number of variables (that is no the only advantage, of course). In my case, it is extremely essential due to the fact that I do not have a powerful machine. I have decided to use thresh = 0,9.


```r
preprocess_f <- preProcess((train_f[,-56]), 
                method = c("pca", "scale",
                "center"), thresh = 0.9)
PCA <- predict(preprocess_f, (train_f[,-56]))
PCA2 <- predict(preprocess_f, (test_f[,-56]))

For_m <- data.frame(classe = train_f$classe, PCA)
For_t <- data.frame(classe = test_f$classe, PCA2)
preprocess_f
```

```
## Created from 13737 samples and 55 variables
## 
## Pre-processing:
##   - centered (55)
##   - ignored (0)
##   - principal component signal extraction (55)
##   - scaled (55)
## 
## PCA needed 20 components to capture 90 percent of the variance
```

## Fitting a model 

After preprocessing I can fit my model. I have used repeated CV, setting 5 folds. That should be quick enough as well as effective.


```r
set.seed(3333)
TC <- trainControl(method="repeatedcv", number=5,
      repeats=3)
GRID <- expand.grid(.mtry=c(1:15))
RF2 <- train(data=For_m, y=For_m[,1], x=For_m[,2:21],
       method="rf",trControl=TC, tuneGrid=GRID)
RF2
```

```
## Random Forest 
## 
## 13737 samples
##    20 predictor
##     5 classes: 'A', 'B', 'C', 'D', 'E' 
## 
## No pre-processing
## Resampling: Cross-Validated (5 fold, repeated 3 times) 
## Summary of sample sizes: 10989, 10990, 10990, 10989, 10990, 10991, ... 
## Resampling results across tuning parameters:
## 
##   mtry  Accuracy   Kappa    
##    1    0.9709789  0.9632892
##    2    0.9706876  0.9629219
##    3    0.9698141  0.9618184
##    4    0.9689162  0.9606810
##    5    0.9689161  0.9606835
##    6    0.9676302  0.9590574
##    7    0.9670720  0.9583503
##    8    0.9663196  0.9573981
##    9    0.9656160  0.9565098
##   10    0.9649849  0.9557113
##   11    0.9639174  0.9543602
##   12    0.9634808  0.9538075
##   13    0.9625101  0.9525798
##   14    0.9621461  0.9521216
##   15    0.9613938  0.9511679
## 
## Accuracy was used to select the optimal model using the largest value.
## The final value used for the model was mtry = 1.
```

```r
confusionMatrix(For_t$classe, predict(RF2, For_t))
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 1658   10    1    4    1
##          B   14 1102   18    3    2
##          C    2   12 1004    8    0
##          D    1    2   39  919    3
##          E    0    8    1   10 1063
## 
## Overall Statistics
##                                           
##                Accuracy : 0.9764          
##                  95% CI : (0.9722, 0.9801)
##     No Information Rate : 0.2846          
##     P-Value [Acc > NIR] : < 2.2e-16       
##                                           
##                   Kappa : 0.9701          
##                                           
##  Mcnemar's Test P-Value : 0.0001835       
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            0.9899   0.9718   0.9445   0.9735   0.9944
## Specificity            0.9962   0.9922   0.9954   0.9909   0.9961
## Pos Pred Value         0.9904   0.9675   0.9786   0.9533   0.9824
## Neg Pred Value         0.9960   0.9933   0.9879   0.9949   0.9988
## Prevalence             0.2846   0.1927   0.1806   0.1604   0.1816
## Detection Rate         0.2817   0.1873   0.1706   0.1562   0.1806
## Detection Prevalence   0.2845   0.1935   0.1743   0.1638   0.1839
## Balanced Accuracy      0.9930   0.9820   0.9700   0.9822   0.9952
```

We have got quite a good accuracy (Accuracy : 0.9764) and stable cross validation performance.

## Predicting with the model 

First of all, I have to "clean" the new data and apply PCA preprocess. After that I can try my model, predicting results.


```r
test_20 <- read.csv(file = "D:/pml-testing.csv", 
           sep = ",", header = TRUE, 
           stringsAsFactors = FALSE)

test_20_p <- Filter(function(x) !any(is.na(x)), test_20)
test_20_f <- data.frame(test_20_p[,!(colnames(test_20_p) %in% c("X","cvtd_timestamp","new_window","user_name","problem_id"))])

PCA3 <- predict(preprocess_f,(test_20_f))

predict(RF2, PCA3)
```

```
##  [1] B A A A A E D B A A B C B A E E A B B B
## Levels: A B C D E
```

I have chosen Random forest due to several factors:
 1) you can get feature importance;
 1) it is a robust method;
 2) you need not care about distribution;
 3) if you alter your RF well, it will not be very time-consuming.
 
 Thanks for reading!
