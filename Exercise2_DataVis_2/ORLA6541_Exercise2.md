---
title: "ORLA6541_Exercise2"
author: "Karlie (Yuxin) Meng, Maeghan Sill, Summer Wu"
date: "2024-10-03"
output:
  html_document:
    keep_md: true
---


``` r
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
library(ggplot2)
library(gapminder)
library(socviz)
library(scales)
```

```
## 
## Attaching package: 'scales'
## 
## The following object is masked from 'package:purrr':
## 
##     discard
## 
## The following object is masked from 'package:readr':
## 
##     col_factor
```

``` r
library(GGally)
```

```
## Registered S3 method overwritten by 'GGally':
##   method from   
##   +.gg   ggplot2
```

# Healy (2017)

## Chapter 4 (Show the Right Numbers)

**1. Revisit the `gapminder` plots at the beginning of the chapter and experiment with different ways to facet the data. Try plotting population and per capita GDP while faceting on `year`, or even on `country`. In the latter case you will get a lot of panels, and plotting them straight to the screen may take a long time. Instead, assign the plot to an object and save it as a PDF file to your `figures/`folder. Experiment with the height and width of the figure.**

Given the number of panels produced by faceting on `country`, only the plot faceted by `year` is shown here. Key modifications I made include:

-   Added points and a smooth line to visualize trend;
-   Applied a log transformation to population and displayed it in exponential notation;
-   Formatted GDP per capita as dollar values;
-   Used faceting by year to explore changes over time;
-   Edited axis labels for clarits;
-   Adjusted height and width of the figure.


``` r
# Faceting on year 
ggplot(data = gapminder,
       mapping = aes(x = pop, y = gdpPercap)) + 
  geom_point(size = 1,
             aes(color = continent)) +
  geom_smooth(se = FALSE, color = "grey", linewidth = 1) +
  scale_x_log10(labels = label_log()) +
  scale_y_continuous(labels = dollar) +
  facet_wrap(~year) +
  labs(x = "Population",
       y = "GDP Per Capita",
       color = "Continent") +
  theme_minimal()
```

```
## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-2-1.png)<!-- -->

As suggested by the prompt, we can assign the plot faceted by country to an object. At the same time, we can adjust

-   The layout of the faceted plot by setting up the `ncol` parameter
-   The color legend position so that it is easier to locate
-   The figure size


``` r
# Faceting on country and assigning the plot to an object p4.1 
p4.1 <- ggplot(data = gapminder,
       mapping = aes(x = pop, y = gdpPercap)) + 
  geom_point(size = 1,
             aes(color = continent)) +
  geom_smooth(se = FALSE, color = "grey", linewidth = 1) +
  scale_x_log10(labels = label_log()) +
  scale_y_continuous(labels = dollar) +
  facet_wrap(~country, ncol = 3) +
  labs(x = "Population",
       y = "GDP Per Capita",
       color = "Continent") +
  theme_minimal() +
  theme(legend.position = "top")

# Saving p1 as a pdf
ggsave("healy_p4.1.pdf", plot = p4.1, width = 10, height = 70, dpi = 300, limitsize = FALSE)
```

```
## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'
```

**2. Investigate the difference between a formula written as `facet_grid(sex ~ race)` versus one written as `facet_grid(~ sex + race)`.**

To answer this question, we can preset a plot without any facet.


``` r
# Setting up the basic plot without facets
p4.2 <- ggplot(data = gss_sm,
            mapping = aes(x = age, y = childs)) +
  geom_point(alpha = 0.2) +
  geom_smooth()
```

`facet_grid(sex ~ race)` specifies a two-dimensional grid layout, where `sex` is displayed on the rows and `race` is displayed on the columns.


``` r
# Trying facet_grid(sex ~ race)
p4.2 + facet_grid(sex ~ race)
```

```
## `geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'
```

```
## Warning: Removed 18 rows containing non-finite outside the scale range
## (`stat_smooth()`).
```

```
## Warning: Removed 18 rows containing missing values or values outside the scale range
## (`geom_point()`).
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

`facet_grid(~ sex + race)` specifies a one-dimensional layout where both `sex` and `race` will be combined and displayed across the columns only, forming multiple panels in a single row.


``` r
# Trying facet_grid(~ sex + race)
p4.2 + facet_grid(~ sex + race)
```

```
## `geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'
```

```
## Warning: Removed 18 rows containing non-finite outside the scale range
## (`stat_smooth()`).
```

```
## Warning: Removed 18 rows containing missing values or values outside the scale range
## (`geom_point()`).
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-6-1.png)<!-- -->

**3. Experiment to see what happens when you use `facet_wrap()` with more complex forumulas like `facet_wrap(~ sex + race)` instead of facet_grid. Like `facet_grid()`, the `facet_wrap()` function can facet on two or more variables at once. But it will do it by laying the results out in a wrapped one-dimensional table instead of a fully cross-classified grid.**

From the experiment, we can see that the two plots differ in terms of how the panels are laid out.

`facet_wrap()` creates a single-dimensional layout where panels are "wrapped" into multiple rows or columns, depending on the available space in the plot. The panels are arranged sequentially in a single row at first, and when space runs out, the panels "wrap" onto the next row.


``` r
# Using facet_wrap() with the baisc plot created in question 2
p4.2 + facet_wrap(~ sex + race)
```

```
## `geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'
```

```
## Warning: Removed 18 rows containing non-finite outside the scale range
## (`stat_smooth()`).
```

```
## Warning: Removed 18 rows containing missing values or values outside the scale range
## (`geom_point()`).
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

`facet_grid()` enforces a strict grid layout, fully cross-classifying the variables on both the rows and columns. Each unique combination of sex and race will have its own distinct position in the grid.


``` r
# Using facet_grid() with the baisc plot created in question 2
p4.2 + facet_grid(~ sex + race)
```

```
## `geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'
```

```
## Warning: Removed 18 rows containing non-finite outside the scale range
## (`stat_smooth()`).
```

```
## Warning: Removed 18 rows containing missing values or values outside the scale range
## (`geom_point()`).
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

**4. Frequency polygons are closely related to histograms. Instead of displaying the count of observations using bars, they display it with a series of connected lines instead. You can try the various `geom_histogram()` calls in this chapter using `geom_freqpoly()` instead.**

We can use both `geom_histogram()` and `geom_freqpoly()` to display the distribution of the area variable. The `geom_freqpoly()` function connects the midpoints of each bar in the histogram, creating a line plot that visually traces the distribution's shape. Toward the tails, the frequency polygon always reaches 0, reflecting the edges of the data range.


``` r
# Experimenting geom_freqpoly using default bins parameter 
ggplot(midwest, aes(x = area)) + 
  geom_histogram(fill = "grey") +
  geom_freqpoly(color = "skyblue", size = 1) +
  theme_minimal()
```

```
## Warning: Using `size` aesthetic for lines was deprecated in ggplot2 3.4.0.
## ℹ Please use `linewidth` instead.
## This warning is displayed once every 8 hours.
## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
## generated.
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

It’s important to note that both `geom_histogram()` and g`eom_freqpoly()` have a `bins` parameter. If we adjust the number of `bins` for `geom_histogram()`, we should also adjust it for `geom_freqpoly()`. Otherwise, `geom_freqpoly()` will default to `bins = 3`0, potentially leading to mismatched bin widths between the two visualizations.


``` r
# Only adjust bins parameter for geom_histogram()
ggplot(midwest, aes(x = area)) + 
  geom_histogram(bins = 10, fill = "grey") +
  geom_freqpoly(color = "skyblue", size = 1) +
  theme_minimal()
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-10-1.png)<!-- -->

``` r
# Also adjust bins parameter for geom_freqpoly()
ggplot(midwest, aes(x = area)) + 
  geom_histogram(bins = 10, fill = "grey") +
  geom_freqpoly(bins = 10, color = "skyblue", size = 1) +
  theme_minimal()
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-10-2.png)<!-- -->

**5. A histogram bins observations for one variable and shows a bars with the count in each bin. We can do this for two variables at once, too. The `geom_bin2d()` function takes two mappings, x and y. It divides your plot into a grid and colors the bins by the count of observations in them. Try using it on the `gapminder` data to plot life expectancy versus per capita GDP. Like a histogram, you can vary the number or width of the bins for both x or y. Instead of saying `bins = 30` or `binwidth = 1`, provide a number for both `x` and `y` with, for example, `bins = c(20, 50)`. If you specify `bindwith` instead, you will need to pick values that are on the same scale as the variable you are mapping.**

I experimented with `geom_bin2d` using both the `bins` and `binwidth` parameters. As with histogram bins, the choice of these parameters affects how patterns are displayed. I did some rough calculations to match `bins` and `binwidth` to ensure the visualizations look similar or reveal comparable patterns.

In the example below, setting `bins = c(20, 80)` in `geom_bin2d` instructs ggplot to create 20 bins for the `x` variable, representing per capita GDP, and 80 bins for the `y` variable, representing life expectancy.


``` r
# Using parameter bins
ggplot(gapminder, aes(x = gdpPercap, y = lifeExp)) +
  geom_bin2d(bins = c(20, 80))
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-11-1.png)<!-- -->

Using `geom_bin2d` with the parameter `binwidth = c(5000, 1)` instructs ggplot to create bins where each spans 5,000 units on the `x` axis, representing \$5,000, and 1 unit on the `y` axis, representing 1 year.


``` r
# Using parameter binwidth
ggplot(gapminder, aes(x = gdpPercap, y = lifeExp)) +
  geom_bin2d(binwidth = c(5000, 1))
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-12-1.png)<!-- -->

During the group discussion, we realized it is important to set the `bins` or `binwidth` to capture the actual patterns in the data so that the visualization is not misleading. Some people may use this to cheat, knowingly or unknowingly.

**6. Density estimates can also be drawn in two dimensions. The `geom_density_2d()` function draws contour lines estimating the joint distribution of two variables. Try it with the `midwest` data, for example, plotting percent below the poverty line (`percbelowpoverty`) against percent college-educated (`percollege`). Try it with and without a `geom_point()` layer.**

The contour lines drawn by `geom_density_2d()` are powerful tools for visualizing the distribution patterns within the data. These lines represent areas where the density of points is similar, providing an estimate of the joint distribution of the two variables on the plot. When combined with `geom_point()`, the points show the exact locations of individual data points, while the contour lines help us see areas of higher and lower concentration across the plot.

From the visualization below, we can see that:

-   The innermost contour lines surround a dense cluster of points, suggesting that most areas have a percentage below the poverty line (around 5-15%) and a percentage of college-educated individuals (around 10-25%).
-   As the contour lines move outward, they encompass fewer data points. The outer contours represent areas with lower density, suggesting high values for both variables are relatively rare.


``` r
# Presetting the basic plot 
p4.6 <- ggplot(data = midwest, 
       mapping = aes(x = percbelowpoverty, y = percollege))

# Density estimates in two dimensions  
p4.6 + geom_density_2d()
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-13-1.png)<!-- -->

``` r
# Adding points to the plot
p4.6 + geom_point() + geom_density_2d()
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-13-2.png)<!-- -->

During group discussion, we think using `geom_point()` with `geom_density_2d()` is more honest since it allows us to see the points outside the contour lines as well.

We also noticed that the order of the two functions make a difference: the later entered function will be the top layer.

We also tried `geom_density_2d_filled()`, which will color the differnt density areas, as shown below:


``` r
p4.6 + geom_point() + geom_density_2d() + geom_density_2d_filled(alpha = 0.5)
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-14-1.png)<!-- -->

In addition, we also added another `geom` layer to `geom_density_2d()`.


``` r
p4.6 + stat_density_2d(geom = "point", aes(size = after_stat(density)), n = 20, contour = FALSE)
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-15-1.png)<!-- -->

## Chapter 5 (Graph Tables, Add Labels, Make Notes)


``` r
library(ggrepel)
```

```
## Warning: package 'ggrepel' was built under R version 4.3.3
```

**1. The `subset()` function is very useful when used in conjunction with a series of layered geoms. Go back to your code for the Presidential Elections plot (Figure 5.18) and redo it so that it shows all the data points but only labels elections since 1992. You might need to look again at the `elections_historic` data to see what variables are available to you. You can also experiment with subsetting by political party, or changing the colors of the points to reflect the winning party.**

Figure 5.18 is a scatterplot illustrating the relationship between election winners' share of the popular vote and their share of Electoral College votes. This visualization includes a horizontal line at 50% and a vertical line at 50% to highlight the majority thresholds. Each data point is labeled with the election winner's name and the year, providing context for each election outcome. Both the x-axis and y-axis have been scaled to represent percentages.


``` r
# Presetting labels 
p_title <- "Presidential Elections: Popular & Electoral College Margins"
p_subtitle <- "1824-2016"
p_caption <- "Data for 2016 are provisional."
x_label <- "Winner's share of Popular Vote"
y_label <- "Winner's share of Electoral College Votes"

# Replicating Figure 5.18 
p5.18 <- ggplot(elections_historic, aes(x = popular_pct, y = ec_pct,
                                    label = winner_label)) +
  geom_hline(yintercept = 0.5, size = 1.4, color = "gray80") +
  geom_vline(xintercept = 0.5, size = 1.4, color = "gray80") +
  geom_point() +
  geom_text_repel() +
  scale_x_continuous(labels = scales::percent) +
  scale_y_continuous(labels = scales::percent) +
  labs(x = x_label, y = y_label, title = p_title, subtitle = p_subtitle,
      caption = p_caption) +
  theme_minimal()

# View the vis 
p5.18
```

```
## Warning: ggrepel: 15 unlabeled data points (too many overlaps). Consider
## increasing max.overlaps
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-17-1.png)<!-- -->

To extend Figure 5.18, I made the following adjustments:

-   I used a subset of the data to add labels only for data points from elections held after 1992.
-   I customized the colors of the data points and labels to align with the commonly representative colors of the winning parties (blue for Democrats and red for Republicans).
-   I adjusted the legend labels to improve readability.


``` r
p5.1 <- ggplot(elections_historic, aes(x = popular_pct, y = ec_pct, color = win_party)) +
  geom_hline(yintercept = 0.5, size = 1.4, color = "gray80") +
  geom_vline(xintercept = 0.5, size = 1.4, color = "gray80") +
  geom_point() +
  # Using geom_text_repel() for data subset
  geom_text_repel(data = subset(elections_historic, year >= 1992),
                  mapping = aes(label = winner_label, color = win_party)) +
  scale_x_continuous(labels = scales::percent, limits = c(0, 1)) +
  scale_y_continuous(labels = scales::percent, limits = c(0, 1)) +
  # Customizing the color palette
  scale_color_manual(values = c("Dem." = "#007FFF",
                                "Rep." = "#FF0000",
                                "Whig" = "#FFA500",
                                "D.-R."= "#3CB371"), 
                     # Customizing the color labels 
                     labels = c("Democratic-Republican", "Democratic", "Republican", "Whig")) +
  labs(x = x_label, 
       y = y_label, 
       title = p_title, 
       subtitle = "1992-2016",
       caption = p_caption, 
       # Changing the legend label 
       color = "Political Party") +
  theme_minimal()

# View the vis 
p5.1
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-18-1.png)<!-- -->

**2. Use`geom_point()` and `reorder()` to make a Cleveland dot plot of all Presidential elections, ordered by share of the popular vote.**

Originally, I calculated the mean popular share of popular vote for each winner, and use it as the x-axis and winner name as the y-axis. During group discussion, we opted to use election year as the y-axis since the question asks for a dot plot for all Presidential elections.


``` r
# Visualization
p5.2 <- ggplot(elections_historic, aes(x = popular_pct, y = reorder(year, popular_pct), color = win_party)) + 
  geom_point() + 
  scale_x_continuous(labels = scales::percent, limits = c(0, 1)) +
  scale_color_manual(values = c("Dem." = "#007FFF",
                                "Rep." = "#FF0000",
                                "Whig" = "#FFA500",
                                "D.-R."= "#3CB371"), 
                     # Customizing the color labels 
                     labels = c("Democratic-Republican", "Democratic", "Republican", "Whig")) + 
  labs(x = "The Share of Popular Vote",
       y = "Election Year",
       color = "Political Party") +
  theme_minimal() +
  theme(legend.position = "top")

# View the vis 
p5.2
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-19-1.png)<!-- -->

**3. Try using `annotate()` to add a rectangle that lightly colors the entire upper left quadrant of Figure 5.18.**

For this exercise, I directly called and extended the plot, `p5.1`, I created for question 1.


``` r
# Directly extending from p5.1
p5.3 <- p5.1 +
  annotate(geom = "rect", 
           # No values observed outside this range 
           xmin = 0, xmax = 0.5, 
           ymin = 0.5, ymax = 1, 
           fill = "pink", alpha = 0.2)

# View the vis 
p5.3
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-20-1.png)<!-- -->

**4. The main action verbs in the dplyr library are `group_by()`, `filter()`, `select()`, `summarize()`, and `mutate()`. Practice with them by revisiting the `gapminder` data to see if you can reproduce a pair of graphs from Chapter One, shown here again in Figure 5.28. You will need to filter some rows, group the data by continent, and calculate the mean life expectancy by continent before beginning the plotting process.**

The two plots include a bar chart and a dot plot that display life expectancy (in years) for each continent in 2017. Key features of these plots include:

-   Data points are limited to 2017, achieved by using `filter()`.
-   The grouping variable is `continent`, as indicated by the y-axis labels.
-   Continent names have been reordered based on life expectancy values.
-   The x-axis label has been customized, while the y-axis label has been removed.
-   A white background theme has been applied for a clean, minimalist appearance.


``` r
data_5.4 <- gapminder |>
  filter(year == 2007) |>
  group_by(continent) |>
  summarize(mean_life_expectancy = mean(lifeExp, na.rm = TRUE))
```


``` r
p5.4_1 <- ggplot(data_5.4, aes(x = mean_life_expectancy, y = reorder(continent, mean_life_expectancy))) +
  geom_bar(stat = "identity") + 
  labs(x = "Life Expetancy in years, 2007",
       y = NULL) + 
  theme_minimal()

p5.4_1
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-22-1.png)<!-- -->


``` r
p5.4_2 <- ggplot(data_5.4, aes(x = mean_life_expectancy, y = reorder(continent, mean_life_expectancy))) +
  geom_point(size = 3) + 
  labs(x = "Life Expetancy in years, 2007",
       y = NULL) + 
  theme_minimal()

p5.4_2
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-23-1.png)<!-- -->

**5. Get comfortable with grouping, mutating, and summarizing data in pipelines. This will become a routine task as you work with your data. There are many ways that tables can be aggregated and transformed. Remember `group_by()` groups your data from left to right, with the rightmost or innermost group being the level calculations will be done at; `mutate()` adds a column at the current level of grouping; and `summarize()` aggregates to the next level up. Try creating some grouped objects from the GSS data, calculating frequencies as you learned in this Chapter, and then check to see if the totals are what you expect. For example, start by grouping degree by race, like this:**

In the example given by the book, the codes is calculating the count and the percentage of each types of degrees within each race subgroup. If we sum all the `N` values when the `race == "White"`, we would expect the number being equal to the number of count of `race == "White"` without any grouping.


``` r
# Book example
gss_sm |>
  group_by(race, degree) |>
  summarize(N = n()) |>
  mutate(pct = round(N / sum(N)*100, 0))
```

```
## `summarise()` has grouped output by 'race'. You can override using the
## `.groups` argument.
```

```
## # A tibble: 18 × 4
## # Groups:   race [3]
##    race  degree             N   pct
##    <fct> <fct>          <int> <dbl>
##  1 White Lt High School   197     9
##  2 White High School     1057    50
##  3 White Junior College   166     8
##  4 White Bachelor         426    20
##  5 White Graduate         250    12
##  6 White <NA>               4     0
##  7 Black Lt High School    60    12
##  8 Black High School      292    60
##  9 Black Junior College    33     7
## 10 Black Bachelor          71    14
## 11 Black Graduate          31     6
## 12 Black <NA>               3     1
## 13 Other Lt High School    71    26
## 14 Other High School      112    40
## 15 Other Junior College    17     6
## 16 Other Bachelor          39    14
## 17 Other Graduate          37    13
## 18 Other <NA>               1     0
```

``` r
# Calculating total number of observations where `race == "White"`
gss_sm |> summarize(total_white = sum(race == "White"))
```

```
## # A tibble: 1 × 1
##   total_white
##         <int>
## 1        2100
```

Following this example, we can explore the distribution of sex within each region. From the output, we can see that in New England, there are about 43% of males and 57% of females.


``` r
# Distribution of sex within each region 
gss_sm |> 
  group_by(region, sex) |>
  summarize(N = n()) |> 
  mutate(freq = N / sum(N),
         pct = round(N / sum(N) * 100, 0))
```

```
## `summarise()` has grouped output by 'region'. You can override using the
## `.groups` argument.
```

```
## # A tibble: 18 × 5
## # Groups:   region [9]
##    region          sex        N  freq   pct
##    <fct>           <fct>  <int> <dbl> <dbl>
##  1 New England     Male      76 0.434    43
##  2 New England     Female    99 0.566    57
##  3 Middle Atlantic Male     128 0.409    41
##  4 Middle Atlantic Female   185 0.591    59
##  5 E. Nor. Central Male     246 0.490    49
##  6 E. Nor. Central Female   256 0.510    51
##  7 W. Nor. Central Male      90 0.466    47
##  8 W. Nor. Central Female   103 0.534    53
##  9 South Atlantic  Male     234 0.425    43
## 10 South Atlantic  Female   316 0.575    57
## 11 E. Sou. Central Male      87 0.424    42
## 12 E. Sou. Central Female   118 0.576    58
## 13 W. Sou. Central Male     137 0.461    46
## 14 W. Sou. Central Female   160 0.539    54
## 15 Mountain        Male      94 0.4      40
## 16 Mountain        Female   141 0.6      60
## 17 Pacific         Male     184 0.463    46
## 18 Pacific         Female   213 0.537    54
```

If we change the grouping slightly different by sex first and then region, the question we can answer becomes: what percentage of males are from each of the 9 regions? We can see that, among all males, 6% of them are from New England and 10% of them are from Middle Atlantic.


``` r
# Distribution of region within each sex 
gss_sm |> 
  group_by(sex, region) |>
  summarize(N = n()) |> 
  mutate(freq = N / sum(N),
         pct = round(N / sum(N) * 100, 0))
```

```
## `summarise()` has grouped output by 'sex'. You can override using the `.groups`
## argument.
```

```
## # A tibble: 18 × 5
## # Groups:   sex [2]
##    sex    region              N   freq   pct
##    <fct>  <fct>           <int>  <dbl> <dbl>
##  1 Male   New England        76 0.0596     6
##  2 Male   Middle Atlantic   128 0.100     10
##  3 Male   E. Nor. Central   246 0.193     19
##  4 Male   W. Nor. Central    90 0.0705     7
##  5 Male   South Atlantic    234 0.183     18
##  6 Male   E. Sou. Central    87 0.0682     7
##  7 Male   W. Sou. Central   137 0.107     11
##  8 Male   Mountain           94 0.0737     7
##  9 Male   Pacific           184 0.144     14
## 10 Female New England        99 0.0622     6
## 11 Female Middle Atlantic   185 0.116     12
## 12 Female E. Nor. Central   256 0.161     16
## 13 Female W. Nor. Central   103 0.0647     6
## 14 Female South Atlantic    316 0.199     20
## 15 Female E. Sou. Central   118 0.0742     7
## 16 Female W. Sou. Central   160 0.101     10
## 17 Female Mountain          141 0.0886     9
## 18 Female Pacific           213 0.134     13
```

**6. This code is similar to what you saw earlier, but a little more compact. (We calculate the `pct` values directly.) Check the results are as you expect by grouping by `race` and summing the percentages. Try doing the same exercise grouping by `sex` or `region`.**

When we only group by `race` and calculate percentages, we are treating the entire dataset (all observations) as a single denominator within each race category. This approach calculates the percentage of the total dataset represented by each race group. In contrast, when we group by `race` and then by `degree`, the denominator becomes the count within each race group, making the calculation relative to each race subgroup. This method provides the percentage of each degree level within each specific race category.


``` r
# Only group by race 
gss_sm |>
  group_by(race) |>
  summarize(N = n()) |>
  mutate(pct = round(N / sum(N)*100, 0))
```

```
## # A tibble: 3 × 3
##   race      N   pct
##   <fct> <int> <dbl>
## 1 White  2100    73
## 2 Black   490    17
## 3 Other   277    10
```

Similarly, when we only group by `sex`, we are calculating the percentage of each sex subgroup relative to the entire dataset. This provides an overall distribution across all observations, where the resulting percentages for males and females will sum to 100%.


``` r
# Only group by sex 
gss_sm |>
  group_by(sex) |>
  summarize(N = n()) |>
  mutate(pct = round(N / sum(N)*100, 0))
```

```
## # A tibble: 2 × 3
##   sex        N   pct
##   <fct>  <int> <dbl>
## 1 Male    1276    45
## 2 Female  1591    55
```

**7. Try summary calculations with functions other than `sum`. Can you calculate the mean and median number of children by `degree`? (Hint: the `childs` variable in `gss_sm` has children as a numeric value.**

To answer this question, we can directly use the `mean()` and `median()` functions. Additionally, I rounded the output since it doesn’t make practical sense to have the number of children represented as a decimal.


``` r
# Calculating the mean and median of number of children in each degree 
gss_sm |> 
  group_by(degree) |>
  summarize(mean_childs = round(mean(childs, na.rm = TRUE), 0),
            median_childs = round(median(childs, na.rm = TRUE),  0))
```

```
## # A tibble: 6 × 3
##   degree         mean_childs median_childs
##   <fct>                <dbl>         <dbl>
## 1 Lt High School           3             3
## 2 High School              2             2
## 3 Junior College           2             2
## 4 Bachelor                 1             1
## 5 Graduate                 2             2
## 6 <NA>                     4             4
```

During our discussion, we talked about another way to achieve this by using the codes below. The textbook used `funs()` but this function is deprecated in `dplyr 0.8.0`, and we followed the warning to use `list()` instead.


``` r
mean_median <- gss_sm |>
  group_by(degree) |>
    summarize_if(is.numeric, list(mean, sd), na.rm = TRUE) |>
    ungroup()

mean_median
```

```
## # A tibble: 6 × 19
##   degree       year_fn1 id_fn1 ballot_fn1 age_fn1 childs_fn1 sibs_fn1 pres12_fn1
##   <fct>           <dbl>  <dbl>      <dbl>   <dbl>      <dbl>    <dbl>      <dbl>
## 1 Lt High Sch…     2016  1541.       1.98    52.3       2.81     5.79       1.22
## 2 High School      2016  1501.       2.04    48.2       1.86     3.81       1.45
## 3 Junior Coll…     2016  1487.       1.95    47.5       1.77     3.80       1.53
## 4 Bachelor         2016  1296.       2.02    48.1       1.45     2.90       1.46
## 5 Graduate         2016  1213.       2.04    53.0       1.52     2.41       1.32
## 6 <NA>             2016  1422.       2.25    52.6       3.6      7.33       1   
## # ℹ 11 more variables: wtssall_fn1 <dbl>, obama_fn1 <dbl>, year_fn2 <dbl>,
## #   id_fn2 <dbl>, ballot_fn2 <dbl>, age_fn2 <dbl>, childs_fn2 <dbl>,
## #   sibs_fn2 <dbl>, pres12_fn2 <dbl>, wtssall_fn2 <dbl>, obama_fn2 <dbl>
```

**8. `dplyr` has a large number of helper functions that let you summarize data in many different ways. The vignette on window functions included with the `dplyr` documentation is a good place to begin learning about these. You should also look at Chapter 3 of Wickham & Grolemund (2016) for more information on transforming data with `dplyr`.**

In this chapter, we reviewed key functions such as `filter()`, `group_by()`, `mutate()`, and `summmarize()`, which we have learned from Wickham & Grolemund (2016) in previous weeks. Wickham & Grolemund (2016) also introduced the `slice()` family, including `slice_head()` and `slice_tail()`, which allow us to select the first or last rows of a dataset.

From the help section in R Studio, we also learned that we can summarize data using `IQR()` and `n_distinct()`.

Recently, I’ve been working with some statistical models in R and noticed that certain statistical packages also include functions with names similar to those in `dplyr`. If we don’t specify which package a function is from, this can sometimes lead to errors. Therefore, it may be a good practice to use `package::function()` syntax for commonly named functions to avoid confusion.

**9. Experiment with the `gapminder` data to practice some of the new geoms we have learned. Try examining population or life expectancy over time using a series of boxplots. (Hint: you may need to use the group aesthetic in the `aes()` call.) Can you facet this boxplot by `continent`? Is anything different if you create a tibble from `gapminder` that explicitly groups the data by `year` and `continent` first, and then create your plots with that?**

-   Examining population or life expectancy over time using boxplots, faceted by `continent`.


``` r
# Plotting using raw data 
ggplot(gapminder, aes(x = lifeExp, y = factor(year))) +
  geom_boxplot() + 
  facet_wrap(~ continent) +
  labs(x = "Life Expectancy (Yrs)", 
       y = NULL) + 
  theme_minimal()
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-31-1.png)<!-- -->

-   Data preparation first, then plot.


``` r
# Explicitly groups the data by year and continent
data_5.9 <- gapminder |> 
  group_by(year, continent)

# Plotting using the grouped data 
ggplot(data_5.9, aes(x = lifeExp, y = factor(year))) +
  geom_boxplot() + 
  facet_wrap(~ continent) +
  labs(x = "Life Expectancy (Yrs)", 
       y = NULL) + 
  theme_minimal()
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-32-1.png)<!-- -->

These two approaches did not affect the plot results because `group_by()` only influences data manipulation, not the plotting itself.

However, we can skip the group aesthetic in `aes()` by converting the variable into a factor, which tells `ggplot2` to create a separate boxplot for each year without requiring additional grouping specifications. This approach is also beneficial because it automatically displays labels for each year in the dataset, whereas if we use group aesthetic, we have to specify the year labels manually, as shown below. In other words, By factoring year, we avoid the need to manually specify breaks or labels in `scale_y_continuous()`, as `ggplot2` will automatically display each year as a discrete category on the y-axis.


``` r
# Setting group aesthetic and manually adjust the breaks and labels
ggplot(gapminder, aes(x = lifeExp, y = year, group = year)) +
  geom_boxplot() + 
  facet_wrap(~ continent, ncol = 5) +
  scale_y_continuous(breaks = gapminder$year, 
                     labels = gapminder$year) +
  labs(x = "Life Expectancy (Yrs)", 
       y = NULL) + 
  theme_minimal()
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-33-1.png)<!-- -->

**10. Read the help page for `geom_boxplot()` and take a look at the `notch` and `varwidth` options. Try them out to see how they change the look of the plot.**

According to the help page, setting `notch = TRUE` allows us to compare means between groups. But we found it confusing, and in this visualization, it interferes with making inferences.


``` r
# Trying notch = TRUE 
ggplot(gapminder, aes(x = lifeExp, y = factor(year))) +
  geom_boxplot(notch = TRUE) + 
  facet_wrap(~ continent) +
  labs(x = "Life Expectancy (Yrs)", 
       y = NULL) + 
  theme_minimal()
```

```
## Notch went outside hinges
## ℹ Do you want `notch = FALSE`?
## Notch went outside hinges
## ℹ Do you want `notch = FALSE`?
## Notch went outside hinges
## ℹ Do you want `notch = FALSE`?
## Notch went outside hinges
## ℹ Do you want `notch = FALSE`?
## Notch went outside hinges
## ℹ Do you want `notch = FALSE`?
## Notch went outside hinges
## ℹ Do you want `notch = FALSE`?
## Notch went outside hinges
## ℹ Do you want `notch = FALSE`?
## Notch went outside hinges
## ℹ Do you want `notch = FALSE`?
## Notch went outside hinges
## ℹ Do you want `notch = FALSE`?
## Notch went outside hinges
## ℹ Do you want `notch = FALSE`?
## Notch went outside hinges
## ℹ Do you want `notch = FALSE`?
## Notch went outside hinges
## ℹ Do you want `notch = FALSE`?
## Notch went outside hinges
## ℹ Do you want `notch = FALSE`?
## Notch went outside hinges
## ℹ Do you want `notch = FALSE`?
## Notch went outside hinges
## ℹ Do you want `notch = FALSE`?
## Notch went outside hinges
## ℹ Do you want `notch = FALSE`?
## Notch went outside hinges
## ℹ Do you want `notch = FALSE`?
## Notch went outside hinges
## ℹ Do you want `notch = FALSE`?
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-34-1.png)<!-- -->

Setting `varwidth = TRUE` makes the boxes' widths proportional to the square roots of the number of observations in each group (and can be weighted with the `weight` aesthetic if specified). In simpler terms, this means the width of each box reflects the sample size within each subgroup of the grouping variable.


``` r
# Trying varwidth = TRUE 
ggplot(gapminder, aes(x = lifeExp, y = factor(year))) +
  geom_boxplot(varwidth = TRUE) + 
  facet_wrap(~ continent) +
  labs(x = "Life Expectancy (Yrs)", 
       y = NULL) + 
  theme_minimal()
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-35-1.png)<!-- -->

As shown in the plot above, the box widths appear to differ across facets. However, I’m uncertain if the widths vary within the same facet. In the Oceania facet, for example, the box for 1987 seems to have a larger width than the others. As Gestalt principles suggest, humans are generally not great at accurately comparing widths, so I’m unsure if this perceived difference is due to an actual variation in width or simply a visual effect caused by the differing box lengths. More broadly, my question is whether `varwidth = TRUE` applies specifically to the grouping variable we specified in the `group` aesthetic, or if it also considers the facet groups, which may be what the help manual refers to by "groups."

After running the diagnostic code below, I realized why the widths aren’t varying within each facet. Since each continent has the same sample size for each year, the box widths do not differ. Technically, `varwidth = TRUE` adjusts the width based on the number of observations for each unique combination of `year` and `continent` -- our two grouping variables.


``` r
# Diagnosis 
gapminder |>
  group_by(continent, year)  |>
  summarize(count = n())
```

```
## `summarise()` has grouped output by 'continent'. You can override using the
## `.groups` argument.
```

```
## # A tibble: 60 × 3
## # Groups:   continent [5]
##    continent  year count
##    <fct>     <int> <int>
##  1 Africa     1952    52
##  2 Africa     1957    52
##  3 Africa     1962    52
##  4 Africa     1967    52
##  5 Africa     1972    52
##  6 Africa     1977    52
##  7 Africa     1982    52
##  8 Africa     1987    52
##  9 Africa     1992    52
## 10 Africa     1997    52
## # ℹ 50 more rows
```

**11. As an alternative to `geom_boxplot()` try `geom_violin()` for a similar plot, but with a mirrored density distribution instead of a box and whiskers.**

As shown below, we can see that violin plots do not show median, quartiles, and range but the density of data points across different values. Wider sections indicate higher density (more data points), and narrower sections indicate lower density (fewer data points).

For example, in the Americas, life expectancy across different values show a similar density in earlier years. However, in later years, there are more countries (more data points) having higher life expectancy compared to lower ones.


``` r
# Trying geom_violin
ggplot(gapminder, aes(x = lifeExp, y = factor(year))) +
  geom_violin() + 
  facet_wrap(~ continent) +
  labs(x = "Life Expectancy (Yrs)", 
       y = NULL) + 
  theme_minimal()
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-37-1.png)<!-- -->

**12. `geom_pointrange()` is one of a family of related geoms that produce different kinds of error bars and ranges, depending on your specific needs. They include `geom_linerange()`, `geom_crossbar()`, and `geom_errorbar()`. Try them out using `gapminder` or `organdata` to see how they differ.**

For this question, I used the `organdata` dataset. Before exploring the different geom functions, I first summarized the data by calculating the mean donor rate for each country and then set up the basic plot.


``` r
# Summarizing data 
data_5.12 <- organdata |> 
  group_by(country) |>
  summarize(mean_donors_rate = mean(donors, na.rm = TRUE), 
            sd_donors_rate = sd(donors, na.rm = TRUE))

# Setting up basic plot 
p5.12 <- ggplot(data_5.12, aes(x = mean_donors_rate, y = reorder(country, mean_donors_rate))) + 
  scale_x_continuous(limits = c(0, 100)) + 
  labs(x = "Mean Donor Rate (%)",
       y = NULL) + 
  theme_minimal()
```

-   `geom_pointrange()` marks the central value (the mean) and then extends lines to the upper and lower bounds we’ve set, representing the mean ± standard deviation.


``` r
# Trying geom_pointrange()
p5.12 + geom_pointrange(aes(xmin = mean_donors_rate - sd_donors_rate, xmax = mean_donors_rate + sd_donors_rate))
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-39-1.png)<!-- -->

-   `geom_linerange()` is similar to `geom_pointrange()`, but it does not mark the mean values. It only extends lines to the upper and lower bounds.


``` r
# Trying geom_linerange()
p5.12 + geom_linerange(aes(xmin = mean_donors_rate - sd_donors_rate, xmax = mean_donors_rate + sd_donors_rate))
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-40-1.png)<!-- -->

-   `geom_crossbar()` resembles a boxplot, but instead of showing a median line, it marks the central line as the mean value, with the box enclosing the upper and lower bounds we’ve set. Unlike boxplots, it does not display outliers.


``` r
# Trying geom_crossbar()
p5.12 + geom_crossbar(aes(xmin = mean_donors_rate - sd_donors_rate, xmax = mean_donors_rate + sd_donors_rate))
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-41-1.png)<!-- -->

-   `geom_errorbar()` is similar to `geom_linerange()`, but it includes lines at the bounds, making the endpoints more distinct and visually emphasizing the range limits.


``` r
# Trying geom_errorbar()
p5.12 + geom_errorbar(aes(xmin = mean_donors_rate - sd_donors_rate, xmax = mean_donors_rate + sd_donors_rate))
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-42-1.png)<!-- -->

We also used `gapminder` dataset for this question.


``` r
newgapminder <- gapminder %>%
  filter(year == 2007) %>%
  group_by(continent) %>%
  summarize(lifeExp_mean = mean(lifeExp, na.rm = TRUE),
            lifeExp_sd = sd(lifeExp, na.rm = TRUE))

newgapminder
```

```
## # A tibble: 5 × 3
##   continent lifeExp_mean lifeExp_sd
##   <fct>            <dbl>      <dbl>
## 1 Africa            54.8      9.63 
## 2 Americas          73.6      4.44 
## 3 Asia              70.7      7.96 
## 4 Europe            77.6      2.98 
## 5 Oceania           80.7      0.729
```

``` r
p <- ggplot(newgapminder, 
            aes(x = lifeExp_mean, y = reorder(continent, lifeExp_mean)))

#geom_pointrange()
p + 
  geom_pointrange(aes(xmin = lifeExp_mean - lifeExp_sd, xmax = lifeExp_mean + lifeExp_sd)) +
  labs(x = "Life Expectancy in years, 2007", y = "", title = "geom_pointrange()") + 
  theme_minimal()
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-43-1.png)<!-- -->

``` r
#geom_linerange()
p + 
  geom_linerange(aes(xmin = lifeExp_mean - lifeExp_sd, xmax = lifeExp_mean + lifeExp_sd)) +
  labs(x = "Life Expectancy in years, 2007", y = "", title = "geom_linerange()") + 
  theme_minimal()
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-43-2.png)<!-- -->

``` r
#geom_crossbar()
p + 
  geom_crossbar(aes(xmin = lifeExp_mean - lifeExp_sd, xmax = lifeExp_mean + lifeExp_sd)) +
  labs(x = "Life Expectancy in years, 2007", y = "", title = "geom_crossbar()") + 
  theme_minimal()
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-43-3.png)<!-- -->

``` r
#geom_errorbar()
p + 
  geom_errorbar(aes(xmin = lifeExp_mean - lifeExp_sd, xmax = lifeExp_mean + lifeExp_sd)) +
  labs(x = "Life Expectancy in years, 2007", y = "", title = "geom_errorbar") + 
  theme_minimal()
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-43-4.png)<!-- -->

## Chapter 6 (Work with Models)


``` r
library(broom)
```

```
## Warning: package 'broom' was built under R version 4.3.3
```

``` r
library(survival)
```

```
## Warning: package 'survival' was built under R version 4.3.3
```

``` r
library(margins)
```

```
## Warning: package 'margins' was built under R version 4.3.3
```

``` r
library(survey)
```

```
## Loading required package: grid
```

```
## Loading required package: Matrix
```

```
## 
## Attaching package: 'Matrix'
```

```
## The following objects are masked from 'package:tidyr':
## 
##     expand, pack, unpack
```

```
## 
## Attaching package: 'survey'
```

```
## The following object is masked from 'package:graphics':
## 
##     dotchart
```

``` r
library(coefplot)
library(ggfortify)
```

```
## Registered S3 methods overwritten by 'ggfortify':
##   method         from  
##   autoplot.acf   useful
##   fortify.acf    useful
##   fortify.kmeans useful
##   fortify.ts     useful
```


``` r
#textbook's simple OLS model
out <- lm(formula = lifeExp ~ log(gdpPercap) + pop + continent, data = gapminder)
```


``` r
plot(out, which = c(1,2), ask = FALSE)
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-46-1.png)<!-- -->![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-46-2.png)<!-- -->

The `which()` statement here selects the first two of four default plots for this kind of model. If you want to easily reproduce base R’s default model graphics using ggplot, the ggfortify library is worth examining. It is in some ways similar to broom, in that it tidies the output of model objects, but it focuses on producing a standard plot (or group of plots) for a wide variety of model types. It does this by defining a function called `autoplot()`. The idea is to be able to use `autoplot()` with the output of many different kinds of model.


``` r
# try using ggfortify
df <- iris[c(1, 2, 3, 4)] 
autoplot(prcomp(df))
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-47-1.png)<!-- -->

``` r
autoplot(prcomp(df), data = iris, colour = 'Species', shape = FALSE, label.size = 3)
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-47-2.png)<!-- -->


``` r
out <- lm(formula = lifeExp ~ log(gdpPercap) + log(pop) + continent, data = gapminder)
coefplot(out, sort = "magnitude", intercept = FALSE)
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-48-1.png)<!-- -->


``` r
organdata_sm <- organdata %>%
    select(donors, pop_dens, pubhealth,
           roads, consent_law)

ggpairs(data = organdata_sm,
        mapping = aes(color = consent_law),
        upper = list(continuous = wrap("density"), combo = "box_no_facet"),
        lower = list(continuous = wrap("points"), combo = wrap("dot_no_facet")))
```

```
## Warning: Removed 34 rows containing non-finite outside the scale range
## (`stat_density()`).
```

```
## Warning: Removed 34 rows containing non-finite outside the scale range
## (`stat_density2d()`).
```

```
## Warning: Removed 37 rows containing non-finite outside the scale range
## (`stat_density2d()`).
```

```
## Warning: Removed 34 rows containing non-finite outside the scale range
## (`stat_density2d()`).
```

```
## Warning: Removed 34 rows containing non-finite outside the scale range
## (`stat_boxplot()`).
```

```
## Warning: Removed 34 rows containing missing values or values outside the scale range
## (`geom_point()`).
```

```
## Warning: Removed 17 rows containing non-finite outside the scale range
## (`stat_density()`).
```

```
## Warning: Removed 21 rows containing non-finite outside the scale range
## (`stat_density2d()`).
```

```
## Warning: Removed 17 rows containing non-finite outside the scale range
## (`stat_density2d()`).
```

```
## Warning: Removed 17 rows containing non-finite outside the scale range
## (`stat_boxplot()`).
```

```
## Warning: Removed 37 rows containing missing values or values outside the scale range
## (`geom_point()`).
```

```
## Warning: Removed 21 rows containing missing values or values outside the scale range
## (`geom_point()`).
```

```
## Warning: Removed 21 rows containing non-finite outside the scale range
## (`stat_density()`).
```

```
## Warning: Removed 21 rows containing non-finite outside the scale range
## (`stat_density2d()`).
```

```
## Warning: Removed 21 rows containing non-finite outside the scale range
## (`stat_boxplot()`).
```

```
## Warning: Removed 34 rows containing missing values or values outside the scale range
## (`geom_point()`).
```

```
## Warning: Removed 17 rows containing missing values or values outside the scale range
## (`geom_point()`).
```

```
## Warning: Removed 21 rows containing missing values or values outside the scale range
## (`geom_point()`).
```

```
## Warning: Removed 17 rows containing non-finite outside the scale range
## (`stat_density()`).
```

```
## Warning: Removed 17 rows containing non-finite outside the scale range
## (`stat_boxplot()`).
```

```
## Warning: Removed 34 rows containing missing values or values outside the scale range
## (`geom_point()`).
```

```
## Warning: Removed 17 rows containing missing values or values outside the scale range
## (`geom_point()`).
```

```
## Warning: Removed 21 rows containing missing values or values outside the scale range
## (`geom_point()`).
```

```
## Warning: Removed 17 rows containing missing values or values outside the scale range
## (`geom_point()`).
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-49-1.png)<!-- -->

# Bulut & Dejardins (2019)

## Prework


``` r
# Loading necessary data vis packages
library(GGally)
library(ggExtra)
library(ggalluvial)
library(plotly)
```

```
## 
## Attaching package: 'plotly'
```

```
## The following object is masked from 'package:ggplot2':
## 
##     last_plot
```

```
## The following object is masked from 'package:stats':
## 
##     filter
```

```
## The following object is masked from 'package:graphics':
## 
##     layout
```

``` r
# Loading necessary package for big data preparation 
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
# Loading dataset 
pisa2015 <- fread("pisa2015.csv")
```


``` r
# Defining a function to convert dichotomous items to numerical binary coding
bin_to_num <- function(x){
  if (is.na(x)) NA
  else if (x == "Yes") 1L
  else if (x == "No") 0L 
}

# Recoding some variables 
pisa2015[, `:=`
     (sex = ST004D01T,
       female = ifelse(ST004D01T == "Female", 1, 0),
       computer = sapply(ST011Q04TA, bin_to_num),
       software = sapply(ST011Q05TA, bin_to_num),
       internet = sapply(ST011Q06TA, bin_to_num),
       math = rowMeans(pisa2015[, c(paste0("PV", 1:10, "MATH"))], na.rm = TRUE),
       reading = rowMeans(pisa2015[, c(paste0("PV", 1:10, "READ"))], na.rm = TRUE),
       science = rowMeans(pisa2015[, c(paste0("PV", 1:10, "SCIE"))], na.rm = TRUE)
       )]
       
# Filtering countries of interest 
country <- c("United States", "Canada", "Mexico", "B-S-J-G (China)", "Japan",
             "Korea", "Germany", "Italy", "France", "Brazil", "Colombia", "Uruguay",
             "Australia", "New Zealand", "Jordan", "Israel", "Lebanon")

# Selecting Variables 
data <- pisa2015[CNT %in% country,
             . (CNT, OECD, CNTSTUID, W_FSTUWT, sex, female, ST001D01T, computer, software, internet, 
                ST011Q05TA, ST071Q02NA, ST071Q01NA, ST123Q02NA, ST082Q01NA, ST119Q01NA, ST119Q05NA, 
                ANXTEST, COOPERATE, BELONG,  EMOSUPS, HOMESCH, ENTUSE, ICTHOME, ICTSCH, WEALTH, PARED, 
                TMINS, ESCS, TEACHSUP, TDTEACH, IBTEACH, SCIEEFF, math, reading, science)]

# Further recoding 
data <- data[, `:=` (
  # New grade variable 
  grade = (as.numeric(sapply(ST001D01T, function(x) {
  if(x=="Grade 7") "7"
  else if (x=="Grade 8") "8"
  else if (x=="Grade 9") "9"
  else if (x=="Grade 10") "10"
  else if (x=="Grade 11") "11"
  else if (x=="Grade 12") "12"
  else if (x=="Grade 13") NA_character_
  else if (x=="Ungraded") NA_character_}))),
  # Total learning time as hours
  learning = round(TMINS/60, 0),
  # Regions for selected countries
  Region = (sapply(CNT, function(x) {
  if(x %in% c("Canada", "United States", "Mexico")) "N. America"
    else if (x %in% c("Colombia", "Brazil", "Uruguay")) "S. America"
    else if (x %in% c("Japan", "B-S-J-G (China)", "Korea")) "Asia"
    else if (x %in% c("Germany", "Italy", "France")) "Europe"
    else if (x %in% c("Australia", "New Zealand")) "Australia"
    else if (x %in% c("Israel", "Jordan", "Lebanon")) "Middle-East"
    }))
  )]
```

## Exercise 5.2.1

**1. Create a plot of math scores over countries with different colors based on region. You need to modify the R code below by replacing `geom_boxplot()` with:**


``` r
ggplot(data = data,
       mapping = aes(x = reorder(CNT, math), y = math, fill = Region)) +
  geom_boxplot() +
  labs(x=NULL, y="Math Scores") +
  coord_flip() +
  geom_hline(yintercept = 490, linetype="dashed", color = "red", size = 1) +
  theme_bw()
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-53-1.png)<!-- -->

-   `geom_point(aes(color = Region))`


``` r
# geom_point(aes(color = Region))
ggplot(data, aes(x = reorder(CNT, math), y = math, fill = Region)) +
  geom_point(aes(color = Region)) +
  coord_flip() +
  theme_bw() + 
  labs(x = NULL,
       y = "Math Scores") +
  geom_hline(yintercept = 490, linetype = "dashed", color = "red", size = 1)
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-54-1.png)<!-- -->

-   `geom_violin(aes(color = Region))`


``` r
# geom_violin(aes(color = Region))
ggplot(data, aes(x = reorder(CNT, math), y = math, fill = Region)) +
  geom_violin(aes(color = Region)) +
  coord_flip() +
  theme_bw() + 
  labs(x = NULL,
       y = "Math Scores") + 
  geom_hline(yintercept = 490, linetype = "dashed", color = "red", size = 1)
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-55-1.png)<!-- -->

**How long did it take to create both plots? Which one is a better way to visualize this type of data?**

Each plot took about 3 seconds to generate. I think the violin plot is more suitable for this type of data because it provides information about the spread and distribution. In contrast, the point plot generated by `geom_point()` is less informative, as it primarily highlights potential outliers and suffers from overlapping points.

We also had different opinions about if violin plot or the boxplot is better since each of them provides a different perspective to understand the data distribution.

In addition, on one person's computer, `geom_point()` takes longer, and we think it is because it plots each single data points instead of the summary statistics.

**2. We can also create histograms (or density plots) for a particular variable and split the plot into multiple plots by using a categorical, group variable. In the following example, we use `x = Region` in the mapping in order to identify different regions in the distribution of the science scores. In addition, we use `facet_grid(. ~ sex)` to generate separate histograms by gender. Note that we also added `title = "Science Scores by Gender and Region"` as a title in the `labs` function.**

We can see that the fill parameter acts directly as a grouping variable here, allowing us to see the distribution of science scores within each region and each facet. The plot shows a histogram of science scores by region, with different colors representing each region. The plot is faceted by sex, providing a side-by-side comparison of the distribution for each gender.


``` r
# Histogram of science scores by region faceted by sex 
ggplot(data, aes(x = science, fill = Region)) +
  geom_histogram(alpha = 0.5, bins = 50) +
  labs(x = "Science Scores", 
       y = "Count",
       title = "Science Scores by Gender and Region") + 
  facet_grid(. ~ sex) +
  theme_bw()
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-56-1.png)<!-- -->

**3. If we are interested in visualizing multiple variables, plotting each variable individually can be time consuming. Therefore, we can use the `ggpairs` function from the `GGally` package to build a more complex, diagnostic plot for multiple variables.**

From the example below, we can see that `ggpairs` generates different types of visualizations based on the data type. General patterns observed include:

-   Scatterplots for pairs of continuous variables.
-   Boxplots for categorical vs. continuous variables in the upper triangle, and histograms for categorical vs. continuous variables in the lower triangle.

In the diagonal of the matrix:

-   Density plots are generated for continuous variables.
-   Stacked bar charts are generated for categorical variables.

Additionally, we can customize what to display in the upper and lower triangles to avoid duplicating information. For example, here we display correlation coefficients among continuous variables in the upper triangle to complement the scatterplots.

I’m curious about what plot would be produced for two different categorical variables. From a quick search, it appears that it might be a mosaic plot.


``` r
# Random sample of 500 students from each region 
data_small <- data[,.SD[sample(.N, min(500,.N))], by = Region]

# Using ggpairs to create multiple visualizations simultaniously 
ggpairs(data_small, 
        aes(color = Region),
        columns = c("reading", "science", "math", "sex"),
        upper = list(continuous = wrap("cor", size = 2.5)))
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-57-1.png)<!-- -->

**4. Interpretation**

-   **What can we say about the regions based on the plots above?**

From the density plots, we can see that the distribution of scores in reading, science, and math varies by region. Asian countries tend to have distributions centered around higher scores, while South American countries exhibit larger peaks, indicating a greater concentration around specific score ranges.

-   **Do you see any major gender differences for reading, science, or math?**

The boxplots indicate that males and females have similar distributions across all three subjects. Though there may be slight variations between the distributions for male and female, no major gender differences are apparent in terms of central tendencies (medians) or spread (IQR). Without any statistical tests, we cannot be sure whether those slight variations entail any significant differences.

-   **What is the relationship among reading, science, or math?**

Based on the scatter plots, we can see that the correlations between each pair of the three variables are positive. The correlation coefficients in the upper triangle confirm that higher scores in one of the three subjects are associated with higher scores in the other two subjects. This relationship holds across all regions and globally.

## Exercise 5.3.1

**1. Interpretations**


``` r
p1 <- ggplot(data = data_small,
             mapping = aes(x = learning, y = science)) +
  geom_point() +
  geom_smooth(method = "loess") +
  labs(x = "Weekly Learning Time", y = "Science Scores") +
  theme_bw()

# Replace "histogram" with "boxplot" or "density" for other types
ggMarginal(p1, type = "histogram")
```

```
## `geom_smooth()` using formula = 'y ~ x'
```

```
## Warning: Removed 758 rows containing non-finite outside the scale range
## (`stat_smooth()`).
```

```
## `geom_smooth()` using formula = 'y ~ x'
```

```
## Warning: Removed 758 rows containing non-finite outside the scale range
## (`stat_smooth()`).
```

```
## Warning: Removed 758 rows containing missing values or values outside the scale range
## (`geom_point()`).
```

``` r
p1 <- ggplot(data = data_small,
             mapping = aes(x = learning, y = science)) +
  geom_point() +
  geom_smooth(method = "loess") +
  labs(x = "Weekly Learning Time", y = "Science Scores") +
  theme_bw()

# Replace "histogram" with "boxplot" or "density" for other types
ggMarginal(p1, type = "boxplot")
```

```
## `geom_smooth()` using formula = 'y ~ x'
```

```
## Warning: Removed 758 rows containing non-finite outside the scale range
## (`stat_smooth()`).
```

```
## `geom_smooth()` using formula = 'y ~ x'
```

```
## Warning: Removed 758 rows containing non-finite outside the scale range
## (`stat_smooth()`).
## Removed 758 rows containing missing values or values outside the scale range
## (`geom_point()`).
```

``` r
p1 <- ggplot(data = data_small,
             mapping = aes(x = learning, y = science)) +
  geom_point() +
  geom_smooth(method = "loess") +
  labs(x = "Weekly Learning Time", y = "Science Scores") +
  theme_bw()

# Replace "histogram" with "boxplot" or "density" for other types
ggMarginal(p1, type = "density")
```

```
## `geom_smooth()` using formula = 'y ~ x'
```

```
## Warning: Removed 758 rows containing non-finite outside the scale range
## (`stat_smooth()`).
```

```
## `geom_smooth()` using formula = 'y ~ x'
```

```
## Warning: Removed 758 rows containing non-finite outside the scale range
## (`stat_smooth()`).
## Removed 758 rows containing missing values or values outside the scale range
## (`geom_point()`).
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-58-1.png)<!-- -->


``` r
p2 <- ggplot(data = data_small,
             mapping = aes(x = learning, y = science,
                           colour = sex)) +
  geom_point() +
  geom_smooth(method = "loess") +
  labs(x = "Weekly Learning Time", y = "Science Scores") +
  theme_bw() +
  theme(legend.position = "bottom",
        legend.title = element_blank())

ggMarginal(p2, type = "density", groupColour = TRUE, groupFill = TRUE)
```

```
## `geom_smooth()` using formula = 'y ~ x'
```

```
## Warning: Removed 758 rows containing non-finite outside the scale range
## (`stat_smooth()`).
```

```
## `geom_smooth()` using formula = 'y ~ x'
```

```
## Warning: Removed 758 rows containing non-finite outside the scale range
## (`stat_smooth()`).
```

```
## Warning: Removed 758 rows containing missing values or values outside the scale range
## (`geom_point()`).
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-59-1.png)<!-- -->

-   What can we say about the relationship between weekly learning time and science scores?

It seems that learning time reaches a point of diminishing returns around 27 hours. At this point, science scores reach their highest point, about 525. After this point, they decrease.

-   Do you see any gender differences?

After 8 hours of weekly learning time, boys have higher test scores at each level of weekly learning time.

**2. Create a scatterplot of socio-economic status (`ESCS`) and math scores (`math`) across regions (`region`) and gender (`sex`). Use `geom_smooth(method = "lm")` to draw linear regression lines (instead of loess smoothing). Do you think that the relationship between ESCS and math changes across gender and regions?**


``` r
ggplot(data_small, aes(x = ESCS, y = math)) +
  geom_point() +
  geom_smooth(method = "lm") +
  facet_grid(sex ~ Region) +
  theme_minimal() + 
  labs(x = "Socio-Economic Status", 
       y = "Math Scores")
```

```
## `geom_smooth()` using formula = 'y ~ x'
```

```
## Warning: Removed 95 rows containing non-finite outside the scale range
## (`stat_smooth()`).
```

```
## Warning: Removed 95 rows containing missing values or values outside the scale range
## (`geom_point()`).
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-60-1.png)<!-- -->

From the plot, we can see that across all regions and both genders, there is a positive relationship between socio-economic status and math scores. This means that as socio-economic status increases, math scores tend to increase as well.

I think the plot shows similar positive slopes for makes and females across all regions, which suggest that the relationship between ESCS and math scores do not change significantly by sex.

Compared to other regions the slopes in North America and Europe across sexes appear slightly steeper, indicating a stronger association between ESCS and math scores. The wider standard error shading area in Asia and Middle East suggest that there is greater variability in the relationship between socio-economic status (ESCS) and math scores in these regions.

Therefore, while the impact of ESCS on math scores does not appear to vary significantly by gender, there seems to be some regional differences in the strength of this relationship.

During the group discussion, we tlaked about the difficulty of slope comparision and interpretation using visualizations, though they provide rich information.

## Exercise 5.6.1

**1. Create an alluvial plot for the survey item (`ST119Q01NA`) of whether students want top grades in most or all courses by region (`Region`) and gender (`sex`). Below we create the summary dataset (`dat_alluvial2`) for this plot. Use this dataset to draw the alluvial plot. How should we interpret the plot (e.g., for each region)?**


``` r
# Preparing dataset 
data_alluvial <- data[,
                      .(Freq = .N),
                      by = c("Region", "sex", "ST119Q01NA")
                     ][,
                       ST119Q01NA := as.factor(ifelse(ST119Q01NA == "", "Missing", ST119Q01NA))]

levels(data_alluvial$ST119Q01NA) <- c("Strongly disagree", "Disagree", "Agree",
                                      "Strongly agree", "Missing")

data_alluvial
```

```
##          Region    sex        ST119Q01NA  Freq
##          <char> <char>            <fctr> <int>
##  1:   Australia Female Strongly disagree  4116
##  2:   Australia Female    Strongly agree  4108
##  3:   Australia   Male Strongly disagree  4427
##  4:   Australia Female          Disagree   776
##  5:   Australia   Male    Strongly agree  3561
##  6:   Australia   Male           Missing   199
##  7:   Australia Female             Agree   312
##  8:   Australia   Male             Agree   485
##  9:   Australia   Male          Disagree   958
## 10:   Australia Female           Missing   108
## 11:  S. America   Male             Agree  1668
## 12:  S. America Female Strongly disagree  7844
## 13:  S. America Female             Agree  1530
## 14:  S. America   Male    Strongly agree  9303
## 15:  S. America   Male          Disagree   856
## 16:  S. America Female    Strongly agree 11037
## 17:  S. America   Male Strongly disagree  7495
## 18:  S. America Female          Disagree   775
## 19:  S. America   Male           Missing   291
## 20:  S. America Female           Missing   199
## 21:  N. America Female    Strongly agree  9242
## 22:  N. America Female Strongly disagree  5961
## 23:  N. America Female          Disagree   938
## 24:  N. America   Male    Strongly agree  7526
## 25:  N. America   Male Strongly disagree  6953
## 26:  N. America   Male          Disagree  1281
## 27:  N. America   Male             Agree   577
## 28:  N. America   Male           Missing   322
## 29:  N. America Female           Missing   165
## 30:  N. America Female             Agree   373
## 31:      Europe Female    Strongly agree  3798
## 32:      Europe Female Strongly disagree  5674
## 33:      Europe Female          Disagree  1728
## 34:      Europe   Male Strongly disagree  5580
## 35:      Europe   Male             Agree   856
## 36:      Europe   Male           Missing   390
## 37:      Europe   Male    Strongly agree  3576
## 38:      Europe   Male          Disagree  1693
## 39:      Europe Female             Agree   562
## 40:      Europe Female           Missing   338
## 41: Middle-East Female    Strongly agree  2916
## 42: Middle-East Female Strongly disagree   613
## 43: Middle-East   Male    Strongly agree  2094
## 44: Middle-East   Male Strongly disagree   578
## 45: Middle-East   Male           Missing    93
## 46: Middle-East Female           Missing    37
## 47: Middle-East Female             Agree  6246
## 48: Middle-East Female          Disagree    43
## 49: Middle-East   Male             Agree  5720
## 50: Middle-East   Male          Disagree    71
## 51:        Asia Female Strongly disagree  4560
## 52:        Asia   Male Strongly disagree  4654
## 53:        Asia Female          Disagree  2471
## 54:        Asia   Male    Strongly agree  3967
## 55:        Asia Female    Strongly agree  3253
## 56:        Asia   Male          Disagree  2203
## 57:        Asia Female           Missing   327
## 58:        Asia   Male           Missing   503
## 59:        Asia   Male             Agree    82
## 60:        Asia Female             Agree    49
##          Region    sex        ST119Q01NA  Freq
```

Labeling the strata has failed, making it difficult to identify each category in the alluvial plot. The example provided in the book also lacked labels, and I assume the warning I got here is related to the stratum labeling issue. Despite troubleshooting attempts, I couldn’t find a solution.

Interpreting the patterns without labels is challenging, but based on the produced alluvial plot, it appears that responses vary across regions. The proportion of data flowing to each answer category differs by region. In some regions, only a small proportion of the students selected a certain answer, while in other regions, half of the students chose that answer.

Sex appears to have a smaller impact, as the widths of the alluvia are similar.


``` r
# Alluvial
ggplot(data_alluvial, aes(axis1 = Region, axis2 = ST119Q01NA, y = Freq)) +
  scale_x_discrete(limits = c("Region", "Students wanting top grades \n in most or all courses"),
                   expand = c(.1, .05)) +
  geom_alluvium(aes(fill = sex)) +
  geom_stratum() +
  geom_text(stat = "stratum", label.strata = TRUE) +
  labs(x = "Demographics", y = "Frequency", fill = "Gender") +
  theme_bw()
```

```
## Warning: Computation failed in `stat_stratum()`.
## Caused by error:
## ! The parameter `label.strata` is defunct.
## use `aes(label = after_stat(stratum))`.
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-62-1.png)<!-- -->

## Exercise 5.7.1

**1. Replicate the science-by-region histogram below as a density plot and use `plotly` to make it interactive. You will need to replace `geom_histogram(alpha = 0.5, bins = 50)` with `geom_density(alpha = 0.5)`. Repeat the same process by changing `alpha = 0.5` to `alpha = 0.8`. Which version is better for examining the science score distribution?**

Between the two geom functions, I prefer `geom_density()` because it allows us to view the actual density distribution without being influenced by the number of `bins`. Additionally, adjusting the `alpha` parameter helps reduce the impact of overlapping and enhances the visibility of each region’s distribution. By setting different alpha levels, we can fine-tune the transparency and make all distributions clearer.

Between the two versions of plots with different `alpha` values, I prefer the one with `alpha = 0.5` because the increased transparency makes all distributions clearer. In fact, I think the `alpha` value could be set even lower to further enhance visibility.


``` r
# Original plot with geom_histogram(alpha = 0.5, bins = 50)
ggplot(data = data,
       mapping = aes(x = science, fill = Region)) +
  geom_histogram(alpha = 0.5, bins = 50) +
  labs(x = "Science Scores", y = "Count",
       title = "Science Scores by Gender and Region") +
  facet_grid(. ~ sex) +
  theme_bw()
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-63-1.png)<!-- -->

``` r
# geom_density(alpha = 0.5)
ggplot(data = data,
       mapping = aes(x = science, fill = Region)) +
  geom_density(alpha = 0.5) +
  labs(x = "Science Scores", y = "Density",
       title = "Science Scores by Gender and Region") +
  facet_grid(. ~ sex) +
  theme_bw()
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-63-2.png)<!-- -->

``` r
# geom_density(alpha = 0.8)
ggplot(data = data,
       mapping = aes(x = science, fill = Region)) +
  geom_density(alpha = 0.8) +
  labs(x = "Science Scores", y = "Density",
       title = "Science Scores by Gender and Region") +
  facet_grid(. ~ sex) +
  theme_bw()
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-63-3.png)<!-- -->

Given the discussion above,I decided to use `geom_density(alpha = 0.3)` for the interactive density plot.


``` r
# Creating a static plot using geom_density(alpha = 0.3) 
p5.7.1 <- ggplot(data = data,
                 mapping = aes(x = science, fill = Region)) +
  geom_density(alpha = 0.3) +
  labs(x = "Science Scores", y = "Density",
       title = "Science Scores by Gender and Region") +
  facet_grid(~ sex) +
  theme_bw()

# Converting to interactive plot 
ggplotly(p5.7.1)
```

```{=html}
<div class="plotly html-widget html-fill-item" id="htmlwidget-03a1f6bb15dd4ca14a27" style="width:672px;height:480px;"></div>
```

## 5.9 Lab

**We want to examine the relationships between reading scores and technology-related variables in the dat dataset that we created earlier. Create at least two visualizations (either static or interactive) using some of the variables shown below:**

-   Region
-   sex
-   grade
-   HOMESCH
-   ENTUSE
-   ICTHOME
-   ICTSCH

**You can focus on a particular country or region or use the entire dataset for your visualizations.**

Before creating any visualization, I would like to see what variables have correlations with reading based on Pearson's coefficients.


``` r
# Creating correlation matrix 
ggcorr(data = data[,.(reading, Region, sex, grade, HOMESCH, ENTUSE, ICTHOME, ICTSCH)],
       method = c("pairwise.complete.obs", "pearson"),
       label = TRUE, label_size = 4)
```

```
## Warning in ggcorr(data = data[, .(reading, Region, sex, grade, HOMESCH, : data
## in column(s) 'Region', 'sex' are not numeric and were ignored
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-65-1.png)<!-- -->

1.  As the correlation matrix shows, there is a negative relationship between `HOMESCH` (ICT use outside of school for schoolwork) and `reading`, and I would like to see if this relationship varies by region and sex.

From the visualization, we can see that the relationship varies by region and sex: Asia shows a positive association for both sexes, particularly stronger for females, while Europe and the Middle-East exhibit slight negative trends for both. In Australia, North America, and South America, the association between ICT use and reading scores is minimal, with a slightly stronger correlation for females.


``` r
# A scatterplot with lm fitting lines 
ggplot(data, aes(x = HOMESCH, y = reading)) + 
  geom_point(color = "grey", alpha = 0.2) + 
  geom_smooth(method = "lm", se = TRUE, aes(color = sex)) + 
  facet_wrap(~ Region) +
  labs(x = "ICT Use Outside of School for Schoolwork", 
       y = "Reading Scores",
       title = "Sex Difference Appears Starting Grade 11") +
  scale_color_manual(values = c("#D81B60", "#1E88E5")) + 
  theme_minimal()
```

```
## `geom_smooth()` using formula = 'y ~ x'
```

```
## Warning: Removed 60013 rows containing non-finite outside the scale range
## (`stat_smooth()`).
```

```
## Warning: Removed 60013 rows containing missing values or values outside the scale range
## (`geom_point()`).
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-66-1.png)<!-- -->

2.  According to Pearson's coefficient, there is a positive relationship between `ICTHOME` (ICT available at home, discrete integer, number of devices) and `reading`, and I would like to 1) explore whether this relationship holds true for both sexes and 2) determine if the correlation patterns differ between sexes.

From the visualization, we see a clear positive association between the number of ICT devices at home and the mean reading score. As the number of devices increases, the mean reading score also tends to be higher, suggesting that more ICT resources at home may be associated with improved reading performance. This relationship holds true for both sexes, indicating that ICT access impacts reading scores similarly for males and females. However, females consistently have higher reading scores than males across all levels of ICT availability, and as ICT availability increases, the gap between male and female reading scores appears to widen slightly.


``` r
# Preparing summary stats for vis
data_5.9.2 <- data |>
  filter(!is.na(grade) & !is.na(ICTHOME)) |>
  mutate(ICTHOME = as.factor(ICTHOME)) |>
  group_by(sex, ICTHOME) |>
  summarize(mean_reading = mean(reading), 
            sd_reading = sd(reading), 
            N = n())
```

```
## `summarise()` has grouped output by 'sex'. You can override using the `.groups`
## argument.
```

``` r
# Creating bubble chart
p5.9.2 <- ggplot(data_5.9.2, aes(x = mean_reading, y = ICTHOME, fill = sex, size = N)) +
  geom_point(shape = 21) +
  scale_fill_manual(values = c("#D81B60", "#1E88E5")) +
  theme_minimal() +
  labs(x = "Mean Reading Score",
       y = "Number of ICT Available at Home", 
       size = "Observations",
       fill = "Sex")

# Previewing the plot 
p5.9.2
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-67-1.png)<!-- -->

An interactive version of the bubble chart was also created.


``` r
# Converting to a interactive plot 
ggplotly(p5.9.2)
```

```{=html}
<div class="plotly html-widget html-fill-item" id="htmlwidget-43182d21d15a898cc1a5" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-43182d21d15a898cc1a5">{"x":{"data":[{"x":[358.4549290794979,391.47967477876108,409.9625216848674,428.06662118822294,447.52474941763722,467.42298842321219,485.70783375080981,500.85937895260196,511.8721319078071,514.73934006660693,513.58391823320414,473.41981438154011],"y":[1,2,3,4,5,6,7,8,9,10,11,12],"text":["mean_reading: 358.4549<br />ICTHOME: 0<br />sex: Female<br />N:  478","mean_reading: 391.4797<br />ICTHOME: 1<br />sex: Female<br />N:  678","mean_reading: 409.9625<br />ICTHOME: 2<br />sex: Female<br />N: 1282","mean_reading: 428.0666<br />ICTHOME: 3<br />sex: Female<br />N: 1902","mean_reading: 447.5247<br />ICTHOME: 4<br />sex: Female<br />N: 2404","mean_reading: 467.4230<br />ICTHOME: 5<br />sex: Female<br />N: 3412","mean_reading: 485.7078<br />ICTHOME: 6<br />sex: Female<br />N: 4631","mean_reading: 500.8594<br />ICTHOME: 7<br />sex: Female<br />N: 6034","mean_reading: 511.8721<br />ICTHOME: 8<br />sex: Female<br />N: 7506","mean_reading: 514.7393<br />ICTHOME: 9<br />sex: Female<br />N: 7807","mean_reading: 513.5839<br />ICTHOME: 10<br />sex: Female<br />N: 6192","mean_reading: 473.4198<br />ICTHOME: 11<br />sex: Female<br />N: 3727"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(216,27,96,1)","opacity":1,"size":[3.7795275590551185,6.8923855817789033,10.020788487740607,12.085668762755374,13.439423687926952,15.702220793872394,17.964394970172783,20.186386101581878,22.232239049429726,22.623249016792837,20.418037601709052,16.325928754467053],"symbol":"circle","line":{"width":1.8897637795275593,"color":"rgba(0,0,0,1)"}},"hoveron":"points","name":"Female","legendgroup":"Female","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[353.01197384305834,379.72097348927872,396.26758230184583,417.38932765042983,435.28227843511451,455.24212777976408,470.01630786953143,485.41313269483567,489.47507579617832,494.77877688877567,485.98333186893916,437.69623018731988],"y":[1,2,3,4,5,6,7,8,9,10,11,12],"text":["mean_reading: 353.0120<br />ICTHOME: 0<br />sex: Male<br />N:  497","mean_reading: 379.7210<br />ICTHOME: 1<br />sex: Male<br />N:  513","mean_reading: 396.2676<br />ICTHOME: 2<br />sex: Male<br />N:  921","mean_reading: 417.3893<br />ICTHOME: 3<br />sex: Male<br />N: 1396","mean_reading: 435.2823<br />ICTHOME: 4<br />sex: Male<br />N: 2096","mean_reading: 455.2421<br />ICTHOME: 5<br />sex: Male<br />N: 2797","mean_reading: 470.0163<br />ICTHOME: 6<br />sex: Male<br />N: 3863","mean_reading: 485.4131<br />ICTHOME: 7<br />sex: Male<br />N: 5325","mean_reading: 489.4751<br />ICTHOME: 8<br />sex: Male<br />N: 6908","mean_reading: 494.7788<br />ICTHOME: 9<br />sex: Male<br />N: 7849","mean_reading: 485.9833<br />ICTHOME: 10<br />sex: Male<br />N: 6806","mean_reading: 437.6962<br />ICTHOME: 11<br />sex: Male<br />N: 5552"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(30,136,229,1)","opacity":1,"size":[4.7389748382817594,5.0817294969995643,8.412355575423021,10.448603392304314,12.633406382110801,14.379254790565723,16.585826977840643,19.103833894502227,21.429734498034385,22.677165354330711,21.289180999465572,19.458570393892355],"symbol":"circle","line":{"width":1.8897637795275593,"color":"rgba(0,0,0,1)"}},"hoveron":"points","name":"Male","legendgroup":"Male","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null}],"layout":{"margin":{"t":26.228310502283108,"r":7.3059360730593621,"b":40.182648401826498,"l":37.260273972602747},"font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724},"xaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[344.92560553188093,522.82570837778439],"tickmode":"array","ticktext":["350","400","450","500"],"tickvals":[350,400,450,500],"categoryorder":"array","categoryarray":["350","400","450","500"],"nticks":null,"ticks":"","tickcolor":null,"ticklen":3.6529680365296811,"tickwidth":0,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.68949771689498},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(235,235,235,1)","gridwidth":0.66417600664176002,"zeroline":false,"anchor":"y","title":{"text":"Mean Reading Score","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}},"hoverformat":".2f"},"yaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[0.40000000000000002,12.6],"tickmode":"array","ticktext":["0","1","2","3","4","5","6","7","8","9","10","11"],"tickvals":[1,2,3,4.0000000000000009,5,6,7.0000000000000009,8,9,10,11,12],"categoryorder":"array","categoryarray":["0","1","2","3","4","5","6","7","8","9","10","11"],"nticks":null,"ticks":"","tickcolor":null,"ticklen":3.6529680365296811,"tickwidth":0,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.68949771689498},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(235,235,235,1)","gridwidth":0.66417600664176002,"zeroline":false,"anchor":"x","title":{"text":"Number of ICT Available at Home","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}},"hoverformat":".2f"},"shapes":[{"type":"rect","fillcolor":null,"line":{"color":null,"width":0,"linetype":[]},"yref":"paper","xref":"paper","x0":0,"x1":1,"y0":0,"y1":1}],"showlegend":true,"legend":{"bgcolor":null,"bordercolor":null,"borderwidth":0,"font":{"color":"rgba(0,0,0,1)","family":"","size":11.68949771689498},"title":{"text":"Observations<br />Sex","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}}},"hovermode":"closest","barmode":"relative"},"config":{"doubleClick":"reset","modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"source":"A","attrs":{"c40b466c698f":{"x":{},"y":{},"fill":{},"size":{},"type":"scatter"}},"cur_data":"c40b466c698f","visdat":{"c40b466c698f":["function (y) ","x"]},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.20000000000000001,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>
```

# Tidy Tuesday

For the Tidy Tuesday challenge, we used the dataset from [The UK Office of National Statistics](https://www.ons.gov.uk/). In the paper titled ["Why do children and young people in smaller towns do better academically than those in larger towns?"](https://www.ons.gov.uk/peoplepopulationandcommunity/educationandchildcare/articles/whydochildrenandyoungpeopleinsmallertownsdobetteracademicallythanthoseinlargertowns/2023-07-25), they used the dataset to explore the relationship between education attainment and variables, and most of their visualizations are very compelling.

We decided to improve (or maybe just extend) the descriptive plot they created for the distribution of education attainments in differnt types of towns and cities.


``` r
# Loading data 
tuesdata <- tidytuesdayR::tt_load('2024-01-23')
```

```
## ---- Compiling #TidyTuesday Information for 2024-01-23 ----
## --- There is 1 file available ---
## 
## 
## ── Downloading files ───────────────────────────────────────────────────────────
## 
##   1 of 1: "english_education.csv"
```

``` r
data <- tuesdata$english_education
```


``` r
# Loading packages 
library(ggdist)
```


``` r
# We filter the data to only recoding the values 
data_cleaned <- data |>
  mutate(city_town_mapping = case_when(
    size_flag == "Small Towns" ~ "Small Towns",
    size_flag == "Medium Towns" ~ "Medium Towns",
    size_flag == "Large Towns" ~ "Large Towns",
    size_flag == "City" ~ "City",
    size_flag == "Inner London BUA" ~ "City",
    size_flag == "Outer london BUA" ~ "City",
    size_flag == "Not BUA" ~ "City",
    size_flag == "Other Small BUAs" ~ "City"
  )) |>
  mutate(city_town_mapping = factor(city_town_mapping,
                                    levels = c("Small Towns", "Medium Towns", "Large Towns", "City")))
```

For the visualization below, we referred to [this tutorial](https://www.cedricscherer.com/2021/06/06/visualizing-distributions-with-raincloud-plots-and-how-to-create-them-with-ggplot2/).


``` r
ggplot(data_cleaned, aes(x = education_score, y = city_town_mapping)) + 
  
  stat_halfeye(
    aes(fill = city_town_mapping),
    color = "transparent",
    adjust = 0.5,
    width = 0.6, 
    justification = -0.2,
    .width = c(0.05, 0.95),
    
  ) +
 
  geom_boxplot(
    aes(color = city_town_mapping),
    width = 0.3,
  ) +
  geom_point(alpha = 0.2, 
             aes(color = city_town_mapping),
             position = position_jitter(seed = 0.5, width = 0.5, height = 0.1)) +
  
  scale_color_manual(values = c("#1E88E5", "#D81B60", "#FFC107", "#004D40")) +
  scale_fill_manual(values = c("#1E88E5", "#D81B60", "#FFC107", "#004D40")) +
  
  theme_minimal() + 
  labs(x = "Education Attainment Score",
       y = "Town Size", 
       title = "Range in Educational Attainment Increases as City Size Decreases",
       subtitle = "Reporting the Mean is Not the Whole Story") +
  theme(legend.position = "none") 
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-72-1.png)<!-- -->

Here is another version of plot we made, but it is not polished.


``` r
towns <- data |>
  filter(! size_flag %in% c("Not BUA", "Other Small BUAs")) |>
  
  mutate(city_town_mapping = case_when(
    size_flag == "Small Towns" ~ "Small Towns",
    size_flag == "Medium Towns" ~ "Medium Towns",
    size_flag == "Large Towns" ~ "Large Towns",
    size_flag == "City" ~ "City (excluding London)",
    size_flag == "Inner London BUA" ~ "London",
    size_flag == "Outer london BUA" ~ "London"
  )) |> 
  mutate(city_town_mapping = factor(city_town_mapping, 
                                    levels = c("London", "City (excluding London)", "Large Towns", "Medium Towns", "Small Towns")))

ggplot(towns, aes(x = education_score, y = city_town_mapping)) +
  
  geom_jitter(data = towns |> filter(city_town_mapping %in% c("City (excluding London)"))) +
  
  geom_point(data = towns |> filter(city_town_mapping == "London"), aes(color = size_flag)) +
  
  geom_text_repel(data = subset(towns, city_town_mapping == "London"),
                  mapping = aes(label = size_flag, color = size_flag)) +
  
  geom_violin(data = towns |> filter(city_town_mapping %in% c("Small Towns", "Medium Towns", "Large Towns")), fill = "skyblue", alpha = 0.5) +

  scale_color_manual(values = c("#1E88E5", "#D81B60")) +
  
  stat_summary(data = towns |> filter(city_town_mapping %in% c("Small Towns", "Medium Towns", "Large Towns")),
               fun.y = mean, geom = "point", color = "#FFC107", shape = 18, size = 3) + 
  
  theme_minimal() + 
  
  theme(legend.position = "none") +
  
  labs(x = "Education Score",
       y = "City / Town Type")
```

```
## Warning: The `fun.y` argument of `stat_summary()` is deprecated as of ggplot2 3.3.0.
## ℹ Please use the `fun` argument instead.
## This warning is displayed once every 8 hours.
## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
## generated.
```

![](ORLA6541_Exercise2_files/figure-html/unnamed-chunk-73-1.png)<!-- -->