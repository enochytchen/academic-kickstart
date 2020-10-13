---
title: Untied time data → Tied
linktitle: Untied → Tied
toc: true
type: docs
date: "2020-10-08"
draft: false

menu:
  relsurv:
    weight: 17
---
(Not completed yet)

The codes used in this tutorial are available below. \
[rs.surv](https://enochytchen.com/tutorials/relsurv/untied/using_sim.R) \
 [stns, stpp, strs, stnet](https://enochytchen.com/tutorials/relsurv/untied/using_sim.do)


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

|Time <br/> (year)|tt1<br/> original|tt2 <br/> days|tt3  <br/> weeks|tt4  <br/> months|tt5  <br/> quarters|tt6  <br/> years|
|-------| ------|-----|----|-----|-----|-----|
|1|0.797|0.797 | 0.797 | 0.790 | 0.796 | 0.788|
|5|0.618|0.618 | 0.618 | 0.618 | 0.617 | 0.613|
|10|0.534|0.534| 0.534 | 0.536 | 0.535 | 0.529|

#### stns

|Time <br/> (year)|tt1<br/> original|tt2 <br/> days|tt3  <br/> weeks|tt4  <br/> months|tt5  <br/> quarters|tt6  <br/> years|
|-------| ------|-----|----|-----|-----|-----|
|1|0.788|0.787 | 0.786 | 0.780 | 0.767 | 0.701|
|5|0.580|0.580 | 0.580 | 0.580 | 0.570 | 0.563|
|10|0.464|0.464| 0.464 | 0.465 | 0.465 | 0.463|

#### stpp

|Time <br/> (year)|tt1<br/> original|tt2 <br/> days|tt3  <br/> weeks|tt4  <br/> months|tt5  <br/> quarters|tt6  <br/> years|
|-------| ------|-----|----|-----|-----|-----|
|1|0.797|0.797 |0.796 | 0.790 | 0.777 |0.709|
|5|0.613|0.613 | 0.613| 0.613 | 0.603 |0.595|
|10|0.512|0.512|0.512 | 0.509 | 0.502 |0.484|

#### strs
|Time <br/> (year)|tt1<br/> original|tt2 <br/> days|tt3  <br/> weeks|tt4  <br/> months|tt5  <br/> quarters|tt6  <br/> years|
|-------| ------|-----|----|-----|-----|-----|
|1|0.798|0.797 |0.795 | 0.792 | 0.778 | 0.732|
|5|0.612|0.612 |0.612 | 0.616 | 0.605 | 0.617|
|10|0.513|0.513|0.513 | 0.512 | 0.505 | 0.506|


#### stnet
|Time <br/> (year)|tt1<br/> original|tt2 <br/> days|tt3  <br/> weeks|tt4  <br/> months|tt5  <br/> quarters|tt6  <br/> years|
|-------|------|-----|----|-----|-----|-----|
|1|0.798 |0.797 |0.795 | 0.798 | 0.798 | 0.799|
|5|0.612 |0.612 |0.612 | 0.612 | 0.613 | 0.614|
|10|0.513|0.513 |0.513 | 0.513 | 0.513 | 0.514|

### Explanation
- Generally, introducing ties into the data did not change the esitmates of relative survival no matter which package was used for calculation in this case. One exception happened if discrete time was made into years (```tt6```), which merely 13 distinct values exist, ```rs.surv()``` and ```strs``` gave slightly lower estimates for 1-year relative survival (0.788 and 0.732 separately);  ```stns``` and ```stpp``` gave much lower survival, particularly in 1-year net survival; surprisingly, the output estimated by ```stnet```almost did not change given heavy ties were added.
- One thing we should bear in mind is that introducing ties to the data has in fact changed the original data. I tried to add ties in a sensible way and not to change the order of the data as much as possible. However, I am innocent of the lower or higher estimates  ```rs.surv()```, ```stns```, ```stpp```, and ```strs``` gave. 

### Conclusion
- It is common that the data from cancer register has tied time, e.g., due to protection on patients' privacy. As estimating survival, we should pay attention to the potential change on the estimates given the amount of tie is introduced.