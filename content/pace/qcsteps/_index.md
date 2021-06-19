---
date: "2021-01-03T00:00:00Z"
draft: false
lastmod: "2021-01-03T00:00:00Z"
linktitle: QC Steps
menu:
  qcsteps:
    name: QC Steps
    weight: 1
summary: Further background on QC steps
title: QC Steps
toc: true
type: docs
weight: 1
---

<p>&nbsp;</p>

## loadingSamples
This function renames the variables, puts the variables in the right mode for the analysis, and loads the raw array data.

### 1. Input files

#### 1.1.	IDAT files

#### 1.2.	SamplePlacement 
A data.frame that includes 'Basename', sample ID and optionally technical batch information. Not required if 'Basename' is in PhenoData. NOTE: Basename is the ID present in the IDAT files.

#### 1.3 PhenoData
A required data.frame that includes exposure/outcome, covariates and sample ID.

<p>&nbsp;</p>

## ExploratoryDataAnalysis

### 2.	Sample QC

#### 2.1.	Low-quality samples
It plots the beta-value distributions and the median methylated vs un-methylated signal intensities to guide the identification of samples of low quality (minfi R package). If the average of the median methylated and un-methylated signal intensities is less than 10.5 (cut-off used in the minfi package), the samples are flagged for removal in the Recommended_Samples_to_Remove file (UnmethylMedInt, MethylMedInt and IntensityCat variables). 

#### 2.2.	Sample call rate
To identify probes not different from the background, the researcher can select one of the two approaches:

  -	The detection p-value calculated by the SeSAMe R package, which uses out-of-band array hybridization to calculate the detection p-values ([link](https://academic.oup.com/nar/article/46/20/e123/5061974)).
  -	The detection p-value calculated by the ewastools R package, which restricts to probes for which no high concentration of the opposite fluorophore is expected to estimate the background distribution ([link](https://clinicalepigeneticsjournal.biomedcentral.com/articles/10.1186/s13148-019-0615-3)).
Then, samples with a call rate <95% are listed in the Recommended_Samples_to_Remove file (TooManyFailedProbes variable).

#### 2.3.	Sex consistency
To identify samples with discordant sex between methylation and metadata, it plots the chip wide medians of the X and Y chromosome probes using the minfi R package. Samples for which their predicted sex (getSex in minfi R package) is discordant with their labelled sex are listed in the Recommended_Samples_to_Remove file (Sex_Wrong variable).

#### 2.4.	Duplicates
It clusters samples based on their genetic similarity using the SNPs included in the methylation array. The pvclust R package is used to estimate the significance of the clusters based on multiscale bootstrap resampling ([link](https://academic.oup.com/bioinformatics/article/22/12/1540/207339)). It provides heatmap and dendogram plots and a list of potentially duplicated samples (identical SNPs) or related samples to be considered for removal in the Recommended_Samples_to_Remove file (Cluster variable). It is recommended researchers carefully check whether these are intentional or unintentional replicates. 

#### 2.5.	(Maternal) contaminated samples
Detection of contaminated samples, either with maternal DNA or with DNA from another participant in the study, is based on the method implemented in the ewastools R package ([link](https://clinicalepigeneticsjournal.biomedcentral.com/articles/10.1186/s13148-018-0504-1)), which uses the SNPs in the methylation array. Genotypes of contaminated samples deviate from the ideal trimodal distribution, and this deviation is calculated by ewastools and represented as an average log2 odds. The package suggests that samples with an average log2 odds > -4 are potentially contaminated samples and flagged to be removed in the Recommended_Samples_to_Remove file (Contamination and Meanlog2oddsContamination variables). However, we recommend looking at the heatmap and apply a more relaxed threshold which represents extreme outliers in your study (for instance > - 0.1). In addition, Meanlog2oddsContamination can be used as a confounder in the models.

### 3.	Probe QC

#### 3.1.	Probe call rate
To identify probes not different from the background, the researcher can select one of the two approaches:
-	The detection p-value calculated by the SeSAMe R package, which uses out-of-band array hybridization to calculate the detection p-values ([link](https://academic.oup.com/nar/article/46/20/e123/5061974)).
-	The detection p-value calculated by the ewastools R package, which restricts to probes for which no high concentration of the opposite fluorophore is to be expected to estimate the background distribution ([link](https://clinicalepigeneticsjournal.biomedcentral.com/articles/10.1186/s13148-019-0615-3)).
If FilterZeroIntensities is TRUE, intensity values less than 1 are also masked. If minNbeads is greater than zero, methylation values based on less than the specified number of beads are also masked. Then, probes with a call rate <95% are removed.

#### 3.2.	Problematic probes
Problematic probes (probes with SNPs, probes in sex chromosomes, probes that cross-hybridize) are not removed during the QC, thus the researcher should remove them during the EWAS, if desired.

<p>&nbsp;</p>

## preprocessingofData

### 4.	Normalization of the methylation data

#### 4.1.	Dye-bias and background correction
Dye-bias correction by linear scaling based on normalization control probes on both color channels (minfi approach) ([link](https://academic.oup.com/bioinformatics/article/33/4/558/2666344)), followed by Noob (normal-exponential using out-of-band probes) background correction ([link](https://academic.oup.com/nar/article/41/7/e90/1070878)).

#### 4.2.	Functional normalization ([link](https://genomebiology.biomedcentral.com/articles/10.1186/s13059-014-0503-2))

#### 4.3.	Beta-mixture quantile (BMIQ) normalization method to correct for the bias of type-2 probe values ([link](https://academic.oup.com/bioinformatics/article/29/2/189/204142)).

### 5.	Technical batch control

#### 5.1.	Principal components
In order to decide the most relevant technical variable, it tests the association of technical batch variables and other key variables with top principal components.

#### 5.2.	Technical batch control
It applies ComBat to control the effect of the variable indicated by the researcher ([link](https://academic.oup.com/biostatistics/article/8/1/118/252073)). Then, in order to confirm that technical batch has been eliminated, it performs the association between technical batch variables and principal components calculated with the new methylation dataset.

### 6.	Estimation of placenta cellular composition
One of the two methods has to be selected. 

#### 6.1.	Reference-free method 
Reference-free cell composition estimation occurs after normalization of the methylation data and technical batch control, but without masking based on the detection p-value or correction of outliers.  Before applying the Ref-free method ([link](https://doi.org/10.1093/bioinformatics/btu029)), it removes probes in the sex chromosomes and problematic probes according to this article: [link](https://academic.oup.com/nar/article/45/4/e22/2290930)

  -	Probes with cross-hybridizing potential
  -	Probes containing SNPs (European MAF< 5%) 
  -	Probes in sexual chromosomes

It provides:

  -	K-choose: number of estimated cellular components
  -	Omega: values of these cellular components for each individual

#### 6.2.	Reference-based method
##### 6.2.1.	For the placenta
It is based on the placenta reference panel implemented in the planet R package ([link](https://bmcgenomics.biomedcentral.com/articles/10.1186/s12864-020-07186-6)) with cell composition projected using the constrained Houseman method. It provides a file named Omega, which contains the estimated cell type proportion for six placenta specific cell types. 

##### 6.2.2.	For cord blood or whole blood
It uses the IDOL optimized CpGs ([link](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/s12859-016-0943-7)) and updated reference sets ([link](https://genomebiology.biomedcentral.com/articles/10.1186/s13059-018-1448-7))

### 7.	Final files

It provides the following files:

  -	mset: A GenomicRatioSet subset to the samples and CpG loci among the matrix of processed beta-values. It contains the metadata provided by the researcher.
  -	processedBetas: Matrix of the processed beta-values
  -	Kchoose: A numeric value indicating the number of cell types selected by the Ref-Free method (if applied, if not it is empty)
  -	Omega: A matrix of cell composition estimates generated by the RefFree method or the Planet R package.

<p>&nbsp;</p>

## detectionMask

### 8.	Masks beta-values based on poor intensity values
This function masks the beta-values based on a specified detection p-value (default is 0.05) and/or poor intensity values based on the number of beads and/or intensity values of zero;. It provides the beta-value matrix after this masking.

<p>&nbsp;</p>

## outlierprocess

### 9.	Control of outliers
Different approaches can be applied to reduce the influence of outliers. This function will take an input beta-value matrix and output a beta-matrix with the approach applied. The two possible approaches are described below. Percentiles can be estimated based on the quantile function or the empirical beta-distribution. 

#### 9.1.	Trimming of outliers
It removes extreme outliers based on gaps that must be at least 3*IQR; the cutoff for the number of outliers in a group is either a maximum of 5 or 0.0025 of the total number of samples (whichever is larger). Detected outliers are recoded as NA.

#### 9.2.	Winzorizing of outliers
 Values are winzorized based on the specified percentile. 

