Long and Wide Data
================
David Rommel
2025-06-26

## Load Libraries

``` r
library("tidyverse")
```

## Create Data Set

**Wide data** has fewer rows and more columns, with each column
representing a variable.  
<br> **Long data** has more rows and fewer columns, with each row
representing a single observation and a combination of variables.  
<br> The following code creates a data frame containing data in a
**wide** format.

``` r
df <- data.frame(team = c("A","B","C","D"), 
                 points = c(88L,91L,99L,94L), 
                 assists = c(12L,17L,24L,28L), 
                 rebounds = c(22L,28L,30L,31L))
```

    ##   team points assists rebounds
    ## 1    A     88      12       22
    ## 2    B     91      17       28
    ## 3    C     99      24       30
    ## 4    D     94      28       31

## Convert Data From Wide To Long Format

To convert data from a wide format to a long format, use the
`pivot_longer()` function. This code combines the **points**,
**assists**, and **rebounds** column names into the **variable** column.
The observations, corresponding to those three columns, are all placed
into the **value** column.

``` r
df_tall <- df %>% pivot_longer( cols = c( "points", "assists", "rebounds"), 
                                names_to = "variable", 
                                values_to = "value")
```

    ## # A tibble: 12 × 3
    ##    team  variable value
    ##    <chr> <chr>    <int>
    ##  1 A     points      88
    ##  2 A     assists     12
    ##  3 A     rebounds    22
    ##  4 B     points      91
    ##  5 B     assists     17
    ##  6 B     rebounds    28
    ##  7 C     points      99
    ##  8 C     assists     24
    ##  9 C     rebounds    30
    ## 10 D     points      94
    ## 11 D     assists     28
    ## 12 D     rebounds    31

## Convert Data from Long to Wide Format

To convert data from a long format to a wide format, use the
`pivot_wider()` function. This code creates new columns **points,
assists, rebounds** from the values stored in the variable column. The
values in the value column are placed in their respectable cells.

``` r
df_wide <- df_tall %>% pivot_wider(names_from = "variable", 
                                   values_from = "value")
```

    ## # A tibble: 4 × 4
    ##   team  points assists rebounds
    ##   <chr>  <int>   <int>    <int>
    ## 1 A         88      12       22
    ## 2 B         91      17       28
    ## 3 C         99      24       30
    ## 4 D         94      28       31

## Alternative Method

An older method of performing this operation was by using the `gather()`
in place of the `pivot_long()` function, and the `spread()` function in
place of the `pivot_wider()` function. These functions operate almost
identically to the modern counterparts.

``` r
df_tall <- gather(df, 
                  "points", "assists", "rebounds",
                  key = "variable",
                  value = "value") %>% arrange(team)

df_wide <- spread(df_tall,
                  key = "variable",
                  value = "value")
```
