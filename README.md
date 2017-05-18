# SurveyMissingData
---
title: "SurveyMissingData"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

Here we are trying the example to get a feel for the program
```{r}
library(mitools)
data = data.frame(a = rnorm(10), b = rnorm(10), w = abs(rnorm(10)))
# Now we need to create missing data
data[2:4,1] = NA
data[8:10, 2] = NA
data

# Now we need to impute the missing data.
library(mice)
data.imp = mice(data)
data.imp
data.imp = imputationList(data)
# So it looks like it just conducts the analyses with the different sets from the 
library(survey)
designs<-svydesign(id =~ 1, weights =~ w, data=data)
designs

results<-with(designs, svymean(~drkfre))

MIcombine(results)

summary(MIcombine(results))
```
Here is the example from the internet
```{r}
library(mitools)
data.dir<-system.file("dta",package="mitools")
files.men<-list.files(data.dir,pattern="m.\\.dta$",full=TRUE)
men<-imputationList(lapply(files.men, foreign::read.dta))
files.women<-list.files(data.dir,pattern="f.\\.dta$",full=TRUE)
women<-imputationList(lapply(files.women, foreign::read.dta))
men<-update(men, sex=1)
women<-update(women,sex=0)
all<-rbind(men,women)
all
designs<-svydesign(id=~ 1, strata=~sex, data=all)

results<-with(designs, svymean(~drkfre))

MIcombine(results)

summary(MIcombine(results))
```

