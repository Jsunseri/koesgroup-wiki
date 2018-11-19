**Department Documentation:** https://www.csb.pitt.edu/using-the-cluster/

## Launch an interactive session
`qsub -I -q any_gpu -l nodes=1:ppn=1:gpus=1,walltime=1:00:00`

Change any flag such as `-q`, `nodes`, `walltime`, etc. as needed.

## Array Jobs
### PBS Script
```
#!/bin/bash
#PBS -N example_array
#PBS -j oe
#PBS -l nodes=1:ppn=4:gpus=1
#PBS -l walltime=14:00:00:00
#PBS -q dept_gpu_12GB
#PBS -m abe
#PBS -M your_email@pitt.edu
cd $PBS_O_WORKDIR
cmd=`sed -n "${PBS_ARRAYID}p" your_cmds.txt`
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

`qsub -t 1-24 your_array_job_script.pbs`

## Checking job status
```
alias q='qstat -t  -n -1'
```
`-t` shows array jobs as individual jobs, `-n` shows the host name of the node, `-1` puts everything on one line

`-u USERNAME` shows only jobs from a certain user

```
/net/pulsar/home/koes/dkoes/git/scripts/gpus.py
```
Shows per-GPU status.  Can limit output to a provided queue (e.g. `gpus.py dept_gpu`)