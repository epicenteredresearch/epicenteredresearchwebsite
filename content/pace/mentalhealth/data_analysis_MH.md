---
date: "2021-01-03T00:00:00+01:00"
draft: false
linktitle: Step 3. Data Analysis
menu:
  mentalhealth:
    name: Step 3. Data Analysis
    weight: 2
title: Site-Specific Data Analysis
toc: true
type: docs
weight: 2
---

This stage of the analysis is specific to the chosen exposure/outcome and the specified adjustment variables. Below is the code for all of the analyses to run for the mental health project. Please be sure to update the cohort and date information in the below code for your analysis, as well as the destination path. Finally, be sure to update the column names of the exposure/outcome(s) of interest, the adjustment variables, and the table 1 variables. These should correspond to column names in the dataframe specified in the phenofinal argument of the dataAnalysis function. 

### Quick check to make sure the function runs in your cohort

Given the modeling approaches used, the dataAnalysis function requires a good deal of time to run. We recommend first checking whether the function runs on a relatively small subset of sites (i.e. 100 CpG loci). If you encounter any issues, please let us know. If not, proceed to the next step.

#### If you plan to run your analysis in parallel

Running your site-specific analysis in parallel (argument `runparallel=TRUE`) can greatly reduce the computation time. To run your analysis in parallel, you will need to specify the number of cores to use (argument `number_cores`). You can use the `detectCores()` function in the `parallel` library to check the number of cores available; depending on your computing resources, you may need to specify less cores than the total number available. If you are not planning to run your analysis in parallel, specify `runparallel=FALSE`.

```r

# if needed reload Preprocessed data of step 1 and the final phenodataframe from step 2
# library(PACEanalysis)
# load("C:/methylation_placenta/ITU_20220709_Output/ITU_20220709_Preprocessed.Rdata")
# load("C:/methylation_placenta/phenofinal_mentalhealth.Rdata")

# MAIN MODELS WITH EXPOSURE AS CONTINUOUS (M01-M03 in the Analysis plan)

modelstorun<-data.frame(varofinterest=c("ANX_CONT","DEP_CONT","COM_CONT"))
modelstorun$varofinterest<-as.character(modelstorun$varofinterest)
modelstorun$vartype<-"ExposureCont"


for (i in 1:nrow(modelstorun)){
  
  cat("Exposure:",modelstorun$varofinterest[i],"\n")
  
  tempresults<-dataAnalysis(phenofinal=phenodataframe,
                            betafinal=Betasnooutliers[1:100,],
                            array="EPIC",
                            maxit=100,
                            robust=TRUE,
                            Omega=processedOut$Omega,
                            vartype=modelstorun$vartype[i],
                            varofinterest=modelstorun$varofinterest[i],
                            Table1vars=c("Sex","SES","AGE","PARITY",
                                         "SMOKE","PCA1","PCA2","PCA3","SELECTION","Meanlog2oddsContamination"),
                            StratifyTable1=FALSE,
                            StratifyTable1var=NULL,             
                            adjustmentvariables=c("Sex","SES","AGE","PARITY",
                                                  "SMOKE","PCA1","PCA2","PCA3","SELECTION","Meanlog2oddsContamination"),
                            RunUnadjusted=FALSE,
                            RunAdjusted=FALSE,
                            RunCellTypeAdjusted=TRUE,
                            RunSexSpecific=FALSE,
                            RestrictToSubset = FALSE,
                            RestrictionVar = NULL,
                            RestrictToIndicator = NULL,
                            RunCellTypeInteract = FALSE,
                            destinationfolder="C:/methylation_placenta",
                            savelog=TRUE,
                            cohort="ITU",
                            analysisdate = "20220709",
                            analysisname = "testMAIN")
}


# MAIN MODELS WITH EXPOSURE AS BINARY (M04-M06 in the Analysis plan)

modelstorun<-data.frame(varofinterest=c("ANX_BIN","DEP_BIN","COM_BIN"))
modelstorun$varofinterest<-as.character(modelstorun$varofinterest)
modelstorun$vartype<-"ExposureCat"


for (i in 1:nrow(modelstorun)){
  
  cat("Exposure:",modelstorun$varofinterest[i],"\n")
  
  tempresults<-dataAnalysis(phenofinal=phenodataframe,
                            betafinal=Betasnooutliers[1:100,],
                            array="EPIC",
                            maxit=100,
                            robust=TRUE,
                            Omega=processedOut$Omega,
                            vartype=modelstorun$vartype[i],
                            varofinterest=modelstorun$varofinterest[i],
                            Table1vars=c("Sex","SES","AGE","PARITY",
                                         "SMOKE","PCA1","PCA2","PCA3","SELECTION","Meanlog2oddsContamination"),
                            StratifyTable1=TRUE,
                            StratifyTable1var=modelstorun$varofinterest[i],             
                            adjustmentvariables=c("Sex","SES","AGE","PARITY",
                                                  "SMOKE","PCA1","PCA2","PCA3","SELECTION","Meanlog2oddsContamination"),
                            RunUnadjusted=FALSE,
                            RunAdjusted=FALSE,
                            RunCellTypeAdjusted=TRUE,
                            RunSexSpecific=FALSE,
                            RestrictToSubset = FALSE,
                            RestrictionVar = NULL,
                            RestrictToIndicator = NULL,
                            RunCellTypeInteract = FALSE,
                            destinationfolder="C:/methylation_placenta",
                            savelog=TRUE,
                            cohort="ITU",
                            analysisdate = "20220709",
                            analysisname = "testMAIN")
}

# MODELS ADDITIONALLY ADJUSTED FOR GESTATIONAL AGE AND BIRTH WEIGHT - BINARY EXPOSURE (M07-M09 in the Analysis plan)

modelstorun<-data.frame(varofinterest=c("ANX_BIN","DEP_BIN","COM_BIN"))
modelstorun$varofinterest<-as.character(modelstorun$varofinterest)
modelstorun$vartype<-"ExposureCat"


for (i in 1:nrow(modelstorun)){
  
  cat("Exposure:",modelstorun$varofinterest[i],"\n")
  
  tempresults<-dataAnalysis(phenofinal=phenodataframe,
                            betafinal=Betasnooutliers[1:100,],
                            array="EPIC",
                            maxit=100,
                            robust=TRUE,
                            Omega=processedOut$Omega,
                            vartype=modelstorun$vartype[i],
                            varofinterest=modelstorun$varofinterest[i],
                            Table1vars=c("Sex","SES","AGE","PARITY",
                                         "SMOKE","PCA1","PCA2","PCA3","BWT","GESTA","SELECTION"),
                            StratifyTable1=TRUE,
                            StratifyTable1var=modelstorun$varofinterest[i],             
                            adjustmentvariables=c("Sex","SES","AGE","PARITY",
                                                  "SMOKE","PCA1","PCA2","PCA3","BWT","GESTA","SELECTION"),
                            RunUnadjusted=FALSE,
                            RunAdjusted=FALSE,
                            RunCellTypeAdjusted=TRUE,
                            RunSexSpecific=FALSE,
                            RestrictToSubset = FALSE,
                            RestrictionVar = NULL,
                            RestrictToIndicator = NULL,
                            RunCellTypeInteract = FALSE,
                            destinationfolder="C:/methylation_placenta",
                            savelog=TRUE,
                            cohort="ITU",
                            analysisdate = "20220709",
                            analysisname = "testBWTGESTA")
}


# SENSITIVITY ANALYSIS MODELS - excluding mothers who used psychiatric medication (M10-M12 in the Analysis plan)

modelstorun<-data.frame(varofinterest=c("ANX_CONT","DEP_CONT","COM_CONT"))
modelstorun$varofinterest<-as.character(modelstorun$varofinterest)
modelstorun$vartype<-"ExposureCont"


for (i in 1:nrow(modelstorun)){
  
  cat("Exposure:",modelstorun$varofinterest[i],"\n")
  
  tempresults<-dataAnalysis(phenofinal=phenodataframe,
                            betafinal=Betasnooutliers[1:100,],
                            array="EPIC",
                            maxit=100,
                            robust=TRUE,
                            Omega=processedOut$Omega,
                            vartype=modelstorun$vartype[i],
                            varofinterest=modelstorun$varofinterest[i],
                            Table1vars=c("Sex","SES","AGE","PARITY",
                                         "SMOKE","PCA1","PCA2","PCA3","SELECTION","Meanlog2oddsContamination"),
                            StratifyTable1=FALSE,
                            StratifyTable1var=NULL,             
                            adjustmentvariables=c("Sex","SES","AGE","PARITY",
                                                  "SMOKE","PCA1","PCA2","PCA3","SELECTION","Meanlog2oddsContamination"),
                            RunUnadjusted=FALSE,
                            RunAdjusted=FALSE,
                            RunCellTypeAdjusted=TRUE,
                            RunSexSpecific=FALSE,
                            RestrictToSubset = TRUE,
                            RestrictionVar = "MEDICATION",
                            RestrictToIndicator = 0,
                            RunCellTypeInteract = FALSE,
                            destinationfolder="C:/methylation_placenta",
                            savelog=TRUE,
                            cohort="ITU",
                            analysisdate = "20220709",
                            analysisname = "testSENSITIVITY")
}





```

### Running the final models

Now running the models for all CpG loci.
Please note that the R package automatically saves the results in the end.

```r

# MAIN MODELS WITH EXPOSURE AS CONTINUOUS (M01-M03 in the Analysis plan)

modelstorun<-data.frame(varofinterest=c("ANX_CONT","DEP_CONT","COM_CONT"))
modelstorun$varofinterest<-as.character(modelstorun$varofinterest)
modelstorun$vartype<-"ExposureCont"


for (i in 1:nrow(modelstorun)){
  
  cat("Exposure:",modelstorun$varofinterest[i],"\n")
  
  tempresults<-dataAnalysis(phenofinal=phenodataframe,
                            betafinal=Betasnooutliers,
                            array="EPIC",
                            maxit=100,
                            robust=TRUE,
                            Omega=processedOut$Omega,
                            vartype=modelstorun$vartype[i],
                            varofinterest=modelstorun$varofinterest[i],
                            Table1vars=c("Sex","SES","AGE","PARITY",
                                         "SMOKE","PCA1","PCA2","PCA3","SELECTION","Meanlog2oddsContamination"),
                            StratifyTable1=FALSE,
                            StratifyTable1var=NULL,             
                            adjustmentvariables=c("Sex","SES","AGE","PARITY",
                                                  "SMOKE","PCA1","PCA2","PCA3","SELECTION","Meanlog2oddsContamination"),
                            RunUnadjusted=FALSE,
                            RunAdjusted=FALSE,
                            RunCellTypeAdjusted=TRUE,
                            RunSexSpecific=FALSE,
                            RestrictToSubset = FALSE,
                            RestrictionVar = NULL,
                            RestrictToIndicator = NULL,
                            RunCellTypeInteract = FALSE,
                            destinationfolder="C:/methylation_placenta",
                            savelog=TRUE,
                            cohort="ITU",
                            analysisdate = "20220709",
                            analysisname = "MAIN")
}


# MAIN MODELS WITH EXPOSURE AS BINARY (M04-M06 in the Analysis plan)

modelstorun<-data.frame(varofinterest=c("ANX_BIN","DEP_BIN","COM_BIN"))
modelstorun$varofinterest<-as.character(modelstorun$varofinterest)
modelstorun$vartype<-"ExposureCat"


for (i in 1:nrow(modelstorun)){
  
  cat("Exposure:",modelstorun$varofinterest[i],"\n")
  
  tempresults<-dataAnalysis(phenofinal=phenodataframe,
                            betafinal=Betasnooutliers,
                            array="EPIC",
                            maxit=100,
                            robust=TRUE,
                            Omega=processedOut$Omega,
                            vartype=modelstorun$vartype[i],
                            varofinterest=modelstorun$varofinterest[i],
                            Table1vars=c("Sex","SES","AGE","PARITY",
                                         "SMOKE","PCA1","PCA2","PCA3","SELECTION","Meanlog2oddsContamination"),
                            StratifyTable1=TRUE,
                            StratifyTable1var=modelstorun$varofinterest[i],             
                            adjustmentvariables=c("Sex","SES","AGE","PARITY",
                                                  "SMOKE","PCA1","PCA2","PCA3","SELECTION","Meanlog2oddsContamination"),
                            RunUnadjusted=FALSE,
                            RunAdjusted=FALSE,
                            RunCellTypeAdjusted=TRUE,
                            RunSexSpecific=FALSE,
                            RestrictToSubset = FALSE,
                            RestrictionVar = NULL,
                            RestrictToIndicator = NULL,
                            RunCellTypeInteract = FALSE,
                            destinationfolder="C:/methylation_placenta",
                            savelog=TRUE,
                            cohort="ITU",
                            analysisdate = "20220709",
                            analysisname = "MAIN")
}

# MODELS ADDITIONALLY ADJUSTED FOR GESTATIONAL AGE AND BIRTH WEIGHT - BINARY EXPOSURE (M07-M09 in the Analysis plan)

modelstorun<-data.frame(varofinterest=c("ANX_BIN","DEP_BIN","COM_BIN"))
modelstorun$varofinterest<-as.character(modelstorun$varofinterest)
modelstorun$vartype<-"ExposureCat"


for (i in 1:nrow(modelstorun)){
  
  cat("Exposure:",modelstorun$varofinterest[i],"\n")
  
  tempresults<-dataAnalysis(phenofinal=phenodataframe,
                            betafinal=Betasnooutliers,
                            array="EPIC",
                            maxit=100,
                            robust=TRUE,
                            Omega=processedOut$Omega,
                            vartype=modelstorun$vartype[i],
                            varofinterest=modelstorun$varofinterest[i],
                            Table1vars=c("Sex","SES","AGE","PARITY",
                                         "SMOKE","PCA1","PCA2","PCA3","BWT","GESTA","SELECTION"),
                            StratifyTable1=TRUE,
                            StratifyTable1var=modelstorun$varofinterest[i],             
                            adjustmentvariables=c("Sex","SES","AGE","PARITY",
                                                  "SMOKE","PCA1","PCA2","PCA3","BWT","GESTA","SELECTION"),
                            RunUnadjusted=FALSE,
                            RunAdjusted=FALSE,
                            RunCellTypeAdjusted=TRUE,
                            RunSexSpecific=FALSE,
                            RestrictToSubset = FALSE,
                            RestrictionVar = NULL,
                            RestrictToIndicator = NULL,
                            RunCellTypeInteract = FALSE,
                            destinationfolder="C:/methylation_placenta",
                            savelog=TRUE,
                            cohort="ITU",
                            analysisdate = "20220709",
                            analysisname = "BWTGESTA")
}


# SENSITIVITY ANALYSIS MODELS - excluding mothers who used psychiatric medication (M10-M12 in the Analysis plan)

modelstorun<-data.frame(varofinterest=c("ANX_CONT","DEP_CONT","COM_CONT"))
modelstorun$varofinterest<-as.character(modelstorun$varofinterest)
modelstorun$vartype<-"ExposureCont"


for (i in 1:nrow(modelstorun)){
  
  cat("Exposure:",modelstorun$varofinterest[i],"\n")
  
  tempresults<-dataAnalysis(phenofinal=phenodataframe,
                            betafinal=Betasnooutliers,
                            array="EPIC",
                            maxit=100,
                            robust=TRUE,
                            Omega=processedOut$Omega,
                            vartype=modelstorun$vartype[i],
                            varofinterest=modelstorun$varofinterest[i],
                            Table1vars=c("Sex","SES","AGE","PARITY",
                                         "SMOKE","PCA1","PCA2","PCA3","SELECTION","Meanlog2oddsContamination"),
                            StratifyTable1=FALSE,
                            StratifyTable1var=NULL,             
                            adjustmentvariables=c("Sex","SES","AGE","PARITY",
                                                  "SMOKE","PCA1","PCA2","PCA3","SELECTION","Meanlog2oddsContamination"),
                            RunUnadjusted=FALSE,
                            RunAdjusted=FALSE,
                            RunCellTypeAdjusted=TRUE,
                            RunSexSpecific=FALSE,
                            RestrictToSubset = TRUE,
                            RestrictionVar = "MEDICATION",
                            RestrictToIndicator = 0,
                            RunCellTypeInteract = FALSE,
                            destinationfolder="C:/methylation_placenta",
                            savelog=TRUE,
                            cohort="ITU",
                            analysisdate = "20220709",
                            analysisname = "SENSITIVITY")
}


```
### Check model convergence 

The function dataAnalysis includes an indicator of whether each site-specific model converged. If the models are not converging, you can increase the number of specified iterations for the robust regression models using the argument maxit in the dataAnalysis function. The current default number of iterations is 100; increasing this number will make the function slower.

```r

baseoutputdirectory<-"C:/methylation_placenta/ITU_20220709_Output"

# BINARY EXPOSURE MODELS

modelstorun<-data.frame(varofinterest=c("ANX_BIN","DEP_BIN","COM_BIN"))
modelstorun$varofinterest<-as.character(modelstorun$varofinterest)

listchecking_MAIN_BIN<-as.list(rep(NA,nrow(modelstorun)))
names(listchecking_MAIN_BIN)<-modelstorun$varofinterest

for (i in 1:nrow(modelstorun)){
  
  tempvarofinterest<-modelstorun$varofinterest[i]
  
  cat("Outcome:",tempvarofinterest,"\n")
  tempdirectory<-paste(baseoutputdirectory,"/",tempvarofinterest,"_MAIN",sep="")
  setwd(tempdirectory)
  tempfilename<-paste("ITU_20220709_",tempvarofinterest,"_MAIN_allanalyses.RData",sep="")
  load(tempfilename)
  
  if("warnings" %in% colnames(alldataout[[1]])) listchecking_MAIN_BIN[[i]]<-lapply(alldataout,function(x) if(length(x)>1) table(x$warnings))
  
}

listchecking_BWTGESTA<-as.list(rep(NA,nrow(modelstorun)))
names(listchecking_BWTGESTA)<-modelstorun$varofinterest

for (i in 1:nrow(modelstorun)){
  
  tempvarofinterest<-modelstorun$varofinterest[i]
  
  cat("Outcome:",tempvarofinterest,"\n")
  tempdirectory<-paste(baseoutputdirectory,"/",tempvarofinterest,"_BWTGESTA",sep="")
  setwd(tempdirectory)
  tempfilename<-paste("ITU_20220709_",tempvarofinterest,"_BWTGESTA_allanalyses.RData",sep="")
  load(tempfilename)
  if("warnings" %in% colnames(alldataout[[1]])) listchecking_BWTGESTA[[i]]<-lapply(alldataout,function(x) if(length(x)>1) table(x$warnings))
  
}



## CONTINUOUS EXPOSURE MODELS

modelstorun<-data.frame(varofinterest=c("ANX_CONT","DEP_CONT","COM_CONT"))
modelstorun$varofinterest<-as.character(modelstorun$varofinterest)

listchecking_MAIN_CONT<-as.list(rep(NA,nrow(modelstorun)))
names(listchecking_MAIN_CONT)<-modelstorun$varofinterest

for (i in 1:nrow(modelstorun)){
  
  tempvarofinterest<-modelstorun$varofinterest[i]
  
  cat("Outcome:",tempvarofinterest,"\n")
  tempdirectory<-paste(baseoutputdirectory,"/",tempvarofinterest,"_MAIN",sep="")
  setwd(tempdirectory)
  tempfilename<-paste("ITU_20220709_",tempvarofinterest,"_MAIN_allanalyses.RData",sep="")
  load(tempfilename)
  
  if("warnings" %in% colnames(alldataout[[1]])) listchecking_MAIN_CONT[[i]]<-lapply(alldataout,function(x) if(length(x)>1) table(x$warnings))
  
}


listchecking_SENSITIVITY<-as.list(rep(NA,nrow(modelstorun)))
names(listchecking_SENSITIVITY)<-modelstorun$varofinterest

for (i in 1:nrow(modelstorun)){
  
  tempvarofinterest<-modelstorun$varofinterest[i]
  
  cat("Outcome:",tempvarofinterest,"\n")
  tempdirectory<-paste(baseoutputdirectory,"/",tempvarofinterest,"_SENSITIVITY/MEDICATION_0",sep="")
  setwd(tempdirectory)
  tempfilename<-paste("ITU_20220709_",tempvarofinterest,"_SENSITIVITY_allanalyses.RData",sep="")
  load(tempfilename)
  
  if("warnings" %in% colnames(alldataout[[1]])) listchecking_SENSITIVITY[[i]]<-lapply(alldataout,function(x) if(length(x)>1) table(x$warnings))
  
}

# CHECK ALL MODELS 
# "none" means no problems. "rlm failed to converge" means that maxit should be increased from 100.

listchecking_MAIN_BIN
listchecking_BWTGESTA
listchecking_MAIN_CONT
listchecking_SENSITIVITY



```

### Data upload

The **PACEanalysis** functions will create an “Output” folder(s) in your specified destination directory, which will contain everything to be shared for the subsequent meta-analysis. We would like to have all the output-files from dataAnalyses (models 1-12, or all models that you ran), AND the probe summary (COHORTNAME_DATE_Summarize_Beta_Values.csv) and "trimming log" (COHORTNAME_DATE_NewGapHunter_CpGs.csv) from the data pre-processing phase. Descriptives tables are produced automatically by the package as output-files. Finally, we ask that you  send us a README along with the results (see step 2 or analysis plan for instructions on what to report in the README).

Please do not send us any individual-level data.

Please do not delete the files created during the analyses. We may need to ask you to check further details during the meta-analysis phase.

To submit your results, zip your results and ask for an upload link from Sara (sara.sammallahti@helsinki.fi).  

**Thank you very much for participating in this project!**
