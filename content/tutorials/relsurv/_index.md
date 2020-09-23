---
# Course title, summary, and position in the list.
Title:  Estimate relative survival $:$ tied data vs. untied data
linktitle: Estimate relative survival 
summary: rs.surv in R and stpp, strs, stnet in Stata are applied to estimate relative survival. What should we pay attention to as using tied/untied data? Why are the estimates different?

# Page metadata.
date: "2020-09-22"
lastmod: "2020-09-22"
draft: false 
toc: true  # Show table of contents? true/false
type: docs  # Do not modify.
menu:
  relsurv: 
    weight: 5
# ### Acknowledgements
#We especially thank [Paul C Lambert](https://pclambert.net) for providing the scenario2_1.dta as one of the example datasets and also for his insight on using
---

### Introduction
Tied event times in survival mean that some individuals have the same survival times (1), though in reality lifetime is a continuous variable; that is, no individual has exactly the same survival times. However, in practice, cancer registers often only provide data on discrete survival times (for example, in months or in years) (2).

Pohar-Perme estimator is a non-parametric estimate of relative survival. It is distinct from other approaches (Ederer I, Ederer II, and Hakulinen), by  weighting by the inverse of the individual's expected survival (3), which makes it an unbiased estimator. For estimating relative survival using Pohar-Perme estimator, there are already multiple packages developed in R and Stata . In ```rs.surv``` and ```stpp```, survival time is treated continuosly, whereas ```strs``` and ```stnet``` implemented Pohar-Perme estimator into discrete time, where survival time is split into intervals.

In this tutorial, two different datasets were applied to compare the estimates of  ```rs.surv``` in R, ```stpp```,  ```strs```,  ```stnet``` in Stata. The colon.dta was used as an example data of tied times (discrete survival times), whereas the scenario2_1.dta has untied times (continuous survival times).



### Aim
- To understand why different esimates exist between/within the commands as they treat tied/untied survival time data using the same Pohar-Perme estimator.



### References
1. Zhang MJ. Tied Survival Times. Encyclopedia of Biostatistics. 2005. doi: https://doi.org/10.1002/0470011815.b2a11075
2. Dickman P, Coviello E. Estimating and modelling relative survival. The Stata Journal. 2015;15:186-215.
3. Pohar Perme M, Stare J, Estève J. On Estimation in Relative Survival. Biometrics. 2012;68:113-120


