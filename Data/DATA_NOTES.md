# Combined Nutrient  Data

This repository shows how we compared and combined nutrient data received from
Friends of Casco Bay and from Maine DEP.  A key step was checking to ensure that
we are not double counting nutrient samples.  All data was derived from sources
in the related 
[DEP_nutrients_sum repository](https://github.com/CBEP-SoCB/DEP_Nutrients.git) and the
[FOCB_Nutrients_sum repository](https://github.com/CBEP-SoCB/FOCB_Nutrients.git)
Data ultimately derives from raw data and data management scripts available in 
related archives in related repositories found 
[here](https://github.com/CBEP-SoCB-Details).

## `combined_surface_data.csv`
The file "combined_surface_data.csv" is the primary data file used in the 
archive.  It was produced via an R Notebook "combine_dep_and_focb_data.Rmd"
Available  at the related GitHub repository 
[here](https://github.com/CBEP-SoCB-Details/Combined_Nutrients.git).

The R code in that notebook assembles the data from "focb_data_strict.csv" and 
"dep_nutrient_data.csv". It also addresses inconsistent columns names and a few 
data quality concerns.  It is a "long format" file, including data for both
dissolved inorganic nitrogen (DIN) and total nitrogen  (TN) from sites sampled
by either FOCB or DEP with sufficient regularity to make analysis practical.

Variable Name |  Meaning                     | Units                
--------------|------------------------------|----------------------
source        | Did this data originate with FOCB or DEP? |  "FOCB", "DEP"
site          | Site code, from original source |  Alphanumeric *
dt            | Date of sample collection    | YYYY-MM-DD format
year          | Year of sample collection    | Four digit integer
month         | Month of sample  collection  | Three letter code         
doy           | Day of year                  | integer, 1 to 366
parameter     | What measurement?            | "TN" or "DIC"
concentration | Concentration of DIC or TN   | mg/l as N

\* See details, below.

## `combined_locations.csv`
The file was created in Excel by combining data from 
"FOCB Monitoring Sites SHORT NAMES.xlsx" and "dep_locations.csv".  Those files
are available in related repositories found 
[here](https://github.com/CBEP-SoCB-Details).  

The two lists of sites and locations were combined in Excel by hand.   The
resulting file includes location data for a number of sites that were rrarely
sampled, and thus are not depicted in maps for State of Casco Bay.

*  We checked for duplicate site codes and locations with multiple
   codes. 

*  We added new alternate (shorter) site names that addressed some site naming 
   inconsistencies between DEP and FOCB to reduce potential confusion in maps 
   and figures. 

*  We added a "source" column to indicate which monitoring program used each 
   site.

*  Finally, we added a "region" column to structure graphics more readily by Bay 
   region

Variable Name |  Meaning                        | Units                
--------------|---------------------------------|----------------------
site          | Site code, from original source |  Alphanumeric
site_name     | Site name, as provided by principal data provider  |  
short_name    | Short location name for labels and maps | 
latitude      | Latitude, WGS 1984              | decimal degrees
longitude     | Longitude, WGS 1984             | decimal degrees
source        | Which organization reported data from this site? | "FOCB", "DEP", "Both"
region        | Designation of the region of the Bay | See below *

## `surface_recent_results.csv`
The file `surface_recent_results.csv` was produced in the R Notebook
"combined_dep_and_focb_graphics.Rmd".  It contains summary statistics by site
for 2015 through 2019 data collected by FOCB and DEP at less than under 1 meter
water depth.  It's primary purpose was to export summary statistics for
potential use in GIS.

Variable Name |  Meaning                        | Units                
--------------|---------------------------------|----------------------
site          | Site code, from original source |  Alphanumeric
short_name    | Short text location name for labels and maps | 
region        | Designation of the region of the Bay | See below *
loc_sources   | Which organization reported data from this site? | "FOCB", "DEP", "Both"
latitude      | Latitude, WGS 1984              | decimal degrees
longitude     | Longitude, WGS 1984             | decimal degrees
DIN_mn        | Mean DIN (dissolved inorganic nitrogen) | mg/l as N
DIN_sd        | Standard deviation of DIN       | mg/l as N
DIN_n         | Sample size for DIN at this site | mg/l as N
DIN_md        | Median DIN                      | mg/l as N
DIN_iqr       | Interquartile range of DIN      | mg/l as N
DIN_p90       | 90th percentile of DIN          | mg/l as N
DIN_gm        | Geometric mean of DIN           | mg/l as N
TN_mn         | Mean TN (Total nitrogen)        | mg/l as N
TN_sd         | Standard deviation of TN        | mg/l as N
TN_n          | Sample size for TN at this site | mg/l as N
TN_md         | Median TN                       | mg/l as N
TN_iqr        | Interquartile range of TN       | mg/l as N
TN_p90        | 90th percentile for TN          | mg/l as N
TN_gm         | Geometric mean TN               | mg/l as N


\* Regions include the following: "Harraseeket", "Inner Bay", "New Meadows", 
"Outer Bay", "Presumpscot Estuary", "Royal River Estuary"
