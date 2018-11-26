**Department Documentation:** https://www.csb.pitt.edu/using-the-cluster/

## Launch an interactive session
`qsub -I -q any_gpu -l nodes=1:ppn=1:gpus=1,walltime=1:00:00`

Change any flag such as `-q`, `nodes`, `walltime`, etc. as needed.

## Queues 

`dept_gpu` All departmental GPU nodes.  Anyone can use.  About half of these nodes I bought, but I was taught to share as a child so they are in the departmental queue.

`dept_gpu_8GB`  Since all dept_gpu nodes now have 8GB of RAM (on the GPU), this is a higher priority version of dept_gpu.  

`dept_gpu_12GB`  Only nodes with >= 11GB of GPU RAM

`any_gpu`  Job will be scheduled on any available GPU (including nodes in group queues), but has a time limit of 24 hours.

### Node Properties

The following aren't queues, but node properties that are added to the resource request line (e.g. ` -l nodes=1:ppn=1:gpus=1:gtx1080Ti`)

`C5`: Compute capability >= 5.0 (probably want this if running deep learning jobs on any_gpu)

`C6`: Compute capability >= 6.0

`M12`: GPU memory >=12 GB (excludes 11GB cards)

`gtx1080Ti`, `Volta`, `TitanV`, etc: Specific class of video card.

## Array Jobs

Array jobs launch many jobs using the same run script, changing only the PBS_ARRAYID variable.  These are good ways to launch large numbers of jobs without overburdening the queue system.  You might use this to run three replicates of an MD simulation using the same run script. It also supports an easy pattern from running many commands listed in a text file (one per a line): 

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

## Running Caffe

You will need to setup your local environment:
```
export LD_LIBRARY_PATH=/net/pulsar/home/koes/dkoes/local/lib:/usr/lib64:/usr/lib/x86_64-linux-gnu:/usr/local/cuda-9.0/lib64:/usr/lib
export PYTHONPATH=/net/pulsar/home/koes/dkoes/local/python
export PATH=$PATH:/net/pulsar/home/koes/dkoes/git/gninascripts:/net/pulsar/home/koes/dkoes/local/bin
```
