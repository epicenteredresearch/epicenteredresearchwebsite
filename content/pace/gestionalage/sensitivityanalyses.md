---
date: "2021-01-03T00:00:00+01:00"
draft: false
linktitle: Step 4. Sensitivity Analyses
menu:
  gestationalage:
    name: Step 4. Sensitivity Analyses
    weight: 3
title: Sensitivity Analyses
toc: true
type: docs
weight: 2
---

The last step is re-running the analysis among mothers without pregnancy complications or physician initiated delivery that might affect gestational age. As an extra sensitivity analysis, we ask you to run the gestational age model again among the subset of  **term births (>=37 weeks gestation)** from mothers without pregnancy complications or physician initiated delivery that might affect gestational age.  Here a suggested list of variables to consider for exclusion:
+	Pre-existing medical conditions in the mother: 
    - diabetes (type 1, type 2)
    - hypertension
    - autoimmunediseases (including SLE, RA, and sclerodermia)
    - immuno-compromised patients
+	Pregnancy complications:
    - gestational diabetes
    - placental abruption 
    - placenta previa
    - pre-eclampsia/eclampsia
    - polyhydramnios, oligohydramnios
    - placental insufficiency
    - cervical insufficiency, isoimmunization, 
    - cervical cerclaje
+ Physician initiated delivery, which might be indicative of pregnancy complications.
+ Caesarian section, if due to pregnancy complications. C-sections due to complications during labor are kept in
+ Restrict to only term births (>=37 weeks gestation)

### Note: you only need to subset the phenofinal

You only need to subset the phenofinal data.frame to the samples in the given subset, the function will automatically subset the betafinal matrix and Omega matrix.

### Re-running final models

Re-run the models in Step 3 among the specified subset. Be sure to change the destination folder so that you do not overwrite prior results.

#### Example, if running among multiple race/ethnicities

```{r eval=FALSE}

## The exclusions is going to depend on your cohort
phenodataframesensitivity<-phenodataframe[which(phenodataframe$GDM == 0 &
                                                phenodataframe$PE == 0 &
                                                phenodataframe$Diabetes == 0 &
                                                phenodataframe$Hypertension == 0 &
                                                phenodataframe$Previa == 0),]

tempresults<-dataAnalysis(phenofinal=phenodataframesensitivity,
                          betafinal=processedOut$processedBetas,
                          array="450K",
                          maxit=100,
                          Omega=processedOut$Omega,
                          vartype="OutcomeCont",
                          varofinterest="Gestage",
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
                          destinationfolder="H:\\UCLA\\PACE\\Gestationalage-sensitivityanalysis-placenta",
                          savelog=TRUE,
                          cohort="HEBC",analysisdate="20210103")

tempresultsNonHispanicWhite<-dataAnalysis(phenofinal=phenodataframesensitivity,
                          betafinal=processedOut$processedBetas,
                          array="450K",
                          maxit=100,
                          Omega=processedOut$Omega,
                          vartype="OutcomeCont",
                          varofinterest="Gestage",
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
                          destinationfolder="H:\\UCLA\\PACE\\Gestationalage-sensitivityanalysis-placenta",
                          savelog=TRUE,
                          cohort="HEBC",analysisdate="20210103")
                  
## Then we run the models without adjusting for birth weight
## Be sure to change the destination folder

tempresults<-dataAnalysis(phenofinal=phenodataframesensitivity,
                          betafinal=processedOut$processedBetas,
                          array="450K",
                          maxit=100,
                          Omega=processedOut$Omega,
                          vartype="OutcomeCont",
                          varofinterest="Gestage",
                          Table1vars=c("Sex","Age","Parity","MaternalEd",
                                       "Smoke","preBMI","ModeDelivery","Ethnic"),
                          StratifyTable1=FALSE,
                          StratifyTable1var=NULL,
                          adjustmentvariables=c("Sex","Age","Parity","MaternalEd",
                                                "Smoke","preBMI","ModeDelivery","Ethnic"),
                          RunUnadjusted=TRUE,
                          RunAdjusted=TRUE,
                          RunCellTypeAdjusted=TRUE,
                          RunSexSpecific=TRUE,
                          RestricttoEthnicity=FALSE,
                          IndicatorforEthnicity=NULL,
                          destinationfolder="H:\\UCLA\\PACE\\Gestationalage-noBWT-sensitivityanalysis-placenta",
                          savelog=TRUE,
                          cohort="HEBC",analysisdate="20210103")

tempresultsNonHispanicWhite<-dataAnalysis(phenofinal=phenodataframesensitivity,
                          betafinal=processedOut$processedBetas,
                          array="450K",
                          maxit=100,
                          Omega=processedOut$Omega,
                          vartype="OutcomeCont",
                          varofinterest="Gestage",
                          Table1vars=c("Sex","Age","Parity","MaternalEd",
                                       "Smoke","preBMI","ModeDelivery"),
                          StratifyTable1=FALSE,
                          StratifyTable1var=NULL,
                          adjustmentvariables=c("Sex","Age","Parity","MaternalEd",
                                                "Smoke","preBMI","ModeDelivery"),
                          RunUnadjusted=TRUE,
                          RunAdjusted=TRUE,
                          RunCellTypeAdjusted=TRUE,
                          RunSexSpecific=TRUE,
                          RestricttoEthnicity=TRUE,
                          IndicatorforEthnicity="1",
                          destinationfolder="H:\\UCLA\\PACE\\Gestationalage-noBWT-sensitivityanalysis-placenta",
                          savelog=TRUE,
                          cohort="HEBC",analysisdate="20210103")



```