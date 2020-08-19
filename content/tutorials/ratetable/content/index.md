---
title: Make ratetable using R
linktitle: Content
toc: true
type: docs
date: "2020-08-19"
draft: false

menu:
  ratetable:
    weight: 10
---
The syntax used in this tutorial can be found [here](create_ratetable.R).

### Data preparation
First, please install and use the packages needed in the following syntax, and read the colon data.

```r
#' 1 Preparation

#' Clear all
#' Use it if you need to clear all
#' rm(list = ls()) 

#' Load required packages
x<-c(  "haven",       # read.dta()
       "tidyverse",   # dplyr::mutate
       "lubridate",   # decimal_date
       "survival",    # Surv(), survSplit()
       "rstpm2",      # stpm2()
       "splines",     # nsx()
       "relsurv",     # transrate(), joinrate(), rs.surv()
       "popEpi",      # as.data.frame(ratetable)
       "ggplot2")     # ggplot()

lapply(x, require, character.only = TRUE)

#' Read the data from web 
colon <- read_dta( "http://enochytchen.com/directory/data/colon.dta")
str(colon)
```

We then exacted the birth date of each observation by using dx (date of diagnosis) - age at diagnosis * 365.241. The unit here is days in order to be consistent with the unit in ```relsurv::rs.surv()```. Variables sex, subsite, stage, and strata were converted into factor class, which will be automatically converted into dummy varaibles in a regression model in R. In this example, we used two dimensions: subsite + stage, and combined them into one stratum, a combination of all the dimensions except sex, age, and year. Splitting the data frame into lists will be done on this stratum afterwards.  However, if there is only one extra dimension, it is not required to create a stratum. Instead, it is straightforward to split on that single variable.

```r
#' Extract the date of birth,
#' Will take care of derived variables (entry/exit years) later
colon2 <- colon %>%
  mutate(sex     = as_factor(sex),       ## as_factor preserves labels
         status  = as.numeric(status),
         subsite = as_factor(subsite),   ## as_factor preserves labels
         stage   = as_factor(stage),     ## as_factor preserves labels
         strata  = as_factor(paste(subsite, stage, sep = ", ")), # strata consists all the dimensions
         dob     = as.Date(dx) - age*365.241,
  ) %>%
  select(id, sex, status, subsite, stage, strata, dob, dx, exit, age)
str(colon2)
summary(colon2)
```
### Splitting the time
Since the secondary time scale in this example is calendar year, we splitted the survival data time at specified times (2 years). One-year interval would lead to convergence problem on ```stpm2``` due to insufficient sample size within each time interval, so we splitted into 2-year intervals instead. 

It is worth taking a look at the definition of ```episode``` in ```
survival::survSplit```, where it explains, "```episode``` 1= less than the first cutpoint, 2= between the first and the second." Based on this default setting, we then moved each episode (period) by 2.

```
#' Split calendar time into 2-year intervals;
#' splitting in 1-year intervals leads to convergence problems later if
#' stpm2 does not have enough events within each time interval
colon_split <- survSplit(Surv(decimal_date(dx), decimal_date(exit), status, type = "mstate") ~ .,
                         data = colon2, cut = seq(1975, 1995, by = 2),
                         event = "status", episode = "period"
) %>%
  # changed word "censor" to 0, so to keep it consistent with original definition
  mutate(status = as.numeric(ifelse(as.character(status) == "censor",
                                    "0", as.character(status))) )

#' Inspect: select the first 20 to take a look
head(colon_split, 20)

#' For downstream analysis, we want age as primary time scale;
#' Calculate age at entry and age at exit
#' Also, it would be nice to have the period expressed as actual starting year
#' of the time interval; see ?survSplit for its definition
#' (i.e. 1 = before first interval)
colon_split = mutate(colon_split,
                     age_start = tstart - decimal_date(dob),
                     age_stop  = tstop  - decimal_date(dob),
                     period    = 1975 + (period - 2)*2) # *2 for 2-year intervals

#' Save the data for running AIC/BIC test
#' saveRDS(colon_split, "./Data/split_colon.rds")
```

### Flexible parametric model
The colon cancer patients' data was modelled in multiple time scales, where the primary time scale is attained age calculated from two time points (```age_start``` and ```age_stop```), and the secondary is time-split calendar year fitted a natural spline using ```nsx()``` . Time-dependent effect of explanatory varibles was taken into consideration specifying ```tvc=list()```.

```
fpm <- stpm2(Surv(time = age_start, time2 = age_stop, event = status==2) ~
                  sex + nsx(period, df=2) + subsite+ stage, data = colon_split,
             tvc = list(sex=3, period=2), df = 3)
summary(fpm)

#' A nice hazard plot
#' Age-specific hazard by sex, subsite=2, stage =1, 1980
newdata = data.frame(sex = levels(colon2$sex), 
                     subsite = "Descending and sigmoid", 
                     stage = "Localised",
                     period = 1980)
newdata
plot(fpm, newdata = newdata[2,,drop=FALSE],
     type = "hazard", ci = FALSE,
     xlim = c(40,110), xlab = "Attained age (years)",
     ylim = c(1E-6,100), ylab  = "Hazard (log-scale)",
     log = "y", main = "Age-specific log-hazard for subsite=2, stage =1, period 1980, by sex")
lines(fpm, newdata = newdata[1,,drop=FALSE],
      type = "hazard", lty = 2, col = "red")
legend("topleft", legend=c("Female", "Male"),
       col=c("black", "red"), lty=1:2)                   
```
{{< figure src="loghazard.jpeg" >}}

### Predicted rates in a data frame
An empty data frame was generated using ```expand.grid()``` stratified by all the dimensions: sex, age_stop (age), period (year), subsite, and stage. The names of the variables must be consistent with which in the flexible parametric model. Then, ```predict()``` was applied to obtain predicted rates (i.e.,survival) in a data frame, similar to the concept in the extrapolation example using ```predict``` in ```Stata```: [Extrapolating survival (all-cause survival framework)](enochytchen.com/tutorials/extrapolation/allcause/article/).

```
colon_new <- expand.grid(sex = levels(colon2$sex), 
                         subsite = levels(colon2$subsite),
                         stage = levels(colon2$stage),
                         age_stop = 1:110, 
                         period = 1975:1995)

#'Populate the empty data frame with predicted hazards (based on the fitted model)
colon_new$hazard <- predict(fpm, newdata = colon_new, type = "hazard")

colon_new <- colon_new %>%
  mutate(prob  = exp(-hazard),
         age   = age_stop,   
  ) %>%
  select(sex, subsite, stage, age, period, prob, hazard)
str(colon_new)

#' Take look at the first 20 rows
head(colon_new, 20)
```
### Create ratetable
**Here comes the highlight of this tutorial!** \
The target focuses on how to transform the mortality rate dataframe into to a ```ratetable``` class data, which is required in ```rs.surv()```.
The subset of the mortality rate data frame, popmort_new, consists of age, sex, period, strata, and prob (survival probability). 

First, we splitted the data frame into lists by the varaible strata (subsite + stage in this example. i.e., all the combinations of the extra dimensions). Second, we splitted the lists by sex, so we are supposed to obtain the lists stratified by both strata and sex. 

```spread()``` was then applied to transpose the matricies. Afterwards, ```transrate()```  combined the lists stratified by sex and ```joinrate()``` made the lists stratified by strata into a ```ratetable```. 

```
popmort_new <- colon_new %>%
  mutate(strata = paste(subsite, stage, sep = ", "),
  )%>%
  select(age, sex, period, strata, prob)

#' transrate() wants two matrices, both age x year, one for men, one for
#' women;  using split() repeatedly to make it work
#' split() converted a data.fram into lists based on the specified variable

#' First, split our popmort file by strata
pm_split = split(popmort_new[, -4], popmort_new$strata)
str(pm_split)

#' Then we split the list again by sex
pm_split = lapply(pm_split, function(x) split(x[, -2], x$sex))
str(pm_split)

#' Using spread + as.matrix to generate the input matrices that transrate()
#' needs
spread_df = function(x)
{
  ret =  spread(x, period, prob)
  rownames(ret) = ret$age - 1 # Drop the age variable
  ret = ret[, -1]
  as.matrix( ret )
}
pm_split = lapply(pm_split, function(x) lapply(x, spread_df ))
str(pm_split)

#' Now do the transrate() for each strar=ta; we get a list of
#' ratetable-objects
pm_split = lapply(pm_split, function(x) transrate(x$Male, x$Female, yearlim = c(1975, 1995)))
str(pm_split)

#' We can directly use the jointable-command on this list
myratetable <- joinrate(pm_split, dim.name="strata")
str(myratetable)

#' Check whether is a readable ratetable for rs.surv()
is.ratetable(myratetable) 
```


### Use rs.surv() to estimate net survival
It is important to keep in mind that in ```relsuv::rs.surv()``` the unit of follow-up time is specified in days (1). Thus, given that the time in the original colon dataset is in years, we need to multiply the time by 365.241. The same rule applies to age, where in ```relsuv::rs.surv()::rmap``` age should be multiplied by 365.241 as well.
The rate table was generated from this patient data. Therefore, if we estimate net survival using the same data, we should get relative survival close to 1, shown in the following syntax and graph.

```
rssurv<-rs.surv(Surv(time  =  (decimal_date(exit)-  decimal_date(dx)) * 365.241,
                     event = status == 2) ~
                  sex  + subsite+ stage,                     
                rmap = list(age = age*365.241, year = dx, strata = strata),
                ratetable = myratetable,
                data = colon2,
                method = "ederer2")

rssurv.sum <- summary(rssurv, time = c(0:10) * 365.241, scale = 365.241)
rs.table   <- as.data.frame(rssurv.sum[c("strata", "time", "n.risk", "n.event", "surv", "std.err", "lower", "upper")])

#' Cut the value from the strata
#' We split the strata to get variables
rs.table_temp <- data.frame(do.call(rbind, 
                                    strsplit(as.character(strsplit(as.character(rs.table$strata), ",")),"=", fixed=TRUE)
))

rs.table_temp <- rs.table_temp %>%
  mutate(sex        = substr(X2,1,1),
         subsite2   = substr(X3,1,1),
         subsite3   = substr(X4,1,1), 
         subsite4   = substr(X5,1,1),
         Localised  = substr(X6,1,1),
         Regional   = substr(X7,1,1),
         Distant    = substr(X8,1,1),
  )%>% 
  select(sex, subsite2, subsite3, subsite4, Localised, Regional, Distant)

rs.table <- cbind(rs.table[ ,-c(1, 9)], rs.table_temp)

#' Take a look
#' surv here is cumulative relative survival 
head(rs.table, 20)

# Make plotdata for subsite=2 and Localised=1
rssurv.plotdata <- subset( rs.table, (subsite2 == 1 & Localised ==1) )
summary(rssurv.plotdata)
```

### Plot net survival
```
 ggplot(rssurv.plotdata, aes(x = time, y = surv, color = sex, fill = sex )) +
  geom_smooth(alpha = 0.25) +   
  scale_x_continuous(breaks = seq(0, 10, by = 2), limits = c(0,10))+
  scale_y_continuous(breaks = seq(0, 1.2, by = 0.2), limits = c(0,1.2))+
  labs(title="Cumulative relative survival for subsite=2 & stage =localised",
       x="Time since diagnosis (years)", 
       y="Cumulative relative survival") +
  theme(plot.title = element_text(size = 10))
```
{{<figure src = "ggplot.jpeg">}}

### Reference
1. Perme, MP, Pavlic, K. Nonparametric relative survival analysis with the R package relsurv. Journal of Statistical Software. 2018; 87(1), 1-27..

### Appendix: codebook
- sex: 1 (Male)
       2 (Female)
- stage: 0 (Unknown); 
         1 (Localised); 
         2 (Regional); 
         3(Distant)
- subsite: 1(Coecum and ascending); 
           2 (Transverse); 
           3 (Descending and sigmoid); 
           4 (Other and NOS)
- staus: 0 (Alive); 
         1 (dead from colon cancer); 
         2 (dead from other causes)