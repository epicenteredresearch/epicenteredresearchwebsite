---
date: "2019-05-05T00:00:00+01:00"
draft: false
linktitle: Tips 1-2
menu:
  birthsize:
    name: Step 2. Site-Specific Analysis
    weight: 1
title: Site-Specific Analysis
toc: true
type: docs
weight: 1
---

This stage of the analysis is specific to the chosen exposure/outcome and the specified adjustment variables. Below is the code for all of the analyses to run for the birth size project. Please be sure to update the cohort and date information in the below code for your analysis, as well as the destination path. Finally, be sure to update the column names of the exposure/outcome(s) of interest, the adjustment variables, and the table 1 variables. These should correspond to column names in the dataframe specified in the phenofinal argument of the dataAnalysis function. 

As noted in our analysis plan, if any of the categorical variables below have less than 10 individuals in one of the categories, please let us know and we can decide whether to combine categories or restrict the analysis. In the case of ancestry, if there are more than two ancestry categories in your cohort with less than 10 individuals, we will discuss combining these categories into one “other” classification.  If you don’t have one of these variables, let us know, and we can discuss the best approach to take with your data. 

Make sure that all categorical adjustment variables are coded as a factor; if they are coded numerically, the function will assume to code them as continuous adjustment variables. 

```{r eval=FALSE}

## If you closed prior R session, you can load list of pre-processed objects 
## that is automatically saved by the preprocessingofData function

setwd("H:\\UCLA\\PACE\\Birthweight-placenta\\HEBC_20200710_Output")
load("HEBC_20200710_Preprocessed.RData")

phenodataframe<-as.data.frame(pData(processedOut$mset))
phenodataframe$LBWbin<-ifelse(phenodataframe$BWT<2500,1,0)
phenodataframe$HBWbin<-ifelse(phenodataframe$BWT>4000,1,0)

modelstorun<-data.frame(varofinterest=c("BWT","LBWbin","HBWbin","BirthLength",
                                        "WeightLengthRatio","HeadCircum"))
modelstorun$varofinterest<-as.character(modelstorun$varofinterest)
modelstorun$vartype<-"OutcomeCont"
modelstorun$vartype[modelstorun$varofinterest %in% c("LBWbin","HBWbin")]<-"OutcomeBin"
modelstorun$adjustmentvariables<-c("Gestage","Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","Ethnic")
modelstorun$table1vars<-c("Gestage","Sex","Age","Parity","MaternalEd",
                          "Smoke","preBMI","Ethnic")

## You can reduce this dataframe to whatever variables you have.
## For example, if you only have birthweight, you would specify:
## modelstorun<-data.frame(varofinterest=c("BWT","LBWbin","HBWbin"))

## Make sure that you have sufficient numbers of samples between categories 
## of exposure/outcome of interest as well as adjustment variables; 
## you are assumed to have at least 10

table(phenodataframe$LBWbin)
table(phenodataframe$HBWbin)
table(phenodataframe$Sex)
table(phenodataframe$Parity)
table(phenodataframe$MaternalEd)
table(phenodataframe$Smoke)
table(phenodataframe$Ethnic)

## Make sure all categorical adjustment variables are coded as factors or characters
## 'Sex' is already coded as a character

phenodataframe$Parity<-as.factor(phenodataframe$Parity)
phenodataframe$MaternalEd<-as.factor(phenodataframe$MaternalEd)
phenodataframe$Smoke<-as.factor(phenodataframe$Smoke)
phenodataframe$Ethnic<-as.factor(phenodataframe$Ethnic)

```

### Quick check to make sure the function runs in your cohort

Given the modeling approaches used, the dataAnalysis function requires a good deal of time to run. We recommend first checking whether the function runs on a relatively small subset of sites (i.e. 100 CpG loci). If you encounter any issues, please let us know. If not, proceed to the next step.

```{r eval=FALSE}

for (i in 1:nrow(modelstorun)){
  
  cat("Outcome:",modelstorun$varofinterest[i],"\n")
  
  ## restricting to first 100 loci
  tempresults<-dataAnalysis(phenofinal=phenodataframe,
                  betafinal=processedOut$processedBetas[1:100,], 
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
