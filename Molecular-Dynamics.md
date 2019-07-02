http://ambermd.org/tutorials/Introductory.php

https://github.com/dkoes/md-scripts/blob/master/prepareamber.py

Below is a PBS script that can be used to submit multiple simulations using array jobs.  It assumes simulations were setup with prepareamber.py and the base name of the simulation is the current directory name.  You would launch three simulations with `qsub -t 1-3 run.pbs`

```
#!/bin/bash
#PBS -N I_forgot_to_name_my_job 
#PBS -j oe
#PBS -l nodes=1:ppn=1:gpus=1
#PBS -l walltime=24:00:00:00
#PBS -q dept_gpu

#the following may need to be updated if amber is rebuilt with newer cuda
export LD_LIBRARY_PATH=/usr/local/cuda-9.0/lib64:$LD_LIBRARY_PATH

echo Running on `hostname`
echo workdir $PBS_O_WORKDIR
echo ld_library_path $LD_LIBRARY_PATH

#for some reason when the job starts on the node, it auto-cd's to your home dir
cd $PBS_O_WORKDIR

#scratch drive folder to work in
SCRDIR=/scr/$PBS_JOBID

#if the scratch drive doesn't exist (it shouldn't) make it.
if [[ ! -e $SCRDIR ]]; then
        mkdir $SCRDIR
fi

chmod +rX $SCRDIR

echo scratch drive ${SCRDIR}

cp $PBS_O_WORKDIR/*.in ${SCRDIR}
cp $PBS_O_WORKDIR/*.prmtop ${SCRDIR}
cp $PBS_O_WORKDIR/*_md2.rst ${SCRDIR}
cp $PBS_O_WORKDIR/*.inpcrd ${SCRDIR}

cd ${SCRDIR}

#amber setup
AMBERHOME=/usr/local/amber18
PATH=$AMBERHOME/bin:$PATH

#setup to copy files back to working dir on exit
trap "mv *md3.nc $PBS_O_WORKDIR" EXIT

#run the MD - the below line assumes the directory name is the base name of the simulation
i=${PBS_O_WORKDIR##*/}
pmemd.cuda -O -i ${i}_md3.in -o $PBS_O_WORKDIR/${i}_${PBS_ARRAYID}_md3.out -p ${i}.prmtop -c ${i}_md2.rst -r ${i}_${PBS_ARRAYID}_md3.rst -x ${i}_${PBS_ARRAYID}_md3.nc -inf $PBS_O_WORKDIR/mdinfo.$PBS_ARRAYID  

exit
```