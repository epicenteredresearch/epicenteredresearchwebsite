---
date: "2021-01-03T00:00:00+01:00"
draft: false
linktitle: Step 5. Secondary analysis if single race/ethnicity in cohort
menu:
  birthsize:
    parent: Step 4. Secondary Site-Specific Analyses
    weight: 5
title: Secondary analysis if single race/ethnicity in cohort
toc: true
type: docs
weight: 5
---

This part of the analysis is the same as before except we are not removing pregnancy complications from the dataset. Instead, we are including pregnancy complications and adjusting for them in the analysis. Please be sure to add these variables to your `adjustmentvariables` argument in the `dataAnalysis` function. We are also changing the `analysisname` to "secondary".

### Quick check to make sure the function runs in your cohort

Given the modeling approaches used, the `dataAnalysis` function requires a good deal of time to run. We recommend first checking whether the function runs on a relatively small subset of sites (i.e. 100 CpG loci). If you encounter any issues, please let us know. If not, proceed to the next step.

```{r eval=FALSE}

allvarsofinterest=c("BWT_Zscore","BirthLength_Zscore","HeadCircum_Zscore","wlr_Zscore")

## You can reduce this dataframe to whatever variables you have.
## For example, if you only have birthweight, you would specify:
## allvarsofinterest=c("BWT_Zscore")

for (i in 1:length(allvarsofinterest)){
  
  cat("Outcome:",allvarsofinterest[i],"\n")
  
  tempresults<-dataAnalysis(phenofinal=phenodataframe,
                  betafinal=Betasnooutliers[1:100,],
                  array="450K",
                  maxit=100,
                  robust=TRUE,
                  Omega=processedOut$Omega,
                  vartype="ExposureCont",
                  varofinterest=allvarsofinterest[i],
                  Table1vars=c("BWT","Gestage","Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","Ethnic","GDM","Meanlog2oddsContamination"),
                  StratifyTable1=FALSE,
                  StratifyTable1var=NULL,
                  adjustmentvariables=c("Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","Ethnic","GDM","Meanlog2oddsContamination"),
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
                  cohort="HEBC",analysisdate="20210618",
                  analysisname="secondary")
  
  
}

```

### Running the final models

Now running the models for all CpG loci

```{r eval=FALSE}

for (i in 1:length(allvarsofinterest)){
  
  cat("Outcome:",allvarsofinterest[i],"\n")
  
  tempresults<-dataAnalysis(phenofinal=phenodataframe,
                  betafinal=Betasnooutliers,
                  array="450K",
                  maxit=100,
                  robust=TRUE,
                  Omega=processedOut$Omega,
                  vartype="ExposureCont",
                  varofinterest=allvarsofinterest[i],
                  Table1vars=c("BWT","Gestage","Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","Ethnic","GDM","Meanlog2oddsContamination"),
                  StratifyTable1=FALSE,
                  StratifyTable1var=NULL,
                  adjustmentvariables=c("Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","Ethnic","GDM","Meanlog2oddsContamination"),
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
                  cohort="HEBC",analysisdate="20210618",
                  analysisname="secondary")
  
  
}

```
### Check model convergence 

The function dataAnalysis includes an indicator of whether each site-specific model converged. If the models are not converging, you can increase the number of specified iterations for the robust regression models using the argument maxit in the dataAnalysis function. The current default number of iterations is 100; increasing this number will make the function slower.

```{r eval=FALSE}

baseoutputdirectory<-"H:/UCLA/PACE/Birthweight-placenta/HEBC_20210618_Output"

listchecking<-as.list(rep(NA,length(allvarsofinterest)))
names(listchecking)<-allvarsofinterest

for (i in 1:length(allvarsofinterest)){

  tempvarofinterest<-allvarsofinterest[i]

  cat("Outcome:",tempvarofinterest,"\n")
  tempdirectory<-paste(baseoutputdirectory,"/",tempvarofinterest,"_secondary",sep="")
  setwd(tempdirectory)
  tempfilename<-paste("HEBC_20210618_",tempvarofinterest,"_secondary_allanalyses.RData",sep="")
  load(tempfilename)
  if("CellInteraction" %in% names(alldataout)) alldataout$CellInteraction<-NULL
  if("warnings" %in% colnames(alldataout[[1]])) listchecking[[i]]<-lapply(alldataout,function(x) if(length(x)>1) table(x$warnings))

}

## Check them out
listchecking




```

### Data upload

The **PACEanalysis** functions will create an “Output” folder in your specified destination directory, which will contain everything to be shared for the subsequent meta-analysis. To submit your results, zip this Output folder, and send it to Alexandra (ambinder@hawaii.edu).  
