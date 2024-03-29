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

This stage of the analysis is specific to the chosen exposure/outcome and the specified adjustment variables. Below is the code for all of the analyses to run for the birth size project. Please be sure to update the cohort and date information in the below code for your analysis, as well as the destination path. Finally, be sure to update the column names of the exposure/outcome(s) of interest, the adjustment variables, and the table 1 variables. These should correspond to column names in the dataframe specified in the `phenofinal` argument of the `dataAnalysis` function. 

A few important specifications to note in the `dataAnalysis` function:

   - By specifying `vartype="ExposureCont"`, the gestational age characteristic is modeled as a continuous exposure and assumed to be numeric; this specification also works when the exposure is a binary indicator (0 or 1)
   - By specifying `robust=TRUE`, analyses are run via robust regression using iterated re-weighted least squares (Huber weights), and White's estimator for the variance
   - The `Table1vars` argument is used to specify the variables to include in our descriptive Table 1
   - The `adjustmentvariables` argument specifies the variables we will adjust for in our models
   - `RunUnadjusted=TRUE`, indicates to run unadjusted models
   - `RunAdjusted=TRUE`, indicates to also run adjusted models
   - `RunCellTypeAdjusted=TRUE`, indicates to also run adjusted models, further adjusting for estimated cell composition
   - `RunSexSpecific=TRUE`, indicates to also run the models stratified by infant sex
   - `RunCellTypeInteract=TRUE`, indicates to evaluate interactions with cell compositions

Additional details regarding the `dataAnalysis` function can be found by running `?dataAnalysis`


### Quick check to make sure the function runs in your cohort

Given the modeling approaches used, the `dataAnalysis` function requires a good deal of time to run. We recommend first checking whether the function runs on a relatively small subset of sites (i.e. 100 CpG loci). If you encounter any issues, please let us know. If not, proceed to the next step.

#### If you plan to run your analysis in parallel

Running your site-specific analysis in parallel (argument `runparallel=TRUE`) can greatly reduce the computation time. To run your analysis in parallel, you will need to specify the number of cores to use (argument `number_cores`). You can use the `detectCores()` function in the `parallel` library to check the number of cores available; depending on your computing resources, you may need to specify less cores than the total number available. If you are not planning to run your analysis in parallel, specify `runparallel=FALSE`.


```{r eval=FALSE}

## if running in parallel, checking the number of available cores 
library(parallel)
detectCores() # should probably choose at least one less than the number available 

allvarsofinterest=c("Gestage","PTB")

for (i in 1:length(allvarsofinterest)){
  
  cat("Exposure:",allvarsofinterest[i],"\n")
  tempresults<-dataAnalysis(phenofinal=phenodataframe,
                  betafinal=Betasnooutliers[1:100,],
                  array="450K",
                  maxit=100,
                  robust=TRUE,
                  Omega=processedOut$Omega,
                  vartype="ExposureCont",
                  varofinterest=allvarsofinterest[i],
                  Table1vars=c("BWT","Gestage","PTB","Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","ModeDelivery","Meanlog2oddsContamination"),
                  StratifyTable1=FALSE,
                  StratifyTable1var=NULL,
                  adjustmentvariables=c("Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","ModeDelivery","Meanlog2oddsContamination"),
                  RunUnadjusted=TRUE,
                  RunAdjusted=TRUE,
                  RunCellTypeAdjusted=TRUE,
                  RunSexSpecific=TRUE,
                  RunCellTypeInteract=TRUE,
                  RestrictToSubset=FALSE,
                  RestrictionVar=NULL,
                  RestrictToIndicator=NULL,
                  number_cores=8,
                  runparallel=TRUE,
                  destinationfolder="H:\\UCLA\\PACE\\Gestationalage-placenta",
                  savelog=TRUE,
                  cohort="HEBC",analysisdate="20220709",
                  analysisname="main")
  
}

```

### Running the final models

Now running the models for all CpG loci

```{r eval=FALSE}

for (i in 1:length(allvarsofinterest)){
  
  cat("Exposure:",allvarsofinterest[i],"\n")
  tempresults<-dataAnalysis(phenofinal=phenodataframe,
                  betafinal=Betasnooutliers,
                  array="450K",
                  maxit=100,
                  robust=TRUE,
                  Omega=processedOut$Omega,
                  vartype="ExposureCont",
                  varofinterest=allvarsofinterest[i],
                  Table1vars=c("BWT","Gestage","PTB","Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","ModeDelivery","Meanlog2oddsContamination"),
                  StratifyTable1=FALSE,
                  StratifyTable1var=NULL,
                  adjustmentvariables=c("Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","ModeDelivery","Meanlog2oddsContamination"),
                  RunUnadjusted=TRUE,
                  RunAdjusted=TRUE,
                  RunCellTypeAdjusted=TRUE,
                  RunSexSpecific=TRUE,
                  RunCellTypeInteract=TRUE,
                  RestricttoEthnicity=FALSE,
                  IndicatorforEthnicity=NULL,
                  number_cores=8,
                  runparallel=TRUE,
                  destinationfolder="H:\\UCLA\\PACE\\Gestationalage-placenta",
                  savelog=TRUE,
                  cohort="HEBC",analysisdate="20220709",
                  analysisname="main")
  
}

```

### Check model convergence 

The function `dataAnalysis` includes an indicator of whether each site-specific model converged. If the models are not converging, you can increase the number of specified iterations for the robust regression models using the argument `maxit` in the `dataAnalysis` function. The current default number of iterations is 100; increasing this number will make the function slower.

```{r eval=FALSE}

baseoutputdirectory<-"H:/UCLA/PACE/Gestationalage-placenta/HEBC_20220709_Output"

listchecking<-as.list(rep(NA,length(allvarsofinterest)))
names(listchecking)<-allvarsofinterest

for (i in 1:length(allvarsofinterest)){

  tempvarofinterest<-allvarsofinterest[i]

  cat("Exposure:",tempvarofinterest,"\n")
  tempdirectory<-paste(baseoutputdirectory,"/",tempvarofinterest,"_main",sep="")
  setwd(tempdirectory)
  tempfilename<-paste("HEBC_20220709_",tempvarofinterest,"_main_allanalyses.RData",sep="")
  load(tempfilename)
  if("CellInteraction" %in% names(alldataout)) alldataout$CellInteraction<-NULL
  if("warnings" %in% colnames(alldataout[[1]])) listchecking[[i]]<-lapply(alldataout,function(x) if(length(x)>1) table(x$warnings))

}

## Check them out
listchecking

```

### Data upload

The **PACEanalysis** functions will create an “Output” folder in your specified destination directory, which will contain everything to be shared for the subsequent meta-analysis. To submit your results, zip this Output folder, and send it to Sofía Aguilar Lacasaña (sofia.aguilar@isglobal.org).
