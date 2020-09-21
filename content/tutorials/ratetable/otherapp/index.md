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
In the last section of this tutorial, we would like to briefly introduce two other approaches for making rate table from a sub-population (Bower et al. 2018, Rachet et al. 2015). First, to summarise these three approaches, we made a comparison chart shown below:

|  | Our approach|Bower 2018| Rachet 2015|
| -| --------------| --------------|--------------|
| Nationwide life table| x|v|x|
| Individual data on a reference population| v| v|x (Grouped data by age, sex, deprivation)  
| Dealing with unceratinty|x|Parametric bootstrapping|Using data on multiple sampling populations|
| Characteristics|Modelling mortality rates directly without requiring nationwide life table |Stabilised  by nationwide life table| Not relying on underlying pattern of nationwide life table|

### Example 1: Adjust expected mortality rate by using adjustment factor
[Bower et al. (2018)](https://doi.org/10.1093/aje/kwx303)  used the individual-level data from a reference population to scale the expected mortality for each level of social class, aided by nationwide life tables.

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

[Rachet et al. (2015)](https://doi.org/10.1186/s12889-015-2534-3)  proposed a flexible method to construct smoothed life tables for sub-populations by using flexible relational model or flexible Poisson model. Individual-level data is not required in their method, whereas grouped data from multiple sampling populations was used (in their examples, simulation datasets of life tables and Wales' census data by age, sex, and calendar year).

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

Rather than using exisiting pattern of mortality by age, e.g., from a standard population mortality life table, they took account of the variance existing in the data.

### Conclusion
Our approach is distinct from Bower et al. and Rachet et al. , since only individual-level data is required to model the mortality rate directly from a representative population free of the disease of interest, the advantages of which are that in some situation nationwide life tables are not available and allowing more variance on the estimates directly generated from the reference population. However, the disadvantage is that the estimates of the model is not aided by the underlying pattern of existing life tables.

In terms of concerns on uncertainty, Rachet et al. sampled the grouped data for multiple times, and Bower et al. used a sensible approach of bootstrapping to tackle the issue, which is eligible to be considered in optimising our approach.

### References

Bower H, Andersson TM, Crowther MJ, Dickman PW, Lambe M, Lambert PC. Adjusting Expected Mortality Rates Using Information From a Control Population: An Example Using Socioeconomic Status. *Am J Epidemiol.* 2018;187(4):828-836. [doi: 10.1093/aje/kwx303](https://doi.org/10.1093/aje/kwx303).

Rachet B, Maringe C, Woods LM, Ellis L, Spika D, Allemani C. Multivariable flexible modelling for estimating complete, smoothed life tables for sub-national populations. *BMC Public Health*. 2015;15:1240. [doi: 10.1186/s12889-015-2534-3](https://doi.org/10.1186/s12889-015-2534-3).