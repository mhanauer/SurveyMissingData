---
title: "SurveyMissingData"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```
Here is an example of using a created data set on how to analyze imputed survey data in R with mitools, Ameila and the survey packages.

First, we start by using the library function to library all of the packages and create the data set with missing data.
```{r}
library(mitools)
library(Amelia)
library(survey)
data = data.frame(a = rnorm(10), b = rnorm(10), w = abs(rnorm(10)))
# Now we need to create missing data
data[2:4,1] = NA
data[8:10, 2] = NA
data
```
Now we are going to impute the missing values in the example data set, by running the data set through the amelia function setting x equal to the data set and m equal to the number of imputed data sets we desire, five in this example.  

Then we need to grab the imputed data sets from the a.out object we created above, which are located in a.out$imputations, and transform these imputed data sets into an imputation list that the survey package and mitools can read.
```{r}
m <- 5
# Create the ameila data set with 
a.out <- amelia(x = data, m=m)
a.out.imp = imputationList(a.out$imputations)
```
Next, we can create the survey design object, with the svydesign command.  For this data, there is no id so we use the value 1, which indicates now no id value, we set the weights to w, and the data as the imputed data sets that were transformed into an imputation list, a.out.imp.

Now we can appropriately weight the survey statistics and parameter estimates that we want to analyze.  To do this we need to use the with command, starting with the survey design object, designs in this example, and then select the analysis we want.  Below we have an analysis extracting the mean for the variable a and a bivariate regression with the imputed data sets and appropriate survey weights.

Finally, we can combine the results from the analyses, the mean of a and a bivariate regression of a on b in this example, using the MIcombine function in the mitools package to appropriately combine the results from the five imputed data sets into one mean and one set of parameter estimates in this example.  
```{r}
designs<-svydesign(id =~ 1, weights =~ w, data=a.out.imp)
testMeans = MIcombine(with(designs, svymean(~a)))
testMeans

model1 = with(designs, svyglm(a ~ b)) 
summary(MIcombine((model1)))
```

