---
date: "2021-01-03T00:00:00Z"
draft: false
lastmod: "2021-01-03T00:00:00Z"
linktitle: Troubleshooting
menu:
  troubleshooting:
    name: Troubleshooting
    weight: 1
summary: Suggestions if you run into issues using the PACEanalysis package
title: Troubleshooting
toc: true
type: docs
weight: 1
---

### If you find you are no longer seeing results in your R console

Each of the PACEanalysis functions automatically create a log file to catch any warnings or errors encountered while running the function, which helps to troubleshoot any issues encountered. If the function fails to run completely (e.g. if it encounters an error), the log file will not be closed, and anything you run in R will be logged into that file instead of showing up in your R console. To close the open log file, run the below in your R console:

```{r eval=FALSE}

sink()
sink(type="message")

```

### Different Errors

#### Error in dataAnalysis(phenofinal = temppheno, betafinal = processedOut$processedBetas : One of the adjustment variables is not in the dataset

This means that one of the adjustment variables you specified in the dataAnalysis function adjustmentvariables argument is not in the phenofinal data.frame. Check the column names in the data.frame you specified for the phenofinal argument, are the adjustment variables in there?

```{r eval=FALSE}

tempadjustmentvariables<-c("Sex","Age","Parity","MaternalEd",
                                   "Smoke","preBMI","ModeDelivery","Ethnic")
colnames(temppheno)

## let's check which of the specified adjustment variables are not in 
## the data.frame we specified for our phenofinal argument
tempadjustmentvariables[!(tempadjustmentvariables %in% colnames(temppheno))]

```

#### Error in setwd(destinationfolder) : cannot change working directory

The specified destination folder for the function does not exist, make sure you create that folder first

#### If you encounter a different error

Please send the log file from the function to Alexandra (ambinder@hawaii.edu); she developed and maintains the PACEanalysis package and can help troubleshoot the issue.