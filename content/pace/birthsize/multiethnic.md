---
date: "2021-01-03T00:00:00+01:00"
draft: false
linktitle: Step 3. If multiple race/ethnicities in cohort
menu:
  birthsize:
    parent: Step 2. Main Site-Specific Analyses
    weight: 2
title: If multiple race/ethnicities in cohort
toc: true
type: docs
weight: 2
---

This stage of the analysis is specific to the chosen exposure/outcome and the specified adjustment variables. Below is the code for all of the analyses to run for the birth size project. Please be sure to update the cohort and date information in the below code for your analysis, as well as the destination path. Finally, be sure to update the column names of the exposure/outcome(s) of interest, the adjustment variables, and the table 1 variables. These should correspond to column names in the dataframe specified in the `phenofinal` argument of the `dataAnalysis` function. 

If you have multiple race/ethnicities in your cohort, run the below code. In the analysis restricting to the most prevalent race/ethnicity (i.e. the argument `RestrictToSubset=TRUE`), the data.frame specified by the `phenofinal` argument must include the column specified by the argument `RestrictionVar`, which is assumed to be a categorical variable for race/ethnicity. Be sure to update `RestrictToIndicator` to the indicator for the most prevalent race/ethnicity in your cohort. For example, if one of your race/ethnicity categories is “White” for individuals of European descent, use that instead of “1”.

A few important specifications to note in the `dataAnalysis` function:

   - By specifying `vartype="ExposureCont"`, the birth size characteristic is modeled as a continuous exposure and assumed to be numeric
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

```r

allvarsofinterest=c("BWT_Zscore","BirthLength_Zscore","HeadCircum_Zscore","wlr_Zscore")

## You can reduce this dataframe to whatever variables you have.
## For example, if you only have birthweight, you would specify:
## allvarsofinterest=c("BWT_Zscore")

for (i in 1:length(allvarsofinterest)){
  
  cat("Outcome:",allvarsofinterest[i],"\n")
  
  ## analysis across all race/ethnicities, adjusting for race/ethnicity
  tempresults<-dataAnalysis(phenofinal=phenodataframe,
                  betafinal=Betasnooutliers[1:100,],
                  array="450K",
                  maxit=100,
                  robust=TRUE,
                  Omega=processedOut$Omega,
                  vartype="ExposureCont",
                  varofinterest=allvarsofinterest[i],
                  Table1vars=c("BWT","Gestage","Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","Ethnic","Meanlog2oddsContamination"),
                  StratifyTable1=FALSE,
                  StratifyTable1var=NULL,
                  adjustmentvariables=c("Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","Ethnic","Meanlog2oddsContamination"),
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
                  analysisname="main")
  
    ## restricting to the most prevalent race/ethnicity
    tempresultsNonHispanicWhite<-dataAnalysis(phenofinal=phenodataframe,
                  betafinal=Betasnooutliers[1:100,],
                  array="450K",
                  maxit=100,
                  robust=TRUE,
                  Omega=processedOut$Omega,
                  vartype="ExposureCont",
                  varofinterest=allvarsofinterest[i],
                  Table1vars=c("BWT","Gestage","Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","Ethnic","Meanlog2oddsContamination"),
                  StratifyTable1=FALSE,
                  StratifyTable1var=NULL,
                  adjustmentvariables=c("Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","Ethnic","Meanlog2oddsContamination"),
                  RunUnadjusted=TRUE,
                  RunAdjusted=TRUE,
                  RunCellTypeAdjusted=TRUE,
                  RunSexSpecific=TRUE,
                  RunCellTypeInteract=TRUE,
                  RestrictToSubset=TRUE,
                  RestrictionVar="Ethnic",
                  RestrictToIndicator="1",
                  destinationfolder="H:\\UCLA\\PACE\\Birthweight-placenta",
                  savelog=TRUE,
                  cohort="HEBC",analysisdate="20210618",
                  analysisname="main")
  
}

```

### Running the final models

Now running the models for all CpG loci

```r
for (i in 1:length(allvarsofinterest)){
  
  cat("Outcome:",allvarsofinterest[i],"\n")
  
  ## analysis across all race/ethnicities, adjusting for race/ethnicity
  tempresults<-dataAnalysis(phenofinal=phenodataframe,
                  betafinal=Betasnooutliers,
                  array="450K",
                  maxit=100,
                  robust=TRUE,
                  Omega=processedOut$Omega,
                  vartype="ExposureCont",
                  varofinterest=allvarsofinterest[i],
                  Table1vars=c("BWT","Gestage","Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","Ethnic","Meanlog2oddsContamination"),
                  StratifyTable1=FALSE,
                  StratifyTable1var=NULL,
                  adjustmentvariables=c("Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","Ethnic","Meanlog2oddsContamination"),
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
                  analysisname="main")
  
    ## restricting to the most prevalent race/ethnicity
    tempresultsNonHispanicWhite<-dataAnalysis(phenofinal=phenodataframe,
                  betafinal=Betasnooutliers,
                  array="450K",
                  maxit=100,
                  robust=TRUE,
                  Omega=processedOut$Omega,
                  vartype="ExposureCont",
                  varofinterest=allvarsofinterest[i],
                  Table1vars=c("BWT","Gestage","Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","Ethnic","Meanlog2oddsContamination"),
                  StratifyTable1=FALSE,
                  StratifyTable1var=NULL,
                  adjustmentvariables=c("Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","Ethnic","Meanlog2oddsContamination"),
                  RunUnadjusted=TRUE,
                  RunAdjusted=TRUE,
                  RunCellTypeAdjusted=TRUE,
                  RunSexSpecific=TRUE,
                  RunCellTypeInteract=TRUE,
                  RestrictToSubset=TRUE,
                  RestrictionVar="Ethnic",
                  RestrictToIndicator="1",
                  destinationfolder="H:\\UCLA\\PACE\\Birthweight-placenta",
                  savelog=TRUE,
                  cohort="HEBC",analysisdate="20210618",
                  analysisname="main")
  
}

```

### Check model convergence 

The function `dataAnalysis` includes an indicator of whether each site-specific model converged. If the models are not converging, you can increase the number of specified iterations for the robust regression models using the argument `maxit` in the `dataAnalysis` function. The current default number of iterations is 100; increasing this number will make the function slower.

```r

baseoutputdirectory<-"H:/UCLA/PACE/Birthweight-placenta/HEBC_20210618_Output"

listchecking<-as.list(rep(NA,nrow(modelstorun)))
names(listchecking)<-modelstorun$varofinterest

listchecking_Ethnic_1<-as.list(rep(NA,nrow(modelstorun)))
names(listchecking_Ethnic_1)<-modelstorun$varofinterest

for (i in 1:nrow(modelstorun)){

  tempvarofinterest<-modelstorun$varofinterest[i]

  cat("Outcome:",tempvarofinterest,"\n")
  tempdirectory<-paste(baseoutputdirectory,"/",tempvarofinterest,"_main",sep="")
  setwd(tempdirectory)
  tempfilename<-paste("HEBC_20210618_",tempvarofinterest,"_main_allanalyses.RData",sep="")
  load(tempfilename)
  if("CellInteraction" %in% names(alldataout)) alldataout$CellInteraction<-NULL
  if("warnings" %in% colnames(alldataout[[1]])) listchecking[[i]]<-lapply(alldataout,function(x) if(length(x)>1) table(x$warnings))
  
  tempdirectory<-paste(baseoutputdirectory,"/",tempvarofinterest,"_main/Ethnic_1",sep="")
  setwd(tempdirectory)
  tempfilename<-paste("HEBC_20210618_",tempvarofinterest,"_main_allanalyses.RData",sep="")
  load(tempfilename)
  if("CellInteraction" %in% names(alldataout)) alldataout$CellInteraction<-NULL
  if("warnings" %in% colnames(alldataout[[1]])) listchecking_Ethnic_1[[i]]<-lapply(alldataout,function(x) if(length(x)>1) table(x$warnings))
  
}

## Check them out
listchecking
listchecking_Ethnic_1

```

### Data upload

The **PACEanalysis** functions will create an “Output” folder in your specified destination directory, which will contain everything to be shared for the subsequent meta-analysis. To submit your results, zip this Output folder, and send it to Alexandra (ambinder@hawaii.edu).  
