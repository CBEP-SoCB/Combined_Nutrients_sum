# Derived Data
This repository was created to compare nutrient data received from Friends 
of Casco Bay and from Maine DEP, to ensure that we are not double counting
nutrient samples.  Accordingly, there is no "Original_Data" folder for this 
repository.  All data was derived from sources in "DEP_nutrients" and 
"FOCB_Nutrients" repositories, and copied here.

The versions of the date here were copied from those repositories as of June 23, 
2021. Data sets had gone through full QA/QC at that point, and draft graphics
produced and analyses completed, so while those data file may appear in later 
version because of revising the R Notebooks that produce them, they are not 
expected to change.

The file "combined_surface_data.csv" is the primary data file used in the 
archive.  It was produced via the R Notebook "combine_dep_and_focb_data.Rmd".
The R code in that notebook assembles the data from "focb_data_strict.csv" and 
"dep_nutrient_data.csv"  it also addresses inconsistent columns names and a few 
data quality concerns.

The file "combined_locations.csv' was created in Excel by combining data from 
"FOCB Monitoring Sites SHORT NAMES.xlsx" and "dep_locations.csv".  The two lists 
of sites and locations were combined in Excel by hand.   

*  We checked for duplicate site codes and duplicate locations with multiple
   codes. 

*  We added new alternate (shorter) site names that addressed some site naming 
   inconsistencies between DEP and FOCB to reduce potential confusion in maps 
   and figures.  
    
*  We added a "source" column to indicate which monitoring program used each 
   site.

*  Finally, we added a "region" column to structure graphics more readily by Bay 
   region
   
The file "surface_recent_results.csv" was produced in the R Notebook 
"combined_dep_and_focb_graphics.Rmd".  It contains summary statistics by site
for 2015 through 2019 data collected by FOCB and DEP at less than under 1 meter 
water depth.




