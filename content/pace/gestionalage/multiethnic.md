---
date: "2021-01-03T00:00:00+01:00"
draft: false
linktitle: Step 3. If multiple race/ethnicities in cohort
menu:
  gestationalage:
    parent: Step 2. Site-Specific Analysis
    weight: 2
title: If multiple race/ethnicities in cohort
toc: true
type: docs
weight: 2
---

If you have multiple race/ethnicities in your cohort, run the below code. Be sure to update IndicatorforEthnicity to whatever is appropriate for your cohort. For example, if one of your race/ethnicity categories is "White" for individuals of European descent, use that instead of "1". 

First, we will run the models adjusting for birth weight, and then we will run the models without adjusting for birth weight. Be sure to change the destination folder for your models not adjusting for birth weight so that you do not overwrite your initial results.


```{r eval=FALSE}

## First we run the models adjusting for birth weight 
## (we refer to adjustmentvariablesV1, which includes birth weight)

for (i in 1:nrow(modelstorun)){
  
  cat("Outcome:",modelstorun$varofinterest[i],"\n")
  tempresults<-dataAnalysis(phenofinal=phenodataframe,
                  betafinal=processedOut$processedBetas,
                  array="450K",
                  maxit=100,
                  Omega=processedOut$Omega,
                  vartype=modelstorun$vartype[i],
                  varofinterest=modelstorun$varofinterest[i],
                  Table1vars=modelstorun$table1vars[i],
                  StratifyTable1=FALSE,
                  StratifyTable1var=NULL,
                  adjustmentvariables=modelstorun$adjustmentvariablesV1[i],
                  RunUnadjusted=TRUE,
                  RunAdjusted=TRUE,
                  RunCellTypeAdjusted=TRUE,
                  RunSexSpecific=TRUE,
                  RestricttoEthnicity=FALSE,
                  IndicatorforEthnicity=NULL,
                  destinationfolder="H:\\UCLA\\PACE\\Gestationalage-placenta",
                  savelog=TRUE,
                  cohort="HEBC",analysisdate="20210103")
  
    tempresultsNonHispanicWhite<-dataAnalysis(phenofinal=phenodataframe,
                  betafinal=processedOut$processedBetas,
                  array="450K",
                  maxit=100,
                  Omega=processedOut$Omega,
                  vartype=modelstorun$vartype[i],
                  varofinterest=modelstorun$varofinterest[i],
                  Table1vars=modelstorun$table1vars[i],
                  StratifyTable1=FALSE,
                  StratifyTable1var=NULL,
                  adjustmentvariables=modelstorun$adjustmentvariablesV1[i],
                  RunUnadjusted=TRUE,
                  RunAdjusted=TRUE,
                  RunCellTypeAdjusted=TRUE,
                  RunSexSpecific=TRUE,
                  RestricttoEthnicity=TRUE,
                  IndicatorforEthnicity="1",
                  destinationfolder="H:\\UCLA\\PACE\\Gestationalage-placenta",
                  savelog=TRUE,
                  cohort="HEBC",analysisdate="20210103")
  
}

## Then we run the models without adjusting for birth weight
## (we now refer to adjustmentvariablesV2, which do not include birth weight)
## Be sure to change the destination folder

for (i in 1:nrow(modelstorun)){
  
  cat("Outcome:",modelstorun$varofinterest[i],"\n")
  tempresults<-dataAnalysis(phenofinal=phenodataframe,
                  betafinal=processedOut$processedBetas,
                  array="450K",
                  maxit=100,
                  Omega=processedOut$Omega,
                  vartype=modelstorun$vartype[i],
                  varofinterest=modelstorun$varofinterest[i],
                  Table1vars=modelstorun$table1vars[i],
                  StratifyTable1=FALSE,
                  StratifyTable1var=NULL,
                  adjustmentvariables=modelstorun$adjustmentvariablesV2[i],
                  RunUnadjusted=FALSE, ## don't have to run unadjusted analysis again
                  RunAdjusted=TRUE,
                  RunCellTypeAdjusted=TRUE,
                  RunSexSpecific=TRUE,
                  RestricttoEthnicity=FALSE,
                  IndicatorforEthnicity=NULL,
                  destinationfolder="H:\\UCLA\\PACE\\Gestationalage-noBWT-placenta",
                  savelog=TRUE,
                  cohort="HEBC",analysisdate="20210103")
  
    tempresultsNonHispanicWhite<-dataAnalysis(phenofinal=phenodataframe,
                  betafinal=processedOut$processedBetas,
                  array="450K",
                  maxit=100,
                  Omega=processedOut$Omega,
                  vartype=modelstorun$vartype[i],
                  varofinterest=modelstorun$varofinterest[i],
                  Table1vars=modelstorun$table1vars[i],
                  StratifyTable1=FALSE,
                  StratifyTable1var=NULL,
                  adjustmentvariables=modelstorun$adjustmentvariablesV2[i],
                  RunUnadjusted=FALSE, ## don't have to run unadjusted analysis again
                  RunAdjusted=TRUE,
                  RunCellTypeAdjusted=TRUE,
                  RunSexSpecific=TRUE,
                  RestricttoEthnicity=TRUE,
                  IndicatorforEthnicity="1",
                  destinationfolder="H:\\UCLA\\PACE\\Gestationalage-noBWT-placenta",
                  savelog=TRUE,
                  cohort="HEBC",analysisdate="20210103")
  
}

```