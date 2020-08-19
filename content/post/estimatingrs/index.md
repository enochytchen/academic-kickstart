---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Estimating relative survival: using rs.surv() vs. strs"
subtitle: ""
summary: "As estimating relative survival, it is commonly done by using rs.surv() in R and strs in Stata. But are the results identical?"
authors: ["Enoch Chen"]
tags: [expected survival, relative survival]
categories: []
date: 2020-08-19
lastmod: 2020-08-19
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---
 {{% toc %}}
### Introduction

The codes of this blog post can be found at [rssurv.R](https://enochytchen.com/post/estimatingrs/rssurv.R) and [strs.do](https://enochytchen.com/post/estimatingrs/strs.do).

The beauty of using relative survival to estimate net survival is that cause of death information is not required, whereas cause of death needs to be classified completely within cause-specific framework (1). Both ```relsurv::rs.surv()``` command in R and ```strs``` command in Stata supports estimating net survival in relative survival framework. However, people might be curious about the difference in between. Here is an example below of using the same dataset, identical definition of status (alive, dead from cancer, or dead from other causes), and same approaches (Ederer II and Pohar Perme estimator) to estimate relative survival using ```rs.surv()``` and ```strs```.

### Ederer II esitmator
#### ```rs.surv()``` in R
```
rssurv_e2 <- rs.surv(Surv(time  =  (decimal_date(exit)-  decimal_date(dx)) * 365.241, # time in days
                          event = status== 1 | status== 2 ) ~ sex,
                     rmap = list(age = age * 365.241, year = dx),
                     data = colon1,
                     ratetable = popmort_new,
                     method = "ederer2")

rs.table_e2 <- as.data.frame(summary(rssurv_e2, time = c(0:10) * 365.241, scale = 365.241)
                             [c("strata", "time", "n.risk", "n.event", "surv", "std.err", "lower", "upper")])

#' Rename the columns
strs_e2 <- rs.table_e2 %>%
  mutate(cr_e2 = surv, 
         lo_cr_e2 = round(lower, digits = 4),
         hi_cr_e2 = round(upper, digits = 4),
         sex = ifelse(strata == "sexFemale=0", "Male", "Female"),
         
  )%>%
  select(sex, time, n.risk, n.event, r, cr_e2, lo_cr_e2, hi_cr_e2)
```
{{<figure src="rssurv_e2.png" title="Estimating net survival using rs.surv(), Ederer II">}}
#### ```strs``` in Stata
```r
use http://enochytchen.com/directory/data/colon.dta if stage==1, clear
stset exit,fail(status==1 2) origin (dx) exit(exit) id(id) scale(365.241) 
strs using popmort, br(0(1)10) mergeby(_year sex _age) by(sex)
```
{{<figure src="strs_e2.png" title="Estimating net survival using strs, Ederer II">}}


### Pohar-Perme estimator
#### ```rs.surv()``` in R
```
rssurv_pohar <- rs.surv(Surv(time  =  (decimal_date(exit) - decimal_date(dx)) * 365.241, # time in days
                             event = status== 1 | status== 2 ) ~ sex,
                        rmap = list(age = age * 365.241, year = dx),
                        data = colon1,
                        ratetable = popmort_new,
                        method = "pohar-perme")

rs.table_pohar <- as.data.frame(summary(rssurv_pohar, time = c(0:10) * 365.241, scale = 365.241)
                                [c("strata", "time", "n.risk", "n.event", "surv", "std.err", "lower", "upper")])

#' Rename the columns
strs_pohar <- rs.table_pohar %>%
  mutate(cr_pp = surv, 
         lo_pp = round(lower, digits = 4),
         hi_pp = round(upper, digits = 4),
         sex = ifelse(strata == "sexFemale=0", "Male", "Female"),
         r   =   round( cr_pp/lag(cr_pp), digits = 4),
  )%>%
  select(sex, time, n.risk, n.event, cr_pp, lo_pp, hi_pp)
```
{{<figure src="rssurv_pp.png" title="Estimating net survival using rs.surv(), Pohar-Perme">}}

#### ```strs``` in Stata
```r
use http://enochytchen.com/directory/data/colon.dta if stage==1, clear
stset exit,fail(status==1 2) origin (dx) exit(exit) id(id) scale(365.241) 
strs using popmort, br(0(1)10) mergeby(_year sex _age) by(sex) pohar
```
{{<figure src="strs_pp.png" title="Estimating net survival using strs, Pohar-Perme">}}


### Reference
1. Dickman2006

