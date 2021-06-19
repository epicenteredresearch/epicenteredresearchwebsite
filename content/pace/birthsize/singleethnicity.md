---
date: "2021-01-03T00:00:00+01:00"
draft: false
linktitle: Step 3. If single race/ethnicity in cohort
menu:
  birthsize:
    parent: Step 2. Main Site-Specific Analyses
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
                  betafinal=Betasnooutliers[1:100,],
                  array="450K",
                  maxit=100,
                  robust=TRUE,
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
                  RunCellTypeInteract=TRUE,
                  RestrictToSubset=FALSE,
                  RestrictionVar=NULL,
                  RestrictToIndicator=NULL,
                  destinationfolder="H:\\UCLA\\PACE\\Birthweight-placenta",
                  savelog=TRUE,
                  cohort="HEBC",analysisdate="20210330",
                  analysisname="main")
  
}

```

### Running the final models

Now running the models for all CpG loci

```{r eval=FALSE}

for (i in 1:nrow(modelstorun)){
  
  cat("Outcome:",modelstorun$varofinterest[i],"\n")
  tempresults<-dataAnalysis(phenofinal=phenodataframe,
                  betafinal=Betasnooutliers,
                  array="450K",
                  maxit=100,
                  robust=TRUE,
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
                  RunCellTypeInteract=TRUE,
                  RestrictToSubset=FALSE,
                  RestrictionVar=NULL,
                  RestrictToIndicator=NULL,
                  destinationfolder="H:\\UCLA\\PACE\\Birthweight-placenta",
                  savelog=TRUE,
                  cohort="HEBC",analysisdate="20210330",
                  analysisname="main")
  
}

```
### Check model convergence 

The function dataAnalysis includes an indicator of whether each site-specific model converged. If the models are not converging, you can increase the number of specified iterations for the robust regression models using the argument maxit in the dataAnalysis function. The current default number of iterations is 100; increasing this number will make the function slower.

```{r eval=FALSE}

baseoutputdirectory<-"H:/UCLA/PACE/Birthweight-placenta/HEBC_20210330_Output"

listchecking<-as.list(rep(NA,nrow(modelstorun)))
names(listchecking)<-modelstorun$varofinterest

for (i in 1:nrow(modelstorun)){

  tempvarofinterest<-modelstorun$varofinterest[i]

  cat("Outcome:",tempvarofinterest,"\n")
  tempdirectory<-paste(baseoutputdirectory,"/",tempvarofinterest,"_main",sep="")
  setwd(tempdirectory)
  tempfilename<-paste("HEBC_20210330_",tempvarofinterest,"_main_allanalyses.RData",sep="")
  load(tempfilename)
  if("CellInteraction" %in% names(alldataout)) alldataout$CellInteraction<-NULL
  if("warnings" %in% colnames(alldataout[[1]])) listchecking[[i]]<-lapply(alldataout,function(x) if(length(x)>1) table(x$warnings))

}

## Check them out
listchecking




```

### Data upload

The **PACEanalysis** functions will create an “Output” folder in your specified destination directory, which will contain everything to be shared for the subsequent meta-analysis. To submit your results, zip this Output folder, and send it to Alexandra (ambinder@hawaii.edu).  
