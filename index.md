By Hanrui Zhang on 08/30/2021


## Columbia HPC resources

Documentation for Habanero can be found [here](https://confluence.columbia.edu/confluence/display/rcs/Habanero+HPC+Cluster+User+Documentation). New users should read the documentation carefully. The resources are shared and often busy. The max walltime is 5 days (or 6 hours on the free partition).

## Connecting by SSH
#### Use SSH from a terminal to login
```
$ ssh <UNI>@habanero.rcs.columbia.edu
```

#### Transfer files from local computer to the cluster you can use `scp`, or download data directly on the cluster if it is hosted online.
```
# Transfer files or dirs from local computer to the scratch space
$ scp <path-to-file-or-dir> <UNI>@habaxfer.rcs.columbia.edu:
```

## Submit jobs to the cluster
Habanero uses the SLURM job submissiono system to manage shared resources on the cluster. When you login you are connected to the login node, which should not be used to run any intensive tasks. Instead, you need to submit your jobs using a job script to reserve resouces and send the joob to run on a compute node.

#### Sample job submission script
```
#!/bin/sh
#
# Simple "Hello World" submit script for Slurm.
#
# Replace <ACCOUNT> with your account name before submitting.
#
#SBATCH --account=free      # The account name for the job.
#SBATCH --job-name=HelloWorld    # The job name.
#SBATCH -c 1                     # The number of cpu cores to use.
#SBATCH --time=00:01:00              # total run time limit (HH:MM:SS).
#SBATCH --mem-per-cpu=1gb        # The memory the job will use per cpu core.
#SBATCH --mail-type=begin        # send email when job begins
#SBATCH --mail-type=end          # send email when job ends
#SBATCH --mail-user=<UNI>@cumc.columbia.edu

echo "Hello World"
sleep 10
date

# End of script
```

#### Check the job status
```
# Submit the job to the scheduling queue
$ sbatch helloworld.sh
# To view all jobs on the system and check whether the job has started yet
$ squeue 
# To view information about a particular job
$ scontrol show job [job ID]
# Once it starts check log files for the output
$ cat slurm-<joobid>.out
```
## Habanero-Storage
After logging in to Habanero you will be in your home directory. This storage space (20 GB, the quota, i.e. how much you are allowed to store there) is appropriate for smaller files, such as documents, source code, and scripts but will fill up quickly if used for data sets or other large files.

In principle, you can store up to 32 GB to /rigel/free.



## Interactive mode
To test the script, it is better to start an interactive session rather than submit a job. Your laptop should stay on and connected for interactive jobs. For batch jobs, you should be able to disconnect without any issues, and then come back later again to see where they stand in their processing.
 
```
# Ask for an interactive sessioon
$ srun --pty -A free /bin/bash
# Check the default time limit (6 hours), core (1) and memory (4,000 MB), e.g. for job ID 24633553
$ sacct -j 24633553 -o jobid,timelimit,ncpus,reqmem

# Set up to 4 cores and a 30 min session
$ srun --pty -t 30:00 -A free -n 4 /bin/bash 

# This partition can be useful if your wait time for regular interactive jobs is long and you're in a hurry.
$ srun -p test

# Set up an email notification when a node is assigned.
$ srun --pty -A free -t 0-00:01:00 --mail-type=BEGIN --mail-user=<UNI>@columbia.edu /bin/bash

```
## Install packages on Habanero
#### Check already installed packages
```
$ module avail
```
#### Install R packages on Habanero
HPC users can Install R packages locally in their home directory or group's scratch space (see below).   

```
$ module load R
$ R
```
You can see the default library paths (where R looks for packages) by calling .libPaths():    

```
> .libPaths()
[1] "/rigel/opt/R-4.1.0/lib64/R/library"
```
These paths are all read-only, and so you cannot install packages to them. To fix this, we will tell R to look in additional places for packages.

Exit R and create a directory rpackages in /rigel/<GROUP>/users/<UNI>/.
```
$ mkdir /rigel/<GROUP>/users/<UNI>/rpackages
```

Go back into R and add this path to .libPaths()  
     
```
$ R
> .libPaths("/rigel/<GROUP>/users/<UNI>/rpackages/")
# For example  
> .libPaths("/rigel/free/users/hz2418/rpackages/")
```

Call .libPaths() to make sure the path has been added    

```
> .libPaths()
```

To install a package, such as the "sm" package, tell R to put the package in your newly created local library:    

```
> install.packages("sm", lib="/rigel/<GROUP>/users/<UNI>/rpackages")
# For example
> install.packages("sm", lib="/rigel/free/users/hz2418/rpackages")
```

Select appropriate mirror and follow install instructions.   

Test to see if package can be called:     
 
```
> library(sm)
Package 'sm', version 2.2-5.6: type help(sm) for summary information
Warning message:
no DISPLAY variable so Tk is not available
```

In order to access this library from your programs, make sure you add the following line to the top of every program:   
    
```
> .libPaths("/rigel/<GROUP>/users/<UNI>/rpackages/")

```




#### May need to contact hpc-support@columbia.edu to install packages, but can try to install first.


 

## References
https://carpentries-incubator.github.io/hpc-intro/    
 
https://eaton-lab.org/articles/Eaton-lab-HPC-setup/     

https://github.com/TheJacksonLaboratory/IntroToHPC     
 
https://github.com/hbctraining/Intro-to-rnaseq-hpc-salmon-flipped        
 
https://github.com/hbctraining/Intro-to-ChIPseq-flipped   

 
