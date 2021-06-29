---
date: "2019-05-05T00:00:00+01:00"
draft: false
linktitle: Tips 1-2
menu:
  birthsize:
    name: Step 2. Main Site-Specific Analyses
    weight: 1
title: Main Site-Specific Analyses
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

#### Main Analysis

To evaluate fetal growth not linked to gestational age or pregnancy complications, our main analyses will be restricted to children at term (between >=37 and <43 weeks of gestation) & without pregnancy complications. Here is a suggested list of variables to consider for exclusion:

+	Pregnancy complications:
    - gestational diabetes
    - placental abruption 
    - placenta previa
    - pre-eclampsia/eclampsia and gestational hypertension
    - polyhydramnios, oligohydramnios
    - placental insufficiency
    - cervical insufficiency, isoimmunization, 
    - cervical cerclaje

#### Secondary Analysis

Our secondary analyses will include all children at term (between >=37 and <43 weeks of gestation) and adjust for pregnancy complications. These secondary analyses will be restricted to cohorts with at least 10 mothers with pregnancy complications in their dataset. 

### Outcome

The outcomes of interest are:

1.	**Birthweight (BWT) Z-score**: Continuous 
2.	**Birthlength (BL) Z-score**: Continuous (cm)
3.	**Birthweight-for-length (BWL) Z-score** - an indicator of adiposity under 2 years:  Continuous (kg/cm)
4.	**Head circumference (HC) Z-score**: Continuous (cm)

The Z-score for each of these characteristics is automatically calculated in the `loadingSamples` function, and are based on the INTERGROWTH-21(st) Project ([link](https://pubmed.ncbi.nlm.nih.gov/25209487/)).

### Methylation data

Placental DNA methylation from the fetal-side or maternal-side assessed with the Illumina Infinium450k BeadChip or the Illumina Infinium EPIC BeadChip (if you have both fetal-side and maternal-side samples, we will ask you to run these analyses separately). We will use beta values, from 0 to 1.  We are providing a package (**PACEanalysis**) that enables each cohort to run all required pre-processing and EWAS in a few lines of code. You will have the option to adjust for batch effects with the ComBat method in the pre-processing if you have an indicator for batch in your dataset. Please see the **PACEanalysis** help manual for details about each function, and the package vignette for example code. 

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
+	**Meanlog2oddsContamination**: Adjusting for the contamination score, which is output by the the `ExploratoryDataAnalysis` function. See below how to add this variable to your dataset

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
Betasnooutliers<-betafinal.nooutlier ## name of the object saved in the RData file

phenodataframe<-as.data.frame(pData(processedOut$mset))

```
We also want to adjust for estimated contamination. To do this, we want to add in our contamination score to our phenotype dataframe; this information is in the Recommended_Samples_to_Remove csv output by the `ExploratoryDataAnalysis` function.

```{r eval=FALSE}

setwd("H:\\UCLA\\PACE\\Birthweight-placenta\\HEBC_20210618_Output\\EDA")
samplestoexclude<-read.csv("HEBC_20210618_Recommended_Samples_to_Remove.csv",header=TRUE)
samplestoexclude<-samplestoexclude[,c("Basename","Meanlog2oddsContamination")]
samplestoexclude<-unique(samplestoexclude)

phenodataframe<-merge(phenodataframe,samplestoexclude,by="Basename")
summary(phenodataframe$Meanlog2oddsContamination)

```

Remove samples that do not meet inclusion requirements. The main analysis restricts to children at term (between >=37 and <43 weeks of gestation) & without pregnancy complications. The variable `Gestage` is automatically added to the dataset if the `GESTvar` argument in `loadingSamples` is not NULL; it is assumed to be in weeks. If you subset the phenotype information data frame, the `dataAnalysis` function will automatically subset the beta-value function to the same samples. 

```{r eval=FALSE}

## Restricting to term
phenodataframe<-phenodataframe[which(phenodataframe$Gestage>=37 & phenodataframe$Gestage < 43),]

## Removing pregnancy complications (this will depend on your dataset)
phenodataframe<-phenodataframe[which(phenodataframe$GDM=="No"),]
phenodataframe<-phenodataframe[which(phenodataframe$PE=="No"),]

```

Make sure that you have sufficient numbers of samples between categories of exposure/outcome of interest as well as adjustment variables; you are assumed to have at least 10. Please note, these variable names may be different in your cohort

```{r eval=FALSE}
table(phenodataframe$Sex)
table(phenodataframe$Parity)
table(phenodataframe$MaternalEd)
table(phenodataframe$Smoke)
table(phenodataframe$Ethnic)
```

Double check the distribution of Z-scores - if they seem off, make sure your input in loadingSamples is correct (e.g. BWT is in grams). These Z-scores are automatically added to your dataset in the `loadingSamples` function

```{r eval=FALSE}

## Automatically added to dataset by loadingSamples if BWTvar and GESTvar are not NULL 
summary(phenodataframe$BWT_Zscore)

## Automatically added to dataset by loadingSamples if BIRTHLENGTHvar and GESTvar are not NULL
summary(phenodataframe$BirthLength_Zscore)

## Automatically added to dataset by loadingSamples if BWTvar, BIRTHLENGTHvar, and GESTvar are not NULL
summary(phenodataframe$wlr_Zscore)

## Automatically added to dataset by loadingSamples if HEADCIRCUMvar and GESTvar are not NULL
summary(phenodataframe$HeadCircum_Zscore)

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
