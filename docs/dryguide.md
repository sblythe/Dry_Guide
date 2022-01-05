# Blythe Dry Lab Guide

[TOC]

Many thanks to the Andersen Lab, whose own "Dry Guide" is the basis for this adaptation. 

The computing cluster available to researchers at Northwestern is called Quest. Please see the link below to go to the table of contents for Northwestern IT's support docs for Quest.

__[Quest Docs](http://www.it.northwestern.edu/research/user-services/quest/index.html)__

## New Users

To gain access to Quest: 

Register new user with your NetID [here](https://app.smartsheet.com/b/form?EQBCT=9b3647a8cb2145929737ab4a0540cb46).
		
Apply to be added to the lab's partition/project/allocation (p31603) [here](https://app.smartsheet.com/b/form/797775d810274db5889b5199c4260328).
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
In general, we will not be saving files long-term anywhere on Quest! Most of your work will take place within the project directory __p31603__. All files should be stored on the lab's network accessible storage device/server. 

## Projects and Queues

Quest is broadly organized into projects (or partitions, or allocations). Projects have associated with them storage, nodes, and users.

To determine what "groups" you have access to, enter `groups` on the command line. You should see at the very least `b1042` and `p31603` in the returned text. 

The Blythe lab has access to one project, __p31603__. This is where we have 500 GB of total storage. This is where we will store any compiled software necessary for running our analyses. 

__b1042__ - The 'Genomics' Project has 200 Tb of space and 100 nodes associated with it. This space is shared with other labs and is designed for temporary use only. The space is available at `/projects/b1042/BlytheLab/`. By default, files are deleted after 30 days. One can submit jobs to `--partition=genomicsguestA` to use this partition, with a max job duration of 48hr. 

!!! Note
    Anyone who uses quest should build your own project folder under `/projects/b1042/projects/BlytheLab/` with your name. You should only write and revise files under your project folder. You can read/copy data from __b1042__ but don't write any data outside of your project folder. Storage on __b1042__ is for 30 days only, so do not store anything here that you hope to maintain access to over a long period. 

## Running interactive jobs on Quest

If you are running a few simple commands or want to experiment with files directly you can start an interactive session on Quest. The command below will give you access to a node where you can run your commands. 

```
srun -A b1042 --partition=genomicsguestA -N 1 -n 24 --mem=64G --time=12:00:00 --pty bash -i 
```

!!! Important
    Do not run commands for big data on `quser21-24`. These are login nodes and are not meant for running heavy-load workflows. Using `srun` allows you to access (interactively) the correct nodes for your work. Another option (below) is to schedule a batch job. 
   
## Using packages already installed on Quest

Quest has a collection of packages installed. You can run `module avail` to see what packages are currently available on Quest. You can use `module load bowtie2` or `module load bowtie2/2.4.1` to load packages with the default or a specific version (`module add` does the same thing). If you do `echo $PATH` before and after loading modules, you can see what module does is simply appending paths to the packages into your `$PATH` so the packages can be found in your environment.

`module purge` will remove all loaded packages and can be helpful to try when troubleshooting.

## Submitting jobs to Quest

If you are all set up to use the lab's resources on Quest, you should be able to run the following test script to confirm that everything is operating as expected. The lab's project allocation contains a directory (.../TestData) that we use to check that everything is configured properly. 

Jobs on Quest are managed by [SLURM](https://slurm.schedmd.com/). Below is a template to submit a job to SLURM. One can run it with `sbatch script.sh`.

Scripts for SLURM submissions differ slightly from regular bash scripts in that they have an extensive header that passes commands to SLURM that helps it determine how many resources to allocate to the job. Please see the `/projects/p31603/Quest_Setup_Test_Script_PE.sh` for an example of a script that is set up to run via the batch scheduler. 

## Monitoring SLURM Jobs on Quest

Here are some commmon commands used to interact with SLURM and check on job status. For more, check out [this page](https://slurm.schedmd.com/quickstart.html).

* `squeue -u <netid>` to check all jobs of a user.

* `scancel <jobid>` to cancel a job. `scancel  {30000..32000}` to cancel a range of jobs (job id between 30000 and 32000).

* `sinfo | grep genomicsguestA` to check status of nodes. `alloc` means all cores on a node are completely engaged; `mix` means some cores on a node are engaged; `idle` means all cores on a node are available to accept jobs.

* `seff <jobID>` to check how much '**eff**ort' a job took

* `sacct -X` to display all accounting data for all jobs for a user. `-X` options only shows stats relevant to the job allocation itself, leaving out different steps in the process. This is a fine way to check on the status of submitted jobs and to get the job id. 

# Checking your setup with a Test Script

To test that everything is setup properly, I have made a "test script" that can be edited appropriately according to the comments and then submitted to the batch scheduler to test that everything is running well in your local environment.

The basic test script is located at

```
/projects/p31603/Quest_Setup_Test_Script_PE.sh
```

To test your setup:

1) If you haven't done so, create a directory with your name at `projects/p31603/`

2) Make within your personal folder a directory for the test run (e.g., `TestSetup`)

3) Within your test-run directory, make a directory for the output of the test (e.g., `TestOutput`)

4) Now, copy the test script from the location above to your `TestSetup` directory. Use the `cp` command. 

5) Edit the **copy** of the script as indicated in the comments so that the filepaths are accurately rendered. It will not run without some edits from you.

6) The script on its own is also not yet executable. To make it executable, run the command (while in the directory that contains your copy of the test script):

```
chmod u+x Quest_Setup_Test_Script_PE.sh
```

This makes you (the **u**ser) able to e**x**ecute the file.

7) To run the script using the batch scheduler, run (from within the directory containing the newly executable copy of the script):

```
sbatch Quest_Setup_Test_Script_PE.sh
```

8) On an early test, it took about seven minutes for the test to run. You can check the status of the run using `sacct -X`. When it is done, the output of `sacct` will say "COMPLETED". To check how many resources the test used, you can run `seff <jobID>`, where the Job ID is the first column of the output from `sacct`. This is useful to know so that you can scale your demands for resources accordingly, particularly the memory, time, and number of cores. 

# Setup of Blythe Lab Resources on Quest p31603

To run our standard analyses, we need to build resources. We like to use TrimGalore/cutadapt to trim adapters. We need bowtie (and HiSat) indices to map our reads to. This document just indicates what steps were taken to customize the lab's __p31603__ environment to do our analyses. Note, it should not be necessary for any lab user to need to repeat any of these operations. This is provided mainly as a record of what was done. Any future customization will be logged in this document.

### installing TrimGalore, Cutadapt, 

I checked that `virtualenv` was installed by running `which virtualenv`. I have it, but it may be from a prior custom installation, and may not be included in new Quest accounts. To install virtualenv, try `pip install virtualenv`. 

To install `TrimGalore`, I first built within `p31603` a python3 virtual environment using `virtualenv -p python3 TrimGaloreEnv`

Next, I changed to the new directory `TrimGaloreEnv` and activated the environment `source bin/activate`.

I then installed `TrimGalore` using `git`: `git clone https://github.com/FelixKrueger/TrimGalore.git`

I switch to the `TrimGalore` directory and copied the executable `trim_galore` to the `bin` directory of the virtual environment by entering `cp trim_galore ../bin`.

We next need to install `cutadapt` and its dependencies. First, we need `cython`. To install it, we run `pip install cython`. Next, we need `setuptools_scm`. This is also installed via `pip`.

We then install `cutadapt` using `python3 -m pip install --upgrade cutadapt`. 

`TrimGalore` likes to use `FastQC` as well, but this is available as a Quest Module. (`module add fastqc/0.11.5`)

### downloading the Drosophila genome

I have made a folder called "Genomes" in the `p31603` directory. From within `Genomes`, I downloaded the fly genome using

```
wget --timestamping 'ftp://hgdownload.cse.ucsc.edu/goldenPath/dm6/bigZips/dm6.fa.gz'
```

### Building a bowtie2 index

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
