---
title: "ORLA6541_Exercise4"
date: "2024-11-15"
output:
  html_document:
    keep_md: true
bibliography: references.bib
---

# Necessary Packages


``` r
# Data preparation 
library(tidyverse) # For smaller dataset
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
library(data.table) # For large-scale dataset 
```

```
## Warning: package 'data.table' was built under R version 4.3.3
```

```
## 
## Attaching package: 'data.table'
## 
## The following objects are masked from 'package:lubridate':
## 
##     hour, isoweek, mday, minute, month, quarter, second, wday, week,
##     yday, year
## 
## The following objects are masked from 'package:dplyr':
## 
##     between, first, last
## 
## The following object is masked from 'package:purrr':
## 
##     transpose
```

``` r
# CART
library(rpart) # For creating Classification and Regression Trees (CART)
library(caret) # For training and evaluating machine learning models
```

```
## Loading required package: lattice
## 
## Attaching package: 'caret'
## 
## The following object is masked from 'package:purrr':
## 
##     lift
```

``` r
library(rpart.plot) # For plotting decision trees 
library(modelr) # For regression tree evaluation
library(randomForest) # For random forest
```

```
## Warning: package 'randomForest' was built under R version 4.3.3
```

```
## randomForest 4.7-1.2
## Type rfNews() to see new features/changes/bug fixes.
## 
## Attaching package: 'randomForest'
## 
## The following object is masked from 'package:dplyr':
## 
##     combine
## 
## The following object is masked from 'package:ggplot2':
## 
##     margin
```

``` r
library(rfUtilities) # Cross validation for random forest 

# ROC AUC 
library(pROC) # For calculating and plotting ROC AUC        
```

```
## Type 'citation("pROC")' for a citation.
## 
## Attaching package: 'pROC'
## 
## The following objects are masked from 'package:stats':
## 
##     cov, smooth, var
```

``` r
# Our best friend 
library(ggplot2) # For visualization when needed 

# Dataframe to markdown table
library(knitr)
```

```
## Warning: package 'knitr' was built under R version 4.3.3
```

# Replicating Bowers and Zhou (2019)

The first part of this markdown will try to replicate the figure and tables in [@bowers2019] with the reference to the online supplement offered by them [@bowersalexj.2018].

## 1. Loading Dataset


``` r
# Direct link to the zipped dataset
url <- "https://nces.ed.gov/datalab/files/zip/OnlineCodebook/ELS_2002-12_PETS_v1_0_Student_CSV_Datasets.zip"

# Set up temporary file paths
temp <- tempfile()
temp_dir <- tempdir()

# Download the zip file to the temporary file
download.file(url, temp, mode = "wb")

# Unzip the contents to the temporary directory
unzip(temp, exdir = temp_dir)

# Getting the path to the csv file 
csv_file_path <- file.path(temp_dir, "els_02_12_byf3pststu_v1_0.csv")

# Read the file to R 
els_02_all <- read.csv(csv_file_path)

# Freeing up space
unlink(temp)
```


``` r
# Checking dimension
dim(els_02_all)
```

```
## [1] 16197  4012
```


``` r
# Turning STU_ID as row index 
els_02_all <- els_02_all |> column_to_rownames(var = "STU_ID")

# Convert all variable names in the dataset to uppercase
names(els_02_all) <- toupper(names(els_02_all))
```

## 2. Dropout

### 2.1 Dropout and Dichotomous Predictors

When working with dichotomous predictors for dropout, I encountered many challenges. In the markdown provided by Bowers and Zhou (2018), only the code for the ROC AUC part was included, and it directly used pre-processed data. As a result, it was difficult for me to fully understand the data engineering logic. I had to rely on my intuition and observations from descriptive statistics table to infer the processing logic.

There were no issues with converting continuous variables into dichotomous ones, as evident from my descriptive statistics. However, when creating composite flags, I couldn’t replicate the descriptive statistics reported in the paper. The only thing I could confirm was that the variable `one_or_more_flags` had no missing values, as the number of non-missing values matched the total number of rows in the dataset For other composite predictors, each had a different number of missing values as described in the original paper, but I couldn’t obtain further information. I had to rely on my own judgment to recreate the logic.

Here are the basic logic I took:

1.  If any of the four flags were present, `one_or_more_flags` will be coded as `1` and the rest of the cases will be `0`.
2.  For the other composite flags, like `any_one_flag`, the rows do not meet the positive conditions were coded as 0, and I considered rows with values of `NA` and those with a mixture of `0` and `NA` as missing values.

After several hours to try different approach, I had to give up. Although the results I obtained differed from the data in the paper, I had to proceed with the analysis using these data.

*Notes: Discrepancies of the variable descriptive in appendix A and ELS 2002 Codebook also brought some challenges. For example, the appendix said the variable `absent` only considers 1-4 times of absence, which correspond to `2` and `3` in ELS 2002 dataset, but this has led mismatch between my calculated stats and the ones in appendix A. After trails and errors, I realized the stats calculation in Appendix A actually includes the value for "four times and above", responding to values of `2`, `3`, and `4`.*


``` r
dropout_flag <- els_02_all |>
  # Selecting dropout and flag prectictors for dropout
  select(F2EVERDO, BYP52E, BYS24F, BYP51, BYTXMSTD, BYTXRSTD) |>
  # Converting all negative values to NA 
  mutate(across(everything(), ~ ifelse(. < 0, NA, .))) 

# Finding cutoff points for math score flag 
math_cutoff <- quantile(dropout_flag$BYTXMSTD, probs = 0.25, na.rm = TRUE)
print(math_cutoff) # 44.0675, different from the the one used in online supplement, 44.065
```

```
##     25% 
## 44.0675
```

``` r
# Finding cutoff points for reading score flag 
reading_cutoff <- quantile(dropout_flag$BYTXRSTD, probs = 0.25, na.rm = TRUE)
print(reading_cutoff) # 43.64
```

```
##   25% 
## 43.64
```

``` r
# Converting to binary flags
dropout_binary <- dropout_flag |>
  reframe(dropout = F2EVERDO,
          absent = ifelse(is.na(BYP52E), NA, ifelse(BYP52E %in% c(2, 3, 4), 1, 0)),
          suspension = ifelse(is.na(BYS24F), NA, ifelse(BYS24F %in% c(2, 3, 4, 5), 1, 0)),
          misbehavior = BYP51,
          math_flag = ifelse(BYTXMSTD < math_cutoff, 1, 0),
          reading_flag = ifelse(BYTXRSTD < reading_cutoff, 1, 0))
```


``` r
flags = c("absent", "suspension", "misbehavior", "reading_flag")

# Function for custom flag logic
compute_flag <- function(row, n) {
  num_ones <- sum(row == 1, na.rm = TRUE)  # Count of 1s in the row
  has_na <- any(is.na(row))               # Check if there's any NA
  
  if (num_ones == n) {
    1  # Exact match
  } else if (num_ones > 0) {
    0  # Positive but not equal to `n`
  } else if (has_na) {
    NA # No 1s but has NA
  } else {
    0  # All 0s and no NA
  }
}

dropout_flags_df <- dropout_binary |>
  # All rows that do not have any 1 were considered as 0 
  mutate(one_or_more_flags = as.integer(rowSums(across(all_of(flags)) == 1, na.rm = TRUE) > 0)) |>
  # Composite flags 
  mutate(
    any_one_flag = apply(across(all_of(flags)), 1, compute_flag, n = 1),
    any_two_flags = apply(across(all_of(flags)), 1, compute_flag, n = 2),
    any_three_flags = apply(across(all_of(flags)), 1, compute_flag, n = 3),
    all_four_flags = apply(across(all_of(flags)), 1, compute_flag, n = 4)
  )
```


``` r
# A function for descriptive stats 
calculate_stats <- function(x) {
  data.frame(
    N = sum(!is.na(x)),       
    Mean = mean(x, na.rm = TRUE), 
    SD = sd(x, na.rm = TRUE),    
    Min = min(x, na.rm = TRUE),    
    Max = max(x, na.rm = TRUE)   
  )
}

# Apply the function to the dichonomous predictors for dropout 
descriptive_stats <- do.call(rbind, lapply(dropout_flags_df, calculate_stats))

kable(descriptive_stats, 
      format = "markdown", 
      caption = "Descriptive Statistics for Dichonomous Predictors for Dropout")
```



Table: Descriptive Statistics for Dichonomous Predictors for Dropout

|                  |     N|      Mean|        SD| Min| Max|
|:-----------------|-----:|---------:|---------:|---:|---:|
|dropout           | 16197| 0.1129839| 0.3165829|   0|   1|
|absent            | 12286| 0.1295784| 0.3358527|   0|   1|
|suspension        | 14476| 0.0806853| 0.2723606|   0|   1|
|misbehavior       | 12457| 0.0724894| 0.2593069|   0|   1|
|math_flag         | 15892| 0.2500000| 0.4330263|   0|   1|
|reading_flag      | 15892| 0.2479864| 0.4318575|   0|   1|
|one_or_more_flags | 16197| 0.3520405| 0.4776213|   0|   1|
|any_one_flag      | 13204| 0.3169494| 0.4653051|   0|   1|
|any_two_flags     | 13204| 0.0894426| 0.2853923|   0|   1|
|any_three_flags   | 13204| 0.0217358| 0.1458252|   0|   1|
|all_four_flags    | 13204| 0.0037110| 0.0608071|   0|   1|

As we can see, the descriptive statistics are largely different from the ones from the paper, and I suspect this will affect the curves and areas in later analysis.


``` r
roc1a_1 = roc(dropout_flags_df$dropout, dropout_flags_df$absent,
              legacy.axes= TRUE, asp = FALSE,
              main = "ROC Curves for Dichonomous Predictors for Dropout",
              plot = TRUE, grid = FALSE, lty = 1, 
              xaxs = "i", yaxs = "i")
```

```
## Setting levels: control = 0, case = 1
```

```
## Setting direction: controls < cases
```

``` r
roc1a_2 = roc(dropout_flags_df$dropout, dropout_flags_df$math_flag,
              plot = TRUE, add = TRUE, lty = 2)
```

```
## Setting levels: control = 0, case = 1
## Setting direction: controls < cases
```

``` r
roc1a_3 = roc(dropout_flags_df$dropout, dropout_flags_df$suspension,
              plot = TRUE, add = TRUE, lty = 3)
```

```
## Setting levels: control = 0, case = 1
## Setting direction: controls < cases
```

``` r
roc1a_4 = roc(dropout_flags_df$dropout, dropout_flags_df$misbehavior,
              plot = TRUE, add = TRUE, lty = 4)
```

```
## Setting levels: control = 0, case = 1
## Setting direction: controls < cases
```

``` r
roc1a_5 = roc(dropout_flags_df$dropout, dropout_flags_df$one_or_more_flags,
              plot = TRUE, add = TRUE, lty = 5, col = "#ACF4AD")
```

```
## Setting levels: control = 0, case = 1
## Setting direction: controls < cases
```

``` r
roc1a_6 = roc(dropout_flags_df$dropout, dropout_flags_df$any_one_flag,
              plot = TRUE, add = TRUE, lty = 6, col = "#1E88E5")
```

```
## Setting levels: control = 0, case = 1
## Setting direction: controls < cases
```

``` r
roc1a_7 = roc(dropout_flags_df$dropout, dropout_flags_df$any_two_flags,
              plot = TRUE, add = TRUE, lty = 7, col = "#FFC107")
```

```
## Setting levels: control = 0, case = 1
## Setting direction: controls < cases
```

``` r
roc1a_8 = roc(dropout_flags_df$dropout, dropout_flags_df$any_three_flags,
              plot = TRUE, add = TRUE, lty = 8, col = "#D81B60")
```

```
## Setting levels: control = 0, case = 1
## Setting direction: controls < cases
```

``` r
roc1a_9 = roc(dropout_flags_df$dropout, dropout_flags_df$all_four_flags,
              plot = TRUE, add = TRUE, lty = 9, col = "#004D40")
```

```
## Setting levels: control = 0, case = 1
## Setting direction: controls < cases
```

``` r
legend(
  "bottomright",
  legend = c(
    "Absent",
    "1st                math t score (2022)",
    "Suspension",
    "Misbehavior",
    "One or more flags",
    "Any one flag",
    "Any two flags",
    "Any three flags",
    "All four flags"
  ),
  lty = c(1, 2, 3, 4, 5, 6, 7, 8, 9),
  col = c("black", "black", "black", "black", "#ACF4AD", "#1E88E5", "#FFC107", "#D81B60", "#004D40"),
  lwd = 2,
  cex = 0.8
)
```

![](ORLA6541_Exercise4_files/figure-html/unnamed-chunk-8-1.png)<!-- -->


``` r
# Creating a AUC table for Dropout Dichonomous predictors 
auc_table1a <- data.frame(
  Predictor = c(
    "Absent",
    "1st quantile math t score (2022)",
    "Suspension",
    "Misbehavior",
    "One or more flags",
    "Any one flag",
    "Any two flags",
    "Any three flags",
    "All four flags"
  ),
  AUC = c(
    auc(roc1a_1),
    auc(roc1a_2),
    auc(roc1a_3),
    auc(roc1a_4),
    auc(roc1a_5),
    auc(roc1a_6),
    auc(roc1a_7),
    auc(roc1a_8),
    auc(roc1a_9)
  )) |>
    arrange(desc(AUC)) |>
    mutate(AUC = round(AUC, 3)
    )

kable(auc_table1a, format = "markdown", caption = "AUC Values for Dichonomous Dropout Predictors")
```



Table: AUC Values for Dichonomous Dropout Predictors

|Predictor                        |   AUC|
|:--------------------------------|-----:|
|One or more flags                | 0.693|
|1st quantile math t score (2022) | 0.647|
|Absent                           | 0.643|
|Any one flag                     | 0.600|
|Misbehavior                      | 0.593|
|Any two flags                    | 0.587|
|Suspension                       | 0.584|
|Any three flags                  | 0.535|
|All four flags                   | 0.508|

As I imagined, the plot is very differently from the one from the paper. Looking at the AUC values, I noticed that the takeaways from the plot are also totally different. The paper suggests that `any one flag` is the most accurate composite flag, but it is only the 4th accurate feature in my plot. The value is more than 0.1 slower than the value in the paper and I think that is a huge difference. Similarly, `any two flags` also shows large difference. It demonstrates how important data engineering is in data science. The paper mentioned the ensemble approach employs Boolean operators and I do wonder how that is realized in codes.

From our ROC AUC plot and AUC value table, we can see that `one or more flags` is the only composite flag that has higher AUC value than the individual flag, having a value of 0.693. Compared to the table from the paper, this value is slightly lower, as shown by the alike curve in my plot.

The failure to replicate the engineered features did not affect the performance of `any three flags` and `all four flags`. We can also observe that the AUC values and ROC are the same as those in the paper since I successfully reproduced individual flags.

### 2.2 Dropout and Continuous Predictors

In this section, I will try to replicate Figure 1 Panel (B) in the paper.

**STEP 1: Data Preparation**


``` r
dropout_continuous_df <- els_02_all |>
  # Selecting dropout and continuous prectictors for dropout
  select(F2EVERDO, BYP52E, BYS24F, BYTXMSTD, BYTXRSTD, BYS42) |>
  # Converting all negative values to NA 
  mutate(across(everything(), ~ ifelse(. < 0, NA, .)))|>
  # Renaming variables
  mutate(dropout = F2EVERDO,
         absence = BYP52E,
         suspension = BYS24F,
         math_score = BYTXMSTD, 
         reading_score = BYTXRSTD, 
         extracurricular_activities_02 = BYS42) 
```

**STEP 2: Plotting ROC AUC**


``` r
# Creating the ROC AUC plot and plotting ROC curve for predictor Reading t Score
roc1b_1 = roc(dropout_continuous_df$dropout, dropout_continuous_df$reading_score,
              legacy.axes = TRUE, asp = FALSE, 
              main = "ROC Curves for Predicting Dropout",
              plot=TRUE, grid=FALSE, lty = 1, col = "#D81B60", 
              xaxs="i", yaxs="i")
```

```
## Setting levels: control = 0, case = 1
```

```
## Setting direction: controls > cases
```

``` r
# Adding ROC curve for predictor Math t Score
roc1b_2 = roc(dropout_continuous_df$dropout, dropout_continuous_df$math_score,
              plot = TRUE, add = TRUE, lty = 2, col = "#1E88E5")
```

```
## Setting levels: control = 0, case = 1
## Setting direction: controls > cases
```

``` r
# Adding ROC curve for predictor Extracurricular Activities (2022)
roc1b_3 = roc(dropout_continuous_df$dropout, dropout_continuous_df$extracurricular_activities_02, 
              plot = TRUE, add = TRUE, percent = roc1b_1$percent, lty = 3, col = "#FFC107")
```

```
## Setting levels: control = 0, case = 1
## Setting direction: controls > cases
```

``` r
# Adding ROC curve for predictor Absence
roc1b_4 = roc(dropout_continuous_df$dropout, dropout_continuous_df$absence, 
              plot = TRUE, add = TRUE, percent = roc1b_1$percent, lty=4, col = "#3E9474")
```

```
## Setting levels: control = 0, case = 1
```

```
## Setting direction: controls < cases
```

``` r
# Adding ROC curve for predictor Suspension
roc1b_5 = roc(dropout_continuous_df$dropout, dropout_continuous_df$suspension,
              plot = TRUE, add = TRUE, percent = roc1b_1$percent, lty=5)
```

```
## Setting levels: control = 0, case = 1
## Setting direction: controls < cases
```

``` r
# Adding legend to the plot 
legend("bottomright", 
       legend= c("Reading t Score (2002)",
                "Math t Score (2002)",
                "Extracurricular Activities (2002)",
                "Absence",
                "Suspension"), 
       lty = c(1,2,3,4,5), 
       col = c("#D81B60", "#1E88E5", "#FFC107", "#3E9474", "black"),
       lwd=2)
```

![](ORLA6541_Exercise4_files/figure-html/unnamed-chunk-11-1.png)<!-- -->


``` r
# Creating a AUC table for Dropout continuous predictors 
auc_table1b <- data.frame(Predictor = c("Reading t Score (2002)",
                                       "Math t Score (2002)",
                                       "Extracurricular Activities (2002)",
                                       "Absence",
                                       "Suspension"), 
                         AUC = c(auc(roc1b_1), 
                                 auc(roc1b_2), 
                                 auc(roc1b_3), 
                                 auc(roc1b_4), 
                                 auc(roc1b_5))) |>
  arrange(desc(AUC)) |>
  mutate(AUC = round(AUC, 3))

kable(auc_table1b, format = "markdown", caption = "AUC Values for Continuous Dropout Predictors")
```



Table: AUC Values for Continuous Dropout Predictors

|Predictor                         |   AUC|
|:---------------------------------|-----:|
|Math t Score (2002)               | 0.722|
|Reading t Score (2002)            | 0.706|
|Extracurricular Activities (2002) | 0.672|
|Absence                           | 0.647|
|Suspension                        | 0.584|

I successfully replicated the plots and table in Penal (B) in Figure 2!!! The AUC values are the same as the ones in the paper!!! So excited!!! The process was less challenging since this part utilized continuous variables and there weren't any difficulties in data preparation.

As we can see from the plot and the table, `Math t Score` and `Reading t Score` are the two most accurate predictors for dropout, with AUC values of `0.722` and `0.706`. But as the paper suggested, the test data are NAEP data, so the findings may not apply when state assessment data are utilzied. `Absence` and `Suspension`, when treated as continuous variables, still do not have high accuracy.

We can also run statistical test to see if the difference these AUC values are significant or not.


``` r
# Pairwise significance test for AUC difference 
roc1b <- list(roc1b_1, roc1b_2, roc1b_3, roc1b_4, roc1b_5)

pairs <- combn(seq_along(roc1b), 2, simplify = FALSE)

tests1b <- lapply(pairs, function(pair) {
  roc.test(roc1b[[pair[1]]], roc1b[[pair[2]]], method = "delong")
})
```

```
## Warning in roc.test.roc(roc1b[[pair[1]]], roc1b[[pair[2]]], method = "delong"):
## DeLong's test should not be applied to ROC curves with a different direction.
## Warning in roc.test.roc(roc1b[[pair[1]]], roc1b[[pair[2]]], method = "delong"):
## DeLong's test should not be applied to ROC curves with a different direction.
## Warning in roc.test.roc(roc1b[[pair[1]]], roc1b[[pair[2]]], method = "delong"):
## DeLong's test should not be applied to ROC curves with a different direction.
## Warning in roc.test.roc(roc1b[[pair[1]]], roc1b[[pair[2]]], method = "delong"):
## DeLong's test should not be applied to ROC curves with a different direction.
## Warning in roc.test.roc(roc1b[[pair[1]]], roc1b[[pair[2]]], method = "delong"):
## DeLong's test should not be applied to ROC curves with a different direction.
## Warning in roc.test.roc(roc1b[[pair[1]]], roc1b[[pair[2]]], method = "delong"):
## DeLong's test should not be applied to ROC curves with a different direction.
```


``` r
# Creating a significance stats table for Dropout continuous predictors

predictor_1 <- c("Reading t Score (2002)", "Reading t Score (2002)", "Reading t Score (2002)",
                 "Reading t Score (2002)", "Math t Score (2002)", "Math t Score (2002)", 
                 "Math t Score (2002)", "Extracurricular Activities (2002)", 
                 "Extracurricular Activities (2002)", "Absence")

predictor_2 <- c("Math t Score (2002)", "Extracurricular Activities (2002)", "Absence", 
                 "Suspension", "Extracurricular Activities (2002)", "Absence", 
                 "Suspension", "Absence", "Suspension", "Suspension")

z_values <- sapply(tests1b, function(tests1b) round(tests1b$statistic, 3))

p_values <- sapply(tests1b, function(tests1b) ifelse(tests1b$p.value < 0.001, "<0.001", round(tests1b$p.value, 3)))

table_1b <- data.frame(
  Predictor_1 = predictor_1,
  Predictor_2 = predictor_2,
  Z = z_values,
  P_Value = p_values
)

kable(table_1b, 
      format = "markdown", 
      caption = "Significance of AUC Difference for Continious Dropout Predictors")
```



Table: Significance of AUC Difference for Continious Dropout Predictors

|Predictor_1                       |Predictor_2                       |      Z|P_Value |
|:---------------------------------|:---------------------------------|------:|:-------|
|Reading t Score (2002)            |Math t Score (2002)               | -3.220|0.001   |
|Reading t Score (2002)            |Extracurricular Activities (2002) |  3.641|<0.001  |
|Reading t Score (2002)            |Absence                           |  6.456|<0.001  |
|Reading t Score (2002)            |Suspension                        | 14.571|<0.001  |
|Math t Score (2002)               |Extracurricular Activities (2002) |  5.744|<0.001  |
|Math t Score (2002)               |Absence                           |  8.006|<0.001  |
|Math t Score (2002)               |Suspension                        | 16.735|<0.001  |
|Extracurricular Activities (2002) |Absence                           |  2.790|0.005   |
|Extracurricular Activities (2002) |Suspension                        | 10.380|<0.001  |
|Absence                           |Suspension                        |  5.846|<0.001  |

Uh... I noticed a few things:

1.  I received warnings which suggest that delong test should only apply to ROC curves with the same direction. That means, the two predictors in the same pair should both have either positive or negative relationship with the outcome variable. In our analysis, the logic is higher score and more extracurricular activities indicate non-dropout, and higher values of suspension and absence indicate dropout (as evidenced from the warnings). I initially ignored those warnings, but as we can see from the table, the values are not correct, at least different from the paper statistics. So I tried manually setting the directions but that rendered a different plot, with the changed direction being under the random guess line. This makes me wonder if we should compare "positive" and "negative" predictors' AUC at the same time since they seem to be not comparable. But the metafile rendered without any warnings and got the "correct" statistics, so I wonder what I missed in the code.
2.  I noticed that the Z stat I got are opposite from the ones the paper presented. This did not bother me too much since it is a relative score and would not affect p-value, but I still wonder why that happened since I assumed that had something to do with the order of ROC results in the function, which I had made sure they are the same as the metafile.
3.  From those correct stats, we can see that all pairs are significantly different at a 0.001 level.

## 3. College Enrollment

This section will focus on replicating Figure 3 Panel (A) and Table 2 Part 1 in the paper.

**STEP 1: Data Preparation**


``` r
college_enrollment_df <- els_02_all |>
  # Selecting college enrollment and prectictors 
  select(F2PS0601, F1RGPP2, F1S27, BYS33A) |>
  # Converting all negative values to NA 
  mutate(across(everything(), ~ ifelse(. < 0, NA, .))) |>
  # Renaming variables 
  rename(college_enrollment = F2PS0601,
         gpa = F1RGPP2,
         extracurricular_activities_04 = F1S27,
         ap = BYS33A) |>
  # Recoding college_enrollment to binary 
  mutate(college_enrollment = ifelse(college_enrollment == 1, 1, 0))
```

**STEP 2: Plotting ROC AUC**


``` r
# Creating the ROC AUC plot and plotting ROC curve for predictor GPA
roc2a_1 = roc(college_enrollment_df$college_enrollment, college_enrollment_df$gpa, 
              legacy.axes=TRUE, asp=FALSE, 
              main = "ROC Curves for Predicting College Enrollment",
              plot=TRUE, grid=FALSE, lty=1,
              xaxs="i", yaxs="i")
```

```
## Setting levels: control = 0, case = 1
```

```
## Setting direction: controls < cases
```

``` r
# Adding ROC curve for predicator AP 
roc2a_2 = roc(college_enrollment_df$college_enrollment, college_enrollment_df$ap,
              plot=TRUE, add = TRUE, percent=roc2a_1$percent, lty=2)
```

```
## Setting levels: control = 0, case = 1
## Setting direction: controls < cases
```

``` r
# Adding ROC curve for predicator Extracurricular Activities
roc2a_3 = roc(college_enrollment_df$college_enrollment, college_enrollment_df$extracurricular_activities_04,
              plot=TRUE, add = TRUE, percent=roc2a_1$percent, lty=3)
```

```
## Setting levels: control = 0, case = 1
## Setting direction: controls < cases
```

``` r
# Adding legend to the plot 
legend("bottomright", legend=c("GPA","AP","Extrac. Activities (2004)"), lty=c(1,2,3), lwd=2)
```

![](ORLA6541_Exercise4_files/figure-html/unnamed-chunk-16-1.png)<!-- -->


``` r
# Creating a AUC table for College Enrollment predictors
auc_table2a <- data.frame(
  Predictor = c("GPA", "AP", "Extracurricular Activities (2024)"),
  AUC = c(auc(roc2a_1), auc(roc2a_2), auc(roc2a_3))
) |>
  arrange(desc(AUC)) |>
  mutate(AUC = round(AUC, 3))

kable(auc_table2a, 
      format = "markdown", 
      caption = "AUC Values of College Enrollment predictors")
```



Table: AUC Values of College Enrollment predictors

|Predictor                         |   AUC|
|:---------------------------------|-----:|
|GPA                               | 0.767|
|Extracurricular Activities (2024) | 0.642|
|AP                                | 0.565|

Sure, `GPA` is the most accurate predictors among the three, with the AUC value of 0.767. This suggests that high school grades and performance is very important in the current application procedure, and high school education, at least for now, should continue working on improving student' score to get them ready for college. `AP` is the least accurate predictor among the three and in the plot it is close to the random guess line, since participation in college-level course is more like a bonus point instead of a deterministic factor. This really provides some insights for my midterm since I particularly focused on relationship betwen AP particitation and college readiness.

**STEP 3: Significance of AUC Difference for Predictors**


``` r
# GPA vs AP
test2a_1 <- roc.test(roc2a_1, roc2a_2, method = "delong") 
# GPA vs Extracurricular Activities
test2a_2 <- roc.test(roc2a_1, roc2a_3, method = "delong") 
# AP vs Extracurricular Activities 
test2a_3 <- roc.test(roc2a_2, roc2a_3, method = "delong") 
```


``` r
# Creating a significance stats table for College Enrollment predictors
table_2a <- data.frame(
  Predictor_1 = c("GPA", "GPA", "AP"),
  Predictor_2 = c("AP", "Extracurricular Activities", "Extracurricular Activities"),
  Z = round(c(test2a_1$statistic, test2a_2$statistic, test2a_3$statistic), 3),
  P_Value = ifelse(c(test2a_1$p.value, test2a_2$p.value, test2a_3$p.value) < 0.001, 
                   "<0.001", 
                   round(c(test2a_1$p.value, test2a_2$p.value, test2a_3$p.value), 3)) 
)

# Display the table
kable(table_2a, 
      format = "markdown", 
      caption = "Significance of AUC Difference for College Enrollment Predictors")
```



Table: Significance of AUC Difference for College Enrollment Predictors

|Predictor_1 |Predictor_2                |       Z|P_Value |
|:-----------|:--------------------------|-------:|:-------|
|GPA         |AP                         |  32.679|<0.001  |
|GPA         |Extracurricular Activities |  17.070|<0.001  |
|AP          |Extracurricular Activities | -10.939|<0.001  |

Yay!!! So thankful that these predictors are in the same direction so I didn't have to spend more hours investigating the solution to Delong test warning again. As we can see, all pairs are statistically different at a 0.001 level, demonstrating the strong predictive power of GPA in post-secondary enrollment.

## 4. Postsecondary STEM Degree

This section aims to replicate Figure 3 Panel (B) and Table 2 Part 2.

**STEP 1: Data Preparation**


``` r
post12_stem_df <- els_02_all |>
  # Selecting college enrollment and prectictors 
  select(F3TZSTEM1CRED, BYTXMSTD, F3TZSTEM1TOT, F3TZSTEM2GPA) |>
  # Converting all negative values to NA 
  mutate(across(everything(), ~ ifelse(. < 0, NA, .))) |>
  # Renaming variables 
  rename(post12_stem = F3TZSTEM1CRED,
         math_score = BYTXMSTD,
         num_stem_crs = F3TZSTEM1TOT,
         stem_gpa = F3TZSTEM2GPA) |>
  # Recoding post12_stem to binary 
  mutate(post12_stem = ifelse(post12_stem == 0, 0, 1))
```

**STEP 2: Plotting ROC AUC**


``` r
roc2b_1 <- roc(post12_stem_df$post12_stem, post12_stem_df$num_stem_crs,
               legacy.axes=TRUE, asp=FALSE, 
               main = "ROC Curves for Predicting Postsecondary STEM Degree",
               plot=TRUE, grid=FALSE, lty=1,
               xaxs="i", yaxs="i")
```

```
## Setting levels: control = 0, case = 1
```

```
## Setting direction: controls < cases
```

``` r
roc2b_2 <- roc(post12_stem_df$post12_stem, post12_stem_df$stem_gpa,
               plot=TRUE, add = TRUE, percent=roc2a_1$percent, lty=2)
```

```
## Setting levels: control = 0, case = 1
## Setting direction: controls < cases
```

``` r
roc2b_3 <- roc(post12_stem_df$post12_stem, post12_stem_df$math_score,
               plot=TRUE, add = TRUE, percent=roc2a_1$percent, lty=3)
```

```
## Setting levels: control = 0, case = 1
## Setting direction: controls < cases
```

``` r
# Adding legend to the plot 
legend("bottomright", legend = c("Number of STEM Courses","STEM Course GPA","Math t Score (2002)"), lty = c(1,2,3), lwd = 2)
```

![](ORLA6541_Exercise4_files/figure-html/unnamed-chunk-21-1.png)<!-- -->


``` r
# 
auc_table2b <- data.frame(
  Predictor = c("Number of STEM Courses","STEM Course GPA","Math t Score (2002)"),
  AUC = c(auc(roc2b_1), auc(roc2b_2), auc(roc2b_3))
) |>
  arrange(desc(AUC)) |>
  mutate(AUC = round(AUC, 3))

kable(auc_table2b, 
      format = "markdown",
      caption = "AUC Values of Postsecondary STEM Degree Predictors")
```



Table: AUC Values of Postsecondary STEM Degree Predictors

|Predictor              |   AUC|
|:----------------------|-----:|
|Number of STEM Courses | 0.957|
|Math t Score (2002)    | 0.671|
|STEM Course GPA        | 0.580|

Wow! Number of STEM courses is killing!!! With an AUC of 0.957, it is the highest predictor compared to Math t Score and STEM course GPA. I find the comparison between number of STEM courses and STEM GPA very interesting, as you do not to excel in certain courses to get the degree as long as you pass the course.

However, the output AUC values for Math t Score and STEM Course GPA align with the AUC values in the markdown, but do not align with what is reported in the paper. I wonder if the paper results were obtained using another r script.


``` r
# Number of STEM Course vs. STEM Course GPA
test2b_1 <- roc.test(roc2b_1, roc2b_2, method="delong")
# Number of STEM Course vs. Math t Score (2002)
test2b_2 <- roc.test(roc2b_1, roc2b_3, method="delong")
# STEM Course GPA vs. Math t Score (2002)
test2b_3 <- roc.test(roc2b_2, roc2b_3, method="delong")
```


``` r
# Creating a significance stats table for Postsecondary STEM Degree predictors
table_2b <- data.frame(
  Predictor_1 = c("Number of STEM Courses", "Number of STEM Courses", "STEM Course GPA"),
  Predictor_2 = c("STEM Course GPA", "Math t Score (2002)", "Math t Score (2002)"),
  Z = round(c(test2b_1$statistic, test2b_2$statistic, test2b_3$statistic), 3),
  P_Value = ifelse(c(test2b_1$p.value, test2b_2$p.value, test2b_3$p.value) < 0.001, 
                   "<0.001", 
                   round(c(test2b_1$p.value, test2b_2$p.value, test2b_3$p.value), 3)))

# Display the table
kable(table_2b, 
      format = "markdown", 
      caption = "Significance of AUC Difference for Postsecondary STEM Degree Predictors")
```



Table: Significance of AUC Difference for Postsecondary STEM Degree Predictors

|Predictor_1            |Predictor_2         |      Z|P_Value |
|:----------------------|:-------------------|------:|:-------|
|Number of STEM Courses |STEM Course GPA     | 39.743|<0.001  |
|Number of STEM Courses |Math t Score (2002) | 32.086|<0.001  |
|STEM Course GPA        |Math t Score (2002) | -8.655|<0.001  |

Since AUC values obtained are different from the paper, the z statistics outputted here are also different from the paper. But again, they align with the meta-file.

We can see that all pairs are statistically different, and the pairs with Number of STEM Courses have a HUGE Z statistic.

## 5. STEM Career

This section will replicate Figure 4 and Table 3 in the paper.

**STEP1: Data Preparation**


``` r
stem_career_df <- els_02_all |>
  select(F3STEMOCCCUR, BYTXMSTD, F3TZSTEM1TOT, F3TZSTEM2GPA) |>
  # Converting all negative values to NA 
  mutate(across(everything(), ~ ifelse(. < 0, NA, .))) |>
  # Renaming variables 
  rename(stem_career_all = F3STEMOCCCUR,
         math_score = BYTXMSTD,
         num_stem_crs = F3TZSTEM1TOT,
         stem_gpa = F3TZSTEM2GPA) |>
  # Obtaining hard stem career and soft stem career
  mutate(hard_stem_career = ifelse(is.na(stem_career_all), 
                                   NA, 
                                   ifelse(grepl("^1", stem_career_all), 1, 0)),
         soft_stem_career = ifelse(is.na(stem_career_all), 
                                   NA, 
                                   ifelse(grepl("^(2|4)", stem_career_all), 1, 0))) |>
  # Relocating for convenient validation
  relocate(hard_stem_career, soft_stem_career) |>
  filter()
```

### 5.1 Hard STEM Career

**STEP 2: Plotting ROC AUC**


``` r
roc3a_1 <- roc(stem_career_df$hard_stem_career, stem_career_df$num_stem_crs,
               legacy.axes=TRUE, asp = FALSE, 
               main = "ROC Curves for Predicting Hard STEM Career",
               plot=TRUE, grid=FALSE, lty=1,
               xaxs="i", yaxs="i")
```

```
## Setting levels: control = 0, case = 1
```

```
## Setting direction: controls < cases
```

``` r
roc3a_2 <- roc(stem_career_df$hard_stem_career, stem_career_df$stem_gpa,
               plot=TRUE, add = TRUE, percent=roc2a_1$percent, lty=2)
```

```
## Setting levels: control = 0, case = 1
## Setting direction: controls < cases
```

``` r
roc3a_3 <- roc(stem_career_df$hard_stem_career, stem_career_df$math_score,
               plot=TRUE, add = TRUE, percent=roc2a_1$percent, lty=3)
```

```
## Setting levels: control = 0, case = 1
## Setting direction: controls < cases
```

``` r
# Adding legend to the plot 
legend("bottomright", legend = c("Number of STEM Courses","STEM Course GPA","Math t Score (2002)"), lty = c(1,2,3), lwd = 2)
```

![](ORLA6541_Exercise4_files/figure-html/unnamed-chunk-26-1.png)<!-- -->


``` r
auc_table3a <- data.frame(
  Predictor = c("Number of STEM Courses","STEM Course GPA","Math t Score (2002)"),
  AUC = c(auc(roc3a_1), auc(roc3a_2), auc(roc3a_3))
) |>
  arrange(desc(AUC)) |>
  mutate(AUC = round(AUC, 3))

kable(auc_table3a, 
      format = "markdown", 
      caption = "AUC Values for Hard STEM Career Predictors")
```



Table: AUC Values for Hard STEM Career Predictors

|Predictor              |   AUC|
|:----------------------|-----:|
|Number of STEM Courses | 0.799|
|Math t Score (2002)    | 0.712|
|STEM Course GPA        | 0.593|

**STEP 3: Significance of AUC Difference for Predictors**


``` r
# Number of STEM Course vs. STEM Course GPA
test3a_1 <- roc.test(roc3a_1, roc3a_2, method="delong")
# Number of STEM Course vs. Math t Score (2002)
test3a_2 <- roc.test(roc3a_1, roc3a_3, method="delong")
# STEM Course GPA vs. Math t Score (2002)
test3a_3 <- roc.test(roc3a_2, roc3a_3, method="delong")
```


``` r
# Creating a significance stats table for Postsecondary STEM Degree predictors
table_3a <- data.frame(
  Predictor_1 = c("Number of STEM Courses", "Number of STEM Courses", "STEM Course GPA"),
  Predictor_2 = c("STEM Course GPA", "Math t Score (2002)", "Math t Score (2002)"),
  Z = round(c(test3a_1$statistic, test3a_2$statistic, test3a_3$statistic), 3),
  P_Value = ifelse(c(test3a_1$p.value, test3a_2$p.value, test3a_3$p.value) < 0.001, 
                   "<0.001", 
                   round(c(test3a_1$p.value, test3a_2$p.value, test3a_3$p.value), 3)))

# Display the table
kable(table_3a, 
      format = "markdown", 
      caption = "Significance of AUC Difference for Hard STEM Career Predictors")
```



Table: Significance of AUC Difference for Hard STEM Career Predictors

|Predictor_1            |Predictor_2         |      Z|P_Value |
|:----------------------|:-------------------|------:|:-------|
|Number of STEM Courses |STEM Course GPA     | 15.055|<0.001  |
|Number of STEM Courses |Math t Score (2002) |  8.943|<0.001  |
|STEM Course GPA        |Math t Score (2002) | -7.401|<0.001  |

### 5.2 Soft STEM Career

**STEP 2: Plotting ROC AUC**


``` r
roc3b_1 <- roc(stem_career_df$soft_stem_career, stem_career_df$num_stem_crs,
               legacy.axes=TRUE, asp=FALSE, 
               main = "ROC Curves for Predicting Soft STEM Career",
               plot=TRUE, grid=FALSE, lty=1,
               xaxs="i", yaxs="i")
```

```
## Setting levels: control = 0, case = 1
```

```
## Setting direction: controls < cases
```

``` r
roc3b_2 <- roc(stem_career_df$soft_stem_career, stem_career_df$stem_gpa,
               plot=TRUE, add = TRUE, percent=roc2a_1$percent, lty=2)
```

```
## Setting levels: control = 0, case = 1
## Setting direction: controls < cases
```

``` r
roc3b_3 <- roc(stem_career_df$soft_stem_career, stem_career_df$math_score,
               plot=TRUE, add = TRUE, percent=roc2a_1$percent, lty=3)
```

```
## Setting levels: control = 0, case = 1
## Setting direction: controls < cases
```

``` r
# Adding legend to the plot 
legend("bottomright", legend = c("Number of STEM Courses","STEM Course GPA","Math t Score (2002)"), lty = c(1,2,3), lwd = 2)
```

![](ORLA6541_Exercise4_files/figure-html/unnamed-chunk-30-1.png)<!-- -->


``` r
auc_table3b <- data.frame(
  Predictor = c("Number of STEM Courses","STEM Course GPA","Math t Score (2002)"),
  AUC = c(auc(roc3b_1), auc(roc3b_2), auc(roc3b_3))
) |>
  arrange(desc(AUC)) |>
  mutate(AUC = round(AUC, 3))

kable(auc_table3b, 
      format = "markdown", 
      caption = "AUC Values for Soft STEM Career Predictors")
```



Table: AUC Values for Soft STEM Career Predictors

|Predictor              |   AUC|
|:----------------------|-----:|
|Number of STEM Courses | 0.693|
|STEM Course GPA        | 0.673|
|Math t Score (2002)    | 0.611|

**STEP 3: Significance of AUC Difference for Predictors**


``` r
# Number of STEM Course vs. STEM Course GPA
test3b_1 <- roc.test(roc3b_1, roc3b_2, method="delong")
# Number of STEM Course vs. Math t Score (2002)
test3b_2 <- roc.test(roc3b_1, roc3b_3, method="delong")
# STEM Course GPA vs. Math t Score (2002)
test3b_3 <- roc.test(roc3b_2, roc3b_3, method="delong")
```


``` r
# Creating a significance stats table for Postsecondary STEM Degree predictors
table_3b <- data.frame(
  Predictor_1 = c("Number of STEM Courses", "Number of STEM Courses", "STEM Course GPA"),
  Predictor_2 = c("STEM Course GPA", "Math t Score (2002)", "Math t Score (2002)"),
  Z = round(c(test3b_1$statistic, test3b_2$statistic, test3b_3$statistic), 3),
  P_Value = ifelse(c(test3b_1$p.value, test3b_2$p.value, test3b_3$p.value) < 0.001, 
                   "<0.001", 
                   round(c(test3b_1$p.value, test3b_2$p.value, test3b_3$p.value), 3)))

# Display the table
kable(table_3b, 
      format = "markdown",
      caption = "Significance of AUC Difference for Soft STEM Career Predictors")
```



Table: Significance of AUC Difference for Soft STEM Career Predictors

|Predictor_1            |Predictor_2         |      Z|P_Value |
|:----------------------|:-------------------|------:|:-------|
|Number of STEM Courses |STEM Course GPA     |  0.846|0.397   |
|Number of STEM Courses |Math t Score (2002) | 10.523|<0.001  |
|STEM Course GPA        |Math t Score (2002) | 11.111|<0.001  |

I am so happy this time no issues encountered at all. From the AUC tables, we can see that Number of STEM courses outperforms the other two predictors again for predicting both hard STEM career and soft STEM career. However, taking closer look at the delong test results, it is interesting to see that the AUC of Number of STEM Courses and STEM Course GPA differ significantly when predicting hard STEM career but not soft STEM career. This made me think about quantity (breath) vs. quality (depth) in STEM education, and maybe for Hard STEM courses, quantity matters more than quality???

# Bulut & Desjardins (Chapter 7)

## 7.2 Decision trees in R


``` r
# Loading Dataset
pisa <- fread("../pisa2015.csv")
```


``` r
# Creating new variables 
pisa[, `:=`(
  science = rowMeans(.SD[, paste0("PV", 1:10, "SCIE"), with = FALSE], na.rm = TRUE),
  reading = rowMeans(.SD[, paste0("PV", 1:10, "READ"), with = FALSE], na.rm = TRUE),
  math = rowMeans(.SD[, paste0("PV", 1:10, "MATH"), with = FALSE], na.rm = TRUE)
)]

pisa[, science_perf := as.factor(ifelse(science >= 493, "High", "Low"))]

# Subsetting students from the United States and Canada and some variables 
pisa_small <- pisa[
  CNT %in% c("Canada", "United States"),  # Filter rows
  .(science_perf, reading, math, 
    family_wealth = WEALTH, 
    home_ed_resources = HEDRES,
    env_awareness = ENVAWARE,
    ict_resources = ICTRES,
    epist_beliefs = EPIST,
    home_possessions = HOMEPOS,
    eco_soc_cul_status = ESCS)
  ]
```


``` r
# Set the seed before splitting the data
set.seed(123)

# Removing missing values 
pisa_nm <- na.omit(pisa_small)

# Split the data into training and test
index <- createDataPartition(pisa_nm$science_perf, p = 0.7, list = FALSE)
train_data <- pisa_nm[index, ]
test_data  <- pisa_nm[-index, ]

# Checking sample sizes 
nrow(train_data)
```

```
## [1] 16561
```

``` r
nrow(test_data)
```

```
## [1] 7097
```

**Classification Trees**


``` r
# Creating first decision tree 
dt_fit1 <- rpart(formula = science_perf ~ .,
                 data = train_data,
                 method = "class", 
                 control = rpart.control(minsplit = 20, 
                                         cp = 0, 
                                         xval = 0),
                parms = list(split = "gini"))

rpart.plot(dt_fit1)
```

```
## Warning: labs do not fit even at cex 0.15, there may be some overplotting
```

![](ORLA6541_Exercise4_files/figure-html/unnamed-chunk-37-1.png)<!-- -->

The plot above is very hard to read and interpret since it makes too specific classification in order to fit our data perfectly, which is overfitting. Therefore we need to prune it by increasing the value of complexity parameter.


``` r
# Prunning using cp = 0.005 
dt_fit2.1 <- rpart(formula = science_perf ~ .,
                   data = train_data,
                   method = "class",
                   control = rpart.control(minsplit = 20,
                                           cp = 0.005,
                                           xval = 0),
                   parms = list(split = "gini"))

rpart.plot(dt_fit2.1)
```

![](ORLA6541_Exercise4_files/figure-html/unnamed-chunk-38-1.png)<!-- -->

Now the plot becomes clear at each branch level, and we can clearly see that `reading` and `math` are the only two variables that are involved in the classifications. Students that achieve highest in science performance if their math score is at least 478 and reading score is at least 471 if not 495.


``` r
# Trying entropy 
dt_fit2.2 <- rpart(formula = science_perf ~ .,
                   data = train_data,
                   method = "class",
                   control = rpart.control(minsplit = 20,
                                           cp = 0.005,
                                           xval = 0),
                   parms = list(split = "information"))

rpart.plot(dt_fit2.2)
```

![](ORLA6541_Exercise4_files/figure-html/unnamed-chunk-39-1.png)<!-- -->

With `entropy` split, we can see that the overall tree structure does not differ a lot from the tree with `gini`, but the cutoff points changed.


``` r
# Plotting with RdBu palette, extra, and gray shadow
rpart.plot(dt_fit2.2, extra = 8, box.palette = "RdBu", shadow.col = "gray")
```

![](ORLA6541_Exercise4_files/figure-html/unnamed-chunk-40-1.png)<!-- -->

There are options to change the color palette using `box.palette()` and `shadow.col()`. We can also choose what statistics to display in the boxes by using `extra`. 104 for classification tree in my perspective is th best `extra` value since it shows probability to both directions and also the percentage of observations that are classified to certain category.


``` r
# An alternative way to prune the tree
dt_fit_prune <- prune(dt_fit1, cp = 0.005)
rpart.plot(dt_fit_prune, extra = 104, box.palette = "RdBu", shadow.col = "gray")
```

![](ORLA6541_Exercise4_files/figure-html/unnamed-chunk-41-1.png)<!-- -->


``` r
# Printing out some statistics 
printcp(dt_fit2.2)
```

```
## 
## Classification tree:
## rpart(formula = science_perf ~ ., data = train_data, method = "class", 
##     parms = list(split = "information"), control = rpart.control(minsplit = 20, 
##         cp = 0.005, xval = 0))
## 
## Variables actually used in tree construction:
## [1] math    reading
## 
## Root node error: 6461/16561 = 0.39013
## 
## n= 16561 
## 
##         CP nsplit rel error
## 1 0.739050      0   1.00000
## 2 0.017103      1   0.26095
## 3 0.012150      3   0.22675
## 4 0.005000      5   0.20245
```

``` r
summary(dt_fit2.2)
```

```
## Call:
## rpart(formula = science_perf ~ ., data = train_data, method = "class", 
##     parms = list(split = "information"), control = rpart.control(minsplit = 20, 
##         cp = 0.005, xval = 0))
##   n= 16561 
## 
##           CP nsplit rel error
## 1 0.73904968      0 1.0000000
## 2 0.01710262      1 0.2609503
## 3 0.01214982      3 0.2267451
## 4 0.00500000      5 0.2024454
## 
## Variable importance
##               math            reading      env_awareness eco_soc_cul_status 
##                 43                 40                  5                  5 
##      epist_beliefs   home_possessions 
##                  4                  4 
## 
## Node number 1: 16561 observations,    complexity param=0.7390497
##   predicted class=High  expected loss=0.3901334  P(node) =1
##     class counts: 10100  6461
##    probabilities: 0.610 0.390 
##   left son=2 (9936 obs) right son=3 (6625 obs)
##   Primary splits:
##       math               < 481.263  to the right, improve=5711.4740, (0 missing)
##       reading            < 501.1274 to the right, improve=5594.7600, (0 missing)
##       epist_beliefs      < 0.198    to the right, improve= 795.4807, (0 missing)
##       env_awareness      < -0.70615 to the right, improve= 522.1373, (0 missing)
##       eco_soc_cul_status < 0.68805  to the right, improve= 448.1033, (0 missing)
##   Surrogate splits:
##       reading            < 499.5629 to the right, agree=0.852, adj=0.631, (0 split)
##       env_awareness      < -0.70615 to the right, agree=0.650, adj=0.124, (0 split)
##       eco_soc_cul_status < -0.1143  to the right, agree=0.648, adj=0.120, (0 split)
##       epist_beliefs      < -0.177   to the right, agree=0.641, adj=0.102, (0 split)
##       home_possessions   < -0.5198  to the right, agree=0.637, adj=0.092, (0 split)
## 
## Node number 2: 9936 observations,    complexity param=0.01214982
##   predicted class=High  expected loss=0.07659018  P(node) =0.5999638
##     class counts:  9175   761
##    probabilities: 0.923 0.077 
##   left son=4 (7839 obs) right son=5 (2097 obs)
##   Primary splits:
##       reading            < 518.2321 to the right, improve=906.52680, (0 missing)
##       math               < 522.0251 to the right, improve=635.50910, (0 missing)
##       epist_beliefs      < 0.1958   to the right, improve= 87.79355, (0 missing)
##       env_awareness      < -0.65545 to the right, improve= 55.10106, (0 missing)
##       eco_soc_cul_status < 0.5433   to the right, improve= 19.40137, (0 missing)
##   Surrogate splits:
##       math             < 498.7565 to the right, agree=0.811, adj=0.104, (0 split)
##       home_possessions < -3.27025 to the right, agree=0.789, adj=0.001, (0 split)
##       family_wealth    < -3.10015 to the right, agree=0.789, adj=0.000, (0 split)
##       env_awareness    < 3.2845   to the left,  agree=0.789, adj=0.000, (0 split)
## 
## Node number 3: 6625 observations,    complexity param=0.01710262
##   predicted class=Low   expected loss=0.1396226  P(node) =0.4000362
##     class counts:   925  5700
##    probabilities: 0.140 0.860 
##   left son=6 (2121 obs) right son=7 (4504 obs)
##   Primary splits:
##       reading          < 475.4796 to the right, improve=764.75740, (0 missing)
##       math             < 443.9397 to the right, improve=592.71010, (0 missing)
##       env_awareness    < -0.2328  to the right, improve= 76.41640, (0 missing)
##       epist_beliefs    < 0.2322   to the right, improve= 50.29704, (0 missing)
##       home_possessions < -0.57535 to the right, improve= 15.12494, (0 missing)
##   Surrogate splits:
##       math < 451.3456 to the right, agree=0.761, adj=0.253, (0 split)
## 
## Node number 4: 7839 observations
##   predicted class=High  expected loss=0.01058809  P(node) =0.473341
##     class counts:  7756    83
##    probabilities: 0.989 0.011 
## 
## Node number 5: 2097 observations,    complexity param=0.01214982
##   predicted class=High  expected loss=0.323319  P(node) =0.1266228
##     class counts:  1419   678
##    probabilities: 0.677 0.323 
##   left son=10 (1612 obs) right son=11 (485 obs)
##   Primary splits:
##       reading          < 471.6592 to the right, improve=157.084400, (0 missing)
##       math             < 522.0719 to the right, improve=111.314500, (0 missing)
##       env_awareness    < -0.70475 to the right, improve= 16.041640, (0 missing)
##       epist_beliefs    < 0.15145  to the right, improve= 11.283280, (0 missing)
##       home_possessions < -0.03605 to the right, improve=  2.188593, (0 missing)
##   Surrogate splits:
##       home_ed_resources < -3.7279  to the right, agree=0.770, adj=0.004, (0 split)
##       home_possessions  < -3.1059  to the right, agree=0.769, adj=0.002, (0 split)
## 
## Node number 6: 2121 observations,    complexity param=0.01710262
##   predicted class=Low   expected loss=0.3866101  P(node) =0.128072
##     class counts:   820  1301
##    probabilities: 0.387 0.613 
##   left son=12 (689 obs) right son=13 (1432 obs)
##   Primary splits:
##       reading           < 516.8486 to the right, improve=160.783100, (0 missing)
##       math              < 457.8792 to the right, improve=137.607700, (0 missing)
##       env_awareness     < -0.4352  to the right, improve= 28.677020, (0 missing)
##       epist_beliefs     < 0.3483   to the right, improve=  6.573638, (0 missing)
##       home_ed_resources < -0.7019  to the left,  improve=  4.999447, (0 missing)
##   Surrogate splits:
##       family_wealth     < 3.8374   to the right, agree=0.676, adj=0.003, (0 split)
##       home_ed_resources < -3.7279  to the left,  agree=0.676, adj=0.003, (0 split)
##       home_possessions  < 3.08425  to the right, agree=0.676, adj=0.001, (0 split)
## 
## Node number 7: 4504 observations
##   predicted class=Low   expected loss=0.02331261  P(node) =0.2719643
##     class counts:   105  4399
##    probabilities: 0.023 0.977 
## 
## Node number 10: 1612 observations
##   predicted class=High  expected loss=0.221464  P(node) =0.09733712
##     class counts:  1255   357
##    probabilities: 0.779 0.221 
## 
## Node number 11: 485 observations
##   predicted class=Low   expected loss=0.3381443  P(node) =0.02928567
##     class counts:   164   321
##    probabilities: 0.338 0.662 
## 
## Node number 12: 689 observations
##   predicted class=High  expected loss=0.3396226  P(node) =0.04160377
##     class counts:   455   234
##    probabilities: 0.660 0.340 
## 
## Node number 13: 1432 observations
##   predicted class=Low   expected loss=0.2548883  P(node) =0.08646821
##     class counts:   365  1067
##    probabilities: 0.255 0.745
```

``` r
varImp(dt_fit2.2)
```

```
##                        Overall
## eco_soc_cul_status  467.504697
## env_awareness       698.373384
## epist_beliefs       951.428235
## home_ed_resources     4.999447
## home_possessions     17.313535
## math               7188.614877
## reading            7583.911786
## family_wealth         0.000000
## ict_resources         0.000000
```


``` r
# Printing out the decision rules from the trees 
rpart.rules(dt_fit2.2, cover = TRUE)
```

```
##  science_perf                                            cover
##          0.01 when math >= 481 & reading >=        518     47%
##          0.22 when math >= 481 & reading is 472 to 518     10%
##          0.34 when math <  481 & reading >=        517      4%
##          0.66 when math >= 481 & reading <  472             3%
##          0.75 when math <  481 & reading is 475 to 517      9%
##          0.98 when math <  481 & reading <  475            27%
```

We can print out some descriptive statistics to 1) see what variables play important roles in the decision tree; 2) what are the rules applied to our decision tree. We can see that, aligned from our observation from the plot, reading and math are the most important varianles.


``` r
# Checking classification accuracy (note: this is not the ideal accuracy as area under curve)
dt_pred_pct <- predict(dt_fit2.2, test_data) |>
  as.data.frame()

head(dt_pred_pct)
```

```
##         High        Low
## 1 0.77853598 0.22146402
## 2 0.98941191 0.01058809
## 3 0.98941191 0.01058809
## 4 0.02331261 0.97668739
## 5 0.02331261 0.97668739
## 6 0.98941191 0.01058809
```

``` r
# Turning predicted percentages into classes 
dt_pred_class <- dt_pred_pct |>
  mutate(science_perf = as.factor(ifelse(High >= 0.5, "High", "Low"))) |>
  select(science_perf)

head(dt_pred_class)
```

```
##   science_perf
## 1         High
## 2         High
## 3         High
## 4          Low
## 5          Low
## 6         High
```

``` r
# Getting confusion matrix (note: confusionMatrix() comes from caret) 
confusionMatrix(dt_pred_class$science_perf, test_data$science_perf)
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction High  Low
##       High 4072  294
##       Low   256 2475
##                                          
##                Accuracy : 0.9225         
##                  95% CI : (0.916, 0.9286)
##     No Information Rate : 0.6098         
##     P-Value [Acc > NIR] : <2e-16         
##                                          
##                   Kappa : 0.8367         
##                                          
##  Mcnemar's Test P-Value : 0.1146         
##                                          
##             Sensitivity : 0.9409         
##             Specificity : 0.8938         
##          Pos Pred Value : 0.9327         
##          Neg Pred Value : 0.9063         
##              Prevalence : 0.6098         
##          Detection Rate : 0.5738         
##    Detection Prevalence : 0.6152         
##       Balanced Accuracy : 0.9173         
##                                          
##        'Positive' Class : High           
## 
```

We need to remember that the accuracy we see in the confusion matrix has nothing to do with the AUC value. The former is the overall accuracy that is the correctly predicted observations over all observations, and AUC is obtained using integral, and the ROC curve is true positive rate vs. false positive rate.


``` r
# A decision tree based on other less important variables
dt_fit3.1 <- rpart(formula = science_perf ~ . - math -reading,
                   data = train_data,
                   method = "class",
                   control = rpart.control(minsplit = 20,
                                           cp = 0.001,
                                           xval = 0),
                   parms = list(split = "gini"))

rpart.plot(dt_fit3.1, extra = 104, box.palette = "RdBu", shadow.col = "gray")
```

![](ORLA6541_Exercise4_files/figure-html/unnamed-chunk-45-1.png)<!-- -->

``` r
# A pruned version
dt_fit3.2 <- rpart(formula = science_perf ~ . - math -reading,
                   data = train_data,
                   method = "class",
                   control = rpart.control(minsplit = 20,
                                           cp = 0.005,
                                           xval = 0),
                   parms = list(split = "gini"))

rpart.plot(dt_fit3.2, extra = 104, box.palette = "RdBu", shadow.col = "gray")
```

![](ORLA6541_Exercise4_files/figure-html/unnamed-chunk-45-2.png)<!-- -->

The above plot shows the decisions made based on variables excluding math and reading since we want to see if not considering math and reading scores, what would the decision tree look like. We can see that the key variables in play are `epist_beliefs`, `env_awareness`, `eco_soc_cul_status`, and `home_possessions`. On the left side, the tree is less complex.


``` r
# Defining a function to experiment different values of cp 
decision_check <- function(cp){
  require("rpart")
  require("dplyr")
  
  dt <- rpart(formula = science_perf ~ . -math-reading,
              data = train_data,
              method = "class",
              control = rpart.control(minsplit = 20,
                                      cp = cp,
                                      xval = 0),
              parms = list(split = "gini"))
  
  dt_pred <- predict(dt, test_data) |>
    as.data.frame() |>
    mutate(science_perf = as.factor(ifelse(High > 0.5, "High", "Low"))) |>
    select(science_perf)
  
  cm <- confusionMatrix(dt_pred$science_perf, test_data$science_perf)
  
  results <- data.frame(cp = cp,
                        Accuracy = round(cm$overall[1], 3),
                        Sensitivity = round(cm$byClass[1], 3),
                        Specificity = round(cm$byClass[2], 3))
  
  return(results)
}

result <- NULL
for(i in seq(from = 0.001, to = 0.08, by = 0.005)){
  result <- rbind(result, decision_check(cp = i))
}

result <- result[order(result$Accuracy, result$Sensitivity, result$Specificity),]
result
```

```
##               cp Accuracy Sensitivity Specificity
## Accuracy10 0.051    0.670       0.943       0.245
## Accuracy11 0.056    0.670       0.943       0.245
## Accuracy12 0.061    0.670       0.943       0.245
## Accuracy13 0.066    0.670       0.943       0.245
## Accuracy14 0.071    0.670       0.943       0.245
## Accuracy15 0.076    0.670       0.943       0.245
## Accuracy2  0.011    0.685       0.773       0.548
## Accuracy3  0.016    0.685       0.773       0.548
## Accuracy4  0.021    0.685       0.773       0.548
## Accuracy5  0.026    0.685       0.773       0.548
## Accuracy6  0.031    0.685       0.773       0.548
## Accuracy7  0.036    0.685       0.773       0.548
## Accuracy8  0.041    0.685       0.773       0.548
## Accuracy9  0.046    0.685       0.773       0.548
## Accuracy1  0.006    0.695       0.848       0.456
## Accuracy   0.001    0.698       0.836       0.481
```

One of the key parameters for decision trees is `cp`, the complexity parameter since it decides how complex and how accurate the tree is. In other words, we need to find the best `cp` value that balances between complexity and accuracy to avoid overfitting. We can definately define a function to try different parameter values, but it would be more efficient to do it using cross validation which I will explore later.


``` r
# Transforming to long format
result_long <- pivot_longer(result,
                            col = c("Accuracy", "Sensitivity", "Specificity"),
                            names_to = "Index",
                            values_to = "Value")

# Visualize the results
ggplot(result_long, aes(x = cp, y = Value)) +
  geom_point(aes(color = Index), size = 3) +
  labs(x = "Complexity Parameter", y = "Value") +
  theme_bw()
```

![](ORLA6541_Exercise4_files/figure-html/unnamed-chunk-47-1.png)<!-- -->

Personally, I don't find this visualization helpful to digest information about the best `cp` value, compared to the cp plot we will make in cross validation section. But this is very helpful to illustrate the trade-off between sensitivity and specificity, as we can see one goes up as the other goes down.

### 7.2.1 Cross Validation


``` r
# Cross validation by setting xval = 10 
dt_fit4.1 <- rpart(formula = science_perf ~ .-reading -math,
                   data = train_data,
                   method = "class",
                   control = rpart.control(minsplit = 20,
                                           cp = 0,
                                           xval = 10),
                   parms = list(split = "gini"))

printcp(dt_fit4.1)
```

```
## 
## Classification tree:
## rpart(formula = science_perf ~ . - reading - math, data = train_data, 
##     method = "class", parms = list(split = "gini"), control = rpart.control(minsplit = 20, 
##         cp = 0, xval = 10))
## 
## Variables actually used in tree construction:
## [1] eco_soc_cul_status env_awareness      epist_beliefs      family_wealth     
## [5] home_ed_resources  home_possessions   ict_resources     
## 
## Root node error: 6461/16561 = 0.39013
## 
## n= 16561 
## 
##            CP nsplit rel error  xerror      xstd
## 1  7.9709e-02      0   1.00000 1.00000 0.0097156
## 2  4.8754e-02      2   0.84058 0.84213 0.0093551
## 3  6.1136e-03      3   0.79183 0.79616 0.0092169
## 4  5.4171e-03      6   0.77093 0.79879 0.0092252
## 5  3.8694e-03      8   0.76010 0.77790 0.0091575
## 6  3.7146e-03      9   0.75623 0.77620 0.0091518
## 7  3.2503e-03     11   0.74880 0.77155 0.0091363
## 8  2.1668e-03     12   0.74555 0.76830 0.0091253
## 9  2.0121e-03     13   0.74338 0.76459 0.0091126
## 10 1.4704e-03     15   0.73936 0.76412 0.0091110
## 11 1.3156e-03     20   0.73178 0.76428 0.0091115
## 12 1.2382e-03     25   0.72481 0.76443 0.0091121
## 13 1.0834e-03     26   0.72357 0.76412 0.0091110
## 14 1.0318e-03     31   0.71723 0.76675 0.0091200
## 15 1.0060e-03     34   0.71413 0.76923 0.0091284
## 16 9.2865e-04     42   0.70515 0.77000 0.0091310
## 17 7.7387e-04     54   0.69386 0.77496 0.0091477
## 18 7.3518e-04     63   0.68643 0.78053 0.0091662
## 19 6.9649e-04     78   0.67296 0.78007 0.0091647
## 20 6.7069e-04     84   0.66878 0.78022 0.0091652
## 21 6.5779e-04     93   0.66259 0.78022 0.0091652
## 22 6.1910e-04    101   0.65733 0.78099 0.0091677
## 23 5.7689e-04    116   0.64773 0.78362 0.0091764
## 24 5.6751e-04    133   0.63736 0.78564 0.0091830
## 25 5.4171e-04    136   0.63566 0.78626 0.0091850
## 26 5.3066e-04    138   0.63458 0.78672 0.0091865
## 27 5.1592e-04    155   0.62405 0.78672 0.0091865
## 28 4.9528e-04    161   0.62096 0.79229 0.0092045
## 29 4.6432e-04    166   0.61848 0.79307 0.0092070
## 30 4.2563e-04    200   0.60115 0.79647 0.0092179
## 31 4.1273e-04    205   0.59882 0.79740 0.0092208
## 32 3.8694e-04    208   0.59759 0.80003 0.0092291
## 33 3.7146e-04    228   0.58938 0.80003 0.0092291
## 34 3.6114e-04    239   0.58427 0.80080 0.0092316
## 35 3.4824e-04    271   0.57112 0.80204 0.0092354
## 36 3.0955e-04    281   0.56725 0.81071 0.0092623
## 37 2.7086e-04    365   0.53769 0.81458 0.0092742
## 38 2.5796e-04    383   0.53196 0.82185 0.0092961
## 39 2.4764e-04    417   0.52159 0.83300 0.0093289
## 40 2.3216e-04    422   0.52035 0.83346 0.0093302
## 41 2.0637e-04    459   0.50952 0.83919 0.0093467
## 42 1.9347e-04    496   0.49992 0.84213 0.0093551
## 43 1.9049e-04    504   0.49837 0.84290 0.0093573
## 44 1.8057e-04    517   0.49590 0.84290 0.0093573
## 45 1.7689e-04    526   0.49373 0.84290 0.0093573
## 46 1.5477e-04    540   0.49126 0.86210 0.0094103
## 47 1.2382e-04    640   0.47485 0.86612 0.0094211
## 48 1.1608e-04    652   0.47284 0.86983 0.0094309
## 49 1.0318e-04    664   0.47144 0.87463 0.0094435
## 50 9.2865e-05    673   0.47052 0.87541 0.0094455
## 51 8.3340e-05    680   0.46974 0.87541 0.0094455
## 52 7.7387e-05    693   0.46866 0.88562 0.0094716
## 53 6.6332e-05    737   0.46510 0.88624 0.0094732
## 54 6.1910e-05    744   0.46463 0.88717 0.0094755
## 55 5.1592e-05    754   0.46401 0.89274 0.0094894
## 56 3.8694e-05    766   0.46340 0.89274 0.0094894
## 57 2.5796e-05    770   0.46324 0.89537 0.0094959
## 58 0.0000e+00    776   0.46309 0.89584 0.0094971
```

``` r
plotcp(dt_fit4.1)
```

![](ORLA6541_Exercise4_files/figure-html/unnamed-chunk-48-1.png)<!-- -->

``` r
# Here, instead of using the cp value used by Bulut and Desjardins, I followed Seftor's piece which recommends the first model that falls under the one-SE line, i.e., the 6th model
dt_fit4.2 <- rpart(formula = science_perf ~ .-reading -math,
                   data = train_data,
                   method = "class",
                   control = rpart.control(minsplit = 20,
                                           cp = 3.7146e-03, 
                                           xval = 0),
                   parms = list(split = "gini"))

printcp(dt_fit4.2)
```

```
## 
## Classification tree:
## rpart(formula = science_perf ~ . - reading - math, data = train_data, 
##     method = "class", parms = list(split = "gini"), control = rpart.control(minsplit = 20, 
##         cp = 0.0037146, xval = 0))
## 
## Variables actually used in tree construction:
## [1] eco_soc_cul_status env_awareness      epist_beliefs      family_wealth     
## [5] home_possessions  
## 
## Root node error: 6461/16561 = 0.39013
## 
## n= 16561 
## 
##          CP nsplit rel error
## 1 0.0797090      0   1.00000
## 2 0.0487541      2   0.84058
## 3 0.0061136      3   0.79183
## 4 0.0054171      6   0.77093
## 5 0.0038694      8   0.76010
## 6 0.0037146      9   0.75623
```

``` r
rpart.plot(dt_fit4.2, extra = 104, box.palette = "RdBu", shadow.col = "gray")
```

![](ORLA6541_Exercise4_files/figure-html/unnamed-chunk-48-2.png)<!-- -->

In Bulut and Desjardins (2021), they picked a `cp` value of 0.0039 based on the cross validation results. But I followed the one-SE line recommendation to pick the first model whose relative error falls under the one-SE line, which is supposed to be the most simple model with the highest accuracy.

Compared to the previous plot we made, this decision tree has one more variable in play -- `family_wealth`. Still, the left side of the tree is less complex.

**Regression Tree**


``` r
train_data
```

```
##        science_perf  reading     math family_wealth home_ed_resources
##              <fctr>    <num>    <num>         <num>             <num>
##     1:         High 624.2843 573.1664       -0.3839            0.0321
##     2:         High 607.0757 594.5418        0.8915            1.1563
##     3:          Low 418.4516 380.7532        1.5253           -0.5343
##     4:          Low 449.1595 451.7884        0.3377           -1.0509
##     5:          Low 482.1293 427.3010        1.1332           -0.5343
##    ---                                                               
## 16557:          Low 385.1880 366.9398        0.2219            1.1563
## 16558:          Low 440.8033 429.3842        1.4456           -0.7218
## 16559:          Low 462.2532 495.2366        0.7810            1.1563
## 16560:          Low 449.5735 422.0503        0.2360           -1.3957
## 16561:         High 528.0603 588.7859        0.8339           -0.5343
##        env_awareness ict_resources epist_beliefs home_possessions
##                <num>         <num>         <num>            <num>
##     1:       -0.7058       -0.3173        1.5636           0.1645
##     2:       -0.0280        0.6231        0.5524           1.5253
##     3:       -1.8311        0.7936       -0.1933           0.3171
##     4:       -0.9658       -0.7882       -0.1933          -0.5511
##     5:        2.0258        1.4083       -0.1933           0.7776
##    ---                                                           
## 16557:        0.6187        0.2991       -0.1933           0.4178
## 16558:        1.4721        1.6987        2.1552           0.8803
## 16559:        0.0194        1.4083       -2.7904           0.8809
## 16560:       -0.7541        0.1321       -0.7885          -0.4982
## 16561:        0.3330        0.8250        1.1342           1.1267
##        eco_soc_cul_status
##                     <num>
##     1:             0.3087
##     2:             1.3685
##     3:             0.2881
##     4:             0.1137
##     5:             0.7838
##    ---                   
## 16557:            -0.1233
## 16558:             0.8443
## 16559:             0.9891
## 16560:             0.2137
## 16561:             1.1208
```

``` r
# Starting with cross validation
rt_fit1 <- rpart(formula = math ~ . -reading -science_perf,
                 data = train_data,
                 method = "anova",
                 control = rpart.control(minsplit = 20,
                                         cp = 0.001,
                                         xval = 10),
                 parms = list(split = "gini"))

printcp(rt_fit1)
```

```
## 
## Regression tree:
## rpart(formula = math ~ . - reading - science_perf, data = train_data, 
##     method = "anova", parms = list(split = "gini"), control = rpart.control(minsplit = 20, 
##         cp = 0.001, xval = 10))
## 
## Variables actually used in tree construction:
## [1] eco_soc_cul_status env_awareness      epist_beliefs      family_wealth     
## [5] home_ed_resources 
## 
## Root node error: 104245883/16561 = 6294.7
## 
## n= 16561 
## 
##           CP nsplit rel error  xerror      xstd
## 1  0.1161699      0   1.00000 1.00021 0.0101171
## 2  0.0398464      1   0.88383 0.88473 0.0092134
## 3  0.0350759      2   0.84398 0.85258 0.0089318
## 4  0.0174752      3   0.80891 0.81210 0.0086026
## 5  0.0073988      4   0.79143 0.79504 0.0084453
## 6  0.0070254      5   0.78403 0.78811 0.0083867
## 7  0.0050259      6   0.77701 0.78381 0.0083400
## 8  0.0049643      7   0.77198 0.78088 0.0083228
## 9  0.0038231      8   0.76702 0.77840 0.0082834
## 10 0.0035472      9   0.76320 0.77783 0.0082840
## 11 0.0030106     10   0.75965 0.77428 0.0082643
## 12 0.0024601     11   0.75664 0.77103 0.0082357
## 13 0.0024180     12   0.75418 0.76728 0.0082058
## 14 0.0023678     13   0.75176 0.76628 0.0081952
## 15 0.0019522     14   0.74939 0.76342 0.0081713
## 16 0.0018450     15   0.74744 0.76341 0.0081782
## 17 0.0016869     16   0.74559 0.76258 0.0081656
## 18 0.0016057     17   0.74391 0.76138 0.0081588
## 19 0.0015595     18   0.74230 0.76050 0.0081472
## 20 0.0015452     19   0.74074 0.76053 0.0081464
## 21 0.0014152     21   0.73765 0.76008 0.0081733
## 22 0.0014084     22   0.73624 0.75855 0.0081631
## 23 0.0013381     23   0.73483 0.75824 0.0081622
## 24 0.0012680     24   0.73349 0.75763 0.0081533
## 25 0.0011094     25   0.73222 0.75726 0.0081692
## 26 0.0010587     26   0.73111 0.75650 0.0081595
## 27 0.0010260     28   0.72900 0.75542 0.0081507
## 28 0.0010170     29   0.72797 0.75509 0.0081489
## 29 0.0010149     30   0.72695 0.75496 0.0081480
## 30 0.0010000     31   0.72594 0.75477 0.0081426
```

``` r
# Chance the cp value to that of the 6th model
rt_fit2 <- rpart(formula = math ~ . - reading -science_perf,
                 data = train_data,
                 method = "anova",
                 control = rpart.control(minsplit = 20,
                                         cp = 0.007,
                                         xval = 0),
                 parms = list(split = "gini"))

rpart.plot(rt_fit2, extra = 100, box.palette = "RdBu", shadow.col = "gray")
```

![](ORLA6541_Exercise4_files/figure-html/unnamed-chunk-49-1.png)<!-- -->

``` r
# Mean absolute error of training data 
mae(model = rt_fit2, data = train_data)
```

```
## [1] 56.14761
```

``` r
# Root mean square error of training data 
rmse (model = rt_fit2, data = train_data)
```

```
## [1] 69.93572
```

``` r
# Mean absolute error of training data 
mae(model = rt_fit2, data = test_data)
```

```
## [1] 57.44555
```

``` r
# Root mean square error of training data 
rmse (model = rt_fit2, data = test_data)
```

```
## [1] 71.26332
```

Regression tree does not have the option for `extra = 104`, since there is no grouping and the outcome is the exact scores. As the tree suggests, math score also depends on variables like `epist_beliefs`, `eco_soc_cul_status`, and `env_awareness`, but this time the left side of the tree seems to be more complex. I think for regression tree, this color palette could be misleading since I tended to think hotter value being larger number (math score in this case), but looking at the value, I realized the bluer ones are actually higher values. Maybe we can try `BuRd` instead of `RdBu`.

## 7.4 Random Forests in R


``` r
# Initial setup of random forest 
rf_fit1 <- randomForest(formula = science_perf ~ .,
                        data = train_data,
                        importance = TRUE,
                        ntree = 1000)

print(rf_fit1)
```

```
## 
## Call:
##  randomForest(formula = science_perf ~ ., data = train_data, importance = TRUE,      ntree = 1000) 
##                Type of random forest: classification
##                      Number of trees: 1000
## No. of variables tried at each split: 3
## 
##         OOB estimate of  error rate: 7.65%
## Confusion matrix:
##      High  Low class.error
## High 9473  627  0.06207921
## Low   640 5821  0.09905587
```

``` r
plot(rf_fit1)
```

![](ORLA6541_Exercise4_files/figure-html/unnamed-chunk-50-1.png)<!-- -->


``` r
# Cutting tree number to 50 
rf_fit2 <- randomForest(formula = science_perf ~ .,
                        data = train_data,
                        importance = TRUE,
                        ntree = 50)

print(rf_fit2)
```

```
## 
## Call:
##  randomForest(formula = science_perf ~ ., data = train_data, importance = TRUE,      ntree = 50) 
##                Type of random forest: classification
##                      Number of trees: 50
## No. of variables tried at each split: 3
## 
##         OOB estimate of  error rate: 8.03%
## Confusion matrix:
##      High  Low class.error
## High 9452  648  0.06415842
## Low   682 5779  0.10555642
```

``` r
# Getting overall accuracy 
sum(diag(rf_fit2$confusion)) / nrow(train_data)
```

```
## [1] 0.9196908
```

Random forests, compared to decision trees, are less explainable since it just directly does the classification and their is no way to display or show the rules. Also, the code to realize the model also suggests that there is some randomness in terms of how many trees we will include in the forest. Eyeballing the error plot can lead people to decide the ideal `ntree` being either 50 or other values that have lower OOB errors.


``` r
# Getting feature importance
importance(rf_fit2) %>%
  as.data.frame() %>%
  mutate(Predictors = row.names(.)) %>%
  arrange(desc(MeanDecreaseGini))
```

```
##                          High       Low MeanDecreaseAccuracy MeanDecreaseGini
## math               28.6361546 41.836271            57.338086        3722.5981
## reading            37.6112696 34.052490            57.228208        2566.7833
## env_awareness       3.1190353  6.641222             6.131665         308.9174
## epist_beliefs       0.7451681  1.971789             1.796252         301.0978
## eco_soc_cul_status  4.7888690  5.792610             7.931875         251.3789
## home_possessions    5.8160451  8.021345             8.369707         207.9932
## family_wealth       5.2271438  7.341639             8.950185         203.1964
## ict_resources       6.7873170  7.071192            10.613812         177.8410
## home_ed_resources   4.3637659  3.897891             5.456569         134.3787
##                            Predictors
## math                             math
## reading                       reading
## env_awareness           env_awareness
## epist_beliefs           epist_beliefs
## eco_soc_cul_status eco_soc_cul_status
## home_possessions     home_possessions
## family_wealth           family_wealth
## ict_resources           ict_resources
## home_ed_resources   home_ed_resources
```

``` r
# Visualizing feature importance
varImpPlot(rf_fit2,
           main = "Importance of Variable for Science Performance")
```

![](ORLA6541_Exercise4_files/figure-html/unnamed-chunk-52-1.png)<!-- -->

Displaying and visualizing importance of variable provide us with some information about what the model is doing. We can see that if we take out math or reading, the accuracy and gini index will drop dramatically, indicating their importacne.


``` r
# Checking model performance using testing data
rf_pred <- predict(rf_fit2, test_data) %>%
  as.data.frame() %>%
  mutate(science_perf = as.factor(`.`)) %>%
  select(science_perf)

confusionMatrix(rf_pred$science_perf, test_data$science_perf)
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction High  Low
##       High 4093  270
##       Low   235 2499
##                                           
##                Accuracy : 0.9288          
##                  95% CI : (0.9226, 0.9347)
##     No Information Rate : 0.6098          
##     P-Value [Acc > NIR] : <2e-16          
##                                           
##                   Kappa : 0.8501          
##                                           
##  Mcnemar's Test P-Value : 0.1303          
##                                           
##             Sensitivity : 0.9457          
##             Specificity : 0.9025          
##          Pos Pred Value : 0.9381          
##          Neg Pred Value : 0.9140          
##              Prevalence : 0.6098          
##          Detection Rate : 0.5767          
##    Detection Prevalence : 0.6148          
##       Balanced Accuracy : 0.9241          
##                                           
##        'Positive' Class : High            
## 
```

Again, we can apply our model with the test data and get the confusion matrix to check the performance metrics. The overall accuracy of the random forest ensemble is 0.9288, slightly higher than that of our decision tree, 0.9225. But we need to refer to other metrics as well since overall accuracy can be biased depending on the balancedness of classes.


``` r
# Data for visualization 
rf_class <- data.frame(actual = test_data$science_perf,
                       predicted = rf_pred$science_perf) |>
  mutate(Status = ifelse(actual == predicted, TRUE, FALSE))

# Visualizing classification results 
ggplot(rf_class, aes(x = predicted, fill = Status)) +
  geom_bar(position = "dodge") +
  labs(x = "Predicted Science Performance",
       y = "Count") +
  theme_bw()
```

![](ORLA6541_Exercise4_files/figure-html/unnamed-chunk-54-1.png)<!-- -->

``` r
# Alternative visualization 
ggplot(rf_class, aes(x = predicted, y = actual, color = Status, shape = Status)) +
  geom_jitter(size = 2, alpha = 0.6) +
  labs(x = "Predicted Science Performance",
       y = "Actual Science Performance") +
  theme_bw()
```

![](ORLA6541_Exercise4_files/figure-html/unnamed-chunk-54-2.png)<!-- -->

I find it is very helpful to visualize our confusion matrix this way, since it enables us to digest the performance more intuitively. An extension idea of this plot would be include the number of observations in each class to the plot so that we have clearer ideas of the distribution of classifications, especially when there are too many observations whcih hinder us to compare.


``` r
# Cross validation for random forests
rf_fit2_cv <- rf.crossValidation(
  x = rf_fit2, 
  xdata = train_data,
  p = 0.1,
  n = 10,
  ntree = 50
)
```

```
## running: classification cross-validation with 10 iterations
```

``` r
# Plot cross validation verses model producers accuracy
par(mfrow = c(1, 2))
plot(rf_fit2_cv, type = "cv", main = "CV producers accuracy")
plot(rf_fit2_cv, type = "model", main = "Model producers accuracy")
```

![](ORLA6541_Exercise4_files/figure-html/unnamed-chunk-55-1.png)<!-- -->

``` r
# Plot cross validation verses model oob
par(mfrow = c(1,2)) 
plot(rf_fit2_cv, type = "cv", stat = "oob", main = "CV oob error")
plot(rf_fit2_cv, type = "model", stat = "oob", main = "Model oob error")   
```

![](ORLA6541_Exercise4_files/figure-html/unnamed-chunk-55-2.png)<!-- -->

``` r
# Resetting layout
par(mfrow = c(1,1))
```

I found this plot hard to interpret. Does it mean the error is stablized as the number of trees increase, but after 8, the OOB error increases. Does that suggest the number of tree should be 8 based on the cross validation results?

# Research Proposal 

Note: This is a group work by Karlie Meng, Maeghan Sill, and Summer Wu.

1.  **What is the purpose of your study?**

The purpose of this study is to use ROC/AUC and classification trees to predict whether a teacher will not continue teaching based on their experience, qualifications, role, school characteristics, and student characteristics. This knowledge will allow school leaders to take action that supports teachers, empowering them to continue growing as educators, while improving student outcomes by providing students with experienced teachers.

2.  **Is there any research literature and theory that supports this argument? How so?**

Teacher retention is critical for maintaining educational quality and improving student outcomes. High teacher turnover disrupts learning environments, increases financial costs for schools, and negatively impacts student achievement [@carver-thomas2017]. While much of the focus in addressing teacher turnover has been on increasing teacher supply, research indicates that organizational factors, school characteristics, and job satisfaction play a more significant role than shortages of qualified teachers [@ingersoll2001].

Teacher characteristics, such as experience, grade level, and subject area, are strongly linked to retention. Early-career and late-career teachers are the most likely to leave the profession due to dissatisfaction and retirement, respectively [@carver-thomas2017; @ingersoll2001]. Though experience level does impact teacher attrition, advanced degrees have minimal direct impact when other factors are controlled [@ingersoll2001]. Turnover is higher in STEM, special education, and high-minority schools [@carver-thomas2017]. Teachers at elementary schools are more likely to move to another school, while teachers at high schools are more likely to leave the profession ([National Center for Education Statistics, 2024](https://nces.ed.gov/programs/coe/pdf/2024/slc_508c.pdf)). 

Richard Ingersoll (2001) posited that organizational factors may be impacting staffing issues more profoundly than teacher supply shortages by causing teacher turnover and unnecessarily increasing demand for teachers. He suggested that these problems necessitate more systemic solutions. Effective principals, for example, have been shown to reduce turnover, especially for high-performing teachers [@grissom2018]. “No-nonsense” management styles, on the other hand, can exacerbate turnover and create trust issues both among staff and between staff members and administration [@mccluskey2022].

Schools that provide adequate administrative support correlate strongly with teacher retention [@ingersoll2001]. Effective administrative support may include opportunities for professional growth, meaningful feedback during evaluations, and access to teaching resources, all of which contribute to a positive school culture [@simon2015]. It should also include “adequate numbers of counselors, special education teachers, and behavioral specialists” ([Bouffard, 2017](https://www.gse.harvard.edu/ideas/usable-knowledge/17/08/riding-turnover-wave)). Collaboration with colleagues and opportunities for observing effective teaching practices have been shown to improve teacher satisfaction and reduce attrition, particularly when embedded in professional development efforts [@mcjames2023].

Additionally, school, district, or state-level policies may impact teachers’ desire to stay in the field. Salary is one of the most discussed aspects of teacher job satisfaction. Carver-Thomas and Darling-Hammond (2017) found that it does, in fact, matter, noting that teachers in districts that offered salaries over \$72,000 were 20% less likely to turn over than those in the bottom quintile. Leave policies also impact the longevity of a teacher. In a field where 77% of the employees are female, a lack of paid family leave policies can make it challenging for employees to balance work and family ([Harper, 2019](https://www.k12dive.com/news/district-maternity-leave-policies-fall-short-on-teacher-support/551920/)).

Demographic factors have also been shown to be reliable predictors of teacher turnover. Teachers of color, who often work in high-poverty schools, face elevated turnover rates, reporting insufficient administrative support and dissatisfaction with administrative actions [@carver-thomas2017]. However, this study is building off of the recommendations for early-warning indicator systems presented by [@oecddig2021], stating that outcome predictors must be “Accurate, Accessible, Actionable and Accountable” (p. 33). Actionable predictors, such as professional development opportunities, induction programs, and mentoring, have demonstrated positive effects on retention [@carver-thomas2017; @mcjames2023]. Education leaders cannot take action on a teacher’s race, age, or gender, so we will not be including them in this study. 

Teachers in schools with high poverty and lower-performing students face higher turnover rates [@carver-thomas2017]. Similarly, turnover is 50% higher in Title I schools and 70% higher in schools with large concentrations of students of color [@carver-thomas2017]. While these student body demographics are not something that school leaders can change, they can adapt their management practices to better support their teaching staff. For these reasons, we have included student academic achievement data, urbanicity, and poverty levels in this analysis. 

3.  **Why is ROC AUC accuracy and/or regression trees (or both) a means to address this purpose?**

ROC AUC can help evaluate the predictive power of individual variables, helping to identify which factors best distinguish between teachers who stay and leave. Usually, researchers pick a subset of variables to explore their relationship with teacher turnover, but with ROC AUC, we can examine each possible predictor and then compare their predictive accuracy. This allows researchers to select their variables and schools to focus on the most impactful areas for intervention.

Decision trees complement this by examining how multiple variables interact to influence turnover. They provide an intuitive, actionable view of the complex relationships among predictors, revealing patterns and thresholds that guide targeted strategies. ROC AUC can also validate the decision tree’s predictive performance, ensuring both interpretability and accuracy. Together, these methods enable data-driven, effective solutions to reduce teacher turnover.

4.  **What would be the research question(s)? (To what extent…)**

**Research Question 1:** What predictors are more accurate in predicting whether a teacher will leave their current school in the following year in California?

**Research Question 2:** To what extent do teacher characteristics and school characteristics accurately predict whether a teacher will leave their current school in the following year in California?

5.  **What type of dataset would you need? Is there a dataset you know of that would work?**

Many of the teacher characteristic variables we need are available through the California Department of Education’s (CDE) Staff Downloadable Data Files page. These datasets also include school characteristics, such as pupil services per student and administrators per student. School level student academic achievement data and free and reduced price lunch percentages as a proxy for school poverty levels are also available through the CDE website. To aggregate information about school leave policies, and collect survey information about management styles, we would need to partner school leaders.

6.  **What types of data would you be looking for?**

We will include both continuous and non-continuous variables that fall under two main categories – teacher characteristics and school characteristics. Teacher characteristics include their years of experience, education level, and teaching grade and subject. School characteristics cover  management style, leave policies, pupil services per student, administrators per student, observation frequency, and student academic achievement. 

7.  **Provide the generalized equations.**

When evaluating classification accuracy, metrics like accuracy, precision, sensitivity, and specificity capture different aspects of a model's performance [@swets1988; @swets2000]. General accuracy alone can be misleading, especially with imbalanced data, as a model may perform well on the majority class while failing on the minority class. Sensitivity (True Positive Rate) and specificity (True Negative Rate) provide a more nuanced understanding by assessing how well positive and negative cases are identified. In this study, we focus on identifying teachers who quit, emphasizing the balance between true positive and false positive proportions. These measures are central to the ROC plot, where the trade-off between them is visualized [@bowers2013; @swets1988] .

AUC, or Area Under the ROC Curve, is a metric that simultaneously considers true positive and false positive proportions, offering a step beyond ROC analysis [@bowers2019]. It provides a single, summary measure of overall classification performance and is calculated using integral calculus (see Equation 1). What makes ROC AUC analysis more empirical is that it allows for the comparison of different AUC scores using the DeLong test. For continuous variables, the difference between two AUC values can be tested for statistical significance by calculating the z-score of the difference and comparing it to the standard normal distribution to obtain a p-value [@hanley1982]. This determines whether the observed difference in AUC values is statistically significant (see Equation 2). Comparisons of AUC values for non-continuous variables can be achieved using Equation 3 [@bowers2019]. 

**Equation 1:** $AUC = \int_{0}^{1} f(x) \, dx$

**Equation 2:** $z = \frac{AUC_1 - AUC_2}{\sqrt{SE^2_{AUC_1} + SE^2_{AUC_2} - 2r \cdot SE_{AUC_1} \cdot SE_{AUC_2}}}$

**Equation 3:** $(\hat{AUC} - AUC) X' \left[ X \left( \frac{1}{p} v_{10} + \frac{1}{q} v_{01} \right) X' \right]^{-1} X (\hat{AUC} - AUC)'$

In classification trees, Gini Impurity and Entropy are two key metrics used to evaluate the quality of splits by measuring the “purity” of a node. Gini Impurity calculates the probability of misclassifying a randomly chosen sample, favoring splits that minimize this impurity (see Equation 4; Bulut & Desjardins, 2021). Entropy, on the other hand, measures the level of disorder or uncertainty in a node (see Equation 5; Bulut & Desjardins, 2021). In both equations, `p` means the proportion of samples in class `i`.

**Equation 4:** $Gini = 1 - ∑(p_i^2)$

**Equation 5:** $Entropy = - ∑(p_i * log₂(p_i))$

8.  **What do you think you would find?**

We expect to identify the predictive accuracy of all variables related to teacher turnover using ROC curves and AUC scores. Based on the results, we will prioritize predictors with the highest accuracy and significance for further analysis. These selected predictors will be used to construct classification trees. We anticipate that the resulting trees will not only achieve high accuracy in predicting teacher turnover but also highlight the most influential factors and their hierarchical relationships. This approach aims to uncover actionable insights into the key drivers of teacher turnover, facilitating the development of targeted interventions and policy recommendations.

9.  **Why would this be important? What would be the implications for this research domain?**

ROC AUC analysis offers critical information to the predictive accuracy of various factors influencing teacher turnover, addressing existing gaps in the research. The classification tree provides interpretable results and clear visualizations of how each predictor contributes to teacher retention, making the findings accessible and actionable for stakeholders.

This research can inform teacher retention programs by identifying key factors that drive turnover, enabling targeted interventions. Additionally, it deepens the understanding of how teacher, school, and student variables interact to influence workforce stability. These insights can shape more effective policies and strategies, improving teacher satisfaction, reducing turnover, and supporting a more stable and effective educational system.

# References

Bouffard, S. (2017). Riding the Turnover Wave \| Harvard Graduate School of Education. <https://www.gse.harvard.edu/ideas/usable-knowledge/17/08/riding-turnover-wave>

California Department of Education. (n.d.) Staff Downloadable Data Files—Accessing Educational Data. <https://www.cde.ca.gov/ds/ad/staff.asp>

Harper, A. (2019). District maternity leave policies fall short on teacher support. K-12 Dive. <https://www.k12dive.com/news/district-maternity-leave-policies-fall-short-on-teacher-support/551920/>

National Center for Education Statistics. (2024). Teacher Turnover: Stayers, Movers, and Leavers. <https://nces.ed.gov/programs/coe/indicator/slc/teacher-turnover>
