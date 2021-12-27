Compare DEP and FOCB Nutrient Data
================
Curtis C. Bohlen, Casco Bay Estuary Partnership.
06/07/2021

-   [Load Libraries](#load-libraries)
-   [Folder References](#folder-references)
-   [Load Data](#load-data)
    -   [DEP Data](#dep-data)
    -   [FOCB Data](#focb-data)
-   [Compare Data](#compare-data)

<img
    src="https://www.cascobayestuary.org/wp-content/uploads/2014/04/logo_sm.jpg"
    style="position:absolute;top:10px;right:50px;" />

\#Introduction This repository was created only to compare nutrient data
received from Friends of Casco Bay and from Maine DEP, to ensure that we
are not double counting nutrient samples. Accordingly, there is no
“Original\_Data” folder for this repository. All data was derived from
sources in “DEP\_nutrients” and “FOCB\_Nutrients” repositories, and
copied here.

“DEP\_nutrients” -&gt; ‘dep\_nutrient\_data.csv’ “FOCB\_Nutrients” -&gt;
‘focb\_n\_data\_strict.csv’

We use a simple strategy, using `inner_join()` to check for samples that
match with regards to date and site, and examine the TN and NOx data
from the two sources.

We find only four samples where DEP and FOCB sampled the same location
on the same dates, and reported TN and NOx values do not match. We
conclude that these are independent data sources.

# Load Libraries

``` r
library(tidyverse)
#> Warning: package 'tidyverse' was built under R version 4.0.5
#> -- Attaching packages --------------------------------------- tidyverse 1.3.1 --
#> v ggplot2 3.3.3     v purrr   0.3.4
#> v tibble  3.1.2     v dplyr   1.0.6
#> v tidyr   1.1.3     v stringr 1.4.0
#> v readr   1.4.0     v forcats 0.5.1
#> Warning: package 'tidyr' was built under R version 4.0.5
#> Warning: package 'dplyr' was built under R version 4.0.5
#> Warning: package 'forcats' was built under R version 4.0.5
#> -- Conflicts ------------------------------------------ tidyverse_conflicts() --
#> x dplyr::filter() masks stats::filter()
#> x dplyr::lag()    masks stats::lag()

library(GGally)
#> Warning: package 'GGally' was built under R version 4.0.5
#> Registered S3 method overwritten by 'GGally':
#>   method from   
#>   +.gg   ggplot2

library(CBEPgraphics)
load_cbep_fonts()
theme_set(theme_cbep())
```

# Folder References

``` r
sibfldnm <- 'Derived_Data'
parent <- dirname(getwd())
sibling <- file.path(parent,sibfldnm)
```

# Load Data

## DEP Data

``` r
fn <- 'dep_nutrient_data.csv'
dep_data <- read_csv(file.path(sibling, fn )) %>%
  select(site, dt, depth, nox_n, tn) %>%
  filter(! is.na(tn) & ! is.na(nox_n)) %>%
  mutate(source = 'DEP') %>%
  relocate(source)
#> 
#> -- Column specification --------------------------------------------------------
#> cols(
#>   .default = col_logical(),
#>   site_name = col_character(),
#>   site = col_character(),
#>   depth_designation = col_character(),
#>   dt = col_date(format = ""),
#>   month = col_character(),
#>   year = col_double(),
#>   time = col_time(format = ""),
#>   hour = col_double(),
#>   depth = col_double(),
#>   chl = col_double(),
#>   phaeo = col_double(),
#>   nox_n = col_double(),
#>   nh4_n = col_double(),
#>   tn = col_double(),
#>   op_p = col_double(),
#>   tp = col_double(),
#>   tss = col_double(),
#>   `Sample Comments` = col_character(),
#>   `Validation Comments` = col_character(),
#>   Latitude = col_double()
#>   # ... with 1 more columns
#> )
#> i Use `spec()` for the full column specifications.
```

## FOCB Data

``` r
fn <- 'focb_n_data_strict.csv'
focb_data <- read_csv(file.path(sibling, fn )) %>%
  select(station, dt, tn_depth, din_depth, nox_N, tn) %>%
  filter(! is.na(tn) & ! is.na(nox_N)) %>%
  rename(site = station) %>%
  mutate(source = 'FOCB') %>%
  relocate(source)
#> 
#> -- Column specification --------------------------------------------------------
#> cols(
#>   station = col_character(),
#>   dt = col_datetime(format = ""),
#>   year = col_double(),
#>   yearf = col_double(),
#>   month = col_logical(),
#>   doy = col_double(),
#>   tn_depth = col_double(),
#>   din_depth = col_double(),
#>   tn = col_double(),
#>   nox = col_double(),
#>   nh4 = col_double(),
#>   din = col_double(),
#>   din_N = col_double(),
#>   nox_N = col_double(),
#>   nh4_N = col_double(),
#>   organic_N = col_double()
#> )
```

# Compare Data

``` r
mixed_data <- dep_data %>%
  inner_join(focb_data, by = c('site', 'dt'))
mixed_data %>%
  select(site, dt, nox_n, nox_N, tn.x, tn.y)
#> # A tibble: 4 x 6
#>   site  dt                  nox_n   nox_N  tn.x  tn.y
#>   <chr> <dttm>              <dbl>   <dbl> <dbl> <dbl>
#> 1 BMR02 2018-06-25 00:00:00 0.012 0.00700 0.423  0.23
#> 2 BMR02 2018-07-18 00:00:00 0.045 0.0164  0.387  0.36
#> 3 EEB18 2018-06-25 00:00:00 0.023 0.00336 0.314  0.28
#> 4 EEB18 2018-07-18 00:00:00 0.041 0.0178  0.294  0.41
```

So the FOCB and DEP data only overlap on four days and locations, and
the underlying data values are not the same, suggesting these are truly
independent samples, despite overlaps of names.
