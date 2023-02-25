# Setup of Blythe Lab Resources on Quest p31603

To run our standard analyses, we need to build resources. We like to use TrimGalore/cutadapt to trim adapters. We need bowtie (and HiSat) indices to map our reads to. This document just indicates what steps were taken to customize the lab's __p31603__ environment to do our analyses. 

!!! note

     It should not be necessary for any lab user to need to repeat any of these operations. This information is provided mainly as a record of what was done. Any future customization will be logged in this document.

## installing TrimGalore and Cutadapt 

TrimGalore and cutadapt run within a virtual environment that we need to set up. 

I checked that `virtualenv` was installed by running `which virtualenv`. I have it in my Quest environment, but it may be from a prior custom installation, and may not be included in new Quest accounts. 

If necessary to install virtualenv:

```
pip install virtualenv 
```

To install `TrimGalore`, I first built within `p31603` a python3 virtual environment using 

```
virtualenv -p python3 TrimGaloreEnv
```
Next, I changed to the new directory `TrimGaloreEnv` and activated the environment:

```
source bin/activate
```

I then installed `TrimGalore` using `git`: 

```
git clone https://github.com/FelixKrueger/TrimGalore.git
```

I then switched to the `TrimGalore` directory and copied the executable `trim_galore` to the `bin` directory of the virtual environment by entering 

```
cp trim_galore ../bin
```

We next need to install `cutadapt` and its dependencies. First, we need `cython`. To install it, we run

```
pip install cython
```

Next, we need `setuptools_scm`. This is also installed via `pip` in a manner similar to the line above.

We then install `cutadapt`:

```
python3 -m pip install --upgrade cutadapt
```

Finally, to allow for parallel processing of TrimGalore jobs, we need to install `pigz` (parallel gzip). 

```
git clone https://github.com/madler/pigz.git
```

We also have to switch to the pigz directory, 'make' it, and copy the
executable into the parental `bin` directory.

```
cd pigz
make
cp pigz ../bin
```

!!! note

    `TrimGalore` likes to use `FastQC` as well, but this is available as a Quest Module. (`module add fastqc/0.11.5`), so we do not need to install it.

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
## Downloading the Khost/Larracuente Repbase and Bowtie indexing.

This database is published in [Khost... Larracuente Genome Research 2017.](https://genome.cshlp.org/content/early/2017/04/03/gr.213512.116) 

The data is in supplemental file 9. 

(https://genome.cshlp.org/content/suppl/2017/04/03/gr.213512.116.DC1/Supplemental_file_S9.txt)

This was downloaded and placed on Quest. As above, the indices were built...
```
srun -A b1042 --partition=genomicsguestA -N 1 -n 24 --mem=64G --time=12:00:00 --pty bash -i
```

Then,

```
module add bowtie2
cd Bowtie_Indices
mkdir Dmel_Repbase
cd Dmel_Repbase
bowtie2-build --threads 16 ../../Genomes/Khost_Larracuente_2017_Repbase.txt dmRep
```

This goes super quick. All done.

# Downloading the Clogima albipunctata genome

I navigated to the DNAZoo website. The link under the Clogmia albipunctata genome for the current assembly (v6) seemed to be broken. I managed to find a download at:

(https://dnazoo.s3.wasabisys.com/index.html?prefix=Clogmia_albipunctata__clogmia.6/)

Date: 2/25/2023

This was manually downloaded and copied to the Genomes directory in p31603, filename `clogmia.6_HiC.fasta.gz`

# Bowtie index for C. alb

I set up an interactive session on b1042 as described above. Then:

```
module add bowtie2
cd Bowtie_Indices
mkdir Calb6
bowtie2-build --threads 16 ../../Genomes/clogmia.6_HiC.fasta.gz calb6
```


