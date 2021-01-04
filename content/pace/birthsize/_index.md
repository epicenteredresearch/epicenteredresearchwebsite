---
date: "2021-01-03T00:00:00Z"
draft: false
lastmod: "2021-01-03T00:00:00Z"
linktitle: Placenta Birth Size Analysis
menu:
  birthsize:
    name: Step 1. Data Pre-Processing
    weight: 1
summary: Guidance on how to perform the placenta birth size analysis
title: Data Loading and Pre-Processing
toc: true
type: docs
weight: 1
---

## Loading the IDAT files and performing exploratory data analysis

This part of the analysis can be used for multiple downstream site-specific analyses. If you have already run this part, you can skip to "Step 2. Site-Specific Analysis"

### Installing the PACEanalysis package

First need to install required packages if you don't have them already 

```{r eval=FALSE}

## First need to install required packages if you don't have them already
install.packages(c("ggplot2","gplots","reshape","RPMM","RefFreeEWAS",
                    "pvclust","heatmap.plus","GGally","Hmisc","MASS",
                    "sandwich", "lmtest","plyr","remotes"))
remotes::install_github("ki-tools/growthstandards") 

if (!requireNamespace("BiocManager", quietly = TRUE))
  install.packages("BiocManager")
BiocManager::install(c("minfi","sva","sesame","wateRmelon","EpiDISH",
                       "IlluminaHumanMethylationEPICmanifest",
                       "IlluminaHumanMethylation450kmanifest",
                       "IlluminaHumanMethylation450kanno.ilmn12.hg19",
                       "IlluminaHumanMethylationEPICanno.ilm10b4.hg19",
                       "FlowSorted.CordBlood.450k",
                       "FlowSorted.Blood.450k"))

## Need to then install package, specifying path to the source package
install.packages("F:\\PACE\\PACEanalysis_0.1.0.tar.gz",
                 repos = NULL, type="source")

```

### Attaching package and running the first functions

Extensive details regarding the input arguments and outputs for these functions are provided in the function documentation. The function documentation can be viewed after attaching the package (i.e. library(PACEanalysis)), and entering ? followed by the function name in your R console, e.g. ?loadingSamples

```{r eval=FALSE}
## Attach package
library(PACEanalysis)

setwd("F:\\PACE\\Birthweight-placenta")
allphenodata<-read.csv("All_Pheno_and_Basenames.csv",header=TRUE)

## If the data.frame you are going to specify as PhenoData does not include 
## the column "Basename", you will need to also load a data.frame you are
## going to specify for the SamplePlacement argument. PhenoData and 
## SamplePlacement are merged on the column name specified by the 
## IDlink argument

exampledat<-loadingSamples(SamplePlacement=NULL,PhenoData=allphenodata,
                  IDlink="ID",
                  BWTvar="BWT",BATCHvar="Study",
                  SEXvar="GENDER_A",FemaleInd="1",MaleInd="2",
                  ETHNICvar="ETHNIC",GESTvar="gestAge",
                  BIRTHLENGTHvar="BirthLength",HEADCIRCUMvar=NULL,
                  IDATdir="H:\\UCLA\\PACE\\Birthweight-placenta\\IDATfiles",
                  destinationfolder="H:\\UCLA\\PACE\\Birthweight-placenta",
                  savelog=TRUE,
                  cohort="HEBC",analysisdate="20201229")

EDAresults<-ExploratoryDataAnalysis(RGset=exampledat,
                  globalvarexplore=c("BWT","Sex"),
                  destinationfolder="H:\\UCLA\\PACE\\Birthweight-placenta",
                  savelog=TRUE,
                  cohort="HEBC",analysisdate="20201229")
```


### Examining the Exploratory Data Analysis

Before moving on to the next stage, check out the figures and csv files in the new "EDA" subfolder created by the function 'ExploratoryDataAnalysis'. As noted in the function documentation, output includes: 

- red/green signal intensities (are their any low outliers?)

- distribution of beta-values across all sites

- heatmap of chrY (used to identify sex mix-ups)

- signal intensities on X and Y chromosomes (used to identify sex mix-ups)

- dendogram of the SNPs on the array (to identify possible sample replications or appraise relatedness)

- scatterplot of first two PCs

- boxplots of batch and other characteristics (if indicated) vs the top principle components

- csv file of suggested probes to remove based on the percent of samples that failed for that probe (see function details)

- csv file of suggested samples to potentially remove based on evidence of sex mix-ups, a high proportion of failed probes for that sample, or unintentional replicates based on clustering of SNP probes (see dendogram of the SNPs on the array).

The function also returns a list that includes:  

- *SamplestoRemove* : A character vector of Basenames to exclude based on evidence of sex mix-ups, or a high proportion of failed probes for that sample. This vector does not the suspected unintentional replicates; we suggest users check the SNP dendogram to evaluate this themselves.

- *ProbestoRemove* : A character vector of CpGIDs to exclude based on the exploratory data analysis

- *DetectionPval* : A matrix used to mask methylation values with a poor detection p-value (p>0.05); NA if poor detection, 1 otherwise

## Pre-processing the data

This part of the analysis can similarly be used for multiple downstream site-specific analyses because the data pre-processing does not depend on the exposure/outcome of interest. As noted in the function documentation, there are a few options for estimating cell composition, including reference-based methods for blood, cord blood, and placenta. The default is a reference-free based option (see function documentation for full details). To adjust for batch effects using ComBat, the pData for the specified RGset argument must include the column 'Batch'

```{r eval=FALSE}
processedOut<-preprocessingofData(RGset=exampledat,
                  SamplestoRemove=EDAresults$SamplestoRemove,
                  ProbestoRemove=EDAresults$ProbestoRemove,
                  DetectionPvals=EDAresults$DetectionPval,
                  destinationfolder="H:\\UCLA\\PACE\\Birthweight-placenta",
                  compositeCellType="RefFree",
                  KchooseManual=NULL,
                  savelog=TRUE,
                  cohort="HEBC",analysisdate="20201229")

```

### Further quality checking the data

If using a reference-free method to estimate Omega (matrix of cell composition estimates), examine OutlierScreening plots generated by this function to ensure no extreme outliers are being generated. If you see extreme outliers driving the number of cell types, reduce Kchoose value by 1 using the argument KchooseManual, re-run the function preprocessingofData, and re-examine outlier plots. Reduce KchooseManual until extreme outliers are gone. This function will also generate figures (PDFs/PNGs) of associations between top PCs and indicators of batch before and after ComBat (if an indicator for batch was included in the dataset).