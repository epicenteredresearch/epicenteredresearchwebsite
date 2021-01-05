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

This stage of the analysis is specific to the chosen exposure/outcome and the specified adjustment variables. Below is the code for all of the analyses to run for the birth size project. Please be sure to update the cohort and date information in the below code for your analysis, as well as the destination path. Finally, be sure to update the column names of the exposure/outcome(s) of interest, the adjustment variables, and the table 1 variables. These should correspond to column names in the dataframe specified in the phenofinal argument of the dataAnalysis function. 

### Quick check to make sure the function runs in your cohort

Given the modeling approaches used, the dataAnalysis function requires a good deal of time to run. We recommend first checking whether the function runs on a relatively small subset of sites (i.e. 100 CpG loci). If you encounter any issues, please let us know. If not, proceed to the next step.

```{r eval=FALSE}

modelstorun<-data.frame(varofinterest=c("Gestage","PTB"),
                        vartype=c("OutcomeCont","OutcomeBin"))
modelstorun$varofinterest<-as.character(modelstorun$varofinterest)
modelstorun$vartype<-as.character(modelstorun$vartype)
modelstorun$table1vars<-c("BWT","Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","ModeDelivery")
                                   
## including birth weight from adjustment variables
modelstorun$adjustmentvariablesV1<-c("BWT","Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","ModeDelivery")
                                   
## excluding birth weight from adjustment variables
modelstorun$adjustmentvariablesV2<-c("Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","ModeDelivery")

## First we run the models adjusting for birth weight 
## (we refer to adjustmentvariablesV1, which includes birth weight)

for (i in 1:nrow(modelstorun)){
  
  cat("Outcome:",modelstorun$varofinterest[i],"\n")
  tempresults<-dataAnalysis(phenofinal=phenodataframe,
                  betafinal=processedOut$processedBetas[1:100,],
                  array="450K",
                  maxit=100,
                  Omega=processedOut$Omega,
                  vartype=modelstorun$vartype[i],
                  varofinterest=modelstorun$varofinterest[i],
                  Table1vars=c("BWT","Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","ModeDelivery"),
                  StratifyTable1=FALSE,
                  StratifyTable1var=NULL,
                  adjustmentvariables=c("BWT","Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","ModeDelivery"),
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
                  betafinal=processedOut$processedBetas[1:100,],
                  array="450K",
                  maxit=100,
                  Omega=processedOut$Omega,
                  vartype=modelstorun$vartype[i],
                  varofinterest=modelstorun$varofinterest[i],
                  Table1vars=c("Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","ModeDelivery"),
                  StratifyTable1=FALSE,
                  StratifyTable1var=NULL,
                  adjustmentvariables=c("Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","ModeDelivery"),
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

### Running the final models

```{r eval=FALSE}

## First we run the models adjusting for birth weight 

for (i in 1:nrow(modelstorun)){
  
  cat("Outcome:",modelstorun$varofinterest[i],"\n")
  tempresults<-dataAnalysis(phenofinal=phenodataframe,
                  betafinal=processedOut$processedBetas,
                  array="450K",
                  maxit=100,
                  Omega=processedOut$Omega,
                  vartype=modelstorun$vartype[i],
                  varofinterest=modelstorun$varofinterest[i],
                  Table1vars=c("BWT","Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","ModeDelivery"),
                  StratifyTable1=FALSE,
                  StratifyTable1var=NULL,
                  adjustmentvariables=c("BWT","Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","ModeDelivery"),
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
                  Table1vars=c("Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","ModeDelivery"),
                  StratifyTable1=FALSE,
                  StratifyTable1var=NULL,
                  adjustmentvariables=c("Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","ModeDelivery"),
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

### Check model convergence 

The function dataAnalysis includes an indicator of whether each site-specific model converged. If the models are not converging, you can increase the number of specified iterations for the robust regression models using the argument maxit in the dataAnalysis function. The current default number of iterations is 100; increasing this number will make the function slower.

```{r eval=FALSE}

baseoutputdirectory<-"H:/UCLA/PACE/Gestationalage-placenta/HEBC_20210103_Output"

for (i in 1:nrow(modelstorun)){
  
  tempvarofinterest<-modelstorun$varofinterest[i]
  
  cat("Outcome:",tempvarofinterest,"\n")
  tempdirectory<-paste(baseoutputdirectory,tempvarofinterest,sep="/")
  setwd(tempdirectory)
  load("HEBC_20210103_allanalyses.RData")
  lapply(alldataout,function(x)table(x$warnings))

}

baseoutputdirectory<-"H:/UCLA/PACE/Gestationalage-noBWT-placenta/HEBC_20210103_Output"

for (i in 1:nrow(modelstorun)){
  
  tempvarofinterest<-modelstorun$varofinterest[i]
  
  cat("Outcome:",tempvarofinterest,"\n")
  tempdirectory<-paste(baseoutputdirectory,tempvarofinterest,sep="/")
  setwd(tempdirectory)
  load("HEBC_20210103_allanalyses.RData")
  lapply(alldataout,function(x)table(x$warnings))

}



```

### Data upload

The **PACEanalysis** functions will create an “Output” folder in your specified destination directory, which will contain everything to be shared for the subsequent meta-analysis. To submit your results, zip this Output folder, and send it to Sofía Aguilar Lacasaña (sofia.aguilar@isglobal.org).
