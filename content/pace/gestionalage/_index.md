---
date: "2021-01-03T00:00:00Z"
draft: false
lastmod: "2021-01-03T00:00:00Z"
linktitle: Placenta Gestational Age Analysis
menu:
  gestationalage:
    name: Step 1. Data Pre-Processing
    weight: 1
summary: Guidance on how to perform the placenta gestational age analysis
title: Data Loading and Pre-Processing
toc: true
type: docs
weight: 1
---

## Loading the IDAT files and performing exploratory data analysis

This part of the analysis can be used for multiple downstream site-specific analyses. If you have already run this part, you can skip to "Step 2. Site-Specific Analysis"

If you encounter any issues, please check out our troubleshooting guide to see if there is any guidance that may help: https://www.epicenteredresearch.com/pace/troubleshooting/

### Installing the PACEanalysis package

First need to install required packages if you don't have them already 

```{r eval=FALSE}

## First need to install required packages if you don't have them already
install.packages(c("ggplot2","gplots","reshape","RPMM","RefFreeEWAS","pvclust",
                   "GGally","Hmisc","MASS","sandwich", "lmtest","plyr","remotes","devtools"))

remotes::install_version("heatmap.plus", "1.3")

if (!requireNamespace("BiocManager", quietly = TRUE))
  install.packages("BiocManager")
BiocManager::install(c("methylumi","minfi","sva","sesame","wateRmelon","EpiDISH",
                       "IlluminaHumanMethylationEPICmanifest",
                       "IlluminaHumanMethylation450kmanifest",
                       "IlluminaHumanMethylation450kanno.ilmn12.hg19",
                       "IlluminaHumanMethylationEPICanno.ilm10b4.hg19",
                       "FlowSorted.CordBlood.450k",
                       "FlowSorted.Blood.450k",
                       "illuminaio"))

if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")

BiocManager::install("FlowSorted.Blood.EPIC")

remotes::install_github("bokeh/rbokeh")
remotes::install_github("ki-tools/growthstandards")
devtools::install_github("hhhh5/ewastools")

## If ExperimentHub (>1.17.2), need to update caching location
moveFiles<-function(package){
       olddir <- path.expand(rappdirs::user_cache_dir(appname=package))
       newdir <- tools::R_user_dir(package, which="cache")
       dir.create(path=newdir, recursive=TRUE)
       files <- list.files(olddir, full.names =TRUE)
       moveres <- vapply(files,
           FUN=function(fl){
           filename = basename(fl)
           newname = file.path(newdir, filename)
           file.rename(fl, newname)
           },
           FUN.VALUE = logical(1))
       if(all(moveres)) unlink(olddir, recursive=TRUE)
       }

package="ExperimentHub"
moveFiles(package)

## If recently installed sesame, need to cache the associated annotation data
## This only needs to be done once per new installation of sesame
sesameData::sesameDataCacheAll()

## Need to then install package, specifying path to the source package
install.packages("F:\\PACE\\PACEanalysis_0.1.6.tar.gz",
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
                  cohort="HEBC",analysisdate="20210330")

EDAresults<-ExploratoryDataAnalysis(RGset=exampledat,
                  globalvarexplore=c("BWT","Sex"),
                  DetectionPvalMethod="SeSAMe",
                  DetectionPvalCutoff=0.05,
                  minNbeads=3,
                  FilterZeroIntensities=TRUE,
                  destinationfolder="H:\\UCLA\\PACE\\Birthweight-placenta",
                  savelog=TRUE,
                  cohort="HEBC",analysisdate="20210330")
```


### Examining the Exploratory Data Analysis

Before moving on to the next stage, check out the figures and csv files in the new "EDA" subfolder created by the function 'ExploratoryDataAnalysis'. As noted in the function documentation, output includes: 

- red/green signal intensities (are their any low outliers?)

- distribution of beta-values across all sites

- heatmap of chrY (used to identify sex mix-ups)

- signal intensities on X and Y chromosomes (used to identify sex mix-ups)

- dendogram and heatmap of the SNPs on the array (to identify possible sample replications or appraise relatedness)

- scatterplot of first two PCs

- boxplots of batch and other characteristics (if indicated) vs the top principle components

- csv file of suggested probes to remove based on the percent of samples that failed for that probe (see function details)

- csv file of suggested samples to potentially remove based on evidence of sex mix-ups, a high proportion of failed probes for that sample, low global methylated or unmethylated intensities, or unintentional replicates based on clustering of SNP probes (see dendogram of the SNPs on the array).

The function also returns a list that includes:  

- *SamplestoRemove* : A character vector of Basenames to exclude based on evidence of sex mix-ups, or a high proportion of failed probes for that sample. This vector does not the suspected unintentional replicates; we suggest users check the SNP dendogram to evaluate this themselves.

- *ProbestoRemove* : A character vector of CpGIDs to exclude based on the exploratory data analysis

- *DetectionPval* : A matrix of detection p-values

- *IndicatorGoodIntensity* : A matrix used to mask methylation values with a poor intensity value based on the number of beads (if minNbeads is larger than zero) and intensity values of zero (if FilterZeroIntensities=TRUE); NA if poor intensity value and 1 otherwise

- *logOddsContamin* : The average log odds from the SNP posterior probabilities from the outlier component; capturing how irregular the SNP beta-values deviate from the ideal trimodal distribution. Values greater than -4 are suggest potentially contaminated samples

## Pre-processing the data

This part of the analysis can similarly be used for multiple downstream site-specific analyses because the data pre-processing does not depend on the exposure/outcome of interest. As noted in the function documentation, there are a few options for estimating cell composition, including reference-based methods for blood, cord blood, and placenta. The default is a reference-free based option (see function documentation for full details). To adjust for batch effects using ComBat, the pData for the specified RGset argument must include the column 'Batch'. See ?preprocessingofData for more details.

The detectionMask function masks the beta-values based on a specified detection p-value (default is 0.05) and/or poor intensity values based on the number of beads (specified by minNbeads in the ExploratoryDataAnalysis function; default minimum is zero, meaning no filtering) and/or intensity values of zero (if FilterZeroIntensities is TRUE in the ExploratoryDataAnalysis). It provides the beta-value matrix after this masking. See ?detectionMask for more details.

The outlierprocess function is used to reduce the influence of outliers by one of two methods: trimming or winsorizing. See ?outlierprocess for more details.

```{r eval=FALSE}
processedOut<-preprocessingofData(RGset=exampledat,
                  SamplestoRemove=EDAresults$SamplestoRemove,
                  ProbestoRemove=EDAresults$ProbestoRemove,
                  destinationfolder="H:\\UCLA\\PACE\\Birthweight-placenta",
                  compositeCellType="Placenta",
                  KchooseManual=NULL,
                  savelog=TRUE,
                  cohort="HEBC",analysisdate="20210330")
                  
betasabovedetection<-detectionMask(processedBetas=processedOut$processedBetas,
                                  DetectionPvals=EDAresults$DetectionPval,
                                  DetectionPvalCutoff=0.05,
                                  IndicatorGoodIntensity=EDAresults$IndicatorGoodIntensity,
                                  destinationfolder="H:\\UCLA\\PACE\\Birthweight-placenta",
                                  cohort="HEBC",analysisdate="20210330")

Betasnooutliers<-outlierprocess(processedBetas=betasabovedetection,
                                  quantilemethod="Quantile",
                                  trimming=TRUE,
                                  pct=0.25,
                                  destinationfolder="H:\\UCLA\\PACE\\Birthweight-placenta",
                                  cohort="HEBC",analysisdate="20210330")
  

```

### Further quality checking the data

If using a reference-free method to estimate Omega (matrix of cell composition estimates), examine OutlierScreening plots generated by this function to ensure no extreme outliers are being generated. If you see extreme outliers driving the number of cell types, reduce Kchoose value by 1 using the argument KchooseManual, re-run the function preprocessingofData, and re-examine outlier plots. Reduce KchooseManual until extreme outliers are gone. This function will also generate figures (PDFs/PNGs) of associations between top PCs and indicators of batch before and after ComBat (if an indicator for batch was included in the dataset).

### Looking at cell composition distribution

Check out the estimated cell composition distribution for the samples, does it look correct?

```{r eval=FALSE}

library(reshape)
evaluatingOmega<-as.data.frame(processedOut$Omega)
evaluatingOmega<-cbind(Basename=rownames(evaluatingOmega),evaluatingOmega)
evaluatingOmega<-melt(evaluatingOmega,id="Basename")

ggplot(evaluatingOmega,aes(Basename,value,fill=variable))+
  geom_bar(stat="identity",position="stack")+
  scale_fill_brewer("Cell Type",palette="Set2")+
  theme_bw()+
  ylab("")+xlab("")+
  scale_x_discrete(expand=c(0,0))+
  scale_y_continuous(expand=c(0,0))+
  theme(axis.text.x = element_text(angle=90,vjust=0.5))
```