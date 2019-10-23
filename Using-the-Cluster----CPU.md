**Department Documentation:** https://www.csb.pitt.edu/using-the-cluster/  We will be focusing on the slurm details

## Connecting to the Head Node
`ssh username@slurm.csb.pitt.edu`

## Launch an interactive session
`salloc -p bahar_12 srun --pty /bin/bash -i`

Change any flag as needed.

## Queues 

There are many available that can be viewed with `sinfo`. We will mainly be using dept resources

`dept_64`  64 cores per node, tends to be the first place to submit things

`dept_24`  24 cores per node, tends to be the secondary place to submit things

`dmz_8`  8 cores per node. Tends to be the slower of the CPUs available

`any_cpu`  Job will be scheduled on any available cpu (including nodes in group queues), please limit to 24 hours.

## Array Jobs

Array jobs launch many jobs using the same run script, changing only the SLURM_ARRAY_TASK_ID variable.  These are good ways to launch large numbers of jobs without overburdening the queue system.  You might use this to run multiple docking jobs. It also supports an easy pattern from running many commands listed in a text file (one per a line): 

### PBS Script
```
#!/bin/bash
#SBATCH -J example_array
#SBATCH -t 14-00:00:00
#SBATCH --partition=dept_64
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=1

cmd=`sed -n "${SLURM_ARRAY_TASK_ID}p" your_cmds.txt`
eval $cmd
exit
```
### Command file, `your_cmds.txt`:
One command per line. Line numbers are 1-indexed.
```
python train.py 1 --do-stuff
python train.py 2 --do-stuff
...
```

### Launch jobs 1-24 of an array job script

`sbatch --array 1-24 your_array_job_script.slurm`

Note: Job arrays can only be in range [1-1000].
## Checking job status
```
squeue -u USERNAME
```

`-u USERNAME` shows only jobs from a certain user


## Checking output
Upon completion anything printed to stdout/stderr should be copied into a file `slurm-<jobid>.out`.  This is the first place to look for errors when jobs end abruptly.