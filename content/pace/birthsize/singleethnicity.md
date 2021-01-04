---
date: "2021-01-03T00:00:00+01:00"
draft: false
linktitle: Step 3. If single race/ethnicity in cohort
menu:
  birthsize:
    parent: Step 2. Site-Specific Analysis
    weight: 3
title: If single race/ethnicity in cohort
toc: true
type: docs
weight: 3
---

If you only have individuals of European descent in your cohort, run this instead:

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
  
}

```

