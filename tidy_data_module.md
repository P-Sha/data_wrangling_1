tidy data and relational datasets
================

``` r
library(tidyverse)
```

    ## -- Attaching packages --------------------------------- tidyverse 1.3.0 --

    ## v ggplot2 3.3.2     v purrr   0.3.4
    ## v tibble  3.0.3     v dplyr   1.0.2
    ## v tidyr   1.1.2     v stringr 1.4.0
    ## v readr   1.3.1     v forcats 0.5.0

    ## -- Conflicts ------------------------------------ tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

## Module 3

Limit the number of lines printed in a tibble to 5.

``` r
options(tibble.print_min = 5)
```

Load “PULSE biomarker”s dataset (using “haven” package since data is not
csv, it’s in sas).

``` r
pulse_df = 
  haven::read_sas("./data/public_pulse_data.sas7bdat") %>%
  janitor::clean_names()

pulse_df
```

    ## # A tibble: 1,087 x 7
    ##      id   age sex   bdi_score_bl bdi_score_01m bdi_score_06m bdi_score_12m
    ##   <dbl> <dbl> <chr>        <dbl>         <dbl>         <dbl>         <dbl>
    ## 1 10003  48.0 male             7             1             2             0
    ## 2 10015  72.5 male             6            NA            NA            NA
    ## 3 10022  58.5 male            14             3             8            NA
    ## 4 10026  72.7 male            20             6            18            16
    ## 5 10035  60.4 male             4             0             1             2
    ## # ... with 1,082 more rows

Data Description: For each ID, data shows 4 different observations for
bdi score.

Change data from wide format to long format:

## “Pivot\_longer”

Tidy and transform the PULSE dataset into a usable format.

``` r
pulse_df_tidy = 
  pulse_df %>%
   pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visit", 
    names_prefix = "bdi_score_", 
    values_to = "bdi"
    ) 
```

Rwrite, commbine, and extend(to add a mutuate)

``` r
pulse_df = 
  haven::read_sas("./data/public_pulse_data.sas7bdat") %>%
  janitor::clean_names() %>% 
   pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visit", 
    names_prefix = "bdi_score_", 
    values_to = "bdi"
   ) %>% 
  relocate(id, visit) %>% 
  mutate(visit = recode(visit, "bl" = "00m"))
```

## “Pivot\_wider”

Create a (tidy) data table:

``` r
analysis_result = 
  tibble(
    group = c("treatment", "treatment", "placebo", "placebo"),
    time = c("pre", "post", "pre", "post"),
    mean = c(4, 8, 3.5, 4)
)

analysis_result
```

    ## # A tibble: 4 x 3
    ##   group     time   mean
    ##   <chr>     <chr> <dbl>
    ## 1 treatment pre     4  
    ## 2 treatment post    8  
    ## 3 placebo   pre     3.5
    ## 4 placebo   post    4

Rearrange with pivot\_wider to write “time” subcategories in columns:

``` r
pivot_wider(
  analysis_result, 
  names_from = "time",   ## column names are from "time"
  values_from = "mean"
  )
```

    ## # A tibble: 2 x 3
    ##   group       pre  post
    ##   <chr>     <dbl> <dbl>
    ## 1 treatment   4       8
    ## 2 placebo     3.5     4

Easier to read after pivot\_wider\!

## Binding rows

Using the LotR data.

First step: import each table.

``` r
fellowship_ring = 
  readxl::read_excel("./data/LotR_Words.xlsx", range = "B3:D6") %>% 
  mutate(movie = "fellowship_ring")

two_towers = 
  readxl::read_excel("./data/LotR_Words.xlsx", range = "F3:H6") %>% 
  mutate(movie = "two_towers")

return_king = 
  readxl::read_excel("./data/LotR_Words.xlsx", range = "J3:L6") %>% 
  mutate(movie = "return_king")
```

Bind all the rows together

``` r
lotr_tidy =
  bind_rows(fellowship_ring, two_towers, return_king) %>% 
  janitor::clean_names() %>% 
  relocate(movie) %>% 
  pivot_longer(
    female:male,
    names_to = "gender",
    values_to = "words"
  )
```

## Joining datasets

Import and clean the FAS datasets.

``` r
pups_df =
  read_csv("./data/FAS_pups.csv") %>% 
  janitor::clean_names() %>% 
  mutate(sex = recode(sex, `1` = "male", `2` = "female"))  # recoding sex column from numbers to labels.
```

    ## Parsed with column specification:
    ## cols(
    ##   `Litter Number` = col_character(),
    ##   Sex = col_double(),
    ##   `PD ears` = col_double(),
    ##   `PD eyes` = col_double(),
    ##   `PD pivot` = col_double(),
    ##   `PD walk` = col_double()
    ## )

``` r
litters_df = 
  read_csv("./data/FAS_litters.csv") %>% 
  janitor::clean_names() %>% 
  relocate(litter_number) %>%    # relocate this column to beginning
  separate(group, into = c("dose", "day_of_tx"), sep = 3)  
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
#separate groups column in to two, have to specify where to split the data value: separate after 3 characters!
```

Next up, time to join them\! Call it fas\_df = fetal alcohol syndrome
dataframe. Join litter df into pups df, using litter\_number as Key.

``` r
fas_df = 
  left_join(pups_df, litters_df, by = "litter_number") %>%
  arrange(litter_number) %>% 
  relocate(litter_number, dose, day_of_tx)
```
