---
date: "2019-05-05T00:00:00+01:00"
draft: false
linktitle: Tips 1-2
menu:
  birthsize:
    name: Step 2. Site-Specific Analysis
    weight: 1
title: Site-Specific Analysis
toc: true
type: docs
weight: 1
---

## Analysis Plan

If you do not have a copy of the full analysis plan, please email Alexandra Binder (ambinder@hawaii.edu) for a copy. The full analysis plan includes additional information regarding study background, downstream analyses, and study timeline.

### Objective

The overall aim is to investigate the association between birth size metrics and epigenome-wide placental DNA methylation.

### Cohorts

All PACE-placenta and PLACENTOMICS cohorts will be invited to participate 

### Cohort description

Before starting analyzing their data, we will ask for cohorts to fill out a [brief online form](https://forms.gle/HsgQbS6aRac1N1fUA) that will inform our guidance on potential cohort-specific modifications to the analysis plan. This form will solicit the following information:

+ Anticipated number of eligible samples
+ Whether these samples are from the maternal- or fetal-side of the placenta
+ Number of participants in each self-reported race/ethnicity category
+ Do you have genetic data to estimate genetic ancestry?
+ Number of male and female infants
+	Number of low birth weight infants
+	Number of high birth weight infants

If both fetal-side and maternal-side samples are available from the cohort, we will ask you to fill out the online form for both sides separately.
Link to the online form: https://forms.gle/HsgQbS6aRac1N1fUA

Together with the summary statistic files, all participating cohorts will be asked to provide a short description of:

+ Their study population, and any important study-specific selection criteria
+	Confirmation of informed consent and appropriate ethics approvals 
+	Birth outcomes and covariate definitions
+	Placenta collection and DNA extraction procedure
+	DNA methylation assay processing in the laboratory

We provide an example description for reference (PACEPla_BW_Cohort_Methods_Date.doc).  

### Exclusion criteria

Analysis will be restricted to singleton births. If the DNA methylation data stems from a case-cohort study, please restrict the analysis to the sub-cohort. If the DNA methylation data stems from a case-control study, please let us know, and we can discuss the best exclusion criteria to use given the study design that generated the data.
We will not exclude participants based on the incidence of maternal conditions, such as gestational diabetes and preeclampsia, or preterm birth. If placenta DNA methylation and birth size influence the risk of any of these outcomes, adjusting for these factors may introduce collider bias (selection bias), and make it appear there is a relationship between DNA methylation and birth size that is not a true direct effect. 

### Outcome

The outcomes of interest are:
1.	**Birthweight (BW)**: 
    +	Continuous (grams)
    +	Categorical:
        -	Low birth weight (LBW): Binary, LBW (<2500g) versus normal BW (2500-4000g
        -	High birth weight (HBW): Binary, HBW (>4000g) versus normal BW (2500-4000g)
2.	**Birthlength (BL)**: Continuous (cm)
3.	**Birthweight-for-length (BWL)** - an indicator of adiposity under 2 years:  Continuous (kg/cm)
4.	**Head circumference (HC)**: Continuous (cm)

### Methylation data

Placental DNA methylation from the fetal-side or maternal-side assessed with the Illumina Infinium450k BeadChip or the Illumina Infinium EPIC BeadChip. We will use beta values, from 0 to 1.  We are providing a package (**PACEanalysis**) that enables each cohort to run all required pre-processing and EWAS in a few lines of code. You will have the option to adjust for batch effects with the ComBat method in the pre-processing if you have an indicator for batch in your dataset. Please see the **PACEanalysis** help manual for details about each function, and the package vignette for example code. 

### Covariates

If any of the categorical variables below have less than 10 individuals in one of the categories, please let us know and we can decide whether to combine categories or restrict the analysis. In the case of ancestry, if there are more than two ancestry categories in your cohort with less than 10 individuals, we will discuss combining these categories into one “other” classification.  If you don’t have one of these variables, let us know, and we can discuss the best approach to take with your data. More generally, if you want to use different categorization of the below covariates, please contact Alexandra Binder (ambinder@hawaii.edu) to discuss. 

+	**Gestational age**: Continuous (days)
+	**Infant sex**: Categorical: "Female", "Male". 
+	**Maternal age**: Continuous (years)
+	**Maternal pre-pregnancy BMI**: Continuous (kg/m2)
+	**Parity**: preferred categorization is into 2 groups: 0 and >=1. 
+	**Maternal education**: Preferred categorization is into 3 groups: primary, secondary and university. It is used as an indicator of socioeconomic status.
+	**Maternal smoking status**: Preferred classification is into three groups: 1. No smoking in pregnancy, 2. Smoking, but stopped in early pregnancy (first trimester), 3. Smoking throughout pregnancy. If you want to use a different categorization, please contact the meta-analysis center to discuss.
+	**Ancestry (optional)**: We will first run models adjusting for self-reported ancestry. We will then run models stratified by major ethnic groups (ie. European, African, Asian…). Within each major ethic group, additional adjustment for ethnic background is optional (ie. country, GWAS PCs, etc).
+	**Selection factors (optional)**: If your study design includes an enrichment of cases of some condition, please include the case-control variable, or discuss with us. 

## Data Checking

As noted in our analysis plan, if any of the categorical variables below have less than 10 individuals in one of the categories, please let us know and we can decide whether to combine categories or restrict the analysis. In the case of ancestry, if there are more than two ancestry categories in your cohort with less than 10 individuals, we will discuss combining these categories into one “other” classification.  If you don’t have one of these variables, let us know, and we can discuss the best approach to take with your data. 

Make sure that all categorical adjustment variables are coded as a factor; if they are coded numerically, the function will assume to code them as continuous adjustment variables. 

If you encounter any issues, please check out our troubleshooting guide to see if there is any guidance that may help: https://www.epicenteredresearch.com/pace/troubleshooting/


```{r eval=FALSE}

## If you closed prior R session, you can load list of pre-processed objects 
## that is automatically saved by the preprocessingofData function

setwd("H:\\UCLA\\PACE\\Birthweight-placenta\\HEBC_20201229_Output")
load("HEBC_20201229_Preprocessed.RData")

phenodataframe<-as.data.frame(pData(processedOut$mset))
phenodataframe$LBWbin<-ifelse(phenodataframe$BWT<2500,1,0)
phenodataframe$HBWbin<-ifelse(phenodataframe$BWT>4000,1,0)

## Make sure that you have sufficient numbers of samples between categories 
## of exposure/outcome of interest as well as adjustment variables; 
## you are assumed to have at least 10

## These variable names may be different in your cohort

table(phenodataframe$LBWbin)
table(phenodataframe$HBWbin)
table(phenodataframe$Sex)
table(phenodataframe$Parity)
table(phenodataframe$MaternalEd)
table(phenodataframe$Smoke)
table(phenodataframe$Ethnic)

## Make sure all categorical adjustment variables are coded as factors or characters
## 'Sex' is already coded as a character

phenodataframe$Parity<-as.factor(phenodataframe$Parity)
phenodataframe$MaternalEd<-as.factor(phenodataframe$MaternalEd)
phenodataframe$Smoke<-as.factor(phenodataframe$Smoke)
phenodataframe$Ethnic<-as.factor(phenodataframe$Ethnic)

```

### Now you can move on to Step 3

Choose the appropriate Step 3, depending on whether or not you have multiple race/ethnicities in your cohort
