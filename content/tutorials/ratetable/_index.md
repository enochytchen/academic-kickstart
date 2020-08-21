---
# Course title, summary, and position in the list.
Title:  Make ratetable using R
linktitle: Make ratetable 
summary: Learn how to make ratetable based on multiple time-scale flexible parametric models in R

# Page metadata.
date: "2020-08-19"
lastmod: "2020-08-19"
draft: false 
toc: true  # Show table of contents? true/false
type: docs  # Do not modify.
menu:
  ratetable: 
    weight: 2

---

Author: Enoch Chen, Paul Dickman

### Introduction
Population mortality rate tables (life tables) are typically stratified by age, sex, and year, and tabulated by government statistics offices (e.g., Human Mortality Database available at https://www.mortality.org/). Occasionally, we would also aim to stratify the rate table by other factors (e.g., race, social class, comorbidity, etc). This tutorial illustrates how to create a rate table from individual data for a cohort representative of the target population (i.e., a random sample of individuals without a diagnosis of cancer). 

Data on a cohort of patients with colon cancer diagnosis is used as an example dataset in this tutorial.
The original data is available (in Stata format) at https://pauldickman.com/data/colon.dta, whereas it is stored in the directory of this website as well.

### Steps in a nutshell
1. Model survival in multiple time scale flexible parametric model, where age is modeled continuously, and calendar year is a time-split variable.
2. Save the predicted rates for each combination of variables in a data frame. In this example, besides sex, age, and calendar year, we use other two dimensions (subsite + stage) as well.
3. Split the data frame of this mortality rate table into lists in order to apply ```transrate()``` and ```joinrate()``` to make a ```ratetable``` format in R. 
4. Use  ```rs.surv() ``` along with  the rate table  to estimate net survival.

### Acknowledgements
We especially want to thank [Alexander Ploner](https://staff.ki.se/people/aleplo) for providing statistical consulting and R code optimisation tips, [Nurgul Batyrbekova](https://staff.ki.se/people/nurbat) for her insight in modeling survival data on multiple time scale, [Joshua Entrop](https://joshua-entrop.com) for his teaching on plotting regression model results, and [
Quang Thinh Trac](https://staff.ki.se/people/quatra) for his assistance in optimising the syntax.