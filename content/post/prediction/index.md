---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Not easy to be a fortune teller: prediction is hard"
subtitle: ""
summary: "Is it easy to make prediction about the future? Take a look at the UK's and Sweden's life expectancy projection."
authors: ["Enoch Chen"]
tags: [expected survival, life expectancy, Sweden, UK]
categories: []
date: 2020-05-12T14:52:16+02:00
lastmod: 2020-05-12T14:52:16+02:00
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
There are a bunch of quotes related to prediction which can be found at
[It’s Difficult to Make Predictions, Especially About the Future](https://quoteinvestigator.com/2013/10/20/no-predict/). Basically, these quotes do not differ from one another. They generally want to express that predection on future is risky and chanllenging. Moreover, it could lead to quantitative problems, such as underestimation or overestimation.

Recently, while I was working on life expectancy estimation, a colleague proposed a quesiton, "If you are validating expected survival of patients in 1990s, shall you use the survival projection made in 1990s rather than the empircal survival?" As a person living in 2020, it was quite intuitive to me to use empirical data to validate the survival prediction. Nevertheless, practically, statisticians back to 1990s only had projection data. Based on this fact, I was quite convinced that I should have used the projection data made at that time.

However, how precise was the data? Here come two small examples of validating life expectancy projection in the UK and Sweden.

### The UK's life expectancy projection
The UK's Office for National Statistics has ever published a report of validating the accurancy of life expectancy. The following figure was downloaded from [National Population Projections Accuracy Report](https://www.ons.gov.uk/peoplepopulationandcommunity/populationandmigration/populationprojections/methodologies/nationalpopulationprojectionsaccuracyreport) (1).

{{< figure src="./uk_LEprojection.png" title="Actual and projected life expectancy at birth, UK, 1966-2030" >}}

It seems that the projection made before 2002 did not perform quite well. For example, projection made in 1975 saying males' life expectancy at birth was estimated to be 71 in 2001, whereas in 2001 it was actuall 79. A 8 years of difference! Then let's take a look on the Swedish population. 

### The Sweden's life expectancy projection
Statistiska centralbyrån (Statistics Sweden) also published a similar report as the UK did to evaluate their previous projection on life expectancy. The following figure was downloaded from [Sveriges framtida befolkning 2012–2060](http://share.scb.se/ov9993/data/publikationer/statistik/_publikationer/be0401_2012i60_br_be51br1202.pdf)(2).
  
  {{< figure src="./se_LEprojection.png" title="Actual and projected life expectancy at birth, Sweden, 1950-2012">}}

The similar issue happened here as well. Earlier projection (before 2000) was not quite right in comparison with the empirical one.

This report was done in 2012 (the empirical trend until 2012). I then followed what Statistics Sweden conducted to generate a similar graph with the earliest open-access projection data till 2009 and empirical data till 2018 using data from Statistics Sweden (3) as well.

{{< figure src = "./LEprojection_validation_SE_2030.jpg" title = "Actual and projected life expectancy at birth, Sweden, 2000-2030.">}}

The difference was not as large as projection before 2000, but still, there could be 1-2 years of difference if projecting to 2030. 

### Reflection
The validation between the empirical and the projection makes me aware that the longer we predict, the more potential bias may happen as well. This is just an example of why projection into the future could be adventurous. Imagine that if it is already uncertain in life expectancy prediction of the general population, would it be more difficult to predict a group of people's (such as a patient cohort's) survival? For sure, I would pretty much say yes because multiple factors can all contribute to the change of that specific group of people, like advancement of treatment, geographical difference, healthcare delievery, etc.

Then shall we give up projection? I would however say no. I believe making prediction on something is better than knowing nothing and going ahead, even though people can always argue that we are forecasting **the future** and say, "You never know." Well, at least having a reference point is better than none, isn't it? Still, I agree that there is no perfect prediction. Who knows what is happening tomorrow? However, prediction made diligently is more believable than casually forecasting the future. 

Doesn't my job sound like a fortune teller? 

### Reference
1. Office for National Statistics, UK. National Population Projections Accuracy Report, 2015. Available from:[https://www.ons.gov.uk/](https://www.ons.gov.uk/peoplepopulationandcommunity/populationandmigration/populationprojections/methodologies/nationalpopulationprojectionsaccuracyreport)
2. Statistiska centralbyrån (Statistics Sweden). Sveriges framtida befolkning 2012–2060 (Sweden's future population), 2012. Available from [http://share.scb.se/](http://share.scb.se/ov9993/data/publikationer/statistik/_publikationer/be0401_2012i60_br_be51br1202.pdf)
3. Statistiska centralbyrån (Statistics Sweden). Statistical database: Earlier projections on life expectancy by sex and age. Available from: http://www.statistikdatabasen.scb.se/pxweb/en/ssd/




