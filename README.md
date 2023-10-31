---
title: "Assignment 8 - Multiple Regression Part 2"
author: "Ashlin Samples"
date: "10/31/23"
output:
  word_document: default
  pdf_document: default
---

Problem statements are provided in this file.
Write your brief answer immediately after the :  
(if R code is required, have the R code be AFTER your completed response).

The following chunk loads library regclass and two dataframes (BIKE, AUTO, and TIPS).
(Make sure that the file "Assignment8_SP22.RData" is in the same folder as this markdown file!)


```{r load up regclass, message=FALSE, echo=FALSE}
#Do not change anything in this R chunk
library(regclass)
load("Assignment8_SP22.RData")
knitr::opts_chunk$set(echo=TRUE, warning=FALSE, message=FALSE, collapse=TRUE)
```

**************

**Question 1**:  Use the daframe `AUTO`. We want to predict `FuelEfficiency` from a car's characteristics.

Hint: `Chapter4pt1` slide 15, 107, 19-30;  `Chapter4pt2` slide 39-46

*a:*  Make the scatterplot matrix of the variables in the dataframe.  Do the relationships of each predictor with `FuelEfficiency` look roughly linear?  If not, which ones don't?
 
**Response:**
 
```{r q1 scatterplot matrix}
#scatterplot matrix
M<-lm(FuelEfficiency~.,data=AUTO)
pairs(FuelEfficiency~.,data=AUTO)
plot(FuelEfficiency~.,data=AUTO)
```


**Grading:** 5 points


*b:*  Fit the "full model" (with all variables in it) using the "twidle dot" shortcut.  Examine  `summary` as well as `check_regression` on the model (with `extra=TRUE`).  Discuss at least one issue with the regression that *potentially* makes it a bad model.

**Response:**

```{r q1 first regression}
#fitting model
#checking model
FM <- lm(FuelEfficiency ~ ., data = AUTO)
summary(FM)
check_regression(FM, extra = TRUE)
```

**Grading:** 5 points



*c:* Nonlinearities may exist in the relationships, so let us try polynomial fits.  Fit five separate simple linear regressions (one for each predictor) and run `choose_order` (use max.order = 6) on each. Report the selected orders for each predictor based on the 0.005 heuristic. 


**`Cylinders`**

**`Displacement`**

**`Horsepower`**

**`Weight`**

**`Acceleration`**


```{r q1 choosing orders, eval=FALSE}  
#Note:  I have set eval=FALSE here so the code shows up but is not evaluated in the document
#This is fine, just make sure to choose and report the correct order!
Cylinders=lm(FuelEfficiency~Cylinders, data= AUTO)
choose_order(Cylinders,max.order=6)

```

**Grading:** 2.5 points each, 10 points total.

*d:* Fit a multiple regression model with the relevant polynomial terms and call it `M.poly`.  Report $R^2_{adj}$ and the $RMSE$ of this model and compare them to the model in *b:*.  Comment on whether these extra terms have  "significantly" improved the model.  
Note:  your command should look something like the line in the following chunk (replace the question marks with the selected orders):


**Response:**

```{r poly}
#M.poly <- lm(FuelEfficiency~poly(Cylinders,?)+
#                            poly(Displacement,?)+
#                            poly(Horsepower,?)+
#                            poly(Weight,?)+
#                            poly(Acceleration,?), data=AUTO)

M.poly <- lm(FuelEfficiency ~ 
#put number from above in for question marks

#summary(M.poly)


```

**Grading:** 5 points

*e:*  Run `check_regression` on the polynomial model (be sure to add `extra=TRUE`). Comment on whether you think the model's assumptions are satisfied.

**Response:** 

```{r check poly}
#check polynomial model
check_regression()

```

**Grading:** 5 points


**************

**Question 2**:  Use the dataframe `BIKE`.


*a:*  Fit a model predicting `Demand` from all predictors and look at its `summary`. Examine the influence plot for this model.

Hint: `Chapter4pt2` slide 3 - 18, 75.

```{r q2a fitting model and influence plot}
#fit model
P=lm(Demand~.,data = BIKE)
#summary
summary(P)
#influence_plot
influence_plot(P)

```

**Grading:** 5 points


*a1:*  There is one extremely influential point in this dataset.  Show the influential row, then explain what makes it so unusual through the use of the scatterplot matrix (its not possible to see why its unusual with `summary` on the individuals variables).

**Response:**

```{r q2a1 investigation}
#BIKE[?,]    #Replacing the ? with the row number will print out that row to the screen
#pairs()  #scatterplot matrix
#summary(BIKE) #summaries of each variable


```


**Grading:** 5 points


*a2:* The influential point represents a data error, so we are justified in removing it.  Re-fit the model *without* this point and examine the results of `summary` on the model.  Comment on the change in $R^2$ and $RMSE$ (has it gotten better by throwing away the point?) and the significance of predictors using this new model.  

**Response:**

```{r q2a2 newmodel}
#M.new <- lm(Demand~.,data=BIKE[-?,])  #replace ? by the offending row number so the model is fit WITHOUT that row
#summary(M.new)


```

**Grading:** 5 points


*b:* The *strength* of the relationship between `Demand` and the other predictors may not be a constant.  For example, the strength of the relationship between `Demand` and `AvgTemp` may depend on `AvgWindspeed` (on warm days, wind speed may not matter, but on cold days, wind speed may matter a lot).  Fit a model with all two-way interactions (but without that influential point) and use `see_interactions` to scan for possible interactions (note:  add the argument `many=TRUE` or there will be too many plots).  Does it look like we should include interactions into the model?  Why or why not?

Hint: `Chapter4pt2` slide 47 - 73, 80 - 81.


**Response:**

```{r q2b}
#model with all two way interactions omitting the offending row
#see interactions, with argument many=TRUE
P2=lm(Demand~.,^2,data=BIKE([-317])
see_interactions(P2, many=TRUE)


```

**Grading:** 10 points, 5 points for R output, 5 points for explanation.


*c:*  Fit a model predicting `Demand` from `AvgWindspeed` and `AvgHumidity`, including the interaction (but omitting the offensive row).   Examine the results of `summary` and `visualize_model`.  Is the interaction statistically significant?  



**Response:**  

```{r q2c}
#model with all two AvgWindspeed, AvgHumidity and interaction between them  omitting the offending row
#see interactions, with argument many=TRUE
P4=lm(Demand~AvgWindspeed,AvgHumidity, data=BIKE([-317])
see_interactions(P4, many=TRUE)


```

**Grading:** 5 points


**************

**Question 3**:  Use the dataframe `TIPS`.  The percentage tip left on a bill `TipPercentage` has an interesting relationship with the bill amount `Bill` and number of people at the table `PartySize`.

*a:* Fit a model predicting the tip percentage from the bill amount and size of party, including the interaction.  Look at `visualize_model` and `summary`.  You'll see that the interaction is significant but `PartySize` itself is not.  Would we be justified in dropping the `PartySize` term from the model?  Why or why not?

**Response:**

```{r q3a}
#model predicting the tip percentage from the bill amount and size of party with interaction
#see interactions, with argument many=TRUE
R=lm(TipPercentage~(Bill+PartySize)^2, data = TIPS)
visualize_model()



```

**Grading:** 5 points


*b:*  Write out the overall regression equation.  Round each coefficient to two decimal places.

**Response**:


**Grading:** 7 points


*c:*  Now write out the implicit regression equations between tip percentage and bill for parties of size 2 and for parties of size 3.


**Equation when party is size 2**


**Equation when party is size 3**



**Grading:** 12 points, 6 points each.


*d:*  What is the story that the data tells us?  In other words, describe how the relationship between tip percentage and bill amount changes as the party size gets bigger.  Does it get weaker, stronger? Is it positive, negative?  

Hint: `Chapter4pt2` slide 64 - 68.

**Response:**

**Grading:** 6 points


*e:*  A better predictor of tip percentage may be the bill amount *per person*  Define a variable to be `Bill` divided by `PartySize` (call it `Bill.per.Person` and add it to the `TIPS` dataframe).  Then fit a simple linear regression predicting tip percentage from it.  Examine the `summary` output, and comment on whether this is a better model than predicting tip percentage from bill, party size, and the interaction from *a:*

**Response:**

```{r q3e}
#Code defining and adding bill per person to the dataframe
#Code fitting the simple linear regression
#Code looking at summary


```

**Grading:** 10 points, 5 points for R output, 5 points for explanation.
