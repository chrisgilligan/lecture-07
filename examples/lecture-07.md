Lecture-07 Examples
================
Christopher Prener, Ph.D.
(March 01, 2019)

## Introduction

This notebook illustrates basic data cleaning operations using a number
of core tools for data wrangling.

## Dependencies

This notebook requires a number of packages for working with data:

``` r
# tidyverse packages
library(dplyr)       # data wrangling
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
library(readr)       # read csv files
library(stringr)     # string tools

# other packages
library(here)        # file path management
```

    ## here() starts at /Users/chris/GitHub/SOC5650/LectureRepos/lecture-07

``` r
library(janitor)     # data wrangling
library(naniar)      # missing data analyses
```

## Load Data

This notebook requires data from the University of Missouri on lakes
listed under the Clean Water
Act.

``` r
lakes <- read_csv(here("data", "example-data", "MO_HYDRO_ImpairedLakes.csv"))
```

    ## Parsed with column specification:
    ## cols(
    ##   .default = col_character(),
    ##   YR = col_double(),
    ##   BUSINESSID = col_double(),
    ##   WBID = col_double(),
    ##   MDNR_IMPSZ = col_double(),
    ##   SIZE_ = col_double(),
    ##   EPA_APPRSZ = col_double(),
    ##   DWN_X = col_double(),
    ##   DWN_Y = col_double(),
    ##   EVENTDAT = col_date(format = ""),
    ##   RCHSMDATE = col_logical(),
    ##   RCH_RES = col_logical(),
    ##   FEAT_URL = col_logical(),
    ##   SHAPE_Leng = col_double(),
    ##   Shape_Le_1 = col_double(),
    ##   Shape_Area = col_double()
    ## )

    ## See spec(...) for full column specifications.

When the data are read in, they are read in as a special type object
called a `tibble` - these are data frame objects that have particular,
useful properties, like clean printing. `readr` will always create
`tibble` objects for you. If you want to create one from an object that
is not a tibble, you can use the following syntax:

``` r
obj <- dplyr::as_tibble(obj)
```

## Manage Variable Names

We have a couple choices when it comes to cleaning-up variable names.
When you have many columns that require cleaning, the `janitor`
package’s `clean_names()` function is particularly useful. It will
reformat your variable names all at once, using a case you optionally
change:

  - `snake_case`
  - `lowerCamel`
  - `UpperCamel`

The default is `"snake"`, which we’ll clearly specify here:

``` r
clean_names(lakes, case = "snake")
```

    ## # A tibble: 86 x 28
    ##       yr businessid  wbid water_body wb_cls mdnr_impsz  size epa_apprsz
    ##    <dbl>      <dbl> <dbl> <chr>      <chr>       <dbl> <dbl>      <dbl>
    ##  1  2014      60348  7309 Bee Tree … L3             10    10         10
    ##  2  2006      59691  7365 Belcher B… L3             42    42         42
    ##  3  2014      54686  7003 Bowling G… L1              7     7          7
    ##  4  2012      51313  7003 Bowling G… L1              7     7          7
    ##  5  2012      51312  7003 Bowling G… L1              7     7          7
    ##  6  2016      60247  7117 Buffalo B… L3             45    45         45
    ##  7  2006      60020  7057 Busch W.A… L3             51    51         51
    ##  8  2014      55954  7326 Clearwate… L2           1635  1635       1635
    ##  9  2002      60036  7326 Clearwate… L2           1635  1635       1635
    ## 10  2016      60037  7326 Clearwate… L2           1635  1635       1635
    ## # … with 76 more rows, and 20 more variables: unit <chr>, pollutant <chr>,
    ## #   source <chr>, iu <chr>, ou <chr>, dwn_x <dbl>, dwn_y <dbl>,
    ## #   county_u_d <chr>, wb_epa <chr>, comment <chr>, perm_id <chr>,
    ## #   eventdat <date>, reachcode <chr>, rchsmdate <lgl>, rch_res <lgl>,
    ## #   src_desc <chr>, feat_url <lgl>, shape_leng <dbl>, shape_le_1 <dbl>,
    ## #   shape_area <dbl>

Notice how underscores (i.e. `_`) have been introduced in variable
names. The following code chunk illustrates the `"lower_camel"` option
for comparison.

``` r
clean_names(lakes, case = "lower_camel")
```

    ## # A tibble: 86 x 28
    ##       yr businessid  wbid waterBody wbCls mdnrImpsz  size epaApprsz unit 
    ##    <dbl>      <dbl> <dbl> <chr>     <chr>     <dbl> <dbl>     <dbl> <chr>
    ##  1  2014      60348  7309 Bee Tree… L3           10    10        10 Acres
    ##  2  2006      59691  7365 Belcher … L3           42    42        42 Acres
    ##  3  2014      54686  7003 Bowling … L1            7     7         7 Acres
    ##  4  2012      51313  7003 Bowling … L1            7     7         7 Acres
    ##  5  2012      51312  7003 Bowling … L1            7     7         7 Acres
    ##  6  2016      60247  7117 Buffalo … L3           45    45        45 Acres
    ##  7  2006      60020  7057 Busch W.… L3           51    51        51 Acres
    ##  8  2014      55954  7326 Clearwat… L2         1635  1635      1635 Acres
    ##  9  2002      60036  7326 Clearwat… L2         1635  1635      1635 Acres
    ## 10  2016      60037  7326 Clearwat… L2         1635  1635      1635 Acres
    ## # … with 76 more rows, and 19 more variables: pollutant <chr>,
    ## #   source <chr>, iu <chr>, ou <chr>, dwnX <dbl>, dwnY <dbl>,
    ## #   countyUD <chr>, wbEpa <chr>, comment <chr>, permId <chr>,
    ## #   eventdat <date>, reachcode <chr>, rchsmdate <lgl>, rchRes <lgl>,
    ## #   srcDesc <chr>, featUrl <lgl>, shapeLeng <dbl>, shapeLe1 <dbl>,
    ## #   shapeArea <dbl>

The underscores used in snake case are gone, and replaced by a capital
letter in the second word. With `"uower_camel"`, the first character of
each variable name will also be capitalized:

``` r
clean_names(lakes, case = "upper_camel")
```

    ## # A tibble: 86 x 28
    ##       Yr Businessid  Wbid WaterBody WbCls MdnrImpsz  Size EpaApprsz Unit 
    ##    <dbl>      <dbl> <dbl> <chr>     <chr>     <dbl> <dbl>     <dbl> <chr>
    ##  1  2014      60348  7309 Bee Tree… L3           10    10        10 Acres
    ##  2  2006      59691  7365 Belcher … L3           42    42        42 Acres
    ##  3  2014      54686  7003 Bowling … L1            7     7         7 Acres
    ##  4  2012      51313  7003 Bowling … L1            7     7         7 Acres
    ##  5  2012      51312  7003 Bowling … L1            7     7         7 Acres
    ##  6  2016      60247  7117 Buffalo … L3           45    45        45 Acres
    ##  7  2006      60020  7057 Busch W.… L3           51    51        51 Acres
    ##  8  2014      55954  7326 Clearwat… L2         1635  1635      1635 Acres
    ##  9  2002      60036  7326 Clearwat… L2         1635  1635      1635 Acres
    ## 10  2016      60037  7326 Clearwat… L2         1635  1635      1635 Acres
    ## # … with 76 more rows, and 19 more variables: Pollutant <chr>,
    ## #   Source <chr>, Iu <chr>, Ou <chr>, DwnX <dbl>, DwnY <dbl>,
    ## #   CountyUD <chr>, WbEpa <chr>, Comment <chr>, PermId <chr>,
    ## #   Eventdat <date>, Reachcode <chr>, Rchsmdate <lgl>, RchRes <lgl>,
    ## #   SrcDesc <chr>, FeatUrl <lgl>, ShapeLeng <dbl>, ShapeLe1 <dbl>,
    ## #   ShapeArea <dbl>

Once we have done some initial cleaning, there may be other variable
names we wish to change. It is useful to pipe these functions together,
cleaning variable names with `clean_names()` and then using `rename()`
afterwards to clean-up specific variable names. You can rename multiple
variables with `rename()` all at once as well, which this example does.

``` r
lakes %>%
  clean_names(case = "snake") %>%
  rename(
    year = yr,
    water_body_id = wbid) -> lakes_names

lakes_names
```

    ## # A tibble: 86 x 28
    ##     year businessid water_body_id water_body wb_cls mdnr_impsz  size
    ##    <dbl>      <dbl>         <dbl> <chr>      <chr>       <dbl> <dbl>
    ##  1  2014      60348          7309 Bee Tree … L3             10    10
    ##  2  2006      59691          7365 Belcher B… L3             42    42
    ##  3  2014      54686          7003 Bowling G… L1              7     7
    ##  4  2012      51313          7003 Bowling G… L1              7     7
    ##  5  2012      51312          7003 Bowling G… L1              7     7
    ##  6  2016      60247          7117 Buffalo B… L3             45    45
    ##  7  2006      60020          7057 Busch W.A… L3             51    51
    ##  8  2014      55954          7326 Clearwate… L2           1635  1635
    ##  9  2002      60036          7326 Clearwate… L2           1635  1635
    ## 10  2016      60037          7326 Clearwate… L2           1635  1635
    ## # … with 76 more rows, and 21 more variables: epa_apprsz <dbl>,
    ## #   unit <chr>, pollutant <chr>, source <chr>, iu <chr>, ou <chr>,
    ## #   dwn_x <dbl>, dwn_y <dbl>, county_u_d <chr>, wb_epa <chr>,
    ## #   comment <chr>, perm_id <chr>, eventdat <date>, reachcode <chr>,
    ## #   rchsmdate <lgl>, rch_res <lgl>, src_desc <chr>, feat_url <lgl>,
    ## #   shape_leng <dbl>, shape_le_1 <dbl>, shape_area <dbl>

I like to rename each individual variable on a separate line in the code
to make the changes I’m making crystal clear. Once I have names that I
want to keep, I’ll assign the results of my pipeline into a new object
(or an existing one) so that I can refer to those new names down the
road.

## Missing Data

Once we’ve got column names that are predictable and easy to work with,
we want to check for missing data. This can be done with the `naniar`
package’s `miss_var_summary()` function, which will provide us with a
tibble where each row is a variable. The count and percentage of missing
observations per variable is provided in the columns:

``` r
lakes_names %>%
  miss_var_summary()
```

    ## # A tibble: 28 x 3
    ##    variable      n_miss pct_miss
    ##    <chr>          <int>    <dbl>
    ##  1 rchsmdate         86   100   
    ##  2 rch_res           86   100   
    ##  3 feat_url          86   100   
    ##  4 reachcode         10    11.6 
    ##  5 perm_id            8     9.30
    ##  6 eventdat           7     8.14
    ##  7 ou                 4     4.65
    ##  8 year               0     0   
    ##  9 businessid         0     0   
    ## 10 water_body_id      0     0   
    ## # … with 18 more rows

When we have missing data, we have two choices. We can either try to
“recover it” (perhaps we can find another data source with the same
information), or we simply ignore it and move on. Sometimes, these are
superfluous columns (like `feat_url`) that are not necessary for the
work we are doing.

## Duplicates

Before we start removing columns, however, we want to look for duplicate
observations. We can do this with the `janitor` package’s `get_dupes()`
function:

``` r
lakes_names %>%
  get_dupes()
```

    ## No variable names specified - using all columns.

    ## No duplicate combinations found of: year, businessid, water_body_id, water_body, wb_cls, mdnr_impsz, size, epa_apprsz, unit, ... and 19 other variables

    ## # A tibble: 0 x 29
    ## # … with 29 variables: year <dbl>, businessid <dbl>, water_body_id <dbl>,
    ## #   water_body <chr>, wb_cls <chr>, mdnr_impsz <dbl>, size <dbl>,
    ## #   epa_apprsz <dbl>, unit <chr>, pollutant <chr>, source <chr>, iu <chr>,
    ## #   ou <chr>, dwn_x <dbl>, dwn_y <dbl>, county_u_d <chr>, wb_epa <chr>,
    ## #   comment <chr>, perm_id <chr>, eventdat <date>, reachcode <chr>,
    ## #   rchsmdate <lgl>, rch_res <lgl>, src_desc <chr>, feat_url <lgl>,
    ## #   shape_leng <dbl>, shape_le_1 <dbl>, shape_area <dbl>, dupe_count <int>

Output like this indicates that there are no true duplicates that have
identical information in every variable. This is a good start, but it
does not mean there are not duplicated observations. For instance, we
can check and see if the same body of water appears in more than one
row:

``` r
lakes_names %>%
  get_dupes(water_body)
```

    ## # A tibble: 45 x 29
    ##    water_body dupe_count  year businessid water_body_id wb_cls mdnr_impsz
    ##    <chr>           <int> <dbl>      <dbl>         <dbl> <chr>       <dbl>
    ##  1 Bowling G…          3  2014      54686          7003 L1              7
    ##  2 Bowling G…          3  2012      51313          7003 L1              7
    ##  3 Bowling G…          3  2012      51312          7003 L1              7
    ##  4 Clearwate…          3  2014      55954          7326 L2           1635
    ##  5 Clearwate…          3  2002      60036          7326 L2           1635
    ##  6 Clearwate…          3  2016      60037          7326 L2           1635
    ##  7 Crane Lake          2  2016      60023          7334 L3            109
    ##  8 Crane Lake          2  2016      60022          7334 L3            109
    ##  9 Forest La…          4  2010      51337          7151 L1            580
    ## 10 Forest La…          4  2016      60145          7151 L1            580
    ## # … with 35 more rows, and 22 more variables: size <dbl>,
    ## #   epa_apprsz <dbl>, unit <chr>, pollutant <chr>, source <chr>, iu <chr>,
    ## #   ou <chr>, dwn_x <dbl>, dwn_y <dbl>, county_u_d <chr>, wb_epa <chr>,
    ## #   comment <chr>, perm_id <chr>, eventdat <date>, reachcode <chr>,
    ## #   rchsmdate <lgl>, rch_res <lgl>, src_desc <chr>, feat_url <lgl>,
    ## #   shape_leng <dbl>, shape_le_1 <dbl>, shape_area <dbl>

It does\! When we identify this, we need to revisit what we think the
observational unit for these data are. We understandably may have
believed that each observation was for a single body of water, and that
is true (i.e. no *single* observation is for *multiple* bodies of
water), but we have bodies of water listed multiple times. We can test
this easily by creating a list of all of the unique bodies of water:

``` r
lakes_names %>%
  distinct(water_body)
```

    ## # A tibble: 55 x 1
    ##    water_body              
    ##    <chr>                   
    ##  1 Bee Tree Lake           
    ##  2 Belcher Branch Lake     
    ##  3 Bowling Green Lake - Old
    ##  4 Buffalo Bill Lake       
    ##  5 Busch W.A. No. 35 Lake  
    ##  6 Clearwater Lake         
    ##  7 Coot Lake               
    ##  8 Cottontail Lake         
    ##  9 Crane Lake              
    ## 10 Crowder St. Park Lake   
    ## # … with 45 more rows

We get a tibble that has 55 rows, which is shorter than our origianl
data frame with 86 observations. This tells us that there are duplicate
bodies of water.

In these sorts of situations, we want to explore our data further and
identify what differs between these columns. In this case, we have data
that are fundamentally observations of one pollutant per body of water.
So a body of water with multiple pollutants will have multiple rows.
This is complicated by data that differs in some ancillary variables
(like `perm_id`), and some data that appear to be for multiple features
(Mark Twin Lake, for example, appears to be constituted by two polygons
in these data). Getting to the bottom this can be complicated and can
take time.

Once we are ready to get rid of duplicated data, we can use the `dplyr`
`distinct()` function. The `.keep_all` argument is important, as it
ensures that we get all of the other columns in addition to the one we
specify:

``` r
lakes_names %>%
  distinct(water_body, .keep_all = TRUE) -> lakes_unique

lakes_unique
```

    ## # A tibble: 55 x 28
    ##     year businessid water_body_id water_body wb_cls mdnr_impsz  size
    ##    <dbl>      <dbl>         <dbl> <chr>      <chr>       <dbl> <dbl>
    ##  1  2014      60348          7309 Bee Tree … L3             10    10
    ##  2  2006      59691          7365 Belcher B… L3             42    42
    ##  3  2014      54686          7003 Bowling G… L1              7     7
    ##  4  2016      60247          7117 Buffalo B… L3             45    45
    ##  5  2006      60020          7057 Busch W.A… L3             51    51
    ##  6  2014      55954          7326 Clearwate… L2           1635  1635
    ##  7  2016      60336          7378 Coot Lake  L3             20    20
    ##  8  2016      60338          7379 Cottontai… L3             22    22
    ##  9  2016      60023          7334 Crane Lake L3            109   109
    ## 10  2016      60260          7135 Crowder S… L3             18    18
    ## # … with 45 more rows, and 21 more variables: epa_apprsz <dbl>,
    ## #   unit <chr>, pollutant <chr>, source <chr>, iu <chr>, ou <chr>,
    ## #   dwn_x <dbl>, dwn_y <dbl>, county_u_d <chr>, wb_epa <chr>,
    ## #   comment <chr>, perm_id <chr>, eventdat <date>, reachcode <chr>,
    ## #   rchsmdate <lgl>, rch_res <lgl>, src_desc <chr>, feat_url <lgl>,
    ## #   shape_leng <dbl>, shape_le_1 <dbl>, shape_area <dbl>

## Subsetting Columns

Once we have a data set with a predictable number of observations, we
can start to get rid of columns. This practice of whittling down our
data is known as “subsetting.” We always use the `select()` function
from `dplyr` for this. We can list one or more variables preceded by a
negative sign (i.e. `-`) to remove a column from our data:

``` r
lakes_unique %>%
  select(-year)
```

    ## # A tibble: 55 x 27
    ##    businessid water_body_id water_body wb_cls mdnr_impsz  size epa_apprsz
    ##         <dbl>         <dbl> <chr>      <chr>       <dbl> <dbl>      <dbl>
    ##  1      60348          7309 Bee Tree … L3             10    10         10
    ##  2      59691          7365 Belcher B… L3             42    42         42
    ##  3      54686          7003 Bowling G… L1              7     7          7
    ##  4      60247          7117 Buffalo B… L3             45    45         45
    ##  5      60020          7057 Busch W.A… L3             51    51         51
    ##  6      55954          7326 Clearwate… L2           1635  1635       1635
    ##  7      60336          7378 Coot Lake  L3             20    20         20
    ##  8      60338          7379 Cottontai… L3             22    22         22
    ##  9      60023          7334 Crane Lake L3            109   109        109
    ## 10      60260          7135 Crowder S… L3             18    18         18
    ## # … with 45 more rows, and 20 more variables: unit <chr>, pollutant <chr>,
    ## #   source <chr>, iu <chr>, ou <chr>, dwn_x <dbl>, dwn_y <dbl>,
    ## #   county_u_d <chr>, wb_epa <chr>, comment <chr>, perm_id <chr>,
    ## #   eventdat <date>, reachcode <chr>, rchsmdate <lgl>, rch_res <lgl>,
    ## #   src_desc <chr>, feat_url <lgl>, shape_leng <dbl>, shape_le_1 <dbl>,
    ## #   shape_area <dbl>

Alternatively, we can list variables without negative signs that we want
to retain:

``` r
lakes_unique %>%
  select(water_body_id, water_body, pollutant) -> lakes_subset_cols

lakes_subset_cols
```

    ## # A tibble: 55 x 3
    ##    water_body_id water_body               pollutant                 
    ##            <dbl> <chr>                    <chr>                     
    ##  1          7309 Bee Tree Lake            Mercury in Fish Tissue (T)
    ##  2          7365 Belcher Branch Lake      Mercury in Fish Tissue (T)
    ##  3          7003 Bowling Green Lake - Old Chlorophyll-a (W)         
    ##  4          7117 Buffalo Bill Lake        Mercury in Fish Tissue (T)
    ##  5          7057 Busch W.A. No. 35 Lake   Mercury in Fish Tissue (T)
    ##  6          7326 Clearwater Lake          Chlorophyll-a (W)         
    ##  7          7378 Coot Lake                Mercury in Fish Tissue (T)
    ##  8          7379 Cottontail Lake          Mercury in Fish Tissue (T)
    ##  9          7334 Crane Lake               Chlorophyll-a (W)         
    ## 10          7135 Crowder St. Park Lake    Mercury in Fish Tissue (T)
    ## # … with 45 more rows

As before, we write our data to a new object when we are happy with the
changes we have made.

Our data will be created in whatever order we list the columns in, so
re-ordering our list to put `pollutant` first will mean that pollutant
is now the first column in the data we have created.

``` r
lakes_subset_cols %>%
  select(pollutant, water_body_id, water_body)
```

    ## # A tibble: 55 x 3
    ##    pollutant                  water_body_id water_body              
    ##    <chr>                              <dbl> <chr>                   
    ##  1 Mercury in Fish Tissue (T)          7309 Bee Tree Lake           
    ##  2 Mercury in Fish Tissue (T)          7365 Belcher Branch Lake     
    ##  3 Chlorophyll-a (W)                   7003 Bowling Green Lake - Old
    ##  4 Mercury in Fish Tissue (T)          7117 Buffalo Bill Lake       
    ##  5 Mercury in Fish Tissue (T)          7057 Busch W.A. No. 35 Lake  
    ##  6 Chlorophyll-a (W)                   7326 Clearwater Lake         
    ##  7 Mercury in Fish Tissue (T)          7378 Coot Lake               
    ##  8 Mercury in Fish Tissue (T)          7379 Cottontail Lake         
    ##  9 Chlorophyll-a (W)                   7334 Crane Lake              
    ## 10 Mercury in Fish Tissue (T)          7135 Crowder St. Park Lake   
    ## # … with 45 more rows

## Subsetting Observations

We can also subset our data by observation. For instance, we can use the
`filter()` function from `dplyr` to return only observations that are
for lakes at or over 100 acres in size:

``` r
lakes_names %>% 
  filter(size >= 100)
```

    ## # A tibble: 45 x 28
    ##     year businessid water_body_id water_body wb_cls mdnr_impsz  size
    ##    <dbl>      <dbl>         <dbl> <chr>      <chr>       <dbl> <dbl>
    ##  1  2014      55954          7326 Clearwate… L2           1635  1635
    ##  2  2002      60036          7326 Clearwate… L2           1635  1635
    ##  3  2016      60037          7326 Clearwate… L2           1635  1635
    ##  4  2016      60023          7334 Crane Lake L3            109   109
    ##  5  2016      60022          7334 Crane Lake L3            109   109
    ##  6  2012      60105          7237 Fellows L… L1            800   800
    ##  7  2010      51337          7151 Forest La… L1            580   580
    ##  8  2016      60145          7151 Forest La… L1            580   580
    ##  9  2010      51335          7151 Forest La… L1            580   580
    ## 10  2010      51336          7151 Forest La… L1            580   580
    ## # … with 35 more rows, and 21 more variables: epa_apprsz <dbl>,
    ## #   unit <chr>, pollutant <chr>, source <chr>, iu <chr>, ou <chr>,
    ## #   dwn_x <dbl>, dwn_y <dbl>, county_u_d <chr>, wb_epa <chr>,
    ## #   comment <chr>, perm_id <chr>, eventdat <date>, reachcode <chr>,
    ## #   rchsmdate <lgl>, rch_res <lgl>, src_desc <chr>, feat_url <lgl>,
    ## #   shape_leng <dbl>, shape_le_1 <dbl>, shape_area <dbl>

Our `filter()` statement uses a **relational operator** (`>=`, i.e.
“greater than or equal to”), to identify observations to keep. For
each observation, it tests whether the `size` variable is greater than
or equal to `100`. This creates a series of implict `TRUE` or `FALSE`
results - the lake is either greater than or equal to `100` or it is
not. The lakes that are implicity identified as `TRUE` are then selected
and returned.

This is known as “boolean logic,” and is a core principle within
computer science. The relational operators that we can use in `R` are:

  - `>` - greater than
  - `>=` - greater than or equal to
  - `<` - less than
  - `<=` - less than or equal to
  - `==` - exactly equal to
  - `!-` - not equal to

The `==` (i.e. “exactly equal to”) operator is particularly useful for
selecting data that are character:

``` r
lakes_names %>% 
  filter(pollutant == "Mercury in Fish Tissue (T)")
```

    ## # A tibble: 58 x 28
    ##     year businessid water_body_id water_body wb_cls mdnr_impsz  size
    ##    <dbl>      <dbl>         <dbl> <chr>      <chr>       <dbl> <dbl>
    ##  1  2014      60348          7309 Bee Tree … L3             10    10
    ##  2  2006      59691          7365 Belcher B… L3             42    42
    ##  3  2016      60247          7117 Buffalo B… L3             45    45
    ##  4  2006      60020          7057 Busch W.A… L3             51    51
    ##  5  2002      60036          7326 Clearwate… L2           1635  1635
    ##  6  2016      60336          7378 Coot Lake  L3             20    20
    ##  7  2016      60338          7379 Cottontai… L3             22    22
    ##  8  2016      60260          7135 Crowder S… L3             18    18
    ##  9  2002      59683          7015 Deer Ridg… L3             39    39
    ## 10  2012      60105          7237 Fellows L… L1            800   800
    ## # … with 48 more rows, and 21 more variables: epa_apprsz <dbl>,
    ## #   unit <chr>, pollutant <chr>, source <chr>, iu <chr>, ou <chr>,
    ## #   dwn_x <dbl>, dwn_y <dbl>, county_u_d <chr>, wb_epa <chr>,
    ## #   comment <chr>, perm_id <chr>, eventdat <date>, reachcode <chr>,
    ## #   rchsmdate <lgl>, rch_res <lgl>, src_desc <chr>, feat_url <lgl>,
    ## #   shape_leng <dbl>, shape_le_1 <dbl>, shape_area <dbl>

We can combine our two filter statements into one if we wanted to select
large lakes with mercury in fish tissue by using a **logical operator**:

``` r
lakes_names %>% 
  filter(size >= 100 & pollutant == "Mercury in Fish Tissue (T)")
```

    ## # A tibble: 27 x 28
    ##     year businessid water_body_id water_body wb_cls mdnr_impsz  size
    ##    <dbl>      <dbl>         <dbl> <chr>      <chr>       <dbl> <dbl>
    ##  1  2002      60036          7326 Clearwate… L2          1635  1635 
    ##  2  2012      60105          7237 Fellows L… L1           800   800 
    ##  3  2016      60145          7151 Forest La… L1           580   580 
    ##  4  2014      60245          7386 Harrison … L1           280   280 
    ##  5  2008      60129          7152 Hazel Cre… L1           453   453 
    ##  6  2016      60068          7193 Holden Ci… L1           290.  290.
    ##  7  2012      59887          7029 Hunnewell… L3           228   228 
    ##  8  2012      59887          7029 Hunnewell… L3           228   228 
    ##  9  2012      59887          7029 Hunnewell… L3           228   228 
    ## 10  2012      59887          7029 Hunnewell… L3           228   228 
    ## # … with 17 more rows, and 21 more variables: epa_apprsz <dbl>,
    ## #   unit <chr>, pollutant <chr>, source <chr>, iu <chr>, ou <chr>,
    ## #   dwn_x <dbl>, dwn_y <dbl>, county_u_d <chr>, wb_epa <chr>,
    ## #   comment <chr>, perm_id <chr>, eventdat <date>, reachcode <chr>,
    ## #   rchsmdate <lgl>, rch_res <lgl>, src_desc <chr>, feat_url <lgl>,
    ## #   shape_leng <dbl>, shape_le_1 <dbl>, shape_area <dbl>

We use the `&` operator to create a conditon where there are two boolean
tests (one for `size` and one for `pollutant`) that **both** must be
`TRUE`. We can also use `|` as well to create a situation where one test
or the other must be true.

## Putting Things Together

If we wanted to get a list of all the large lakes that have mercury as a
pollutant, we could combine this with our `distinct` function:

``` r
lakes_names %>%
  filter(size >= 100 & pollutant == "Mercury in Fish Tissue (T)") %>%
  distinct(water_body)
```

    ## # A tibble: 19 x 1
    ##    water_body                 
    ##    <chr>                      
    ##  1 Clearwater Lake            
    ##  2 Fellows Lake               
    ##  3 Forest Lake                
    ##  4 Harrison County Lake       
    ##  5 Hazel Creek Lake           
    ##  6 Holden City Lake           
    ##  7 Hunnewell Lake             
    ##  8 Indian Creek Community Lake
    ##  9 Lake Paho                  
    ## 10 Lake St. Louis             
    ## 11 Lake Winnebago             
    ## 12 Longview Lake              
    ## 13 Maple Leaf Lake            
    ## 14 Mark Twain Lake            
    ## 15 Mozingo Lake               
    ## 16 Norfork Lake               
    ## 17 Palmer Lake                
    ## 18 Sugar Creek Lake           
    ## 19 Weatherby Lake

We get a `tibble` with the 19 lake names that are both 100 acres or
larger and have mercury as a pollutant.

We can extend this logic further, by combining all of our renaming and
`select()` statements into a single pipeline. Remember that we can read
the pipeline like so:

1.  First we take the `lakes` data, **then**
2.  we clean all of the variable names to snake\_case, **then**
3.  we rename two of the variables further, **then**
4.  we subset columns to clarify the characteristics we are interested
    in, **then**
5.  we subset observations to retain only the lakes that are 100 acres
    or greater in size, **and**
6.  we assign those changes to a new object called `lakes_large`.

<!-- end list -->

``` r
lakes %>% 
  clean_names(case = "snake") %>%
  rename(
    year = yr,
    water_body_id = wbid
    ) %>%
  select(water_body_id, water_body, size, unit, pollutant) %>%
  filter(size >= 100) -> lakes_large

lakes_large
```

    ## # A tibble: 45 x 5
    ##    water_body_id water_body       size unit  pollutant                 
    ##            <dbl> <chr>           <dbl> <chr> <chr>                     
    ##  1          7326 Clearwater Lake  1635 Acres Chlorophyll-a (W)         
    ##  2          7326 Clearwater Lake  1635 Acres Mercury in Fish Tissue (T)
    ##  3          7326 Clearwater Lake  1635 Acres Phosphorus, Total (W)     
    ##  4          7334 Crane Lake        109 Acres Chlorophyll-a (W)         
    ##  5          7334 Crane Lake        109 Acres Phosphorus, Total (W)     
    ##  6          7237 Fellows Lake      800 Acres Mercury in Fish Tissue (T)
    ##  7          7151 Forest Lake       580 Acres Chlorophyll-a (W)         
    ##  8          7151 Forest Lake       580 Acres Mercury in Fish Tissue (T)
    ##  9          7151 Forest Lake       580 Acres Nitrogen, Total (W)       
    ## 10          7151 Forest Lake       580 Acres Phosphorus, Total (W)     
    ## # … with 35 more rows

## Creating and Modifying Variables

Often we want to create new variables as well. The `mutate()` function
from `dplyr` can be used to both create new variables and edit existing
ones.

### Modifying Existing Variables

For instance, we often want to convert variables to character from
numeric using `as.character()`:

``` r
lakes_large %>%
  mutate(size = as.character(size)) -> lakes_chr

lakes_chr
```

    ## # A tibble: 45 x 5
    ##    water_body_id water_body      size  unit  pollutant                 
    ##            <dbl> <chr>           <chr> <chr> <chr>                     
    ##  1          7326 Clearwater Lake 1635  Acres Chlorophyll-a (W)         
    ##  2          7326 Clearwater Lake 1635  Acres Mercury in Fish Tissue (T)
    ##  3          7326 Clearwater Lake 1635  Acres Phosphorus, Total (W)     
    ##  4          7334 Crane Lake      109   Acres Chlorophyll-a (W)         
    ##  5          7334 Crane Lake      109   Acres Phosphorus, Total (W)     
    ##  6          7237 Fellows Lake    800   Acres Mercury in Fish Tissue (T)
    ##  7          7151 Forest Lake     580   Acres Chlorophyll-a (W)         
    ##  8          7151 Forest Lake     580   Acres Mercury in Fish Tissue (T)
    ##  9          7151 Forest Lake     580   Acres Nitrogen, Total (W)       
    ## 10          7151 Forest Lake     580   Acres Phosphorus, Total (W)     
    ## # … with 35 more rows

The size variable is now character instead of numeric, which can be
useful when writing data to shapefiles (which do not like numbers over a
certain size, an issue that crops up with large areas measured in square
meters or feet).

We can convert our data back with `as.numeric()`:

``` r
lakes_chr %>%
  mutate(size = as.numeric(size))
```

    ## # A tibble: 45 x 5
    ##    water_body_id water_body       size unit  pollutant                 
    ##            <dbl> <chr>           <dbl> <chr> <chr>                     
    ##  1          7326 Clearwater Lake  1635 Acres Chlorophyll-a (W)         
    ##  2          7326 Clearwater Lake  1635 Acres Mercury in Fish Tissue (T)
    ##  3          7326 Clearwater Lake  1635 Acres Phosphorus, Total (W)     
    ##  4          7334 Crane Lake        109 Acres Chlorophyll-a (W)         
    ##  5          7334 Crane Lake        109 Acres Phosphorus, Total (W)     
    ##  6          7237 Fellows Lake      800 Acres Mercury in Fish Tissue (T)
    ##  7          7151 Forest Lake       580 Acres Chlorophyll-a (W)         
    ##  8          7151 Forest Lake       580 Acres Mercury in Fish Tissue (T)
    ##  9          7151 Forest Lake       580 Acres Nitrogen, Total (W)       
    ## 10          7151 Forest Lake       580 Acres Phosphorus, Total (W)     
    ## # … with 35 more rows

Now we have numeric data again if we were to write this object to our
global enviornment.

### Creating New Variables

One way common way we create variables is to recode them into a binary
outcome. For instance, we may want to make two mapes - one of lakes that
are between 100 acres and 999 acres, and one of lakes that are 1,000
acres or greater. We can identify these features with a logical variable
that is `TRUE` if the lake is `1000` acres or greater and `FALSE`
otherwise. We use the `ifelse()` function to create an expression we
test in a boolean fashion along with what we return if the test is
`TRUE` (in this case, `TRUE`), and what we return if the test is `FALSE`
(in this case, `FALSE`):

``` r
lakes_large %>%
  mutate(vlarge = ifelse(size >= 1000, TRUE, FALSE)) %>%
  select(water_body, size, vlarge)
```

    ## # A tibble: 45 x 3
    ##    water_body       size vlarge
    ##    <chr>           <dbl> <lgl> 
    ##  1 Clearwater Lake  1635 TRUE  
    ##  2 Clearwater Lake  1635 TRUE  
    ##  3 Clearwater Lake  1635 TRUE  
    ##  4 Crane Lake        109 FALSE 
    ##  5 Crane Lake        109 FALSE 
    ##  6 Fellows Lake      800 FALSE 
    ##  7 Forest Lake       580 FALSE 
    ##  8 Forest Lake       580 FALSE 
    ##  9 Forest Lake       580 FALSE 
    ## 10 Forest Lake       580 FALSE 
    ## # … with 35 more rows

I’ve subset the columns afterwards just to illustrate how this looks
clearly - you would only want to emulate this if you really only wanted
those three variables\!

We can do the same thing, but return characters instead:

``` r
lakes_large %>%
  mutate(vlarge = ifelse(size >= 1000, "Over 1000 acres", "Under 1000 acres")) %>%
  select(water_body, size, vlarge)
```

    ## # A tibble: 45 x 3
    ##    water_body       size vlarge          
    ##    <chr>           <dbl> <chr>           
    ##  1 Clearwater Lake  1635 Over 1000 acres 
    ##  2 Clearwater Lake  1635 Over 1000 acres 
    ##  3 Clearwater Lake  1635 Over 1000 acres 
    ##  4 Crane Lake        109 Under 1000 acres
    ##  5 Crane Lake        109 Under 1000 acres
    ##  6 Fellows Lake      800 Under 1000 acres
    ##  7 Forest Lake       580 Under 1000 acres
    ##  8 Forest Lake       580 Under 1000 acres
    ##  9 Forest Lake       580 Under 1000 acres
    ## 10 Forest Lake       580 Under 1000 acres
    ## # … with 35 more rows

In both cases, the expression `size >= 1000` is our boolean test. If the
observation is `TRUE` with this test, either `TRUE` or `"Over 1000
acres"` is returned. Likewise, if the observation is `FALSE` with this
test, either `FALSE` or `"Under 1000 acres"`.

We can do the same with our test for mercury in fish:

``` r
lakes_large %>%
  mutate(mercury = ifelse(pollutant == "Mercury in Fish Tissue (T)", TRUE, FALSE)) %>%
  select(water_body, mercury)
```

    ## # A tibble: 45 x 2
    ##    water_body      mercury
    ##    <chr>           <lgl>  
    ##  1 Clearwater Lake FALSE  
    ##  2 Clearwater Lake TRUE   
    ##  3 Clearwater Lake FALSE  
    ##  4 Crane Lake      FALSE  
    ##  5 Crane Lake      FALSE  
    ##  6 Fellows Lake    TRUE   
    ##  7 Forest Lake     FALSE  
    ##  8 Forest Lake     TRUE   
    ##  9 Forest Lake     FALSE  
    ## 10 Forest Lake     FALSE  
    ## # … with 35 more rows

If we have a lot of string data, it can be useful to look for specific
words, like “Mercury”. We can do this with the `str_detect()` function
from `stringr`, which again creates a boolean test to identify whether
patterns exist in a given string:

``` r
lakes_large %>%
  mutate(mercury = ifelse(str_detect(pollutant, pattern = "Mercury"), TRUE, FALSE)) %>%
  select(water_body, pollutant, mercury)
```

    ## # A tibble: 45 x 3
    ##    water_body      pollutant                  mercury
    ##    <chr>           <chr>                      <lgl>  
    ##  1 Clearwater Lake Chlorophyll-a (W)          FALSE  
    ##  2 Clearwater Lake Mercury in Fish Tissue (T) TRUE   
    ##  3 Clearwater Lake Phosphorus, Total (W)      FALSE  
    ##  4 Crane Lake      Chlorophyll-a (W)          FALSE  
    ##  5 Crane Lake      Phosphorus, Total (W)      FALSE  
    ##  6 Fellows Lake    Mercury in Fish Tissue (T) TRUE   
    ##  7 Forest Lake     Chlorophyll-a (W)          FALSE  
    ##  8 Forest Lake     Mercury in Fish Tissue (T) TRUE   
    ##  9 Forest Lake     Nitrogen, Total (W)        FALSE  
    ## 10 Forest Lake     Phosphorus, Total (W)      FALSE  
    ## # … with 35 more rows

We can make this even more complicated if there are two words we want to
search for. For instance, we could look for both “Phosphorus” or
“Nitrogen” to be present:

``` r
lakes_large %>%
  mutate(phosNitro = ifelse(str_detect(pollutant, pattern = "Phosphorus|Nitrogen"), TRUE, FALSE)) %>%
  select(water_body, pollutant, phosNitro)
```

    ## # A tibble: 45 x 3
    ##    water_body      pollutant                  phosNitro
    ##    <chr>           <chr>                      <lgl>    
    ##  1 Clearwater Lake Chlorophyll-a (W)          FALSE    
    ##  2 Clearwater Lake Mercury in Fish Tissue (T) FALSE    
    ##  3 Clearwater Lake Phosphorus, Total (W)      TRUE     
    ##  4 Crane Lake      Chlorophyll-a (W)          FALSE    
    ##  5 Crane Lake      Phosphorus, Total (W)      TRUE     
    ##  6 Fellows Lake    Mercury in Fish Tissue (T) FALSE    
    ##  7 Forest Lake     Chlorophyll-a (W)          FALSE    
    ##  8 Forest Lake     Mercury in Fish Tissue (T) FALSE    
    ##  9 Forest Lake     Nitrogen, Total (W)        TRUE     
    ## 10 Forest Lake     Phosphorus, Total (W)      TRUE     
    ## # … with 35 more rows

Just like before, we use the `|` as a logical operator for the word
“or”. Any time either of these words are found, we return a value of
`TRUE`.

Another way to recode variables is to take long strings and shorten
them. We’ll use `distinct()` to get a list of the pollutants in our
data:

``` r
lakes_large %>%
  distinct(pollutant)
```

    ## # A tibble: 5 x 1
    ##   pollutant                                   
    ##   <chr>                                       
    ## 1 Chlorophyll-a (W)                           
    ## 2 Mercury in Fish Tissue (T)                  
    ## 3 Phosphorus, Total (W)                       
    ## 4 Nitrogen, Total (W)                         
    ## 5 Nutrient/Eutrophication Biol. Indicators (W)

If we want to keep all of these data but create shorthand references for
them, we can use `case_when()` combined with `mutate()` to specify how
each pollutant should be simplified:

``` r
lakes_large %>% 
  mutate(pollutant_simple = case_when(
    pollutant == "Chlorophyll-a (W)" ~ "Chlorophyll",
    pollutant == "Mercury in Fish Tissue (T)" ~ "Mercury",
    pollutant == "Phosphorus, Total (W)" ~ "Phosphorus",
    pollutant == "Nitrogen, Total (W)"  ~ "Nitrogen",
    pollutant == "Nutrient/Eutrophication Biol. Indicators (W)"  ~ "Eutrophication"
    ))
```

    ## # A tibble: 45 x 6
    ##    water_body_id water_body     size unit  pollutant       pollutant_simple
    ##            <dbl> <chr>         <dbl> <chr> <chr>           <chr>           
    ##  1          7326 Clearwater L…  1635 Acres Chlorophyll-a … Chlorophyll     
    ##  2          7326 Clearwater L…  1635 Acres Mercury in Fis… Mercury         
    ##  3          7326 Clearwater L…  1635 Acres Phosphorus, To… Phosphorus      
    ##  4          7334 Crane Lake      109 Acres Chlorophyll-a … Chlorophyll     
    ##  5          7334 Crane Lake      109 Acres Phosphorus, To… Phosphorus      
    ##  6          7237 Fellows Lake    800 Acres Mercury in Fis… Mercury         
    ##  7          7151 Forest Lake     580 Acres Chlorophyll-a … Chlorophyll     
    ##  8          7151 Forest Lake     580 Acres Mercury in Fis… Mercury         
    ##  9          7151 Forest Lake     580 Acres Nitrogen, Tota… Nitrogen        
    ## 10          7151 Forest Lake     580 Acres Phosphorus, To… Phosphorus      
    ## # … with 35 more rows

The strings after the tilde (`~`) are what will be returned as new
values any time an old value is detected. Notice again how the exactly
equal to relational operator (i.e. `==`) is used here\!

## Grouping and Summarizing Observations

Since we have bodies of water in multiple observations, we may want to
summarize them. For instance, we could ask how many distinct pollutants
are listed per body of water, and whether or not mercury is one of them.
The following pipeline groups our data and then summarizes it to answer
these two questions.

We:

1.  Take the `lakes_large` data, **then**
2.  we create a list of distinct combinations of lake names and
    pollutants, **then**
3.  create our binary indicator for the presence of mercury, **then**
4.  group our observations by body of water, **then**
5.  create new summary variables that count the number of pollutants per
    bodyof water and test whether any of the values for `mercury` for
    each body of water are `TRUE`, **then**
6.  re-order the output in descending order (the role of `desc()`) so
    the lake with the largest number of pollutants is listed first.

<!-- end list -->

``` r
lakes_large %>%
  distinct(water_body, pollutant) %>%
  mutate(mercury = ifelse(pollutant == "Mercury in Fish Tissue (T)", TRUE, FALSE)) %>%
  group_by(water_body) %>%
  summarize(
    pollutant_count = n(),
    mercury = any(mercury)) %>%
  arrange(desc(pollutant_count))
```

    ## # A tibble: 22 x 3
    ##    water_body           pollutant_count mercury
    ##    <chr>                          <int> <lgl>  
    ##  1 Forest Lake                        4 TRUE   
    ##  2 Weatherby Lake                     4 TRUE   
    ##  3 Clearwater Lake                    3 TRUE   
    ##  4 Table Rock Lake                    3 FALSE  
    ##  5 Crane Lake                         2 FALSE  
    ##  6 Hazel Creek Lake                   2 TRUE   
    ##  7 Fellows Lake                       1 TRUE   
    ##  8 Harrison County Lake               1 TRUE   
    ##  9 Holden City Lake                   1 TRUE   
    ## 10 Hunnewell Lake                     1 TRUE   
    ## # … with 12 more rows
