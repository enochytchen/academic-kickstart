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
|tt2| Days| floor(tt1*365.241)/365.241 <br/> replace tt2 = 0.5/365.241  if tt2 == 0|569|
|tt3| Weeks| floor(tt1*52.177)/52.177 <br/> replace tt3 = 0.5/52.177   if tt3 == 0|332|
|tt4| Months| floor(tt1*12)/12 <br/> replace tt4 = 0.5/12       if tt4 == 0|132|
|tt5| Quarters| floor(tt1*4)/4 <br/> replace tt5 = 0.5/4        if tt5 == 0|49|
|tt6| Years| floor(tt1) <br/> replace tt6 = 0.5	       if tt6 == 0|13|



## Relative survival estimates
The following tables show the estimates of 1-, 5-, and 10-year relative survival (RS) using the Pohar-Perme estimator by ```rs.surv```, ```stpp```, ```strs```, and ```stnet```. To make the tables look tidier, here we dismissed the 95% CI, which however can be found in the outputs of the syntax.

### rs.surv()

|RS|tt1<br/> original|tt2 <br/> days|tt3  <br/> weeks|tt4  <br/> months|tt5  <br/> quarters|tt6  <br/> years|
|-------| ------|-----|----|-----|-----|-----|
|1|0.797|0.797 | 0.796 | 0.790 | 0.777 | 0.708|
|5|0.618|0.618 | 0.618 | 0.618 | 0.607 | 0.595|
|10|0.534|0.534| 0.533 | 0.531 | 0.522 | 0.498|

### stpp

|RS|tt1<br/> original|tt2 <br/> days|tt3  <br/> weeks|tt4  <br/> months|tt5  <br/> quarters|tt6  <br/> years|
|-------| ------|-----|----|-----|-----|-----|
|1|0.797|0.797 |0.796 |0.793 | 0.785 |0.732|
|5|0.613|0.613 | 0.614| 0.616 |0.610 |0.617|
|10|0.513|0.513|0.513 | 0.512 | 0.509|0.504|

### strs
|RS|tt1<br/> original|tt2 <br/> days|tt3  <br/> weeks|tt4  <br/> months|tt5  <br/> quarters|tt6  <br/> years|
|-------| ------|-----|----|-----|-----|-----|
|1|0.798|0.797 |0.795 | 0.792 | 0.778 | 0.732|
|5|0.612|0.612 |0.612 | 0.616 | 0.605 | 0.617|
|10|0.513|0.513|0.513 | 0.512 | 0.505 | 0.506|


### stnet
|RS|tt1<br/> original|tt2 <br/> days|tt3  <br/> weeks|tt4  <br/> months|tt5  <br/> quarters|tt6  <br/> years|
|-------|------|-----|----|-----|-----|-----|
|1|0.798 |0.797 |0.795 | 0.798 | 0.798 | 0.799|
|5|0.612 |0.612 |0.612 | 0.612 | 0.613 | 0.614|
|10|0.513|0.513 |0.513 | 0.513 | 0.513 | 0.514|
