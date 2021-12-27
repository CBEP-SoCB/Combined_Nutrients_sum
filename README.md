# Combined_Nutrients

<img
    src="https://www.cascobayestuary.org/wp-content/uploads/2014/04/logo_sm.jpg"
    style="position:absolute;top:10px;right:50px;" />
    
# Introduction
This repository was created  to compare nutrient data received from Friends 
of Casco Bay and from Maine DEP.  Initially, this was only to ensure that we
were not double counting nutrient samples.  When we discovered that there was
no sample overlap between the two data sources, we extended the archive to 
combine data from both sources for graphical display purposes.

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

- Original Data.  Original data, with a "DATA_SOURCES.md" or "READ ME.txt" file 
that documents data sources.
**DATA IN THIS FOLDER IS AS ORIGINALLY PROVIDED OR ACCESSED.** 

> There is no "Original_Data" folder for this repository.  All data was derived 
  from sources described in two source data repositories ("DEP_nutrients" and 
  "FOCB_Nutrients"). Visit those repositories for data sources and details of
  initial data review and preparation.

- Derived Data.  Data derived from the original raw data.  Includes
documentation of data reorganization steps, either in the form of files (R
notebooks, Excel files, etc.) that embody data transformations, or via README.md
or DATA_NOTES.md files.  Code describing data review and QA/QC are often 
included here as well.

- Analysis.  Contains one or more R Notebooks proceeding through the data
analysis steps. This often includes both exploratory data analysis --
principally graphical -- and detailed analysis where necessary.

- Graphics.  Contains R Notebooks stepping through development of graphics, and
also copies of resulting graphics, usually in \*.png and \*.pdf formats.  These
graphics may differ from graphics as they appear in final State of the Bay
graphical layouts.

# Summary of Data Sources
Data presented here were shared with CBEP by Friends of Casco Bay and
Maine's Department of Environmental Protection, via e-mail.  Details of
sources and when files were received, reviewed, and corrected are included in
the data repositories where the main source data files were developed, as 
follows:

"DEP_nutrients"   ->  'dep_nutrient_data.csv'
"FOCB_Nutrients"  ->  'focb_n_data_strict.csv'
