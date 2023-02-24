# Blythe Dry Lab Guide

[TOC]

Many thanks to the Andersen Lab, whose own "Dry Guide" is the basis for this adaptation. 

The computing cluster available to researchers at Northwestern is called Quest. Please see the link below to go to the table of contents for Northwestern IT's support docs for Quest.

__[Quest Docs](http://www.it.northwestern.edu/research/user-services/quest/index.html)__

## Introduction/Overview

New users will likely have some confusion about how all of this works. This guide is meant to help you with that. As a high-level overview: we can use a remote computing cluster called Quest to run processor-intensive jobs without tying down our local computation resources. Quest is a collection of "Nodes" of many multithreaded processors, as well as disk storage space. You can think of Quest as a remote computer that you can interact with solely through the command line. The computational resources on Quest are parceled out into 'allocations'. The two most important allocations for our purposes are __b1042__ and __b1182__. __b1042__ is the "Genomics" buy-in allocation. Anyone at NU can sign up for access to __b1042__, and once they have access, they can begin submitting jobs to this system. __b1182__ is the Blythe Lab's personal allocation, with (at the moment) 4 TB of storage space. Jobs for __b1042__ can be stored in directories within __b1182__.

When you log in to Quest, however, you aren't 'within' __b1042__ or __b1182__, instead, when you log in, you are dropped off in your home directory (in terms of the file-system), and are using (in terms of computational resources) what is referred to as a __login node__. While __b1042__ has many cores and much memory available for computational processes, __login nodes__ have relatively less computational power. Therefore, if we have processor-instensive jobs to perform, we don't want to use a __login node__, instead we want to use __b1042__. To do this, we have to ask Quest to do our jobs on __b1042__ through one of two methods: as an 'interactive session' or as a 'batch submission'. Below, I describe how to initiate both of these approaches. 

Storage is another matter: each user's home directory has a limited amount of *private* storage that will be indefinitely maintained (and backed up). This is on the order of 80 Gigabytes, which isn't much. There are three other storage options: within the *Scratch Space* within __b1042__, within the lab's allocation in __b1182__, as well as within the lab's own dedicated project, __p31603__. This latter project is an older allocation that may eventually be migrated to __b1182__. *Scratch Space* is essentially a wild-west first-come-first-served disk space for *all* users of __b1042__ to *temoprarily* store files that they are using in a project. It is vast (~200 Terabytes), not backed up, and files over 30-days in age are deleted regularly. The Blythe Lab has a directory within __b1042__ created within this *Scratch Space* that lab members can use to temporarily store files if necessary. 

The Blythe Lab has a dedicated "project" within Quest (__p31603__)that affords us 500 Gigabytes of storage space. As mentioned above, this is an older allocation and may eventually be migrated to __b1182__. For now, all projects should be staged on __b1182__, although they may make reference to resources currently housed in __p31603__.

In the following, I describe how to get access to Quest, how to use some of its unique features, and how to submit jobs. 

## New Users

To gain access to Quest: 

Register new user with your NetID [here](https://app.smartsheet.com/b/form?EQBCT=9b3647a8cb2145929737ab4a0540cb46).
		
Apply to be added to the lab's partition/project/allocations (p31603 and b1182) [here](https://app.smartsheet.com/b/form/797775d810274db5889b5199c4260328).
Allocation manager: Shelby Blythe, `shelby.blythe@northwestern.edu`
		
Apply to be added to partition b1042 [here](https://app.smartsheet.com/b/form/797775d810274db5889b5199c4260328).
Allocation manager: Janna Nugent, `janna.nugent@northwestern.edu`

Use the Quest Help email: `quest-help@northwestern.edu` if necessary to get help

## Using Terminal or Something Fancier

Mac users can access Quest using Terminal. If you haven't used Terminal, search for it (`command + Space`, then enter "Terminal"). It is possible, however, to configure text editors to access Quest directly. This way, you can use the text editor to directly modify scripts without having to rely on Command Line text editors (like `nano`). I particularly like [Visual Studio Code (VSCode)](https://code.visualstudio.com/). VSCode runs on both Mac and Windows machines.

You can then configure VSCode to connect to the ssh host (Quest) by following [these instructions](https://code.visualstudio.com/docs/remote/ssh).

## Using the Command Line and `bash`

In general, you will be using the command line and `bash` when you use Quest. There are many available resources to learn the basics (Google it). To get started, Northwestern has collected some tutorials [here](https://sites.northwestern.edu/researchcomputing/2020/03/20/online-learning-resources-command-line/).

## Signing into Quest

Once you have been granted access to the cluster and have sorted out how you will connect, you can login using:

```
ssh <netid>@quest.northwestern.edu
```

If you are using Terminal, to avoid typing in the password everytime, one can [set up a ssh key](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-2).

You can set an alias in your `~/.bash_profile` to make logging in quicker:

```
alias quest="ssh <netid>@quest.it.northwestern.edu"
```

The above line makes it so you simply type `quest` and the login process is initiated. 

If you are not familiar with what a bash profile is, [take a look at this](https://www.quora.com/What-is-bash_profile-and-what-is-its-use).

!!! Important
    When you login it is important to be conscientious of the fact that you are on a login node. You should not be running any sort of heavy duty operation on these nodes. Instead, any analysis you perform should be submitted as a job or run using an interactive job (see below).

## Login Nodes

There are four login nodes we use: quser21-24. When you login you will be assigned to a random login node. You can switch login nodes by typing ssh and the node desired (_e.g._ `ssh quser21`).

You can easily determine your current login node by reading the command prompt, which takes the form: `<netID>@quser21`, if --for instance-- you are on the quser21 login node.

## Home Directory

Logging in places you in your home directory. You can install software in your home directory for use when you run jobs, or for small files/analysis.

Your home directory has a quota of 80 Gb. [More information on quotas, storage, etc](http://www.it.northwestern.edu/research/user-services/quest/file-systems.html).  You can check your storage space using the following command (from within your home directory):

```
du -hs *
```

This command `du` will estimate the **d**isk **u**sage of the current directory. `-hs` returns the **s**ummary of **h**uman-readable (i.e., not hidden) files.

From anywhere, you can determine the disk usage of your home directory using 

```
homedu
```

More information is provided below to help install and use software.

!!! Note
    In general, we will not be saving files long-term anywhere on Quest! Most of your work will take place within the lab's allocation __b1182__. All files should be stored on the lab's network accessible storage device/server. 

## Projects

Quest is broadly organized into projects (or partitions, or allocations). Projects have associated with them storage, nodes, and users.

To determine what "groups" you have access to, enter `groups` on the command line. You should see at the very least `b1042`, `b1182`, and `p31603` in the returned text. 

The Blythe lab has access to one specialized allocation, __b1182__. This is where we have 4 TB of total storage. This is the current location to perform Quest based tasks. Please keep in mind that while this is nice storage, any important products of an analysis should be backed up to the lab server. *No one but you is responsible for ensuring that your data is properly backed up!*

The Blythe lab has access to one project, __p31603__. This is an older workspace and will eventually be migrated to __b1182__. This is where we have 500 GB of total storage. It currently houses some repositories for mapping, as well as a virtual environment for running our preferred read trimmer.  

__b1042__ - The 'Genomics' Project has 200 Tb of space and 100 nodes associated with it. This space is shared with other labs and is designed for temporary use only. The space is available at `/projects/b1042/BlytheLab/`. By default, files are deleted after 30 days. One can submit jobs to `--partition=genomicsguestA` to use this partition, with a max job duration of 48hr. 

!!! Note
    Anyone who uses Quest should build your own folder under `/projects/b1182` with your name. You should only write and revise files under your project folder. You can read/copy data from elsewhere in __b1182__ (or __p31603__) but don't write/save any data to locations outside of your project folder. You could also store things on `/projects/b1042/BlytheLab`, but storage anywhere on __b1042__ is for 30 days only, so do not store anything here that you hope to maintain access to over a long period. We have limited space on __p31603__ and __b1182__. This is also not a long-term location for storage, but rather a site where you import raw data, and save the output of processing, before you download the data back to long-term storage on the lab server. 

## Running interactive jobs on Quest

If you are running a few simple commands or want to experiment with files directly you can start an interactive session on Quest. The command below will give you access to a node where you can run your commands. 

```
srun -A b1042 \
--partition=genomicsguestA \
-N 1 \
-n 24 \
--mem=64G \
--time=12:00:00 \
--pty bash \
-i 
```

!!! Important
    Do not run commands for big data on `quser21-24`. These are login nodes and are not meant for running heavy-load workflows. Using `srun` allows you to access (interactively) the correct nodes for your work. Another option (below) is to schedule a batch job. 
   
## Using packages already installed on Quest

Quest has a collection of packages installed. You can run `module avail` to see what packages are currently available on Quest. You can use `module load bowtie2` or `module load bowtie2/2.4.1` to load packages with the default or a specific version (`module add` does the same thing). If you do `echo $PATH` before and after loading modules, you can see what module does is simply appending paths to the packages into your `$PATH` so the packages can be found in your environment.

`module purge` will remove all loaded packages and can be helpful to try when troubleshooting.

## Submitting jobs to Quest

If you are all set up to use the lab's resources on Quest, you should be able to run the following test script to confirm that everything is operating as expected. The lab's project allocation contains a directory (.../TestData) that we use to check that everything is configured properly. 

Jobs on Quest are managed by [SLURM](https://slurm.schedmd.com/). Scripts for SLURM submissions differ slightly from regular bash scripts in that they have an extensive header that passes commands to SLURM that helps it determine how many resources to allocate to the job. Please see the `/projects/p31603/Quest_Setup_Test_Script_PE.sh` for an example of a script that is set up to run via the batch scheduler. A set of brief instructions on how to run the test script is included on a later page in this guide.

## NU Resources for learning about Quest:

There are useful guides provided by NU Research Computing on how to use Quest. Please refer [to this guide](https://services.northwestern.edu/TDClient/30/Portal/KB/ArticleDet?ID=1964) for general inquiries on use of this resource.

## Deciding How Much Resources to Ask For

This is tricky to guess. In general, you want to ask for about 2x more resources than your job actually takes. On the job scheduler, you need to request an amount of cluster up-time, a number of cores/cpus, and an amount of memory per cpu.

__The trade-off__ is that the more resources you ask for, the longer it takes for the scheduler to start your job. On the other hand, if you don't ask for sufficient resources, the process will terminate if you run out of time, or perhaps throw an error if memory resources are exceeded. 

!!! Note
    All of our jobs are only performed on a single _node_. The line on the job scheduler for nodes should read: `#SBATCH --nodes=1`

Once you have an idea of how your jobs run, you can adjust the following as necessary. In a recent run, tasks like trimming a set of paired-end reads (~20M reads/mate pair/sample) took around 10 minutes per sample. Mapping with bowtie2 (plus samtools and picard) took an additional 30 minutes. Therefore, if you have 12 samples of this type, a trimming and mapping script would take at least 8 hours of clock time. Pad this. Ask for 10 hours of compute time, the appropriate number of cores (8?), and sufficient memory (8 GB).

```
#SBATCH --ntasks-per-node=8
#SBATCH --time=10:00:00
#SBATCH --mem-per-cpu=8G
```

As mentioned above, this could take some time to schedule, but it should be sufficient to complete the job.

## Running jobs in parallel, rather than in series

A typical task we perform on Quest is to trim and map Illumina reads. We will have anywhere from 1 to >12 samples that we need to perform this on, and we essentially do the same thing to each sample. An old-timey way to do this is to write for-loops in our scripts and loop over the input filenames for each stage of the procedure. This "in series" approach works, but it doesn't take advantage of the fact that we can start all of the tasks at the same time in parallel for each input file. Running samples in parallel will accomplish the task of trimming and mapping in a fraction of the time compared with running them in series, typically because we end up reserving more resources in these cases for each individual task.

For instance, mapping a complete PE150 HiSeq4000 dataset (~400 M pairs of reads) from 12 pooled libraries can take around 21 hours if performed in series, allocating 12 cores and 8 GB of memory per core. In contrast, when run in parallel (4 cores/sample, 8 GB of memory per core), the timing can be considerably shorter (3-4 hours). 

Parallel processing is accomplished on SLURM by using the `array` option, and by carefully setting up a batch request to allocate all of the necessary resources. 

Within the header for a `SBATCH` script, one can specify the total number of jobs to perform in parallel using the `array` option. The batch job then sort of operates like a for-loop, in that we can take each starting file, and perform a task on it, by addressing it by an index number in a list. Say we have 12 samples in an experiment that we want to process in parallel. Then, you specify this by using a (zero-based) array:

```
...
#SBATCH --array=0-11
...
```

What this will do is set up 12 processes to run in parallel, each with a `SLURM_ARRAY_TASK_ID` from 0 to 11. Then, if you make a list of file names (in this example, housed within a directory "Test"):

```
# assign to the object `name_list` the filenames in the Raw_Data directory
# that contain the characters "_R1_". 

name_list=(/projects/b1182/Test/Raw_Data/*_R1_*)
```

Then, assign an input filename that will be specific to the specific array that lanuches. Use the index position of the arrays (reported as an environment variable) to select one of the list elements:

```
file=${name_list[$SLURM_ARRAY_TASK_ID]}
```

Such an array script will initiate N independent instances of your script, each with a single array task id value. The environment variable `SLURM_ARRAY_TASK_ID` will report that single value, and subset the name list, so that particular instance of the script will only operate on that single indexed file.

!!! Important
        When re-writing scripts to operate on an array, be mindful that all of your individual samples will be processed simultaneously, and if you write temporary files to an output directory, they need to have unique filenames. See the example script below for a way of dealing with this (in the calls to `samtools sort` and `picard CleanSam`).

Running array scripts are best handled by dividing the task into two parts: a script that handles the dispensing of arrayed tasks, and a script that will be run by each of the individual arrays. 

For instance, the following script handles the setup of an array for trimming and mapping 12 ChIP-seq samples. Using one node, it sets aside 4 cpus per 12 tasks (48 total cpus). Each cpu will have at most 8 GB of memory. This memory value was arrived at empirically, as described in the following section. It names the jobs based on the task ID. Unlike typical jobs, each arrayed job will show up as an independent line in the job scheduler. Both for the job name, as well as for the output logs, it is good to include identifiers that distinguish between the different arrayed instances. 

```
#!/bin/bash
#SBATCH --account=b1042
#SBATCH --partition=genomicsguestA
#SBATCH -t 8:00:00          
#SBATCH --nodes=1
#SBATCH --cpus-per-task=4
#SBATCH --mem-per-cpu=8G
#SBATCH --array=0-11
#SBATCH --job-name="ArrayTest_\${SLURM_ARRAY_TASK_ID}"
#SBATCH --output=Logs/outlog.%A_%a
#SBATCH --error=Logs/screenlog.%A_%a

# This script will run our standard trimming and mapping approach on 
# all samples in parallel. It is much faster than doing them in series. 
# 1) ensure that the 'array' parameter above is the size of the number
# of samples in the experiment. Start with zero. 
# 2) change the 'basedir' and the path to the filenames ("name_list" definition)
# below.
# 3) Ensure that you've made a directory (Logs) in the base directory.
#
# You should be good to go. It refers to a separate script to run the 
# trimming and mapping. This script just passes the files to the script.

basedir=/projects/b1182/Test

## make destinations
mkdir ${basedir}/Trimmed_Reads
mkdir ${basedir}/Mapped_Reads

name_list=(/projects/b1182/Test/Raw_Data/*_R1_*) 
# the length of this list must equal the array length above.

input=${name_list[$SLURM_ARRAY_TASK_ID]}

# We've packed the usual script into a new script that is designed to take
# single filename inputs (rather than looping). It has three input values:
# 1) the base directory
# 2) the value of "input", defined above
# 3) the number of cores, defined above

script_path="/projects/b1182/Paired_End_Trimming_and_Mapping_Single_Sample.sh"

. "$script_path" \
${basedir} \
${input} \
${SLURM_CPUS_PER_TASK}
```

It refers to a trimming and mapping script, `Paired_End_Trimming_and_Mapping_Single_Sample.sh`:

```
#!/bin/bash

# this is a basic script for trimming and mapping paired end reads.
# It takes as input options:
# 1) for the base directory
# 2) for the filename of read 1
# 3) for the number of cores per task, as defined in slurm

# IT ASSUMES: that the raw data is in the base directory, and that
# the destination directories will be in the base directory as well.

# It uses 2G of memory per cpu, which is hard-coded. 

basedir=$1
filename=$(basename $2)
cores=$3

echo "Base directory: $basedir";
echo "Read1 Filename: $filename";

module load python/3.8.4
module load fastqc/0.11.5
module load bowtie2/2.4.1
module load samtools/1.10.1
module load picard/2.21.4

rawdir=${basedir}/Raw_Data
trimdir=Trimmed_Reads
mapdir=Mapped_Reads
index=/projects/p31603/Bowtie_Indices/dm6/dm6

source /projects/p31603/TrimGaloreEnv/bin/activate

echo "path to the first read"
echo ${rawdir}/${filename}
filename2=$(echo $filename | sed "s/_R1_/_R2_/g")
echo "path to the second read"
echo ${rawdir}/${filename2}
echo

trim_galore \
-o ${basedir}/${trimdir} \
--gzip \
--fastqc \
--cores ${cores} \
--paired ${rawdir}/${filename} ${rawdir}/${filename2}

deactivate

tfilename=$(echo $filename | sed "s/.fastq.gz/_val_1.fq.gz/g")
tfilename2=$(echo $tfilename | sed "s/_val_1/_val_2/g")
tfilename2=$(echo $tfilename2 | sed "s/_R1_/_R2_/g")

echo "Next we use Bowtie2 on the output of Trimgalore"
echo ${basedir}/${trimdir}/${tfilename}
echo ${basedir}/${trimdir}/${tfilename2}

shortname=$(echo $tfilename |cut -d_ -f1)
echo
echo "The output for this step will have a filename that contains"
echo ${shortname}
echo

bowtie2 \
-p ${cores} \
-X 2000 \
-x $index \
-1 ${basedir}/${trimdir}/${tfilename} \
-2 ${basedir}/${trimdir}/${tfilename2} \
 2> ${basedir}/${mapdir}/${shortname}.bowtie.mapping.log.txt | \
samtools view \
-bS - > ${basedir}/${mapdir}/${shortname}.mapped.bam

samtools sort -@ ${cores} -m 2G -T ${basedir}/${mapdir}/temp${shortname} \
-o ${basedir}/${mapdir}/temp${shortname}1.bam \
${basedir}/${mapdir}/${shortname}.mapped.bam

java -jar /software/picard/2.21.4/bin/picard.jar CleanSam \
INPUT=${basedir}/${mapdir}/temp${shortname}1.bam \
OUTPUT=${basedir}/${mapdir}/temp${shortname}2.bam

java -jar /software/picard/2.21.4/bin/picard.jar MarkDuplicates \
INPUT=${basedir}/${mapdir}/temp${shortname}2.bam \
OUTPUT=${basedir}/${mapdir}/${shortname}.mapped.md.bam \
METRICS_FILE=${basedir}/${mapdir}/${shortname}.dup.metrics.txt

rm ${basedir}/${mapdir}/temp${shortname}1.bam \
${basedir}/${mapdir}/temp${shortname}2.bam \
${basedir}/${mapdir}/${shortname}.mapped.bam

samtools flagstat ${basedir}/${mapdir}/${shortname}.mapped.md.bam > \
${basedir}/${mapdir}/${shortname}.mapped.md.bam.FLAGSTAT.txt

samtools index ${basedir}/${mapdir}/${shortname}.mapped.md.bam
```

The first script would be passed to `sbatch`, and so long as the second one is found at the path indicated in the first, it should operate on the designated files.

!!! Note
        The above script examples are meant to provide a starting point for developing your own approaches. The resource allocation was sufficient for mapping PE150 samples with between 30M and 50M pairs of reads. If you have fewer reads (or maybe shorter reads), this will be over-specified. Similarly, larger datasets may require additional resources (most likely more RAM). The section below suggests a way to figure out how much RAM to allocate to this kind of a request.

### Issues with running arrays

In setting this up, I've run into problems from time to time with memory allocations. When the script above is executed, it will likely set up 12 4-core tasks, with each core being allocated 8 GB of RAM. This is more resource heavy than when this is done in parallel. If not enough memory is allocated per core, then the task will fail with an "out of memory" error code. Sometimes you might see a more foreboding "Node Fail" error message. To troubleshoot this, the line:

```
#SBATCH --mem-per-cpu=8G
```

can be commented out and replaced with:

```
#SBATCH --mem=0
```

Specifying `--mem=0` will allocate the maximum available memory for the node to the task at hand, and you can then determine how much memory the task took to run by querying the effort needed to complete the job:

```
seff <job_ID>
```

Where `<job_ID>` is the identification of a particular job that you can see by asking:

```
sacct -X
```

These commands are described in more detail in the following section. Once you find the memory used for the job, assume that the memory indicated by `seff` is the total amount of memory used by the total number of cores dedicated to the job. You can then divide the memory amount by the number of cores, and add 25-50% for padding. This can then be the amount of memory you allocate for your jobs once you uncomment the original `--mem-per-cpu=` option (and delete the `--mem=0` line) in the troubleshooting script.

In the script above, for instance, it failed if I assigned 5 GB of memory per cpu (total = 20 GB). Following a re-run with `--mem=0`, I found that the samples required between 22 and 25 GB of memory total (5.5 to 6.25 GB per cpu). I therefore specified 8 GB per cpu in the final script and this provides sufficient headroom for the job to complete without error.

### Questions about performance

Running 12 jobs in series with 12 cores and 8 GB memory per core takes about 21 hours. 12 jobs in parallel with 4 cores per job and 8 GB memory per core takes about 3-4 hours. This is not a proportional decrease in runtime, and it is possible that conditions could be found to map reads on the order of 2 hours or less. Most likely the reason for the difference in performance is the difference in the number of cores assigned to the job. In addition, the timing comparison above (although the same amount of RAM is allocated to the jobs) has different hard-coded memory usage (4 GB for the series, 2 GB for parallel in calls to `samtools`). *At some point, we may find that we are asking for too much in the way of resources to get our jobs done quickly.* How fast do we really need the trimming and mapping to be? If the performance of these scripts needs to be faster for some reason, we could try adding more cores (try +2) per job. At the moment, I'm not sure how that affects the memory requirements, as having 6 cores/array would mean we could get by with ~6 GB of memory to reach the >32 GB value we decided on using above. This is an open question at the moment in terms of how much we can optimize within the constraints of using Quest.

## Monitoring SLURM Jobs on Quest

Here are some commmon commands used to interact with SLURM and check on job status. For more, check out [this page](https://slurm.schedmd.com/quickstart.html).

* `squeue -u <netid>` to check all jobs of a user.

* `scancel <jobid>` to cancel a job. `scancel  {30000..32000}` to cancel a range of jobs (job id between 30000 and 32000).

* `sinfo | grep genomicsguestA` to check status of nodes. `alloc` means all cores on a node are completely engaged; `mix` means some cores on a node are engaged; `idle` means all cores on a node are available to accept jobs.

* `seff <jobID>` to check how much '**eff**ort' a job took

* `sacct -X` to display all accounting data for all jobs for a user. `-X` options only shows stats relevant to the job allocation itself, leaving out different steps in the process. This is a fine way to check on the status of submitted jobs and to get the job id. 

## Merging FASTQ files from multiple lanes

If we do sequencing ourselves on a NextSeq or any Illumina machine that organizes flowcells into "Lanes", the data we receive is returned 'by lane', even though each sample is present in all lanes. In this case, it is a good idea to merge reads by lane.

The following bash script will do it, but it has been generalized so that you need to enter the proper information w/r/t directories. 

This script will change to the _parental_ directory containing the downloaded data, create a new directory "Raw_Data" where it will save the merged fastq.gz files, then change to the subdirectory that contains the downloaded (split by lane) data, and loop through the fastq files, merging them. 

!!! Note
     For this to work, you have to move each pair of reads (for paired-end data) out of the individual directory that Illumina provides it in, to a single common directory. To be super careful, you could do it yourself, by hand. But this would obviously become tedious with more than a handful of different barcoded samples. You can do this from the command line, by navigating to the directory that contains each of the individual directories, and then using the command `mv` to move the contents of each subdirectory _up_ one level. The command for this looks like `mv */* .`. Naturally, this is not a command that you want to mess up, or run in the wrong place because there is no 'undo' here. 

```
#!/bin/bash

# One possible source of error in running this code is that the directory 
# structures are not properly entered. This code assumes that you have 
# downloaded barcode-split NextSeq (or other multi-lane) data from BaseSpace.
# The download generates two directories, one with the barcode split data, 
# and then an "Unindexed Reads" directory. We do not concern ourselves with 
# the unindexed reads directory. The barcode split data will be named with 
# the experiment name from the sample sheet, plus a serial number of some sort. 
# Within *that* directory, there are individual folders for each sample. The 
# problem we hope to solve here is that each sample's read (forward and reverse)
# is split into four individual files representing reads that originated from
# clusters in one of the four lanes of the flowcell.

# One of these files will have the filename structure: 

# <sample_name_from_sheet>_S[number]_L00[1-4]_R[1-2]_001.fastq.gz

# The sample name from sheet is self-explanatory. 
# The S[number] is the sample number order from the sample sheet.
# The L00[1-4] tells you what lane the data came from.
# The R[1-2] identifies whether it is mate1 or mate2 data.
# I have no idea what the 001 is, but perhaps this records re-dos of the base calls.

# The fields are delimited with underscores.

# One potential area of error can arise if the user uses underscores in the 
# Sample names that are provided in the sample sheet. 

####

# This code assumes that you have first placed the downloaded directory into
# a parental directory for the experiment. It will 1) create a new directory
# within the parental directory for the output of the code. This directory will
# be named "Raw_Data". 2) It will go to the downloaded directory and yank the 
# per-lane fastQ files from each of the per-sample subdirectories and move them
# one level up to the parental directory. 3) It will identify all unique sample
# names and 4) go through each of them and concatenate them by lane. 5) Files will 
# be written to "Raw_Data" directory with the structure of:

# <Sample_name_from_sheet>_S[number]_R[1-2]_cat.fastq.gz

# where the L00[1-4] has been dropped and replaced with 'cat' to indicate the
# files have been concatenated.

# in the code below, please replace any text between <> with the data the text
# describes.

# AS WRITTEN, THE CODE WILL FAIL IF YOU HAVE UNDERSCORES IN YOUR SAMPLE NAMES
# Best policy is to avoid using them. However, I have found that GenerateFASTQ will
# automatically convert them to hyphens, which is nice. Not sure if it will always happen.

# If this remains an issue, one option is to alter the filenames of each file to drop
# the underscores, which isn't too hard if you know how to do it either on the
# command line, or even in R. Another alternative is to parse the command below 
# where the unique names are pulled out to accurately capture the first two fields
# of the filename as described above (<sample_name_from_sheet>_S[number]). This
# needs to be consistent, however, between all the samples...

# If in doubt, comment out the two commands beginning with `cat` in the for-loop
# below and run the code, checking the verbose output that tells you what sample
# name the code intends on working on. 

### BEGIN

# define the parental directory 
parentdir=<path/to/directory/above/your/downloaded/fastQ/data>

# define the directory containing the downloaded data
downloaddir=<directory name with the downloaded fastQ data>

# change to parental directory
cd $parentdir
# make a new destination for the output
mkdir Raw_Data

# change to the downloaded data directory
cd $parentdir/$downloaddir

# yank the data from subdirectories up one level
# NOTE: if you are re-running this code be aware that if you re-run this line
# it will move anything that has appeared in the subdirectories up to the parental
# downloaded directory. This shouldn't be a problem, but we find ways to make 
# new problems all the time. 
mv */*/* .

# get the unique sample names (<sample_name_from_sheet>) and sample 
# numbers (S[number]). Write these to a file in the downloaded directory.

ls -1 *R1*.gz | awk -F '_' '{print $1"_"$2}' | sort | uniq > ID

# loop through sample names in the file, concatenating by lane. Write output
# to the "Raw_Data" directory you created in the parental directory. 

for i in `cat ./ID`
do
    echo "Merging"
    echo $i
    echo

    cat $i\_L001\_R1_001.fastq.gz $i\_L002\_R1_001.fastq.gz $i\_L003\_R1_001.fastq.gz $i\_L004\_R1_001.fastq.gz > $parentdir/Raw_Data/$i\_R1_cat.fastq.gz

    cat $i\_L001\_R2_001.fastq.gz $i\_L002\_R2_001.fastq.gz $i\_L003\_R2_001.fastq.gz $i\_L004\_R2_001.fastq.gz > $parentdir/Raw_Data/$i\_R2_cat.fastq.gz

done
```

