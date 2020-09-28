---
title: Untied time data → Tied
linktitle: Untied → Tied
toc: true
type: docs
date: "2020-09-24"
draft: false

menu:
  relsurv:
    weight: 17
---
(Not completed yet)

The codes used in this tutorial are available below. \
[rs.surv](https://enochytchen.com/tutorials/relsurv/untied/using_sim.R) \
 [stpp, strs, stnet](https://enochytchen.com/tutorials/relsurv/untied/using_sim.do)


 

### Dataset
The dataset (```scenario2_1.dta```) is a simulated data, containing 1000 subjects. The original survival time is untied data with 651 distinct values in years (min: 0.0027; max: 12). We then added ties into the data by flooring the time into discrete days, weeks, months, quaters, or years. More ties are added (i.e., less distinct values) as the time interval becomes larger (days→years).

The following table shows how the time was treated in different units.

|Time variable| Discrete time in| Stata code| Distinct values|
|-------| ------| ------|  ------| 
|tt1| Original continuous time| |651|
|tt2| Days| floor(tt1*365.241)/365.241|569|
|tt3| Weeks| floor(tt1*52.177)/52.177|332|
|tt4| Months| floor(tt1*12)/12|132|
|tt5| Quarters| floor(tt1*4)/4|49|
|tt6| Years| floor(tt1)|13|



## Relative survival estimates
The following tables show the estimates of 1-, 5-, and 10-year relative survival (RS) using the Pohar-Perme estimator by ```rs.surv```, ```stpp```, ```strs```, and ```stnet```. To make the tables look tidier, here we dismissed the 95% CI, which however can be found in the outputs of the syntax.

### rs.surv()

|RS|tt1|tt2|tt3|tt4|tt5|tt6|
|-------| ------|-----|----|-----|-----|-----|
|1|0.797|0.812 |0.823 |0.852 | 0.884 | 0.915|
|5|0.618|0.630 | 0.639| 0.667 |0.691 | 0.769|
|10|0.534|0.544|0.552 | 0.572 | 0.594| 0.644|

### stpp

|RS|tt1|tt2|tt3|tt4|tt5|tt6|
|-------| ------|-----|----|-----|-----|-----|
|1|0.797|0.812 |0.823 |0.853 | 0.886 |0.919|
|5|0.613|0.625 | 0.634| 0.663 |0.690 |0.775|
|10|0.513|0.522|0.530 | 0.551 | 0.575|0.633|

### strs
|RS|tt1|tt2|tt3|tt4|tt5|tt6|
|-------| ------|-----|----|-----|-----|-----|
|1|0.798|0.812 |0.823 |0.853 | 0.886 |0.920|
|5|0.612|0.624 | 0.633| 0.663 |0.689 | 0.775|
|10|0.513|0.523|0.531 | 0.551 | 0.576|0.636|


### stnet
|RS|tt1|tt2|tt3|tt4|tt5|tt6|
|-------| ------|-----|----|-----|-----|-----|
|1|0.798|0.812 |0.823 |0.861 | 0.909 | 1.037|
|5|0.612|0.624 | 0.633| 0.661 |0.698 | 0.796|
|10|0.513|0.523|0.531 | 0.553 | 0.584| 0.667|