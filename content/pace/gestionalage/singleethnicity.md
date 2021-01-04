---
date: "2021-01-03T00:00:00+01:00"
draft: false
linktitle: Step 3. If single race/ethnicity in cohort
menu:
  gestationalage:
    parent: Step 2. Site-Specific Analysis
    weight: 3
title: If single race/ethnicity in cohort
toc: true
type: docs
weight: 3
---

If you only have individuals of European descent in your cohort, run this instead:

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
  
}

```

