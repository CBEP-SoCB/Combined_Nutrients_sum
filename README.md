# Combined_Nutrients

<img
    src="https://www.cascobayestuary.org/wp-content/uploads/2014/04/logo_sm.jpg"
    style="position:absolute;top:10px;right:50px;" />
    
# Introduction
This repository was created  to prepare graphics that combine information on
nutrients in Casco Bay from both Maine DEP and FOCB data. We combine data from 
both sources for graphical display purposes.

We do not conduct detailed statistical analyses of differences between sites in
this repository, as data comes from two sources with different histories,
purposes and QA/QC practices.  Sampling histories of different sites vary. We
know from detailed analyses in two related data repositories (which look in more
detail at the source data) that nitrogen levels vary by location, time of year,
and year.

The different sample histories thus induce a certain degree of bias in results
by site.  Analysis presented in the other repositories suggests that the
practical import of that bias is small, at least regarding the qualitative
conclusions we want readers to draw from State of Casco Bay chapters. We
minimize the potential impact of the bias by:

1.  Presenting raw observations, not derived statistics, which could encourage
    readers to over interpret small differences, especially in location.
    
2.  Restricting presentation to sites with at least five samples, representing
    multiple times of year.  All sites were studied for at least one complete
    season, but for many locations, we only have data from a single year.

# Statement of Purpose
CBEP is committed to the ideal of open science.  Our State of the Bay data
archives ensure the science underlying the 2020 State of the Bay report is
documented and reproducible by others. The purpose of these archives is to
release raw data and data analysis code whenever possible to allow others to
review, critique, learn from, and build upon CBEP science.

# Archive Structure
CBEP 2020 State of the Bay data analysis repositories are divided into from two
to four sub-folders.

- Data. Contains working data on which R code in other folders depend. Often
these data are derived from source data not provided here.

- Graphics.  Contains R Notebooks stepping through development of graphics, and
also copies of resulting graphics, usually in \*.png and \*.pdf formats.  These
graphics may differ from graphics as they appear in final State of the Bay
graphical layouts.

# Summary of Data Sources
Data presented here were shared with CBEP by Friends of Casco Bay and
Maine's Department of Environmental Protection, via e-mail. More information is
available at related summary GitHub repositories.
[DEP_nutrients_sum repository](https://github.com/CBEP-SoCB/DEP_Nutrients.git) and the
[FOCB_Nutrients_sum repository](https://github.com/CBEP-SoCB/FOCB_Nutrients.git)

Data ultimately derives from raw data and data management scripts available in
related archives found [here](https://github.com/CBEP-SoCB-Details). Those files
include details of sources and how files were received, reviewed, and corrected.
