# Computational Best Practices

[TOC]

## Introduction:

One of the challenges of doing computational work is keeping track of everything you do. The 'lab notebook' that you are used to using for benchwork doesn't translate well to computational stuff. Instead, you need to learn how to reproducibly structure projects, filenames, analyses, et cetera so that you can follow the work you do over a long period of time. The following notes are suggested best practices for doing computational analysis in the lab. 

## The Standard:

__It is expected that your analysis be fully documented and reproducible, such that upon publication, 100% of your code for not only processing raw genomics data, but also each and every figure, graph, and comparison, be made available in a publicly accessible repository. Someone should be able to download your raw data, point the code to these files, and reproduce exactly your work.__

While this standard is admittedly higher than what we see from many labs out there, this is the standard that I expect us to live up to in our work. 

## Collecting Sample Metadata

I expect that you keep meticulous records of the relevant metadata for each sample subjected to genomic analysis. While the relevant variables differ between experiment type, at a minimum, we should know: 

- The date of sample collection/preparation
- The date of library preparation
- The concentration of the library
- The number of PCR amplification cycles
- Barcode(s)
- Genotype
- Developmental Stage

For single-embryo ATAC experiments, it is also good to note the ambient temperature during the period of observation/sample collection.

For quantitative imaging experiments, it is also expected that users will note any laser power measurements performed before/after imaging sessions. While temperature should be constant in the microscope room, try to note the ambient temperature as well. 

In certain cases, sample metadata can also come from the downstream analysis. For instance, single-embryo ATAC experiments allow us to both determine the zygotic genotype of the sample, as well as the sex of the embryo. These data can be added after the fact to any relevant metadata tables. 

Besides being important for aspects of downstream analyses, these metadata are necessary to have for submission of your data to repositories for publication.

## Organizing a Project

A first step to making predictable and reproducible analytical pipelines is to settle on a standard directory structure for a genomics experiment. Once we receive raw sequencing data (.fastq) from the machine, we often process it and forget about it because 99% of the downstream work is done solely on the processed data. However, when it comes time to publish, we need to submit our data to GEO/SRA. You need to know where these raw files are, because those are typically *the only* files we submit to the repository as a record of the experiment. To facilitate the organization of these files, I suggest that each sequencing experiment have designated its own parental directory. This directory contains at a minimum four subdirectories: `Raw_Data`, `Trimmed_Reads`, `Mapped_Reads`, `R_files`. 

`Raw_Data` is the location of your raw, demultiplexed fastQ files. 

`Trimmed_Reads` is the location of the quality/adapter trimmed output from `TrimGalore`. 

`Mapped_Reads` is the location of .bam-formatted files mapping to the Drosophila (or relevant species') genome. 

The content of these directories should not change very much. The raw data cannot change. Trimming is relatively straightforward and does not require 'going back to' the process to tweak conditions. Our mapping approach does not filter reads substantially (we do that in R when we import reads), therefore we preserve maximal information in our .bam files. (It may be the case that we could try different mapping approaches, however. For instance, how multi-mapping reads are handled could be a variable that we play with from time to time. In this case, subdirectories could be created within the `Mapped_Reads` directory that reflect the different approaches taken.)

The rest of the directories represent "Processed data"

`R_files` is the location of analysis scripts.

It is possible that additional subdirectories may make sense to make for specific types of Processed data files. For instance, if peaks are to be called, a `MACS` subdirectory would be appropriate. If motif are to be called, a `MEME` subdirectory would be good. There is no restriction on how you organize this, but 1) it should be obvious to you what these things are, and 2) nothing important should be kept outside of the parental directory. 

## File Naming Conventions for Processed Data

All of your processed data files, be they analysis scripts/markdowns, peak lists, tables, graphs, whatever, should abide by the following naming convention:

__DateSerialNumber_Descriptive_Title.ProperExtension__

So, if you made a script to do DESeq on the fifth of January, 2022, and it is in R markdown format, you would expect to name it:

`220105_My_DESeq2_attempt.Rmd`

The date serial number can be YYMMDD or it could be YYYYMMDD, but I don't see the point of adding a leading "20" given that few of us will make it to 2100, and by that point who knows if any of this information will be readable or useful to anyone. __The reason to lead with a date serial number that begins with the year is because this is the only way to get your dates to sort chronologically if a list of filenames are sorted numerically.__

Please use a descriptive name that future you (or future me) will be able to use to guess what the content of the file is. 

Please use the proper file type extension when you have the responsibility of specifying it (i.e., when it isn't specified by default). When saving `R` objects, you can indicate that it is an `R` object with the extension `.R`. If an object is in `GenomicRanges` format, `.gr` is also suitable.

## Where To Save Files? 

The lab has a network accessible server with space for everyone to store their data. __This is the primary storage location for all of your data.__ All storage resources on Quest are effectively temporary. No files should be kept on Quest for long-term storage. Please arrange to receive logon credentials for the lab server from Shelby and make a personal folder under the appropriate subdirectories (`HTSeq` or `Imaging`). 

When new data becomes available from a sequencing run that we do ourselves, the raw data will be placed in a folder on the lab server. You must transfer it to Quest in order to work with it there. There are a number of utilities for performing file transfers between local storage and Quest, beginning with Globus. A pricier, but nicer, option is to use Transmit. I believe that FileZilla is also a viable and perhaps still free option. 

Once processing of data on Quest is complete, it is the user's responsibility to transfer the files back to the lab server for long-term storage. 

## Backing Up Your Data

It is each lab member's responsibility to ensure their data are backed up regularly. The lab will purchase an external hard drive for the purpose of performing backups of your data if this is a solution you would like to pursue. Other options are to keep copies of your files on cloud-accessible storage services (e.g., Microsoft OneDrive, which we have access to for free via NU). Even better is to redundantly keep copies of raw data at least in both external HD and cloud based backup formats. 

## Organizing an Analysis

Once genomic reads are mapped, the majority of the work that is done with the data is done on R. Typical first steps, post-mapping, are to plot coverage of reads, to call peaks, and to call enriched motifs (but of course these things vary depending on the expeirment). The scripts used to import data into R and/or to call peaks/motifs should be saved and named with a clear and obvious name. 

It is harder to organize the random walk of an analysis such that the path taken is clear six months or six years down the line.

1) I strongly suggest that 100% of your R work is done in markdown (Rmarkdown/.Rmd) format. Markdowns should be saved in raw format as well as .html format to reflect the state with which the script functioned at the time of composition. This also serves as proof that the script works when run independently of the local working environment. Scripts that won't run are useless scripts. 
   
2) Use the file naming convention described above so that files can be organized in chronological order, in a worst case scenario.
   
3) Use the top area of a markdown to describe the analytical __aim__ of the markdown, as well as to provide some narrative as to how this relates to prior analyses. If a markdown/analysis derives heavily on a prior markdown/analysis, cite the filename in this preamble. This way, if you are trying to piece together what you did, this leaves you some clues. 
   
4) Please take the time to __be verbose__ and explain what each code chunk is meant to do. If something isn't working, describe what isn't working and steps you've taken to try to fix it. __State your conclusions at the end.__
   
5) If you have a script that you wrote, and want to tweak it to run differently __do not overwrite old scripts: instead `save-as` and update the date serial number and add a "version" number (e.g., ".v2") to the filename.__

6) All scripts should be able to run on their own. Use best practices for composing code so that you know you haven't ambiguously defined a variable or over-written it. Coding should be linear. Don't go back to the middle of a script and branch it off by working on it anew from the middle. If you need to do this, `save as` and edit appropriately. 
   
7) Sometimes it becomes useful to save large intermediate files to shorten the time it takes to do an analysis. For instance, you might want to save the .wig formatted coverage data for a bunch of ChIP-seq experiments without needing to recalculate coverage each time you want to visualize it. When you do this, save the script that generates these files (consider having a standalone script that generates the files as well). Be sure to use the same date serial number on both the intermediate file *and* the script that generates it so that you can determine how you made the intermediate file. Sometimes we make choices in generating these files that we want to revisit later on. It is a shame when we can't figure out how these things were made.
   
8) For anything that uses random number generation (or that you suspect uses random number generation), you must precede the code with `set.seed(XXX)` where XXX can be any positive integer of your choice. 
   
9)  The first chunk of any markdown should include:
```
rm(list = ls())
```
   
10) The last chunk of any markdown should be:
```
sessionInfo()
```

!!! important
    The `sessionInfo` will report all of the versions of packages you were using at the time of an analysis. It is not uncommon for different versions to behave differently. In the interest of reproducibility, this would allow a user unable to reproduce your analysis to load up all of the exact versions of your packages in an effort to combat these potential differences.


11) Once you have begun an analytic project or series of genomics experiments __DO NOT UPDATE ANY OF YOUR PACKAGES or R VERSION.__ Try to perform your analyses on the same computer, or try to set up different computers as closely as possible. There are fancy ways to create stable computing environments. If this becomes an issue for us, we will explore ways of building these resources.  