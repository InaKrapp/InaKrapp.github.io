---
title: Linear Regression in R
summary: A follow-up to the workshop 'Introduction to R', suitable for anyone familiar with the general principle of Linear Regression.
date: 2023-11-20
type: docs
math: true
tags:
  - R
image:
  caption: 'Embed rich media such as videos and LaTeX math'
---
## Load data

If you were not attending the previous workshop 'Introduction to R', you'll need to load the data first:

```r
library(readr)
```

We'll use the package to load the data from here: https://github.com/allisonhorst/palmerpenguins/blob/main/inst/extdata/penguins.csv

Download it and put it into the same folder that you have currently open in RStudio.

Read it into R:

```r
penguins <- read_csv("penguins.csv")
View(penguins)
```

## Modifying data

Often, when you've loaded the data, you will not be immediately able to work with it. More often than not, it needs to be changed beforehand.

You can not simply write in a dataframe by clicking on it. This is because such changes would not be reproducible. You can modify a dataframe in any way you want, but in R, it has to be done using code.

This dataset contains no identifier yet. We'll add one:

```r
penguins$ID <- 1:nrow(penguins)
```

For example, assume that we knew the penguin in row 48 (which currently has a NA value) was female. The gender is in the seventh column. You indicate the position of a value in the dataframe by giving the row first and the column second.

```r
penguins[48, 7] <- 'female'
```

If we want our dataset to only include rows in which all values are known, we remove the NA's with this command:

```r
penguins <- na.omit(penguins)
```

Remember that in R, it are always entire rows which get removed. It is impossible to only remove individual values because a dataframe always needs to have one entry for each column in every row.

There are also other options such as replacing NA's with the mean value of their column, but these are not always scientifically sound. Which strategy for missing data is appropriate depends on the dataset and your research question.

Once we are confident the data is what it should be, we can start our regression.

## Running a regression

We'll use the fixest package to run regressions. Many packages in R can be used for this purpose, and base R allows to run regressions, too. But the fixest package is very fast - for large datasets, that's important.

```r
library(fixest)
```

A regression in R is a function. It has a name and function arguments. Since we work with linear regressions, we use the feols command (ols are ordinary least squares).

```r
print_3 <- function(x){
  print(3)
  print("I printed 3")
}
```

```r
print_3(4)
```

The first regression checks if penguins who weigh more have longer flippers:

```r
lm(penguins$flipper_length_mm ~ penguins$body_mass_g)
```

```r
feols(flipper_length_mm ~ body_mass_g, data = penguins)
```

They do, the estimate is positive and significant (with a very small p-value). The effect is not very large, though.

Regression results can be saved by assigning them to a name:

```r
flipper_regression <- feols(flipper_length_mm ~ body_mass_g, data = penguins)
```

This ensures that you can always access them. You can call the table of results we saw above with the summary function:

```r
summary(flipper_regression)
```

It is good practice to calculate heteroskedasticity-robust standard errors. This is done with the vcov-argument. You can do it in the regression function or in the summary function.

```r
summary(flipper_regression, vcov = 'hetero')
```

As you can see, the estimate remains the same. But the standard error and the metrics calculated based on it (the t-value) change.

In the next step, write your own regression. Is the bill length positively related to the bill depth? Calculate heteroskedasticity-robust standard errors for this.

```r
bill_regression <- feols(bill_length_mm ~ bill_depth_mm + bill_depth_mm^2, data = penguins, vcov = 'hetero')
```

```r
summary(bill_regression, vcov = 'hetero')
```

Surprisingly, the relation is significantly negative. Penguins, it appears, either have a short, but thick bill or a long, but thin one.

## A technical remark on lists

The regression is stored in R as a list. A list is a special form of datatype.

```r

```

Important to know: If you extract an element from a list with single brackets, even if it only contains a single value, it will be a smaller list. For example, the first element of a regression is the number of observations.

```r
bill_regression[1]
class(bill_regression[1])
```

To get the element itself, use double brackets:

```r
bill_regression[["nobs"]]
class(bill_regression[["nobs"]])
```

## Fixed effects.

In many datasets, such regressions as the ones above are not a good approach. That is because they are only valid when the data is a random sample of the underlying population. But what is the population we are considering here?

The penguin data contains three species, covers three islands and was collected over 3 years. The bills may not be formed the same for all three species. To test this, we use a fixed effect.

```r
species_regression <- feols(bill_length_mm ~ bill_depth_mm |  species, data = penguins)
```

Again, we need to think carefully about standard errors. If the data is clustered according to specific variables (here: species), the standard errors have to be clustered as well.

```r
summary(species_regression, vcov = 'hetero')
summary(species_regression, vcov = 'cluster')
```

How does it look like when we also want to control for the sex of the penguins?

Clustering by sex:

```r
species_regression <- feols(bill_length_mm ~ bill_depth_mm |  species + sex, data = penguins ) 
summary(species_regression, vcov = 'hetero')
summary(species_regression, vcov = 'cluster')
```

## Running a regression with factor variables

This form of clustering is to control for a certain variable, like species. If, instead, we wanted to know the effect such a variable has, we need to run the regression with a factor variable.

Transforming a variable into a factor is straightforward:

```r
penguins$sex = as.factor(penguins$sex)
```

```r
penguinsex <- penguins$sex
```

```r
penguins$ID = as.factor(penguins$ID)
penguinID <- penguins$ID
```

Note that the type changed: Previously, the type of genres was 'chr'. Now, it is 'Factor w/ 2 levels' "female", "male".

Levels are the different values a factor variable can take.

```r
weight_regression <- feols(body_mass_g ~ sex, data = penguins)
summary(weight_regression, vcov = 'hetero')
```

The variable 'sexmale' is created automatically when the regression has a factor or character vector as input. The system creates a dummy, a variable 'sexmale' that can take 0 or 1, depending on if the penguin is male or not. It then estimates the effect of this dummy, compared to a baseline (here, female penguins are the baseline).

Again, we want to control for species effects:

```r
weight_regression <- feols(body_mass_g ~ sex  | species + island + species^island, data = penguins)
summary(weight_regression, vcov = 'cluster')
summary(weight_regression, vcov = 'twoway')
```

We see that although the effect is still significant at the 5%-level, the species fixed effects have a certain influence as well.

In the next step, create your own factor variable. Answer the following question: Does the body weight of the penguins change with the years?

```r
penguins$year = as.factor(penguins$year)
```

Do penguins weigh more or less depending on the year?

```r
year_regression <- feols(body_mass_g ~ year, data = penguins)
summary(year_regression, vcov = 'hetero')
```

No. The estimates are positive, but not significant (the p-value is very large). That is also important to know: If they had lost weight in 2009 or 2008, it may mean that the colonies were endangered.

