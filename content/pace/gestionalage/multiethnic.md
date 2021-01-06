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

This stage of the analysis is specific to the chosen exposure/outcome and the specified adjustment variables. Below is the code for all of the analyses to run for the gestational age project. Please be sure to update the cohort and date information in the below code for your analysis, as well as the destination path. Finally, be sure to update the column names of the exposure/outcome(s) of interest, the adjustment variables, and the table 1 variables. These should correspond to column names in the dataframe specified in the phenofinal argument of the dataAnalysis function. 

If you have multiple race/ethnicities in your cohort, run the below code. In the analysis restricting to the most prevalent race/ethnicity (i.e. the argument RestricttoEthnicity is TRUE), the data.frame specified by the phenofinal argument must include the column ‘Ethnic’, which is assumed to be a categorical variable. Be sure to update IndicatorforEthnicity to the indicator for the most prevalent race/ethnicity in your cohort. For example, if one of your race/ethnicity categories is “White” for individuals of European descent, use that instead of “1”.

First, we will run the models adjusting for birth weight, and then we will run the models without adjusting for birth weight. Be sure to change the destination folder for your models not adjusting for birth weight so that you do not overwrite your initial results.

### Quick check to make sure the function runs in your cohort

Given the modeling approaches used, the dataAnalysis function requires a good deal of time to run. We recommend first checking whether the function runs on a relatively small subset of sites (i.e. 100 CpG loci). If you encounter any issues, please let us know. If not, proceed to the next step.

```{r eval=FALSE}

modelstorun<-data.frame(varofinterest=c("Gestage","PTB"),
                        vartype=c("OutcomeCont","OutcomeBin"))
modelstorun$varofinterest<-as.character(modelstorun$varofinterest)
modelstorun$vartype<-as.character(modelstorun$vartype)

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
                                   "Smoke","preBMI","ModeDelivery","Ethnic"),
                  StratifyTable1=FALSE,
                  StratifyTable1var=NULL,
                  adjustmentvariables=c("BWT","Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","ModeDelivery","Ethnic"),
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
                  RestricttoEthnicity=TRUE,
                  IndicatorforEthnicity="1",
                  destinationfolder="H:\\UCLA\\PACE\\Gestationalage-placenta",
                  savelog=TRUE,
                  cohort="HEBC",analysisdate="20210103")
  
}

## Then we run the models without adjusting for birth weight
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
                                   "Smoke","preBMI","ModeDelivery","Ethnic"),
                  StratifyTable1=FALSE,
                  StratifyTable1var=NULL,
                  adjustmentvariables=c("Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","ModeDelivery","Ethnic"),
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
                  RestricttoEthnicity=TRUE,
                  IndicatorforEthnicity="1",
                  destinationfolder="H:\\UCLA\\PACE\\Gestationalage-noBWT-placenta",
                  savelog=TRUE,
                  cohort="HEBC",analysisdate="20210103")
  
}


```

### Running the final models

Now running the models for all CpG loci

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
                  Table1vars=c("BWT","Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","ModeDelivery","Ethnic"),
                  StratifyTable1=FALSE,
                  StratifyTable1var=NULL,
                  adjustmentvariables=c("BWT","Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","ModeDelivery","Ethnic"),
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
                  Table1vars=c("BWT","Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","ModeDelivery","Ethnic"),
                  StratifyTable1=FALSE,
                  StratifyTable1var=NULL,
                  adjustmentvariables=c("BWT","Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","ModeDelivery","Ethnic"),
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
                                   "Smoke","preBMI","ModeDelivery","Ethnic"),
                  StratifyTable1=FALSE,
                  StratifyTable1var=NULL,
                  adjustmentvariables=c("Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","ModeDelivery","Ethnic"),
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
                  Table1vars=c("Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","ModeDelivery","Ethnic"),
                  StratifyTable1=FALSE,
                  StratifyTable1var=NULL,
                  adjustmentvariables=c("Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","ModeDelivery","Ethnic"),
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

### Check model convergence 

The function dataAnalysis includes an indicator of whether each site-specific model converged. If the models are not converging, you can increase the number of specified iterations for the robust regression models using the argument maxit in the dataAnalysis function. The current default number of iterations is 100; increasing this number will make the function slower.

```{r eval=FALSE}

baseoutputdirectory<-"H:/UCLA/PACE/Gestationalage-placenta/HEBC_20210103_Output"

listchecking<-as.list(rep(NA,nrow(modelstorun)))
names(listchecking)<-modelstorun$varofinterest

listchecking_race1<-as.list(rep(NA,nrow(modelstorun)))
names(listchecking_race1)<-modelstorun$varofinterest

for (i in 1:nrow(modelstorun)){

  tempvarofinterest<-modelstorun$varofinterest[i]

  cat("Outcome among all race/ethnicities:",tempvarofinterest,"\n")
  tempdirectory<-paste(baseoutputdirectory,tempvarofinterest,sep="/")
  setwd(tempdirectory)
  load("HEBC_20210103_allanalyses.RData")
  listchecking[[i]]<-lapply(alldataout,function(x) if(length(x)>1) table(x$warnings))

  cat("Outcome among most prevalent race/ethnicity:",tempvarofinterest,"\n")
  tempdirectory<-paste(tempdirectory,"Race_1",sep="/")
  setwd(tempdirectory)
  load("HEBC_20210103_allanalyses.RData")
  listchecking_race1[[i]]<-lapply(alldataout,function(x) if(length(x)>1) table(x$warnings))

}

## Check them out

listchecking
listchecking_race1


baseoutputdirectory<-"H:/UCLA/PACE/Gestationalage-noBWT-placenta/HEBC_20210103_Output"

listchecking_nobwt<-as.list(rep(NA,nrow(modelstorun)))
names(listchecking_nobwt)<-modelstorun$varofinterest

listchecking_nobwt_race1<-as.list(rep(NA,nrow(modelstorun)))
names(listchecking_nobwt_race1)<-modelstorun$varofinterest

for (i in 1:nrow(modelstorun)){

  tempvarofinterest<-modelstorun$varofinterest[i]

  cat("Outcome among all race/ethnicities:",tempvarofinterest,"\n")
  tempdirectory<-paste(baseoutputdirectory,tempvarofinterest,sep="/")
  setwd(tempdirectory)
  load("HEBC_20210103_allanalyses.RData")
  listchecking_nobwt[[i]]<-lapply(alldataout,function(x) if(length(x)>1) table(x$warnings))

  cat("Outcome among most prevalent race/ethnicity:",tempvarofinterest,"\n")
  tempdirectory<-paste(tempdirectory,"Race_1",sep="/")
  setwd(tempdirectory)
  load("HEBC_20210103_allanalyses.RData")
  listchecking_nobwt_race1[[i]]<-lapply(alldataout,function(x) if(length(x)>1) table(x$warnings))

}

## Check them out

listchecking_nobwt
listchecking_nobwt_race1


```

### Data upload

The **PACEanalysis** functions will create an “Output” folder in your specified destination directory, which will contain everything to be shared for the subsequent meta-analysis. To submit your results, zip this Output folder, and send it to Sofía Aguilar Lacasaña (sofia.aguilar@isglobal.org).