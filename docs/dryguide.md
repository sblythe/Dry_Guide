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
		
Apply to be added to the lab's partition/project/allocations (p31603 and b1182) [here](https://app.smartsheet.com/b/form/797775d810274db5889b5199c4260328).
Allocation manager: Shelby Blythe, `shelby.blythe@northwestern.edu`
		
Apply to be added to partition b1042 [here](https://app.smartsheet.com/b/form/f6e96bd561114be8a33dc778bc00b919).
Allocation manager: Janna Nugent, `janna.nugent@northwestern.edu`

Use the Quest Help email: `quest-help@northwestern.edu` if necessary to get help

!!! Note
    If any of these links are dead, please tell Shelby so he can update them, and search for "Quest access Northwestern" to find hopefully current, non-dead links.

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

The Blythe lab has access to one project, __p31603__. This is an older workspace and will eventually be migrated to __b1182__. This is where we have 5000 GB of total storage. It currently houses some repositories for mapping, as well as a virtual environment for running our preferred read trimmer.  

__b1042__ - The 'Genomics' Project has 200 Tb of space and 100 nodes associated with it. This space is shared with other labs and is designed for temporary use only. The space is available at `/projects/b1042/BlytheLab/`. By default, files are deleted after 30 days. One can submit jobs to `--partition=genomicsguestA` to use this partition, with a max job duration of 48hr. 

!!! Note
    Anyone who uses Quest should build your own folder under `/projects/b1182` with your name. You should only write and revise files under your project folder. You can read/copy data from elsewhere in __b1182__ (or __p31603__) but don't write/save any data to locations outside of your project folder. You could also store things on `/projects/b1042/BlytheLab`, but storage anywhere on __b1042__ is for 30 days only, so do not store anything here that you hope to maintain access to over a long period. We have limited space on __p31603__ and __b1182__. This is also not a long-term location for storage, but rather a site where you import raw data, and save the output of processing, before you download the data back to long-term storage on the lab server. 

### Checking available storage space on b1182

If you would like to check the available storage on any project, including b1182, the command `checkproject b1182` will report current storage usage, as well as provide other information about the allocation's status.

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

For instance, mapping a complete PE150 HiSeq4000 dataset (~400 M pairs of reads) from 12 pooled libraries can take around 21 hours if performed in series, allocating 12 cores and 8 GB of memory per core. In contrast, when run in parallel (12 cores/sample, 8 GB of memory per core), the timing can be considerably shorter. In this case, an individual sample can be processed in as little as 1.5 to 2 hours, but the whole job can take longer than 2 hours since the job scheduler may penalize you for requesting so many resources (in total: 12 x 12 cores, 144 x 8 GB of memory).

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
    When re-writing scripts to operate on an array, be mindful that all of your individual samples will be processed simultaneously, and if you write temporary files to an output directory, they need to have unique filenames. See the example script below for a way of dealing with this (in the calls to `samtools sort` and `picard CleanSam`). Similarly, scripts should be re-written to purge temporary files as soon as they are no longer needed, rather than just throwing them away at the end of the process. The reason for this is because required disk space for storage can balloon significantly when using temporary files, especially if all those temporary files are being generated simultaneously, instead of once per sample.

Running array scripts are best handled by dividing the task into two parts: a script that handles the dispensing of arrayed tasks, and a script that will be run by each of the individual arrays. 

For instance, the following script handles the setup of an array for trimming and mapping 12 ChIP-seq samples. Using one node, it sets aside 12 cpus per 12 tasks (144 total cpus). Each cpu will have at most 8 GB of memory. This memory value was arrived at empirically, as described in the following section. It turns out that this is not optimal, as we discuss further below. The scheduler names the jobs based on the task ID. Unlike typical jobs, each arrayed job will show up as an independent line in the job scheduler. Both for the job name, as well as for the output logs, it is good to include identifiers that distinguish between the different arrayed instances. 

```
#!/bin/bash
#SBATCH --account=b1042
#SBATCH --partition=genomicsguestA
#SBATCH -t 4:00:00          
#SBATCH --nodes=1
#SBATCH --cpus-per-task=12
# #SBATCH --mem=0                 ## For troubleshooting only
#SBATCH --mem-per-cpu=8G 
#SBATCH --array=0-11
#SBATCH --job-name="ArrayTest_\${SLURM_ARRAY_TASK_ID}"
#SBATCH --output=Logs/outlog.%A_%a
#SBATCH --error=Logs/screenlog.%A_%a
#SBATCH --mail-type=ALL
#SBATCH --mail-user=shelby.blythe@northwestern.edu

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

name_list=(/projects/b1182/Test/Raw_Data/*_R1_*) # the length of this list must equal the array length above.

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

# END 
```

The script above refers to a trimming and mapping script, `Paired_End_Trimming_and_Mapping_Single_Sample.sh`. It does the actual trimming and mapping, as follows:

```
#!/bin/bash

# this is a basic script for trimming and mapping paired end reads.
# It takes as input options:
# 1) for the base directory
# 2) for the filename of read 1
# 3) for the number of cores per task, as defined in slurm

# IT ASSUMES: that the raw data is in the base directory, and that
# the destination directories will be in the base directory as well.

# It uses 4G of memory per cpu, which is hard-coded. 

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

samtools sort -@ ${cores} -m 4G -T ${basedir}/${mapdir}/temp${shortname} \
-o ${basedir}/${mapdir}/temp${shortname}1.bam \
${basedir}/${mapdir}/${shortname}.mapped.bam

rm ${basedir}/${mapdir}/${shortname}.mapped.bam

java -jar /software/picard/2.21.4/bin/picard.jar CleanSam \
INPUT=${basedir}/${mapdir}/temp${shortname}1.bam \
OUTPUT=${basedir}/${mapdir}/temp${shortname}2.bam

rm ${basedir}/${mapdir}/temp${shortname}1.bam

java -jar /software/picard/2.21.4/bin/picard.jar MarkDuplicates \
INPUT=${basedir}/${mapdir}/temp${shortname}2.bam \
OUTPUT=${basedir}/${mapdir}/${shortname}.mapped.md.bam \
METRICS_FILE=${basedir}/${mapdir}/${shortname}.dup.metrics.txt

rm ${basedir}/${mapdir}/temp${shortname}2.bam

samtools flagstat ${basedir}/${mapdir}/${shortname}.mapped.md.bam > \
${basedir}/${mapdir}/${shortname}.mapped.md.bam.FLAGSTAT.txt

samtools index ${basedir}/${mapdir}/${shortname}.mapped.md.bam
```

The first script would be passed to `sbatch`, and so long as the second one is found at the path indicated in the first, it should operate on the designated files.

!!! Note
    The above script examples are meant to provide a starting point for developing your own approaches. The resource allocation was sufficient for mapping PE150 samples with between 30M and 50M pairs of reads. If you have fewer reads (or maybe shorter reads), this will be over-specified. Similarly, larger datasets may require additional resources (most likely more RAM). The section below suggests a way to figure out how much RAM to allocate to this kind of a request.

### Issues with running arrays

In setting this up, I've run into problems from time to time with memory allocations. When the script above is executed, it will likely set up 12 12-core tasks, with each core being allocated 8 GB of RAM. This is more resource heavy than when this is done in series. If not enough memory is allocated per core, then the task will fail with an "out of memory" error code. Sometimes you might see a more foreboding "Node Fail" error message. To troubleshoot this, the line:

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

These commands for monitoring SLURM jobs are described in more detail in the next section. In the scripts above, I eventually settled on 12 cores and 8 GB per core based on trial and error, seeking to have the shortest per-sample wall clock time (as reported by `seff`). However:

`seff` reports that in the end, we only use around 25% of the allocated total 96 GB of RAM (~25 GB). It turns out that we need 8 GB per core (even though we only use 4 GB per core in the `samtools sort` command) because the `picard MarkDuplicates` commands end up needing >6 GB per core to do their job. We are running `MarkDuplicates` with default parameters, so we could dive in and figure out the memory management options for this program, but I have not yet done that. If we specify 6 GB or less memory per core, some of the jobs fail because `picard` will require more than that to complete.

!!! Note
    Another issue that comes up is that asking for 12 x 12 cores plus 8 GB RAM per core may be seen as a 'big ask' depending on how busy Quest is. If each array takes a while to transition from "Pending" to "Running", then the time savings associated with the arrayed jobs is reduced. This script above works as well with 4 cores (instead of 12) per job, and I haven't had any issues with slurm delays when asking for this amount of resources. The trade off is that it could take 5-6 hours for the job to complete. The point here is that there are a number of ways to ask for resources, and if you observed significant delays in the job running, try reducing your ask for cores. 

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

## Current Approach for Routine Trimming, Mapping, and G-Ranging of Paired-End Illumina Data

We have a routine approach for -- using Quest -- taking FastQ files from a Paired-End sequencing experiment, trimming adapter sequences, mapping to the `dm6` genome assembly, marking duplicates, indexing, and converting to a genomic ranges object in R. Typically, we start with raw data being delivered to the lab server, in a subdirectory named `Raw_Data` within an experiment-specific subdirectory, within a specific User's `HTseq` subdirectory.

```
Volumes
    |__ BlytheLab_Files
        |__ HTseq
            |__ Shelby
                |__ My_Experiment
                    |__ Raw_Data
```

There may be additional directories within the `My_Experiment` directory corresponding to the containers in which the data is delivered. Take any raw data from directories downloaded from BaseSpace (for instance) that you hope to work with and consolidate it within the `Raw_Data` directory.

!!! Important
    The "My_Experiment" directory in the example above represents a particular sequencing run, for a specific experiment. Keep separate runs in separate directories. Do not modify the "Raw_Data" directory following the initial mapping. This includes adding data generated in other sequencing runs.

What I do, is I create an identical `My_Experiment` directory in the proper location in Quest `/projects/b1182/Shelby/My_Experiment` and I copy the `Raw_Data` folder from the server to this location.

The following scripts will take the raw data and yield BAM files and a GRangesList of a subset of the mapped reads. Importantly, they will impose this directory structure on Quest:

```
projects
    |__ b1182
        |__ Shelby
            |__ My_Experiment
                |__ Raw_Data
                |__ Logs
                |__ Trimmed_Reads
                |__ Mapped_Reads
                |__ GRanges
```

Once the scripts are complete, all but the original `Raw_Data` directory within `My_Experiment` should be copied back to the lab server to allow for work locally.

### Overview of the approach

We want to trim adapters, map, mark duplicates, and index each individual set of fastQ files. Once this is done, we want to generate a `GRangesList` object of the mapped reads that meet necessary criteria (do we want to exclude duplicates? what is the map-quality cutoff?). To do this, we use two separate "manager" scripts that set up a batch job on slurm. The first (Trimming and Mapping) uses an array to process in parallel all samples relatively quickly. The second (GRanges) Operates in a single thread to sequentially import a subset of each BAM file and convert it to GRanges format.

Both of the "manager" scripts call their own "operator" scripts that do the necessary work. The "manager" scripts are edited to meet the specific needs of an experimental dataset. The "operator" scripts should be unchanged from use-to-use, however, if a user requires an "operator" script to do something different than standard, a user can make a custom variant. For instance, our standard GRanges importing script does not distinguish sizes of reads in a paired-end dataset. A user could make a variant that only imports 'small' reads (e.g., for importing ATAC-seq datasets).

### Trimming and Mapping

The typical "manager" script for Trimming and Mapping is called: `Mapping_and_Trimming_manager.sh` and the master copy can be found in the top level of `b1182`. __Users should copy this file to their "My_Experiment" directory and make modifications there that fit the specifics of their experiment.__ The script is written in such a way that a user will be guided to set up appropriately their target directory, and specify slurm conditions that are appropriate for their particular analysis. 

`Mapping_and_Trimming_manager.sh`:

```
#!/bin/bash

# This script will run our standard trimming and mapping approach on 
# all samples in parallel. It is much faster than doing them in series.

### First,  Make a directory in your base directory called "Logs"

### Next: Change the following lines to fit your specific project: ###

### 1) Estimate the amount of processing time you will need. ###
### Four hours is good for a small project, twelve hours for a longer one ###

#SBATCH -t 4:00:00

### 2) How many samples are there? If 5, then set the array from 0-4 ###
### If 8, then set the array from 0-7... etc... ###

#SBATCH --array=0-31

### 3) Give your job a name. Replace the indicated text with your job name ###

#SBATCH --job-name="NG230622_\${SLURM_ARRAY_TASK_ID}"

### 4) Enter your e-mail address to get updates ###

#SBATCH --mail-user=shelby.blythe@northwestern.edu

### The following lines should be the same regardless of the specific job ###
### Save this file in the base directory, chmod it to be executable, and  ###
### run it. ###

#SBATCH --account=b1042
#SBATCH --partition=genomicsguestA
#SBATCH --nodes=1
#SBATCH --cpus-per-task=12
# #SBATCH --mem=0                 ## For troubleshooting only
#SBATCH --mem-per-cpu=8G 
#SBATCH --output=Logs/outlog.%A_%a
#SBATCH --error=Logs/screenlog.%A_%a
#SBATCH --mail-type=ALL

### Next, specify the base directory that contains the data:

basedir=/projects/b1182/Natalie/230622_Trl_Ez

### the script will make the following directories:

mkdir ${basedir}/Trimmed_Reads
mkdir ${basedir}/Mapped_Reads

### Finally, change the directory below to point to your raw data files.

name_list=(/projects/b1182/Natalie/230622_Trl_Ez/Raw_Data/*_R1_*) # the length of this list must equal the array length above.

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

# END 

```

This is an overall simple script that sets up an array with appropriate resources, and then calls a script (`Paired_End_Trimming_and_Mapping_Single_Sample.sh`) for each set of fastQ files. It passes the base directory, the input filename, and the number of available cores to the script in order for it to function properly.

The *actual* trimming and mapping happens in the abovementioned script. 

`Paired_End_Trimming_and_Mapping_Single_Sample.sh`:

```
#!/bin/bash

# this is a basic script for trimming and mapping paired end reads.
# It takes as input options:
# 1) for the base directory
# 2) for the filename of read 1
# 3) for the number of cores per task, as defined in slurm

# IT ASSUMES: that the raw data is in the base directory, and that
# the destination directories will be in the base directory as well.

# It uses 6G of memory per cpu, which is hard-coded. 

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
### index=/projects/p31603/Bowtie_Indices/dm6/dm6
index=/projects/b1182/Bowtie_Indices/dm6/dm6

### source /projects/p31603/TrimGaloreEnv/bin/activate
source /projects/b1182/TrimGaloreEnv/bin/activate

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

samtools sort -@ ${cores} -m 6G -T ${basedir}/${mapdir}/temp${shortname} \
-o ${basedir}/${mapdir}/temp${shortname}1.bam \
${basedir}/${mapdir}/${shortname}.mapped.bam

rm ${basedir}/${mapdir}/${shortname}.mapped.bam

java -jar /software/picard/2.21.4/bin/picard.jar CleanSam \
INPUT=${basedir}/${mapdir}/temp${shortname}1.bam \
OUTPUT=${basedir}/${mapdir}/temp${shortname}2.bam

rm ${basedir}/${mapdir}/temp${shortname}1.bam

java -jar /software/picard/2.21.4/bin/picard.jar MarkDuplicates \
INPUT=${basedir}/${mapdir}/temp${shortname}2.bam \
OUTPUT=${basedir}/${mapdir}/${shortname}.mapped.md.bam \
METRICS_FILE=${basedir}/${mapdir}/${shortname}.dup.metrics.txt

rm ${basedir}/${mapdir}/temp${shortname}2.bam

samtools flagstat ${basedir}/${mapdir}/${shortname}.mapped.md.bam > \
${basedir}/${mapdir}/${shortname}.mapped.md.bam.FLAGSTAT.txt

samtools index ${basedir}/${mapdir}/${shortname}.mapped.md.bam
```

This script will perform automated trimming of adapter sequences (output is saved to `Trimmed_Reads` directory). Then each of the trimmed read sets are mapped using `bowtie2` with fairly default parameters (the only specification is that reads mapping >2000 bp apart are discarded). Mapped reads and associated files are stored in the `Mapped_Reads` directory. These include: the bowtie mapping log, a duplicate-marked bam file of the mapped reads, a flagstat document, and the index.

### Making GRanges

The following script, `Bam_to_GRanges_Manager.sh` has been used to manage the conversion of all BAMs in a directory into a GRangesList object, stored in a new directory, `GRanges`. __As with the other manager script, the user would copy this into a base directory for an experiment, edit the script to suit the specific needs of the analysis, and then execute.__ 

`Bam_to_GRanges_Manager.sh`
```
#!/bin/bash
#SBATCH --account=b1042
#SBATCH --partition=genomicsguestA
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --time=04:00:00
#SBATCH --mem-per-cpu=64G
#SBATCH --job-name=BamToGR                 ## change this to job name
#SBATCH --output=Logs/BamToGR_outlog        ## will write files to the Logs dir.               
#SBATCH --error=Logs/BamToGR_screenlog

module load R/4.3.0

### enter the full path to mapped reads:
mapdir="/projects/b1182/Corinne/Bowman_eLife_K27/Mapped_Reads"

### enter the minimal mapQ score to import:
mapq="10"

### do you want to exclude duplicates? (TRUE or FALSE?)
ex="FALSE"

### change the directory below to your base working directory
cd /projects/b1182/Corinne/Bowman_eLife_K27

mkdir GRanges 

Rscript --vanilla \
/projects/b1182/ImportPairedEndv1.R \
$mapdir \
$mapq \
$ex


```

!!! Note
    Importing the data into a GRangesList object can be very memory intensive. The approach above is single-threaded and basically needs to have enough memory to encompass the entire set of .bam files. On the specified partition, up to 180 GB of memory can be requested. 64 GB should be sufficient for our routine sequencing runs.

!!! Note
    The script above uses R version 4.3.0. A user may wish to change this to match the version of R that they use in downstream applications.

This script sets up the slurm environment, handles user-specified options (where is the data, minimum map quality, and whether to exclude duplicates), and then passes this information to an operator function, `ImportPairedEndv1.R`. As before, the operator script could be edited for different needs, but it should not be overwritten.

`ImportPairedEndv1.R`
```
#!/usr/bin/env Rscript
# Script name: "ImportPairedEndv1.R"
# Import all paired-end BAMs in a directory, export GRangesList.
# Input Args:
####### 1) Directory containing the input data (default = "Mapped_Reads/")
####### 2) MapQuality Filter Value (default = 10)
####### 3) Exclude Duplicates? (T/F, default = FALSE)
#
# Output: Directory named GRanges, containing a compressed GRangesList object.
#
# Assumptions: your BAM filenames within the source directory end with the text "mapped.md.bam"
#
# This function does not distinguish reads by size, and only imports data on canonical chromosomes.

args = NULL
args = commandArgs(trailingOnly = TRUE)

if(length(args) == 0){
    args[1] = paste0(getwd(),"/Mapped_Reads")
    args[2] = 10
    args[3] = TRUE
}else if(length(args) < 3 & length(args) > 0){
    stop("Please either specify all options or leave all blank to use defaults.")
}

datadir = args[1]
mapQval = as.numeric(args[2])
if(as.logical(args[3])){
    dupchoice = FALSE
}else{ dupchoice = NA }

cat("ImportPairedEndv1.R\n")
cat(date())
cat("\n\n")
cat("Current working directory:\n")
cat(getwd())
cat("\nInput data directory:\n")
cat(datadir)
cat("\nSpecified Map Quality Cutoff:\n")
cat(mapQval)
cat("\nExclude Duplicates?\n")
cat(as.logical(args[3]))
cat("\n")


library(GenomicAlignments)
library(BSgenome.Dmelanogaster.UCSC.dm6)

dm6 = Dmelanogaster
good.uns = c('chrX','chr2L','chr2R','chr3L','chr3R','chr4')

params = ScanBamParam(
    flag = scanBamFlag(
        isPaired = TRUE,
        isProperPair = TRUE,
        isSecondaryAlignment = FALSE,
        isUnmappedQuery = FALSE,
        isDuplicate = dupchoice,
    ),
    mapqFilter = mapQval
)

files = list.files(
    datadir,
    pattern = ".mapped.md.bam$",
    full.names = TRUE
)

cat("\n")
cat("Input Data Files:\n")
cat(basename(files), sep = "\n")
cat("\n")
cat("Processed: ", sep = "\n")

gr = GRangesList(lapply(files, function(x){
    cat(x, sep = "\n")
    r = readGAlignmentPairs(
            file = BamFile(x),
            param = params
    )

    names(r) = NULL

    r = r[seqnames(r) %in% good.uns]

    seqlevels(r) = seqlevels(dm6)
    seqinfo(r) = seqinfo(dm6)
    seqlevels(r) = seqlevelsInUse(r)
    genome(r) = 'dm6'

    return(r)
}))

names(gr) = gsub(x = basename(files), pattern = ".mapped.md.bam$", replacement = "")

saveRDS(gr, file = paste0(
    getwd(),
    "/GRanges/MappedReadsList.gr"
))

cat(date())
cat("\n\n\n")
sessionInfo()
```

This script will import over the canonical chromosomes the .bam files that are properly paired and non-secondary aligned. User specifies whether duplicates are excluded or not. The seqinfo and seqlevels are adjusted as necessary to reflect the reference genome (dm6). These are collected in a GRangesList object (named after their sample names) and saved in the `GRanges` directory with the generic filename `MappedReadsList.gr`. The script is verbose and prints to screen (and saved in the 'outlog') the filenames that are being processed as well as the session info for the operation.

### Running these scripts

1) Copy the two manager scripts above to the top level of your experiment's directory. 

2) Make and save the necessary changes in *your copies of these scripts* to suit your specific analysis needs. Double check that all file paths are properly specified. Do not change paths to the operator scripts unless you specifically intend to use a different operator script.

3) Make your copies of these scripts executable.

```
chmod u+x Mapping_and_Trimming_manager.sh
chmod u+x Bam_to_GRanges_Manager.sh
```

4) There are several ways to run these in series. They must be run in series, because the second manager script needs the first one to complete before running.

Option 1: run the first script. Wait for it to finish. Then run the second script.

```
cd "path to the top level of your experiment's directory"

sbatch Mapping_and_Trimming_manager.sh

#... wait ...

sbatch Bam_to_GRanges_Manager.sh
```

Option 2: use the `--dependency` option in slurm to let the first job complete before starting the second:

```
jobID=$(sbatch Mapping_and_Trimming_manager.sh)

sbatch --dependency=afterok:$jobID Bam_to_GRanges_Manager.sh
```

A call to `sbatch` returns the JobID to stdout, which in the above code, we assign to the variable `jobID`. Then, what happens is in the second call to `sbatch`, the `--dependency=afterok:$jobID` option means that the second command will not start until the job associated with the first JobID is completed with an "ok" status. 

The downside to this is, of course, that if problems are encountered in the first job, then the second job will never initiate. 