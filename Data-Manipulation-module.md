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

Read data, print only 3 lines for tibble, and Change column names.

``` r
options(tibble.print_min = 3)

litters_data = read_csv("./data/FAS_litters.csv",
  col_types = "ccddiiii")
litters_data = janitor::clean_names(litters_data)

pups_data = read_csv("./data/FAS_pups.csv",
  col_types = "ciiiii")
pups_data = janitor::clean_names(pups_data)
```

### function “Select”

Select columns from “Litters” dataset: 1. Specify names to select

``` r
select(litters_data, group, litter_number, gd0_weight, pups_born_alive)
```

    ## # A tibble: 49 x 4
    ##   group litter_number gd0_weight pups_born_alive
    ##   <chr> <chr>              <dbl>           <int>
    ## 1 Con7  #85                 19.7               3
    ## 2 Con7  #1/2/95/2           27                 8
    ## 3 Con7  #5/5/3/83/3-3       26                 6
    ## # ... with 46 more rows

2.  Specify range to select

<!-- end list -->

``` r
select(litters_data, group:gd_of_birth)
```

    ## # A tibble: 49 x 5
    ##   group litter_number gd0_weight gd18_weight gd_of_birth
    ##   <chr> <chr>              <dbl>       <dbl>       <int>
    ## 1 Con7  #85                 19.7        34.7          20
    ## 2 Con7  #1/2/95/2           27          42            19
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19
    ## # ... with 46 more rows

3.  Select by specifying columns to remove

<!-- end list -->

``` r
select(litters_data, -pups_survive)
```

    ## # A tibble: 49 x 7
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <int>           <int>
    ## 1 Con7  #85                 19.7        34.7          20               3
    ## 2 Con7  #1/2/95/2           27          42            19               8
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19               6
    ## # ... with 46 more rows, and 1 more variable: pups_dead_birth <int>

### Reorganizing columns with “everything()”, or “relocate”

``` r
select(litters_data, litter_number, pups_survive, everything())
```

    ## # A tibble: 49 x 8
    ##   litter_number pups_survive group gd0_weight gd18_weight gd_of_birth
    ##   <chr>                <int> <chr>      <dbl>       <dbl>       <int>
    ## 1 #85                      3 Con7        19.7        34.7          20
    ## 2 #1/2/95/2                7 Con7        27          42            19
    ## 3 #5/5/3/83/3-3            5 Con7        26          41.4          19
    ## # ... with 46 more rows, and 2 more variables: pups_born_alive <int>,
    ## #   pups_dead_birth <int>

``` r
relocate(litters_data, litter_number, pups_survive)
```

    ## # A tibble: 49 x 8
    ##   litter_number pups_survive group gd0_weight gd18_weight gd_of_birth
    ##   <chr>                <int> <chr>      <dbl>       <dbl>       <int>
    ## 1 #85                      3 Con7        19.7        34.7          20
    ## 2 #1/2/95/2                7 Con7        27          42            19
    ## 3 #5/5/3/83/3-3            5 Con7        26          41.4          19
    ## # ... with 46 more rows, and 2 more variables: pups_born_alive <int>,
    ## #   pups_dead_birth <int>

### Learning assessment

1.  selecting litter number, sex, and PD ears in “pups” data:

<!-- end list -->

``` r
select(pups_data, litter_number, sex, pd_ears)
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
filter(pups_data, sex == 1)
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
filter(pups_data, pd_walk < 11, sex == 2)
```

    ## # A tibble: 127 x 6
    ##   litter_number   sex pd_ears pd_eyes pd_pivot pd_walk
    ##   <chr>         <int>   <int>   <int>    <int>   <int>
    ## 1 #1/2/95/2         2       4      13        7       9
    ## 2 #1/2/95/2         2       4      13        7      10
    ## 3 #1/2/95/2         2       5      13        8      10
    ## # ... with 124 more rows

#### End assessments 1, 2

## “mutate” : create new variable “wt\_gain” using “mutate”

``` r
mutate(litters_data,
  wt_gain = gd18_weight - gd0_weight)
```

    ## # A tibble: 49 x 9
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <int>           <int>
    ## 1 Con7  #85                 19.7        34.7          20               3
    ## 2 Con7  #1/2/95/2           27          42            19               8
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19               6
    ## # ... with 46 more rows, and 3 more variables: pups_dead_birth <int>,
    ## #   pups_survive <int>, wt_gain <dbl>

#### Learing assessment

3a. Create a variable that subtracts 7 from PD pivot in data “pups”

``` r
mutate(pups_data,
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
mutate(pups_data, 
       pd_sum = pd_ears + pd_eyes + pd_pivot + pd_walk)
```

    ## # A tibble: 313 x 7
    ##   litter_number   sex pd_ears pd_eyes pd_pivot pd_walk pd_sum
    ##   <chr>         <int>   <int>   <int>    <int>   <int>  <int>
    ## 1 #85               1       4      13        7      11     35
    ## 2 #85               1       4      13        7      12     36
    ## 3 #1/2/95/2         1       5      13        7       9     34
    ## # ... with 310 more rows

4.  Piping Assessment: loads the pups data, clean the variable names,
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
