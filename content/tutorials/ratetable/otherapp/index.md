---
title: Make ratetable using R 
linktitle: Other approaches
toc: true
type: docs
date: "2020-09-22"
draft: false

menu:
  ratetable:
    weight: 15
---
In the last section of this tutorial, we would like to briefly introduce two other approaches for making rate table from a sub-population (Bower et al. 2018, Rachet et al. 2015). First, to summarise these three approaches, we made a comparison chart shown below: 

|  | Our approach|Bower 2018| Rachet 2015|
| -| --------------| --------------|--------------|
|Nationwide life table| x | v |Flexible relation model: v Flexible Poisson model: x|
| Individual data on a reference population| v| v| individual-level or grouped data|
| Dealing with unceratinty|x|Parametric bootstrapping|Using data on multiple sampling populations|
| Characteristics|Modelling mortality rates directly without requiring nationwide life table |Stabilised  by nationwide life table| Alternative to avoiding integrating the pattern of nationwide life table, but still obtaining robust estimates|

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

[Rachet et al. (2015)](https://doi.org/10.1186/s12889-015-2534-3)  proposed two alternative approaches to construct smoothed life tables for sub-populations. Both these two models can be applied into either abriged or complete data. That is to say, both individual-level data and grouped data can be modelled.

#### Flexible relational model
The first one, flexible relational model, requires survival function of a reliable standard population, $l_{x_{s}}$, and multivariables, such as deprivation level and the interaction between deprivation level and age.

$$
\begin{aligned}
\operatorname{logit}\left(l_{x, i}\right) &=f\left(\log i t\left(l_{x_{s}}\right)\right) +\sum_{i=2}^{5} \beta_{i} \text {dep}_{i}+g(\text {agedep})
\end{aligned},
$$
where $f$ and $g$ are restricted cubic spline functions.

#### Flexible Poisson model
The second one, flexible Poisson model, does not require  external infromation on standard population mortality, and it models age-specific death counts with multiple variables.
The advantage is that this approach takes account of the variance existing in the data.

$$
\begin{aligned}
\log \left(d_{x, i}\right) &=\beta_{0}+f(x)+\sum_{i=2}^{5} \beta_{i} \text {dep}_{i}+g(\text {agedep})+\log \left(\text {pyrs}_{x, i}\right)
\end{aligned},
$$



### Conclusion
Our approach is distinct from Bower et al. and Rachet et al. , since only individual-level data is required to model the mortality rate directly from a representative population free of the disease of interest, the advantages of which are that in some situation nationwide life tables are not available and allowing more variance on the estimates directly generated from the reference population. However, the disadvantage is that the estimates of the model is not aided by the underlying pattern of existing life tables.

In terms of concerns on uncertainty, Bower et al. used a sensible approach of bootstrapping to tackle the issue, which is eligible to be considered in optimising our approach.

### References
Bower H, Andersson TM, Crowther MJ, Dickman PW, Lambe M, Lambert PC. Adjusting Expected Mortality Rates Using Information From a Control Population: An Example Using Socioeconomic Status. *Am J Epidemiol.* 2018;187(4):828-836. [doi: 10.1093/aje/kwx303](https://doi.org/10.1093/aje/kwx303).

Rachet B, Maringe C, Woods LM, Ellis L, Spika D, Allemani C. Multivariable flexible modelling for estimating complete, smoothed life tables for sub-national populations. *BMC Public Health*. 2015;15:1240. [doi: 10.1186/s12889-015-2534-3](https://doi.org/10.1186/s12889-015-2534-3).