---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: Extrapolating survival (all-cause survival framework)
linktitle: Extrapolating survival (all-cause)
summary: Extrapolating all-cause survival is straightforward, but how good is the preformance?
toc: true
type: docs
date: "2020-04-22"
draft: false
menu:
  extrapolation:
      allcause: 
      parent: All-cause survival
      weight: 7
---

The codes used in this post are available [here](http://enochytchen.com/directory/stata/extrapolating_allcause_stpm2.do).

This blogpost introduces how to extrapolate patients' survival using flexible parametric model with age groups (categorical variable) as covariates. The tutorial is following the post [Predicting in a new data set with stpm2](http://pauldickman.com/software/stata/prediction_new_data/) from [Paul Dickman](http://pauldickman.com)'s website, where he introduced how to extrapolate all-cause survival using flexible parametric model with age (continuous variable) and sex. 

The following extrapolation results can then answer questions such as 
* What is the estimated survival of patients aged 50-59 diagnosed with colon cancer during 1975-1985?
* What is the estimated survival of patients aged <50 diagnosed with breast cancer during 2000-2010?

<!--more--> 
#### Data preparation
First you need to prepare the data before start modeling with stpm2. The exmaple colon cancer data used here can be found from my [previous post: Data preparation](https://enochytchen.com/tutorials/extrapolation/data_prep/data_preparation/).

```
use colon1975_1985,clear
```
```
stset stime, failure(dead==1,2) id(id) exit(time 10) 
```
We stset the data for survival analysis. In this dataset, death from cancer was coded as dead=1 and from other causes as dead=2. We are interested in patients who died from any cause. We then assumed the maximum follow-up was 10 years, so exit(time 10) was set for making everyone censored after 10 years.

#### Modeling with ```stpm2```

```
stpm2 agegroup2-agegroup5, /// agegroup0 is reference group
scale(hazard) df(5) eform /// hazard: propotional hazard
tvc(agegroup2-agegroup5) dftvc(2) // tvc allows non-proportional hazard
```
We then modeled the survival data in the flexible parametric model using stpm2. Basically, the setting showed that it is a proportional hazard model using scale(hazard), but we allowed time-dependent effects on the age group by using tvc(agegroup2-agegroup5).

```
clear
range agegroup 0 4 5 
range _t 0 20 241 // (12*20)+1=241
fillin agegroup _t
drop if missing(agegroup,_t)

quietly tab agegroup, gen(agegroup)
 
predict s, survival timevar(_t) 
sort agegroup _t

save colon1975_1985_10yr_extrap, replace
```
As the model was stored in the background, we first cleared the data but created a new dataset (12 months*20years + the last point) for prediction by using ```
fillin```
 function. We then predict the survival by age group using predict to gain extrapolated values beyond 10 year of follow-up.

{{< figure library="true" src="extrapolating_allcause_stpm2_fig1.png" title="Extrapolation by restricting follow-up data to 10 years" lightbox="true" >}}
 The survival was by age groups, where the first 10 years were observed survival and later came the predicted survival.


{{< figure library="true" src="extrapolating_allcause_stpm2_fig2.png" title="Comparing extrapolation with empirical K-M curve" lightbox="true" >}}

To evaluate the extrapolated survival, we then compared it with the empirical survival estimated by the Kaplan-Meier's (K-M) method. 

It seemed that for certain age groups in this dataset, the extrapolation would deviate from the empirical K-M curve. To obtain robust prediction, we prefer borrowing external information, such as vital statistics, to extrapolate the survival in relative survival framework rather than all-cause survival. Please refer to the next post: [ Extrapolating survival (relative survival framework) ](http://enochytchen.com/tutorials/extrapolation/relative/extrapolating_rel/).

