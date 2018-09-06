<!-- rmarkdown v1 -->
---
output: github_document
---
 
---
 
 
---
 
[![Project Status: WIP – Initial development is in progress, but there has not yet been a stable, usable release suitable for the public.](https://www.repostatus.org/badges/latest/wip.svg)](https://www.repostatus.org/#wip)
[![Licence](https://img.shields.io/github/license/mashape/apistatus.svg)](http://choosealicense.com/licenses/mit/)
[![Build Status](https://travis-ci.com/Tirgit/missCompare.svg?branch=master)](https://travis-ci.com/Tirgit/missCompare)
 
---
 
[![minimal R version](https://img.shields.io/badge/R%3E%3D-3.5.0-6666ff.svg)](https://cran.r-project.org/)
[![CRAN_Status_Badge](http://www.r-pkg.org/badges/version/missCompare)](https://cran.r-project.org/package=missCompare)
[![packageversion](https://img.shields.io/badge/Package%20version-0.1.0-orange.svg?style=flat-square)](commits/master)
 
---
 
[![Last-changedate](https://img.shields.io/badge/last%20change-2018--09--06-yellowgreen.svg)](/commits/master)

<!-- README.md is generated from README.Rmd. Please edit that file -->



# Overview of missCompare

**missCompare** is a missing data imputation pipeline that will guide you through your missing data problem. A range of functions will help you select what could be the most ideal algorithm for your data and provide an easy way to impute missing datapoints in your dataset.    

## The missCompare pipeline

You will find a detailed manual in the missCompare vignette:

```r
install.packages("missCompare")
library(missCompare)
vignette("missCompare")
```

1. **Cleaning your data** using `missCompare::clean()`
2. **Extracting information** on dimensions, missingness, correlations and variables, plotting
missing data using `missCompare::get_data()`
3. **Imputation - simulated data**:
  + simulating full data with no missingness using metadata from the previous step (resembling your original data) using `missCompare::simulated()`
  + spiking in missing data in distinct missing data patterns using `missCompare::all_patterns()`. These patterns are:
    + missing completely at random (MCAR) - `missCompare::MCAR()` - missing data occurence random
    + missing at random (MAR) - `missCompare::MAR()` - missing data occurence correlates with other variables' values (univariate solution in missCompare)
    + missing not at random (MNAR) - `missCompare::MNAR()` - missing data occurence correlates with variables' own values
    + missing in assumed pattern (MAP) - `missCompare::MAP()` - a combination of the previous three, where the user can define a pattern per variable
  + imputing missing data, obtaining imputation accuracy (root mean squared errors - RMSE) per method and plotting results using `missCompare::impute_simulated()`
4. **Imputation - validation** - After the previous step, you will have a general idea on what are the best performing algorithms for your data structure (size, degree of correlation between variables). With `missCompare::impute_data_validate()` you can use your original data and spike in a tiny fraction of missing data, impute and check imputation accuracy on that tiny fraction and validate that the best performing algorithms from the previous step are indeed good to work with.
5. **Imputating your data** - Here you can impute your original data with your chosen algorithm(s) using `missCompare::impute_data()`.
6. **Post imputation diagnostics** will give an informative assessment on how the imputation changed your data structure (e.g. variable means, distributions, correlations). The function here is `missCompare::post_imp_diag()`.

## Installation

You can install the released version of missCompare from [CRAN](https://CRAN.R-project.org) with:

``` r
install.packages("missCompare")
```

## Usage

#### Loading library and data

```r
library(missCompare)
data("clindata_miss")
```

#### Cleaning

```r
cleaned <- missCompare::clean(clindata_miss,
                              var_removal_threshold = 0.5, 
                              ind_removal_threshold = 1,
                              missingness_coding = -9)
```

#### Extracting data

```r
metadata <- missCompare::get_data(cleaned,
                                  matrixplot_sort = T,
                                  plot_transform = T)
```

#### Imputation - simulation framework

```r
missCompare::impute_simulated(rownum = metadata$Rows,
                              colnum = metadata$Columns, 
                              cormat = metadata$Corr_matrix,
                              MD_pattern = metadata$MD_Pattern,
                              NA_fraction = metadata$Fraction_missingness,
                              min_PDM = 10,
                              n.iter = 50, 
                              assumed_pattern = NA)
```

#### Imputation - validation

```r
missCompare::impute_data_validate(cleaned,
                                  scale = T,
                                  spike.in = 0.01,
                                  n.iter = 50,
                                  sel_method = c(1:16))
```
                                  
#### Imputation of data

```r
imputed <- missCompare::impute_data(cleaned, 
                         scale = T, 
                         n.iter = 10, 
                         sel_method = c(1:16))
```
                         
#### Post imputation diagnostics

```r
diag <- missCompare::post_imp_diag(cleaned,
                                   imputed$mean_imputation[[1]],
                                   scale=T, 
                                   n.boot = 100)
```

## Issues, questions

In case you need help or advice on your missing data problem or you need help with the missCompare package, please e-mail the authors. If you would like to report an issue, please do so in a reproducible example at the [missCompare GitHub](https://github.com/Tirgit/missCompare/issues) page.

