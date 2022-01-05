# Setup of Blythe Lab Resources on Quest p31603

To run our standard analyses, we need to build resources. We like to use TrimGalore/cutadapt to trim adapters. We need bowtie (and HiSat) indices to map our reads to. This document just indicates what steps were taken to customize the lab's __p31603__ environment to do our analyses. Note, it should not be necessary for any lab user to need to repeat any of these operations. This information is provided mainly as a record of what was done. Any future customization will be logged in this document.

## installing TrimGalore and Cutadapt 

I checked that `virtualenv` was installed by running `which virtualenv`. I have it, but it may be from a prior custom installation, and may not be included in new Quest accounts. To install virtualenv, try `pip install virtualenv`. 

To install `TrimGalore`, I first built within `p31603` a python3 virtual environment using `virtualenv -p python3 TrimGaloreEnv`

Next, I changed to the new directory `TrimGaloreEnv` and activated the environment `source bin/activate`.

I then installed `TrimGalore` using `git`: `git clone https://github.com/FelixKrueger/TrimGalore.git`

I switch to the `TrimGalore` directory and copied the executable `trim_galore` to the `bin` directory of the virtual environment by entering `cp trim_galore ../bin`.

We next need to install `cutadapt` and its dependencies. First, we need `cython`. To install it, we run `pip install cython`. Next, we need `setuptools_scm`. This is also installed via `pip`.

We then install `cutadapt` using `python3 -m pip install --upgrade cutadapt`. 

`TrimGalore` likes to use `FastQC` as well, but this is available as a Quest Module. (`module add fastqc/0.11.5`)

## downloading the Drosophila genome

I have made a folder called "Genomes" in the `p31603` directory. From within `Genomes`, I downloaded the fly genome using

```
wget --timestamping 'ftp://hgdownload.cse.ucsc.edu/goldenPath/dm6/bigZips/dm6.fa.gz'
```

## Building a bowtie2 index

Bowtie2 indices will be put in subdirectories within `p31603/Bowtie_Indices/`.

To build the indices, it is a good idea to start an interactive session on __b1042__. 

```
srun -A b1042 --partition=genomicsguestA -N 1 -n 24 --mem=64G --time=12:00:00 --pty bash -i
```

Then,

```
module add bowtie2
cd Bowtie_Indices
mkdir dm6
cd dm6
bowtie2-build --threads 16 ../../Genomes/dm6.fa.gz dm6
```
