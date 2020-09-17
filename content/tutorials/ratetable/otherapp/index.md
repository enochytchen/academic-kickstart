---
title: Make ratetable using R 
linktitle: Other approaches
toc: true
type: docs
date: "2020-09-17"
draft: false

menu:
  ratetable:
    weight: 15
---
In the last section of this tutorial, we would like to briefly introduce two other approaches for making rate table from a sub-population (Bower et al. 2018, Rachet et al. 2015). 

### Example 1: Adjust expected mortality rate by using adjustment factor
[Bower et al. (2018)](https://doi.org/10.1093/aje/kwx303) concluded some previous solutions on creating a stratified life tables by other factors, and presented a solution where they used the control population to scale the nationwide rates for each level of social class. 

Rather than using the the typical defintion of relative survival ($R(t)$, defined as the ration between $S(t)$, the all-cause survival of a patient cohort and $S^*(t)$, the expected survival of a general population), Bower et al. extended it to estimate the relative survival between the breast cancer patients and the Swedish population, with available data on SES, age at diagnosis, year of diagnosis (in their case, all the breast cancer patients were women, so sex was exempted), shown below:

$$
R\left(t \mid \mathrm{SES}, a_{d}, y_{d}\right)=\frac{S\left(t \mid \mathrm{SES}, a_{d}, y_{d}\right)}{S^{*}\left(t \mid \mathrm{SES}, a_{d}, y_{d}\right)}
$$

That is, on hazard scale, the expected hazard (or so-called expected mortality rate) is required to be stratified by SES. Thus, they proposed estimating adjustment factors, $\rho_{j}$, corresponding to SES grops, and multiplied it with the unadjusted rate to generate SES-adjusted mortality rate.
$$
\frac{h_{c}(a, y, j)}{h^{*}(a, y)}=\exp \left[\sum_{j=1}^{3} \rho_{j}(a, y) \times \operatorname{SES}_{j}\right]
$$

In the article, they further showed that how this framework works using Poisson models and flexible parametric models with restricted cubic spline function. For tackling uncertainty in using a sub-population, they ran a parametric bootstrap method for 100 times.

### Example 2: Multivariable flexible modelling 

[Rachet et al. (2015)](https://doi.org/10.1186/s12889-015-2534-3) pointed out that nationwide life tables are not always available in such settings like low- and middle-income countries. They proposed a flexible method to constructs smoothed life tables for sub-populations by using flexible relational model or flexible Poisson model. The mortality rate in the life table generated from their method can further be tranformed into the expected survival and then applied to relative survival.

For the flexible relational model, a logit transformation was applied to the survival function, $l_x$, and make a linear regression out of it, along with a logit transformation of the reference population's survival function, $l_{x_{s}}$, and other covariates, such as deprivation level and the interaction between deprivation level and age.

$$
\begin{aligned}
\operatorname{logit}\left(l_{x, i}\right) &=f\left(\log i t\left(l_{x_{s}}\right)\right) +\sum_{i=2}^{5} \beta_{i} \text {dep}_{i}+g(\text {agedep})
\end{aligned},
$$
where $f$ and $g$ are restricted cubic spline functions.

For the flexible Poisson model, in which Rachet et al. modelled the number of age-specific deaths in generalised lineal model, shown below:
$$
\begin{aligned}
\log \left(d_{x, i}\right) &=\beta_{0}+f(x)+\sum_{i=2}^{5} \beta_{i} \text {dep}_{i}+g(\text {agedep})+\log \left(\text {pyrs}_{x, i}\right)
\end{aligned},
$$
where ${pyrs}_{x, i}$ denotes the number of person-years at risk corresponding to specific age and deprivation level.

### Conclusion
In these two examples, Bower et al. and Rachet et al. separately used socioeconomic status (SES) and deprivation level as a dimension to model hazard (mortality rate), which is eligible to be further transformed into expected survival and used for estimating net survival using relative survial framework. However, other dimensions, such as race, region, comorbidity can also be considered to be adopted (if there is available data) in a sensible way in the model.

### References

Bower H, Andersson TM, Crowther MJ, Dickman PW, Lambe M, Lambert PC. Adjusting Expected Mortality Rates Using Information From a Control Population: An Example Using Socioeconomic Status. *Am J Epidemiol.* 2018;187(4):828-836. [doi: 10.1093/aje/kwx303](https://doi.org/10.1093/aje/kwx303).

Rachet B, Maringe C, Woods LM, Ellis L, Spika D, Allemani C. Multivariable flexible modelling for estimating complete, smoothed life tables for sub-national populations. *BMC Public Health*. 2015;15:1240. [doi: 10.1186/s12889-015-2534-3](https://doi.org/10.1186/s12889-015-2534-3).