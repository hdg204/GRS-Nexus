*Dr. Harry Green, University of Exeter*

***PLINK FILES MOVING AROUND HAS BROKEN THIS SCRIPT. FIX COMING TOMORROW***

# GRS-Nexus

## Installation and Background

This repository contains code to run genetic risk scores on the UK Biobank cohort through the R Studio Interface on DNA Nexus. To set up the function, first run

```
library(devtools)
source_url("https://raw.githubusercontent.com/hdg204/GRS-Nexus/main/initialise.R")
```

Which will create a new R function called generate_grs, as well as install plink, plink2, bgenix and qctools. It also downloads an example GRS file called Example_GRS

## Running generate_grs

generate_grs has one mandatory input, the GRS SNP file, and one optional input for quality control metrics (on by default).

If you simply want the GRS score output, run `GRS=generate_grs('Example_GRS',info=F)` as an example. This will create a file with the IDS, allele counts and genetic risk scores.

If you also want the info score and maf for each SNP (recommended) run `GRS=generate_grs('Example_GRS')`

Without the info scores, the function takes about 3-5 minutes on default DNA Nexus settings. It might seem like it's frozen sometimes - do not touch R Studio while the function is running. With the info scores, it can take up to 10 minutes but seems quite inconsistent in speed.

## Input files

These must be tab separated in the order chr, bp, other, effect, weight, where 'other' and 'effect' represent the reference and trait-raising alleles respectively.

## Technical Details

The script identifies base pair position matching the correct chromosome, uses bgenix to pull those out of the bgen file and qctool to output a bgen file. For each chromosome with SNPs that are found, a plink command merges the bgens into a plink binary file. Plink2 is used to rename the IDs so it can match the input file, and plink is used to create the score. The exact command that handles the calculation is

`./plink --bfile merged_dataset_2 --score plink_score 1 2 3 --out score`

Where plink_score is a file created earlier in step 2. The  output file is score, which is saved in `score.profile` then immediately read back into R.

## Terms of Use

If any of the contained code is used in any academic article, please acknowledge the authors (Harry Green and Bethan Mallabar-Rimmer) include a link to this github repository in the acknowledgements section, and please make **ALL** other code used in your article open source.

## Known Issues

There is no cleanup of intermediate files. If you run a GRS without all the chromosomes it will scoop up any files left behind for the info file. This doesn't affect the score.
