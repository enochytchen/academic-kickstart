---
title: Tied time data → Untied time data
linktitle: Tied → Untied
toc: true
type: docs
date: "2020-09-28"
draft: false

menu:
  relsurv:
    weight: 15
---
The codes used in this tutorial are available below. \
[rs.surv](https://enochytchen.com/tutorials/relsurv/tied/rs.surv.R) \
 [stpp, strs, stnet (original tied data)](https://enochytchen.com/tutorials/relsurv/tied/using_colon_tied.do)\
 [stpp, strs, stnet (untied data)](https://enochytchen.com/tutorials/relsurv/tied/using_colon_untied.do)

### Settings

This tutorial first used the colon.dta, which is originally a tied survival time data, to generate the relative survival estimates of 1-, 5-, and 10-year relative survival using the Pohar-Perme estimator (1), by ```rs.surv()``` in R, ```stpp```,  ```strs```,  ```stnet``` in Stata. In the second part, an small epislon ~ $Norm(0, 0.01)$ was added into each individual's survival time to make no individual have exactly the same survival time (to untie the data). 

- Survival time was calculated by (date of exit - date of diagnosis). 
- We define failure as ```status == 1 2 ``` (died from cancer or other causes).
- In ```strs```, ```ht``` (hazard transformation) must be specified to command the survival be calculated by transforming cumulative excess hazard ($\lambda$) instead of an actuarial approach (default) (2). However, in ```stnet```, by default, survival is calculated by using hazard transformation.
- In ```strs``` and ```stnet```, monthly intervals were calculated  up to ten years by specifying ```br(0(`=1/12')10)```.

### Results
The esimates of 1-, 5-, and 10-year relative survival by each program are shown below:
#### Using colon.dta (original tied time data)
|t|rs.surv|stpp |strs|stnet|
|-------| ------|-----|----|-----|
|1| 0.676 (0.669-0.684)|0.682 (0.674-0.690)|0.677 (0.669-0.685)| 0.677 (0.669-0.685)|
|5| 0.473 (0.463-0.484)|0.479 (0.468-0.490)|0.474 (0.463-0.484)| 0.474 (0.463-0.484)|
|10|0.433 (0.414-0.453)|0.440 (0.421-0.461)|0.434 (0.415-0.454)|0.434 (0.415-0.454)|

#### Using colon.dta (untied time data)
||rs.surv|stpp |strs|stnet|
|-------| ------|-----|----|-----|
|1| 0.676 (0.668-0.684)|0.677 (0.669-0.685)|0.677 (0.669-0.685)|0.677 (0.669-0.685)|
|5| 0.472 (0.461-0.483)| 0.473 (0.463-0.484)|0.474 (0.463-0.484)| 0.474 (0.463-0.484)|
|10|0.431 (0.411-0.451)|0.433 (0.414-0.453)|0.434 (0.415-0.454)| 0.434 (0.415-0.454)|

### Explanation
- The default of ```rs.surv()``` calculates the product integral on the hazard level, whereas ```stpp``` uses  the Fleming-Harrington estimate (3), which seems to be more sensitive to ties.
- ```stnet``` should generate the identical estimates as ```strs```, given ```ht``` is specified in ```strs```.
- Removing ties did not have an effect with discrete time estimators using life-table framework. (```strs``` and ```stnet```).

### References
1. Pohar Perme M, Stare J, Estève J. On Estimation in Relative Survival. Biometrics. 2012;68:113-120 
2. Dickman P, Coviello E. Estimating and modelling relative survival. The Stata Journal. 2015;15:186-215.
3. Fleming TR, and Harrington DP. Nonparametric Estimation of the Survival Distribution in Censored Data.Communications in Statistics—Theory and Methods. 1984;13:2469–2486.