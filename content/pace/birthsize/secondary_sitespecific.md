---
date: "2019-05-05T00:00:00+01:00"
draft: false
linktitle: Step 4. Secondary Site-Specific Analyses
menu:
  birthsize:
    name: Step 4. Secondary Site-Specific Analyses
    weight: 4
title: Secondary Site-Specific Analyses
toc: true
type: docs
weight: 4
---

## Analysis Plan

The analysis plan for this secondary analysis is the same as the main analyses with the exception of the exclusion criteria.  

### Exclusion criteria

Analysis will be restricted to singleton births. If the DNA methylation data stems from a case-cohort study, please restrict the analysis to the sub-cohort. If the DNA methylation data stems from a case-control study, please let us know, and we can discuss the best exclusion criteria to use given the study design that generated the data.

#### Main Analysis

To evaluate fetal growth not linked to gestational age or pregnancy complications, in our main analyses we subset to children born at term (between >=37 and <43 weeks of gestation) & without pregnancy complications.

#### Secondary Analysis

For this secondary analyses, we will include all children at term (between >=37 and <43 weeks of gestation) and adjust for pregnancy complications. If you do not have more than 10 infants with pregnancy complications, you do not need to run these analyses. Please contact us if you have any questions regarding this guidance. 

### Covariates

If any of the categorical variables below have less than 10 individuals in one of the categories, please let us know and we can decide whether to combine categories or restrict the analysis. In the case of ancestry, if there are more than two ancestry categories in your cohort with less than 10 individuals, we will discuss combining these categories into one “other” classification.  If you don’t have one of these variables, let us know, and we can discuss the best approach to take with your data. More generally, if you want to use different categorization of the below covariates, please contact Alexandra Binder (ambinder@hawaii.edu) to discuss. 

+	**Infant sex**: Categorical: "Female", "Male". 
+	**Maternal age**: Continuous (years)
+	**Maternal pre-pregnancy BMI**: Continuous (kg/m2)
+	**Parity**: preferred categorization is into 2 groups: 0 and >=1. 
+	**Maternal education**: Preferred categorization is into 3 groups: primary, secondary and university. It is used as an indicator of socioeconomic status.
+	**Maternal smoking status**: Preferred classification is into three groups: 1. No smoking in pregnancy, 2. Smoking, but stopped in early pregnancy (first trimester), 3. Smoking throughout pregnancy. If you want to use a different categorization, please contact the meta-analysis center to discuss.
+	**Ancestry (optional)**: We will first run models adjusting for self-reported ancestry. We will then run models stratified by major ethnic groups (ie. European, African, Asian…). Within each major ethic group, additional adjustment for ethnic background is optional (ie. country, GWAS PCs, etc).
+	**Selection factors (optional)**: If your study design includes an enrichment of cases of some condition, please include the case-control variable, or discuss with us. 
+ **Pregnancy complications**: Include a separate indicator variable for each pregnancy complication.

## Data Checking

As noted in our analysis plan, if any of the categorical variables below have less than 10 individuals in one of the categories, please let us know and we can decide whether to combine categories or restrict the analysis. In the case of ancestry, if there are more than two ancestry categories in your cohort with less than 10 individuals, we will discuss combining these categories into one “other” classification.  If you don’t have one of these variables, let us know, and we can discuss the best approach to take with your data. 

Make sure that all categorical adjustment variables are coded as a factor; if they are coded numerically, the function will assume to code them as continuous adjustment variables. 

If you encounter any issues, please check out our troubleshooting guide to see if there is any guidance that may help: https://www.epicenteredresearch.com/pace/troubleshooting/

If you closed prior R session, you can load list of the phenotype information by loading the RData file automatically saved by the `preprocessingofData` function. You can load the preprocessed beta-values with poor quality probes masked and outliers winsorized that is automatically saved by `outlierprocess`.


```{r eval=FALSE}

setwd("H:\\UCLA\\PACE\\Birthweight-placenta\\HEBC_20210618_Output")
load("HEBC_20210618_Preprocessed.RData")

## load beta-values
load("HEBC_20210618_PreprocessedBetas_nooutliers.RData")

phenodataframe<-as.data.frame(pData(processedOut$mset))

```

Make sure that you have sufficient numbers of samples between categories of exposure/outcome of interest as well as adjustment variables; you are assumed to have at least 10. Please note, these variable names may be different in your cohort. These numbers may be different from your main analyses.

```{r eval=FALSE}
table(phenodataframe$Sex)
table(phenodataframe$Parity)
table(phenodataframe$MaternalEd)
table(phenodataframe$Smoke)
table(phenodataframe$Ethnic)
```

Make sure all categorical adjustment variables are coded as factors or characters; 'Sex' is already coded as a character

```{r}

phenodataframe$Parity<-as.factor(phenodataframe$Parity)
phenodataframe$MaternalEd<-as.factor(phenodataframe$MaternalEd)
phenodataframe$Smoke<-as.factor(phenodataframe$Smoke)
phenodataframe$Ethnic<-as.factor(phenodataframe$Ethnic)

```

### Now you can move on to Step 3

Choose the appropriate Step 3, depending on whether or not you have multiple race/ethnicities in your cohort
