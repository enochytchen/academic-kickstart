---
title: Extrapolating survival (relative survival framework)
linktitle: Extrapolating survival (relative)
summary: Extrapolating relative survival and transforming it back to all-cause survival by mutiplying the expected survival. 
toc: true
type: docs
date: "2020-04-20"
draft: false

menu:
  extrapolation:
    relative:
    parent: Relative survival
    weight: 14
---

The codes used in this post are available [here](http://enochytchen.com/directory/stata/extrapolating_relative_stpm2.do)

#### Data preparation
First you need to prepare the data before start modeling with stpm2. The exmaple data used here can be found from my [previous post: Data preparation](https://http://enochytchen.com/tutorials/extrapolation/data_prep/data_preparation/).

#### Expected survival
Other than modeling on patient survival, $S(t)$, it is more plausible to borrow external information, such as vital statistics, to obtain expected survival, $S^{*}(t)$, to extrapolate survival within relative survival framework.

$R(t)=\frac{S(t)}{S^{*}(t)}$

The analogue of relative survival, $R(t)$,  on a hazard scale is 

$h(t)=h^{*}(t)+\lambda(t)$

By integration, the result is 

$H(t)=H^{*}(t)+\Lambda(t),$

where the cumulative hazard, $H(t)$, consists of the cumulative expected hazard, $H^{*}(t)$, and the cumulative excess hazard, $\Lambda(t)$.

The cumulative expected hazard can be obtained from population mortality. For how to prepare life table data for expected survival, the hand-in-hand teaching materials can be found at my previous posts: [Population mortality rate projection](https://http://enochytchen.com/tutorials/extrapolation/data_prep/popmort_projection/) and [Prepare expected survival](https://http://enochytchen.com/tutorials/extrapolation/extrapolating_rel/make_expected_survival/). colon_expected_survival.dta and popmort_projection.dta, both of which will be used in the following modeling process, will be generated after running the do files. 

After running the above programs, we then acquire the expected survival corresponding to the patient cohort's survival by age, sex, and calendar year. Here we start model the relative survival.

#### Creating relative survival
```
use colon1975_1985,clear
```
```
stset stime, failure(dead==1,2) id(id) exit(time 10) 
```
We stset the data for survival analysis. In this dataset, death from cancer was coded as dead=1 and from other causes as dead=2. We are interested in patients who died from any cause. We then assumed the maximum follow-up was 10 years, so exit(time 10) was set for making everyone censored after 10 years.

```
gen _year= floor(min(yeardiag + _t, 1995)) 
gen _age=floor(min(agediag + _t, 105)) 

merge m:1 _year sex _age /// 
	using popmort_projection.dta, ///
	nolabel keep(match master) keepusing(rate) 
	
drop _age _year _merge
```
_year and _age were created corresponding to the life table. The maximum of _year was set as 1995, and _age as 105. Then the patients' survival data was merged with life table to obtain the expected mortality rate. 

#### Modeling relative survival with ```stpm2```

```
stpm2 agegroup2-agegroup5, ///
scale(hazard) df(5) eform ///
tvc(agegroup2-agegroup5) dftvc(2) bhazard(rate) 
```
Rather than [modeling all-cause survival](http://enochytchen.com/tutorials/extrapolation/allcause/extrapolating_all/), we specified using bhazard(rate) for modeling relative survival on log cumulative excess hazard using stpm2. 

```
clear
range agegroup 0 4 5 
range _t 0 20 241 // (12*20)+1=241
fillin agegroup _t
drop if missing(agegroup,_t)

quietly tab agegroup, gen(agegroup)

predict rs, survival timevar(_t) 
sort agegroup _t

save colon_10yr_rs_extrap, replace
```
Still a empty dataset was created for extrapolation. We then predict the **relative survival** by age group using predict to gain extrapolated values beyond 10 year of follow-up and save the extrapolated relative survival.

So far we have already successfully predicted relative survival. Looking back to the equation of relative survival, R(t), it can be easily transformed back to the all-cause survival, $S(t)$, by multiplying with expected survival, $S^*(t)$.

$S(t)=R(t) \times S^{*}(t)$


```
use colon_expected_survival, clear
sort agegroup _t

merge m:1 agegroup _t  ///
using  colon_10yr_rs_extrap, ///
nolabel keep(match master) keepusing(rs) 

//S(t)=S^*(t) * R(t)
//cp=cp_e2*cr_e2
drop _merge
gen obs=cp_e2*rs
replace obs=1 if _t==0
sort agegroup _t

save colon_10yr_rs_to_allcause, replace
```
{{< figure src="extrap_rel_fig1.png" title="Extrapolation by restricting follow-up data to 10 years"  >}}
 The survival was by age groups, where the first 10 years were observed survival and later came the predicted survival. To compare with the empirical Kaplan-Meier curves, the figure below demonstrates the extrapolation.


{{< figure src="extrap_rel_fig2.png" title="Comparing extrapolation with empirical K-M curve" >}}

Extrapolation within relative survival framework is more robust than all-cause survival. In comparison with the figure in [Extrapolating survival with stpm2 (Part 1)](https://http://enochytchen.com/tutorials/extrapolation/allcause/extrapolating_all/), we could observe that the extrapolated survival curves deviate less from the empirical K-M curves. 

You may wonder it does not seem to be much different from each other. However, due to the maximum 20 years of follow-up in this dataset, we were not able to generate the empirical curve beyond 20 years. If another dataset containing a longer follow-up period is used, we project that extrapolation in all-cause survival would deviate more, resulting uncertainty in prediction.

So far we have briefly compare the difference between extrapolating survival in all-cause survival and in relative survival. But what makes relative survival more plausible for extrapolation? Please check the [next post: Why makes extrapolating relative survival more precise?](https://enochytchen.com/) for explanation!