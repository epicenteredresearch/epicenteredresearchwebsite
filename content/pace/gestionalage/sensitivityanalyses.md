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

The last step is re-running the analysis among mothers without pregnancy complications or physician initiated delivery that might affect gestational age. As extra sensitivity analyses, we will ask you to run the models again in the following subsets of children:

1.	**Subset 1**: Mothers without pregnancy complications or physician initiated delivery that might affect gestational age. 
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
    + Caesarian section, if due to pregnancy complications. C-sections due to complications during labor are kept in.  
    
2.	**Subset 2**: All previous restrictions in only term births (>=37 weeks GA)

### Note: you only need to subset the phenofinal

You only need to subset the phenofinal data.frame to the samples in the given subset, the function will automatically subset the betafinal matrix and Omega matrix.

### Re-running final models

Re-run the models in Step 3 among the specified subset. Be sure to change the destination folder so that you do not overwrite prior results.