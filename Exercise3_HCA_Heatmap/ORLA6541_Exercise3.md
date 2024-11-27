---
title: "ORLA6541_Exercise3"
author: "Karlie (Yuxin) Meng, Maeghan Sill, & Summer Wu"
date: "2024-10-15"
output:
  html_document:
    keep_md: true
bibliography: references.bib
---

# 1. HCA Heatmap Replication

In this section, we replicate the [HCA heatmap tutorial in RMarkdown](https://doi.org/10.7916/r1mg-yn37) using the mtcars dataset [@bowersalexj.2023].

## 1.1 Importing Libraries


``` r
# Data preparation 
library(tidyverse)
```

```
## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
## ✔ dplyr     1.1.4     ✔ readr     2.1.5
## ✔ forcats   1.0.0     ✔ stringr   1.5.1
## ✔ ggplot2   3.5.1     ✔ tibble    3.2.1
## ✔ lubridate 1.9.3     ✔ tidyr     1.3.1
## ✔ purrr     1.0.2     
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()
## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors
```

``` r
# Creating complex heatmap visualizations
library(ComplexHeatmap)
```

```
## Loading required package: grid
## ========================================
## ComplexHeatmap version 2.21.1
## Bioconductor page: http://bioconductor.org/packages/ComplexHeatmap/
## Github page: https://github.com/jokergoo/ComplexHeatmap
## Documentation: http://jokergoo.github.io/ComplexHeatmap-reference
## 
## If you use it in published research, please cite either one:
## - Gu, Z. Complex Heatmap Visualization. iMeta 2022.
## - Gu, Z. Complex heatmaps reveal patterns and correlations in multidimensional 
##     genomic data. Bioinformatics 2016.
## 
## 
## The new InteractiveComplexHeatmap package can directly export static 
## complex heatmaps into an interactive Shiny app with zero effort. Have a try!
## 
## This message can be suppressed by:
##   suppressPackageStartupMessages(library(ComplexHeatmap))
## ========================================
```

``` r
# Supporting ComplexHeatmap by offering color functions and helping to manage complex layout structures within heatmaps.
library(circlize)
```

```
## ========================================
## circlize version 0.4.16
## CRAN page: https://cran.r-project.org/package=circlize
## Github page: https://github.com/jokergoo/circlize
## Documentation: https://jokergoo.github.io/circlize_book/book/
## 
## If you use it in published research, please cite:
## Gu, Z. circlize implements and enhances circular visualization
##   in R. Bioinformatics 2014.
## 
## This message can be suppressed by:
##   suppressPackageStartupMessages(library(circlize))
## ========================================
```

``` r
# Allowing us to perform hierarchical clustering with a unique approach that groups data into clusters while ordering them in a meaningful way
library(hopach)
```

```
## Loading required package: cluster
## Loading required package: Biobase
## Loading required package: BiocGenerics
## 
## Attaching package: 'BiocGenerics'
## 
## The following objects are masked from 'package:lubridate':
## 
##     intersect, setdiff, union
## 
## The following objects are masked from 'package:dplyr':
## 
##     combine, intersect, setdiff, union
## 
## The following objects are masked from 'package:stats':
## 
##     IQR, mad, sd, var, xtabs
## 
## The following objects are masked from 'package:base':
## 
##     anyDuplicated, aperm, append, as.data.frame, basename, cbind,
##     colnames, dirname, do.call, duplicated, eval, evalq, Filter, Find,
##     get, grep, grepl, intersect, is.unsorted, lapply, Map, mapply,
##     match, mget, order, paste, pmax, pmax.int, pmin, pmin.int,
##     Position, rank, rbind, Reduce, rownames, sapply, setdiff, sort,
##     table, tapply, union, unique, unsplit, which.max, which.min
## 
## Welcome to Bioconductor
## 
##     Vignettes contain introductory material; view with
##     'browseVignettes()'. To cite Bioconductor, see
##     'citation("Biobase")', and for packages 'citation("pkgname")'.
```

``` r
library(dendextend)
```

```
## Warning: package 'dendextend' was built under R version 4.3.3
```

```
## 
## ---------------------
## Welcome to dendextend version 1.18.1
## Type citation('dendextend') for how to cite the package.
## 
## Type browseVignettes(package = 'dendextend') for the package vignette.
## The github page is: https://github.com/talgalili/dendextend/
## 
## Suggestions and bug-reports can be submitted at: https://github.com/talgalili/dendextend/issues
## You may ask questions at stackoverflow, use the r and dendextend tags: 
## 	 https://stackoverflow.com/questions/tagged/dendextend
## 
## 	To suppress this message use:  suppressPackageStartupMessages(library(dendextend))
## ---------------------
## 
## 
## Attaching package: 'dendextend'
## 
## The following object is masked from 'package:hopach':
## 
##     prune
## 
## The following object is masked from 'package:stats':
## 
##     cutree
```

## 1.2 Dataset Preparation

**Previewing dataset:** This dataset has 32 observations and 11 numeric variables.


``` r
# Previewing the dataset mtcars
head(mtcars)
```

```
##                    mpg cyl disp  hp drat    wt  qsec vs am gear carb
## Mazda RX4         21.0   6  160 110 3.90 2.620 16.46  0  1    4    4
## Mazda RX4 Wag     21.0   6  160 110 3.90 2.875 17.02  0  1    4    4
## Datsun 710        22.8   4  108  93 3.85 2.320 18.61  1  1    4    1
## Hornet 4 Drive    21.4   6  258 110 3.08 3.215 19.44  1  0    3    1
## Hornet Sportabout 18.7   8  360 175 3.15 3.440 17.02  0  0    3    2
## Valiant           18.1   6  225 105 2.76 3.460 20.22  1  0    3    1
```

**Selecting variables:** We will focus on the first seven continuous variables for now, and the two dichotomous variables `vs` and `am` will be used for later annotations. *Note: During focus group presentation, they removed `cyl` since it is technically a discrete variable, Our group agreed that argument but still kept it since we considered it as a ratio scale which has a meaningful zero, and this decision aligns with the tutorial.\**


``` r
# Selecting the first seven continuous variables 
data <- mtcars[, c(1:7)]
```

**Scaling dataset:** As Dr. Bowers mentioned in class, we need to transform numeric variables into z-score so that all continuous variables are on the same scales.


``` r
# Transforming raw data to z-score
data_scaled <- scale(data)

# Previweing the transformed data 
head(data_scaled)
```

```
##                          mpg        cyl        disp         hp       drat
## Mazda RX4          0.1508848 -0.1049878 -0.57061982 -0.5350928  0.5675137
## Mazda RX4 Wag      0.1508848 -0.1049878 -0.57061982 -0.5350928  0.5675137
## Datsun 710         0.4495434 -1.2248578 -0.99018209 -0.7830405  0.4739996
## Hornet 4 Drive     0.2172534 -0.1049878  0.22009369 -0.5350928 -0.9661175
## Hornet Sportabout -0.2307345  1.0148821  1.04308123  0.4129422 -0.8351978
## Valiant           -0.3302874 -0.1049878 -0.04616698 -0.6080186 -1.5646078
##                             wt       qsec
## Mazda RX4         -0.610399567 -0.7771651
## Mazda RX4 Wag     -0.349785269 -0.4637808
## Datsun 710        -0.917004624  0.4260068
## Hornet 4 Drive    -0.002299538  0.8904872
## Hornet Sportabout  0.227654255 -0.4637808
## Valiant            0.248094592  1.3269868
```

## 1.3 An Unclustered Heatmap with `ComplexHeatmap`

**Key points to remember:**

-   The function name is `Heatmap()` with a capital "H" instead of a lowercase "h", which will use a different package.
-   In the context of heatmaps, hotter color (red) always means higher values and cooler color (blue) always means lower values. The default color is colorblind-friendly.
-   The parameter `name` specifies the name of the heatmap legend, which will appear as the title of the color scale legend and helps indicate what the colors represent.


``` r
Heatmap(data_scaled, name = "mtcars heatmap",
        cluster_rows = FALSE, cluster_columns = FALSE)
```

![](ORLA6541_Exercise3_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

**Some observations:**

-   The first column is for variable `mpg` (miles per gallon), where a higher value or a redder color entails fuel efficiency. So, we can see that gas-consuming cars like Cadillac Fleetwood and Lincoln Continental are bluer and the fuel-efficient cars like Fiat 128 and Toyota Corolla are redder. It’s interesting to see that the two Toyota Corolla entries have different `mpg` values/colors, and maybe they are different models or from different years.

-   Looking at `disp`, which represents the total volume of all cylinders in an internal combustion engine, we can see that the Cadillac Fleetwood, Lincoln Continental, and Chrysler Imperial have a redder color, indicating higher engine displacement. This suggests these cars have more powerful engines, which aligns with our previous observation about fuel-efficiency since more powerful engines typically consume more fuel.

-   The last column in the heatmap is `qsec`, representing the time needed for a car to travel a quarter mile from a standing start, measured in seconds. As we can see Ferrari Dino and Maserati Bora appear bluer, indicating that they accelerate faster, while Fiat 128, Toyota Corolla, and Merc 2xx are redder, suggesting that they accelerate slower.

## 1.4 Default Clustering

In the previous step, we created a heatmap without clustering (by setting `cluster_rows = FALSE` and `cluster_columns = FALSE`). Now, we will focus on clustering either the rows, columns, or both. To achieve clustering, we need to define two key metrics: the distance metric and the agglomeration (or clustering) method.

-   **Distance metric:** This metric determines which data points are closest to each other within the hyper-dimensional dataset.

-   **Agglomeration (clustering) method:** This defines how clusters are built based on the chosen distance metric.

There are various options available for each of these metrics, which we will explore later. In the `ComplexHeatmap` package, the default distance metric is "Euclidean," and the default agglomeration method is "complete linkage".

**1. Clustering rows only**


``` r
# Unmuting cluster_rows only 
Heatmap(data_scaled, name = "mtcars heatmap",
        cluster_rows = TRUE, cluster_columns = FALSE) # TRUE is the default, can be omitted
```

![](ORLA6541_Exercise3_files/figure-html/unnamed-chunk-6-1.png)<!-- -->

As shown, the rows have been reordered by clusters. Looking more closely at the two main clusters, we observe that the top cluster generally has larger `cyl`, `disp`, and `hp` values, while the bottom cluster has smaller values. However, the patterns remain somewhat unclear and could benefit from further refinement.

**2. Clustering columns only**


``` r
# Unmuting cluster_columns only 
Heatmap(data_scaled, name = "mtcars heatmap",
        cluster_rows = FALSE, cluster_columns = TRUE) # TRUE is the default, can be omitted
```

![](ORLA6541_Exercise3_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

Clustering columns solely may not provide detailed insights into individual data values, but it can reveal which columns are more closely related, indicating that the values of those variables tend to vary together across different rows

**3. Clustering both rows and columns**


``` r
# Unmuting both cluster_rows() and cluster_columns()
Heatmap(data_scaled, name = "mtcars heatmap",
        cluster_rows = TRUE, cluster_columns = TRUE)
```

![](ORLA6541_Exercise3_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

Clustering both rows and columns enhances the clarity of patterns, making them more distinguishable through color representation. In this heatmap, the rows and columns are reordered to reveal four main sections, formed by the intersections of two row clusters and two column clusters.

In the top cluster, we observe that cars generally have higher values for `wt` (heavier), `hp` (more horsepower), `cyl` (more cylinders), and `disp` (higher engine displacement), along with lower values for `mpg` (fuel-consuming), `drat` (higher rear axle ratio), and `qsec` (faster). The bottom cluster shows more variance, but overall, it exhibits reverse patterns compared to the top cluster.

**4. Using uncentered correlation and average linkage**

As we discussed in class, there are more suitable metrics for educational data than the default methods, particularly when it comes to agglomeration. In this section, the main focus is to explore uncertered correlation as the distance metric and average linkage as the agglomeration method, which has been recommended by Dr. Bowers in his previous paper [@bowers2010].

-   **Uncertered correlation:** The choice of the "best" measure is under contention, but it is favored over centered correlation since it addresses the need for sensitivity to magnitude differences across patterns, not just their directional similarities [@bowers2010; @bowersalexj.2023]. Mathematically, it is calculated as the cosine angle between two vectors, i.e., `cosangle`.

-   **Average linkage:** It is preferred especially due to its robustness to missing data [@bowers2010; @bowersalexj.2023].

To calculate uncentered correlation, we will use the `hopach` library, which provides access to bioinformatics tools for identifying more informative clustering patterns (CHA) in high-dimensional data [@bowers2010].


``` r
# Defining a function for uncertered correlation using cosangle 
function_uncertered_correlation <- function(matrix) {
  as.dist(as.matrix(distancematrix(matrix, d = "cosangle")))
}
```

The function we defined here, `function_uncertered_correlation()` will do three things:

1.  Calculates the cosine angle distance between each pair of rows in the data matrix;
2.  Converts the distance result to a matrix format;
3.  Converts the matrix into a distance object, suitable for clustering function.

We can call `function_uncertered_correlation()` to obtain the uncentered correlation for our data.


``` r
# Customizing distance metric and clustering metric for both rows and columns
r_cluster <- hclust(function_uncertered_correlation(data_scaled), method = "ave")
# Note that we need to transpose the matrix to cluster the columns 
c_cluster <- hclust(function_uncertered_correlation(t(data_scaled)), method = "ave")
```

**Key points to remember:**

-   Transpose the matrix using `t()` for column clustering

-   `method = "ave"` helps specify the aggolemeration method as average linkage.

Now, we can create the heatma.


``` r
# Creating heatmap
Heatmap(data_scaled, name = "mtcars heatmap",
        cluster_rows = r_cluster, cluster_columns = c_cluster)
```

![](ORLA6541_Exercise3_files/figure-html/unnamed-chunk-11-1.png)<!-- -->

Compared to the heatmap we made using the default metrics, the heatmap with uncertered correlation and average linkage show clearer patterns. As we can see, the row order and column order have changed since different clusters have been made based on our specified metrics.

This heatmap provides useful insights for consumers. Those who prioritize functionality over fuel economy might prefer cars from the bottom cluster, while those who value fuel economy may lean toward options in the top cluster. Notably, the Ferrari Dino has a unique pattern, combining characteristics of both functionality and fuel economy.

**5. Adding Annotations of Dichotomous Variables**

To enhance the heatmap with additional information, we can add the dichotomous variables from the dataset as annotation columns. We will color the values 0 and 1 as white and black, respectively. We can see the way annotation works as creating a plot for each dichotomous variable and then combining them with our main heatmap for the continuous variables using `draw()`.

In our dataset, the two dichotomous variables are Engine Type – `vs: 0 = V-shaped, 1 = straight` and Transformation Type – `am: 0 = automatic, 1 = manual` (*Note that Engine Type is slightly different from the Cylinder Config used in the tutorial*).


``` r
# Plot for Engine Type
plot_engine_type <- Heatmap(mtcars[, c(8)], name = "Engine Type",
                            col = colorRamp2(c(0, 1), c("white", "black")),
                            heatmap_legend_param = list(at = c(0, 1),
                                                        labels = c("V-shaped", "straight")),
                            width = unit(0.5, "cm"))

# Plot for Transmission Type 
plot_transmission_type <- Heatmap(mtcars[, c(9)], name = "Transmission Type",
                                 col = colorRamp2(c(0, 1), c("white", "black")),
                                 heatmap_legend_param = list(at = c(0, 1),
                                                             labels = c("automatic", "manual")),
                                 width = unit(0.5, "cm"))


# Assigning our heatmap to main object 
heatmap_main <- Heatmap(data_scaled, name = "mtcars heatmap",
                        cluster_rows = r_cluster, cluster_columns = c_cluster)

# Combining heatmap and annotations 
draw(heatmap_main + plot_engine_type + plot_transmission_type, auto_adjust = FALSE)
```

![](ORLA6541_Exercise3_files/figure-html/unnamed-chunk-12-1.png)<!-- -->

We can remove the row labels by unmuting `auto_adjust` since in educational dataset, they usually are just IDs.


``` r
draw(heatmap_main + plot_engine_type + plot_transmission_type, auto_adjust = TRUE)
```

![](ORLA6541_Exercise3_files/figure-html/unnamed-chunk-13-1.png)<!-- -->

With the annotations, we can observe that most rows in the top cluster (fuel-efficient, lower functionality) have manual transmissions with straight engines, while most rows in the bottom cluster (fuel-consuming, higher functionality) feature automatic transmissions with V-shaped engines.


``` r
# The order of the objects is crucial!!!
draw(plot_engine_type + heatmap_main + plot_transmission_type)
```

![](ORLA6541_Exercise3_files/figure-html/unnamed-chunk-14-1.png)<!-- -->

**Remember:** As the plot above shows, when combining the heatmap and annotations using `draw()`, [**the order of the objects is crucial!**]{.underline} If the annotations are added before the heatmap, the rows will no longer be ordered based on the desired clusters but instead will follow the clustering of the dichotomous variable. In short, the first object entered should be the plot with the desired clustering.

# 2. Exploring Heatmap Alternatives with ComplexHeatmap

The [@ComplexHeatmap] package enables us to add different kinds of aesthetics to the heatmap. Each of us tried different functions along reading Gu's e-book.

## 2.1 Colors

-   I tried the "green-white-red" schema which in my opinion is not as pretty as the "blue-white-red" schema, and it is not colorblind friendly.


``` r
# Setting up the the color schema 
col_fun = colorRamp2(c(-4, 0, 4), c("green", "white", "red"))
col_fun(seq(-4, 4))
```

```
## [1] "#00FF00FF" "#7DFF64FF" "#B1FF9AFF" "#DBFFCDFF" "#FFFFFFFF" "#FFCFBFFF"
## [7] "#FF9E81FF" "#FF6846FF" "#FF0000FF"
```

``` r
# Apply the color schema 
Heatmap(data_scaled, name = "mtcars heatmap",
        cluster_rows = r_cluster, cluster_columns = c_cluster, 
        col = col_fun)
```

![](ORLA6541_Exercise3_files/figure-html/unnamed-chunk-15-1.png)<!-- -->

-   Borders can be added using `border/border_gp`, which controls the global border of the heatmap body, and `rect_gp`, which controls the border of the grids/cells in the heatmap. We can also use `col` to set the color, `lwd` to set the width, and `lty` to set the line type. Adding borders seems to make cells and units clearer.


``` r
# Adding borders 
Heatmap(data_scaled, name = "mtcars heatmap",
        cluster_rows = r_cluster, cluster_columns = c_cluster, 
        # Global border
        border_gp = gpar(col = "black", lty = 5),
        # Cell borders
        rect_gp = gpar(col = "white", lwd = 2))
```

![](ORLA6541_Exercise3_files/figure-html/unnamed-chunk-16-1.png)<!-- -->

## 2.2 Titles

-   To the version of heatmap with borders, I added titles for columns and rows, change the position and alignment of titles, and add some aesthetics to the titles.


``` r
ht_opt$TITLE_PADDING = unit(c(8.5, 8.5), "points")
# Adding titles 
Heatmap(data_scaled, name = "mtcars heatmap",
        cluster_rows = r_cluster, cluster_columns = c_cluster, 
        # Global border
        border_gp = gpar(col = "black", lty = 5),
        # Cell borders
        rect_gp = gpar(col = "white", lwd = 2),
        # Adding titles 
        column_title = "car features", row_title = "car models",
        # Rotating row title 
        row_title_rot = 0, 
        # Changing the positions
        column_title_side = "bottom", row_title_side = "right",
        # Adding aesthetics for titles 
        column_title_gp = gpar(fill = "skyblue", col = "black", border = "black"),
        row_title_gp = gpar(fill = "skyblue", col = "black", border = "black"))
```

![](ORLA6541_Exercise3_files/figure-html/unnamed-chunk-17-1.png)<!-- -->

## 2.3 Clustering

We tried different clusering methods in this section, which convinced us that uncertered correlation with average linkage gives the clearer pattern.

-   Clustering using `pearson`


``` r
#Heatmaps using pearson correlation distance
Heatmap(data_scaled, name = "mtcars heatmap pearson",
        clustering_distance_rows = "pearson"
        )
```

![](ORLA6541_Exercise3_files/figure-html/unnamed-chunk-18-1.png)<!-- -->

-   Clustering using `diana`.


``` r
Heatmap(data_scaled, name = "mtcarsdiana", cluster_rows = diana(data_scaled),
   cluster_columns = diana(t(data_scaled)))
```

![](ORLA6541_Exercise3_files/figure-html/unnamed-chunk-19-1.png)<!-- -->

-   Clustering using `agnes`.


``` r
Heatmap(data_scaled, name = "mtcarsagnes", cluster_rows = agnes(data_scaled),
   cluster_columns = agnes(t(data_scaled)))
```

![](ORLA6541_Exercise3_files/figure-html/unnamed-chunk-20-1.png)<!-- -->

-   Let's color the clusters for rows and columns!


``` r
# Color the dendrogram for rows 
colored_den_row = color_branches(r_cluster, k = 2, col = c("blue", "red"))
# Color the dendrogram for columns 
colored_den_col = color_branches(c_cluster, k = 2, col = c("blue", "red"))

Heatmap(data_scaled, name = "mtcars heatmap",
        # Clustering the rows and columns with colors 
        cluster_rows = colored_den_row, cluster_columns = colored_den_col, 
        # Global border
        border_gp = gpar(col = "black", lty = 5),
        # Cell borders
        rect_gp = gpar(col = "white", lwd = 2),
        # Adding titles 
        column_title = "car features", row_title = "car models",
        # Rotating row title 
        row_title_rot = 0, 
        # Changing the positions
        column_title_side = "bottom", row_title_side = "right",
        # Adding aesthetics for titles 
        column_title_gp = gpar(fill = "skyblue", col = "black", border = "black"),
        row_title_gp = gpar(fill = "skyblue", col = "black", border = "black"))
```

![](ORLA6541_Exercise3_files/figure-html/unnamed-chunk-21-1.png)<!-- -->

## 2.4 Heatmap Split

-   Trying to split by transmission


``` r
mtcars$transmission <- factor(mtcars$am, labels = c("Automatic", "Manual"))

ht_main = Heatmap(data_scaled, name = "cluster rows",
                  row_split = mtcars$transmission)

ht_main
```

![](ORLA6541_Exercise3_files/figure-html/unnamed-chunk-22-1.png)<!-- -->

-   Split by categorical variables.


``` r
mtcars$configuration <- factor(mtcars$vs, labels = c("V", "Inline"))
mtcars$trans_conf_split <- factor(paste(mtcars$transmission, mtcars$configuration, sep = "_"))

ht_main = Heatmap(data_scaled, name = "cluster rows",
                  row_split = mtcars$trans_conf_split, row_gap = unit(c(2), "mm"), border = TRUE)

ht_main
```

![](ORLA6541_Exercise3_files/figure-html/unnamed-chunk-23-1.png)<!-- -->

-   Something we found very interesting is to use the dots and connect lined to show the value sign and and the change of direction.


``` r
ht_main = Heatmap(data_scaled, name = "cluster rows",
                  cluster_rows=r_cluster, cluster_columns = c_cluster,
                  layer_fun = function(j, i, x, y, w, h, fill) {
                    ind_mat = restore_matrix(j, i, x, y)
                    for(ir in seq_len(nrow(ind_mat))) {
                      for(ic in seq_len(ncol(ind_mat))[-1]) {
                        ind1 = ind_mat[ir, ic-1]
                        ind2 = ind_mat[ir, ic]
                        v1 = data_scaled[i[ind1], j[ind1]]
                        v2 = data_scaled[i[ind2], j[ind2]]
                        if(v1 * v2 > 0) {
                          col = ifelse(v1 > 0, "darkred", "darkblue")
                          grid.segments(x[ind1], y[ind1], x[ind2], y[ind2],
                                        gp = gpar(col = col, lwd =2))
                          grid.points(x[c(ind1, ind2)], y[c(ind1, ind2)],
                                      pch = 16, gp = gpar(col = col), size = unit(4, "mm"))
                        }
                      }
                    }
                  })

ht_main
```

![](ORLA6541_Exercise3_files/figure-html/unnamed-chunk-24-1.png)<!-- -->

-   Adding annotations for groups


``` r
ht_main = Heatmap(data_scaled, name = "cluster rows",
                  cluster_rows=r_cluster, column_km = 4,
                  top_annotation = HeatmapAnnotation(foo = anno_block(gp = gpar(fill = 2:4),
                                                                     labels = c("group1", "group2", "group3", "group4"),
                                                                     labels_gp = gpar(col = "white", fontsize = 10))))

ht_main
```

![](ORLA6541_Exercise3_files/figure-html/unnamed-chunk-25-1.png)<!-- -->

-   Let's split the heatmap using k-means! Eyeballing it seems to gives me 4 row clusters and 2 column clusters, so I set `row_km = 4` and `column_km = 2`. But 4 and 2 are very arbitrary, basically `k` can take any value, so I don't think for this dataset, especially since I am not familiar with cars, it is very hard to pre-define the number of clusters using k-means. In fact, we tried different values for `k`. Regardless of what value we took, they all seemed to make sense in terms of proximity of values. So it is really hard to select the "best" value. We actually wonder in the field of education, when would k-means be useful.


``` r
Heatmap(data_scaled, name = "mtcars heatmap",
        # Clustering the rows and columns with colors 
        row_km = 4, column_km = 2,
        # Global border
        border_gp = gpar(col = "black", lty = 5),
        # Cell borders
        rect_gp = gpar(col = "white", lwd = 2),
        # Adding titles 
        column_title = "car features", row_title = "car models",
        # Rotating row title 
        row_title_rot = 0, 
        # Changing the positions
        column_title_side = "bottom", row_title_side = "right",
        # Adding aesthetics for titles 
        column_title_gp = gpar(fill = "skyblue", col = "black", border = "black"),
        row_title_gp = gpar(fill = "skyblue", col = "black", border = "black"))
```

![](ORLA6541_Exercise3_files/figure-html/unnamed-chunk-26-1.png)<!-- -->

-   Then I tried to split by dendrogram, setting `row_split = 2` and `column_split = 2`.


``` r
Heatmap(data_scaled, name = "mtcars heatmap",
        # Clustering the rows and columns with colors 
        cluster_rows = colored_den_row, cluster_columns = colored_den_col, 
        # Splitting by dendrograms 
        row_split = 2, column_split = 2,
        # Coloring titles
        row_title_gp = gpar(col = c("blue", "red"), font = 1:2),
        column_title_gp = gpar(col = c("blue", "red"), font = 1:2),
        # Global border
        border_gp = gpar(col = "black", lty = 1),
        # Cell borders
        rect_gp = gpar(col = "white", lwd = 2))
```

![](ORLA6541_Exercise3_files/figure-html/unnamed-chunk-27-1.png)<!-- -->

-   Adding other descriptive visualization is very cool because it can help summarize the distribution while the heatmap can help display the individual data points. Especially when the row number gets larger, it is very hard for human to intuitively think about the distribution of each variable. Therefore, here, we tried to add annotation plots fo visualize the distribution of each variable.

    
    ``` r
    # Setting up the annotations 
    density_annotation = columnAnnotation(density = anno_density(data_scaled, 
                                                           height = unit(3, "cm"),
                                                           gp = gpar(fill = 1:7)),
                                           annotation_name_rot = -90)
             
    Heatmap(data_scaled, name = "mtcars heatmap",
            # Clustering the rows and columns with colors 
            cluster_rows = colored_den_row, cluster_columns = colored_den_col, 
            # Splitting by dendrograms 
            row_split = 2, column_split = 2,
            # Coloring titles
            row_title_gp = gpar(col = c("blue", "red"), font = 1:2),
            column_title_gp = gpar(col = c("blue", "red"), font = 1:2),
            # Global border
            border_gp = gpar(col = "black", lty = 1),
            # Cell borders
            rect_gp = gpar(col = "white", lwd = 2),
            # Adding density annotations 
            bottom_annotation = density_annotation,
            # Rotating the column names
            column_names_rot = -90)
    ```
    
    ![](ORLA6541_Exercise3_files/figure-html/unnamed-chunk-28-1.png)<!-- -->

# 3. Research Proposal

-   **What is the purpose of your study?**

The purpose of this study is to explore the relationship between individual differences in affective characteristics and physiological responses to emotions, and their relation to academic achievement. By integrating subjective and objective measures, we aim to illustrate the complex and varying relationship between emotion and cognition. We will use hierarchical clustering analysis (HCA) heatmaps to discover distinct subgroups with similar patterns in state and trait characteristics and non-cumulative GPA. This discovery will create opportunities for further research into holistic, personalized interventions for college students to improve their educational attainment.

-   **Is there any research literature and theory that supports this argument? How so?**

Emotion, from an evolutionary perspective, is widely regarded as an adaptive response that has been crucial for the survival and success of humans as a species [@al-shawaf2015]. Many scholars have identified the basic emotions that all humans share, and Plutchik (2001) categorizes human emotions into eight categories: fear, anger, joy, sadness, trust, disgust, surprise, and anticipation. According to Plutchik (2001), these emotions have evolved to respond to essential life challenges. For instance, the emotion of fear is induced when people cognize an event as dangerous and thus desire to seek safety through a “fight-or-flight” response [@plutchik2001].

The evolutionary perspective of emotions not only illuminates the role of emotions in historical human development but also offers insights into understanding the complex emotional dynamics in contemporary educational settings – if emotions aid survival, they should be involved in learning [@linnenbrink-garcia2011]. Indeed, in academic environments, students often encounter a spectrum of emotions ranging from positive feelings like enjoyment in learning, hopefulness for success, and pride in their achievements to negative emotions such as anger over academic demands, fear about potential exam failure, and boredom during class activities [@handbook2015]. Previous studies have demonstrated that these emotions are instrumental in achievement and personal growth [@clore2009]; [@internat2014]. Aligning with common-sense logic, positive emotions enable students to envision goals, promote creative problem-solving, and support self-regulation [@clore2009]. Alternatively, negative emotions in learning can impede academic performance, promote school dropouts, and negatively influence health [@internat2014].

-   **Why is cluster analysis heatmaps a means to address this purpose?**

Hierarchical clustering (HCA) with heatmaps provides a clear visual representation of the variance between the participants across multiple affective, physiological, and academic variables. This study incorporates many subjective (e.g., emotional regulation surveys) and objective (e.g., heart rate, electrodermal activity) measures, creating a multidimensional dataset that makes it challenging to identify relationships with simpler methods like regression analysis. HCA can handle and synthesize complex data with interrelated variables, unlike linear models.

By standardizing each variable, we can represent each participant across each variable and can reveal natural groupings among the participants even though they vary greatly. Heatmaps allow for an intuitive visual representation of the clusters, which, when combined with hierarchical clustering analysis, make similar participant profiles easier to see. Since our research question is exploratory in nature, HCA is ideal. It enables us to investigate relationships without imposing causal or linear assumptions. Furthermore, if unique subgroups are present, identifying clusters with higher or lower non-cumulative GPAs could lead to future research in personalized interventions for emotional and physiological health to improve educational achievement.

-   **What would be the research question(s)? (To what extent…)**

To what extent are college students’ state and trait characteristics, including emotional regulation, depression level, mood state, and physiological response to emotions, related to their non-cumulative GPA?

-   **What type of dataset would you need? Is there a dataset you know of that would work?**

To comprehensively measure students’ emotions and explore their role in students’ outcomes, the ideal dataset for this study will include three data topics: self-reported data to various emotion-related psychological scales, physiological responses to different emotions, and students’ outcomes. An example dataset was collected by Gao and colleagues (2021), which includes physiological signals from 89 healthy college students during resting state, emotion induction, recovery phases, and various cognitive function assessment tasks [@gao2021]. While this dataset provides necessary information for our research question, it does not include any performance indicators, and the sample size is not sufficient. Therefore, we would need to find or create a dataset that includes both the subjective and objective measures of emotions along with non-cumulative GPA.

-   **What types of data would you be looking for?**

Building on the approach of Gao et al. (2021), we plan to create a new dataset that collects college students’ course performance scores alongside their self-report and physiological data about emotions. Here, students’ letter grades in each course will be utilized as the outcome variables and annotations in a heatmap. To understand participants’ affective characteristics, they will each complete an emotion regulation questionnaire (ERQ), self-rating depression scale (SDS), and profile of mood states (POMS). Their physiological responses to different emotions will be collected by measuring their average heart rate and electrodermal activity while watching a series of videos that will individually evoke one of the 8 major emotions (fear, anger, joy, sadness, trust, disgust, surprise, and anticipation).  

-   **Provide the generalized equation for the clustering and a brief narrative in which you specify the type of clustering, following the examples from the readings.**

In this study, we apply hierarchical clustering using an average linkage approach to explore the relationship between various emotional metrics (such as stress, motivation, and engagement) and cognitive performance, represented by student letter grades. Using uncentered correlation, we capture the similarity in performance trends across students without assuming mean-centered data, as grades and emotional responses may be consistently offset by external factors. This method helps us observe and differentiate clusters of students who may have similar emotional profiles affecting their cognition in comparable ways, even when their absolute performance levels differ. The uncentered correlation function, r(xi,yi) is defined in Equation 1 . Here, ​ $x_i$ and $y_i$ represent data points within the vectors for each emotional and cognitive performance metric respectively.

$r(x_i, y_i) = \frac{1}{n} \sum_{i=1}^n \left( \frac{x_i}{\sigma_x^{(0)}} \right) \left( \frac{y_i}{\sigma_y^{(0)}} \right)$

The modified standard deviation $\sigma_x$ and $\sigma_y$ for vectors *x* and *y* is calculated in equation 2 and 3. These values allow us to compute similarity measures for data vectors with non-zero mean.

$(\sigma_x^{(0)} = \sqrt{\frac{1}{n} \sum_{i=1}^n (x_i)^2})​$

$(\sigma_y^{(0)} = \sqrt{\frac{1}{n} \sum_{i=1}^n (y_i)^2})$

To determine clusters, the average linkage between clusters A and B​ is defined in equation 4.

$(D(A, B) = \frac{1}{n_A n_B} \sum_{i=1}^{n_A} \sum_{j=1}^{n_B} r(x_i, y_j))$

By leveraging the uncentered correlation measure with average linkage, this clustering approach enables us to identify nuanced relationships between emotions and academic outcomes, providing insights into patterns where traditional-centered correlations might fail to reveal meaningful differences.

-   **What do you think you would find?**

Our hypothesis aligns with the current literature, which states that the effects of emotions rely on their valence (positive vs. negative). Thus, we expect higher course scores for students with generally positive state and trait emotion characteristics and lower course scores for the opposite. We also expect to see a relationship between students’ physiological responses to positive and negative emotions and their academic performance.

-   **Why would this be important? What would be the implications for this research domain?**

Due to the diversity and complexity of learning emotions, most current research tends to focus on a specific emotion, and then synthesize the findings from different studies with data collected from different participants. In this study, through the use of an HCA heatmap, we are able to incorporate all types of emotions as variables for measurement without any selection. This allows for a more direct comparison of how different emotions impact learning outcomes. Most importantly, our heatmap can help verify the taxonomy of academic achievement emotions, assessing whether categorizing emotions based on the two dimensions of valence and activation is reliable, or if additional dimensions may have been overlooked.

# 4. Billboard Top 100 Songs

## 4.1 Dataset Preparation

### **4.1.1 Loading dataset**


``` r
# Directly loading from Github 
audio_features <- read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2021/2021-09-14/audio_features.csv')
```

```
## Rows: 29503 Columns: 22
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr  (7): song_id, performer, song, spotify_genre, spotify_track_id, spotify...
## dbl (14): spotify_track_duration_ms, danceability, energy, key, loudness, mo...
## lgl  (1): spotify_track_explicit
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

### **4.1.2 Data cleaning**

For this exercise, we will focus on songs whose `spotify_track_popularity > 90`.


``` r
# Filtering songs based on spotify_track_popularity
popularity_larger_than_90 <- audio_features |> filter(spotify_track_popularity > 90)

# Preview the dataset
head(popularity_larger_than_90)
```

```
## # A tibble: 6 × 22
##   song_id  performer song  spotify_genre spotify_track_id spotify_track_previe…¹
##   <chr>    <chr>     <chr> <chr>         <chr>            <chr>                 
## 1 7 Rings… Ariana G… 7 Ri… ['dance pop'… 6ocbgoVGwYJhOv1… <NA>                  
## 2 AdictoT… Tainy, A… Adic… ['pop reggae… 3jbT1Y5MoPwEIpZ… <NA>                  
## 3 Adore Y… Harry St… Ador… ['dance pop'… 1M4qEo4HE3PRaCO… https://p.scdn.co/mp3…
## 4 All I W… Mariah C… All … ['dance pop'… 0bYg9bo50gSsH3L… https://p.scdn.co/mp3…
## 5 All I W… Mariah C… All … ['dance pop'… 0bYg9bo50gSsH3L… https://p.scdn.co/mp3…
## 6 Astrona… Masked W… Astr… ['australian… 3Ofmpyhv5UAQ70m… https://p.scdn.co/mp3…
## # ℹ abbreviated name: ¹​spotify_track_preview_url
## # ℹ 16 more variables: spotify_track_duration_ms <dbl>,
## #   spotify_track_explicit <lgl>, spotify_track_album <chr>,
## #   danceability <dbl>, energy <dbl>, key <dbl>, loudness <dbl>, mode <dbl>,
## #   speechiness <dbl>, acousticness <dbl>, instrumentalness <dbl>,
## #   liveness <dbl>, valence <dbl>, tempo <dbl>, time_signature <dbl>,
## #   spotify_track_popularity <dbl>
```

We can see that "All I Want For Christmas Is You" by Mariah Carey have two rows. So I would like to explore if there are any other songs having more than one row.


``` r
# Locating the songs that have more than one rows
popularity_larger_than_90 |> 
  group_by(song_id) |> 
  filter(n() > 1) |> 
  ungroup()
```

```
## # A tibble: 14 × 22
##    song_id performer song  spotify_genre spotify_track_id spotify_track_previe…¹
##    <chr>   <chr>     <chr> <chr>         <chr>            <chr>                 
##  1 All I … Mariah C… All … ['dance pop'… 0bYg9bo50gSsH3L… https://p.scdn.co/mp3…
##  2 All I … Mariah C… All … ['dance pop'… 0bYg9bo50gSsH3L… https://p.scdn.co/mp3…
##  3 Bad Gu… Billie E… Bad … ['electropop… 2Fxmhks0bxGSBdJ… <NA>                  
##  4 Bad Gu… Billie E… Bad … ['electropop… 2Fxmhks0bxGSBdJ… <NA>                  
##  5 Callai… Bad Bunn… Call… ['latin', 'r… 2TH65lNHgvLxCKX… https://p.scdn.co/mp3…
##  6 Callai… Bad Bunn… Call… ['latin', 'r… 2TH65lNHgvLxCKX… https://p.scdn.co/mp3…
##  7 Lucid … Juice WR… Luci… ['chicago ra… 285pBltuF7vW8Te… <NA>                  
##  8 Lucid … Juice WR… Luci… ['chicago ra… 285pBltuF7vW8Te… <NA>                  
##  9 Old To… Lil Nas … Old … ['country ra… 2YpeDb67231RjR0… https://p.scdn.co/mp3…
## 10 Old To… Lil Nas … Old … ['country ra… 2YpeDb67231RjR0… https://p.scdn.co/mp3…
## 11 Someon… Lewis Ca… Some… ['pop', 'uk … 7qEHsqek33rTcFN… <NA>                  
## 12 Someon… Lewis Ca… Some… ['pop', 'uk … 7qEHsqek33rTcFN… <NA>                  
## 13 Trampo… SHAED     Tram… ['electropop… 1iQDltZqI7BXnHr… <NA>                  
## 14 Trampo… SHAED     Tram… ['electropop… 1iQDltZqI7BXnHr… <NA>                  
## # ℹ abbreviated name: ¹​spotify_track_preview_url
## # ℹ 16 more variables: spotify_track_duration_ms <dbl>,
## #   spotify_track_explicit <lgl>, spotify_track_album <chr>,
## #   danceability <dbl>, energy <dbl>, key <dbl>, loudness <dbl>, mode <dbl>,
## #   speechiness <dbl>, acousticness <dbl>, instrumentalness <dbl>,
## #   liveness <dbl>, valence <dbl>, tempo <dbl>, time_signature <dbl>,
## #   spotify_track_popularity <dbl>
```

Upon closer inspection, we can observe that these songs only differ in terms of their `spotify_track_popularity`. For example, *Bad Guy* by Billie Eilish has two values for this variable: 95 and 96. Since this variable will not be used for HCA, we don’t need to worry about it and can further clean the dataset by selecting only the columns we need.

Note that in the `mtcars` dataset, car model names are set as row names, and `Heatmap()` showed them as the row labels. So here, we need to make the `song_id` column as row names as well.


``` r
# Locating the indeces of the columns we need 
colnames(popularity_larger_than_90)
```

```
##  [1] "song_id"                   "performer"                
##  [3] "song"                      "spotify_genre"            
##  [5] "spotify_track_id"          "spotify_track_preview_url"
##  [7] "spotify_track_duration_ms" "spotify_track_explicit"   
##  [9] "spotify_track_album"       "danceability"             
## [11] "energy"                    "key"                      
## [13] "loudness"                  "mode"                     
## [15] "speechiness"               "acousticness"             
## [17] "instrumentalness"          "liveness"                 
## [19] "valence"                   "tempo"                    
## [21] "time_signature"            "spotify_track_popularity"
```

``` r
filtered_songs <- popularity_larger_than_90 |>
  # Selecting the columns we need 
  select(1, 8, 10:16, 18:20) |>
  # Dropping duplicates
  distinct() |>
  # Moving the dichotomous variables to the last two columns
  relocate(c(spotify_track_explicit, mode), .after = tempo) |>
  # Renaming the dichotomous variable to match the example heatmap for later convenience
  rename(Explicit = spotify_track_explicit,
         Mode = mode) |>
  # Making song_id as the colnames
  column_to_rownames(var = "song_id")
```

## 4.2 HCA Heatmap

### **4.2.1. Scaling data**


``` r
# Locating the indeces of the continuous variables 
colnames(filtered_songs)
```

```
##  [1] "danceability" "energy"       "key"          "loudness"     "speechiness" 
##  [6] "acousticness" "liveness"     "valence"      "tempo"        "Explicit"    
## [11] "Mode"
```

``` r
# Selecting continuous variables 
variables_to_scale <- filtered_songs[, c(1:8)]

# Converting numbers to z-score
songs_scaled <- scale(variables_to_scale)
```

### **4.2.2 HCA**

Now, we have scaled the data for HCA, and the object is called `songs_scaled`. We can start creating the heatmap. The biggest known unknown / challenge is we are not sure what metrics the example heatmap used.

Here is the example heatmap:

**We can start with the defaults.**


``` r
Heatmap(songs_scaled, name = "billboard audio heatmap",
        cluster_rows = TRUE, cluster_columns = TRUE)
```

![](ORLA6541_Exercise3_files/figure-html/unnamed-chunk-34-1.png)<!-- -->

Though the heatmap is hard to see since we haven't adjust the figure size yet, we can already tell that the defaults did not render us the desired clusters for rows and columns.

**Lets try Dr. Bowers' favorite combination: Uncertered correlation with average linkage for both rows and columns**

Since we have define the function for uncentered correlation, we can directly use it.


``` r
# Row clustering by uncentered correlation with average linkage 
songs_r_uncertered_ave <- hclust(function_uncertered_correlation(songs_scaled), method = "ave")

# Column clustering by uncentered correlation with average linkage 
songs_c_uncertered_ave <- hclust(function_uncertered_correlation(t(songs_scaled)), method = "ave")
```


``` r
# Heatmap 
Heatmap(songs_scaled, name = "billboard audio heatmap",
        cluster_rows = songs_r_uncertered_ave, cluster_columns = songs_c_uncertered_ave)
```

![](ORLA6541_Exercise3_files/figure-html/unnamed-chunk-36-1.png)<!-- -->

We are still not there... But!!! it worked for the column clustering!!! So, the main task now is to figure out what metrics were used for row clustering.

However, we run out of the time to find the "correct" metric. We will try to figure it out when we have time.

# 5. References
