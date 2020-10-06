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


 This example showed how the relative survival estimates change given more and more ties are added into the data.

### Dataset
The dataset (```scenario2_1.dta```) is a simulated data, containing 1000 subjects. The original survival time is untied data with 651 distinct values in years (min: 0.0027; max: 12). We then added ties into the data by flooring the time into discrete days, weeks, months, quaters, or years. More ties are added (i.e., less distinct values) as the time interval becomes larger (days→years). If any survival time became 0 due to flooring, we then made it 0.5 of the time interval (e.g., 0.5 months).

The following table shows how the time was treated in different units.

|Time variable| Discrete time in| Stata code| Distinct values|
|-------| ------| ------|  ------| 
|tt1| Original continuous time| |651|
|tt2| Days| (floor(tt1*365.241) + 0.5) /365.241|569|
|tt3| Weeks| (floor(tt1*52.177)  + 0.5) /52.177 |332|
|tt4| Months|  (floor(tt1*12)      + 0.5) /12 |132|
|tt5| Quarters| (floor(tt1*4)       + 0.5) /4   |49|
|tt6| Years|(floor(tt1)         + 0.5)    |13|



### Relative survival estimates
The following tables show the estimates of 1-, 5-, and 10-year relative survival (RS) using the Pohar-Perme estimator by ```rs.surv```, ```stpp```, ```strs```, and ```stnet```. To make the tables look tidier, here we dismissed the 95% CI, which however can be found in the outputs of the syntax.

#### rs.surv()

|RS|tt1<br/> original|tt2 <br/> days|tt3  <br/> weeks|tt4  <br/> months|tt5  <br/> quarters|tt6  <br/> years|
|-------| ------|-----|----|-----|-----|-----|
|1|0.797|0.797 | 0.797 | 0.790 | 0.796 | 0.788|
|5|0.618|0.618 | 0.618 | 0.618 | 0.617 | 0.613|
|10|0.534|0.534| 0.534 | 0.536 | 0.535 | 0.529|

#### stpp

|RS|tt1<br/> original|tt2 <br/> days|tt3  <br/> weeks|tt4  <br/> months|tt5  <br/> quarters|tt6  <br/> years|
|-------| ------|-----|----|-----|-----|-----|
|1|0.797|0.797 |0.797 |0.798 | 0.796 |0.789|
|5|0.613|0.613 | 0.613| 0.613 |0.613 |0.615|
|10|0.512|0.512|0.512 | 0.514 | 0.515|0.519|

#### strs
|RS|tt1<br/> original|tt2 <br/> days|tt3  <br/> weeks|tt4  <br/> months|tt5  <br/> quarters|tt6  <br/> years|
|-------| ------|-----|----|-----|-----|-----|
|1|0.798|0.798 |0.798 | 0.798 | 0.798 | 0.825|
|5|0.612|0.612 |0.612 | 0.612 | 0.613 | 0.649|
|10|0.513|0.513|0.513 | 0.513 | 0.513 | 0.554|


#### stnet
|RS|tt1<br/> original|tt2 <br/> days|tt3  <br/> weeks|tt4  <br/> months|tt5  <br/> quarters|tt6  <br/> years|
|-------|------|-----|----|-----|-----|-----|
|1|0.798 |0.798 |0.798 | 0.798 | 0.798 | 0.799|
|5|0.612 |0.612 |0.612 | 0.612 | 0.613 | 0.614|
|10|0.513|0.513 |0.513 | 0.513 | 0.513 | 0.514|
