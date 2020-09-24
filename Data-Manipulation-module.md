Data Wrangling
================

``` r
library(tidyverse)
```

    ## -- Attaching packages ------------------------------ tidyverse 1.3.0 --

    ## v ggplot2 3.3.2     v purrr   0.3.4
    ## v tibble  3.0.3     v dplyr   1.0.2
    ## v tidyr   1.1.2     v stringr 1.4.0
    ## v readr   1.3.1     v forcats 0.5.0

    ## -- Conflicts --------------------------------- tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

## Module 2

## Load in the FAS litters data, and clean/rename

``` r
litters_df = read_csv("./data/FAS_litters.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   Group = col_character(),
    ##   `Litter Number` = col_character(),
    ##   `GD0 weight` = col_double(),
    ##   `GD18 weight` = col_double(),
    ##   `GD of Birth` = col_double(),
    ##   `Pups born alive` = col_double(),
    ##   `Pups dead @ birth` = col_double(),
    ##   `Pups survive` = col_double()
    ## )

``` r
litters_df = janitor::clean_names(litters_df)
```

Loading,cleaning and categorizing pups data frame, output 3 rows only :

``` r
options(tibble.print_min = 3)
pups_df = read_csv("./data/FAS_pups.csv",
  col_types = "ciiiii")
pups_df = janitor::clean_names(pups_df)
```

## looking at Dplyr functions

## function “Select”

Choose some columns and not others.

1.  Specify columns to keep:

<!-- end list -->

``` r
select(litters_df, group, litter_number, gd0_weight, pups_born_alive)
```

    ## # A tibble: 49 x 4
    ##   group litter_number gd0_weight pups_born_alive
    ##   <chr> <chr>              <dbl>           <dbl>
    ## 1 Con7  #85                 19.7               3
    ## 2 Con7  #1/2/95/2           27                 8
    ## 3 Con7  #5/5/3/83/3-3       26                 6
    ## # ... with 46 more rows

2.  Specify range of columns

<!-- end list -->

``` r
select(litters_df, group:gd_of_birth)
```

    ## # A tibble: 49 x 5
    ##   group litter_number gd0_weight gd18_weight gd_of_birth
    ##   <chr> <chr>              <dbl>       <dbl>       <dbl>
    ## 1 Con7  #85                 19.7        34.7          20
    ## 2 Con7  #1/2/95/2           27          42            19
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19
    ## # ... with 46 more rows

3.  Select by specifying columns to remove

<!-- end list -->

``` r
select(litters_df, -pups_survive)
```

    ## # A tibble: 49 x 7
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ## 1 Con7  #85                 19.7        34.7          20               3
    ## 2 Con7  #1/2/95/2           27          42            19               8
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19               6
    ## # ... with 46 more rows, and 1 more variable: pups_dead_birth <dbl>

4.  Renaming columns …

<!-- end list -->

``` r
select(litters_df, GROUP = group, LITTer_NumBer = litter_number)
```

    ## # A tibble: 49 x 2
    ##   GROUP LITTer_NumBer
    ##   <chr> <chr>        
    ## 1 Con7  #85          
    ## 2 Con7  #1/2/95/2    
    ## 3 Con7  #5/5/3/83/3-3
    ## # ... with 46 more rows

or

``` r
rename(litters_df, GROUP = group, LITTer_NumBer = litter_number)
```

    ## # A tibble: 49 x 8
    ##   GROUP LITTer_NumBer gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ## 1 Con7  #85                 19.7        34.7          20               3
    ## 2 Con7  #1/2/95/2           27          42            19               8
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19               6
    ## # ... with 46 more rows, and 2 more variables: pups_dead_birth <dbl>,
    ## #   pups_survive <dbl>

“Select” helpers: starts\_with, ends\_with, contains

``` r
select(litters_df, starts_with("gd"))
```

    ## # A tibble: 49 x 3
    ##   gd0_weight gd18_weight gd_of_birth
    ##        <dbl>       <dbl>       <dbl>
    ## 1       19.7        34.7          20
    ## 2       27          42            19
    ## 3       26          41.4          19
    ## # ... with 46 more rows

### Reorganizing columns with “everything()”, or “relocate”

Select litter\_number, pups\_survive as 1st two columns, keep everything

``` r
select(litters_df, litter_number, pups_survive, everything())
```

    ## # A tibble: 49 x 8
    ##   litter_number pups_survive group gd0_weight gd18_weight gd_of_birth
    ##   <chr>                <dbl> <chr>      <dbl>       <dbl>       <dbl>
    ## 1 #85                      3 Con7        19.7        34.7          20
    ## 2 #1/2/95/2                7 Con7        27          42            19
    ## 3 #5/5/3/83/3-3            5 Con7        26          41.4          19
    ## # ... with 46 more rows, and 2 more variables: pups_born_alive <dbl>,
    ## #   pups_dead_birth <dbl>

Relocate does the same thing:

``` r
relocate(litters_df, litter_number, pups_survive)
```

    ## # A tibble: 49 x 8
    ##   litter_number pups_survive group gd0_weight gd18_weight gd_of_birth
    ##   <chr>                <dbl> <chr>      <dbl>       <dbl>       <dbl>
    ## 1 #85                      3 Con7        19.7        34.7          20
    ## 2 #1/2/95/2                7 Con7        27          42            19
    ## 3 #5/5/3/83/3-3            5 Con7        26          41.4          19
    ## # ... with 46 more rows, and 2 more variables: pups_born_alive <dbl>,
    ## #   pups_dead_birth <dbl>

## function “filter”

Filter gestational-day-0 weight \< 22

``` r
filter(litters_df, gd0_weight < 22)
```

    ## # A tibble: 8 x 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ## 1 Con7  #85                 19.7        34.7          20               3
    ## 2 Mod7  #59                 17          33.4          19               8
    ## 3 Mod7  #103                21.4        42.1          19               9
    ## 4 Mod7  #106                21.7        37.8          20               5
    ## 5 Mod7  #62                 19.5        35.9          19               7
    ## 6 Low8  #53                 21.8        37.2          20               8
    ## 7 Low8  #100                20          39.2          20               8
    ## 8 Low8  #4/84               21.8        35.2          20               4
    ## # ... with 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

Filter gd\_of\_birth equal to 20

``` r
filter(litters_df, gd_of_birth == 20)
```

    ## # A tibble: 32 x 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ## 1 Con7  #85                 19.7        34.7          20               3
    ## 2 Con7  #4/2/95/3-3         NA          NA            20               6
    ## 3 Con7  #2/2/95/3-2         NA          NA            20               6
    ## # ... with 29 more rows, and 2 more variables: pups_dead_birth <dbl>,
    ## #   pups_survive <dbl>

Filter gd\_of\_birth NOT equal to 20

``` r
filter(litters_df, !(gd_of_birth == 20))
```

    ## # A tibble: 17 x 8
    ##    group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##    <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ##  1 Con7  #1/2/95/2           27          42            19               8
    ##  2 Con7  #5/5/3/83/3-3       26          41.4          19               6
    ##  3 Con7  #5/4/2/95/2         28.5        44.1          19               5
    ##  4 Con8  #5/4/3/83/3         28          NA            19               9
    ##  5 Con8  #2/2/95/2           NA          NA            19               5
    ##  6 Mod7  #59                 17          33.4          19               8
    ##  7 Mod7  #103                21.4        42.1          19               9
    ##  8 Mod7  #1/82/3-2           NA          NA            19               6
    ##  9 Mod7  #3/83/3-2           NA          NA            19               8
    ## 10 Mod7  #4/2/95/2           23.5        NA            19               9
    ## 11 Mod7  #5/3/83/5-2         22.6        37            19               5
    ## 12 Mod7  #94/2               24.4        42.9          19               7
    ## 13 Mod7  #62                 19.5        35.9          19               7
    ## 14 Low7  #112                23.9        40.5          19               6
    ## 15 Mod8  #5/93/2             NA          NA            19               8
    ## 16 Mod8  #7/110/3-2          27.5        46            19               8
    ## 17 Low8  #79                 25.4        43.8          19               8
    ## # ... with 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

### Learning assessment

1.  selecting litter number, sex, and PD ears in “pups” data:

<!-- end list -->

``` r
select(pups_df, litter_number, sex, pd_ears)
```

    ## # A tibble: 313 x 3
    ##   litter_number   sex pd_ears
    ##   <chr>         <int>   <int>
    ## 1 #85               1       4
    ## 2 #85               1       4
    ## 3 #1/2/95/2         1       5
    ## # ... with 310 more rows

2a.“Filter” to include only pups with sex 1.

``` r
filter(pups_df, sex == 1)
```

    ## # A tibble: 155 x 6
    ##   litter_number   sex pd_ears pd_eyes pd_pivot pd_walk
    ##   <chr>         <int>   <int>   <int>    <int>   <int>
    ## 1 #85               1       4      13        7      11
    ## 2 #85               1       4      13        7      12
    ## 3 #1/2/95/2         1       5      13        7       9
    ## # ... with 152 more rows

2b. “Filter” pups with PD walk less than 11 and sex 2.

``` r
filter(pups_df, pd_walk < 11, sex == 2)
```

    ## # A tibble: 127 x 6
    ##   litter_number   sex pd_ears pd_eyes pd_pivot pd_walk
    ##   <chr>         <int>   <int>   <int>    <int>   <int>
    ## 1 #1/2/95/2         2       4      13        7       9
    ## 2 #1/2/95/2         2       4      13        7      10
    ## 3 #1/2/95/2         2       5      13        8      10
    ## # ... with 124 more rows

#### End assessments 1, 2

## Function “mutate”

Create new variable “wt\_gain” using “mutate”

``` r
mutate(litters_df,
  wt_gain = gd18_weight - gd0_weight)
```

    ## # A tibble: 49 x 9
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ## 1 Con7  #85                 19.7        34.7          20               3
    ## 2 Con7  #1/2/95/2           27          42            19               8
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19               6
    ## # ... with 46 more rows, and 3 more variables: pups_dead_birth <dbl>,
    ## #   pups_survive <dbl>, wt_gain <dbl>

Mutate by creating new variable, and modifying existing variable:

``` r
mutate(litters_df,
  wt_gain = gd18_weight - gd0_weight,
  group = str_to_lower(group))
```

    ## # A tibble: 49 x 9
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ## 1 con7  #85                 19.7        34.7          20               3
    ## 2 con7  #1/2/95/2           27          42            19               8
    ## 3 con7  #5/5/3/83/3-3       26          41.4          19               6
    ## # ... with 46 more rows, and 3 more variables: pups_dead_birth <dbl>,
    ## #   pups_survive <dbl>, wt_gain <dbl>

## Function “Arrange”

Arrange data by pups\_born\_alive (will be in increasing order)

``` r
arrange(litters_df, pups_born_alive)
```

    ## # A tibble: 49 x 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ## 1 Con7  #85                 19.7        34.7          20               3
    ## 2 Low7  #111                25.5        44.6          20               3
    ## 3 Low8  #4/84               21.8        35.2          20               4
    ## # ... with 46 more rows, and 2 more variables: pups_dead_birth <dbl>,
    ## #   pups_survive <dbl>

Arrange by two variables: pups\_born\_alive and gd0\_weight.
(gd0\_weight will be arranged within pups\_born\_alive)

``` r
arrange(litters_df, pups_born_alive, gd0_weight)
```

    ## # A tibble: 49 x 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ## 1 Con7  #85                 19.7        34.7          20               3
    ## 2 Low7  #111                25.5        44.6          20               3
    ## 3 Low8  #4/84               21.8        35.2          20               4
    ## # ... with 46 more rows, and 2 more variables: pups_dead_birth <dbl>,
    ## #   pups_survive <dbl>

## “%\>%” (pipe operation)

Read litters dataframe -\> clean names -\> select all columns except
pups\_survive -\> mutate df by adding variable wet\_gain -\> drop rows
with missing values from column gd0\_weight:

``` r
litters_df = 
  read_csv("./data/FAS_litters.csv") %>% 
  janitor::clean_names() %>% 
  select(-pups_survive) %>% 
  mutate(wt_gain = gd18_weight - gd0_weight) %>% 
  drop_na(gd0_weight)
```

    ## Parsed with column specification:
    ## cols(
    ##   Group = col_character(),
    ##   `Litter Number` = col_character(),
    ##   `GD0 weight` = col_double(),
    ##   `GD18 weight` = col_double(),
    ##   `GD of Birth` = col_double(),
    ##   `Pups born alive` = col_double(),
    ##   `Pups dead @ birth` = col_double(),
    ##   `Pups survive` = col_double()
    ## )

#### Learning assessment

3a. Create a variable that subtracts 7 from PD pivot in data “pups”

``` r
mutate(pups_df,
  pivot_minus7 = pd_pivot - 7)
```

    ## # A tibble: 313 x 7
    ##   litter_number   sex pd_ears pd_eyes pd_pivot pd_walk pivot_minus7
    ##   <chr>         <int>   <int>   <int>    <int>   <int>        <dbl>
    ## 1 #85               1       4      13        7      11            0
    ## 2 #85               1       4      13        7      12            0
    ## 3 #1/2/95/2         1       5      13        7       9            0
    ## # ... with 310 more rows

3b. Create a variable that is the sum of all the PD-variables

``` r
mutate(pups_df, 
       pd_sum = pd_ears + pd_eyes + pd_pivot + pd_walk)
```

    ## # A tibble: 313 x 7
    ##   litter_number   sex pd_ears pd_eyes pd_pivot pd_walk pd_sum
    ##   <chr>         <int>   <int>   <int>    <int>   <int>  <int>
    ## 1 #85               1       4      13        7      11     35
    ## 2 #85               1       4      13        7      12     36
    ## 3 #1/2/95/2         1       5      13        7       9     34
    ## # ... with 310 more rows

4.  Piping Assessment: load the pups data, clean the variable names,
    filter the data to include only pups with sex 1, remove the PD ears
    variable, create a variable that indicates whether PD pivot is 7 or
    more days.

<!-- end list -->

``` r
read_csv("./data/FAS_pups.csv", col_types = "ciiiii") %>%
  janitor::clean_names() %>% 
  filter(sex == 1) %>% 
  select(-pd_ears) %>% 
  mutate(pd_pivot_gt7 = pd_pivot > 7)
```

    ## # A tibble: 155 x 6
    ##   litter_number   sex pd_eyes pd_pivot pd_walk pd_pivot_gt7
    ##   <chr>         <int>   <int>    <int>   <int> <lgl>       
    ## 1 #85               1      13        7      11 FALSE       
    ## 2 #85               1      13        7      12 FALSE       
    ## 3 #1/2/95/2         1      13        7       9 FALSE       
    ## # ... with 152 more rows

#### End assessment 3, 4

End module 2
