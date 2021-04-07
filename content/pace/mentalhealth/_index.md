---
title: "Data Loading and Pre-Processing"
draft: no
date: '2021-01-03T00:00:00Z'
linktitle: Placenta Mental Health Analysis
menu:
  mentalhealth:
    name: Step 1. Data Pre-Processing
    weight: 1
summary: Guidance on how to perform the placenta mental health analysis
lastmod: '2021-01-03T00:00:00Z'
toc: yes
type: docs
weight: 1
---

## Loading the IDAT files and performing exploratory data analysis

This part of the analysis can be used for multiple downstream site-specific analyses. If you have already run this part, you can skip to "Step 2. Data Preparation"

If you encounter any issues, please check out our troubleshooting guide to see if there is any guidance that may help: https://www.epicenteredresearch.com/pace/troubleshooting/

### Installing the PACEanalysis package

First need to install required packages if you don't have them already 

```{r eval=FALSE}

## First need to install required packages if you don't have them already
install.packages(c("ggplot2","gplots","reshape","RPMM","RefFreeEWAS",
                    "pvclust","heatmap.plus","GGally","Hmisc","MASS",
                    "sandwich", "lmtest","plyr","remotes","devtools"))
devtools::install_github("ki-tools/growthstandards") 
devtools::install_github("hhhh5/ewastools")

if (!requireNamespace("BiocManager", quietly = TRUE))
  install.packages("BiocManager")
BiocManager::install(c("minfi","sva","sesame","wateRmelon","EpiDISH",
                       "IlluminaHumanMethylationEPICmanifest",
                       "IlluminaHumanMethylation450kmanifest",
                       "IlluminaHumanMethylation450kanno.ilmn12.hg19",
                       "IlluminaHumanMethylationEPICanno.ilm10b4.hg19",
                       "FlowSorted.CordBlood.450k",
                       "FlowSorted.Blood.450k"))

### Additionally, before the PACEanalysis package can be installed, it may be necessary to
### install the following packages too - and to choose n (none) if R asks whether or not to update other packages:

BiocManager::install(c("FlowSorted.Blood.EPIC",
                       "FlowSorted.CordBloodCombined.450k"
                       ))


## Need to then install package, specifying path to the source package
install.packages("C:/PACE/PACEanalysis_0.1.4.tar.gz",
                 repos = NULL, type="source")

```

### Attaching package and running the first functions

Extensive details regarding the input arguments and outputs for these functions are provided in the function documentation. The function documentation can be viewed after attaching the package (i.e. library(PACEanalysis)), and entering ? followed by the function name in your R console, e.g. ?loadingSamples

```{r eval=FALSE}
## Attach package
library(PACEanalysis)


## If running on Windows, it may be necessary to set a high memory limit for the pre-processing, e.g 

memory.limit(size=100000) ## 100000 MB

## It may also be necessary to increase Virtual Memory Allocation on Windows. 
## In Windows 10: Control panel - Adjust the appearance and performance of Windows - Advanced - Virtual Memory

setwd("F:\\PACE\\Birthweight-placenta")
allphenodata<-read.csv("All_Pheno_and_Basenames.csv",header=TRUE)

## If the data.frame you are going to specify as PhenoData does not include 
## the column "Basename", you will need to also load a data.frame you are
## going to specify for the SamplePlacement argument. PhenoData and 
## SamplePlacement are merged on the column name specified by the 
## IDlink argument

## It is possibly to load all the phenodata at this stage, but i


exampledat<-loadingSamples(
  SamplePlacement=NULL,
  PhenoData=allphenodata,
  IDlink="Sample_Name",
  BWTvar="BWT",
  BATCHvar="Sample_Plate", # please choose a catecorigal variable that identifies batch effects in your cohort 
  SEXvar="SEX",
  FemaleInd="1",MaleInd="2",
  ETHNICvar=NULL,
  GESTvar="GESTA",
  BIRTHLENGTHvar=NULL,
  HEADCIRCUMvar=NULL,
  IDATdir="C:/methylation_placenta/idat_placenta", # select your idat folder
  destinationfolder="C:/methylation_placenta",     
  savelog=TRUE,                             # A subfolder will be created in this case C:/methylation_placenta/ITU_20210401_Output
  cohort="ITU",
  analysisdate = "20210331")    

## Please note that the following ExploratoryDataAnalysis will take several hours

EDAresults<-ExploratoryDataAnalysis(RGset=exampledat,
   globalvarexplore=c("BWT","Sex"),    # We don't need these in the Mental Health project, but other projects might use these. 
   destinationfolder="C:/methylation_placenta",
   savelog=TRUE,                         # EDA subfolder will be created, in this case C:/methylation_placenta/ITU_20210401_Output/EDA
   cohort="ITU", 
   analysisdate="20210331")

## You may want to save your data at this point, e.g
## save(exampledat,EDAresults,file="C:/methylation_placenta/exampledat_EDAresults.Rdata")

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

- *DetectionPval* : A matrix used to mask methylation values with a poor detection p-value (p>0.05); NA if poor detection, 1 otherwise

- *logOddsContamin* : The average log odds from the SNP posterior probabilities from the outlier component; capturing how irregular the SNP beta-values deviate from the ideal trimodal distribution. Values greater than -4 are suggest potentially contaminated samples

## Pre-processing the data

This part of the analysis can similarly be used for multiple downstream site-specific analyses because the data pre-processing does not depend on the exposure/outcome of interest. As noted in the function documentation, there are a few options for estimating cell composition, including reference-based methods for blood, cord blood, and placenta. The default is a reference-free based option (see function documentation for full details). To adjust for batch effects using ComBat, the pData for the specified RGset argument must include the column 'Batch'

```{r eval=FALSE}

## Please note that this step will take substantially longer than the previous ExploratoryDataAnalysis

processedOut<-preprocessingofData(RGset=exampledat,
                  SamplestoRemove=EDAresults$SamplestoRemove,
                  ProbestoRemove=EDAresults$ProbestoRemove,
                  DetectionPvals=EDAresults$DetectionPval,
                  destinationfolder="H:\\UCLA\\PACE\\Birthweight-placenta",
                  destinationfolder="C:/methylation_placenta",
                  compositeCellType="RefFree",
                  KchooseManual=NULL,
                  savelog=TRUE,        
                  cohort="ITU",
                  analysisdate = "20210331") # If analysisdate is unchanged, output will be saved to the same subfolder than in previous steps

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