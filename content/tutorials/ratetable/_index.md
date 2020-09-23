---
# Course title, summary, and position in the list.
Title:  Make ratetable using R
linktitle: Make ratetable 
summary: An alternative approach to constructing a ratetable object in R by fitting a multiple time-scale flexible parametric models using individual-level data

# Page metadata.
date: "2020-08-19"
lastmod: "2020-08-23"
draft: false 
toc: true  # Show table of contents? true/false
type: docs  # Do not modify.
menu:
  ratetable: 
    weight: 2

---
Enoch Chen, Paul Dickman

### Introduction

An R [ratetable object](https://www.rdocumentation.org/packages/survival/versions/3.2-3/topics/ratetable) contains event rates per unit time and is typically used to estimate expected event rates for analyses in epidemiology where one compares the numbers of observed and expected events (e.g., SIR, SMR, or relative survival). Our interest lies in using rate tables for estimating relative survival (the ratio of observed survival among a cohort of cancer patients to the expected survival in the absence of cancer). The `ratetable` in R is analogous to what is often called the 'popmort file' used in relative survival in Stata (e.g., by the [strs](http://www.pauldickman.com/software/strs/strs/) command).

For cancer patient survival, ratetables are typically stratified by age, sex, and year and based on rates tabulated by government statistics offices. Mortality rates for many countries are available at the [Human Mortality Database](https://www.mortality.org/) and can be converted to a ratetable object using the `relsurv::transrate.hmd()` function. Occasionally we wish to stratify the ratetable by additional factors (e.g., race, social class, or comorbidity) for which tabulated rates are not available. This tutorial illustrates how to create a ratetable from individual data for a cohort that is representative of the target population (i.e., a random sample of individuals without a diagnosis of cancer). For example, we may wish to stratify the expected mortality rates by socio-economic status, in addition to age, sex, and year. Such rates are often not available, but we may have access to a cohort representative of the general population (called the 'control population') from which we can construct such rates. That is, we assume we have access to a cohort representative of the target population (i.e., a random sample of individuals without a diagnosis of cancer) with follow-up information to facilitate estimation of all-cause mortality.

There are other approaches of generating expected survival from a sub-population (Bower et al. 2018, Rochet et al. 2015). However, the approach we describe here is not as sofisticated; we will simply model the control population to produce the ratetable stratified by the desired factors. The approach is straightforward; fit a suitable model for all-cause mortality and predict rates for each combination of variables one wants in the ratetable. We will fit a Royston-Parmar model (a.k.a.\ flexible parametric model). We need to model two time scales; we'll use age as the primary timescale (which is modelled as a natural spline as it's the baseline hazard) and calendar year as the second time scale (by time-splitting and explicitly fitting a natural spline).

We are not aware of suitable publicly-available individual-level data from the general population, so we will use data on a cohort of patients with a cancer diagnosis as an illustrative example. The data are available (in Stata format) at https://pauldickman.com/data/colon.dta. We'll model the data as a function of age, sex, and year. We don't have a sensible 'extra' dimension (e.g., education or social class) but will use subsite and stage as the additional dimensions. That is, this data set is used purely to demonstrate the approach and the R code. In practice, one would apply the approach to a cohort of individuals representative of the general population.

### Steps in a nutshell
1. Model all-cause mortality using a flexible parametric model with two time scales; attained age is modeled continuously (as te underlying timescale) and attained calendar year is a time-split variable.
2. Save the predicted rates for each combination of variables in a data frame. In this example, besides sex, age, and calendar year, we use other two additonal dimensions (subsite + stage).
3. Split the data frame into lists in order to apply ```transrate()``` and ```joinrate()``` to create a ```ratetable``` object. The `relsurv::transrate()` function converts a data frame containing rates into a ratetable object. The function only operates on data frames containing rates stratified by sex, age, and year (i.e., it does not support additional dimensions). To add aditional dimensions we need to apply ```transrate()``` to rates for each level of the additional dimensions and then join the resulting ratetables using ```joinrate()```.
4. As a test, use  ```rs.surv() ``` along with the newly created ratetable to estimate net survival. The resulting estimates of net survival should be 1.

### References

Bower H, Andersson TM, Crowther MJ, Dickman PW, Lambe M, Lambert PC. Adjusting Expected Mortality Rates Using Information From a Control Population: An Example Using Socioeconomic Status. *Am J Epidemiol.* 2018;187(4):828-836. [doi: 10.1093/aje/kwx303](https://doi.org/10.1093/aje/kwx303).

Rachet B, Maringe C, Woods LM, Ellis L, Spika D, Allemani C. Multivariable flexible modelling for estimating complete, smoothed life tables for sub-national populations. *BMC Public Health*. 2015;15:1240. [doi: 10.1186/s12889-015-2534-3](https://doi.org/10.1186/s12889-015-2534-3).

### Acknowledgements
We especially want to thank [Alexander Ploner](https://staff.ki.se/people/aleplo) for providing statistical consulting and R code optimisation tips, [Nurgul Batyrbekova](https://staff.ki.se/people/nurbat) for her insight in modeling survival data on multiple time scale, [Joshua Entrop](https://joshua-entrop.com) for his teaching on plotting regression model results, and [Quang Thinh Trac](https://staff.ki.se/people/quatra) for his assistance in optimising the syntax.