---
title: "Statistic Basics and Linear Regression"
author: "Erick Jones"
date: 2020-01-01
categories: ["ORIE Basics"]
tags: ["R Markdown", "Regression", "Statistics", "Statistical Testing"]
---

## Background

This post explores some of the basic concepts of statistics. 
I mostly explore these concepts using linear regression.
This is a reproducible example if you have R Studio just  make sure you have installed the correct packages.







```r
library(DataExplorer)
```

```
## Warning: package 'DataExplorer' was built under R version 4.1.2
```

```r
library(tidyverse)
```

```
## Warning: package 'tidyverse' was built under R version 4.1.2
```

```
## Warning: package 'ggplot2' was built under R version 4.1.2
```

```
## Warning: package 'tibble' was built under R version 4.1.2
```

```
## Warning: package 'tidyr' was built under R version 4.1.2
```

```
## Warning: package 'readr' was built under R version 4.1.2
```

```
## Warning: package 'purrr' was built under R version 4.1.2
```

```
## Warning: package 'dplyr' was built under R version 4.1.2
```

```
## Warning: package 'stringr' was built under R version 4.1.2
```

```
## Warning: package 'forcats' was built under R version 4.1.2
```

```r
library(dplyr)
library(caret)
```

```
## Warning: package 'caret' was built under R version 4.1.2
```

```r
library(lmtest)
```

```
## Warning: package 'lmtest' was built under R version 4.1.2
```

```
## Warning: package 'zoo' was built under R version 4.1.2
```

```r
set.seed(1234)
```


## Datasets and Sources

http://r-statistics.co/Linear-Regression.html
https://www.statmethods.net/stats/regression.html
http://r-statistics.co/Statistical-Tests-in-R.html
http://www.sthda.com/english/articles/40-regression-analysis/166-predict-in-r-model-predictions-and-confidence-intervals/

Dr. Sager Utexas datasets


```r
data <- read.table('AustinApartmentRents1.txt', header = TRUE)
summary(data)
```

```
##       Rent             Area     
##  Min.   : 399.0   Min.   : 474  
##  1st Qu.: 470.0   1st Qu.: 666  
##  Median : 535.0   Median : 755  
##  Mean   : 572.3   Mean   : 816  
##  3rd Qu.: 638.8   3rd Qu.: 925  
##  Max.   :1050.0   Max.   :1864
```

```r
cor(data$Rent, data$Area)
```

```
## [1] 0.8740597
```

```r
model <- lm(Rent ~ Area, data = data)

summary(model)
```

```
## 
## Call:
## lm(formula = Rent ~ Area, data = data)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -154.659  -50.882    8.189   54.874  148.207 
## 
## Coefficients:
##              Estimate Std. Error t value Pr(>|t|)    
## (Intercept) 160.18706   31.36081   5.108  3.8e-06 ***
## Area          0.50497    0.03685  13.702  < 2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 68.86 on 58 degrees of freedom
## Multiple R-squared:  0.764,	Adjusted R-squared:  0.7599 
## F-statistic: 187.7 on 1 and 58 DF,  p-value: < 2.2e-16
```

```r
test.Areas <- data.frame(Area = c (500,1000))
predict(model, newdata = test.Areas)
```

```
##        1        2 
## 412.6713 665.1556
```

```r
data1 <- read.table('AustinApartmentRents2.txt', header = TRUE)
```


## Data Explorer

A convient tool to see a lot of the initial data exploration
https://towardsdatascience.com/simple-fast-exploratory-data-analysis-in-r-with-dataexplorer-package-e055348d9619


```r
plot_str(data1)
plot_missing(data1)
```

<img src="/blog/ORIE/Stat Basics/Stat_basics_files/figure-html/unnamed-chunk-3-1.png" width="672" />

```r
plot_histogram(data1)
```

<img src="/blog/ORIE/Stat Basics/Stat_basics_files/figure-html/unnamed-chunk-3-2.png" width="672" />

```r
plot_density(data1)
```

<img src="/blog/ORIE/Stat Basics/Stat_basics_files/figure-html/unnamed-chunk-3-3.png" width="672" />

```r
plot_correlation(data1)
```

<img src="/blog/ORIE/Stat Basics/Stat_basics_files/figure-html/unnamed-chunk-3-4.png" width="672" />

```r
plot_bar(data1)
```

<img src="/blog/ORIE/Stat Basics/Stat_basics_files/figure-html/unnamed-chunk-3-5.png" width="672" />

```r
#create_report(data1) #This creates an HTML report of all the above information and more
```


## Confidence Intervals


```r
#Confidence intervals around indivudual values
pred.int <- predict(model, interval = 'prediction')
```

```
## Warning in predict.lm(model, interval = "prediction"): predictions on current data refer to _future_ responses
```

```r
#Confidence intervals around means
pred.conf <- predict(model, interval = 'confidence')

cbind(data,pred.int,pred.conf)
```

```
##    Rent Area       fit      lwr       upr       fit       lwr       upr
## 1   519  725  526.2893 387.1539  665.4247  526.2893  507.2700  545.3085
## 2   765  995  662.6308 523.0320  802.2296  662.6308  640.4747  684.7868
## 3   475  481  403.0769 261.9228  544.2310  403.0769  372.6213  433.5326
## 4   575  925  627.2830 488.0776  766.4884  627.2830  607.7583  646.8077
## 5   415  600  463.1682 323.2840  603.0524  463.1682  439.2801  487.0563
## 6   530  668  497.5061 358.1044  636.9078  497.5061  476.6278  518.3843
## 7   580  725  526.2893 387.1539  665.4247  526.2893  507.2700  545.3085
## 8   995 1421  877.7474 731.7844 1023.7104  877.7474  829.7031  925.7917
## 9   565  672  499.5259 360.1470  638.9048  499.5259  478.8005  520.2514
## 10  620 1025  677.7799 537.9544  817.6053  677.7799  654.2379  701.3218
## 11  450  781  554.5675 415.5703  693.5648  554.5675  536.5869  572.5481
## 12  520  800  564.1619 425.1837  703.1402  564.1619  546.3289  581.9950
## 13  495  870  599.5097 460.4795  738.5399  599.5097  581.2764  617.7431
## 14  420  700  513.6651 374.4284  652.9017  513.6651  493.9190  533.4112
## 15  575  800  564.1619 425.1837  703.1402  564.1619  546.3289  581.9950
## 16  425  620  473.2676 333.5438  612.9913  473.2676  450.3375  496.1977
## 17  770 1040  685.3544 545.4026  825.3061  685.3544  661.0735  709.6352
## 18  445  520  422.7707 282.0919  563.4495  422.7707  394.5998  450.9416
## 19  510  880  604.5594 465.5062  743.6127  604.5594  586.1509  622.9679
## 20  635  832  580.3209 441.3427  719.2991  580.3209  562.4884  598.1535
## 21  470  545  435.3949 294.9906  575.7993  435.3949  408.6285  462.1614
## 22  700  921  625.2631 486.0744  764.4518  625.2631  605.8580  644.6682
## 23  450  577  451.5539 311.4663  591.6416  451.5539  426.5018  476.6060
## 24  785 1080  705.5531 565.2224  845.8838  705.5531  679.1757  731.9306
## 25  485  710  518.7147 379.5215  657.9080  518.7147  499.2771  538.1524
## 26  415  605  465.6930 325.8504  605.5357  465.6930  442.0494  489.3367
## 27  399  680  503.5657 364.2305  642.9008  503.5657  483.1366  523.9948
## 28  585  730  528.8141 389.6960  667.9322  528.8141  509.9220  547.7063
## 29  525  687  507.1005 367.8016  646.3994  507.1005  486.9201  527.2809
## 30  495  703  515.1800 375.9568  654.4032  515.1800  495.5288  534.8311
## 31  505  672  499.5259 360.1470  638.9048  499.5259  478.8005  520.2514
## 32  445  660  493.4663 354.0171  632.9155  493.4663  472.2734  514.6593
## 33  565  755  541.4383 402.3922  680.4845  541.4383  523.0835  559.7931
## 34  650  810  569.2116 430.2377  708.1855  569.2116  551.4123  587.0109
## 35  515  611  468.7229 328.9288  608.5169  468.7229  445.3683  492.0774
## 36  470  705  516.1899 376.9755  655.4044  516.1899  496.6009  535.7789
## 37  470  564  444.9893 304.7778  585.2009  444.9893  419.2531  470.7255
## 38  700 1250  791.3978 648.7851  934.0105  791.3978  754.7720  828.0235
## 39  455  512  418.7310 277.9593  559.5026  418.7310  390.1001  447.3618
## 40  550  630  478.3173 338.6680  617.9666  478.3173  455.8452  500.7893
## 41  625  850  589.4103 450.4146  728.4061  589.4103  571.4413  607.3794
## 42  745 1156  743.9307 602.7129  885.1486  743.9307  713.1810  774.6805
## 43  540  932  630.8178 491.5816  770.0540  630.8178  611.0749  650.5607
## 44  650  755  541.4383 402.3922  680.4845  541.4383  523.0835  559.7931
## 45  595 1093  712.1177 571.6507  852.5847  712.1177  685.0246  739.2108
## 46  470  751  539.4185 400.3624  678.4745  539.4185  520.9890  557.8479
## 47  480  608  467.2080 327.3898  607.0261  467.2080  443.7095  490.7064
## 48  460  900  614.6588 475.5477  753.7699  614.6588  595.8181  633.4994
## 49  600  860  594.4600 455.4490  733.4711  594.4600  576.3734  612.5467
## 50  575  925  627.2830 488.0776  766.4884  627.2830  607.7583  646.8077
## 51  659  944  636.8774 497.5840  776.1708  636.8774  616.7351  657.0197
## 52  650  940  634.8575 495.5838  774.1312  634.8575  614.8518  654.8632
## 53  750 1048  689.3941 549.3715  829.4168  689.3941  664.7079  714.0803
## 54  455  474  399.5422 258.2967  540.7876  399.5422  368.6660  430.4184
## 55  430  700  513.6651 374.4284  652.9017  513.6651  493.9190  533.4112
## 56  605  921  625.2631 486.0744  764.4518  625.2631  605.8580  644.6682
## 57  929 1229  780.7934 638.5205  923.0664  780.7934  745.5138  816.0731
## 58  695  896  612.6389 473.5406  751.7372  612.6389  593.8932  631.3846
## 59  455  630  478.3173 338.6680  617.9666  478.3173  455.8452  500.7893
## 60 1050 1864 1101.4485 942.4198 1260.4772 1101.4485 1022.1188 1180.7782
```

## Regression line + confidence intervals


```r
mydata <- cbind(data, pred.int)

p <- ggplot(mydata, aes(Area, Rent)) +
  geom_point() +
  stat_smooth(method = lm)

p
```

<img src="/blog/ORIE/Stat Basics/Stat_basics_files/figure-html/unnamed-chunk-5-1.png" width="672" />

# Prediction intervals


```r
p + geom_line(aes(y = lwr), color = "red", linetype = "dashed")+
    geom_line(aes(y = upr), color = "red", linetype = "dashed")
```

<img src="/blog/ORIE/Stat Basics/Stat_basics_files/figure-html/unnamed-chunk-6-1.png" width="672" />


## T Test for samples


```r
sample1 <- sample_n(data,40)

model1 <- lm(Rent ~ Area, data = data)

p1 <- predict(model1, interval = 'confidence', level = 0.95)

summary(p1)
```

```
##       fit              lwr              upr        
##  Min.   : 399.5   Min.   : 368.7   Min.   : 430.4  
##  1st Qu.: 496.5   1st Qu.: 475.5   1st Qu.: 517.5  
##  Median : 541.4   Median : 523.1   Median : 559.8  
##  Mean   : 572.3   Mean   : 548.8   Mean   : 595.7  
##  3rd Qu.: 627.3   3rd Qu.: 607.8   3rd Qu.: 646.8  
##  Max.   :1101.4   Max.   :1022.1   Max.   :1180.8
```

```r
t.test(p1, mu = 550)
```

```
## 
## 	One Sample t-test
## 
## data:  p1
## t = 2.4118, df = 179, p-value = 0.01688
## alternative hypothesis: true mean is not equal to 550
## 95 percent confidence interval:
##  554.0485 590.4849
## sample estimates:
## mean of x 
##  572.2667
```

## MultiVariable Linear Regression


```r
summary(data1)
```

```
##       Rent             Area         Bedrooms       Bathrooms   
##  Min.   : 399.0   Min.   : 474   Min.   :1.000   Min.   :1.00  
##  1st Qu.: 470.0   1st Qu.: 666   1st Qu.:1.000   1st Qu.:1.00  
##  Median : 535.0   Median : 755   Median :1.000   Median :1.00  
##  Mean   : 572.3   Mean   : 816   Mean   :1.517   Mean   :1.25  
##  3rd Qu.: 638.8   3rd Qu.: 925   3rd Qu.:2.000   3rd Qu.:1.25  
##  Max.   :1050.0   Max.   :1864   Max.   :5.000   Max.   :2.00  
##     Security         Parking          Distance         Shuttle      
##  Min.   :0.0000   Min.   :0.0000   Min.   : 1.100   Min.   :0.0000  
##  1st Qu.:0.0000   1st Qu.:0.0000   1st Qu.: 5.000   1st Qu.:1.0000  
##  Median :0.0000   Median :0.0000   Median : 6.000   Median :1.0000  
##  Mean   :0.1667   Mean   :0.1333   Mean   : 5.935   Mean   :0.8667  
##  3rd Qu.:0.0000   3rd Qu.:0.0000   3rd Qu.: 7.000   3rd Qu.:1.0000  
##  Max.   :1.0000   Max.   :1.0000   Max.   :10.500   Max.   :1.0000  
##       Age       
##  Min.   : 1.00  
##  1st Qu.:10.00  
##  Median :16.50  
##  Mean   :16.33  
##  3rd Qu.:22.25  
##  Max.   :32.00
```

```r
model2 <- lm(Rent ~ Area + Bathrooms, data = data1)
summary(model2)
```

```
## 
## Call:
## lm(formula = Rent ~ Area + Bathrooms, data = data1)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -152.02  -45.45   10.38   39.91  129.28 
## 
## Coefficients:
##              Estimate Std. Error t value Pr(>|t|)    
## (Intercept) 143.66927   29.51345   4.868 9.31e-06 ***
## Area          0.38746    0.04982   7.777 1.61e-10 ***
## Bathrooms    89.92902   27.75071   3.241  0.00199 ** 
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 63.83 on 57 degrees of freedom
## Multiple R-squared:  0.8007,	Adjusted R-squared:  0.7937 
## F-statistic: 114.5 on 2 and 57 DF,  p-value: < 2.2e-16
```

```r
test.bathrooms <- data.frame(Area = c(500,1000), Bathrooms = c(1,2))

p2a <- predict(model2, newdata = test.bathrooms, interval =  'confidence')


p2 <- as.data.frame(predict(model2, interval = 'confidence', level = 0.95))

cbind(p2, data1)
```

```
##          fit      lwr       upr Rent Area Bedrooms Bathrooms Security Parking
## 1   514.5062 495.4259  533.5866  519  725        1         1        0       0
## 2   709.0493 673.7669  744.3316  765  995        2         2        0       0
## 3   419.9662 389.8581  450.0743  475  481        1         1        0       0
## 4   681.9271 643.6132  720.2411  575  925        2         2        0       1
## 5   466.0738 443.8498  488.2979  415  600        1         1        0       0
## 6   492.4211 472.8074  512.0348  530  668        1         1        0       0
## 7   514.5062 495.4259  533.5866  580  725        1         1        0       0
## 8   874.1069 829.4987  918.7151  995 1421        2         2        0       1
## 9   493.9709 474.4481  513.4937  565  672        1         1        0       0
## 10  630.7440 594.3939  667.0941  620 1025        2         1        1       0
## 11  536.2040 516.0356  556.3724  450  781        1         1        1       0
## 12  543.5657 522.6986  564.4328  520  800        2         1        0       0
## 13  570.6878 546.1304  595.2452  495  870        2         1        0       0
## 14  504.8198 485.7109  523.9287  420  700        1         1        0       0
## 15  543.5657 522.6986  564.4328  575  800        1         1        0       0
## 16  473.8230 452.5572  495.0888  425  620        1         1        0       0
## 17  726.4849 692.5303  760.4395  770 1040        2         2        0       1
## 18  435.0771 407.8700  462.2843  445  520        1         1        0       0
## 19  574.5624 549.3637  599.7611  510  880        2         1        0       1
## 20  555.9644 533.6045  578.3243  635  832        1         1        0       0
## 21  444.7636 419.2769  470.2502  470  545        1         1        0       0
## 22  680.3773 641.8590  718.8955  700  921        2         2        0       0
## 23  457.1623 433.6744  480.6501  450  577        1         1        0       0
## 24  741.9833 708.7412  775.2254  785 1080        2         2        0       0
## 25  508.6944 489.6360  527.7527  485  710        1         1        0       0
## 26  468.0111 446.0397  489.9826  415  605        1         1        0       0
## 27  497.0706 477.7062  516.4349  399  680        1         1        0       1
## 28  516.4435 497.3298  535.5573  585  730        2         1        0       0
## 29  499.7828 480.5310  519.0346  525  687        1         1        0       0
## 30  505.9821 486.8939  525.0704  495  703        1         1        0       0
## 31  493.9709 474.4481  513.4937  505  672        1         1        1       0
## 32  489.3214 469.5030  509.1398  445  660        1         1        0       0
## 33  526.1300 506.6576  545.6024  565  755        2         1        0       0
## 34  547.4403 526.1468  568.7337  650  810        2         1        0       0
## 35  470.3359 448.6563  492.0154  515  611        1         1        0       0
## 36  506.7571 487.6799  525.8342  470  705        1         1        0       0
## 37  452.1253 427.8562  476.3944  470  564        1         1        0       0
## 38  807.8514 772.3992  843.3035  700 1250        3         2        0       1
## 39  431.9774 404.1949  459.7599  455  512        1         1        1       0
## 40  477.6976 456.8558  498.5395  550  630        1         1        0       0
## 41  562.9387 539.5887  586.2886  625  850        2         1        1       0
## 42  771.4302 738.2367  804.6237  745 1156        3         2        0       0
## 43  594.7103 565.8488  623.5718  540  932        2         1        0       0
## 44  526.1300 506.6576  545.6024  650  755        1         1        1       1
## 45  747.0203 713.9093  780.1312  595 1093        2         2        1       0
## 46  524.5802 505.1862  543.9741  470  751        1         1        1       0
## 47  469.1735 447.3496  490.9974  480  608        1         1        0       0
## 48  582.3116 555.7642  608.8590  460  900        1         1        0       1
## 49  566.8132 542.8728  590.7537  600  860        2         1        0       0
## 50  591.9981 563.6574  620.3388  575  925        2         1        0       0
## 51  689.2888 651.9023  726.6754  659  944        2         2        0       0
## 52  687.7390 650.1632  725.3148  650  940        2         2        0       0
## 53  729.5846 695.8090  763.3602  750 1048        2         2        0       0
## 54  417.2540 386.6020  447.9060  455  474        1         1        0       0
## 55  504.8198 485.7109  523.9287  430  700        1         1        0       0
## 56  590.4483 562.4017  618.4949  605  921        1         1        0       0
## 57  799.7147 764.9734  834.4561  929 1229        2         2        1       0
## 58  670.6908 630.8290  710.5526  695  896        2         2        0       0
## 59  477.6976 456.8558  498.5395  455  630        1         1        1       0
## 60 1045.7513 964.5360 1126.9667 1050 1864        5         2        0       0
##    Distance Shuttle Age
## 1      10.5       1   9
## 2       6.5       1  17
## 3       6.5       1  17
## 4       4.0       1   9
## 5       5.0       1  30
## 6       6.5       1  19
## 7       7.0       1  17
## 8       6.5       1  16
## 9       7.0       1  17
## 10      5.0       1   3
## 11      5.5       1   3
## 12      6.0       1  20
## 13      5.0       1  27
## 14      6.0       1  22
## 15      7.0       1  10
## 16      8.0       0  27
## 17      6.5       1  16
## 18      3.0       1  12
## 19      7.0       0  25
## 20      6.0       1  13
## 21      6.5       1   9
## 22      3.0       1  26
## 23      8.0       1  18
## 24      5.0       1  10
## 25      6.0       1  25
## 26      6.0       1  22
## 27      7.0       0  25
## 28      6.5       1  19
## 29      7.0       1  15
## 30      6.5       1  14
## 31      6.5       1   9
## 32      6.0       1  25
## 33      3.0       1  12
## 34      2.0       1  32
## 35      6.5       1  17
## 36      7.5       0  13
## 37      5.0       1  10
## 38      4.0       1   9
## 39     10.0       0  10
## 40      2.0       1  32
## 41      7.0       1   1
## 42      7.5       0  13
## 43      6.0       1  22
## 44      1.1       1  26
## 45      5.5       1   3
## 46      5.0       1   3
## 47      6.0       1  15
## 48      4.0       1   9
## 49      6.0       1  25
## 50      6.0       1  23
## 51      7.0       1  25
## 52      8.0       0  27
## 53      7.0       1   3
## 54      5.0       1  10
## 55      6.0       1  20
## 56      7.5       0  13
## 57      5.0       1  11
## 58      6.5       1  19
## 59      5.5       1   9
## 60      6.0       1  22
```

```r
p2a
```

```
##        fit      lwr      upr
## 1 427.3279 398.6624 455.9935
## 2 710.9866 675.8776 746.0955
```

```r
model3 <- lm(Rent ~ Area + Bathrooms + Security + Parking + Distance, data = data1)
summary(model3)
```

```
## 
## Call:
## lm(formula = Rent ~ Area + Bathrooms + Security + Parking + Distance, 
##     data = data1)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -163.199  -37.278    4.548   38.345  149.276 
## 
## Coefficients:
##              Estimate Std. Error t value Pr(>|t|)    
## (Intercept) 167.45045   43.12346   3.883 0.000283 ***
## Area          0.39647    0.05084   7.798 2.09e-10 ***
## Bathrooms    92.60040   28.01804   3.305 0.001691 ** 
## Security     -0.67875   22.42818  -0.030 0.975969    
## Parking     -38.26531   25.89638  -1.478 0.145316    
## Distance     -4.92937    5.05014  -0.976 0.333374    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 64.02 on 54 degrees of freedom
## Multiple R-squared:   0.81,	Adjusted R-squared:  0.7925 
## F-statistic: 46.06 on 5 and 54 DF,  p-value: < 2.2e-16
```

```r
model4 <- lm(Rent ~ Distance + Parking + Security + Bathrooms + Area, data = data1)
summary(model4)
```

```
## 
## Call:
## lm(formula = Rent ~ Distance + Parking + Security + Bathrooms + 
##     Area, data = data1)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -163.199  -37.278    4.548   38.345  149.276 
## 
## Coefficients:
##              Estimate Std. Error t value Pr(>|t|)    
## (Intercept) 167.45045   43.12346   3.883 0.000283 ***
## Distance     -4.92937    5.05014  -0.976 0.333374    
## Parking     -38.26531   25.89638  -1.478 0.145316    
## Security     -0.67875   22.42818  -0.030 0.975969    
## Bathrooms    92.60040   28.01804   3.305 0.001691 ** 
## Area          0.39647    0.05084   7.798 2.09e-10 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 64.02 on 54 degrees of freedom
## Multiple R-squared:   0.81,	Adjusted R-squared:  0.7925 
## F-statistic: 46.06 on 5 and 54 DF,  p-value: < 2.2e-16
```


## Multicollinearity
http://www.sthda.com/english/articles/39-regression-model-diagnostics/160-multicollinearity-essentials-and-vif-in-r/



```r
y <- runif(50,min=0, max =100)
x1 <- runif(50, min = 0, max = 100)
x2 <- runif(50, min = 0, max = 100)
z1 <- x1+x2
z2 <- x1 + x2 + 0.005*runif(50,min=0, max =100)



list <- cbind(y,x1,x2,z1,z2)
list
```

```
##               y        x1        x2        z1        z2
##  [1,] 76.545979 74.211966 57.681274 131.89324 132.21284
##  [2,]  7.377988  3.545673 43.904205  47.44988  47.78026
##  [3,] 30.968660 56.507611 22.859970  79.36758  79.63176
##  [4,] 71.727174 28.025778  8.215807  36.24158  36.40033
##  [5,] 50.454591 20.419632 85.026492 105.44612 105.83005
##  [6,] 15.299896 13.373890 23.466126  36.84002  37.10317
##  [7,] 50.393349 32.568192 98.816745 131.38494 131.75109
##  [8,] 49.396092 15.506197 60.189755  75.69595  75.84978
##  [9,] 75.120020 12.996214 99.874081 112.87029 113.07238
## [10,] 17.464982 43.553106 37.559938  81.11304  81.21524
## [11,] 84.839241  3.864265 55.512663  59.37693  59.86974
## [12,] 86.483383 71.330156 42.944396 114.27455 114.55771
## [13,]  4.185728 10.076904 57.587778  67.66468  67.80487
## [14,] 31.718216 95.030494 43.250740 138.28123 138.37376
## [15,]  1.374994 12.181776 22.484576  34.66635  35.04538
## [16,] 23.902573 21.965662  8.498474  30.46414  30.74753
## [17,] 70.649462 91.308777 63.729826 155.03860 155.50469
## [18,] 30.809476 94.585312 43.101637 137.68695 138.00630
## [19,] 50.854757 27.915622  7.271609  35.18723  35.53761
## [20,]  5.164662 12.347109 80.240202  92.58731  92.82692
## [21,] 56.456984 79.716046 32.527830 112.24388 112.66903
## [22,] 12.148019 74.427722 75.728904 150.15663 150.36779
## [23,] 89.283638 91.597422 58.427152 150.02457 150.04027
## [24,]  1.462726 99.459825 70.883941 170.34377 170.47284
## [25,] 78.312110 94.236072 42.697577 136.93365 137.10107
## [26,]  8.996133 48.613541 34.357270  82.97081  83.03759
## [27,] 51.918998 28.345954 75.911999 104.25795 104.50773
## [28,] 38.426669 25.154570 42.403021  67.55759  67.95866
## [29,]  7.005250 50.325517 56.088725 106.41424 106.58282
## [30,] 32.064442 49.696617 11.613577  61.31019  61.56466
## [31,] 66.849540 31.844581 30.302180  62.14676  62.39398
## [32,] 92.640048 96.222283 47.880269 144.10255 144.50108
## [33,] 47.190972 63.409937 34.483055  97.89299  98.17647
## [34,] 14.261534 12.743340 60.071414  72.81475  72.86810
## [35,] 54.426976 42.304699  7.608332  49.91303  50.31686
## [36,] 19.617465 91.431691 95.599261 187.03095 187.31451
## [37,] 89.858049 46.779233  2.220682  48.99992  49.10604
## [38,] 38.949978 90.816915 84.171063 174.98798 175.36277
## [39,] 31.087078 59.774328 63.244245 123.01857 123.17218
## [40,] 16.002866 63.174282 31.009417  94.18370  94.42846
## [41,] 89.618585 86.915832 74.256937 161.17277 161.66762
## [42,] 16.639378 50.274982 63.891131 114.16611 114.37817
## [43,] 90.042460 98.363511 99.251599 197.61511 197.73731
## [44,] 13.407820 32.438603 12.826979  45.26558  45.37415
## [45,] 13.161413 48.137495 88.323958 136.46145 136.80601
## [46,] 10.528750 35.698708 81.008339 116.70705 117.19715
## [47,] 51.158358 62.747768 82.185118 144.93289 145.17140
## [48,] 30.019905 74.160019 83.470266 157.63029 158.01705
## [49,]  2.671690 56.596682 73.273222 129.86990 130.15706
## [50,] 30.964743 98.078651 98.304402 196.38305 196.86602
```

```r
cor(list)
```

```
##              y        x1          x2        z1        z2
## y   1.00000000 0.2652428 -0.02304115 0.1606774 0.1609984
## x1  0.26524277 1.0000000  0.24163581 0.8061092 0.8059147
## x2 -0.02304115 0.2416358  1.00000000 0.7690159 0.7692199
## z1  0.16067742 0.8061092  0.76901586 1.0000000 0.9999963
## z2  0.16099839 0.8059147  0.76921995 0.9999963 1.0000000
```

```r
#model catches exact multicollinearity easily
mcmodel1 <- lm(y ~ x1+x2+z1+z2)
summary(mcmodel1)
```

```
## 
## Call:
## lm(formula = y ~ x1 + x2 + z1 + z2)
## 
## Residuals:
##    Min     1Q Median     3Q    Max 
## -45.22 -22.54  -9.66  23.18  53.40 
## 
## Coefficients: (1 not defined because of singularities)
##             Estimate Std. Error t value Pr(>|t|)  
## (Intercept)    23.39      12.67   1.846   0.0713 .
## x1            -34.20      32.75  -1.044   0.3018  
## x2            -34.60      32.78  -1.056   0.2967  
## z1                NA         NA      NA       NA  
## z2             34.48      32.75   1.053   0.2980  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 28.64 on 46 degrees of freedom
## Multiple R-squared:  0.1001,	Adjusted R-squared:  0.0414 
## F-statistic: 1.705 on 3 and 46 DF,  p-value: 0.179
```

```r
#has a harder time catching near multicollinearity useful to use VIF or tolerance
mcmodel2 <- lm(y ~ x1+x2+z2)
summary(mcmodel2)
```

```
## 
## Call:
## lm(formula = y ~ x1 + x2 + z2)
## 
## Residuals:
##    Min     1Q Median     3Q    Max 
## -45.22 -22.54  -9.66  23.18  53.40 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)  
## (Intercept)    23.39      12.67   1.846   0.0713 .
## x1            -34.20      32.75  -1.044   0.3018  
## x2            -34.60      32.78  -1.056   0.2967  
## z2             34.48      32.75   1.053   0.2980  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 28.64 on 46 degrees of freedom
## Multiple R-squared:  0.1001,	Adjusted R-squared:  0.0414 
## F-statistic: 1.705 on 3 and 46 DF,  p-value: 0.179
```

```r
car::vif(mcmodel2)
```

```
##        x1        x2        z2 
##  60003.52  51509.41 138378.52
```

```r
#Can drop the collinear term
mcmodel3 <- lm(y ~ x1+x2)
summary(mcmodel3)
```

```
## 
## Call:
## lm(formula = y ~ x1 + x2)
## 
## Residuals:
##    Min     1Q Median     3Q    Max 
## -50.39 -22.36 -11.18  21.35  57.80 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)   
## (Intercept) 31.28004   10.22631   3.059  0.00366 **
## x1           0.27492    0.13794   1.993  0.05208 . 
## x2          -0.09555    0.14901  -0.641  0.52447   
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 28.67 on 47 degrees of freedom
## Multiple R-squared:  0.07842,	Adjusted R-squared:  0.0392 
## F-statistic:     2 on 2 and 47 DF,  p-value: 0.1467
```

```r
car::vif(mcmodel3)
```

```
##       x1       x2 
## 1.062008 1.062008
```

```r
#Or drop the other one
mcmodel4 <- lm(y ~ x1+z2)
summary(mcmodel4)
```

```
## 
## Call:
## lm(formula = y ~ x1 + z2)
## 
## Residuals:
##    Min     1Q Median     3Q    Max 
## -50.41 -22.36 -11.17  21.34  57.81 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)   
## (Intercept) 31.27192   10.24772   3.052  0.00374 **
## x1           0.36952    0.22611   1.634  0.10889   
## z2          -0.09476    0.14890  -0.636  0.52758   
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 28.67 on 47 degrees of freedom
## Multiple R-squared:  0.0783,	Adjusted R-squared:  0.03908 
## F-statistic: 1.996 on 2 and 47 DF,  p-value: 0.1472
```

```r
car::vif(mcmodel4)
```

```
##       x1       z2 
## 2.853055 2.853055
```


## Normality check
https://www.statmethods.net/stats/regression.html



```r
nmodel <- summary(model2)
nmodel$residuals
```

```
##           1           2           3           4           5           6 
##    4.493754   55.950733   55.033809 -106.927120  -51.073841   37.578930 
##           7           8           9          10          11          12 
##   65.493754  120.893095   71.029094  -10.744020  -86.203964  -23.565690 
##          13          14          15          16          17          18 
##  -75.687837  -84.819765   31.434310  -48.823026   43.515067    9.922899 
##          19          20          21          22          23          24 
##  -64.562429   79.035614   25.236418   19.622717   -7.162278   43.016698 
##          25          26          27          28          29          30 
##  -23.694358  -53.011137  -98.070580   68.556457   25.217205  -10.982143 
##          31          32          33          34          35          36 
##   11.029094  -44.321396   38.869976  102.559718   44.664107  -36.757062 
##          37          38          39          40          41          42 
##   17.874692 -107.851374   23.022573   72.302382   62.061348  -26.430205 
##          43          44          45          46          47          48 
##  -54.710310  123.869976 -152.020273  -54.580187   10.826485 -122.311614 
##          49          50          51          52          53          54 
##   33.186756  -16.998095  -30.288845  -37.739008   20.415393   37.746024 
##          55          56          57          58          59          60 
##  -74.819765   14.551742  129.285270   24.309199  -22.697618    4.248650
```

```r
layout(matrix(c(1,2,3,4),2,2)) # optional 4 graphs/page
plot(model2)
```

<img src="/blog/ORIE/Stat Basics/Stat_basics_files/figure-html/unnamed-chunk-11-1.png" width="672" />

```r
hist(nmodel$residuals)

#Runs different tests for normality run from the predicted values for rent

p3 <- predict(model2)
summary(p3)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##   417.3   491.6   526.1   572.3   640.7  1045.8
```

```r
sd(p3)
```

```
## [1] 125.7441
```

```r
#H0: from normal distribution p < 0.05 reject
shapiro.test(p3)
```

```
## 
## 	Shapiro-Wilk normality test
## 
## data:  p3
## W = 0.86842, p-value = 1.114e-05
```

```r
#compares if two samples are from same distribution so comparing to a normal distribution H0: from different distributions p < 0.05 reject
ks.test(p3, rnorm(60,572.3,125.7441))
```

```
## Warning in ks.test(p3, rnorm(60, 572.3, 125.7441)): cannot compute exact p-value
## with ties
```

```
## 
## 	Two-sample Kolmogorov-Smirnov test
## 
## data:  p3 and rnorm(60, 572.3, 125.7441)
## D = 0.15, p-value = 0.5095
## alternative hypothesis: two-sided
```

<img src="/blog/ORIE/Stat Basics/Stat_basics_files/figure-html/unnamed-chunk-11-2.png" width="672" />

## Linearity Test
https://bookdown.org/ccolonescu/RPoE4/further-inference-in-multiple-regression.html
http://r-statistics.co/Statistical-Tests-in-R.html



```r
#Ramsey RESET test test whether higher order polynomials are necessary H0: no higher order polynomials are necssary
resettest(model3, power = 2:3, type = 'fitted')
```

```
## 
## 	RESET test
## 
## data:  model3
## RESET = 1.3333, df1 = 2, df2 = 52, p-value = 0.2725
```

```r
resettest(model3, power = 2:3, type = 'regressor')
```

```
## 
## 	RESET test
## 
## data:  model3
## RESET = 2.0443, df1 = 10, df2 = 44, p-value = 0.05111
```

## Heteroscedasticity

Put simply, heteroscedasticity (also spelled heteroskedasticity) refers to the circumstance in which the variability of a variable is unequal across the range of values of a second variable that predicts it.
http://www.statsmakemecry.com/smmctheblog/confusing-stats-terms-explained-heteroscedasticity-heteroske.html


Both of these tests use log parameters as well as lag and leads to determine if the variance changes or if the predictors are truly independant of each other


```r
#Fisher Test can be used to tell if two samples have the same variance H0: ratio of variances is 1 aka they are the same p < 0.05 reject H0
var.test(sample(35,p3, replace = TRUE),sample(35,p3, replace = TRUE))
```

```
## 
## 	F test to compare two variances
## 
## data:  sample(35, p3, replace = TRUE) and sample(35, p3, replace = TRUE)
## F = 0.94881, num df = 513, denom df = 513, p-value = 0.552
## alternative hypothesis: true ratio of variances is not equal to 1
## 95 percent confidence interval:
##  0.7978745 1.1283080
## sample estimates:
## ratio of variances 
##          0.9488141
```

```r
#Independence 

#Chi square tests if two caterogical variables are dependant on each other H0: variables are independant p < 0.05 reject H0
chisq.test(table(data1$Bedrooms,data1$Bathrooms))
```

```
## Warning in chisq.test(table(data1$Bedrooms, data1$Bathrooms)): Chi-squared
## approximation may be incorrect
```

```
## 
## 	Pearson's Chi-squared test
## 
## data:  table(data1$Bedrooms, data1$Bathrooms)
## X-squared = 29.391, df = 3, p-value = 1.853e-06
```

```r
summary(table(data1$Bedrooms,data1$Bathrooms))
```

```
## Number of cases in table: 60 
## Number of factors: 2 
## Test for independence of all factors:
## 	Chisq = 29.391, df = 3, p-value = 1.853e-06
## 	Chi-squared approximation may be incorrect
```

```r
#Correlation test between two continuous variables H0: correlation is 0 aka they are independant p < 0.05 reject H0

#All show some correlation
cor.test(data1$Rent, data1$Area)
```

```
## 
## 	Pearson's product-moment correlation
## 
## data:  data1$Rent and data1$Area
## t = 13.702, df = 58, p-value < 2.2e-16
## alternative hypothesis: true correlation is not equal to 0
## 95 percent confidence interval:
##  0.7970327 0.9231055
## sample estimates:
##       cor 
## 0.8740597
```

```r
cor.test(data1$Bedrooms, data1$Area)
```

```
## 
## 	Pearson's product-moment correlation
## 
## data:  data1$Bedrooms and data1$Area
## t = 12.811, df = 58, p-value < 2.2e-16
## alternative hypothesis: true correlation is not equal to 0
## 95 percent confidence interval:
##  0.7747757 0.9140118
## sample estimates:
##       cor 
## 0.8595894
```

```r
cor.test(data1$Bedrooms, data1$Bathrooms)
```

```
## 
## 	Pearson's product-moment correlation
## 
## data:  data1$Bedrooms and data1$Bathrooms
## t = 6.6217, df = 58, p-value = 1.262e-08
## alternative hypothesis: true correlation is not equal to 0
## 95 percent confidence interval:
##  0.4826241 0.7800925
## sample estimates:
##       cor 
## 0.6561389
```

```r
cor.test(data1$Bedrooms, data1$Bathrooms)
```

```
## 
## 	Pearson's product-moment correlation
## 
## data:  data1$Bedrooms and data1$Bathrooms
## t = 6.6217, df = 58, p-value = 1.262e-08
## alternative hypothesis: true correlation is not equal to 0
## 95 percent confidence interval:
##  0.4826241 0.7800925
## sample estimates:
##       cor 
## 0.6561389
```

```r
#Are uncorrelated
cor.test(data1$Area, data1$Distance)
```

```
## 
## 	Pearson's product-moment correlation
## 
## data:  data1$Area and data1$Distance
## t = -0.4492, df = 58, p-value = 0.655
## alternative hypothesis: true correlation is not equal to 0
## 95 percent confidence interval:
##  -0.3081967  0.1980052
## sample estimates:
##         cor 
## -0.05887988
```

## Other helpful Stat and R learning
http://faculty.marshall.usc.edu/gareth-james/ISL/index.html
https://web.stanford.edu/~hastie/ElemStatLearn/

Essentials of Machine Learning https://www.analyticsvidhya.com/blog/2017/09/common-machine-learning-algorithms/
CLT and Stat Basics https://www.analyticsvidhya.com/blog/2019/05/statistics-101-introduction-central-limit-theorem/


