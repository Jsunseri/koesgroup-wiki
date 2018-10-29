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