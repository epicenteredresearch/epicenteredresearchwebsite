---
date: "2021-01-03T00:00:00+01:00"
draft: false
linktitle: Step 3. If multiple race/ethnicities in cohort
menu:
  birthsize:
    parent: Step 2. Site-Specific Analysis
    weight: 2
title: If multiple race/ethnicities in cohort
toc: true
type: docs
weight: 2
---

<style>
.main-container {
    width: 100%;
    max-width: unset;
}
</style>

If you have multiple race/ethnicities in your cohort, run the below code. Be sure to update IndicatorforEthnicity to whatever is appropriate for your cohort. For example, if one of your race/ethnicity categories is "White" for individuals of European descent, use that instead of "1". 

```{r eval=FALSE}
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
                  adjustmentvariables=modelstorun$adjustmentvariables[i],
                  RunUnadjusted=TRUE,
                  RunAdjusted=TRUE,
                  RunCellTypeAdjusted=TRUE,
                  RunSexSpecific=TRUE,
                  RestricttoEthnicity=FALSE,
                  IndicatorforEthnicity=NULL,
                  destinationfolder="H:\\UCLA\\PACE\\Birthweight-placenta",
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
                  adjustmentvariables=modelstorun$adjustmentvariables[i],
                  RunUnadjusted=TRUE,
                  RunAdjusted=TRUE,
                  RunCellTypeAdjusted=TRUE,
                  RunSexSpecific=TRUE,
                  RestricttoEthnicity=TRUE,
                  IndicatorforEthnicity="1",
                  destinationfolder="H:\\UCLA\\PACE\\Birthweight-placenta",
                  savelog=TRUE,
                  cohort="HEBC",analysisdate="20210103")
  
}

```