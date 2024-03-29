---
date: "2019-05-05T00:00:00+01:00"
draft: false
linktitle: Site-Specific Analysis
menu:
  gestationalage:
    name: Step 2. Site-Specific Analysis
    weight: 1
title: Site-Specific Analysis
toc: true
type: docs
weight: 1
---

## Analysis Plan

If you do not have a copy of the full analysis plan, please email Sofía Aguilar Lacasaña (sofia.aguilar@isglobal.org) for a copy. The full analysis plan includes additional information regarding study background, downstream analyses, and study timeline.

### Objective

The overall aim is to investigate the association between epigenome-wide placental DNA methylation and gestational age and preterm.

### Cohorts

All PACE-placenta and PLACENTOMICS cohorts will be invited to participate.

### Cohorts description

Before starting analysing their data, we will ask cohorts to fill out a [brief online form](https://forms.gle/uwDK2Ag75dwiAotQ9) that will inform our guidance on potential cohort-specific modifications to the analysis plan. This form will solicit the following information:
+	Anticipated number of eligible samples
+	Whether these samples are from the maternal-or fetal-side of the placenta
+	Number of participants in each self-reported race/ethnicity category
+	Do you have genetic data to estimate genetic ancestry?
+	Number of male and female infants
+	Number of preterm infants

If both fetal-side and maternal-side samples are available from the cohort, we will ask to fill out the online form for both sides separately. Link to the online form: https://forms.gle/uwDK2Ag75dwiAotQ9

Together with the summary statistic files, all participating cohorts will be asked to provide a short description of: 
+	Their study population, and any important study-specific selection criteria
+	Confirmation of informed consent and appropriate ethics approvals  
+	Birth outcomes and covariate definitions
+	Placenta collection and DNA extraction procedure
+	DNA methylation assay processing in laboratory 

We provide an example description for reference (PACEPla_GA_Cohort_Methods_Date.doc). 

### Exclusion criteria

### Main Analysis 

Analysis will be restricted to singleton births. If there are siblings in the cohort (non-twins), randomly select one of the siblings for study inclusion. If the DNA methylation data stems from a case-cohort study, please restrict the analysis to the sub-cohort. If the DNA methylation data stems from a case-control study, please let us know, and we can discuss the best exclusion criteria to use given the study design that generated the data.After you estimate cell composition (from function preprocessingofData), we recommend removing samples with estimated syncytiothrophoblast proportions of less than 0.25. We suggest study investigators check into what may be contributing to unusual cell composition estimates by checking the distribution of DNA methylation and clinical characteristics of these samples. 

#### Sensitivity Analysis 

As an extra sensitivity analysis, we ask you to run the gestational age model again among the subset of **term births (>=37 weeks gestation)** from mothers without pregnancy complications or physician initiated delivery that might affect gestational age.  Here a suggested list of variables to consider for exclusion:
+	Pre-existing medical conditions in the mother: 
    - diabetes (type 1, type 2)
    - chronic hypertension
    - autoimmunediseases (including SLE, RA, and sclerodermia)
    - immuno-compromised patients
+	Pregnancy complications:
    - gestational diabetes
    - placental abruption 
    - placenta previa
    - pre-eclampsia/eclampsia and gestational hypertension
    - polyhydramnios, oligohydramnios
    - placental insufficiency
    - cervical insufficiency, isoimmunization, 
    - cervical cerclaje
+ Caesarian section, if due to pregnancy complications. C-sections due to complications during labor are kept in. 
+ Restrict to only term births (>=37 weeks gestation)

### Gestational age outcomes

We will analyse gestational age outcomes, preferably based on ultrasound estimation, otherwise based on last menstrual period (LMP). Please indicate how gestational age (LMP or ultrasound, or other) was assessed in the PACEPla_GA_Cohort_Methods_Date. Verify that the units are days and check extreme values for possible data errors. 
The outcomes of interest are:
1)	**Gestational age (GA)**: Gestational age at birth (days), without any transformation
2)	**Preterm delivery (PTD)**: 
    -	1: <37 full weeks or < 259 days 
    -	0: >= 37 weeks or >= 259 days 

### Methylation data

Placental DNA methylation from fetal-side or maternal-side assessed with the Ilumina Infinium450k BeadChip or the Ilumina Infinium EPIC BeadChip. We will use beta values, from 0 to 1.  We are providing an R package (**PACEanalysis**) that enables each cohort to run all required pre-processing and EWAS in a few lines of code. You will have the option to adjust for batch effects with the ComBat method in the pre-processing if you have an indicator for batch in your dataset. Please see the **PACEanalysis** help manual for details about each function, and the package vignette for example code.  

### Covariates

If any of the categorical variables below have less than 10 individuals in one of the categories, please let us know and we can decide whether to combine categories or restrict the analysis. In the case of ancestry, if there are more than two ancestry categories in your cohort with less than 10 individuals, we will discuss combining these categories into the “other” classification. If you do not have one of these variables, let us know, and we can discuss the best approach to take with your data. More generally, if you want to use different categorization of the below covariates, please contact Sofía Aguilar Lacasaña (sofia.aguilar@isglobal.org) to discuss.

+	**Infant sex**: categorical: "Female", "Male". 
+	**Maternal age**: continuous (years)
+	**Maternal pre-pregnancy BMI**: continuous (kg/m2)
+	**Parity**: preferred categorization is into 2 groups: 0 and >= 1
+	**Maternal education**: preferred categorization is into 3 groups: 1=primary, 2=secondary, 3=university. It is used as an indicator of socioeconomic status. 
+	**Maternal smoking during pregnancy**: preferred classification is into 3 groups: 1= No smoking in pregnancy, 2=Smoking, but stopped in early pregnancy (before first trimester), 3 = Smoking throughout pregnancy. If you want to use a different categorization, please contact the meta-analysis center to discuss.
+	**Labor induced**: categorical into 2 groups: 1=No, 2=Yes.
+	**Caesarian section**: categorical into 2 groups: 1=No, 2=Yes.
+	**Ancestry (optional)**: We will first run models adjusting for self-reported ancestry. We will then run models stratified by major ethnic groups (ie. European, African, Asian…). Within each major ethnic group, additional adjustment for ethnic background is optional (ie. Country, GWAS PCs, etc). 
+	**Selection factors (optional)**: If your study design includes an enrichment of cases of some condition, please include the case-control variable in the models, or discuss with us.
+	**Meanlog2oddsContamination**: Adjusting for the contamination score, which is output by the the `ExploratoryDataAnalysis` function. See below how to add this variable to your dataset

## Data Checking

As noted in our analysis plan, if any of the categorical variables below have less than 10 individuals in one of the categories, please let us know and we can decide whether to combine categories or restrict the analysis. In the case of ancestry, if there are more than two ancestry categories in your cohort with less than 10 individuals, we will discuss combining these categories into one “other” classification.  If you don’t have one of these variables, let us know, and we can discuss the best approach to take with your data. 

Make sure that all categorical adjustment variables are coded as a factor; if they are coded numerically, the function will assume to code them as continuous adjustment variables. 

If you encounter any issues, please check out our troubleshooting guide to see if there is any guidance that may help: https://www.epicenteredresearch.com/pace/troubleshooting/

If you closed prior R session, you can load list of the phenotype information by loading the RData file automatically saved by the `preprocessingofData` function. You can load the preprocessed beta-values with poor quality probes masked and outliers winsorized that is automatically saved by `outlierprocess`.

The variable `Gestage` is automatically added to the dataset if the `GESTvar` argument in `loadingSamples` is not NULL; it is assumed to be in weeks. 


```{r eval=FALSE}

setwd("H:\\UCLA\\PACE\\Gestage-placenta\\HEBC_20220709_Output")
load("HEBC_20220709_Preprocessed.RData")

## load beta-values
load("HEBC_20220709_PreprocessedBetas_nooutliers.RData")
Betasnooutliers<-betafinal.nooutlier ## name of the object saved in the RData file

phenodataframe<-as.data.frame(pData(processedOut$mset))
phenodataframe$PTB<-ifelse(phenodataframe$Gestage*7<259,1,0)

```
We also want to adjust for estimated contamination. To do this, we want to add in our contamination score to our phenotype dataframe; this information is in the Recommended_Samples_to_Remove csv output by the `EDAresults` function.

```{r eval=FALSE}

setwd("H:\\UCLA\\PACE\\Gestage-placenta\\HEBC_20220709_Output\\EDA")
samplestoexclude<-read.csv("HEBC_20220709_Recommended_Samples_to_Remove.csv",header=TRUE)
samplestoexclude<-samplestoexclude[,c("Basename","Meanlog2oddsContamination")]
samplestoexclude<-unique(samplestoexclude)

phenodataframe<-merge(phenodataframe,samplestoexclude,by="Basename")
summary(phenodataframe$Meanlog2oddsContamination)

```


Make sure that you have sufficient numbers of samples between categories of exposure/outcome of interest as well as adjustment variables; you are assumed to have at least 10. Please note, these variable names may be different in your cohort

```{r eval=FALSE}

table(phenodataframe$PTB)
table(phenodataframe$Sex)
table(phenodataframe$Parity)
table(phenodataframe$MaternalEd)
table(phenodataframe$Smoke)
table(phenodataframe$ModeDelivery)
table(phenodataframe$Ethnic)

```

Make sure all categorical adjustment variables are coded as factors or characters; 'Sex' is already coded as a character

```{r}

phenodataframe$Parity<-as.factor(phenodataframe$Parity)
phenodataframe$MaternalEd<-as.factor(phenodataframe$MaternalEd)
phenodataframe$Smoke<-as.factor(phenodataframe$Smoke)
phenodataframe$Ethnic<-as.factor(phenodataframe$Ethnic)
phenodataframe$ModeDelivery<-as.factor(phenodataframe$ModeDelivery)


```

### Now you can move on to Step 3

Choose the appropriate Step 3, depending on whether or not you have multiple race/ethnicities in your cohort

