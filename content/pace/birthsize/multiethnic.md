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

This stage of the analysis is specific to the chosen exposure/outcome and the specified adjustment variables. Below is the code for all of the analyses to run for the birth size project. Please be sure to update the cohort and date information in the below code for your analysis, as well as the destination path. Finally, be sure to update the column names of the exposure/outcome(s) of interest, the adjustment variables, and the table 1 variables. These should correspond to column names in the dataframe specified in the phenofinal argument of the dataAnalysis function. 

If you have multiple race/ethnicities in your cohort, run the below code. In the analysis restricting to the most prevalent race/ethnicity (i.e. the argument RestricttoEthnicity is TRUE), the data.frame specified by the phenofinal  argument must include the column 'Ethnic', which is assumed to be a categorical variable. Be sure to update IndicatorforEthnicity to the indicator for the most prevalent race/ethnicity in your cohort. For example, if one of your race/ethnicity categories is "White" for individuals of European descent, use that instead of "1". 

### Quick check to make sure the function runs in your cohort

Given the modeling approaches used, the dataAnalysis function requires a good deal of time to run. We recommend first checking whether the function runs on a relatively small subset of sites (i.e. 100 CpG loci). If you encounter any issues, please let us know. If not, proceed to the next step.

```{r eval=FALSE}

modelstorun<-data.frame(varofinterest=c("BWT","LBWbin","HBWbin","BirthLength",
                                        "WeightLengthRatio","HeadCircum"))
modelstorun$varofinterest<-as.character(modelstorun$varofinterest)
modelstorun$vartype<-"OutcomeCont"
modelstorun$vartype[modelstorun$varofinterest %in% c("LBWbin","HBWbin")]<-"OutcomeBin"


## You can reduce this dataframe to whatever variables you have.
## For example, if you only have birthweight, you would specify:
## modelstorun<-data.frame(varofinterest=c("BWT","LBWbin","HBWbin"))

for (i in 1:nrow(modelstorun)){
  
  cat("Outcome:",modelstorun$varofinterest[i],"\n")
  
  ## analysis across all race/ethnicities, adjusting for race/ethnicity
  tempresults<-dataAnalysis(phenofinal=phenodataframe,
                  betafinal=processedOut$processedBetas[1:100,],
                  array="450K",
                  maxit=100,
                  Omega=processedOut$Omega,
                  vartype=modelstorun$vartype[i],
                  varofinterest=modelstorun$varofinterest[i],
                  Table1vars=c("Gestage","Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","Ethnic"),
                  StratifyTable1=FALSE,
                  StratifyTable1var=NULL,
                  adjustmentvariables=c("Gestage","Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","Ethnic"),
                  RunUnadjusted=TRUE,
                  RunAdjusted=TRUE,
                  RunCellTypeAdjusted=TRUE,
                  RunSexSpecific=TRUE,
                  RestricttoEthnicity=FALSE,
                  IndicatorforEthnicity=NULL,
                  destinationfolder="H:\\UCLA\\PACE\\Birthweight-placenta",
                  savelog=TRUE,
                  cohort="HEBC",analysisdate="20210103")
  
    ## restricting to the most prevalent race/ethnicity
    tempresultsNonHispanicWhite<-dataAnalysis(phenofinal=phenodataframe,
                  betafinal=processedOut$processedBetas[1:100,],
                  array="450K",
                  maxit=100,
                  Omega=processedOut$Omega,
                  vartype=modelstorun$vartype[i],
                  varofinterest=modelstorun$varofinterest[i],
                  Table1vars=c("Gestage","Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","Ethnic"),
                  StratifyTable1=FALSE,
                  StratifyTable1var=NULL,
                  adjustmentvariables=c("Gestage","Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","Ethnic"),
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

### Running the final models

```{r eval=FALSE}
for (i in 1:nrow(modelstorun)){
  
  cat("Outcome:",modelstorun$varofinterest[i],"\n")
  
  ## analysis across all race/ethnicities, adjusting for race/ethnicity
  tempresults<-dataAnalysis(phenofinal=phenodataframe,
                  betafinal=processedOut$processedBetas,
                  array="450K",
                  maxit=100,
                  Omega=processedOut$Omega,
                  vartype=modelstorun$vartype[i],
                  varofinterest=modelstorun$varofinterest[i],
                  Table1vars=c("Gestage","Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","Ethnic"),
                  StratifyTable1=FALSE,
                  StratifyTable1var=NULL,
                  adjustmentvariables=c("Gestage","Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","Ethnic"),
                  RunUnadjusted=TRUE,
                  RunAdjusted=TRUE,
                  RunCellTypeAdjusted=TRUE,
                  RunSexSpecific=TRUE,
                  RestricttoEthnicity=FALSE,
                  IndicatorforEthnicity=NULL,
                  destinationfolder="H:\\UCLA\\PACE\\Birthweight-placenta",
                  savelog=TRUE,
                  cohort="HEBC",analysisdate="20210103")
  
    ## restricting to the most prevalent race/ethnicity
    tempresultsNonHispanicWhite<-dataAnalysis(phenofinal=phenodataframe,
                  betafinal=processedOut$processedBetas,
                  array="450K",
                  maxit=100,
                  Omega=processedOut$Omega,
                  vartype=modelstorun$vartype[i],
                  varofinterest=modelstorun$varofinterest[i],
                  Table1vars=c("Gestage","Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI"),
                  StratifyTable1=FALSE,
                  StratifyTable1var=NULL,
                  adjustmentvariables=c("Gestage","Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI"),
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

### Check model convergence 

The function dataAnalysis includes an indicator of whether each site-specific model converged. If the models are not converging, you can increase the number of specified iterations for the robust regression models using the argument maxit in the dataAnalysis function. The current default number of iterations is 100; increasing this number will make the function slower.

```{r eval=FALSE}

baseoutputdirectory<-"H:/UCLA/PACE/Birthweight-placenta/HEBC_20210103_Output"

for (i in 1:nrow(modelstorun)){
  
  tempvarofinterest<-modelstorun$varofinterest[i]
  
  cat("Outcome among all race/ethnicities:",tempvarofinterest,"\n")
  tempdirectory<-paste(baseoutputdirectory,tempvarofinterest,sep="/")
  setwd(tempdirectory)
  load("HEBC_20210103_allanalyses.RData")
  lapply(alldataout,function(x)table(x$warnings))
  
  cat("Outcome among most prevalent race/ethnicity:",tempvarofinterest,"\n")
  tempdirectory<-paste(tempdirectory,"Race_1",sep="/")
  setwd(tempdirectory)
  load("HEBC_20210103_allanalyses.RData")
  lapply(alldataout,function(x)table(x$warnings))

}

```

### Data upload

The **PACEanalysis** functions will create an “Output” folder in your specified destination directory, which will contain everything to be shared for the subsequent meta-analysis. To submit your results, zip this Output folder, and send it to Alexandra (ambinder@hawaii.edu).  
