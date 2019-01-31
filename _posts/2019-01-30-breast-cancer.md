``` r message = F
library(egg) # required to arrange plots side-by-side
```

    ## Warning: package 'egg' was built under R version 3.5.2

``` r
library(grid) # required to draw arrows
```

``` r
library(ggplot2)
library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following object is masked from 'package:gridExtra':
    ## 
    ##     combine

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
library(gridExtra)
library(corrplot)
```

    ## Warning: package 'corrplot' was built under R version 3.5.2

    ## corrplot 0.84 loaded

``` r
library(ggthemes)
```

    ## Warning: package 'ggthemes' was built under R version 3.5.2

``` r
data <- read.csv("breast_cancer.csv", header = TRUE)
colnames(data)
```

    ##  [1] "id"                      "diagnosis"              
    ##  [3] "radius_mean"             "texture_mean"           
    ##  [5] "perimeter_mean"          "area_mean"              
    ##  [7] "smoothness_mean"         "compactness_mean"       
    ##  [9] "concavity_mean"          "concave.points_mean"    
    ## [11] "symmetry_mean"           "fractal_dimension_mean" 
    ## [13] "radius_se"               "texture_se"             
    ## [15] "perimeter_se"            "area_se"                
    ## [17] "smoothness_se"           "compactness_se"         
    ## [19] "concavity_se"            "concave.points_se"      
    ## [21] "symmetry_se"             "fractal_dimension_se"   
    ## [23] "radius_worst"            "texture_worst"          
    ## [25] "perimeter_worst"         "area_worst"             
    ## [27] "smoothness_worst"        "compactness_worst"      
    ## [29] "concavity_worst"         "concave.points_worst"   
    ## [31] "symmetry_worst"          "fractal_dimension_worst"
    ## [33] "X"

``` r
# remove the last column with only NA value 

data <- data[,-33] 
dim(data)
```

    ## [1] 569  32

``` r
str(data)
```

    ## 'data.frame':    569 obs. of  32 variables:
    ##  $ id                     : int  842302 842517 84300903 84348301 84358402 843786 844359 84458202 844981 84501001 ...
    ##  $ diagnosis              : Factor w/ 2 levels "B","M": 2 2 2 2 2 2 2 2 2 2 ...
    ##  $ radius_mean            : num  18 20.6 19.7 11.4 20.3 ...
    ##  $ texture_mean           : num  10.4 17.8 21.2 20.4 14.3 ...
    ##  $ perimeter_mean         : num  122.8 132.9 130 77.6 135.1 ...
    ##  $ area_mean              : num  1001 1326 1203 386 1297 ...
    ##  $ smoothness_mean        : num  0.1184 0.0847 0.1096 0.1425 0.1003 ...
    ##  $ compactness_mean       : num  0.2776 0.0786 0.1599 0.2839 0.1328 ...
    ##  $ concavity_mean         : num  0.3001 0.0869 0.1974 0.2414 0.198 ...
    ##  $ concave.points_mean    : num  0.1471 0.0702 0.1279 0.1052 0.1043 ...
    ##  $ symmetry_mean          : num  0.242 0.181 0.207 0.26 0.181 ...
    ##  $ fractal_dimension_mean : num  0.0787 0.0567 0.06 0.0974 0.0588 ...
    ##  $ radius_se              : num  1.095 0.543 0.746 0.496 0.757 ...
    ##  $ texture_se             : num  0.905 0.734 0.787 1.156 0.781 ...
    ##  $ perimeter_se           : num  8.59 3.4 4.58 3.44 5.44 ...
    ##  $ area_se                : num  153.4 74.1 94 27.2 94.4 ...
    ##  $ smoothness_se          : num  0.0064 0.00522 0.00615 0.00911 0.01149 ...
    ##  $ compactness_se         : num  0.049 0.0131 0.0401 0.0746 0.0246 ...
    ##  $ concavity_se           : num  0.0537 0.0186 0.0383 0.0566 0.0569 ...
    ##  $ concave.points_se      : num  0.0159 0.0134 0.0206 0.0187 0.0188 ...
    ##  $ symmetry_se            : num  0.03 0.0139 0.0225 0.0596 0.0176 ...
    ##  $ fractal_dimension_se   : num  0.00619 0.00353 0.00457 0.00921 0.00511 ...
    ##  $ radius_worst           : num  25.4 25 23.6 14.9 22.5 ...
    ##  $ texture_worst          : num  17.3 23.4 25.5 26.5 16.7 ...
    ##  $ perimeter_worst        : num  184.6 158.8 152.5 98.9 152.2 ...
    ##  $ area_worst             : num  2019 1956 1709 568 1575 ...
    ##  $ smoothness_worst       : num  0.162 0.124 0.144 0.21 0.137 ...
    ##  $ compactness_worst      : num  0.666 0.187 0.424 0.866 0.205 ...
    ##  $ concavity_worst        : num  0.712 0.242 0.45 0.687 0.4 ...
    ##  $ concave.points_worst   : num  0.265 0.186 0.243 0.258 0.163 ...
    ##  $ symmetry_worst         : num  0.46 0.275 0.361 0.664 0.236 ...
    ##  $ fractal_dimension_worst: num  0.1189 0.089 0.0876 0.173 0.0768 ...

``` r
data$diagnosis = as.factor(data$diagnosis)
sapply(data, class)
```

    ##                      id               diagnosis             radius_mean 
    ##               "integer"                "factor"               "numeric" 
    ##            texture_mean          perimeter_mean               area_mean 
    ##               "numeric"               "numeric"               "numeric" 
    ##         smoothness_mean        compactness_mean          concavity_mean 
    ##               "numeric"               "numeric"               "numeric" 
    ##     concave.points_mean           symmetry_mean  fractal_dimension_mean 
    ##               "numeric"               "numeric"               "numeric" 
    ##               radius_se              texture_se            perimeter_se 
    ##               "numeric"               "numeric"               "numeric" 
    ##                 area_se           smoothness_se          compactness_se 
    ##               "numeric"               "numeric"               "numeric" 
    ##            concavity_se       concave.points_se             symmetry_se 
    ##               "numeric"               "numeric"               "numeric" 
    ##    fractal_dimension_se            radius_worst           texture_worst 
    ##               "numeric"               "numeric"               "numeric" 
    ##         perimeter_worst              area_worst        smoothness_worst 
    ##               "numeric"               "numeric"               "numeric" 
    ##       compactness_worst         concavity_worst    concave.points_worst 
    ##               "numeric"               "numeric"               "numeric" 
    ##          symmetry_worst fractal_dimension_worst 
    ##               "numeric"               "numeric"

``` r
#check missing value 

cat("Number of missing value:", sum(is.na(data)), "\n")
```

    ## Number of missing value: 0

``` r
summary(data)
```

    ##        id            diagnosis  radius_mean      texture_mean  
    ##  Min.   :     8670   B:357     Min.   : 6.981   Min.   : 9.71  
    ##  1st Qu.:   869218   M:212     1st Qu.:11.700   1st Qu.:16.17  
    ##  Median :   906024             Median :13.370   Median :18.84  
    ##  Mean   : 30371831             Mean   :14.127   Mean   :19.29  
    ##  3rd Qu.:  8813129             3rd Qu.:15.780   3rd Qu.:21.80  
    ##  Max.   :911320502             Max.   :28.110   Max.   :39.28  
    ##  perimeter_mean     area_mean      smoothness_mean   compactness_mean 
    ##  Min.   : 43.79   Min.   : 143.5   Min.   :0.05263   Min.   :0.01938  
    ##  1st Qu.: 75.17   1st Qu.: 420.3   1st Qu.:0.08637   1st Qu.:0.06492  
    ##  Median : 86.24   Median : 551.1   Median :0.09587   Median :0.09263  
    ##  Mean   : 91.97   Mean   : 654.9   Mean   :0.09636   Mean   :0.10434  
    ##  3rd Qu.:104.10   3rd Qu.: 782.7   3rd Qu.:0.10530   3rd Qu.:0.13040  
    ##  Max.   :188.50   Max.   :2501.0   Max.   :0.16340   Max.   :0.34540  
    ##  concavity_mean    concave.points_mean symmetry_mean   
    ##  Min.   :0.00000   Min.   :0.00000     Min.   :0.1060  
    ##  1st Qu.:0.02956   1st Qu.:0.02031     1st Qu.:0.1619  
    ##  Median :0.06154   Median :0.03350     Median :0.1792  
    ##  Mean   :0.08880   Mean   :0.04892     Mean   :0.1812  
    ##  3rd Qu.:0.13070   3rd Qu.:0.07400     3rd Qu.:0.1957  
    ##  Max.   :0.42680   Max.   :0.20120     Max.   :0.3040  
    ##  fractal_dimension_mean   radius_se        texture_se      perimeter_se   
    ##  Min.   :0.04996        Min.   :0.1115   Min.   :0.3602   Min.   : 0.757  
    ##  1st Qu.:0.05770        1st Qu.:0.2324   1st Qu.:0.8339   1st Qu.: 1.606  
    ##  Median :0.06154        Median :0.3242   Median :1.1080   Median : 2.287  
    ##  Mean   :0.06280        Mean   :0.4052   Mean   :1.2169   Mean   : 2.866  
    ##  3rd Qu.:0.06612        3rd Qu.:0.4789   3rd Qu.:1.4740   3rd Qu.: 3.357  
    ##  Max.   :0.09744        Max.   :2.8730   Max.   :4.8850   Max.   :21.980  
    ##     area_se        smoothness_se      compactness_se      concavity_se    
    ##  Min.   :  6.802   Min.   :0.001713   Min.   :0.002252   Min.   :0.00000  
    ##  1st Qu.: 17.850   1st Qu.:0.005169   1st Qu.:0.013080   1st Qu.:0.01509  
    ##  Median : 24.530   Median :0.006380   Median :0.020450   Median :0.02589  
    ##  Mean   : 40.337   Mean   :0.007041   Mean   :0.025478   Mean   :0.03189  
    ##  3rd Qu.: 45.190   3rd Qu.:0.008146   3rd Qu.:0.032450   3rd Qu.:0.04205  
    ##  Max.   :542.200   Max.   :0.031130   Max.   :0.135400   Max.   :0.39600  
    ##  concave.points_se   symmetry_se       fractal_dimension_se
    ##  Min.   :0.000000   Min.   :0.007882   Min.   :0.0008948   
    ##  1st Qu.:0.007638   1st Qu.:0.015160   1st Qu.:0.0022480   
    ##  Median :0.010930   Median :0.018730   Median :0.0031870   
    ##  Mean   :0.011796   Mean   :0.020542   Mean   :0.0037949   
    ##  3rd Qu.:0.014710   3rd Qu.:0.023480   3rd Qu.:0.0045580   
    ##  Max.   :0.052790   Max.   :0.078950   Max.   :0.0298400   
    ##   radius_worst   texture_worst   perimeter_worst    area_worst    
    ##  Min.   : 7.93   Min.   :12.02   Min.   : 50.41   Min.   : 185.2  
    ##  1st Qu.:13.01   1st Qu.:21.08   1st Qu.: 84.11   1st Qu.: 515.3  
    ##  Median :14.97   Median :25.41   Median : 97.66   Median : 686.5  
    ##  Mean   :16.27   Mean   :25.68   Mean   :107.26   Mean   : 880.6  
    ##  3rd Qu.:18.79   3rd Qu.:29.72   3rd Qu.:125.40   3rd Qu.:1084.0  
    ##  Max.   :36.04   Max.   :49.54   Max.   :251.20   Max.   :4254.0  
    ##  smoothness_worst  compactness_worst concavity_worst  concave.points_worst
    ##  Min.   :0.07117   Min.   :0.02729   Min.   :0.0000   Min.   :0.00000     
    ##  1st Qu.:0.11660   1st Qu.:0.14720   1st Qu.:0.1145   1st Qu.:0.06493     
    ##  Median :0.13130   Median :0.21190   Median :0.2267   Median :0.09993     
    ##  Mean   :0.13237   Mean   :0.25427   Mean   :0.2722   Mean   :0.11461     
    ##  3rd Qu.:0.14600   3rd Qu.:0.33910   3rd Qu.:0.3829   3rd Qu.:0.16140     
    ##  Max.   :0.22260   Max.   :1.05800   Max.   :1.2520   Max.   :0.29100     
    ##  symmetry_worst   fractal_dimension_worst
    ##  Min.   :0.1565   Min.   :0.05504        
    ##  1st Qu.:0.2504   1st Qu.:0.07146        
    ##  Median :0.2822   Median :0.08004        
    ##  Mean   :0.2901   Mean   :0.08395        
    ##  3rd Qu.:0.3179   3rd Qu.:0.09208        
    ##  Max.   :0.6638   Max.   :0.20750

B: 357 and M: 212

``` r
library(caret)
```

    ## Warning: package 'caret' was built under R version 3.5.2

    ## Loading required package: lattice

``` r
validation_index <- createDataPartition(data$diagnosis, p = 0.8, list = FALSE)
validation <- data[-validation_index,]
data <- data[validation_index,]
dim(data)
```

    ## [1] 456  32

``` r
percentage <- prop.table(table(data$diagnosis)) * 100
cbind(freq= table(data$diagnosis), percentage = percentage)
```

    ##   freq percentage
    ## B  286    62.7193
    ## M  170    37.2807

``` r
x = data[,3:32]
y = data[,2]
```

``` r
par(mfrow=c(2,3))
  for(i in 1:6) {
  boxplot(x[,i], main=names(x)[i])
  }
```

![](/figures/cancer_files/figure-markdown_github/unnamed-chunk-11-1.png)

``` r
par(mfrow=c(2,3))
  for(i in 7:12) {
  boxplot(x[,i], main=names(x)[i])
  }
```

![](/figures/cancer_files/figure-markdown_github/unnamed-chunk-11-2.png)

``` r
par(mfrow=c(2,3))
  for(i in 13:18) {
  boxplot(x[,i], main=names(x)[i])
  }
```

![](/figures/cancer_files/figure-markdown_github/unnamed-chunk-11-3.png)

``` r
par(mfrow=c(2,3))
  for(i in 19:24) {
  boxplot(x[,i], main=names(x)[i])
}
```

![](/figures/cancer_files/figure-markdown_github/unnamed-chunk-11-4.png)

``` r
par(mfrow=c(2,3))
  for(i in 25:30) {
  boxplot(x[,i], main=names(x)[i])
  }
```

![](/figures/cancer_files/figure-markdown_github/unnamed-chunk-11-5.png)

``` r
p1 <- ggplot(data, aes(x=concavity_se)) + ggtitle("Concavity SE") +
  geom_histogram(aes(y = 100*(..count..)/sum(..count..)), binwidth = 0.1, colour="black", fill="white") + ylab("Percentage")
p2 <- ggplot(data, aes(x= texture_se)) + ggtitle("Texture") +
  geom_histogram(aes(y = 100*(..count..)/sum(..count..)), binwidth = 1, colour="black", fill="white") + ylab("Percentage")
p3 <- ggplot(data, aes(x= radius_se)) + ggtitle("Radius") +
  geom_histogram(aes(y = 100*(..count..)/sum(..count..)), binwidth = 1, colour="black", fill="white") + ylab("Percentage")
grid.arrange(p1, p2, p3, ncol=3)
```

![](/figures/cancer_files/figure-markdown_github/unnamed-chunk-12-1.png)

Let's check the correlation between different features

``` r
features <- data[, 3:ncol(data)]
correlations <- cor(features,method="pearson")
corrplot(correlations, number.cex = .9, method = "square", 
         hclust.method = "ward", order = "FPC",
         type = "full", tl.cex=0.8,tl.col = "black")
```

![](/figures/cancer_files/figure-markdown_github/unnamed-chunk-13-1.png)

``` r
attach(data)
#Correlation bewteen numeric variables and diagnosis
# to check if all the variables have significant correlation with diagnosis 


par(mfrow=c(1,3))

boxplot(data$concavity_mean~ data$diagnosis, main=" concave.point_mean vs Breast Cancer", xlab="Diagnosis", ylab=" Concave Point Mean")


boxplot(data$radius_mean~ data$diagnosis, main=" Radius Mean vs Breast Cancer", xlab="Diagnosis", ylab=" Radius Mean")


boxplot(data$texture_mean~ data$diagnosis, main=" Texture Mean vs Breast Cancer", xlab="Diagnosis", ylab=" Texture Mean")
```

![](/figures/cancer_files/figure-markdown_github/unnamed-chunk-14-1.png)

PCA analysis
============

``` r
ggplot(data, aes(x=concavity_mean, y= texture_mean, color=diagnosis)) + geom_point()
```

![](/figures/cancer_files/figure-markdown_github/unnamed-chunk-15-1.png)

``` r
data %>% select(-diagnosis) %>% # remove diagnosis column
  scale() %>%                 # scale to 0 mean and unit variance
  prcomp() ->                 # do PCA
  pca 
```

``` r
pca_data <- data.frame(pca$x, Diagnosis = data$diagnosis)
head(pca_data)
```

    ##         PC1       PC2        PC3         PC4           PC5         PC6
    ## 1 -9.325813 -2.003009 -1.2080550 3.818414113  0.5241018548  1.48660895
    ## 2 -2.459036  3.815635 -0.4073254 1.101324732 -0.7009796229 -0.09762209
    ## 3 -5.823641  1.108778 -0.5904129 0.899980243  0.0251956985  0.59088817
    ## 5 -4.014021  1.893796  1.5135099 2.858119931 -0.6008860498 -1.28350463
    ## 7 -2.301293  2.783388 -1.6075233 0.222872727  0.0006221458 -0.13692847
    ## 8 -2.136995 -2.342667 -1.0609893 0.005259146  1.5390219828 -1.05189129
    ##          PC7        PC8        PC9        PC10       PC11       PC12
    ## 1  0.2462348  2.1426944  0.5427216 -0.28346790  1.1395557  0.5929142
    ## 2 -0.2228705  0.1982356 -0.3265970 -0.21242998 -1.5602419  0.3956915
    ## 3  0.4794672 -0.5944068 -0.0532147  0.03418022 -0.2499822 -0.6677027
    ## 5  0.4604704 -0.8472403 -0.8636638  0.38745583 -0.7018852  0.2912249
    ## 7 -0.2755198 -0.1985313 -0.1481221  0.06705688 -0.4637973  0.3215718
    ## 8  0.4417951  0.7410542  1.0518842 -0.05687048 -0.6160840 -0.4325242
    ##         PC13         PC14        PC15        PC16        PC17         PC18
    ## 1 -0.8257636 -0.008442045 -0.67516096 -0.65423643 -0.66992794  0.369924775
    ## 2 -0.1599063 -0.904373583 -0.65317194  0.15233379  0.59116495 -0.006756961
    ## 3  0.2944002 -0.384341663  0.03610698  0.49998419 -0.42853779  0.010100668
    ## 5 -0.3559371  0.416356918 -0.45971018  0.31831991  0.16586505 -0.292769414
    ## 7  0.1691560 -0.303752256 -0.20524957 -0.08144726  0.01237246  0.186958309
    ## 8 -0.3997854 -0.551064230 -0.03878582 -0.06987301  0.31426440 -0.233423572
    ##           PC19        PC20          PC21        PC22        PC23
    ## 1  0.181282965 -0.35821051 -0.2253649809  0.03265919 -0.06006823
    ## 2 -0.157758642  0.35806203 -0.0153468176 -0.05161524 -0.07045722
    ## 3  0.355501917  0.29769399  0.0464175232 -0.20640227 -0.20331181
    ## 5  0.009391867 -0.38323632  0.0376443229  0.10522201  0.29024491
    ## 7 -0.273825679 -0.03890205 -0.0004404216  0.07188221 -0.10087356
    ## 8  0.105008347 -0.10053980 -0.1149138302 -0.05277938 -0.02465733
    ##          PC24         PC25        PC26        PC27        PC28        PC29
    ## 1  0.10686754 -0.183636997 -0.14635594 -0.25056215 -0.22049723 -0.07333322
    ## 2 -0.20827300  0.081383370 -0.16934887  0.01662508  0.20140004  0.04863533
    ## 3  0.07524367  0.098732152  0.15689666 -0.01411643  0.04788795  0.04925004
    ## 5  0.05963336 -0.038481185 -0.02115276  0.04683458  0.03346430 -0.03135800
    ## 7 -0.18189949 -0.009478499 -0.26049797  0.02085738  0.04136904 -0.04599174
    ## 8 -0.17580054  0.141538078 -0.16649023 -0.03553050  0.09351648  0.02930807
    ##            PC30          PC31 Diagnosis
    ## 1 -0.0275922755  0.0450772900         M
    ## 2  0.0014109275  0.0021986875         M
    ## 3 -0.0001750982 -0.0006896024         M
    ## 5 -0.0038684769 -0.0235921276         M
    ## 7  0.0156926792 -0.0036449941         M
    ## 8  0.0044926115 -0.0005983771         M

``` r
ggplot(pca_data, aes(x=PC1, y=PC2, color=Diagnosis)) + geom_point()
```

![](/figures/cancer_files/figure-markdown_github/unnamed-chunk-18-1.png)

look at the rotation data

``` r
pc1 <- ggplot(pca_data, aes(x=PC1, fill=data$diagnosis)) + geom_density(alpha=0.25)  
pc2 <- ggplot(pca_data, aes(x=PC2, fill=data$diagnosis)) + geom_density(alpha=0.25)  
grid.arrange(pc1, pc2, ncol=2)
```

![](/figures/cancer_files/figure-markdown_github/unnamed-chunk-19-1.png)

``` r
rotation_data <- data.frame(pca$rotation, variable=row.names(pca$rotation))
```

``` r
# capture the rotation matrix in a data frame
rotation_data <- data.frame(pca$rotation, variable=row.names(pca$rotation))
# define a pleasing arrow style
arrow_style <- arrow(length = unit(0.05, "inches"),
                     type = "closed")
# now plot, using geom_segment() for arrows and geom_text for labels
ggplot(rotation_data) + 
  geom_segment(aes(xend=PC1, yend=PC2), x=0, y=0, arrow=arrow_style) + 
  geom_text(aes(x=PC1, y=PC2, label=variable), hjust=0, size=3, color='red') + 
  xlim(-0.5, 0.5) + 
  ylim(-0.5, 0.5) +
  coord_fixed() # fix aspect ratio to 1:1
```

![](/figures/cancer_files/figure-markdown_github/unnamed-chunk-21-1.png)

Model selection
===============

Since the outcome varianle is diagnosis including Benign or Malign so We will use classification algorithm of supervised learning for this dataset

Different types of classification algorithms in Machine Learning :

1.  Logistic Regression

2.  Nearest Neighbor

3.  Support Vector Machines

4.  Kernel SVM

5.  Naïve Bayes

6.  Decision Tree Algorithm

7.  Random Forest Classification

We will use Classification Accuracy method to find the accuracy of our models. Classification Accuracy is what we usually mean, when we use the term accuracy. It is the ratio of number of correct predictions to the total number of input samples.

``` r
control <- trainControl(method="cv", number=10, classProbs = TRUE) #10-fold crossvalidation to estimate accuracy
metric <- "Accuracy"
```

``` r
#random forest
set.seed(2018)
fit.rf <- train(diagnosis~., data= data, method="rf", metric=metric, trControl=control)


set.seed(2018)
fit.knn <- train(diagnosis~., data = data, method = "knn", metric = metric, trControl = control)

#SVM 
set.seed(2018)
fit.svm <- train(diagnosis~., data = data, method = "svmRadial", metric = metric, trControl = control)
#
set.seed(2018)
fit.rpart <- train(diagnosis~., data = data, method = "rpart", metric = metric, trControl = control)
```

``` r
result <-  resamples(list(rpart = fit.rpart, svm = fit.svm, knn = fit.knn, rf = fit.rf))
summary(result)
```

    ## 
    ## Call:
    ## summary.resamples(object = result)
    ## 
    ## Models: rpart, svm, knn, rf 
    ## Number of resamples: 10 
    ## 
    ## Accuracy 
    ##            Min.   1st Qu.    Median      Mean   3rd Qu.      Max. NA's
    ## rpart 0.8666667 0.8913043 0.9120773 0.9144928 0.9344203 0.9565217    0
    ## svm   0.9111111 0.9399758 0.9671498 0.9627053 0.9782609 1.0000000    0
    ## knn   0.6666667 0.7213768 0.7608696 0.7519807 0.7826087 0.8222222    0
    ## rf    0.8913043 0.9399758 0.9666667 0.9561353 0.9782609 0.9782609    0
    ## 
    ## Kappa 
    ##            Min.   1st Qu.    Median      Mean   3rd Qu.      Max. NA's
    ## rpart 0.7227926 0.7666330 0.8119634 0.8169571 0.8594389 0.9075975    0
    ## svm   0.8109244 0.8720990 0.9297329 0.9204242 0.9537486 1.0000000    0
    ## knn   0.2474916 0.3434183 0.4296707 0.4222965 0.4933248 0.6129032    0
    ## rf    0.7578947 0.8701027 0.9301848 0.9051301 0.9527721 0.9539078    0

``` r
prediction <- predict(fit.rf, validation)
confusionMatrix(prediction, validation$diagnosis)
```

    ## Confusion Matrix and Statistics
    ## 
    ##           Reference
    ## Prediction  B  M
    ##          B 64  0
    ##          M  7 42
    ##                                           
    ##                Accuracy : 0.9381          
    ##                  95% CI : (0.8765, 0.9747)
    ##     No Information Rate : 0.6283          
    ##     P-Value [Acc > NIR] : 1.718e-14       
    ##                                           
    ##                   Kappa : 0.8717          
    ##  Mcnemar's Test P-Value : 0.02334         
    ##                                           
    ##             Sensitivity : 0.9014          
    ##             Specificity : 1.0000          
    ##          Pos Pred Value : 1.0000          
    ##          Neg Pred Value : 0.8571          
    ##              Prevalence : 0.6283          
    ##          Detection Rate : 0.5664          
    ##    Detection Prevalence : 0.5664          
    ##       Balanced Accuracy : 0.9507          
    ##                                           
    ##        'Positive' Class : B               
    ## 

``` r
dotplot(result)
```

![](/figures/cancer_files/figure-markdown_github/unnamed-chunk-26-1.png)

``` r
bwplot(result, metric = "Accuracy")
```

![](/figures/cancer_files/figure-markdown_github/unnamed-chunk-26-2.png) 


The results show that Random forest gives the best result for our dataset

Logistic regression using glm function
======================================

``` r
glm <-  glm(diagnosis ~.,
               data = data,
               family = binomial)
```

    ## Warning: glm.fit: algorithm did not converge

    ## Warning: glm.fit: fitted probabilities numerically 0 or 1 occurred

``` r
lr_data <- data.frame(predictor=glm$linear.predictors, prob=glm$fitted.values, Diagnosis = data$diagnosis)
```

To check how the two diagnosis outcomes are separated by the linear predictor.

``` r
ggplot(lr_data, aes(x=predictor, fill=Diagnosis)) + 
  geom_density(alpha=.5) +
  scale_fill_colorblind()
```

![](/figures/cancer_files/figure-markdown_github/unnamed-chunk-28-1.png)
