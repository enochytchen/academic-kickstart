---
title: FAQ
linktitle: FAQ
toc: true
type: docs
date: "2020-09-30"
draft: false

menu:
  relsurv:
    weight: 20
---
The following FAQs are some tips we should be aware of as using these programs to estimate relative survival.

1. In ```strs``` and ```stnet```, life-table framework is implemented to estimate relative survival. However, should the cutpoints be in months ```br(0(`=1/12')10)```  or in years ```br(0(1)10)```? \
A: Monthly estimate is more accurate. 

Thanks. Let’s look at it on Wednesday.
 
The answer to your question is that monthly intervals are more accurate.
 
-strs- calculates the attained age and attained year at the start of each interval and looks up the popmort file for the floor() of those values. It then assumes the expected probability of death is applicable for the entire interval.
 
The probabilities in the popmort file are the probabilities of surviving 1 year. The probability of surviving the interval is given by p^k where k is the interval length. That is, for monthly intervals we take the twelfth root of the probability.
 
This means that if a person is 71.5 years old at diagnosis then, with annual intervals it is assumed they have the expected survival of a 71 year old for the first year of follow-up.
 
If you use monthly intervals then we assume the expect survival is that of a 71 year old until he turns 72 after which we use the expected survival of a 72 year old. That’s because we are looking up the popmort file at the start of every interval. With monthly intervals we are doing it 12 times and for annual intervals we are doing it just once.
 
I think these comparisons you are doing are interesting.
 
It was before your time, but when the Pohar Perme paper was published (that showed Ederer II was biased) there were a lot of people who were alarmist and essentially said “everything we’ve done is wrong and all previous methods should be abandoned”. Paul Lambert and I tried to bring back some sanity and showed that, in practice, the bias in the age-standardised estimates is negligible (https://doi.org/10.1186/s12874-015-0057-3).
 
I think it’s interesting to look at these other small differences due to various assumptions that can be made (e.g., assuming everyone is diagnosed on their birthday) and comparing the magnitude of the differences to the bias in Ederer II.
 