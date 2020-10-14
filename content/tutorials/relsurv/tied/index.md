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
 [stns, stpp, strs, stnet (original tied data)](https://enochytchen.com/tutorials/relsurv/tied/using_colon_tied.do)\
 [stns, stpp, strs, stnet (untied data)](https://enochytchen.com/tutorials/relsurv/tied/using_colon_untied.do)

### Settings

This tutorial first used the colon.dta, which is originally a tied survival time data, to generate the relative survival estimates of 1-, 5-, and 10-year relative survival using the Pohar-Perme estimator (1), by ```rs.surv()``` in R, ```stpp```,  ```strs```,  ```stnet``` in Stata. In the second part, an small epislon ~ $Norm(0, 0.01)$ was added into each individual's survival time to make no individual have exactly the same survival time (to untie the data). 

- Survival time was calculated by (date of exit - date of diagnosis). 
- We define failure as ```status == 1 2 ``` (died from cancer or other causes).
- In ```strs```, ```ht``` (hazard transformation) must be specified to command the survival be calculated by transforming cumulative excess hazard ($\lambda$) instead of an actuarial approach (default) (2). However, in ```stnet```, by default, survival is calculated by using hazard transformation.
- In ```strs``` and ```stnet```, monthly intervals were calculated  up to ten years by specifying ```br(0(`=1/12')10)```.

### Results
To make the tables look tidier, here we dismissed the 95% CI, which however can be found in the outputs of the syntax.
The esimates of 1-, 5-, and 10-year relative survival by each program are shown below:
#### Using colon.dta (original tied time data)
|t|rs.surv|stns|stpp |strs|stnet|
|-------| ------|------|-----|----|-----|
|1| 0.676 |0.677|0.676 |0.677 | 0.677 |
|5| 0.473 |0.474|0.473 |0.474 | 0.474|
|10|0.433 |0.437|0.434 |0.434 | 0.434 |

#### Using colon.dta (untied time data)
|t|rs.surv|stns|stpp |strs|stnet|
|-------| ------|------|-----|----|-----|
|1| 0.676 | 0.677|0.677 |0.677 |0.677 |
|5| 0.472 | 0.474|0.473 | 0.474| 0.474 |
|10|0.431 | 0.435 |0.433 |0.434 | 0.434 |

### Explanation
- By default, both ```rs.surv()```, ```stns```, ```stpp``` calculate survival using the product integral method on the hazard level, whereas in ```strs``` and ```stnet``` time-scale is split into numbers of intervals (using actuarial life-table approach).
- In ```stpp```, the Fleming-Harrington estimator (using the expoential of the negative cumulative (excess) hazard), which appears to be more sensitive to ties, is also eligible to be applied (3).
- ```stnet``` should generate the identical estimates as ```strs```, given that ```ht``` is specified in ```strs```.
- Removing ties did not have an effect with discrete time estimators using life-table framework. (```strs``` and ```stnet```).
- In ```strs``` and ```stnet```, life-table framework is implemented to estimate relative survival. However, should the cutpoints be in months ```br(0(`=1/12')10)``` or in years ```br(0(1)10)```? \
A: Monthly estimate is more accurate. Both ```strs``` and ```stnet``` - calculates the attained age and attained year at the beginning  of each interval and takes the ```floor()``` of these values from the popmort file to obtain the expected mortality rate. However, typically the survivaly probility from the popmort file (calculated from 1-year probability of death, by using  by $-exp(H)$) is the probability of surviving 1 year, $p$. If it is monthly interval, we take the twelth root of the survival probability, $p^{1/12}$. Calculating by month literally means we do it 12 times to calculate the survival from an $x$ year-old person until he turns $(x+1)$ years old, but if using annual interval, we do it once instead.

{{% alert note %}}
 It is worth paying attention to how age and year are managed in each program. 
  - ```stns```: age of dianosis in the cohort data and age in the popmort file are needed in ```age()```;  year of dianosis ~~date of diagnosis~~ and the calendar year in the popmort file should be specified in ```period()``` .
  - ```stpp```: age and date of diagnosis should be specified in ```agediag()``` and ```datediag()```.
  - ```strs```: age and year of diagnosis are required in ```diagage()``` and ```diagyear()```.
  - ```stnet```: date of dianosis ```diagdate()``` and date of birth ```birthdate()``` are required.
{{% /alert %}}

### References
1. Pohar Perme M, Stare J, Estève J. On Estimation in Relative Survival. Biometrics. 2012;68:113-120 
2. Dickman P, Coviello E. Estimating and modelling relative survival. The Stata Journal. 2015;15:186-215.
3. Fleming TR, and Harrington DP. Nonparametric Estimation of the Survival Distribution in Censored Data.Communications in Statistics—Theory and Methods. 1984;13:2469–2486.

### Acknowledgement
We especially want to say thank-you to [Paul Lambert](https://pclambert.net) for offering his scenario2_1.dta as an example dataset and substantial insights on the syntaxes of ```stns```and ```stnet```.
