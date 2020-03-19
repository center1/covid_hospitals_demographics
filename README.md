# Hospital, population, nursing center data

This repository is a project to present and join datasets pertinent to the COVID-19 pandemic at the county level: hospital location and capacity, nursing home location and capacity, and county-level population estimates by age.

A simplified state-level view of this with only population breakouts for 65+ is available [here](https://docs.google.com/spreadsheets/d/1XC0SfpPgYkhLPe4CeXDKs3sgVw8Cbcqa3MpEt8tUQcY/edit#gid=0). The source data for statewide hospital beds is [here](https://www.ahd.com/state_statistics.html).

In general this repo is trying to follow the datakit repo convention, it isn't *actually* a datakit repo but may become one at some point. 


# Data

Each of the datasets is documented by the readme file in it's respective folder. In general the output files are in /data/processed/. 

These are the main output files

## Hospital-level bed data

CSV: [hospital_data.csv](https://github.com/jsfenfen/covid_hospitals_demographics/blob/master/data/processed/hospital_data.csv) ;  Shapefile [hosp\_geo\_final](https://github.com/jsfenfen/covid_hospitals_demographics/blob/master/data/processed/hosp_geo_final.zip) 

The hospital bed counts data come from the raw CMS cost reports database here:: https://www.cms.gov/Research-Statistics-Data-and-Systems/Downloadable-Public-Use-Files/Cost-Reports/Cost-Reports-by-Fiscal-Year They are upwords of ~600MB unzipped, so aren't included in this repo. 

They don't have header rows, you have to add your own. 

For the NMRC file I used 

	RPT_REC_NUM,WKSHT_CD,LINE_NUM,CLMN_NUM,ITM_VAL_NUM
	
For the RPT file I used

		RPT_REC_NUM,PRVDR_CTRL_TYPE_CD,PRVDR_NUM,Unknown,RPT_STUS_CD,FY_BGN_DATE,FY_END_DATE,PROC_DT,INITL_RPT_SW,LAST_RPT_SW,TRNSMTL_NUM,FI_NUM,ADR_VNDR_CD,FI_CREAT_DT,UTIL_CD,NPR_DT,SPEC_IND,FI_RCPT_DT

The information in the downloadable file comes from the following lines. The documentation is a little hard to follow, see the instructions for completing this form on [p. 62 here](https://github.com/jsfenfen/covid_hospitals_demographics/blob/master/data/source/cost_reports/HOSPITAL2010-DOCUMENTATION/R15P240.pdf).  It refers to [42 CFR 412.105(b) ](https://github.com/jsfenfen/covid_hospitals_demographics/blob/master/data/source/cost_reports/HOSPITAL2010-DOCUMENTATION/CFR-2010-title42-vol2-sec412-105.pdf) which may be relevant. It also cites [69 FR 49093-49098 (August 11, 2004)](https://github.com/jsfenfen/covid_hospitals_demographics/blob/master/data/source/cost_reports/HOSPITAL2010-DOCUMENTATION/FR-2004-08-11.pdf) In general more documentation for the cost reports is [here](https://github.com/jsfenfen/covid_hospitals_demographics/tree/master/data/source/cost_reports/HOSPITAL2010-DOCUMENTATION).

These files have basic hospital information and bed counts from the most recently filed hospital cost report received in 2017 or later. The source report number, fiscal year end date, and filing date is also included. These come from page 9 column 2 of this [original form](https://www.cms.gov/Regulations-and-Guidance/Guidance/Manuals/Paper-Based-Manuals-Items/CMS021935) from 2017.  

There's an awesome [python notebook](https://github.com/jsfenfen/covid_hospitals_demographics/blob/master/data/analysis/HospitalICUBeds_2017.ipynb) written by [Erin Petenko](https://github.com/epetenko/) that makes a little clearer how to navigate this data.

Each of the hospital bed lines corresponds directly to a line in the column 2 of worksheet S-3. Only the "major lines" ending in '00' are used, but CMS allows a variety of other minor lines, not documented on the form, for more specific designations that are also added to the subtotal\_acute\_beds. They may be included in a later release. 

A numeric breakdown of the minor lines is [available here](https://docs.google.com/spreadsheets/d/1nAIbiJ7LMPmHVcJvuceMKHTUn__Hly4LYMjeevMO1mc/edit?usp=sharing) if you know of any documentation for any of these lines, please let me know! 

Here are the bed numbers used, the variable names in **bold**

- **acute_beds** Adult/Pediatric Acute Care Beds 00700
- **icu_beds** Intensive Care Beds 00800
- **coronary_beds** Coronary Care Beds 00900
- **burn_beds Burn** Intensive Care Units 01000
- **surg\_icu_beds** Surgical ICU Beds 01100
- **oth\_spec\_beds** Other Specialty Beds 01200

The total of all of the above bed types is given, *roughly*, by:

- **subtotal\_acute\_beds** Subtotal of acute care beds 01400

Additional hospital bed types (not acute care beds)

- **subprovider\_ipf\_beds** Subprovider Inpatient Psychiatric Facility beds 01600
- **subprovider\_irf\_beds**  Subprovider Inpatient Rehabilitation Facility 01700
- **subprovider\_oth\_beds**  01800
- **skilled\_nursing\_beds**  01900
- **nursing\_fac\_beds**  02000
- **oth\_longterm\_beds** 02100
- **hospice\_beds**  02400

The sum of subtotal\_acute\_beds and all additional bed types is given by

- **all\_beds** All Beds 02700

Military hospitals with an id ending in F are missing bed counts but are included here anyways. Many children's hospitals (e.g. hospital_type = childrens) do not report bed counts. Psychiatric hospitals are not included. Recently opened facilities that have not filed CMS reports yet also show zero bed counts.

(The shapefile leaves out [one hospital](https://data.medicare.gov/resource/xubh-q36u/row-hgvv.mh7i-bzfv) in Puerto Rico.)

The hospital's provider number should correspond to the provider number in the next file.

## Census data by county by age

Downloadable [csv file](https://github.com/jsfenfen/covid_hospitals_demographics/blob/master/data/processed/2018_county_census.csv) ; [shapefile](https://publicaccountability.s3.amazonaws.com/rawfiles/counties_final.zip)
 
County-level population age data comes from the Annual Estimates of the Resident Population for Selected Age Groups by Sex for the United States, States: April 1, 2010 to July 1, 2018 from the [2018 Population Estimates](https://factfinder.census.gov/faces/tableservices/jsf/pages/productview.xhtml?src=bkmk).

Population estimates are given in 5-year age ranges, e.g. 70-74. 



## Geocoded nursing home locations 


[nh\_gen\_info\_geocoded\_final.csv](https://github.com/jsfenfen/covid_hospitals_demographics/blob/master/data/processed/nh_gen_info_geocoded_final.csv) Is a file of CMS nursing home compare data. It contains the number of certified beds and average daily occupancy among other variables.  Lat and lngs were added where they were missing; where this occurred geocode_flag = 1. To see full documentation: 
https://data.medicare.gov/Nursing-Home-Compare/Provider-Info/4pq5-n9py


For more on the underlying data see: https://data.medicare.gov/Nursing-Home-Compare/Provider-Info/4pq5-n9py

## Stories

If you're able to use this in your work, or have relevant data to add, please let us know. 

Portland Tribune, 3/14 ["As number of virus cases grows, Oregon has lowest hospital bed rate in U.S."](https://pamplinmedia.com/pt/9-news/456432-372245-as-deluge-approaches-oregon-has-lowest-hospital-bed-rate-in-us).

Suggested reading: ["COVID-19 story recipe: Analyzing nursing home data for infection-control problems"](https://source.opennews.org/articles/covid-19-story-recipe-analyzing-nursing-home-data/), Source, Mike Stucka, 3/16/20

## Contributors

Jacob Fenton, [PublicAccountability.org](https://publicaccountability.org); Erin Petenko  [VTDigger](https://vtdigger.org/)







