---
title: Data clearance
linktitle: Data clearance
summary: Preparation for data used in upcoming analyses. 
toc: true
type: docs
date: "2020-04-20"
draft: false
menu:
  extrapolation:
    data_prep:
    parent: Data preparation
    weight: 2

---
The codes used in this post are available [here](http://enochytchen.com/directory/stata/data_prep_extrapolation.do).


```stata
// This data was downlaoded from Paul Dickman's website

// Diagnosis 1975-1985
use http://enochytchen.com/directory/data/colon.dta if yydx>=1975 & yydx<=1985, clear
```
The data is originally from https://pauldickman.com/survival/colon.dta, permited use by [Paul Dickman](http://pauldickman.com).

```stata
// Rename variables
rename yydx yeardiag
rename age agediag
gen stime=surv_mm/12
rename status dead
```
The varaibles are renamed for further use in the upcoming analysis.

```
// Smart way to creating age groups
egen agegroup=cut(agediag), at(0 50 60 70 80 200) icodes
label variable agegroup "Age group"
label define agegroup 0 "0-49" 1 "50-59" 2 "60-69" 3 "70-79" 4 "80+" 
label values agegroup agegroup
// Create dummy varaiables for age categories
quietly tab agegroup, gen(agegroup)

save colon1975_1985,replace
```
Patients were categorized by age group <50, 50-59, 60-69, 70-79, >=80. Dummy variables on age group were created for modeling in stpm2. Then save the file for further modeling.