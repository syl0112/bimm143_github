# Class 7: Machine Learning 1
Shuk Yee Leung (PID: A18050465)

- [Clustering](#clustering)
  - [K-means](#k-means)
  - [Hierarchical Clustering](#hierarchical-clustering)
- [Principal Component Analysis
  (PCA)](#principal-component-analysis-pca)
  - [Data import](#data-import)
  - [PCA to the rescue](#pca-to-the-rescue)
  - [Interpreting PCA Results](#interpreting-pca-results)

Today we will explore unsupervised machine learning methods starting
with clustering and dimensionality reduction.

## Clustering

To start, let’s make up some data to cluster where we know what the
answer should be. The `rnorm()` function will help us here.

``` r
hist( rnorm(10000, mean=3) )
```

![](class7Labwork_files/figure-commonmark/unnamed-chunk-1-1.png)

Return 30 numbers centered on -3

``` r
tmp <- c( rnorm(30, mean=-3), 
   rnorm(30, mean=+3) )

x <- cbind(x=tmp, y=rev(tmp))

x
```

                  x         y
     [1,] -3.712289  3.285517
     [2,] -1.808793  2.771956
     [3,] -1.151842  4.378393
     [4,] -3.435336  3.810618
     [5,] -3.198435  2.784968
     [6,] -1.028267  3.708219
     [7,] -5.025494  3.661622
     [8,] -4.415091  3.728412
     [9,] -2.094654  3.836935
    [10,] -1.390886  4.512311
    [11,] -2.576030  2.578658
    [12,] -4.825249  4.269764
    [13,] -2.108179  2.134932
    [14,] -3.114615  3.144624
    [15,] -2.975684  3.977292
    [16,] -4.473181  2.710370
    [17,] -3.033992  4.880918
    [18,] -3.267905  2.637680
    [19,] -3.231101  3.236758
    [20,] -2.898364  3.106736
    [21,] -2.236460  2.006881
    [22,] -1.137022  2.478910
    [23,] -3.626393  2.450835
    [24,] -2.287861  2.630911
    [25,] -3.811380  4.618519
    [26,] -2.925326  3.091622
    [27,] -3.457305  3.391314
    [28,] -3.546393  3.360025
    [29,] -3.233833  4.196447
    [30,] -4.249532  2.869543
    [31,]  2.869543 -4.249532
    [32,]  4.196447 -3.233833
    [33,]  3.360025 -3.546393
    [34,]  3.391314 -3.457305
    [35,]  3.091622 -2.925326
    [36,]  4.618519 -3.811380
    [37,]  2.630911 -2.287861
    [38,]  2.450835 -3.626393
    [39,]  2.478910 -1.137022
    [40,]  2.006881 -2.236460
    [41,]  3.106736 -2.898364
    [42,]  3.236758 -3.231101
    [43,]  2.637680 -3.267905
    [44,]  4.880918 -3.033992
    [45,]  2.710370 -4.473181
    [46,]  3.977292 -2.975684
    [47,]  3.144624 -3.114615
    [48,]  2.134932 -2.108179
    [49,]  4.269764 -4.825249
    [50,]  2.578658 -2.576030
    [51,]  4.512311 -1.390886
    [52,]  3.836935 -2.094654
    [53,]  3.728412 -4.415091
    [54,]  3.661622 -5.025494
    [55,]  3.708219 -1.028267
    [56,]  2.784968 -3.198435
    [57,]  3.810618 -3.435336
    [58,]  4.378393 -1.151842
    [59,]  2.771956 -1.808793
    [60,]  3.285517 -3.712289

Make a plot of `x`

``` r
plot(x)
```

![](class7Labwork_files/figure-commonmark/unnamed-chunk-3-1.png)

### K-means

The main function in “base” R for K-means clustering is called
`kmeans()`:

``` r
km <- kmeans(x, centers = 2)
km
```

    K-means clustering with 2 clusters of sizes 30, 30

    Cluster means:
              x         y
    1 -3.009230  3.341723
    2  3.341723 -3.009230

    Clustering vector:
     [1] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 2 2 2 2 2 2 2 2
    [39] 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2

    Within cluster sum of squares by cluster:
    [1] 50.41873 50.41873
     (between_SS / total_SS =  92.3 %)

    Available components:

    [1] "cluster"      "centers"      "totss"        "withinss"     "tot.withinss"
    [6] "betweenss"    "size"         "iter"         "ifault"      

The `kmeans()` function return a “list” with 9 components. You can see
the named components of any list with the `attributes()` function.

``` r
attributes(km)
```

    $names
    [1] "cluster"      "centers"      "totss"        "withinss"     "tot.withinss"
    [6] "betweenss"    "size"         "iter"         "ifault"      

    $class
    [1] "kmeans"

> Q. How many points are in each cluster?

``` r
km$size
```

    [1] 30 30

> Q. Cluster assignment/membership vector?

``` r
km$cluster
```

     [1] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 2 2 2 2 2 2 2 2
    [39] 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2

> Q. Cluster centers?

``` r
km$centers
```

              x         y
    1 -3.009230  3.341723
    2  3.341723 -3.009230

> Q. Make a plot of our `kmeans()` results showing cluster assignment
> using different colors for each cluster/group of points and cluster
> centers in blue.

``` r
plot(x, col=km$cluster )
points(km$centers, col="blue", pch=15, cex=2)
```

![](class7Labwork_files/figure-commonmark/unnamed-chunk-9-1.png)

> Q. Run `kmeans()` again on `x` and this cluster into 4 groups/clusters
> and plot the same result figure as above.

``` r
km4 <- kmeans(x, centers = 4)
km4
```

    K-means clustering with 4 clusters of sizes 30, 10, 6, 14

    Cluster means:
              x         y
    1  3.341723 -3.009230
    2 -1.781999  3.103811
    3 -4.466655  3.643038
    4 -3.261212  3.382525

    Clustering vector:
     [1] 4 2 2 4 4 2 3 3 2 2 2 3 2 4 4 3 4 4 4 4 2 2 4 2 3 4 4 4 4 3 1 1 1 1 1 1 1 1
    [39] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1

    Within cluster sum of squares by cluster:
    [1] 50.418733 10.485198  3.740266  6.364792
     (between_SS / total_SS =  94.6 %)

    Available components:

    [1] "cluster"      "centers"      "totss"        "withinss"     "tot.withinss"
    [6] "betweenss"    "size"         "iter"         "ifault"      

``` r
plot(x, col=km4$cluster)
points(km4$centers, col="blue", pch=15, cex=2)
```

![](class7Labwork_files/figure-commonmark/unnamed-chunk-11-1.png)

> **key-point**: K-means clustering is supper popular but can be
> miss-used. One big limination is that it can impose a clustering
> pattern on your data even if clear natural grouping don’t exist -
> i.e. it does what you tell it to so un therms of `centers`.

### Hierarchical Clustering

The main function in “base” R for Hierarchical clustering is called
`hclust()`.

You can’t just pass out dataset as is into `hclust()`, you must give
“distance matrix” as input. We can get this from the `dist()` function
in R.

``` r
d <- dist(x)
hc <- hclust(d)
hc
```


    Call:
    hclust(d = d)

    Cluster method   : complete 
    Distance         : euclidean 
    Number of objects: 60 

The results of `hclust()` don’t have a useful `print()` method but do
have a special `plot()` method.

``` r
plot(hc)
abline(h=8, col="red")
```

![](class7Labwork_files/figure-commonmark/unnamed-chunk-13-1.png)

To get our main cluster assignment (membershop vector), we need to “cut”
the tree at the big goal posts…

``` r
grps <- cutree(hc, h=8)
grps
```

     [1] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 2 2 2 2 2 2 2 2
    [39] 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2

``` r
table(grps)
```

    grps
     1  2 
    30 30 

``` r
plot(x, col=grps)
```

![](class7Labwork_files/figure-commonmark/unnamed-chunk-16-1.png)

Hierarchical Clustering is distinct in that the dendrogram (tree figure)
can reveal the potential grouping in your data (unlike K-means).

## Principal Component Analysis (PCA)

PCA is a common and highly useful dimensionality reduction technique
used in many fields - particullary bioinformatics.

Here we will analyze some data from the UK on food consumption.

### Data import

``` r
url <- "https://tinyurl.com/UK-foods"
x <- read.csv(url)

head (x)
```

                   X England Wales Scotland N.Ireland
    1         Cheese     105   103      103        66
    2  Carcass_meat      245   227      242       267
    3    Other_meat      685   803      750       586
    4           Fish     147   160      122        93
    5 Fats_and_oils      193   235      184       209
    6         Sugars     156   175      147       139

``` r
rownames(x) <- x[ ,1]
x <- x[ ,-1]
head(x)
```

                   England Wales Scotland N.Ireland
    Cheese             105   103      103        66
    Carcass_meat       245   227      242       267
    Other_meat         685   803      750       586
    Fish               147   160      122        93
    Fats_and_oils      193   235      184       209
    Sugars             156   175      147       139

``` r
x <- read.csv(url, row.names = 1)
head(x)
```

                   England Wales Scotland N.Ireland
    Cheese             105   103      103        66
    Carcass_meat       245   227      242       267
    Other_meat         685   803      750       586
    Fish               147   160      122        93
    Fats_and_oils      193   235      184       209
    Sugars             156   175      147       139

``` r
barplot(as.matrix(x), beside=T, col=rainbow(nrow(x)))
```

![](class7Labwork_files/figure-commonmark/unnamed-chunk-20-1.png)

``` r
barplot(as.matrix(x), beside=F, col=rainbow(nrow(x)))
```

![](class7Labwork_files/figure-commonmark/unnamed-chunk-21-1.png)

One conventional plot that can be useful is called a “paris” plot.

``` r
pairs(x, col=rainbow(10), pch=16)
```

![](class7Labwork_files/figure-commonmark/unnamed-chunk-22-1.png)

### PCA to the rescue

### Interpreting PCA Results

The main function in base R for PCA is called `prcopm()`

``` r
pca <- prcomp( t(x) )
summary(pca)
```

    Importance of components:
                                PC1      PC2      PC3       PC4
    Standard deviation     324.1502 212.7478 73.87622 3.176e-14
    Proportion of Variance   0.6744   0.2905  0.03503 0.000e+00
    Cumulative Proportion    0.6744   0.9650  1.00000 1.000e+00

The `prcomp()` function returns a list object of our results with five
attributes/components.

``` r
attributes(pca)
```

    $names
    [1] "sdev"     "rotation" "center"   "scale"    "x"       

    $class
    [1] "prcomp"

The two main “results” in here are `pca$x` and `pca$rotation`. The first
of these (`pca$x`) contains the scores of the data on the new PC axis -
we use these to make our “PCA plot”.

``` r
pca$x
```

                     PC1         PC2        PC3           PC4
    England   -144.99315   -2.532999 105.768945 -4.894696e-14
    Wales     -240.52915 -224.646925 -56.475555  5.700024e-13
    Scotland   -91.86934  286.081786 -44.415495 -7.460785e-13
    N.Ireland  477.39164  -58.901862  -4.877895  2.321303e-13

``` r
library(ggplot2)
library(ggrepel)

#Make a plot of pca$x with PC1 vs PC2
ggplot(pca$x) +
  aes(PC1, PC2, label=rownames(pca$x)) +
  geom_point() +
  geom_text_repel()
```

![](class7Labwork_files/figure-commonmark/unnamed-chunk-26-1.png)

We map the relationship between variables in terms of the major
principal components.

The second major result is contained the `pca$rotation` object or
component. Let’s plot this to see what PCA is picking up…

``` r
ggplot(pca$rotation) +
  aes(PC1, rownames(pca$rotation)) +
  geom_col()
```

![](class7Labwork_files/figure-commonmark/unnamed-chunk-27-1.png)

Fresh potatoes and soft drink have large positive loadings push N.
Ireland toward the right side of PCA plot. This indicates that N.
Ireland consumes more fresh potatoes and soft drink compared to other
countries. Also, fresh fruit and alcoholic drinks have large negative
loadings push Scotland, England, and Walves toward the left side of PCA
plot. This indicates that Scotland, England, and Walves consume more
fresh potatoes and soft drink compared to N.Ireland.
