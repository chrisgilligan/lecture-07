Lab-06-Replication
================
Branson Fox
(February 25, 2019)

Introduction
------------

This notebook replicates the results of lab 06.

Dependencies
------------

This notebook requires the following packages to load our data and clean it.

``` r
# tidyverse packages
library(readr)   # reading tabular data
library(dplyr)   # data wrangling
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
# other packages
library(here)    # file path management
```

    ## here() starts at /Users/lawrence/Desktop/Lab-06-Replication

``` r
library(janitor) # data wrangling
library(naniar)  # missing data analysis
```

Load Data
---------

This notebook requires the `MO_HYDRO_ImpairedRiversStreams.csv` data from the lecture-07 repository.

``` r
rivers <- read_csv(here("data", "MO_HYDRO_ImpairedRiversStreams.csv"))
```

    ## Parsed with column specification:
    ## cols(
    ##   .default = col_double(),
    ##   WATER_BODY = col_character(),
    ##   WB_CLS = col_character(),
    ##   UNIT = col_character(),
    ##   POLLUTANT = col_character(),
    ##   SOURCE_ = col_character(),
    ##   IU = col_character(),
    ##   OU = col_character(),
    ##   COUNTY_U_D = col_character(),
    ##   WB_EPA = col_character(),
    ##   COMMENT_ = col_character(),
    ##   PERM_ID = col_character(),
    ##   EVENTDAT = col_date(format = ""),
    ##   REACHCODE = col_character(),
    ##   RCHSMDATE = col_logical(),
    ##   RCH_RES = col_logical(),
    ##   SRC_DESC = col_character(),
    ##   FEAT_URL = col_logical()
    ## )

    ## See spec(...) for full column specifications.

Part 1: Data Wrangling
----------------------

### Question 1

We'll start by creating a pipeline using the pipe operator `%>%` that renames variables to `snake_case`, and rename two other variables.

``` r
rivers %>% 
  clean_names(case = "snake") %>%
  rename(
    date = eventdat,
    county = county_u_d) -> rivers_names
# print tibble
rivers_names
```

    ## # A tibble: 6,029 x 31
    ##       yr businessid  wbid water_body wb_cls mdnr_impsz  size epa_apprsz
    ##    <dbl>      <dbl> <dbl> <chr>      <chr>       <dbl> <dbl>      <dbl>
    ##  1  2012      51525  2188 Antire Cr. P             1.9   1.9        1.9
    ##  2  2012      51525  2188 Antire Cr. P             1.9   1.9        1.9
    ##  3  2012      51524  2188 Antire Cr. P             1.9   1.9        1.9
    ##  4  2012      51524  2188 Antire Cr. P             1.9   1.9        1.9
    ##  5  2012      51388   752 Bass Cr.   C             4.4   4.4        4.4
    ##  6  2012      51388   752 Bass Cr.   C             4.4   4.4        4.4
    ##  7  2012      51388   752 Bass Cr.   C             4.4   4.4        4.4
    ##  8  2012      51388   752 Bass Cr.   C             4.4   4.4        4.4
    ##  9  2012      51388   752 Bass Cr.   C             4.4   4.4        4.4
    ## 10  2012      51663  3240 Baynham B… P             4     4          4  
    ## # … with 6,019 more rows, and 23 more variables: unit <chr>,
    ## #   pollutant <chr>, source <chr>, iu <chr>, ou <chr>, up_x <dbl>,
    ## #   up_y <dbl>, dwn_x <dbl>, dwn_y <dbl>, county <chr>, wb_epa <chr>,
    ## #   comment <chr>, perm_id <chr>, date <date>, reachcode <chr>,
    ## #   rchsmdate <lgl>, rch_res <lgl>, src_desc <chr>, feat_url <lgl>,
    ## #   fmeasure <dbl>, tmeasure <dbl>, shape_leng <dbl>, shape_le_1 <dbl>

### Question 2

Next, we'll create a missing variable summary using the `miss_var_summary()` function from `naniar`.

``` r
miss_var_summary(rivers_names)
```

    ## # A tibble: 31 x 3
    ##    variable   n_miss pct_miss
    ##    <chr>       <int>    <dbl>
    ##  1 rchsmdate    6029  100    
    ##  2 rch_res      6029  100    
    ##  3 feat_url     6029  100    
    ##  4 perm_id       256    4.25 
    ##  5 date          256    4.25 
    ##  6 ou             56    0.929
    ##  7 yr              0    0    
    ##  8 businessid      0    0    
    ##  9 wbid            0    0    
    ## 10 water_body      0    0    
    ## # … with 21 more rows

We can observe that `rchsmdate`, `rch_res` and `feat_url` are missing for all observations, and `perm_id`,`date` and `ou` are missing a small percentage of observations.

### Question 3

Next, we'll create a report of duplicate observations. We'll do so by using the `get_dupes()` function from `janitor`.

``` r
get_dupes(rivers_names)
```

    ## No variable names specified - using all columns.

    ## # A tibble: 36 x 32
    ##       yr businessid  wbid water_body wb_cls mdnr_impsz  size epa_apprsz
    ##    <dbl>      <dbl> <dbl> <chr>      <chr>       <dbl> <dbl>      <dbl>
    ##  1  2010      51730  2579 Platte R.  P            142.  142.       142.
    ##  2  2010      51730  2579 Platte R.  P            142.  142.       142.
    ##  3  2010      51730  2579 Platte R.  P            142.  142.       142.
    ##  4  2010      51730  2579 Platte R.  P            142.  142.       142.
    ##  5  2010      51730  2579 Platte R.  P            142.  142.       142.
    ##  6  2010      51730  2579 Platte R.  P            142.  142.       142.
    ##  7  2010      51730  2579 Platte R.  P            142.  142.       142.
    ##  8  2010      51730  2579 Platte R.  P            142.  142.       142.
    ##  9  2010      51730  2579 Platte R.  P            142.  142.       142.
    ## 10  2010      51730  2579 Platte R.  P            142.  142.       142.
    ## # … with 26 more rows, and 24 more variables: unit <chr>, pollutant <chr>,
    ## #   source <chr>, iu <chr>, ou <chr>, up_x <dbl>, up_y <dbl>, dwn_x <dbl>,
    ## #   dwn_y <dbl>, county <chr>, wb_epa <chr>, comment <chr>, perm_id <chr>,
    ## #   date <date>, reachcode <chr>, rchsmdate <lgl>, rch_res <lgl>,
    ## #   src_desc <chr>, feat_url <lgl>, fmeasure <dbl>, tmeasure <dbl>,
    ## #   shape_leng <dbl>, shape_le_1 <dbl>, dupe_count <int>

There are 36 observations in which data is duplicated twice. Or in other words, 18 observations are duplicates. If we have 6029 observations and 18 are duplicate, we only have 6011 *unique* observations.

### Question 4

Next, we'll check for duplicates in the `perm_id` variable

``` r
get_dupes(rivers_names, perm_id)
```

    ## # A tibble: 2,349 x 32
    ##    perm_id dupe_count    yr businessid  wbid water_body wb_cls mdnr_impsz
    ##    <chr>        <int> <dbl>      <dbl> <dbl> <chr>      <chr>       <dbl>
    ##  1 {0034F…          3  2006      51618  3188 N. Fk. Sp… C             1.1
    ##  2 {0034F…          3  2008      51620  3188 N. Fk. Sp… C            55.9
    ##  3 {0034F…          3  2006      51619  3188 N. Fk. Sp… C            55.9
    ##  4 {00B10…          2  2008      51018  2755 W. Fk. Bl… P             2.1
    ##  5 {00B10…          2  2008      51770  2755 W. Fk. Bl… P             2.1
    ##  6 {0145F…          2  2006      51487   623 L. Medici… P            19.8
    ##  7 {0145F…          2  2006      51486   623 L. Medici… P            39.8
    ##  8 {019A3…          4  2006      51548  2080 Big R.     P            52.8
    ##  9 {019A3…          4  2010      55224  2080 Big R.     P            52.3
    ## 10 {019A3…          4  2014      62493  2080 Big R.     P            52.3
    ## # … with 2,339 more rows, and 24 more variables: size <dbl>,
    ## #   epa_apprsz <dbl>, unit <chr>, pollutant <chr>, source <chr>, iu <chr>,
    ## #   ou <chr>, up_x <dbl>, up_y <dbl>, dwn_x <dbl>, dwn_y <dbl>,
    ## #   county <chr>, wb_epa <chr>, comment <chr>, date <date>,
    ## #   reachcode <chr>, rchsmdate <lgl>, rch_res <lgl>, src_desc <chr>,
    ## #   feat_url <lgl>, fmeasure <dbl>, tmeasure <dbl>, shape_leng <dbl>,
    ## #   shape_le_1 <dbl>

It appears that there is a substantial number of observations (2349) in which `perm_id` is duplicated. Therefore, this variable does not uniquely identify variables.

### Question 5

Next, we'll create a pipeline that subsets observations to St. Louis, keeps only variables we are interested in, and assigns these changes to a new tibble.

``` r
rivers_names %>%
  filter(county == "St. Louis") %>%
  select(yr, wbid, water_body, pollutant, source) -> rivers_stl
# print tibble
rivers_stl
```

    ## # A tibble: 179 x 5
    ##       yr  wbid water_body   pollutant            source                   
    ##    <dbl> <dbl> <chr>        <chr>                <chr>                    
    ##  1  2012  2188 Antire Cr.   Escherichia coli (W) Urban Runoff/Storm Sewers
    ##  2  2012  2188 Antire Cr.   Escherichia coli (W) Urban Runoff/Storm Sewers
    ##  3  2012  2188 Antire Cr.   pH (W)               Source Unknown           
    ##  4  2012  2188 Antire Cr.   pH (W)               Source Unknown           
    ##  5  2006  3825 Black Cr.    Chloride (W)         Urban Runoff/Storm Sewers
    ##  6  2006  3825 Black Cr.    Chloride (W)         Urban Runoff/Storm Sewers
    ##  7  2012  3825 Black Cr.    Escherichia coli (W) Urban Runoff/Storm Sewers
    ##  8  2012  3825 Black Cr.    Escherichia coli (W) Urban Runoff/Storm Sewers
    ##  9  2012  1701 Bonhomme Cr. Escherichia coli (W) Urban Runoff/Storm Sewers
    ## 10  2012  1701 Bonhomme Cr. Escherichia coli (W) Urban Runoff/Storm Sewers
    ## # … with 169 more rows

### Question 6

Finally, we'll create a pipeline that changes the word `Creek` to `Cr.`, creates a logical `ecoli` variable and assigns the new data back into the original tibble.

``` r
rivers_stl %>%
  mutate(water_body = case_when(
    water_body == "Gravois Creek tributary" ~ "Gravois Cr. tributary",
    water_body == "Twomile Creek" ~ "Twomile Cr.",
    water_body == "Watkins Creek tributary" ~ "Watkins Cr. tributary",
    TRUE ~ water_body
  ),
  ecoli = ifelse(pollutant == "Escherichia coli (W)",TRUE,FALSE)
  ) -> rivers_stl
# print tibble
rivers_stl
```

    ## # A tibble: 179 x 6
    ##       yr  wbid water_body   pollutant          source                 ecoli
    ##    <dbl> <dbl> <chr>        <chr>              <chr>                  <lgl>
    ##  1  2012  2188 Antire Cr.   Escherichia coli … Urban Runoff/Storm Se… TRUE 
    ##  2  2012  2188 Antire Cr.   Escherichia coli … Urban Runoff/Storm Se… TRUE 
    ##  3  2012  2188 Antire Cr.   pH (W)             Source Unknown         FALSE
    ##  4  2012  2188 Antire Cr.   pH (W)             Source Unknown         FALSE
    ##  5  2006  3825 Black Cr.    Chloride (W)       Urban Runoff/Storm Se… FALSE
    ##  6  2006  3825 Black Cr.    Chloride (W)       Urban Runoff/Storm Se… FALSE
    ##  7  2012  3825 Black Cr.    Escherichia coli … Urban Runoff/Storm Se… TRUE 
    ##  8  2012  3825 Black Cr.    Escherichia coli … Urban Runoff/Storm Se… TRUE 
    ##  9  2012  1701 Bonhomme Cr. Escherichia coli … Urban Runoff/Storm Se… TRUE 
    ## 10  2012  1701 Bonhomme Cr. Escherichia coli … Urban Runoff/Storm Se… TRUE 
    ## # … with 169 more rows
