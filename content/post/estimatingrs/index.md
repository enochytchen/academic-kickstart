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
The beauty of using relative survival to estimate net survival is that cause of death information is not required, whereas cause of death needs to be classified completely within cause-specific framework (1). Both ```relsurv::rs.surv()``` command in R and ```strs``` command in Stata supports estimating net survival in relative survival framework. However, people might be curious about the difference in between. Here is an example below of using the same dataset, identical definition of status (alive, dead from cancer, or dead from other causes), and same approaches (Ederer II and Pohar Perme estimator) to estimate relative survival using ```rs.surv()``` and ```strs```.

### EdererII esitmator

#### ```strs``` in Stata
```r
use http://enochytchen.com/directory/data/colon.dta if stage==1, clear
stset exit,fail(status==1 2) origin (dx) exit(exit) id(id) scale(365.241) 
strs using popmort, br(0(1)10) mergeby(_year sex _age) by(sex)
```

### Pohar Perme estimator
#### ```strs``` in Stata
```r
use http://enochytchen.com/directory/data/colon.dta if stage==1, clear
stset exit,fail(status==1 2) origin (dx) exit(exit) id(id) scale(365.241) 
strs using popmort, br(0(1)10) mergeby(_year sex _age) by(sex) pohar
```


### Reference
1. Dickman2006

