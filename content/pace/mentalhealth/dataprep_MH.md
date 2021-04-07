---
date: "2019-05-05T00:00:00+01:00"
draft: false
linktitle: Tips 1-2
menu:
  mentalhealth:
    name: Step 2. Data Preparation
    weight: 1
title: Preparing for the Site-Specific Analysis
toc: true
type: docs
weight: 1
---

## Analysis Plan

If you do not have a copy of the full analysis plan, please email Sara Sammallahti (sara.sammallahti@helsinki.fi) for a copy. The full analysis plan includes additional information regarding study background, exposures and covariates, file sharing, study timeline etc.

### Objective

The aim of the current study is to establish whether maternal  symptoms of anxiety or depression, measured during pregnancy, are associated with differences in the DNAm in placental samples taken at childbirth. We hypothesise that mothers with high rates of anxiety, depression, or both may have both hypo- and hypermethylation of placental CpG sites, and take an epigenome-wide approach to explore where within the epigenome these alterations may be.

### Cohorts

All PACE-placenta and PLACENTOMICS cohorts with data on depression and/or anxiety during pregnancy will be invited to participate 

### Cohort description

We will ask to providea README file containing the following information:
 
#### Basic information
+ Full name of the cohort; abbreviated name of the cohort (if applicable)
+ Names and email addresses of the researchers (we recommend 1-4 co-authors per cohort)
+ Brief description of study population
+ Study design
+ Country and ethnicity of participants
+ Citation of a cohort profile or 1-3 other key papers describing the cohort
+ Consent and ethical approval for the study. e.g. "The Generation R Study is a population-based prospective cohort study.  All pregnant women living in Rotterdam, the Netherlands, with an expected delivery date between April 2002 and January 2006 were invited to participate. These women and their children have been followed at regular intervals since recruitment. [Kooijman 2016] Only participants who reported being of Dutch ethnicity were included. All participants gave written informed consent, and the study was approved by the Medical Ethical Committee of the Erasmus Medical Center, Rotterdam."

#### Maternal anxiety data
+ Name and citation of questionnaire
+ Citations relating to the psychometric properties of this instrument (e.g. 2-3 papers describing the validity and reliability of the instrument)
+ Number of items, scale, and protocol for calculating sum score 
+ Gestational weeks (range) when mothers filled out the questionnaire, e.g. "Anxiety during pregnancy was measured using the 20-item State anxiety subscale of the Spielberger State-Trait Anxiety Inventory (STAI). [Spielberger 1983]. This scale is widely used and has good psychometric properties [Barnes 2002], and it has been validated among pregnant women [Gunning 2008,, Meades 2011]. The items were rated on a 4-point Likert scale from 1 to 4. Mothers completed the questionnaire at 18-25 weeks of gestation. Sumscores were calculated as the sum of all items (reverse-coded if necessary so that higher scores reflect more symptoms)."

#### Maternal depression data
+ Name and citation of questionnaire
+ Citations relating to the psychometric properties of this instrument (e.g. 2-3 papers describing the validity and reliability of the instrument)
+ Number of items, scale, and protocol for calculating sum score 
+ Gestational weeks (range) when mothers filled out the questionnaire

#### DNAm data
+ How were the placental samples collected, processed, stored and analysed
+ What array was used to measure DNAm
+ Any quality control steps taken in this cohort specifically (note that the preprocessing phase is harmonised using the R package provided)

#### Covariates: 
+ How was maternal SES determined and categorised?
+ Maternal smoking: how determined and categorised?
+ Birth weight: how determined?
+ Gestational age: how determined?
+ What type of ancestry/race variable(s) were included in the model?
+ Any other covariates, or deviations from analysis plan in terms of batch correction, cell type correction, etc

#### Maternal psychiatric medications: 
+ Were medication data available? 
+ If yes, which medications were included? How were data on medications collected? How many mothers used psychiatric medications (and were thus excluded in models 4,8,12)?

#### Further information about the cohort (these can be finalised in the writing phase, but please provide a preliminary description already now if possible)
+ Funding statement on your cohort's behalf
+ Acknowledgements on your cohort's behalf
+ Any deviations from the analysis plan
+ Any other relevant information


### Exclusion criteria

+ Exclude multiple births. 
+ If you have a small number of non-twin siblings (for example, <10%) in the study, please exclude the sibling with missing data or one randomly selected sibling per sibling pair, so that there are no siblings. If you have a large number of siblings in the study, please contact us. 
+ If data are available, please exclude children with chromosomal abnormalities.
+ In sensitivity analyses we will further exclude mothers who used psychiatric medication (details in the Analysis plan) during pregnancy.

### Exposures

#### Exposure A: ANX_CONT (Anxiety, continuous score)
Use a self-report questionnaire score measuring symptoms of anxiety filled out by the mother during pregnancy. Calculate a sum score as recommended for your instrument. If you have measured anxiety during several time points, calculate the average score during pregnancy. Standardise the score within your analytical sample so that mean is 0 and standard deviation is 1. Winsorise outliers beyond 3 SD from the mean (i.e., if a mother's standardised anxiety score is less than -3.0, recode it to -3.0; if a mother's standardised anxiety score is higher than +3.0, recode it to +3.0). 

#### Exposure B: ANX_BIN (High-anxiety=1 vs low-anxiety=0, binary score)
Create a binary variable: Dichotomise the continuous anxiety score closest to the 15th percentile, so that approximately 15% of the highest-scoring (most anxious) mothers form the "high-anxiety" group, and approximately 85% of the lowest-scoring (least anxious) mothers form the reference group, the "low-anxiety group".

#### Exposure C: DEP_CONT (Depression, continuous score)
Use a self-report questionnaire score measuring symptoms of depression filled out by the mother during pregnancy. Calculate a sum score as recommended for your instrument.  If you have measured depression during several time points, calculate the average score during pregnancy. Standardise the score within your analytical sample so that mean is 0 and standard deviation is 1. Winsorise outliers beyond 3 SD from the mean (i.e., if a mother's standardised depression score is less than -3.0, recode it to -3.0; if a mother's standardised depression score is higher than +3.0, recode it to +3.0). 

#### Exposure D: DEP_BIN (High-depression=1 vs low-depression=0, binary score)
Create a binary variable: Dichotomise the continuous depression score closest to the 15th percentile, so that approximately 15% of the highest-scoring (most depressed) mothers form the "high-depression" group, and approximately 85% of the lowest-scoring (least depressed) mothers form the reference group, the "low-depression group". 

#### Exposure E: COM_CONT (Combined score, continuous)
Create a continuous "Combined" variable:
Calculate the sum of the standardised (mean=0, SD=1) anxiety score and the standardised (mean=0, SD=1) depression score. E.g., if a mother's anxiety score is +0.3, and the depression score is -0.1, her continuous combined score is 0.2. 
After calculating the sum score for each person, standardise this sum score so that mean=0 and SD=1 (this improves comparability, despite having only a minor effect on the distribution). 

#### Exposure F: COM_BIN (Combined, high anxiety/depression=1, low anxiety/depression=0, binary)
Create a combined binary variable: Dichotomise the continuous combined score closest to the 15th percentile, so that approximately 15% of the highest-scoring (most depressed and/or anxious) mothers form the "high-combined" group, and approximately 85% of the lowest-scoring (least depressed and/or anxiety) mothers form the reference group, the "low-combined group".

##### Checklist for exposure variables:
+ Measured using a self-report questionnaire during pregnancy
+ Continuous scores standardised so that mean=1 and SD=1
+ Binary scores created using 15 percentile cut-offs
+ Scores are not transformed
+ Higher scores indicates more severe symptoms
+ Participants should only be included if they have replied to majority of items (e.g. if you have a 20-item scale, only include mothers who have replied to at least 10 items)
+ If you have repeated exposures, take the mean of available scores
+ Outlier anxiety and depression scores beyond 3 SD from the mean are winsorised 
+ If you recruited specifically based on these symptoms or your sample is at higher risk for symptoms compared to a population-based sample, please contact us.
+ If you have measured anxiety or depression using several different alternative measures (e.g. mothers filled out both the CES-D scale and the BDI scale, which both measure depression), please contact us before choosing your scale!


#### Diagnostic data (diagnosis of anxiety disorder during pregnancy vs no; diagnosis of depressive disorder during pregnancy vs no) can also be incorporated into the meta-analysis:
+ ANX_BIN=1 if mother was diagnosed during pregnancy with anxiety disorder, and ANX_BIN=0 if mother was not diagnosed with an anxiety disorder during pregnancy
+ DEP_BIN=1 if mother was diagnosed during pregnancy with depressive disorder, and DEP_BIN=0 if mother was not diagnosed with a depressive disorder during pregnancy
+ COM_BIN=1 if mother was diagnosed during pregnancy with depressive or anxiety disorder or both, and COM_BIN=0 if mother was diagnosed with neither a depressive nor an anxiety disorder during pregnancy

If you have diagnostic data, you will likely need at least 10-15 cases (with a diagnosis) for the analyses to make sense. If the number of cases is very low, you might need to drop covariates to get the models to converge - let us know if you have any questions. 

If you have maternal mental health data measured during pregnancy, but you are not sure if/how those data can contribute to this project, please do not hesitate to contact us to discuss! 

### Methylation data

+ Use DNA methylation data of placental tissue samples taken after delivery 
+ Samples taken from the fetal side of the placenta (not the maternal side)
+ Samples taken at birth (if your placental samples were taken during pregnancy through chorionic villus sampling, please contact us)
+ DNAm measured using Illumina Infinium 450k BeadChip or EPIC 850k BeadChip

### Covariates

+ **SEX**: Sex of the child. If child is female, SEX=1 ("Female"). If child is male, SEX=2 ("Male"). 
+ **SES**: Maternal socioeconomic class. Factor variable, with preferably three groups (treated as 2 dummy variables). Use an education-based SES variable if possible. Categorise as appropriate in your cohort. We recommend distinguishing "high" (e.g., tertiary), "medium" (e.g., upper-secondary), and "low" (e.g., primary or lower secondary only) education: however you may need to refine the categorisation according to the overall educational level of your population.
For information on classifying education, please see https://ec.europa.eu/eurostat/statistics-explained/index.php/International_Standard_Classification_of_Education_(ISCED) ). If in doubt, or if you want to use a different SES measure, please contact us.
+ **AGE**: Maternal age at delivery. Continuous, in years.
+ **PARITY**: Primiparous (=0) vs multiparous (=1)
+ **SMOKE**: Maternal smoking during pregnancy. Factor variable, with three groups (treated as 2 dummy variables). If mother never smoked during pregnancy, SMOKE=0 ("never"). If mother quit smoking in early pregnancy, i.e. during the 1st trimester or when pregnancy was known, SMOKE=1 ("quit"). If mother continued smoking after early pregnancy, SMOKE=2 ("smoking continued"). If this categorisation is not possible, alternatively you can use binary coding: 0=did not smoke during pregnancy, vs 1=smoked during pregnancy. Please include a variabe describing the mother's own smoking status, not second-hand smoking: if in doubt, contact us.
+ **BWT**: Birth weight of the child in grams, continuous. Use the most accurate estimate (medical records is preferred to maternal report). 
+ **GESTA**: Gestational age at birth in weeks, continuous. Use the most accurate estimate. The priority is: medical record (ultrasound-confirmed) > medical record (last menstrual period) > self-reported (ultrasound) > self-reported (last menstrual period). 
PCA1, PCA2, PCA3... : Ancestry covariates. These are continuous GWAS-based principal components, which capture ethnicity/ancestry. If GWAS data are not available, use self-reported ethnicity: in this case, create a categorical factor variable called ETHNIC (which will be treated as a dummy-coded covariate by the R package). If you feel another way to address ethnicity/race/ancestry would be more appropriate for your cohort (e.g., stratification, methylation-based estimates...), please contact us first and let's discuss how to proceed.
+ **Selection factors (optional covariate)**: Please include if relevant for your study. For example, if your sample contains cases and controls for some condition, please include the case/control variable in all models. 

Batch effects are dealt with using Combat in the pre-processing phase: batch covariates are not needed in the EWAS phase models, as shown in the example script using the R package provided.

Estimated cell types are estimated in the pre-processing phase using the R package: they are included in the analysis, as shown in the example script using the R package provided.

## Data Checking

If you encounter any issues, please check out our troubleshooting guide to see if there is any guidance that may help: https://www.epicenteredresearch.com/pace/troubleshooting/

If you closed prior R session, you can load list of pre-processed objects that is automatically saved by the preprocessingofData function, e.g.


```r

load("C:/methylation_placenta/ITU_20210331_Output/ITU_20210331_Preprocessed.Rdata")

```

First goal in this step is to create a phenodataframe that contains all the necessary variables and only complete cases. You can get the phenodata from the preprocessed data object as follows:

```r

phenodata <-as.data.frame(pData(processedOut$mset))

```

#### If you already included all the necessary phenodata in step 1 it's only necessary to filter phenodata to complete cases.

```r
# You can see the amount of complete cases

sum(complete.cases(phenodata)==TRUE)   # does the amount look right? Remember that some samples dropped in the methylation QC.

# you can check that the amount does not change in subset of only necessary variables (no need if you know you don't have unnecessary variables with NAs)

pheno_subset <- 
   subset(phenodata, select=c(Basename,Sex,SES,AGE,PARITY,SMOKE,
   ANX_CONT,DEP_CONT,COM_CONT,ANX_BIN,DEP_BIN,COM_BIN,               # EDIT if you don't have all the exposures
   PCA1,PCA2,PCA3,BWT,GESTA,SELECTION,MEDICATION))                   # EDIT if you don't e.g have SELECTION or have different amount of PCAs 

sum(complete.cases(pheno_subset)==TRUE)												  
sum(complete.cases(phenodata$MEDICATION)==FALSE)  # This should be 0.

phenodataframe <- na.omit(phenodata)         
# or phenodataframe <- na.omit(pheno_subset)

# finally, check that you have done the possible exclusions - check Analysis plan or above
# (for example, if you have index variable in which INDEX=1 means that sample must be excluded
# phenodataframe <- phenodataframe[phenodataframe$INDEX !=1,] 
```

#### If you don't have all necessary variables in phenodata, you can just load another phenodata and merge them by ID-variable

```r

## In this case it's practical to check that same variables are not in both phenodatas (otherwise they will be both renamed) and
## probably it's also good to pre-filter pheno2 to complete cases and do possible exclusions of samples to pheno2.

phenodataframe <- merge (phenodata,pheno2,by="ID") 
sum(complete.cases(phenodataframe)==FALSE) # and if necessary
phenodataframe <- na.omit(phenodataframe).

# In any case, and especially if you merge datas, check that the phenodataframe has Basename as rownames: 
rownames(phenodataframe)<-as.character(phenodataframe$Basename)

```
#### Make sure all categorical adjustment variables are coded as factors or characters

```r
## 'Sex' is already coded as a character
phenodataframe$SES<-as.factor(phenodataframe$SES)
phenodataframe$PARITY<-as.factor(phenodataframe$PARITY)
phenodataframe$SMOKE<-as.factor(phenodataframe$SMOKE)
phenodataframe$ANX_BIN <-as.factor(phenodataframe$ANX_BIN)
phenodataframe$DEP_BIN<-as.factor(phenodataframe$DEP_BIN)
phenodataframe$COM_BIN<-as.factor(phenodataframe$COM_BIN)
phenodataframe$SELECTION<-as.factor(phenodataframe$SELECTION) # optional covariate
phenodataframe$MEDICATION<-as.factor(phenodataframe$MEDICATION) 


save(phenodataframe,file="C:/methylation_placenta/phenofinal_mentalhealth.Rdata")
```

### Now you can move on to Step 3

Choose the appropriate Step 3, depending on whether or not you have multiple race/ethnicities in your cohort
