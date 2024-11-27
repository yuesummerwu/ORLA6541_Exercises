---
title: "ORLA6541_Exercise1"
author: "Karlie (Yuxin) Meng, Maeghan Sill, & Summer Wu"
date: "2024-09-30"
output:
  html_document:
    keep_md: true
---


``` r
# Setting tibble width to view all columns 
options(tibble.width = Inf)
```

# Wickham & Grolemund (2023)
## Chapter 2 (Workflow: Basics)

### Exercises 2.5

**1. Why does this code not work?** 


``` r
## my_variable <- 10
## my_varıable
##> Error: object 'my_varıable' not found
```

The code does not work because the object name `my_variable` was misspelled. The `i` was spelled as a `ı` when calling it. When referencing a previously assigned object, the spelling must match exactly as it was during assignment, since machines cannot interpret typos. 

**2. Tweak each of the following R commands so that they run correctly:**  

The original codes had some mistakes: <br>
1. `Library`, `tidyverse`, and `mapping` were misspelled; <br>
2. A comma was missing between the `x` and `y` parameters; <br>
3. Quotation marks around `lm` were not complete. 


``` r
## libary(todyverse)

##ggplot(dTA = mpg) + 
  ##geom_point(maping = aes(x = displ y = hwy)) +
  ##geom_smooth(method = "lm)
```

See below for the corrected codes.


``` r
# Correct spelling  
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
# Missing comma between x and y parameters 
ggplot(mpg, mapping = aes(x = displ, y = hwy)) + 
  geom_point() +
  # Incomplete quotation marks
  geom_smooth(formula = y ~ x, method = "lm")
```

![](ORLA6541_Exercise1_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

**3. Press Option + Shift + K / Alt + Shift + K. What happens? How can you get to the same place using the menus?** 

This shortcut opens the "Keyboard Shortcut Quick Reference" page in RStudio, which display a list of commonly used default keyboard shortcuts. We can also access the page through the menues by navigating to "Tools -> Keyboard Shortcut Help".

**4. Let’s revisit an exercise from the Section 1.6. Run the following lines of code. Which of the two plots is saved as mpg-plot.png? Why?** 


``` r
## my_bar_plot <- ggplot(mpg, aes(x = class)) +
  ## geom_bar()
## my_scatter_plot <- ggplot(mpg, aes(x = cty, y = hwy)) +
  ## geom_point()
## ggsave(filename = "mpg-plot.png", plot = my_bar_plot)
```

The first plot, `my_bar_plot`, will be saved as mpg-plot.png because the codes passed `my_bar_plot` as the value for the plot parameter in the `ggsave()` function.

## Chapter 3 (Data Transformation)

### Exercises 3.2.5

**1. In a single pipeline for each condition, find all flights that meet the condition:** 

- Had an arrival delay of two or more hours


``` r
# Importing the dataset
library(nycflights13)

flights |> 
  filter(arr_delay >= 120) 
```

```
## # A tibble: 10,200 × 19
##     year month   day dep_time sched_dep_time dep_delay arr_time sched_arr_time
##    <int> <int> <int>    <int>          <int>     <dbl>    <int>          <int>
##  1  2013     1     1      811            630       101     1047            830
##  2  2013     1     1      848           1835       853     1001           1950
##  3  2013     1     1      957            733       144     1056            853
##  4  2013     1     1     1114            900       134     1447           1222
##  5  2013     1     1     1505           1310       115     1638           1431
##  6  2013     1     1     1525           1340       105     1831           1626
##  7  2013     1     1     1549           1445        64     1912           1656
##  8  2013     1     1     1558           1359       119     1718           1515
##  9  2013     1     1     1732           1630        62     2028           1825
## 10  2013     1     1     1803           1620       103     2008           1750
##    arr_delay carrier flight tailnum origin dest  air_time distance  hour minute
##        <dbl> <chr>    <int> <chr>   <chr>  <chr>    <dbl>    <dbl> <dbl>  <dbl>
##  1       137 MQ        4576 N531MQ  LGA    CLT        118      544     6     30
##  2       851 MQ        3944 N942MQ  JFK    BWI         41      184    18     35
##  3       123 UA         856 N534UA  EWR    BOS         37      200     7     33
##  4       145 UA        1086 N76502  LGA    IAH        248     1416     9      0
##  5       127 EV        4497 N17984  EWR    RIC         63      277    13     10
##  6       125 B6         525 N231JB  EWR    MCO        152      937    13     40
##  7       136 EV        4181 N21197  EWR    MCI        234     1092    14     45
##  8       123 EV        5712 N826AS  JFK    IAD         53      228    13     59
##  9       123 EV        4092 N16911  EWR    DAY        119      533    16     30
## 10       138 MQ        4622 N504MQ  LGA    BNA        154      764    16     20
##    time_hour          
##    <dttm>             
##  1 2013-01-01 06:00:00
##  2 2013-01-01 18:00:00
##  3 2013-01-01 07:00:00
##  4 2013-01-01 09:00:00
##  5 2013-01-01 13:00:00
##  6 2013-01-01 13:00:00
##  7 2013-01-01 14:00:00
##  8 2013-01-01 13:00:00
##  9 2013-01-01 16:00:00
## 10 2013-01-01 16:00:00
## # ℹ 10,190 more rows
```

- Flew to Houston (`IAH` or `HOU`)


``` r
flights |> 
  filter(dest %in% c("IAH", "HOU"))
```

```
## # A tibble: 9,313 × 19
##     year month   day dep_time sched_dep_time dep_delay arr_time sched_arr_time
##    <int> <int> <int>    <int>          <int>     <dbl>    <int>          <int>
##  1  2013     1     1      517            515         2      830            819
##  2  2013     1     1      533            529         4      850            830
##  3  2013     1     1      623            627        -4      933            932
##  4  2013     1     1      728            732        -4     1041           1038
##  5  2013     1     1      739            739         0     1104           1038
##  6  2013     1     1      908            908         0     1228           1219
##  7  2013     1     1     1028           1026         2     1350           1339
##  8  2013     1     1     1044           1045        -1     1352           1351
##  9  2013     1     1     1114            900       134     1447           1222
## 10  2013     1     1     1205           1200         5     1503           1505
##    arr_delay carrier flight tailnum origin dest  air_time distance  hour minute
##        <dbl> <chr>    <int> <chr>   <chr>  <chr>    <dbl>    <dbl> <dbl>  <dbl>
##  1        11 UA        1545 N14228  EWR    IAH        227     1400     5     15
##  2        20 UA        1714 N24211  LGA    IAH        227     1416     5     29
##  3         1 UA         496 N459UA  LGA    IAH        229     1416     6     27
##  4         3 UA         473 N488UA  LGA    IAH        238     1416     7     32
##  5        26 UA        1479 N37408  EWR    IAH        249     1400     7     39
##  6         9 UA        1220 N12216  EWR    IAH        233     1400     9      8
##  7        11 UA        1004 N76508  LGA    IAH        237     1416    10     26
##  8         1 UA         455 N667UA  EWR    IAH        229     1400    10     45
##  9       145 UA        1086 N76502  LGA    IAH        248     1416     9      0
## 10        -2 UA        1461 N39418  EWR    IAH        221     1400    12      0
##    time_hour          
##    <dttm>             
##  1 2013-01-01 05:00:00
##  2 2013-01-01 05:00:00
##  3 2013-01-01 06:00:00
##  4 2013-01-01 07:00:00
##  5 2013-01-01 07:00:00
##  6 2013-01-01 09:00:00
##  7 2013-01-01 10:00:00
##  8 2013-01-01 10:00:00
##  9 2013-01-01 09:00:00
## 10 2013-01-01 12:00:00
## # ℹ 9,303 more rows
```

- Were operated by United, American, or Delta


``` r
flights |> 
  filter(carrier %in% c("UA", "AA", "DL")) 
```

```
## # A tibble: 139,504 × 19
##     year month   day dep_time sched_dep_time dep_delay arr_time sched_arr_time
##    <int> <int> <int>    <int>          <int>     <dbl>    <int>          <int>
##  1  2013     1     1      517            515         2      830            819
##  2  2013     1     1      533            529         4      850            830
##  3  2013     1     1      542            540         2      923            850
##  4  2013     1     1      554            600        -6      812            837
##  5  2013     1     1      554            558        -4      740            728
##  6  2013     1     1      558            600        -2      753            745
##  7  2013     1     1      558            600        -2      924            917
##  8  2013     1     1      558            600        -2      923            937
##  9  2013     1     1      559            600        -1      941            910
## 10  2013     1     1      559            600        -1      854            902
##    arr_delay carrier flight tailnum origin dest  air_time distance  hour minute
##        <dbl> <chr>    <int> <chr>   <chr>  <chr>    <dbl>    <dbl> <dbl>  <dbl>
##  1        11 UA        1545 N14228  EWR    IAH        227     1400     5     15
##  2        20 UA        1714 N24211  LGA    IAH        227     1416     5     29
##  3        33 AA        1141 N619AA  JFK    MIA        160     1089     5     40
##  4       -25 DL         461 N668DN  LGA    ATL        116      762     6      0
##  5        12 UA        1696 N39463  EWR    ORD        150      719     5     58
##  6         8 AA         301 N3ALAA  LGA    ORD        138      733     6      0
##  7         7 UA         194 N29129  JFK    LAX        345     2475     6      0
##  8       -14 UA        1124 N53441  EWR    SFO        361     2565     6      0
##  9        31 AA         707 N3DUAA  LGA    DFW        257     1389     6      0
## 10        -8 UA        1187 N76515  EWR    LAS        337     2227     6      0
##    time_hour          
##    <dttm>             
##  1 2013-01-01 05:00:00
##  2 2013-01-01 05:00:00
##  3 2013-01-01 05:00:00
##  4 2013-01-01 06:00:00
##  5 2013-01-01 05:00:00
##  6 2013-01-01 06:00:00
##  7 2013-01-01 06:00:00
##  8 2013-01-01 06:00:00
##  9 2013-01-01 06:00:00
## 10 2013-01-01 06:00:00
## # ℹ 139,494 more rows
```

- Departed in summer (July, August, and September)


``` r
flights |> 
  filter(month %in% c(7, 8, 9)) 
```

```
## # A tibble: 86,326 × 19
##     year month   day dep_time sched_dep_time dep_delay arr_time sched_arr_time
##    <int> <int> <int>    <int>          <int>     <dbl>    <int>          <int>
##  1  2013     7     1        1           2029       212      236           2359
##  2  2013     7     1        2           2359         3      344            344
##  3  2013     7     1       29           2245       104      151              1
##  4  2013     7     1       43           2130       193      322             14
##  5  2013     7     1       44           2150       174      300            100
##  6  2013     7     1       46           2051       235      304           2358
##  7  2013     7     1       48           2001       287      308           2305
##  8  2013     7     1       58           2155       183      335             43
##  9  2013     7     1      100           2146       194      327             30
## 10  2013     7     1      100           2245       135      337            135
##    arr_delay carrier flight tailnum origin dest  air_time distance  hour minute
##        <dbl> <chr>    <int> <chr>   <chr>  <chr>    <dbl>    <dbl> <dbl>  <dbl>
##  1       157 B6         915 N653JB  JFK    SFO        315     2586    20     29
##  2         0 B6        1503 N805JB  JFK    SJU        200     1598    23     59
##  3       110 B6         234 N348JB  JFK    BTV         66      266    22     45
##  4       188 B6        1371 N794JB  LGA    FLL        143     1076    21     30
##  5       120 AA         185 N324AA  JFK    LAX        297     2475    21     50
##  6       186 B6         165 N640JB  JFK    PDX        304     2454    20     51
##  7       243 VX         415 N627VA  JFK    LAX        298     2475    20      1
##  8       172 B6         425 N535JB  JFK    TPA        140     1005    21     55
##  9       177 B6        1183 N531JB  JFK    MCO        126      944    21     46
## 10       122 B6         623 N663JB  JFK    LAX        304     2475    22     45
##    time_hour          
##    <dttm>             
##  1 2013-07-01 20:00:00
##  2 2013-07-01 23:00:00
##  3 2013-07-01 22:00:00
##  4 2013-07-01 21:00:00
##  5 2013-07-01 21:00:00
##  6 2013-07-01 20:00:00
##  7 2013-07-01 20:00:00
##  8 2013-07-01 21:00:00
##  9 2013-07-01 21:00:00
## 10 2013-07-01 22:00:00
## # ℹ 86,316 more rows
```

- Arrived more than two hours late but didn’t leave late


``` r
flights |> 
  filter(arr_delay > 120 & dep_delay <=0) 
```

```
## # A tibble: 29 × 19
##     year month   day dep_time sched_dep_time dep_delay arr_time sched_arr_time
##    <int> <int> <int>    <int>          <int>     <dbl>    <int>          <int>
##  1  2013     1    27     1419           1420        -1     1754           1550
##  2  2013    10     7     1350           1350         0     1736           1526
##  3  2013    10     7     1357           1359        -2     1858           1654
##  4  2013    10    16      657            700        -3     1258           1056
##  5  2013    11     1      658            700        -2     1329           1015
##  6  2013     3    18     1844           1847        -3       39           2219
##  7  2013     4    17     1635           1640        -5     2049           1845
##  8  2013     4    18      558            600        -2     1149            850
##  9  2013     4    18      655            700        -5     1213            950
## 10  2013     5    22     1827           1830        -3     2217           2010
##    arr_delay carrier flight tailnum origin dest  air_time distance  hour minute
##        <dbl> <chr>    <int> <chr>   <chr>  <chr>    <dbl>    <dbl> <dbl>  <dbl>
##  1       124 MQ        3728 N1EAMQ  EWR    ORD        135      719    14     20
##  2       130 EV        5181 N611QX  LGA    MSN        117      812    13     50
##  3       124 AA        1151 N3CMAA  LGA    DFW        192     1389    13     59
##  4       122 B6           3 N703JB  JFK    SJU        225     1598     7      0
##  5       194 VX         399 N629VA  JFK    LAX        336     2475     7      0
##  6       140 UA         389 N560UA  JFK    SFO        386     2586    18     47
##  7       124 MQ        4540 N721MQ  LGA    DTW        130      502    16     40
##  8       179 AA         707 N3EXAA  LGA    DFW        234     1389     6      0
##  9       143 AA        2083 N565AA  EWR    DFW        230     1372     7      0
## 10       127 MQ        4674 N518MQ  LGA    CLE         90      419    18     30
##    time_hour          
##    <dttm>             
##  1 2013-01-27 14:00:00
##  2 2013-10-07 13:00:00
##  3 2013-10-07 13:00:00
##  4 2013-10-16 07:00:00
##  5 2013-11-01 07:00:00
##  6 2013-03-18 18:00:00
##  7 2013-04-17 16:00:00
##  8 2013-04-18 06:00:00
##  9 2013-04-18 07:00:00
## 10 2013-05-22 18:00:00
## # ℹ 19 more rows
```

- Were delayed by at least an hour, but made up over 30 minutes in flight


``` r
flights |> 
  filter(dep_delay >= 60 & (dep_delay - arr_delay) > 30)
```

```
## # A tibble: 1,844 × 19
##     year month   day dep_time sched_dep_time dep_delay arr_time sched_arr_time
##    <int> <int> <int>    <int>          <int>     <dbl>    <int>          <int>
##  1  2013     1     1     2205           1720       285       46           2040
##  2  2013     1     1     2326           2130       116      131             18
##  3  2013     1     3     1503           1221       162     1803           1555
##  4  2013     1     3     1839           1700        99     2056           1950
##  5  2013     1     3     1850           1745        65     2148           2120
##  6  2013     1     3     1941           1759       102     2246           2139
##  7  2013     1     3     1950           1845        65     2228           2227
##  8  2013     1     3     2015           1915        60     2135           2111
##  9  2013     1     3     2257           2000       177       45           2224
## 10  2013     1     4     1917           1700       137     2135           1950
##    arr_delay carrier flight tailnum origin dest  air_time distance  hour minute
##        <dbl> <chr>    <int> <chr>   <chr>  <chr>    <dbl>    <dbl> <dbl>  <dbl>
##  1       246 AA        1999 N5DNAA  EWR    MIA        146     1085    17     20
##  2        73 B6         199 N594JB  JFK    LAS        290     2248    21     30
##  3       128 UA         551 N835UA  EWR    SFO        320     2565    12     21
##  4        66 AA         575 N631AA  JFK    EGE        239     1747    17      0
##  5        28 AA         177 N332AA  JFK    SFO        314     2586    17     45
##  6        67 UA         979 N402UA  EWR    PHX        274     2133    17     59
##  7         1 B6          91 N636JB  JFK    OAK        319     2576    18     45
##  8        24 9E        3525 N903XJ  JFK    ORD        110      740    19     15
##  9       141 9E        3439 N931XJ  JFK    CVG         90      589    20      0
## 10       105 AA         575 N5EUAA  JFK    EGE        239     1747    17      0
##    time_hour          
##    <dttm>             
##  1 2013-01-01 17:00:00
##  2 2013-01-01 21:00:00
##  3 2013-01-03 12:00:00
##  4 2013-01-03 17:00:00
##  5 2013-01-03 17:00:00
##  6 2013-01-03 17:00:00
##  7 2013-01-03 18:00:00
##  8 2013-01-03 19:00:00
##  9 2013-01-03 20:00:00
## 10 2013-01-04 17:00:00
## # ℹ 1,834 more rows
```


**2. Sort `flights` to find the flights with the longest departure delays. Find the flights that left earliest in the morning.**

Flight `HA 51` had the longest departure delay.


``` r
# Flight with longest departure delay
flights |> 
  arrange(desc(dep_delay))
```

```
## # A tibble: 336,776 × 19
##     year month   day dep_time sched_dep_time dep_delay arr_time sched_arr_time
##    <int> <int> <int>    <int>          <int>     <dbl>    <int>          <int>
##  1  2013     1     9      641            900      1301     1242           1530
##  2  2013     6    15     1432           1935      1137     1607           2120
##  3  2013     1    10     1121           1635      1126     1239           1810
##  4  2013     9    20     1139           1845      1014     1457           2210
##  5  2013     7    22      845           1600      1005     1044           1815
##  6  2013     4    10     1100           1900       960     1342           2211
##  7  2013     3    17     2321            810       911      135           1020
##  8  2013     6    27      959           1900       899     1236           2226
##  9  2013     7    22     2257            759       898      121           1026
## 10  2013    12     5      756           1700       896     1058           2020
##    arr_delay carrier flight tailnum origin dest  air_time distance  hour minute
##        <dbl> <chr>    <int> <chr>   <chr>  <chr>    <dbl>    <dbl> <dbl>  <dbl>
##  1      1272 HA          51 N384HA  JFK    HNL        640     4983     9      0
##  2      1127 MQ        3535 N504MQ  JFK    CMH         74      483    19     35
##  3      1109 MQ        3695 N517MQ  EWR    ORD        111      719    16     35
##  4      1007 AA         177 N338AA  JFK    SFO        354     2586    18     45
##  5       989 MQ        3075 N665MQ  JFK    CVG         96      589    16      0
##  6       931 DL        2391 N959DL  JFK    TPA        139     1005    19      0
##  7       915 DL        2119 N927DA  LGA    MSP        167     1020     8     10
##  8       850 DL        2007 N3762Y  JFK    PDX        313     2454    19      0
##  9       895 DL        2047 N6716C  LGA    ATL        109      762     7     59
## 10       878 AA         172 N5DMAA  EWR    MIA        149     1085    17      0
##    time_hour          
##    <dttm>             
##  1 2013-01-09 09:00:00
##  2 2013-06-15 19:00:00
##  3 2013-01-10 16:00:00
##  4 2013-09-20 18:00:00
##  5 2013-07-22 16:00:00
##  6 2013-04-10 19:00:00
##  7 2013-03-17 08:00:00
##  8 2013-06-27 19:00:00
##  9 2013-07-22 07:00:00
## 10 2013-12-05 17:00:00
## # ℹ 336,766 more rows
```

The earliest time of departure was 12:01am (00:01). 


``` r
# Flight with earliest departure 
flights |> 
  arrange(dep_time)
```

```
## # A tibble: 336,776 × 19
##     year month   day dep_time sched_dep_time dep_delay arr_time sched_arr_time
##    <int> <int> <int>    <int>          <int>     <dbl>    <int>          <int>
##  1  2013     1    13        1           2249        72      108           2357
##  2  2013     1    31        1           2100       181      124           2225
##  3  2013    11    13        1           2359         2      442            440
##  4  2013    12    16        1           2359         2      447            437
##  5  2013    12    20        1           2359         2      430            440
##  6  2013    12    26        1           2359         2      437            440
##  7  2013    12    30        1           2359         2      441            437
##  8  2013     2    11        1           2100       181      111           2225
##  9  2013     2    24        1           2245        76      121           2354
## 10  2013     3     8        1           2355         6      431            440
##    arr_delay carrier flight tailnum origin dest  air_time distance  hour minute
##        <dbl> <chr>    <int> <chr>   <chr>  <chr>    <dbl>    <dbl> <dbl>  <dbl>
##  1        71 B6          22 N206JB  JFK    SYR         41      209    22     49
##  2       179 WN         530 N550WN  LGA    MDW        127      725    21      0
##  3         2 B6        1503 N627JB  JFK    SJU        194     1598    23     59
##  4        10 B6         839 N607JB  JFK    BQN        202     1576    23     59
##  5       -10 B6        1503 N608JB  JFK    SJU        182     1598    23     59
##  6        -3 B6        1503 N527JB  JFK    SJU        197     1598    23     59
##  7         4 B6         839 N508JB  JFK    BQN        198     1576    23     59
##  8       166 WN         530 N231WN  LGA    MDW        117      725    21      0
##  9        87 B6         608 N216JB  JFK    PWM         56      273    22     45
## 10        -9 B6         739 N586JB  JFK    PSE        189     1617    23     55
##    time_hour          
##    <dttm>             
##  1 2013-01-13 22:00:00
##  2 2013-01-31 21:00:00
##  3 2013-11-13 23:00:00
##  4 2013-12-16 23:00:00
##  5 2013-12-20 23:00:00
##  6 2013-12-26 23:00:00
##  7 2013-12-30 23:00:00
##  8 2013-02-11 21:00:00
##  9 2013-02-24 22:00:00
## 10 2013-03-08 23:00:00
## # ℹ 336,766 more rows
```

**3. Sort flights to find the fastest flights. (Hint: Try including a math calculation inside of your function.**

The fastest flight was `US 1860` on `2013-01-28`. 


``` r
# Finding the fastest flight. We assumed that "fastest" here means the flight's speed.
flights |> 
  arrange(distance / (air_time / 60))
```

```
## # A tibble: 336,776 × 19
##     year month   day dep_time sched_dep_time dep_delay arr_time sched_arr_time
##    <int> <int> <int>    <int>          <int>     <dbl>    <int>          <int>
##  1  2013     1    28     1917           1825        52     2118           1935
##  2  2013     6    29      755            800        -5     1035            909
##  3  2013     8    28      932            940        -8     1116           1051
##  4  2013     1    30     1037            955        42     1221           1100
##  5  2013    11    27      556            600        -4      727            658
##  6  2013     5    21      558            600        -2      721            657
##  7  2013    12     9     1540           1535         5     1720           1656
##  8  2013     6    10     1356           1300        56     1646           1414
##  9  2013     7    28     1322           1325        -3     1612           1432
## 10  2013     4    11     1349           1345         4     1542           1453
##    arr_delay carrier flight tailnum origin dest  air_time distance  hour minute
##        <dbl> <chr>    <int> <chr>   <chr>  <chr>    <dbl>    <dbl> <dbl>  <dbl>
##  1       103 US        1860 N755US  LGA    PHL         75       96    18     25
##  2        86 B6        1491 N328JB  JFK    ACK        141      199     8      0
##  3        25 9E        3608 N8932C  JFK    PHL         61       94     9     40
##  4        81 9E        3667 N832AY  JFK    PHL         59       94     9     55
##  5        29 US        1909 N951UW  LGA    PHL         60       96     6      0
##  6        24 US        1289 N956UW  LGA    PHL         60       96     6      0
##  7        24 US        1775 N945UW  LGA    PHL         59       96    15     35
##  8       152 US        2175 N745VJ  LGA    DCA        131      214    13      0
##  9       100 US        1279 N953UW  LGA    PHL         57       96    13     25
## 10        49 9E        3638 N8631E  JFK    PHL         55       94    13     45
##    time_hour          
##    <dttm>             
##  1 2013-01-28 18:00:00
##  2 2013-06-29 08:00:00
##  3 2013-08-28 09:00:00
##  4 2013-01-30 09:00:00
##  5 2013-11-27 06:00:00
##  6 2013-05-21 06:00:00
##  7 2013-12-09 15:00:00
##  8 2013-06-10 13:00:00
##  9 2013-07-28 13:00:00
## 10 2013-04-11 13:00:00
## # ℹ 336,766 more rows
```

**4. Was there a flight on every day of 2013?**

Yes. There are 365 distinct combination of year, month, and day in the dataset, which means there was a flight on every day of 2013.


``` r
# Distinct value of year, month, day 
flights |> 
  count(year, month, day) |>
  filter(is.na(n) == FALSE) 
```

```
## # A tibble: 365 × 4
##     year month   day     n
##    <int> <int> <int> <int>
##  1  2013     1     1   842
##  2  2013     1     2   943
##  3  2013     1     3   914
##  4  2013     1     4   915
##  5  2013     1     5   720
##  6  2013     1     6   832
##  7  2013     1     7   933
##  8  2013     1     8   899
##  9  2013     1     9   902
## 10  2013     1    10   932
## # ℹ 355 more rows
```

**5. Which flights traveled the farthest distance? Which traveled the least distance?**

There is a tie for flights that traveled the farthest distance. One of the flights was `HA 51` on multiple days. 


``` r
# Farthest distance
flights |>
  arrange(desc(distance))
```

```
## # A tibble: 336,776 × 19
##     year month   day dep_time sched_dep_time dep_delay arr_time sched_arr_time
##    <int> <int> <int>    <int>          <int>     <dbl>    <int>          <int>
##  1  2013     1     1      857            900        -3     1516           1530
##  2  2013     1     2      909            900         9     1525           1530
##  3  2013     1     3      914            900        14     1504           1530
##  4  2013     1     4      900            900         0     1516           1530
##  5  2013     1     5      858            900        -2     1519           1530
##  6  2013     1     6     1019            900        79     1558           1530
##  7  2013     1     7     1042            900       102     1620           1530
##  8  2013     1     8      901            900         1     1504           1530
##  9  2013     1     9      641            900      1301     1242           1530
## 10  2013     1    10      859            900        -1     1449           1530
##    arr_delay carrier flight tailnum origin dest  air_time distance  hour minute
##        <dbl> <chr>    <int> <chr>   <chr>  <chr>    <dbl>    <dbl> <dbl>  <dbl>
##  1       -14 HA          51 N380HA  JFK    HNL        659     4983     9      0
##  2        -5 HA          51 N380HA  JFK    HNL        638     4983     9      0
##  3       -26 HA          51 N380HA  JFK    HNL        616     4983     9      0
##  4       -14 HA          51 N384HA  JFK    HNL        639     4983     9      0
##  5       -11 HA          51 N381HA  JFK    HNL        635     4983     9      0
##  6        28 HA          51 N385HA  JFK    HNL        611     4983     9      0
##  7        50 HA          51 N385HA  JFK    HNL        612     4983     9      0
##  8       -26 HA          51 N389HA  JFK    HNL        645     4983     9      0
##  9      1272 HA          51 N384HA  JFK    HNL        640     4983     9      0
## 10       -41 HA          51 N388HA  JFK    HNL        633     4983     9      0
##    time_hour          
##    <dttm>             
##  1 2013-01-01 09:00:00
##  2 2013-01-02 09:00:00
##  3 2013-01-03 09:00:00
##  4 2013-01-04 09:00:00
##  5 2013-01-05 09:00:00
##  6 2013-01-06 09:00:00
##  7 2013-01-07 09:00:00
##  8 2013-01-08 09:00:00
##  9 2013-01-09 09:00:00
## 10 2013-01-10 09:00:00
## # ℹ 336,766 more rows
```

Flight `US 1632` on `2013-07-27` traveled the least distance (*Wow, never knew there is such a short flight! I wonder how fast that would be, but unfortunately the value for `air_time` is missing. Okay, after the group discussion, we found out that flight was operated by US Airways, which has been merged by AA.*).


``` r
# Least distance 
flights |>
  arrange(distance)
```

```
## # A tibble: 336,776 × 19
##     year month   day dep_time sched_dep_time dep_delay arr_time sched_arr_time
##    <int> <int> <int>    <int>          <int>     <dbl>    <int>          <int>
##  1  2013     7    27       NA            106        NA       NA            245
##  2  2013     1     3     2127           2129        -2     2222           2224
##  3  2013     1     4     1240           1200        40     1333           1306
##  4  2013     1     4     1829           1615       134     1937           1721
##  5  2013     1     4     2128           2129        -1     2218           2224
##  6  2013     1     5     1155           1200        -5     1241           1306
##  7  2013     1     6     2125           2129        -4     2224           2224
##  8  2013     1     7     2124           2129        -5     2212           2224
##  9  2013     1     8     2127           2130        -3     2304           2225
## 10  2013     1     9     2126           2129        -3     2217           2224
##    arr_delay carrier flight tailnum origin dest  air_time distance  hour minute
##        <dbl> <chr>    <int> <chr>   <chr>  <chr>    <dbl>    <dbl> <dbl>  <dbl>
##  1        NA US        1632 <NA>    EWR    LGA         NA       17     1      6
##  2        -2 EV        3833 N13989  EWR    PHL         30       80    21     29
##  3        27 EV        4193 N14972  EWR    PHL         30       80    12      0
##  4       136 EV        4502 N15983  EWR    PHL         28       80    16     15
##  5        -6 EV        4645 N27962  EWR    PHL         32       80    21     29
##  6       -25 EV        4193 N14902  EWR    PHL         29       80    12      0
##  7         0 EV        4619 N22909  EWR    PHL         22       80    21     29
##  8       -12 EV        4619 N33182  EWR    PHL         25       80    21     29
##  9        39 EV        4619 N11194  EWR    PHL         30       80    21     30
## 10        -7 EV        4619 N17560  EWR    PHL         27       80    21     29
##    time_hour          
##    <dttm>             
##  1 2013-07-27 01:00:00
##  2 2013-01-03 21:00:00
##  3 2013-01-04 12:00:00
##  4 2013-01-04 16:00:00
##  5 2013-01-04 21:00:00
##  6 2013-01-05 12:00:00
##  7 2013-01-06 21:00:00
##  8 2013-01-07 21:00:00
##  9 2013-01-08 21:00:00
## 10 2013-01-09 21:00:00
## # ℹ 336,766 more rows
```

**6. Does it matter what order you used `filter()` and `arrange()` if you’re using both? Why/why not? Think about the results and how much work the functions would have to do.**

In terms of results, the order of `filter()` and `arrange()` does not change the final output because `arrange()` only sorts the rows and does not modify or exclude them. However, the order may affect operational efficiency. Our group has two thoughts: 1) using `filter()` before `arrange()` is more efficient because `filter()` reduces the number of rows that need to be sorted; 2) using `arrange()` first enables faster `filter()` because it helps you to locate the rows to filter faster if you are applying both functions to the same variable.

### Exercises 3.3.5

**1. Compare `dep_time`, `sched_dep_time`, and `dep_delay` How would you expect those three numbers to be related?**

Since `sched_dep_time` and `dep_time` respectively represents the scheduled departure time and the actual departure time, their difference will be the delay time represented by `dep_delay`. Mathematically speaking, the equation will be `dep_delay` = `sched_dep_time` - `dep_time`. However, the data format of`sched_dep_time` and `dep_time` should be transformed to do the calculation. It will also be more complicated for those whose `sched_dep_time` was before midnight and `dep_time` was after midnight. 

**2. Brainstorm as many ways as possible to select `dep_time`, `dep_delay`, `arr_time`, and `arr_delay` from flights.**

Method 1: Selecting individual variables


``` r
flights |> 
  select(dep_time, dep_delay, arr_time, arr_delay)
```

```
## # A tibble: 336,776 × 4
##    dep_time dep_delay arr_time arr_delay
##       <int>     <dbl>    <int>     <dbl>
##  1      517         2      830        11
##  2      533         4      850        20
##  3      542         2      923        33
##  4      544        -1     1004       -18
##  5      554        -6      812       -25
##  6      554        -4      740        12
##  7      555        -5      913        19
##  8      557        -3      709       -14
##  9      557        -3      838        -8
## 10      558        -2      753         8
## # ℹ 336,766 more rows
```

Method 2: Selecting variables using `start_with()`


``` r
flights |>
  select(starts_with(c("arr_", "dep_")))
```

```
## # A tibble: 336,776 × 4
##    arr_time arr_delay dep_time dep_delay
##       <int>     <dbl>    <int>     <dbl>
##  1      830        11      517         2
##  2      850        20      533         4
##  3      923        33      542         2
##  4     1004       -18      544        -1
##  5      812       -25      554        -6
##  6      740        12      554        -4
##  7      913        19      555        -5
##  8      709       -14      557        -3
##  9      838        -8      557        -3
## 10      753         8      558        -2
## # ℹ 336,766 more rows
```

Method 3: Selecting variables using column index. This approach will only work successfully if no columns are dropped or added to the dataframe, or the column indices will change. 


``` r
flights |>
  select(4, 6, 7, 9)
```

```
## # A tibble: 336,776 × 4
##    dep_time dep_delay arr_time arr_delay
##       <int>     <dbl>    <int>     <dbl>
##  1      517         2      830        11
##  2      533         4      850        20
##  3      542         2      923        33
##  4      544        -1     1004       -18
##  5      554        -6      812       -25
##  6      554        -4      740        12
##  7      555        -5      913        19
##  8      557        -3      709       -14
##  9      557        -3      838        -8
## 10      558        -2      753         8
## # ℹ 336,766 more rows
```

**3. What happens if you specify the name of the same variable multiple times in a `select()` call?**

Specifying the same variable multiple times in a `select()` call has no effect; it works the same as specifying it once. As shown below, the output will only include the `carrier` column once. 


``` r
# Specifying carrier for three times 
flights |> 
  select(carrier, carrier, carrier)
```

```
## # A tibble: 336,776 × 1
##    carrier
##    <chr>  
##  1 UA     
##  2 UA     
##  3 AA     
##  4 B6     
##  5 DL     
##  6 UA     
##  7 B6     
##  8 EV     
##  9 B6     
## 10 AA     
## # ℹ 336,766 more rows
```

**4. What does the `any_of()` function do? Why might it be helpful in conjunction with this vector?** 

This question was phrased differently in the earlier edition of the book, where they used `one_of()` instead of `any_of()`. To explore if the two functions work similarly, we tested both. It turns out that both functions behave in the same way, returning all columns that are predefined in the object variables.

To better understand how `any_of()` works, we included a variable name that does not exist in the dataset within variables. This induced a warning, demonstrating the functions can handle such cases without failing. 

These functions are particularly useful when working with a predefined vector. For instance, if we have stored some variable names in an object in previous steps, we can directly use this object instead of retyping all the variable names.


``` r
# Define a vector named 'variables' that stores a set of column names as character strings.
variables <- c("year", "month", "day", "century")

# Testing one_of()
flights |>
  select(any_of(variables))
```

```
## # A tibble: 336,776 × 3
##     year month   day
##    <int> <int> <int>
##  1  2013     1     1
##  2  2013     1     1
##  3  2013     1     1
##  4  2013     1     1
##  5  2013     1     1
##  6  2013     1     1
##  7  2013     1     1
##  8  2013     1     1
##  9  2013     1     1
## 10  2013     1     1
## # ℹ 336,766 more rows
```

``` r
# Trying one_of() function 
flights |>
  select(one_of(variables))
```

```
## Warning: Unknown columns: `century`
```

```
## # A tibble: 336,776 × 3
##     year month   day
##    <int> <int> <int>
##  1  2013     1     1
##  2  2013     1     1
##  3  2013     1     1
##  4  2013     1     1
##  5  2013     1     1
##  6  2013     1     1
##  7  2013     1     1
##  8  2013     1     1
##  9  2013     1     1
## 10  2013     1     1
## # ℹ 336,766 more rows
```

**5. Does the result of running the following code surprise you? How do the select helpers deal with upper and lower case by default? How can you change that default?**

It was surprising to see the code successfully select variables with different cases, indicating that `select()` helpers are not case-sensitive by default. The help documentation confirms this: the default value for `ignore.case` is `TRUE.` If case sensitivity is required, set `ignore.case = FALSE`. 


``` r
flights |> select(contains("TIME"))
```

```
## # A tibble: 336,776 × 6
##    dep_time sched_dep_time arr_time sched_arr_time air_time time_hour          
##       <int>          <int>    <int>          <int>    <dbl> <dttm>             
##  1      517            515      830            819      227 2013-01-01 05:00:00
##  2      533            529      850            830      227 2013-01-01 05:00:00
##  3      542            540      923            850      160 2013-01-01 05:00:00
##  4      544            545     1004           1022      183 2013-01-01 05:00:00
##  5      554            600      812            837      116 2013-01-01 06:00:00
##  6      554            558      740            728      150 2013-01-01 05:00:00
##  7      555            600      913            854      158 2013-01-01 06:00:00
##  8      557            600      709            723       53 2013-01-01 06:00:00
##  9      557            600      838            846      140 2013-01-01 06:00:00
## 10      558            600      753            745      138 2013-01-01 06:00:00
## # ℹ 336,766 more rows
```

``` r
# Tried to change the default, and we succeeded
## flights |> select(contains("TIME"), ignore.case = FALSE)
```

**6. Rename `air_time` to `air_time_min` to indicate units of measurement and move it to the beginning of the data frame.** 


``` r
flights |>
  # Renaming
  rename(air_time_min = air_time) |>
  # Moving to the first column
  relocate(air_time_min)
```

```
## # A tibble: 336,776 × 19
##    air_time_min  year month   day dep_time sched_dep_time dep_delay arr_time
##           <dbl> <int> <int> <int>    <int>          <int>     <dbl>    <int>
##  1          227  2013     1     1      517            515         2      830
##  2          227  2013     1     1      533            529         4      850
##  3          160  2013     1     1      542            540         2      923
##  4          183  2013     1     1      544            545        -1     1004
##  5          116  2013     1     1      554            600        -6      812
##  6          150  2013     1     1      554            558        -4      740
##  7          158  2013     1     1      555            600        -5      913
##  8           53  2013     1     1      557            600        -3      709
##  9          140  2013     1     1      557            600        -3      838
## 10          138  2013     1     1      558            600        -2      753
##    sched_arr_time arr_delay carrier flight tailnum origin dest  distance  hour
##             <int>     <dbl> <chr>    <int> <chr>   <chr>  <chr>    <dbl> <dbl>
##  1            819        11 UA        1545 N14228  EWR    IAH       1400     5
##  2            830        20 UA        1714 N24211  LGA    IAH       1416     5
##  3            850        33 AA        1141 N619AA  JFK    MIA       1089     5
##  4           1022       -18 B6         725 N804JB  JFK    BQN       1576     5
##  5            837       -25 DL         461 N668DN  LGA    ATL        762     6
##  6            728        12 UA        1696 N39463  EWR    ORD        719     5
##  7            854        19 B6         507 N516JB  EWR    FLL       1065     6
##  8            723       -14 EV        5708 N829AS  LGA    IAD        229     6
##  9            846        -8 B6          79 N593JB  JFK    MCO        944     6
## 10            745         8 AA         301 N3ALAA  LGA    ORD        733     6
##    minute time_hour          
##     <dbl> <dttm>             
##  1     15 2013-01-01 05:00:00
##  2     29 2013-01-01 05:00:00
##  3     40 2013-01-01 05:00:00
##  4     45 2013-01-01 05:00:00
##  5      0 2013-01-01 06:00:00
##  6     58 2013-01-01 05:00:00
##  7      0 2013-01-01 06:00:00
##  8      0 2013-01-01 06:00:00
##  9      0 2013-01-01 06:00:00
## 10      0 2013-01-01 06:00:00
## # ℹ 336,766 more rows
```

**7. Why doesn’t the following work, and what does the error mean?**


``` r
## flights |> 
  ## select(tailnum) |> 
  ## arrange(arr_delay)
## > Error in `arrange()`:
## > ℹ In argument: `..1 = arr_delay`.
## > Caused by error:
## > ! object 'arr_delay' not found
```

The code does not work because after using `select(tailnum)`, only the `tailnum` column is kept in the tibble. Therefore, the attempt to sort rows using `arrange(arr_delay)` fails because the `arr_delay` column no longer exists in the dataset.

The error basically means no column called `arr_delay` was found in the dataset `flights`. 

### Exercises 3.5.7 

**1. Which carrier has the worst average delays? Challenge: can you disentangle the effects of bad airports vs. bad carriers? Why/why not? (Hint: think about `flights |> group_by(carrier, dest) |> summarize(n()))`** 

For this question (actually some other questions as well), we found the prompt not very clear about whether the interested variable is departure delay or arrival delay. We decided to focus on arrival delay, since getting to the destination on time is usually the goal (though it is frustrating to wait at the origin airport). `F9` (Frontier Airlines) has the worst average arrival delays with an average delay time of about 22 minutes.


``` r
# Calculating average delay by carriers
flights |> 
  group_by(carrier) |> 
  summarize(avg_delays = mean(arr_delay, na.rm = TRUE)) |> 
  arrange(desc(avg_delays)) 
```

```
## # A tibble: 16 × 2
##    carrier avg_delays
##    <chr>        <dbl>
##  1 F9          21.9  
##  2 FL          20.1  
##  3 EV          15.8  
##  4 YV          15.6  
##  5 OO          11.9  
##  6 MQ          10.8  
##  7 WN           9.65 
##  8 B6           9.46 
##  9 9E           7.38 
## 10 UA           3.56 
## 11 US           2.13 
## 12 VX           1.76 
## 13 DL           1.64 
## 14 AA           0.364
## 15 HA          -6.92 
## 16 AS          -9.93
```

It is challenging to determine whether airports contribute to delay time for `F9`, as all 685 flights carried by `F9` in 2013 were destined for the same airport. This lack of variation in destinations means we cannot compare `F9`'s on-time performance across different airports to see if delays are higher at specific locations. Therefore, it's not possible to conclude if `F9` tends to experience more delays at certain airports based on this data alone.


``` r
# Investigating the airports for F9 
flights |> 
  group_by(carrier, dest) |> 
  summarize(avg_delays = mean(arr_delay, na.rm = TRUE), n = n(), .groups = "drop") |> 
  arrange(desc(avg_delays)) |> 
  filter(carrier == "F9")
```

```
## # A tibble: 1 × 4
##   carrier dest  avg_delays     n
##   <chr>   <chr>      <dbl> <int>
## 1 F9      DEN         21.9   685
```

After a group discussion, we reconsidered the question. When examining the average delays across all carrier-destination pairs, we found that F9 is no longer at the top. Instead, EV appears to experience frequent delays at numerous airports, making it difficult to differentiate between the impact of airports and carriers.


``` r
# Seeing all carrier-destination pairs 
flights |> 
  group_by(carrier, dest) |> 
  summarize(avg_delays = mean(arr_delay, na.rm = TRUE), n = n(), .groups = "drop") |> 
  arrange(desc(avg_delays))
```

```
## # A tibble: 314 × 4
##    carrier dest  avg_delays     n
##    <chr>   <chr>      <dbl> <int>
##  1 UA      STL        110       2
##  2 OO      ORD        107       1
##  3 OO      DTW         68.5     2
##  4 UA      RDU         56       1
##  5 EV      CAE         42.8   113
##  6 EV      TYS         41.2   323
##  7 EV      PBI         40.7     6
##  8 EV      TUL         33.7   315
##  9 EV      OKC         30.6   346
## 10 UA      JAC         29.9    23
## # ℹ 304 more rows
```

**2. Find the flights that are most delayed upon departure from each destination.**


``` r
# Finding the largest number in dep_delay in each origin group
flights |>
  group_by(origin) |>
  slice_max(dep_delay, n = 1) |>
  relocate(origin, carrier, flight)
```

```
## # A tibble: 3 × 19
## # Groups:   origin [3]
##   origin carrier flight  year month   day dep_time sched_dep_time dep_delay
##   <chr>  <chr>    <int> <int> <int> <int>    <int>          <int>     <dbl>
## 1 EWR    MQ        3695  2013     1    10     1121           1635      1126
## 2 JFK    HA          51  2013     1     9      641            900      1301
## 3 LGA    DL        2119  2013     3    17     2321            810       911
##   arr_time sched_arr_time arr_delay tailnum dest  air_time distance  hour minute
##      <int>          <int>     <dbl> <chr>   <chr>    <dbl>    <dbl> <dbl>  <dbl>
## 1     1239           1810      1109 N517MQ  ORD        111      719    16     35
## 2     1242           1530      1272 N384HA  HNL        640     4983     9      0
## 3      135           1020       915 N927DA  MSP        167     1020     8     10
##   time_hour          
##   <dttm>             
## 1 2013-01-10 16:00:00
## 2 2013-01-09 09:00:00
## 3 2013-03-17 08:00:00
```

**3. How do delays vary over the course of the day? Illustrate your answer with a plot.** 

For this question, we had different interpretations regarding for "the course of the day" and also approached the visualization with different variables. But we ended up with similar observation -- flights get delayed more seriously throughout the day, more like a snowball effect. 

> **Summer** used sechduled delay time as the x-axis and departure delay time as the y-axis.

Based on the visualization, we can observe that: 

- Most flights experience delays of less than 3 hours, as shown by the majority of points clustered below the 180-minute mark on the y-axis. 
- The two peak periods for departure delays are between 6:00 and 10:00 AM, and 4:00 to 9:00 PM, where more significant delays and potential outliers are observed. 
- There are no flights scheduled to depart before 5:00 AM, as evidenced by the absence of data points before this time on the x-axis.


``` r
library(ggplot2)

ggplot(flights, aes(x = sched_dep_time, y = dep_delay)) +
  geom_point(na.rm = TRUE, color = "#0072B2", size = 1) +
  scale_y_continuous(limits = c(0, 1100), breaks = seq(0, 1100, by = 60)) + 
  scale_x_continuous(breaks = seq(0, 2359, by = 100),
                     labels = function(x) sprintf("%02d:%02d", x %/% 100, x %% 100)) +
  theme_minimal() +
  labs(x = "Scheduled Departure Time",
       y = "Departure Delay (mins)")
```

![](ORLA6541_Exercise1_files/figure-html/unnamed-chunk-30-1.png)<!-- -->

> **Maeghan** used departure time as the x-axis and departure delay time as the y-axis.


``` r
ggplot(flights, aes(x = dep_time, y = dep_delay)) +
  geom_point()
```

```
## Warning: Removed 8255 rows containing missing values or values outside the scale range
## (`geom_point()`).
```

![](ORLA6541_Exercise1_files/figure-html/unnamed-chunk-31-1.png)<!-- -->

> **Karlie** picked a particular day, i.e., Janurary 1st, to visualize the delay. 


``` r
#departure delay on Jan_1st
Jan_1st <- flights |>
            select(month, day, dep_delay,hour)|>
             filter(month ==1 & day==1)|>
              group_by(hour)|>
               summarize(avg_delay_h = mean(dep_delay,na.rm =TRUE))

Jan_1st  
```

```
## # A tibble: 19 × 2
##     hour avg_delay_h
##    <dbl>       <dbl>
##  1     5       0.5  
##  2     6       2.16 
##  3     7       3.51 
##  4     8       0.448
##  5     9       5.34 
##  6    10       0.333
##  7    11       3.19 
##  8    12       5.75 
##  9    13      20.4  
## 10    14      17.2  
## 11    15       7.77 
## 12    16      16.3  
## 13    17      28.5  
## 14    18      26.5  
## 15    19      14.7  
## 16    20      14.3  
## 17    21       8.04 
## 18    22      21.8  
## 19    23      -5
```

``` r
plot(x = Jan_1st$hour,
     y = Jan_1st$avg_delay_h,
     type = "b",
     xlab = "Time in hour",
     ylab = "Average delay",
     xaxt = "n" )

axis(1, at = Jan_1st$hour, labels = Jan_1st$hour)
```

![](ORLA6541_Exercise1_files/figure-html/unnamed-chunk-32-1.png)<!-- -->

**4. What happens if you supply a negative n to `slice_min()` and friends?**

According to the help documentation, a negative value of `n` or `prop` will be subtracted from the group size. 

I tested it using the codes below and noticed that: 

1. The row number changed from 336,776 to 336,774 after `flights |> slice_head(n = -2)`, which indicates a negative number means how many rows to deselect. <br>
2. After `flights |> group_by(dest) |> slice_max(dep_delay, n = -1, with_ties = FALSE)`, the number of row reduced to `336,671` from `336,776`, which means `115` rows were deselected. This is exactly the number of `dest` groups. 


``` r
# Number of rows in raw data 
print(nrow(flights))
```

```
## [1] 336776
```

``` r
# Number of dest groups in raw data
print(n_groups(group_by(flights, dest)))
```

```
## [1] 105
```

``` r
# Supplying negative value to n in slice_head
flights |>
  slice_head(n = -2)
```

```
## # A tibble: 336,774 × 19
##     year month   day dep_time sched_dep_time dep_delay arr_time sched_arr_time
##    <int> <int> <int>    <int>          <int>     <dbl>    <int>          <int>
##  1  2013     1     1      517            515         2      830            819
##  2  2013     1     1      533            529         4      850            830
##  3  2013     1     1      542            540         2      923            850
##  4  2013     1     1      544            545        -1     1004           1022
##  5  2013     1     1      554            600        -6      812            837
##  6  2013     1     1      554            558        -4      740            728
##  7  2013     1     1      555            600        -5      913            854
##  8  2013     1     1      557            600        -3      709            723
##  9  2013     1     1      557            600        -3      838            846
## 10  2013     1     1      558            600        -2      753            745
##    arr_delay carrier flight tailnum origin dest  air_time distance  hour minute
##        <dbl> <chr>    <int> <chr>   <chr>  <chr>    <dbl>    <dbl> <dbl>  <dbl>
##  1        11 UA        1545 N14228  EWR    IAH        227     1400     5     15
##  2        20 UA        1714 N24211  LGA    IAH        227     1416     5     29
##  3        33 AA        1141 N619AA  JFK    MIA        160     1089     5     40
##  4       -18 B6         725 N804JB  JFK    BQN        183     1576     5     45
##  5       -25 DL         461 N668DN  LGA    ATL        116      762     6      0
##  6        12 UA        1696 N39463  EWR    ORD        150      719     5     58
##  7        19 B6         507 N516JB  EWR    FLL        158     1065     6      0
##  8       -14 EV        5708 N829AS  LGA    IAD         53      229     6      0
##  9        -8 B6          79 N593JB  JFK    MCO        140      944     6      0
## 10         8 AA         301 N3ALAA  LGA    ORD        138      733     6      0
##    time_hour          
##    <dttm>             
##  1 2013-01-01 05:00:00
##  2 2013-01-01 05:00:00
##  3 2013-01-01 05:00:00
##  4 2013-01-01 05:00:00
##  5 2013-01-01 06:00:00
##  6 2013-01-01 05:00:00
##  7 2013-01-01 06:00:00
##  8 2013-01-01 06:00:00
##  9 2013-01-01 06:00:00
## 10 2013-01-01 06:00:00
## # ℹ 336,764 more rows
```

``` r
# Supplying negative value to n in slice_max
flights |>
  group_by(dest) |>
  slice_max(dep_delay, n = -1, with_ties = FALSE) |> 
  ungroup(dest)
```

```
## # A tibble: 336,671 × 19
##     year month   day dep_time sched_dep_time dep_delay arr_time sched_arr_time
##    <int> <int> <int>    <int>          <int>     <dbl>    <int>          <int>
##  1  2013    12    14     2223           2001       142      133           2304
##  2  2013    12    17     2220           2001       139      120           2304
##  3  2013     7    30     2212           2007       125       57           2259
##  4  2013     9     2     2212           2007       125       48           2259
##  5  2013     7    23     2206           2007       119      116           2259
##  6  2013     5    10     2158           2001       117       53           2308
##  7  2013     8     9     2159           2007       112       37           2259
##  8  2013     6     8     2148           2001       107        9           2308
##  9  2013     9    12     2147           2001       106       25           2248
## 10  2013    10    15     2146           2001       105      106           2248
##    arr_delay carrier flight tailnum origin dest  air_time distance  hour minute
##        <dbl> <chr>    <int> <chr>   <chr>  <chr>    <dbl>    <dbl> <dbl>  <dbl>
##  1       149 B6          65 N659JB  JFK    ABQ        259     1826    20      1
##  2       136 B6          65 N556JB  JFK    ABQ        256     1826    20      1
##  3       118 B6        1505 N621JB  JFK    ABQ        246     1826    20      7
##  4       109 B6        1505 N569JB  JFK    ABQ        227     1826    20      7
##  5       137 B6        1505 N589JB  JFK    ABQ        253     1826    20      7
##  6       105 B6        1505 N588JB  JFK    ABQ        241     1826    20      1
##  7        98 B6        1505 N608JB  JFK    ABQ        246     1826    20      7
##  8        61 B6        1505 N594JB  JFK    ABQ        241     1826    20      1
##  9        97 B6          65 N615JB  JFK    ABQ        241     1826    20      1
## 10       138 B6          65 N640JB  JFK    ABQ        277     1826    20      1
##    time_hour          
##    <dttm>             
##  1 2013-12-14 20:00:00
##  2 2013-12-17 20:00:00
##  3 2013-07-30 20:00:00
##  4 2013-09-02 20:00:00
##  5 2013-07-23 20:00:00
##  6 2013-05-10 20:00:00
##  7 2013-08-09 20:00:00
##  8 2013-06-08 20:00:00
##  9 2013-09-12 20:00:00
## 10 2013-10-15 20:00:00
## # ℹ 336,661 more rows
```

**5. Explain what `count()` does in terms of the dplyr verbs you just learned. What does the sort argument to `count()` do?**

The function `count()` works the same as using a combination of `group_by()` and `summarize(n = n())`. It helps us find the number of occurrences for each unique combination of the variables used for grouping.

Using the `sort` argument with `count()` will arrange the rows in descending order based on the number of occurrences, making it easier to identify the most frequent groups.

**6. Suppose we have the following tiny data frame:**


``` r
df <- tibble(
  x = 1:5,
  y = c("a", "b", "a", "a", "b"),
  z = c("K", "K", "L", "L", "K")
)
```

- Write down what you think the output will look like, then check if you were correct, and describe what `group_by()` does.


``` r
## df |>
  ## group_by(y)
```

I think the output will be a tibble with 5 rows and 3 columns, grouped by the `y` variable into 2 groups (`a` and `b`), shown as `Groups:y [2]`. The `group_by()` function doesn't change the appearance of the tibble itself but adds metadata to indicate that subsequent operations will be performed separately for each group. This means that any following operations will be applied within each of these two groups (a and b) independently, treating the tibble as having two subsets.

Based on the output, my answer is valid. 


``` r
# Checking my answer 
df |>
 group_by(y)
```

```
## # A tibble: 5 × 3
## # Groups:   y [2]
##       x y     z    
##   <int> <chr> <chr>
## 1     1 a     K    
## 2     2 b     K    
## 3     3 a     L    
## 4     4 a     L    
## 5     5 b     K
```
- Write down what you think the output will look like, then check if you were correct, and describe what `arrange()` does. Also, comment on how it’s different from the `group_by()` in part (a).


``` r
## df |>
  ## arrange(y)
```

I think the output tibble will be sorted in ascending order based on the values of `y`, meaning all "a"s will come first, followed by all "b"s. 

This output differs from the `group_by()` function in part (a) in two main ways:

1. `arrange()` changes the order of the rows to sort by the specified variable(s), in this case, `y`. <br>
2.  Unlike `group_by()`, `arrange()` does not add grouping metadata to the tibble. This means that the tibble is not treated as grouped for subsequent operations, and there is no information about groups in the output.

Based on the output, my answer is valid. 


``` r
# Checking my answer 
df |>
  arrange(y)
```

```
## # A tibble: 5 × 3
##       x y     z    
##   <int> <chr> <chr>
## 1     1 a     K    
## 2     3 a     L    
## 3     4 a     L    
## 4     2 b     K    
## 5     5 b     K
```

- Write down what you think the output will look like, then check if you were correct, and describe what the pipeline does.


``` r
## df |>
  ## group_by(y) |>
  ## summarize(mean_x = mean(x))
```

Based on the codes, I think the output will have 2 columns: `y` and `mean_x`, with 2 rows representing the unique values of `y` -- `a` and `b`. The `mean_x` column will contain the mean of the x values within each group defined by y.

The pipeline, first, groups the tibble by the values of `y`, treating all rows within the same `y` value as a single group. Then, it calculates the mean of the `x` values and stores the result in a new column called `mean_x.` 

Based on the output, my answer is valid. 


``` r
# Checking my answer 
df |>
  group_by(y) |>
  summarize(mean_x = mean(x))
```

```
## # A tibble: 2 × 2
##   y     mean_x
##   <chr>  <dbl>
## 1 a       2.67
## 2 b       3.5
```

- Write down what you think the output will look like, then check if you were correct, and describe what the pipeline does. Then, comment on what the message says.


``` r
## df |>
  ## group_by(y, z) |>
  ## summarize(mean_x = mean(x))
```

This pipeline is similar to the previous one, with the key difference being that it groups the tibble by both `y` and `z`. It creates groups based on all possible combinations of `y` and `z` values (i.e., `aK`, `aL`, `bK`), and then calculates the mean of `x` values within each group. Therefore, the output will be a tibble with 3 columns (`y`, `z`, `mean_x`) and 3 rows, and a message on grouping. 

When I ran the code, I noticed that the message was slightly different from what I expected. I anticipated a reminder saying that the tibble is grouped by both `y` and `z.` However, the message only mentions `y` because, by default, dplyr drops the last grouping variable `z` after summarizing, retaining only the first grouping variable `y`. 


``` r
# Checking my answer. 
df |>
  group_by(y, z) |>
  summarize(mean_x = mean(x))
```

```
## `summarise()` has grouped output by 'y'. You can override using the `.groups`
## argument.
```

```
## # A tibble: 3 × 3
## # Groups:   y [2]
##   y     z     mean_x
##   <chr> <chr>  <dbl>
## 1 a     K        1  
## 2 a     L        3.5
## 3 b     K        3.5
```

- Write down what you think the output will look like, then check if you were correct, and describe what the pipeline does. How is the output different from the one in part (d)?


``` r
## df |>
  ## group_by(y, z) |>
  ## summarize(mean_x = mean(x), .groups = "drop")
```

Compared to part (d), this code will output a tibble without the message regarding groups, because this time, the code explicitly commands dplyr to drop all grouping with the `.groups = "drop"` argument.

Based on the output, my answer is valid. 


``` r
# Checking my answer 
df |>
  group_by(y, z) |>
  summarize(mean_x = mean(x), .groups = "drop")
```

```
## # A tibble: 3 × 3
##   y     z     mean_x
##   <chr> <chr>  <dbl>
## 1 a     K        1  
## 2 a     L        3.5
## 3 b     K        3.5
```

- Write down what you think the outputs will look like, then check if you were correct, and describe what each pipeline does. How are the outputs of the two pipelines different?


``` r
## df |>
  ## group_by(y, z) |>
  ## summarize(mean_x = mean(x))

## df |>
  ## group_by(y, z) |>
  ## mutate(mean_x = mean(x))
```

This code groups the data by all unique combinations of `y` and `z`, and then calculates the mean of x within each group, storing it in a new variable called `mean_x` with a grouping message.

The resulting tibble will have 3 rows and 3 columns (`y`, `z`, `mean_x`), representing each unique combination of `y` and `z` with the corresponding mean of `x`.

The output below verifies my anwser. 


``` r
df |>
  group_by(y, z) |>
  summarize(mean_x = mean(x))
```

```
## `summarise()` has grouped output by 'y'. You can override using the `.groups`
## argument.
```

```
## # A tibble: 3 × 3
## # Groups:   y [2]
##   y     z     mean_x
##   <chr> <chr>  <dbl>
## 1 a     K        1  
## 2 a     L        3.5
## 3 b     K        3.5
```

The second code also groups the data by `y` and `z`, and calculates the mean of `x` within each group. However, `mutate()` adds a new column `mean_x` to the original tibble without any message.

The resulting tibble will have 5 rows and 4 columns (`x`, `y`, `z`, and `mean_x`). Each row will retain the original values of `x`, `y`, and `z`, with the new `mean_x` column showing the mean of `x` for each row.

However, I didn't expect to see the second output also including group information. This reminds me that the pipeline itself does not permanently alter the contents of the original data frame; it only temporarily displays the modified output. To retain and use the modified version of the data frame later, it needs to be assigned to an object.


``` r
# Checking my answer 
df |>
  group_by(y, z) |>
  mutate(mean_x = mean(x))
```

```
## # A tibble: 5 × 4
## # Groups:   y, z [3]
##       x y     z     mean_x
##   <int> <chr> <chr>  <dbl>
## 1     1 a     K        1  
## 2     2 b     K        3.5
## 3     3 a     L        3.5
## 4     4 a     L        3.5
## 5     5 b     K        3.5
```

## Chapter 4 (Workflow: Code Style)

### Exercises 4.6

**1. Restyle the following pipelines following the guidelines above.**

Original pipelines:


``` r
# Pipeline 1
## flights|>filter(dest=="IAH")|>group_by(year,month,day)|>summarize(n=n(),
## delay=mean(arr_delay,na.rm=TRUE))|>filter(n>10)

# Pipeline 2
## flights|>filter(carrier=="UA",dest%in%c("IAH","HOU"),sched_dep_time>
## 0900,sched_arr_time<2000)|>group_by(flight)|>summarize(delay=mean(
## arr_delay,na.rm=TRUE),cancelled=sum(is.na(arr_delay)),n=n())|>filter(n>10)
```

Restyled pipelines: 


``` r
# Pipeline 1
flights |> 
  filter(dest == "IAH") |> 
  group_by(year, month, day) |> 
  summarize(
    n = n(),
    delay = mean(arr_delay, na.rm = TRUE)
  ) |> 
  filter(n > 10)
```

```
## `summarise()` has grouped output by 'year', 'month'. You can override using the
## `.groups` argument.
```

```
## # A tibble: 365 × 5
## # Groups:   year, month [12]
##     year month   day     n delay
##    <int> <int> <int> <int> <dbl>
##  1  2013     1     1    20 17.8 
##  2  2013     1     2    20  7   
##  3  2013     1     3    19 18.3 
##  4  2013     1     4    20 -3.2 
##  5  2013     1     5    13 20.2 
##  6  2013     1     6    18  9.28
##  7  2013     1     7    19 -7.74
##  8  2013     1     8    19  7.79
##  9  2013     1     9    19 18.1 
## 10  2013     1    10    19  6.68
## # ℹ 355 more rows
```

``` r
# Pipeline 2
flights |> 
  filter(carrier == "UA",
         dest %in% c("IAH", "HOU"),
         sched_dep_time > 0900,
         sched_arr_time < 2000) |> 
  group_by(flight) |> 
  summarize(
    delay = mean(arr_delay, na.rm = TRUE),
    cancelled = sum(is.na(arr_delay)),
    n = n()
  ) |> 
  filter(n > 10)
```

```
## # A tibble: 74 × 4
##    flight delay cancelled     n
##     <int> <dbl>     <int> <int>
##  1     53 12.5          2    18
##  2    112 14.1          0    14
##  3    205 -1.71         0    14
##  4    235 -5.36         0    14
##  5    255 -9.47         0    15
##  6    268 38.6          1    15
##  7    292  6.57         0    21
##  8    318 10.7          1    20
##  9    337 20.1          2    21
## 10    370 17.5          0    11
## # ℹ 64 more rows
```

## Chapter 5 (Data Tidying)

> “Happy families are all alike; every unhappy family is unhappy in its own way.” <br>
  — Leo Tolstoy

> “Tidy datasets are all alike, but every messy dataset is messy in its own way.” <br>
  — Hadley Wickham
  
> "All high-performing schools have small variance, but low-performing schools are different as they struggle in their own way." <br>
  - Dr. Bowers 

### Exercises 5.2.1 

**1. For each of the sample tables, describe what each observation and each column represents.**


``` r
table1
```

```
## # A tibble: 6 × 4
##   country      year  cases population
##   <chr>       <dbl>  <dbl>      <dbl>
## 1 Afghanistan  1999    745   19987071
## 2 Afghanistan  2000   2666   20595360
## 3 Brazil       1999  37737  172006362
## 4 Brazil       2000  80488  174504898
## 5 China        1999 212258 1272915272
## 6 China        2000 213766 1280428583
```

There are 4 columns and 6 rows in `table1`. 

**columns:** <br>
`country` represents the names of the countries in the dataset. <br>
`year` indicates the year from which the data points are taken. <br>
`cases` represents the number of tuberculosis (TB) cases. <br>
`population` represents the population size.

**rows:** <br>
Each row contains the number of TB `cases` and the `population` of a specific `country` in a particular `year`.


``` r
table2
```

```
## # A tibble: 12 × 4
##    country      year type            count
##    <chr>       <dbl> <chr>           <dbl>
##  1 Afghanistan  1999 cases             745
##  2 Afghanistan  1999 population   19987071
##  3 Afghanistan  2000 cases            2666
##  4 Afghanistan  2000 population   20595360
##  5 Brazil       1999 cases           37737
##  6 Brazil       1999 population  172006362
##  7 Brazil       2000 cases           80488
##  8 Brazil       2000 population  174504898
##  9 China        1999 cases          212258
## 10 China        1999 population 1272915272
## 11 China        2000 cases          213766
## 12 China        2000 population 1280428583
```
There are 4 columns and 12 rows in `table2`.

**columns:** <br>
`country` represents the names of the countries in the dataset. <br>
`year` indicates the year from which the data points are taken. <br>
`type` describes the type of data recorded, either as `"cases"` or `"population"`. <br>
`count` contains the numbers corresponding to the type of data.

**rows:** <br>
Each row provides either the number of TB cases or the population of a specific `country` for a particular `year`.


``` r
table3
```

```
## # A tibble: 6 × 3
##   country      year rate             
##   <chr>       <dbl> <chr>            
## 1 Afghanistan  1999 745/19987071     
## 2 Afghanistan  2000 2666/20595360    
## 3 Brazil       1999 37737/172006362  
## 4 Brazil       2000 80488/174504898  
## 5 China        1999 212258/1272915272
## 6 China        2000 213766/1280428583
```

There are 3 columns and 6 rows in `table3`.

**columns:** <br>
`country` represents the names of the countries in the dataset. <br>
`year` indicates the year from which the data points are taken. <br>
`rate` column contains the numerical expression showing how to calculate the ratio of TB cases to the population for each country in a given year.

**rows:** <br> 
Each row provides a combination of the number of TB cases and the total population of a specific `country` in a particular `year`, formatted as `cases/population`.

**2. Sketch out the process you’d use to calculate the rate for `table2` and `table3.` You will need to perform four operations:**

- Extract the number of TB cases per country per year.
- Extract the matching population per country per year.
- Divide cases by population, and multiply by 10000.
- Store back in the appropriate place.

1. For `table2`:


``` r
# Extracting count_TB_cases and count_population from table2
operation_1_and_2_table2 <- table2|> 
  pivot_wider(
    names_from = type, 
    values_from = count) |> 
  rename(
    count_TB_cases = cases,
    count_population = population
  ) |> 
  mutate(
    count_TB_cases = as.numeric(count_TB_cases),
    count_population = as.numeric(count_population)
  )

# Calculating the rate and create a new variable called rate_TB from table2
operation_3_and_4_table2 <- operation_1_and_2_table2 |>
  mutate(rate_TB = count_TB_cases / count_population * 10000)

# Show the tidied table2 
operation_3_and_4_table2
```

```
## # A tibble: 6 × 5
##   country      year count_TB_cases count_population rate_TB
##   <chr>       <dbl>          <dbl>            <dbl>   <dbl>
## 1 Afghanistan  1999            745         19987071   0.373
## 2 Afghanistan  2000           2666         20595360   1.29 
## 3 Brazil       1999          37737        172006362   2.19 
## 4 Brazil       2000          80488        174504898   4.61 
## 5 China        1999         212258       1272915272   1.67 
## 6 China        2000         213766       1280428583   1.67
```

2. For `table3`: 


``` r
# Extracting count_TB_cases and count_population from table3
operation_1_and_2_table3 <- table3 |> 
  separate(rate, into = c("count_TB_cases", "count_population"), sep = "/") |>
  mutate(
    count_TB_cases = as.numeric(count_TB_cases),
    count_population = as.numeric(count_population)
  )

# Calculating the rate and create a new variable called rate_TB from table3
operation_3_and_4_table3 <- operation_1_and_2_table3 |>
  mutate(rate_TB = count_TB_cases / count_population * 10000)

# Show the tidied table3 
operation_3_and_4_table3
```

```
## # A tibble: 6 × 5
##   country      year count_TB_cases count_population rate_TB
##   <chr>       <dbl>          <dbl>            <dbl>   <dbl>
## 1 Afghanistan  1999            745         19987071   0.373
## 2 Afghanistan  2000           2666         20595360   1.29 
## 3 Brazil       1999          37737        172006362   2.19 
## 4 Brazil       2000          80488        174504898   4.61 
## 5 China        1999         212258       1272915272   1.67 
## 6 China        2000         213766       1280428583   1.67
```

## Chapter 6 (Workflow: Scripts and Projects)

**1. Go to the RStudio Tips Twitter account, https://twitter.com/rstudiotips and find one tip that looks interesting. Practice using it!**

> **Summer:** Wow! I didn't know [RStudio supports Python](https://posit.co/blog/rstudio-v1-4-preview-python-support/)!!! I’m currently working on projects in both R and Python, and I realized I code-switch between these two languages more than I do between English and Mandarin. It’s amazing that `reticulate` enables interfacing between R and Python in scripts and R Markdown. You can even interchange data between R and Python! This is fantastic for team collaboration where members use different programming languages, but you can still seamlessly work with each other's data!

> **Maeghan:** There was a Twitter tip about using ViewPipeSteps by @daranzolin to make it easier to see what every step in a pipeline does by examining the data at each point in the transformation! https://github.com/daranzolin/ViewPipeSteps

> **Karlie:** I found R studio has rainbow parenthesis. It makes it easy to see your layers of function in codes.Here's the link of the tips for using it: https://posit.co/blog/rstudio-1-4-preview-rainbow-parentheses/.

**2. What other common mistakes will RStudio diagnostics report? Read https://support.posit.co/hc/en-us/articles/205753617-Code-Diagnostics to find out.**

> **Summer:** Aha! This page made me realize that I hadn’t turned on all the diagnostics options.

> **Maeghan:** I didn’t know that RStudio would tell you if you defined a variable but did not use it. I also did not know that the ‘i’ in a blue circle was a style diagnostic thatconforms to Hadley Wickham’s style guide. That’s super cool!

> **Karlie:** I didn't know that R studio also have diagnostics for other languages, such as C / C++, JavaScript, and Python.

## Chapter 7 (Data Import)


``` r
students <- read_csv("https://pos.it/r4ds-students-csv", na = c("N/A", ""))
```

```
## Rows: 6 Columns: 5
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (4): Full Name, favourite.food, mealPlan, AGE
## dbl (1): Student ID
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

### Exercises 7.2.4

**1. What function would you use to read a file where fields were separated with “|”?**

Since "|" is a custom delimiter (different from the default comma or semicolon), the `read_delim()` function should be used.

**2. Apart from file, skip, and comment, what other arguments do `read_csv()` and `read_tsv()` have in common?**

`read_csv()` and `read_tsv()` share many other arguments, like `col_names`, `col_types`, `locale`, `na`, `quote`, `trim_ws`, etc.

**3. What are the most important arguments to `read_fwf()`?**

We think the most important element is the FILE, or the URL to the file. 

Among all arguments, there are four most important arguments: <br>
1. `fwf_empty()`: Guesses based on the positions of empty columns. <br>
2. `fwf_widths()`: Supply the widths of the columns. <br>
3. `fwf_positions()`: Supply paired vectors of start and end positions. <br>
4. `fwf_cols()`: Supply named arguments of paired start and end positions or column widths.

**4.Sometimes strings in a CSV file contain commas. To prevent them from causing problems, they need to be surrounded by a quoting character, like `"` or `'`. By default, `read_csv()` assumes that the quoting character will be `"`. To read the following text into a data frame, what argument to `read_csv()` do you need to specify?**


``` r
## "x,y\n1,'a,b'"
```

To correctly read text where strings are enclosed in single quotes (`'`) instead of the default double quotes (`"`), we can specify the quote argument in the `read_csv()` function, as shown below. 


``` r
read_csv("x,y\n1,'a,b'", quote = "'")
```

```
## Rows: 1 Columns: 2
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (1): y
## dbl (1): x
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```
## # A tibble: 1 × 2
##       x y    
##   <dbl> <chr>
## 1     1 a,b
```

**5. Identify what is wrong with each of the following inline CSV files. What happens when you run the code?**

1. `read_csv("a,b\n1,2,3\n4,5,6")` <br>
The header (`a,b`) indicates there are two columns, but each row contains three values (`1,2,3` and `4,5,6`). <br>
When running this code, `read_csv()` will throw an error that suggests the number of columns in the rows does not match the header. 


``` r
read_csv("a,b\n1,2,3\n4,5,6")
```

```
## Warning: One or more parsing issues, call `problems()` on your data frame for details,
## e.g.:
##   dat <- vroom(...)
##   problems(dat)
```

```
## Rows: 2 Columns: 2
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## dbl (1): a
## num (1): b
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```
## # A tibble: 2 × 2
##       a     b
##   <dbl> <dbl>
## 1     1    23
## 2     4    56
```

2. `read_csv("a,b,c\n1,2\n1,2,3,4")` <br>
The header (`a,b,c`) indicates there are three columns, but the first row has only two values (`1,2`), and the second row has four values (`1,2,3,4`). <br>
Similar to the first one, the parsing error indicates the number of values in each row does not match the number of column headers.


``` r
read_csv("a,b,c\n1,2\n1,2,3,4")
```

```
## Warning: One or more parsing issues, call `problems()` on your data frame for details,
## e.g.:
##   dat <- vroom(...)
##   problems(dat)
```

```
## Rows: 2 Columns: 3
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## dbl (2): a, b
## num (1): c
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```
## # A tibble: 2 × 3
##       a     b     c
##   <dbl> <dbl> <dbl>
## 1     1     2    NA
## 2     1     2    34
```

3. `read_csv("a,b\n\"1")` <br>
The string `\"1` starts with a double quote but does not have a closing quote. <br>
Running this code does not generate any warning, but no data has been read as values. 


``` r
read_csv("a,b\n\"1")
```

```
## Rows: 0 Columns: 2
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (2): a, b
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```
## # A tibble: 0 × 2
## # ℹ 2 variables: a <chr>, b <chr>
```

4. `read_csv("a,b\n1,2\na,b")` <br>
Technically, there is no errors in the codes, as the successful generated dataframe shows. However, the values in the second row contains the same values as the header (`a,b`), which can be confusing. 


``` r
read_csv("a,b\n1,2\na,b")
```

```
## Rows: 2 Columns: 2
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (2): a, b
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```
## # A tibble: 2 × 2
##   a     b    
##   <chr> <chr>
## 1 1     2    
## 2 a     b
```

5. `read_csv("a;b\n1;3")` <br>
Here, the delimiter is not comma but semicolons. So instead of using `read_csv()`, we should use `read_csv2()`. <br>
As the output shows, the code will be read as a single value since no delimiter (`,`) was detected. 


``` r
read_csv("a;b\n1;3")
```

```
## Rows: 1 Columns: 1
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (1): a;b
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```
## # A tibble: 1 × 1
##   `a;b`
##   <chr>
## 1 1;3
```

**6. Practice referring to non-syntactic names in the following data frame by:** 


``` r
# Assigning data as a tibble named `annoying`
annoying <- tibble(
  `1` = 1:10,
  `2` = `1` * 2 + rnorm(length(`1`))
)
```

- Extracting the variable called `1`. 


``` r
annoying |> 
  select(`1`)
```

```
## # A tibble: 10 × 1
##      `1`
##    <int>
##  1     1
##  2     2
##  3     3
##  4     4
##  5     5
##  6     6
##  7     7
##  8     8
##  9     9
## 10    10
```

- Plotting a scatterplot of `1` vs. `2`. 


``` r
annoying |> 
  ggplot(aes(x = `1`, y = `2`)) +
  geom_point()
```

![](ORLA6541_Exercise1_files/figure-html/unnamed-chunk-65-1.png)<!-- -->

- Creating a new column called `3`, which is `2` divided by `1`. <br>
- Renaming the columns to `one`, `two`, and `three`. 


``` r
annoying |> 
  mutate(`3` = `2` / `1`) |>
  rename(one = `1`,
         two = `2`,
         three = `3`)
```

```
## # A tibble: 10 × 3
##      one   two three
##    <int> <dbl> <dbl>
##  1     1  1.41  1.41
##  2     2  4.75  2.37
##  3     3  5.66  1.89
##  4     4  6.38  1.60
##  5     5 10.6   2.11
##  6     6 11.1   1.85
##  7     7 14.8   2.11
##  8     8 15.6   1.96
##  9     9 17.9   1.99
## 10    10 21.0   2.10
```

# Bulut & Dejardins (2019)

## Chapter 4 (Data Wrangling)

### Exercises 4.2.1

**1.Read in the pisa data set. Either the full data set (recommended to have 8 Gb of RAM) or one of the smaller data sets.**

Reading the dataset is easy, but previewing the dataset can be challenging due to the size, so subsetting is important for big data wraggling.  


``` r
# Importing data.table 
library(data.table)
```

```
## Warning: package 'data.table' was built under R version 4.3.3
```

```
## 
## Attaching package: 'data.table'
```

```
## The following objects are masked from 'package:lubridate':
## 
##     hour, isoweek, mday, minute, month, quarter, second, wday, week,
##     yday, year
```

```
## The following objects are masked from 'package:dplyr':
## 
##     between, first, last
```

```
## The following object is masked from 'package:purrr':
## 
##     transpose
```

``` r
# Reading pisa data 
pisa <- fread("pisa2015.csv", na.strings = "")
```

### Exercises 4.3.1 

**1. Subset all the "Female" students (`ST004D01T`) in "Germany"**

To avoid displaying a lengthy data table in the output HTML, only the shape of the filtered data table is shown without the actual data.


``` r
# Filtering German females 
germany_female = pisa[ST004D01T == "Female" & CNTRYID == "Germany"]

# Commenting the preview for better display
## germany_female

# Display the shape of the subset
dim(germany_female)
```

```
## [1] 3197  921
```

**2. How many female students are there in Germany?**

As the output from the first question shows, this data table is at student level, and there are 3197 rows in the DT. Therefore, there are 3197 female students in Germany. 

**3. The `.N` function returns the length of a vector/number of rows. Use chaining with the `.N` function to answer Exercise 2.**


``` r
# Using chaining with the .N function to get number of female students in Germany  
pisa[ST004D01T == "Female" & CNTRYID == "Germany"
     ][, .N]
```

```
## [1] 3197
```

### Exercises 4.4.1

**1. The computer and software variables that were created above ask a student whether they had a computer in their home that they can use for school work (`computer`) and whether they had educational software in their home (`software`). Find the proportion of students in the Germany and Uruguay that have a computer in their home or have educational software.**

Before answer the question directly, we need to make sure that the new columns like `computer` and `software` have been prepared. We can define a function to convert a dichotomous item (`yes/no`) to numeric scoring (`1/0`) and then apply the function to selected columns. 


``` r
# Defining a function to convert dichotomous items to numerical binary coding
bin_to_num <- function(x){
  if (is.na(x)) NA
  else if (x == "Yes") 1L
  else if (x == "No") 0L 
}
```


``` r
# Applying function to obtain computer and software variables 
pisa[, `:=`
     (computer = sapply(ST011Q04TA, bin_to_num),
       software = sapply(ST011Q05TA, bin_to_num))]
```

We have created the variables `computer` and `software`, so now we can calculate the proportion of students in Germany and Uruguay that have a computer in their home or have educational software. Since both variables are binary (`0/1`), we can derive the proportion by calling the mean. 

However, the question is stated a bit confusing. I am not sure how to interpret the "or" in the prompt. There are two approaches to understand this question:<br>
1. Seeing the two conditions as independent: proportion of students owning a computer AND proportion of students owning a software <br>
2: Finding the union of the two conditions: proportion of studetns owning a computer OR a software 

Therefore, the codes below calculate all three proportions. 


``` r
# Filtering the data to contain only students from Germany and Uruguay 
pisa[CNTRYID %in% c("Germany", "Uruguay"),
      # Calculating the proportion of these students who own a computer
      .(prop_computer = mean(computer, na.rm = TRUE),
        # Calculating the proportion of these students who own a software
        prop_software = mean(software, na.rm = TRUE),
        # Calculating the proportion of these students who own a computer or a software 
        prop_computer_or_software = mean(computer == 1 | software == 1, na.rm = TRUE))]
```

```
##    prop_computer prop_software prop_computer_or_software
##            <num>         <num>                     <num>
## 1:     0.9227603      0.442047                 0.9346892
```

**2. For just female students, find the proportion of students who have their own room (`own.room`) or a quiet place to study (`quiet.study`).**

Similar to the first question, we need to create binary variables for the selected features. 


``` r
# Converting female variable to binary
pisa[, `:=`
     (female = ifelse(ST004D01T == "Female", 1, 0),
       own_room = sapply(ST011Q02TA, bin_to_num),
       quiet_study = sapply(ST011Q03TA, bin_to_num))]
```

We can calculate the proportion now, using `mean()` again. Similarly, there are two ways to interpret the question, so three values are calculated.


``` r
# Filtering data to contain only female students
pisa[female == 1,
     # Calculating proportion of these students who have their own room 
     .(prop_own_room = mean(own_room, na.rm = TRUE),
       # Calculating proportion of these students who have a quite study place
       prop_quiet_study = mean(quiet_study, na.rm = TRUE),
       # Calculating proportion of these students who have their own room or a quite study place
       prop_own_room_or_quiet_study = mean(own_room == 1 | quiet_study == 1, na.rm = TRUE))]
```

```
##    prop_own_room prop_quiet_study prop_own_room_or_quiet_study
##            <num>            <num>                        <num>
## 1:     0.8067794        0.8794532                    0.9453482
```

### Exercises 4.5.1

**1. Calculate the proportion of students who have art in their home (`art`) and the average age (`AGE`) of the students by gender.**

First, we need to make sure `art` is created.


``` r
# Converting ST011Q09TA to binary values  
pisa[, `:=`
     (art = sapply(ST011Q09TA, bin_to_num))]
```

Then, we can do the calculation.


``` r
pisa[,
     # Calculating proportion of students who have art in their home
     .(prop_art = mean(art, na.rm = TRUE),
       # Calculating average age of students 
       mean_age = mean(AGE, na.rm = TRUE)),
     # Changing grouping key human-readable name
     by = .(sex = ST004D01T)]
```

```
##       sex  prop_art mean_age
##    <char>     <num>    <num>
## 1: Female 0.6147918 15.79282
## 2:   Male 0.5837759 15.79305
```

**2. Within a by argument you can discretize a variable to create a grouping variable. Perform a median split for age within the by argument and assess whether there are age difference associated with having your own room (`own.room`) or a desk (`desk`).**

First, create desk which is the binary presentation of `ST011Q01TA`.


``` r
# Converting ST011Q01TA to binary values 
pisa[, `:=`
     (desk = sapply(ST011Q01TA, bin_to_num))]
```

Then, we can calculate the proportions in older age group and younger age group compared to median age. 


``` r
pisa[,
     # Calculating proportion of students that have their own room 
     .(prop_own_room = mean(own_room, na.rm = TRUE),
       # Calculating proportion of students that have their own desk
       prop_desk = mean(desk, na.rm = TRUE)),
     # Creating a discrete age variable based on median age in the sample 
     by = .(age_group = ifelse(AGE > median(AGE, na.rm = TRUE), "Older", "Younger"))]
```

```
##    age_group prop_own_room prop_desk
##       <char>         <num>     <num>
## 1:     Older     0.8181310 0.8803535
## 2:   Younger     0.8181607 0.8864170
## 3:      <NA>     0.8842344 0.8154006
```

As we can see from the output above, there are some missing values in `AGE`. Therefore, we need to deal with those missing values, where two methods can be adopted.

1. Filtering out those missing values before we do any calculation. <br>


``` r
# Filtering out missing values of group
pisa[!is.na(AGE),
     # Calculating proportion of students that have their own room 
     .(prop_own_room = mean(own_room, na.rm = TRUE),
       # Calculating proportion of students that have their own desk
       prop_desk = mean(desk, na.rm = TRUE)),
     # Creating a discrete age variable based on median age in the sample 
     by = .(age_group = ifelse(AGE > median(AGE, na.rm = TRUE), "Older", "Younger"))]
```

```
##    age_group prop_own_room prop_desk
##       <char>         <num>     <num>
## 1:     Older     0.8181310 0.8803535
## 2:   Younger     0.8181607 0.8864170
```

2. Naming the missing value group as "Unknown". 


``` r
pisa[,
     # Calculating proportion of students that have their own room 
     .(prop_own_room = mean(own_room, na.rm = TRUE),
       # Calculating proportion of students that have their own desk
       prop_desk = mean(desk, na.rm = TRUE)),
     # First condition: if AGE is missing value, age_group = "Unknown" 
     by = .(age_group = ifelse(is.na(AGE), "Unknown",
                              # Second condition: if AGE is larger than median_age, age_group = "Older"
                               ifelse(AGE > median(AGE, na.rm = TRUE), "Older", "Younger")))]
```

```
##    age_group prop_own_room prop_desk
##       <char>         <num>     <num>
## 1:     Older     0.8181310 0.8803535
## 2:   Younger     0.8181607 0.8864170
## 3:   Unknown     0.8842344 0.8154006
```

### 4.8 Lab 

**This afternoon when we discuss supervised learning, we’ll ask you to develop some models to predict the response to the question Do you expect your child will go into a ?" (`PA032Q03TA`).**

**1. Recode this variable so that a “Yes” is 1 and a “No” is a -1 and save the variable as `sci_car`.**
 
We can create a function to achieve this goal, like the book taught. Then, we can recode the variable by using the function. 


``` r
# Defining a function to recode Yes to 1 and No to -1
no_to_neg1 <- function(x){
  if (is.na(x)) NA
  else if (x == "Yes") 1L
  else if (x == "No") -1L
}

# Applying the function to PA032Q03TA and create sci_car
pisa[, `:=`
     (sci_car = sapply(PA032Q03TA, no_to_neg1))]
```

**2. Calculate descriptive for this variable by `sex` and `country.` Specifically, the proportion of test takers whose parents said “Yes” or 1.**

In addition to proportion, I also calculated the standard deviation, total number of records, and total number of valid responses. 


``` r
pisa[, 
     # Proportion of positive responses to sci_car
     .(prop_sci_car = mean(sci_car == 1, na.rm = TRUE),
       # Standard deviation
       sd_sci_car = sd(sci_car, na.rm = TRUE), 
       # Count of rows
       count_sci_car = .N, 
       # Count of non-null values 
       valid_responses = sum(!is.na(sci_car))
     ), 
     # Setting group variables 
     by = .(sex = ST004D01T, country = CNTRYID)]
```

```
##         sex                               country prop_sci_car sd_sci_car
##      <char>                                <char>        <num>      <num>
##   1: Female                               Albania          NaN         NA
##   2:   Male                               Albania          NaN         NA
##   3: Female                               Algeria          NaN         NA
##   4:   Male                               Algeria          NaN         NA
##   5: Female                             Australia          NaN         NA
##  ---                                                                     
## 142: Female                   USA (Massachusetts)          NaN         NA
## 143:   Male                  USA (North Carolina)          NaN         NA
## 144: Female                  USA (North Carolina)          NaN         NA
## 145: Female Argentina (Ciudad Autónoma de Buenos)          NaN         NA
## 146:   Male Argentina (Ciudad Autónoma de Buenos)          NaN         NA
##      count_sci_car valid_responses
##              <int>           <int>
##   1:          2691               0
##   2:          2524               0
##   3:          2670               0
##   4:          2849               0
##   5:          7163               0
##  ---                              
## 142:           808               0
## 143:           949               0
## 144:           938               0
## 145:           870               0
## 146:           787               0
```

**3. Means and standard deviations (sd) for the variables that you think will be most predictive of sci_car.**

By reviewing the list of variables provided in the book, I believe that `JOYSCIE` (Enjoyment of Science), `INTBRSCI` (Interest in Broad Science Topics), and `SCIEACT` (Index of Science Activities) are likely to be the most predictive of sci_car.

To better understand these variables, let's print a few rows to explore them further.


``` r
# Displyaing 5 rows that contain no missing values in all variables. 
head(pisa[complete.cases(JOYSCIE, INTBRSCI, SCIEACT), 
          .(JOYSCIE, INTBRSCI, SCIEACT)], 
     5)
```

```
##    JOYSCIE INTBRSCI SCIEACT
##      <num>    <num>   <num>
## 1: -0.2145  -0.0695 -1.7518
## 2:  0.1250  -0.8161 -0.8649
## 3:  2.1635   0.8721  0.1404
## 4:  0.8931   0.3643  0.7539
## 5: -0.6058   0.0345 -1.7518
```

Since these variables are all of the double type, we can proceed with calculating their descriptive statistics.


``` r
pisa[,
     # Mean and SD for Enjoyment of Science
     .(mean_joy_scie = mean(JOYSCIE, na.rm = TRUE),
       sd_joy_scie = sd(JOYSCIE, na.rm = TRUE),
       
       # Mean and SD for Interest in Broad Science Topic
       mean_interest_scie_topics = mean(INTBRSCI, na.rm = TRUE),
       sd_interest_scie_topics = sd(INTBRSCI, na.rm = TRUE),
       
       # Mean and SD for Index of Science Activities
       mean_scie_activities = mean(SCIEACT, na.rm = TRUE),
       sd_scie_activities = sd(SCIEACT, na.rm = TRUE))]
```

```
##    mean_joy_scie sd_joy_scie mean_interest_scie_topics sd_interest_scie_topics
##            <num>       <num>                     <num>                   <num>
## 1:      0.164122    1.089654                0.09799839               0.9751274
##    mean_scie_activities sd_scie_activities
##                   <num>              <num>
## 1:            0.1601696           1.171576
```

**4. Calculate these same descriptives by groups (by `sci_car` and by `sex`).**


``` r
pisa[,
     # Mean and SD for Enjoyment of Science
     .(mean_joy_scie = mean(JOYSCIE, na.rm = TRUE),
       sd_joy_scie = sd(JOYSCIE, na.rm = TRUE),
       
       # Mean and SD for Interest in Broad Science Topic
       mean_interest_scie_topics = mean(INTBRSCI, na.rm = TRUE),
       sd_interest_scie_topics = sd(INTBRSCI, na.rm = TRUE),
       
       # Mean and SD for Index of Science Activities
       mean_scie_activities = mean(SCIEACT, na.rm = TRUE),
       sd_scie_activities = sd(SCIEACT, na.rm = TRUE)),
     
     # Setting up the grouping variables 
     by = .(sci_car, sex = ST004D01T)] 
```

```
##    sci_car    sex mean_joy_scie sd_joy_scie mean_interest_scie_topics
##      <int> <char>         <num>       <num>                     <num>
## 1:      NA Female    0.14684571   1.0772324              -0.009055617
## 2:      NA   Male    0.19113901   1.1036065               0.183288724
## 3:      -1 Female   -0.22929163   1.0133514              -0.165847877
## 4:       1 Female    0.52098349   0.9986123               0.324439003
## 5:      -1   Male   -0.05881394   1.0610385               0.075884975
## 6:       1   Male    0.54382403   1.0409950               0.514705879
##    sd_interest_scie_topics mean_scie_activities sd_scie_activities
##                      <num>                <num>              <num>
## 1:               0.9206390           0.01564585           1.143845
## 2:               1.0277180           0.34537073           1.195840
## 3:               0.8978408          -0.35663726           1.068025
## 4:               0.8237920           0.23087330           1.008141
## 5:               1.0240510           0.08239998           1.172360
## 6:               0.8988173           0.52013720           1.029012
```

**5. Calculate correlations between these variables and `sci_car`.**


``` r
pisa[, 
     .(cor_joy_scie = cor(sci_car, JOYSCIE, use = "complete.obs"),
       cor_interest_scie_topics = cor(sci_car, INTBRSCI, use = "complete.obs"),
       cor_scie_activities = cor(sci_car, SCIEACT, use = "complete.obs"))]
```

```
##    cor_joy_scie cor_interest_scie_topics cor_scie_activities
##           <num>                    <num>               <num>
## 1:    0.3109379                0.2470945           0.2345618
```

**6. Create new variables** <br>

- Discretize the `math` and `reading` variables using the OECD means (490 for math and 493 for reading) and code them as 1 (at or above the mean) and -1 (below the mean), but do in the data.table way without using the $ operator.<br>

Create variables `math` and `reading` from raw data. 


``` r
# Creating new variables by averaging the ten rows 
pisa[, `:=`
     (math = rowMeans(pisa[, c(paste0("PV", 1:10, "MATH"))], na.rm = TRUE),
       reading = rowMeans(pisa[, c(paste0("PV", 1:10, "READ"))], na.rm = TRUE))]
```


``` r
# Recoding math and reading based on OECD means 
pisa[, `:=`
     (recoded_math = ifelse(math >= 490, 1, -1),
      recoded_reading = ifelse(reading >= 493, 1, -1))]
```

- Calculate the correlation between these variables and the list of variables above.


``` r
pisa[, 
     # Correlation coefficients for DISCLISCI
     .(cor_math_discipline = cor(recoded_math, DISCLISCI, use = "complete.obs"),
       cor_reading_discipline = cor(recoded_reading, DISCLISCI, use = "complete.obs"),
       # Correlation coefficients for TEACHSUP
       cor_math_teacher_support = cor(recoded_math, TEACHSUP, use = "complete.obs"),
       cor_reading_teacher_support = cor(recoded_reading, TEACHSUP, use = "complete.obs"),
       # Correlation coefficients for IBTEACH
       cor_math_inquiry = cor(recoded_math, IBTEACH, use = "complete.obs"),
       cor_reading_inquiry = cor(recoded_reading, IBTEACH, use = "complete.obs"))]
```

```
##    cor_math_discipline cor_reading_discipline cor_math_teacher_support
##                  <num>                  <num>                    <num>
## 1:           0.1175675              0.1160628              -0.06560552
##    cor_reading_teacher_support cor_math_inquiry cor_reading_inquiry
##                          <num>            <num>               <num>
## 1:                 -0.05570923       -0.1262705          -0.1420574
```

**7. Chain together a set of operations** <br>

- For example, create an intermediate variable that is the average of `JOYSCIE` and `INTBRSCI`, and then calculate the mean by `country` by `sci_car` through chaining.


``` r
pisa[, `:=`
     # Average of JOYSCIE and INTBRSCI
     (avg_scie_interest = (JOYSCIE + INTBRSCI) / 2)][,
                                                     # Mean of average by by country and sci_car
                                                     .(mean_avg_scie_interest = mean(avg_scie_interest, na.rm = TRUE)),
                                                     by = .(CNTRYID, sci_car)]
```

```
##                                    CNTRYID sci_car mean_avg_scie_interest
##                                     <char>   <int>                  <num>
##   1:                               Albania      NA                    NaN
##   2:                               Algeria      NA                    NaN
##   3:                             Australia      NA             0.04198405
##   4:                               Austria      NA            -0.13277840
##   5:                               Belgium      -1            -0.29242100
##  ---                                                                     
## 105:                       B-S-J-G (China)      NA             0.41940490
## 106:        Spain (Regions)_duplicated_971      NA             0.07118408
## 107:                   USA (Massachusetts)      NA             0.09526936
## 108:                  USA (North Carolina)      NA             0.18371215
## 109: Argentina (Ciudad Autónoma de Buenos)      NA                    NaN
```

**8. Transform variables, specifically recode `MISCED` and `FISCED` from characters to numeric variables.**

This section failed... We tried every possible ways to recode the variables to numeric, using the book, Google, and ChatGPT, but we still weren't able to manage it using data.table. 


``` r
# Method 1 of many methods we tried... :(
parent_education_mapping <- list(
  None = 0,
  "ISCED 1" = 1,
  "ISCED 2" = 2, 
  "ISCED 3A, ISCED 4" = 3,
  "ISCED 3B, C" = 4,
  "ISCED 5A, 6" = 5, 
  "ISCED 5B" = 6,
  is.na = NA
)

## pisa[, MISCED_num := as.numeric(factor(MISCED, levels = names(parent_education_mapping), labels = unlist(parent_education_mapping)))]
## pisa[, FISCED_num := as.numeric(factor(FISCED, levels = names(parent_education_mapping), labels = unlist(parent_education_mapping)))]
```

**9. Examine other variables in the pisa data set that you think might be predictive of `PA032Q03TA`.**

We chose another numerical variable called `OUTHOURS`, and calculated its mean, sd, and correlation coefficient with `sci_car`. 

We intended to see the correlation between parental education and `sci_car`, but we had so many difficulties (a world of pain) recoding them into numerical variables. We will investigate in how to do this using data.table. 


``` r
pisa[, 
     # Calculating mean of study time 
     .(mean_out_study_time = mean(OUTHOURS, na.rm =TRUE),
       # Calculating standard deviation of study time 
       sd_out_study_time = sd(OUTHOURS, na.rm = TRUE),
       # Calculating correlation coefficient between outside study hours and sci_car 
       cor_study_time_scie_car = cor(OUTHOURS, sci_car, use = "complete.obs"))]
```

```
##    mean_out_study_time sd_out_study_time cor_study_time_scie_car
##                  <num>             <num>                   <num>
## 1:            18.96742          14.32802              0.06870792
```

# Citation 

OpenAI. (2024). ChatGPT (September 30, 2024). https://chat.openai.com/

> "I have a categotical variable, and I need to recode them into numerical in data.table. Could you please tell me how to do this?" <br>
> "To recode a categorical variable (like MISCED in your example) into numeric values in a data.table, you can use the fcase() function from data.table, or you can use dplyr's mutate() and case_when() if you're more comfortable with dplyr. Since you're working with a data.table, I'll focus on the data.table solution.<br>
**Steps:**<br>
Identify each category in your MISCED variable.<br>
Map each category to a numeric value using fcase() or factor().<br>
Here’s an example using fcase():<br>
Solution using fcase() in data.table:<br>
**Explanation:**<br>
- fcase(): This is a powerful function in data.table that allows you to map conditions to values. Each condition is followed by the corresponding numeric value.<br>
- MISCED_num: This creates a new column in the pisa dataset called MISCED_num with the recoded numeric values.<br>
- NA_real_: This ensures that NA values are retained as NA."


