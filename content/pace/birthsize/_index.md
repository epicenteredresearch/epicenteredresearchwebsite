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

## Loading the IDAT files 

This part of the analysis can be used for multiple downstream site-specific analyses. If you have already run this part, you can skip to "Step 2. Site-Specific Analysis"

If you encounter any issues, please check out our troubleshooting guide to see if there is any guidance that may help: https://www.epicenteredresearch.com/pace/troubleshooting/

### Installing the PACEanalysis package

First need to install required packages if you don't have them already 

```r

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
install.packages("F:\\PACE\\PACEanalysis_0.1.7.tar.gz",
                 repos = NULL, type="source")
```

### Attaching package and loading the samples

Extensive details regarding the input arguments and outputs for these functions are provided in the function documentation. The function documentation can be viewed after attaching the package (i.e. `library(PACEanalysis)`), and entering `?` followed by the function name in your R console, e.g. `?loadingSamples`

```r
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
                  cohort="HEBC",analysisdate="20210618")


```

<p>&nbsp;</p>


## Performing exploratory data analysis

To perform initial exploratory data analysis, run the function `ExploratoryDataAnalysis`. For this analysis, we are estimating the detection p-values using the method applied in the SeSAMe package (`DetectionPvalMethod="SeSAMe"`), using a cut-off of 0.05 (`DetectionPvalCutoff=0.05`). We are also suggesting to filter out probes with less than 3 beads (`minNbeads=3`) and with intensity values of zero (`FilterZeroIntensities=TRUE`). The `globalvarexplore` argument is used to specify a vector of characteristic column names to explore in association with the first 12 principle components. See `?ExploratoryDataAnalysis` for more details.

```r

EDAresults<-ExploratoryDataAnalysis(RGset=exampledat,
                  globalvarexplore=c("BWT","Sex"),
                  DetectionPvalMethod="SeSAMe",
                  DetectionPvalCutoff=0.05,
                  minNbeads=3,
                  FilterZeroIntensities=TRUE,
                  destinationfolder="H:\\UCLA\\PACE\\Birthweight-placenta",
                  savelog=TRUE,
                  cohort="HEBC",analysisdate="20210618")
                  
                  
```

### Examining the Exploratory Data Analysis

#### For more details on the quality control checks, see here: [link](https://www.epicenteredresearch.com/pace/qcsteps/)

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

<p>&nbsp;</p>

## Pre-processing the data

#### For more details on the quality control checks, see here: [link](https://www.epicenteredresearch.com/pace/qcsteps/)

This part of the analysis can similarly be used for multiple downstream site-specific analyses because the data pre-processing does not depend on the exposure/outcome of interest. As noted in the function documentation, there are a few options for estimating cell composition, including reference-based methods for blood, cord blood, and placenta. The default is a reference-free based option (see function documentation for full details). For this analysis, please use the reference-based method for the placenta ([ref](https://bmcgenomics.biomedcentral.com/articles/10.1186/s12864-020-07186-6)), which estimates cell composition using the constrained Houseman method. 

To adjust for batch effects using ComBat, the pData for the specified RGset argument must include the column 'Batch'. See `?preprocessingofData` for more details. Also check out Association_between_Batch_and_Top_PCs_Raw_Data plot to see the association between the Batch indicator and the main components of the variation in the data.

For this analysis, we recommend the removing the following samples (see [here](https://www.epicenteredresearch.com/pace/qcsteps/) for more details):

  + **Samples with an estimated contamination score (`EDAresults$logOddsContamin`) that is greater than -1**: You can identify these samples based on the Meanlog2oddsContamination variable in the Recommended_Samples_to_Remove csv output by the `ExploratoryDataAnalysis` function. Also see the Heatmap_SNP_Outliers_Suggesting_Contamination plot. 
  
  + **Samples with the wrong predicted sex**: Check the Sex_Wrong variable in the Recommended_Samples_to_Remove csv output by the `ExploratoryDataAnalysis` function. Also see the Signal_Intensities_on_X_and_Y_Chromosomes_by_Sex plot and the Heatmap_of_Y_Chromosome_by_Sex plot.
  
  + **Unintentional replicates**: Check the Cluster variable in the Recommended_Samples_to_Remove csv output by the `ExploratoryDataAnalysis` function. However, be careful with these exclusions, this information is just intended to help guide an appraisal of potential unintentional duplicates. Also see the Heatmap_of_SNPs plot and Dendrogram_based_on_Array_SNPs plot. 
  
  + **Samples with too many failed probes**: Check the TooManyFailedProbes variable in the Recommended_Samples_to_Remove csv output by the `ExploratoryDataAnalysis` function. Probes that are flagged as 'failed' depend on the DetectionPvalMethod, DetectionPvalCutoff, minNbeads=3, and FilterZeroIntensities arguments in the `ExploratoryDataAnalysis` function.
  
  + **Low signal intensity overall**: Check the IntensityCat variable in the Recommended_Samples_to_Remove csv output by the `ExploratoryDataAnalysis` function. Also see the Methyl_and_Unmethyl_Signal_Intensities plot. 

```r

SexWrongRemove<-c("9630789136_R02C01","9630789048_R06C01","9630789238_R02C01")
TooManyFailedProbes<-c("9630789216_R05C01","9630789238_R03C01","9630789098_R04C02")
PossibleContamination<-c("9630789136_R01C01","9630789121_R06C02")
Unintentionalreps<-c("9630789136_R01C01", "9630789121_R06C02")

allsamplestoexclude<-c(SexWrongRemove,TooManyFailedProbes,PossibleContamination,Unintentionalreps)

processedOut<-preprocessingofData(RGset=exampledat,
                  SamplestoRemove=allsamplestoexclude,
                  ProbestoRemove=EDAresults$ProbestoRemove,
                  destinationfolder="H:\\UCLA\\PACE\\Birthweight-placenta",
                  compositeCellType="Placenta",
                  KchooseManual=NULL,
                  savelog=TRUE,
                  cohort="HEBC",analysisdate="20210618")

```

We will use the SeSAMe method to estimate the detection p-values (specified by the `DetectionPvalMethod` in the `ExploratoryDataAnalysis`). The `detectionMask` function masks the beta-values based on a specified detection p-value (for this analysis, using the cut-off of 0.05; specified by `DetectionPvalCutoff=0.05` in the `detectionMask` function) and a matrix that indicates other poor intensity values, which is output by the `ExploratoryDataAnalysis` function. Other poor intensity values that are masked include those having less than three beads per probe (specified by `minNbeads=3` in the `ExploratoryDataAnalysis` function) and intensity values of zero (`FilterZeroIntensities` is `TRUE` in the `ExploratoryDataAnalysis`). The `detectionMask` function outputs a beta-value matrix after masking (with `NA`) of all of these indicators of poor intensity values. See `?detectionMask` for more details.

```r
betasabovedetection<-detectionMask(processedBetas=processedOut$processedBetas,
                                  DetectionPvals=EDAresults$DetectionPval,
                                  DetectionPvalCutoff=0.05,
                                  IndicatorGoodIntensity=EDAresults$IndicatorGoodIntensity,
                                  destinationfolder="H:\\UCLA\\PACE\\Birthweight-placenta",
                                  cohort="HEBC",analysisdate="20210618")

```

The `outlierprocess` function is used to reduce the influence of outliers by one of two methods: trimming or winsorizing. If `trimming=TRUE`, remove extreme outliers based on gaps that must be at least 3*IQR; the cutoff for the number of outliers in a group is either a maximum of 5 or 0.0025 of the total number of samples (whichever is larger). Detected outliers are recoded as `NA`. If `trimming=FALSE`, winsorize outliers based on the specified percentile (default is 1%, 0.5% on each side, corresponding to `pct=0.005`). Percentiles can be estimated based on the quantile function or the empirical beta-distribution. For this analysis, winsorizing 1%. See `?outlierprocess` for more details.

```r
Betasnooutliers<-outlierprocess(processedBetas=betasabovedetection,
                                  quantilemethod="EmpiricalBeta",
                                  trimming=FALSE,
                                  pct=0.005,
                                  destinationfolder="H:\\UCLA\\PACE\\Birthweight-placenta",
                                  cohort="HEBC",analysisdate="20210618")

```

### Further quality checking the data

If using a reference-free method to estimate Omega (matrix of cell composition estimates), examine OutlierScreening plots generated by this function to ensure no extreme outliers are being generated. If you see extreme outliers driving the number of cell types, reduce `Kchoose` value by 1 using the argument `KchooseManual`, re-run the function `preprocessingofData`, and re-examine outlier plots. Reduce `KchooseManual` until extreme outliers are gone. This function will also generate figures (PDFs/PNGs) of associations between top PCs and indicators of batch before and after ComBat (if an indicator for batch was included in the dataset).

### Looking at cell composition distribution

Check out the estimated cell composition distribution for the samples, does it look correct?

```r

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