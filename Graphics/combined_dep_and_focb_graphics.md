Graphics for DEP and FOCB Nutrient Data
================
Curtis C. Bohlen, Casco Bay Estuary Partnership.
06/07/2021

-   [Introduction](#introduction)
-   [Load Libraries](#load-libraries)
-   [Folder References](#folder-references)
-   [Load Data](#load-data)
-   [Remove Questionable Extreme
    Values](#remove-questionable-extreme-values)
-   [Add Location, Site Names and
    Regions](#add-location-site-names-and-regions)
-   [Data Summary](#data-summary)
    -   [TN Results](#tn-results)
    -   [DIN Results](#din-results)
-   [Save Results](#save-results)
-   [Graphics](#graphics)
    -   [TN Graphic by TN](#tn-graphic-by-tn)
        -   [Order Sites in Rank Order of
            TN](#order-sites-in-rank-order-of-tn)
        -   [Create Base Plot](#create-base-plot)
    -   [TN Graphic By Region](#tn-graphic-by-region)
        -   [Order Sites in Region](#order-sites-in-region)
    -   [With Labels, Not Legend](#with-labels-not-legend)
        -   [Calculate Y Coordinates](#calculate-y-coordinates)
        -   [Add Labels](#add-labels)

<img
    src="https://www.cascobayestuary.org/wp-content/uploads/2014/04/logo_sm.jpg"
    style="position:absolute;top:10px;right:50px;" />

# Introduction

This repository was created to compare nutrient data received from
Friends of Casco Bay and from Maine DEP. initially, this was to to
ensure that we are not double counting nutrient samples. When we
discovered that there is little or no sample overlap between the two
data sources, we extended the archive to combine data from both sources
into one data set for display purposes.

There is no “Original\_Data” folder for this repository. All data was
derived from sources in “DEP\_nutrients” and “FOCB\_Nutrients”
repositories, and copied here.

“DEP\_nutrients” -&gt; ‘dep\_nutrient\_data.csv’ “FOCB\_Nutrients” -&gt;
‘focb\_n\_data\_strict.csv’

In this notebook, we use a simple strategy, using `inner_join()` to
check for samples that match with regards to date and site, and examine
the TN and NOx data from the two sources to see if they are similar.

We find only four samples where DEP and FOCB sampled the same location
on the same dates. Reported TN and NOx values do not match. We conclude
that these are independent data sources.

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

dir.create(file.path(getwd(), 'figures'), showWarnings = FALSE)
```

# Load Data

``` r
fn <- 'combined_surface_data.csv'

surface_data <- read_csv(file.path(sibling, fn )) %>%
  mutate(source = factor(source), 
         site = factor(site),
         month = factor(month, levels = month.abb),
         parameter = factor(parameter))
#> 
#> -- Column specification --------------------------------------------------------
#> cols(
#>   source = col_character(),
#>   site = col_character(),
#>   dt = col_datetime(format = ""),
#>   year = col_double(),
#>   month = col_character(),
#>   doy = col_double(),
#>   parameter = col_character(),
#>   concentration = col_double()
#> )
```

# Remove Questionable Extreme Values

We remove two extreme values from the FOCB TN data. While these values
were not flagged as errors in the source data, they appear likely to
represent errors based on QC review. Details of teh QA/QC review are in
the archve describing initial analysis of the FOCB nutrients data.

``` r
surface_data <- surface_data %>%
  filter(! (parameter == 'TN' & concentration > 1.5))
```

# Add Location, Site Names and Regions

``` r
fn <- 'combined_locations.csv'

locations <- read_csv(file.path(sibling, fn)) %>%
  mutate(source = factor(source), 
         site = factor(site)) %>%
  rename(loc_sources = source)
#> 
#> -- Column specification --------------------------------------------------------
#> cols(
#>   site = col_character(),
#>   site_name = col_character(),
#>   short_name = col_character(),
#>   latitude = col_double(),
#>   longitude = col_double(),
#>   source = col_character(),
#>   region = col_character()
#> )
```

``` r
surface_data <- surface_data %>%
  left_join(locations, by = 'site') %>%
  relocate(site_name, short_name, region, loc_sources, .after = site)
```

# Data Summary

There are a handful of replicate or near replicate values, or at least
samples collected at the same date and site. Al lare in the FOCB data,
and probably only represent data nominally collected from different
depths (DIN and TN depths were often recorded differently). To address
that, we average across all samples collected at one site and date by
each organization BEFORE we calculate descriptive statistics.

``` r
surface_results <- surface_data %>%
  relocate(latitude, longitude, .after = region) %>%
  pivot_wider(id_cols = source:doy, 
              names_from = parameter, 
              values_from = concentration,
              values_fn = function(x) mean(x, na.rm = TRUE)) %>%
  group_by(site, short_name, region, loc_sources, latitude, longitude) %>%
  summarize(across(.cols = c(DIN, TN),
                   .fns = c(mn = ~ mean(.x, na.rm = TRUE),
                            sd = ~ sd(.x, na.rm = TRUE), 
                            n = ~sum(! is.na(.x)),
                            md = ~ median(.x, na.rm = TRUE),
                            iqr = ~ IQR(.x, na.rm = TRUE),
                            p90 = ~ quantile(.x, .9, na.rm = TRUE),
                            gm = ~ exp(mean(log(.x), na.rm = TRUE)))),
            .groups = 'drop') %>%
  mutate(site = fct_reorder(factor(site), TN_md),
         short_name = fct_reorder(factor(short_name), TN_md),
         region = factor(region))
```

## TN Results

``` r
surface_results %>%
  select(site, short_name, contains('TN'))
#> # A tibble: 40 x 9
#>    site    short_name        TN_mn  TN_sd  TN_n TN_md TN_iqr TN_p90 TN_gm
#>    <fct>   <fct>             <dbl>  <dbl> <int> <dbl>  <dbl>  <dbl> <dbl>
#>  1 BMR02   B&M Railroad      0.345 0.186     22 0.294 0.162   0.456 0.314
#>  2 CBPR    Presumpscot Mouth 0.294 0.0863    21 0.289 0.083   0.4   0.282
#>  3 CMS1    Cousins Island    0.220 0.0862     9 0.179 0.0541  0.287 0.210
#>  4 CR-31   Cousins 2         0.444 0.236      7 0.391 0.298   0.766 0.396
#>  5 CR00    Cousins 1         0.65  0.193      6 0.691 0.274   0.837 0.622
#>  6 CRTRIB0 Cousins Trib      0.770 0.351      6 0.714 0.429   1.15  0.706
#>  7 EEB18   East End Beach    0.316 0.134     26 0.282 0.133   0.480 0.293
#>  8 FR09    Fore River 9      0.318 0.123     24 0.292 0.120   0.422 0.300
#>  9 HR01    Harraseeket A     0.335 0.0965     7 0.359 0.0925  0.418 0.320
#> 10 HR02    Harraseeket C     0.389 0.153      7 0.311 0.220   0.587 0.364
#> # ... with 30 more rows
```

## DIN Results

``` r
surface_results %>%
  select(site, short_name, contains('DIN'))
#> # A tibble: 40 x 9
#>    site    short_name        DIN_mn DIN_sd DIN_n DIN_md DIN_iqr DIN_p90 DIN_gm
#>    <fct>   <fct>              <dbl>  <dbl> <int>  <dbl>   <dbl>   <dbl>  <dbl>
#>  1 BMR02   B&M Railroad      0.257  0.852     22 0.0594 0.0431   0.111  0.0695
#>  2 CBPR    Presumpscot Mouth 0.269  0.867     21 0.047  0.056    0.137  0.0708
#>  3 CMS1    Cousins Island    0.0206 0.0296     9 0.0107 0.00667  0.0346 0.0131
#>  4 CR-31   Cousins 2         0.174  0.138      7 0.114  0.156    0.371  0.133 
#>  5 CR00    Cousins 1         0.312  0.163      6 0.322  0.213    0.484  0.271 
#>  6 CRTRIB0 Cousins Trib      0.216  0.209      6 0.138  0.34     0.476  0.125 
#>  7 EEB18   East End Beach    0.230  0.781     26 0.0675 0.0585   0.119  0.0694
#>  8 FR09    Fore River 9      0.0920 0.110     24 0.0620 0.0618   0.129  0.0591
#>  9 HR01    Harraseeket A     0.0547 0.0408     7 0.045  0.0190   0.0906 0.0442
#> 10 HR02    Harraseeket C     0.0599 0.0264     7 0.073  0.0385   0.0834 0.0539
#> # ... with 30 more rows
```

# Save Results

``` r
fn <- 'surface_recent_results.csv'
write_csv(surface_results, file.path(sibling, fn))
```

# Graphics

## TN Graphic by TN

### Order Sites in Rank Order of TN

``` r
surface_data <- surface_data %>%
mutate(site = factor(site, levels = levels(surface_results$site)),
       short_name = factor(short_name, 
                           levels = levels(surface_results$short_name)))
```

### Create Base Plot

``` r
plt <- surface_data %>%
  filter(parameter == 'TN') %>%
  ggplot(aes(concentration, short_name)) +
  
  geom_point(aes(fill = region), shape = 21, size = 2) +
  #stat_summary(geom = 'point', fun = median, shape = 3, size = 2.5) +
  
  scale_fill_brewer(palette = 'Accent', name = '') +
  
  ylab('') +
  xlab('Total Nitrogen (mg/l)') +
  theme_cbep(base_size = 12) +
  theme(legend.position = c(.80, .25),
        legend.text = element_text(size = 9),
        legend.key.height = unit(5, 'pt'),
        plot.margin  = unit(c(8,24,8,8), 'pt'),
        plot.title = element_text(hjust = 0.5))
```

``` r
plt + 
  geom_vline(xintercept = 0.32, color = cbep_colors()[3], lty = 2) +
  geom_text(aes(x = 0.35, y = 1, label = '0.32'), 
            angle = 90, hjust = 0, size = 3,
                color = cbep_colors()[3]) +
  geom_vline(xintercept = 0.45, color = cbep_colors()[3], lty = 2) +
  geom_text(aes(x = 0.48, y = 1, label = '0.45'), 
            angle = 90, hjust = 0, size = 3,
                color = cbep_colors()[3]) +
  labs(title = 'DEP and FOCB Data\n2015-2019')
```

<img src="combined_dep_and_focb_graphics_files/figure-gfm/tn_graphic_by_tn-1.png" style="display: block; margin: auto;" />

``` r
ggsave('figures/tn_by_site_by_tn.pdf', device = cairo_pdf, width = 6, height = 6)
```

``` r
plt +
  
  ylab('Sample Location') +
  
  theme(axis.text.y=element_blank(),
        axis.ticks.y=element_blank(),
        axis.line = element_line(color = 'gray85'))  +
  
  geom_vline(xintercept = 0.32, color = cbep_colors()[3], lty = 2) +
  geom_text(aes(x = 0.35, y = 1, label = '0.32'), 
            angle = 90, hjust = 0, size = 3,
                color = cbep_colors()[3]) +
  geom_vline(xintercept = 0.45, color = cbep_colors()[3], lty = 2) +
  geom_text(aes(x = 0.48, y = 1, label = '0.45'), 
            angle = 90, hjust = 0, size = 3,
                color = cbep_colors()[3]) +
  labs(title = 'DEP and FOCB Data\n2015-2019')
```

<img src="combined_dep_and_focb_graphics_files/figure-gfm/tn_graphic_by_tn_no_labels-1.png" style="display: block; margin: auto;" />

``` r
ggsave('figures/tn_by_site_by_tn_no_labels.pdf', device = cairo_pdf, 
       width = 4, height = 5)
```

## TN Graphic By Region

### Order Sites in Region

We order Regions by Median TN value, then order sites within regions by
the same. Note that to get the final ordering the way we want on the
graphic, we SUBTRACT the median TN value.

``` r
surface_results <- surface_results %>%
  mutate(region = fct_reorder(region, TN_md, na.rm = TRUE, .desc = TRUE),
         site = fct_reorder2(site, region, TN_md, 
                             function(x,y) as.numeric(x) * 100 - y),
         short_name = fct_reorder2(short_name, region, TN_md, 
                             function(x,y) as.numeric(x) * 100 - y))
surface_data <- surface_data %>%
  mutate(region = factor(region, levels = levels(surface_results$region)),
         site = factor(site, levels = levels(surface_results$site)),
         short_name = factor(short_name, levels = levels(surface_results$short_name)))
```

``` r
plt <- surface_data %>%
  filter(parameter == 'TN') %>%
  ggplot(aes(concentration, short_name)) +

  geom_point(aes(fill = region), shape = 21, size = 2) +
  #stat_summary(geom = 'point', fun = median, shape = 3, size = 2.5) +
  
  scale_fill_brewer(palette = 'Accent', name = '') +
  
  ylab('Sample Location') +
  xlab('Total Nitrogen (mg/l)') +
  
  theme_cbep(base_size = 12) +
  theme(axis.text.y=element_blank(),
        axis.ticks.y=element_blank(),
        axis.line = element_line(color = 'gray85'),
        legend.position = c(.80, .25),
        legend.text = element_text(size = 9),
        legend.key.height = unit(5, 'pt'),
        plot.margin  = unit(c(8,24,8,8), 'pt'),
        plot.title = element_text(hjust = 0.5)) +
  labs(title = 'DEP and FOCB Data\n2015-2019') +

  geom_vline(xintercept = 0.32, color = cbep_colors()[3], lty = 2) +
  geom_text(aes(x = 0.35, y = 15, label = '0.32'), 
            angle = 90, hjust = 0, size = 3,
                color = cbep_colors()[3]) +
  geom_vline(xintercept = 0.45, color = cbep_colors()[3], lty = 2) +
  geom_text(aes(x = 0.48, y = 15, label = '0.45'), 
            angle = 90, hjust = 0, size = 3,
                color = cbep_colors()[3])
```

``` r
plt
```

<img src="combined_dep_and_focb_graphics_files/figure-gfm/tn_graphic_region_legend-1.png" style="display: block; margin: auto;" />

``` r
ggsave('figures/tn_by_site_by_region_no_labels.pdf', device = cairo_pdf, 
       width = 4, height = 5)
```

## With Labels, Not Legend

### Calculate Y Coordinates

We want to place labels in the “middle” of the list of entries that
correspond to each region. So, we want to identify median row position
for each group.

``` r
n_sites <- length(levels(surface_results$site))
lab_positions <- surface_results %>%
  group_by(region) %>%
  summarize(y_pos = median(as.numeric(site)), .groups = 'drop') %>%
  mutate(x_pos = .825)
```

### Add Labels

``` r
plt +
  
  theme(legend.position = 'none',
        #plot.margin  = unit(c(8,24,8,8), 'pt')
        ) +
  geom_text(data = lab_positions, 
            mapping = aes(x = x_pos, y = y_pos, label = region),
            hjust = 0, size = 3)
```

<img src="combined_dep_and_focb_graphics_files/figure-gfm/tn_graphic_region_no_legend-1.png" style="display: block; margin: auto;" />

``` r
ggsave('figures/tn_by_site_by_region_final.pdf', device = cairo_pdf, 
       width = 4, height = 5)
```
