Regression Models Project
=========================

Executive summary
-----------------



Data transformations
--------------------

The `am` column represents the type of transmission, where `0` means automatic and `1` means manual. This would be better to have as a factor variable.

```r
mtcars$am <- factor(mtcars$am, levels=c(0, 1), labels=c('Automatic', 'Manual'))
```

I also convert the number of cylinders into an ordered factor.

```r
mtcars$cyl <- ordered(mtcars$cyl, level=c(4,6,8))
```



Exploratory data analysis
-------------------------
First we can simply compare the mean MPG for automatic and manual transmissions:

```r
tapply(mtcars$mpg, mtcars$am, mean)
```

```
## Automatic    Manual 
##     17.15     24.39
```

On first glance, then, it appears that cars with a manual transmission are more fuel efficient. However, that may not be the cause; it is possible that the cars with a manual transmission happen to share some other characteristics.

Let's take a look at the scatterplots of this data set.

```r
pairs(mtcars)
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4.png) 

It appears that the weight (`wt`) and rear axle ratio (`drat`) are correlated with the MPG. In both plots, the red dots represent manual transmissions, and the black dots represent automatic transmissions.

```r
par(mfrow=c(1,2))
with(mtcars, plot(wt, mpg, pch=19, col=am, xlab='Weight (1000s pounds)',
                  ylab='MPG'))
with(mtcars, plot(drat, mpg, pch=19, col=am, xlab='Rear axle ratio',
                  ylab='MPG'))
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5.png) 


Model selection
---------------
I will consider three models:
- Model 1: $MPG_i = \beta_0 + \beta_1 \cdot Transmission_i + \beta_2 \cdot Weight_i + \epsilon_i$
- Model 2: $MPG_i = \beta_0 + \beta_1 \cdot Transmission_i + \beta_2 \cdot RearAxleRatio_i +  \epsilon_i$
- Model 3: $MPG_i = \beta_0 + \beta_1 \cdot Transmission_i + \beta_2 \cdot Weight_i + \beta_3 \cdot RearAxleRatio_i +  \epsilon_i$
- Model 4: $MPG_i = \beta_0 + \beta_1 \cdot Transmission_i + \beta_2 \cdot Weight_i + \beta_3 \cdot Transmission_i \cdot Weight_i + \beta_4 \cdot RearAxleRatio_i + \beta_5 \cdot Transmission_i \cdot RearAxleRatio_i + \epsilon_i$
 

```r
model1 <- lm(mpg ~ am + wt, mtcars)
model2 <- lm(mpg ~ am + drat, mtcars)
model3 <- lm(mpg ~ am + wt + drat, mtcars)
model4 <- lm(mpg ~ am + wt + am*wt + drat + am*drat, mtcars)
```

## Diagnostics

### dfbetas

```r
dfbetas(model1)
```

```
##                     (Intercept)  amManual         wt
## Mazda RX4              0.040944 -0.151209 -0.0420989
## Mazda RX4 Wag          0.036457 -0.074912 -0.0374858
## Datsun 710            -0.016237 -0.099604  0.0166947
## Hornet 4 Drive         0.084136 -0.089721 -0.0622633
## Hornet Sportabout     -0.009450  0.011461  0.0058682
## Valiant               -0.030688  0.037804  0.0185799
## Duster 360            -0.138840  0.190181  0.0684850
## Merc 240D              0.289742 -0.305745 -0.2170441
## Merc 230               0.167937 -0.174450 -0.1280475
## Merc 280               0.013281 -0.016107 -0.0082468
## Merc 280C             -0.050478  0.061219  0.0313450
## Merc 450SE            -0.006163 -0.015291  0.0223421
## Merc 450SL            -0.001172  0.002090  0.0001841
## Merc 450SLC           -0.032221  0.065889 -0.0017982
## Cadillac Fleetwood    -0.146067  0.072526  0.1757899
## Lincoln Continental   -0.313163  0.163653  0.3702540
## Chrysler Imperial     -0.889167  0.454879  1.0592213
## Fiat 128               0.137100  0.307211 -0.1409691
## Honda Civic            0.124508  0.008800 -0.1280219
## Toyota Corolla         0.352435  0.130272 -0.3623798
## Toyota Corona         -0.379859  0.338642  0.3351364
## Dodge Challenger      -0.117178  0.152215  0.0645461
## AMC Javelin           -0.176117  0.212788  0.1100147
## Camaro Z28            -0.042527  0.110483 -0.0215023
## Pontiac Firebird       0.028795 -0.076755  0.0161415
## Fiat X1-9              0.014800  0.008834 -0.0152173
## Porsche 914-2          0.003639  0.005774 -0.0037412
## Lotus Europa           0.097482 -0.001777 -0.1002325
## Ford Pantera L         0.318160 -0.487710 -0.3271374
## Ferrari Dino           0.086664 -0.212113 -0.0891096
## Maserati Bora          0.354732 -0.443275 -0.3647419
## Volvo 142E             0.032287 -0.077504 -0.0331977
```

```r
dfbeta(model2)
```

```
##                     (Intercept)  amManual     drat
## Mazda RX4              -0.31043 -0.283371  0.09446
## Mazda RX4 Wag          -0.31043 -0.283371  0.09446
## Datsun 710             -0.07093 -0.052683  0.02158
## Hornet 4 Drive          1.21007 -0.095932 -0.27509
## Hornet Sportabout       0.38627 -0.071500 -0.07772
## Valiant                 2.03896  0.179456 -0.54776
## Duster 360             -0.27988  0.099776  0.04447
## Merc 240D              -1.35663 -0.664999  0.49917
## Merc 230               -0.97886 -0.376886  0.33475
## Merc 280                0.80974  0.311769 -0.27692
## Merc 280C               1.50530  0.579579 -0.51479
## Merc 450SE              0.11735 -0.008025 -0.02699
## Merc 450SL              0.32457 -0.022196 -0.07466
## Merc 450SLC            -0.15894  0.010870  0.03656
## Cadillac Fleetwood     -1.63572 -0.049762  0.41617
## Lincoln Continental    -1.46943  0.013625  0.35946
## Chrysler Imperial      -0.21293  0.095786  0.02892
## Fiat 128               -0.19191  0.608330  0.05840
## Honda Civic            -0.79521 -0.092558  0.24198
## Toyota Corolla         -1.19105  0.438339  0.36243
## Toyota Corona          -0.55605 -0.268398  0.20357
## Dodge Challenger        0.71731  0.063133 -0.19270
## AMC Javelin            -0.19031  0.035226  0.03829
## Camaro Z28              2.00658  0.928248 -0.72465
## Pontiac Firebird        0.72174 -0.057218 -0.16408
## Fiat X1-9              -0.06696  0.212270  0.02038
## Porsche 914-2           0.19325 -0.007000 -0.05880
## Lotus Europa            1.78000  1.062514 -0.54164
## Ford Pantera L          1.33929 -0.492896 -0.40753
## Ferrari Dino           -0.80715 -0.379161  0.24561
## Maserati Bora          -2.86167 -1.237732  0.87078
## Volvo 142E              0.16381 -0.240598 -0.04985
```

```r
dfbetas(model3)
```

```
##                     (Intercept)   amManual        wt      drat
## Mazda RX4             -0.020727 -0.1430951 -0.017937  0.039619
## Mazda RX4 Wag          0.007526 -0.0648447 -0.028104  0.006708
## Datsun 710            -0.068960 -0.1039616  0.042470  0.069569
## Hornet 4 Drive         0.161343 -0.0715829 -0.137239 -0.119374
## Hornet Sportabout      0.009248 -0.0057762 -0.007277 -0.006598
## Valiant                0.047605 -0.0009260 -0.028914 -0.044081
## Duster 360            -0.129759  0.1265912  0.094599  0.083234
## Merc 240D             -0.067653 -0.3308289 -0.093727  0.190767
## Merc 230              -0.084142 -0.1674344 -0.015884  0.142614
## Merc 280               0.056824  0.0726713 -0.013907 -0.078123
## Merc 280C              0.171037  0.2187351 -0.041860 -0.235145
## Merc 450SE             0.024097 -0.0051498  0.013556 -0.030026
## Merc 450SL             0.015972 -0.0058488 -0.007182 -0.014162
## Merc 450SLC           -0.063991  0.0227084  0.023969  0.058444
## Cadillac Fleetwood    -0.057536  0.0688955  0.161047 -0.004051
## Lincoln Continental   -0.155263  0.1187788  0.326238  0.040619
## Chrysler Imperial     -0.703930  0.2089445  1.063918  0.400297
## Fiat 128               0.080581  0.2919645 -0.140134 -0.026142
## Honda Civic           -0.055073 -0.0316135 -0.004802  0.079153
## Toyota Corolla         0.098206  0.0954853 -0.303712  0.049140
## Toyota Corona         -0.107991  0.3611488  0.300171 -0.066602
## Dodge Challenger      -0.241736  0.0002273  0.140088  0.227127
## AMC Javelin           -0.186941  0.1166099  0.147780  0.133202
## Camaro Z28             0.333656  0.3035534 -0.195274 -0.391086
## Pontiac Firebird       0.101934 -0.0381393 -0.025009 -0.096757
## Fiat X1-9              0.015243  0.0143738 -0.022438 -0.007425
## Porsche 914-2          0.018614 -0.0033500 -0.001590 -0.024794
## Lotus Europa           0.270083  0.0895633 -0.262817 -0.213675
## Ford Pantera L         0.497639 -0.3680485 -0.517209 -0.373621
## Ferrari Dino          -0.103655 -0.2162921 -0.001977  0.144674
## Maserati Bora         -0.001173 -0.4121990 -0.228254  0.140710
## Volvo 142E             0.048178 -0.0736122 -0.052856 -0.034475
```

```r
dfbeta(model4)
```

```
##                     (Intercept) amManual         wt       drat amManual:wt
## Mazda RX4             2.120e-15  -0.5148  2.315e-16 -9.057e-16   -0.038547
## Mazda RX4 Wag        -5.603e-15  -0.1092  5.308e-16  1.111e-15    0.092901
## Datsun 710           -6.280e-16  -2.7445  4.686e-17  2.654e-16    0.202487
## Hornet 4 Drive        2.511e+00  -2.5108 -2.471e-01 -4.304e-01    0.247066
## Hornet Sportabout     3.881e-01  -0.3881 -3.575e-02 -6.514e-02    0.035747
## Valiant               1.325e+00  -1.3253 -8.908e-02 -2.832e-01    0.089082
## Duster 360           -1.171e+00   1.1709  1.026e-01  1.809e-01   -0.102570
## Merc 240D            -1.263e+00   1.2630 -1.452e-01  6.330e-01    0.145156
## Merc 230             -1.823e+00   1.8231 -3.716e-02  6.446e-01    0.037165
## Merc 280              2.583e-01  -0.2583 -6.025e-03 -7.671e-02    0.006025
## Merc 280C             1.697e+00  -1.6974 -3.960e-02 -5.042e-01    0.039597
## Merc 450SE            1.610e-01  -0.1610  1.040e-02 -4.913e-02   -0.010399
## Merc 450SL            2.092e-01  -0.2092 -1.058e-02 -4.417e-02    0.010581
## Merc 450SLC          -6.845e-01   0.6845  2.861e-02  1.495e-01   -0.028608
## Cadillac Fleetwood    5.034e-01  -0.5034 -1.824e-01  3.376e-02    0.182418
## Lincoln Continental   4.992e-01  -0.4992 -1.289e-01 -1.729e-02    0.128882
## Chrysler Imperial    -4.180e+00   4.1800  7.373e-01  4.968e-01   -0.737254
## Fiat 128              5.181e-15   2.1200 -2.880e-16 -1.253e-15   -0.362248
## Honda Civic           9.421e-16   6.9261 -1.091e-16 -8.744e-16    0.048980
## Toyota Corolla        2.355e-15   1.9134 -7.903e-17 -5.897e-16   -0.633773
## Toyota Corona        -2.691e-01   0.2691  1.180e-01 -7.115e-02   -0.118008
## Dodge Challenger     -2.167e+00   2.1666  1.385e-01  4.701e-01   -0.138521
## AMC Javelin          -1.612e+00   1.6123  1.492e-01  2.702e-01   -0.149218
## Camaro Z28            3.985e+00  -3.9853 -2.403e-01 -1.020e+00    0.240324
## Pontiac Firebird      1.039e+00  -1.0394 -2.790e-02 -2.379e-01    0.027901
## Fiat X1-9            -1.021e-15  -1.2269  4.319e-17  2.801e-16    0.209845
## Porsche 914-2        -5.495e-16   1.1289  2.882e-17  6.012e-17   -0.017182
## Lotus Europa          0.000e+00 -10.7237  2.482e-17  3.538e-16    1.303515
## Ford Pantera L       -3.768e-15   5.2210  2.587e-16  6.339e-16   -0.725812
## Ferrari Dino         -1.413e-15  -1.9274  9.160e-17  4.701e-16    0.002549
## Maserati Bora         2.571e-15   0.2689 -1.293e-16 -6.855e-16    0.419062
## Volvo 142E            3.533e-16  -0.2734 -2.037e-17 -7.325e-17    0.042310
##                     amManual:drat
## Mazda RX4               0.1194242
## Mazda RX4 Wag          -0.0103888
## Datsun 710              0.5065209
## Hornet 4 Drive          0.4303582
## Hornet Sportabout       0.0651362
## Valiant                 0.2832136
## Duster 360             -0.1809051
## Merc 240D              -0.6330331
## Merc 230               -0.6445766
## Merc 280                0.0767100
## Merc 280C               0.5041762
## Merc 450SE              0.0491301
## Merc 450SL              0.0441741
## Merc 450SLC            -0.1494868
## Cadillac Fleetwood     -0.0337620
## Lincoln Continental     0.0172914
## Chrysler Imperial      -0.4967972
## Fiat 128               -0.1807904
## Honda Civic            -1.8026135
## Toyota Corolla          0.0003206
## Toyota Corona           0.0711502
## Dodge Challenger       -0.4700853
## AMC Javelin            -0.2701645
## Camaro Z28              1.0199517
## Pontiac Firebird        0.2379061
## Fiat X1-9               0.1477088
## Porsche 914-2          -0.2904312
## Lotus Europa            1.7971024
## Ford Pantera L         -0.9093494
## Ferrari Dino            0.4433818
## Maserati Bora          -0.2783427
## Volvo 142E              0.0488768
```
Measuring how much the coefficients would change without the sample
- Model 1
    - Chrysler imperial
- Model 2
    - Lotus Europa
    - Maserati Bora
- Model 3
    - Chrylster Imperial
- Model 4
    - 


```r
hatvalues(model1)
```

```
##           Mazda RX4       Mazda RX4 Wag          Datsun 710 
##             0.07975             0.09086             0.07746 
##      Hornet 4 Drive   Hornet Sportabout             Valiant 
##             0.07249             0.05963             0.05881 
##          Duster 360           Merc 240D            Merc 230 
##             0.05519             0.07433             0.07743 
##            Merc 280           Merc 280C          Merc 450SE 
##             0.05963             0.05963             0.05850 
##          Merc 450SL         Merc 450SLC  Cadillac Fleetwood 
##             0.05273             0.05264             0.19465 
## Lincoln Continental   Chrysler Imperial            Fiat 128 
##             0.22998             0.21345             0.07981 
##         Honda Civic      Toyota Corolla       Toyota Corona 
##             0.11794             0.09840             0.16270 
##    Dodge Challenger         AMC Javelin          Camaro Z28 
##             0.05664             0.05985             0.05296 
##    Pontiac Firebird           Fiat X1-9       Porsche 914-2 
##             0.05301             0.09159             0.08168 
##        Lotus Europa      Ford Pantera L        Ferrari Dino 
##             0.12913             0.11422             0.08527 
##       Maserati Bora          Volvo 142E 
##             0.16389             0.08574
```

```r
hatvalues(model2)
```

```
##           Mazda RX4       Mazda RX4 Wag          Datsun 710 
##             0.08208             0.08208             0.08610 
##      Hornet 4 Drive   Hornet Sportabout             Valiant 
##             0.06239             0.05689             0.11616 
##          Duster 360           Merc 240D            Merc 230 
##             0.05397             0.09000             0.14472 
##            Merc 280           Merc 280C          Merc 450SE 
##             0.14472             0.14472             0.06336 
##          Merc 450SL         Merc 450SLC  Cadillac Fleetwood 
##             0.06336             0.06336             0.08175 
## Lincoln Continental   Chrysler Imperial            Fiat 128 
##             0.07143             0.05336             0.07713 
##         Honda Civic      Toyota Corolla       Toyota Corona 
##             0.25451             0.08355             0.09188 
##    Dodge Challenger         AMC Javelin          Camaro Z28 
##             0.11616             0.05689             0.09778 
##    Pontiac Firebird           Fiat X1-9       Porsche 914-2 
##             0.06239             0.07713             0.11004 
##        Lotus Europa      Ford Pantera L        Ferrari Dino 
##             0.09490             0.08355             0.11933 
##       Maserati Bora          Volvo 142E 
##             0.13657             0.07775
```

```r
hatvalues(model3)
```

```
##           Mazda RX4       Mazda RX4 Wag          Datsun 710 
##             0.08268             0.09139             0.09123 
##      Hornet 4 Drive   Hornet Sportabout             Valiant 
##             0.10389             0.07230             0.15946 
##          Duster 360           Merc 240D            Merc 230 
##             0.05939             0.09499             0.14556 
##            Merc 280           Merc 280C          Merc 450SE 
##             0.14750             0.14750             0.06461 
##          Merc 450SL         Merc 450SLC  Cadillac Fleetwood 
##             0.06704             0.06553             0.19472 
## Lincoln Continental   Chrysler Imperial            Fiat 128 
##             0.23248             0.23985             0.07990 
##         Honda Civic      Toyota Corolla       Toyota Corona 
##             0.25502             0.09880             0.16637 
##    Dodge Challenger         AMC Javelin          Camaro Z28 
##             0.15270             0.07265             0.11266 
##    Pontiac Firebird           Fiat X1-9       Porsche 914-2 
##             0.06306             0.09337             0.11015 
##        Lotus Europa      Ford Pantera L        Ferrari Dino 
##             0.19587             0.14769             0.11933 
##       Maserati Bora          Volvo 142E 
##             0.18063             0.09165
```

```r
hatvalues(model4)
```

```
##           Mazda RX4       Mazda RX4 Wag          Datsun 710 
##             0.09303             0.12420             0.12188 
##      Hornet 4 Drive   Hornet Sportabout             Valiant 
##             0.12447             0.08029             0.21058 
##          Duster 360           Merc 240D            Merc 230 
##             0.06212             0.11875             0.19904 
##            Merc 280           Merc 280C          Merc 450SE 
##             0.20094             0.20094             0.07135 
##          Merc 450SL         Merc 450SLC  Cadillac Fleetwood 
##             0.07439             0.07236             0.25566 
## Lincoln Continental   Chrysler Imperial            Fiat 128 
##             0.30547             0.31069             0.08754 
##         Honda Civic      Toyota Corolla       Toyota Corona 
##             0.56458             0.14955             0.21856 
##    Dodge Challenger         AMC Javelin          Camaro Z28 
##             0.20142             0.08077             0.14337 
##    Pontiac Firebird           Fiat X1-9       Porsche 914-2 
##             0.06884             0.13675             0.16848 
##        Lotus Europa      Ford Pantera L        Ferrari Dino 
##             0.50238             0.33311             0.19319 
##       Maserati Bora          Volvo 142E 
##             0.39467             0.13063
```
Measuring the influence of points.
- Model 1
    - Chrysler Imperial
- Model 2
    - none
- Model 3
    - none
- Model 4
    - 

## Residuals

```r
par(mfrow=c(2,2))
plot(model1)
```

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-91.png) 

```r
plot(model2)
```

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-92.png) 

```r
plot(model3)
```

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-93.png) 

```r
plot(model4)
```

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-94.png) 

Conclusion
----------
