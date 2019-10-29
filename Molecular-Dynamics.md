# Amber
Our group typically uses Amber for performing molecular dynamics. Amber
describes both a set of force fields and a suite of MD programs. The Amber
website provides extensive documentation of program usage, including
[tutorials](http://ambermd.org/tutorials/Introductory.php) for many common
tasks, [manuals](http://ambermd.org/Manuals.php) for major releases of Amber
and AmberTools (these are the authoritative resource for using the software),
and an active [mailing list](http://archive.ambermd.org/) for debugging and
support. Although we use a shared script for automating the process of setting
up and running Amber molecular dynamics ([prepareamber.py](#prepareamber),
described below), it is still recommened that you read at least the
introductory Amber documentation before getting started so that you understand
what you are doing, and that you refer to it for debugging purposes when you
encounter problems. 

# prepareamber.py<a name="prepareamber"></a>
We maintain a shared script,
[prepareamber.py](https://github.com/dkoes/md-scripts/blob/master/prepareamber.py),
to automate the process of setting up and running Amber MDs as much as
possible. Before using it, you must have a working Amber installation with
properly defined environment variables - i.e. your AMBERHOME should be set to
your Amber installation directory, with your PATH, PYTHONPATH, and
LD\_LIBRARY\_PATH updated accordingly. Amber provides a helper script to assist
in doing this properly, so `source path/to/amber/amber.sh` should update all
the required environment variables; you may want to add this command to an
appropriate dotfile (e.g. your `.bashrc` if you are using bash).  You will also
need to have the `obabel` executable on your path (if you are running on one of
our group's workstations or on the cluster, this file is likely to already be
either in a system install location or somewhere in the `dkoes` home directory
\- relative to your own home directory, try looking in `../dkoes/bin` or
`../dkoes/local/bin` if `which obabel` doesn't return anything). If you can't
find it, you may need to [install
OpenBabel](https://openbabel.org/docs/dev/Installation/install.html). You'll
also need to `pip install plumbum`. 

After all that's done, you should be ready to run the script. `prepareamber.py
--help` will give you a list of options (and if it fails, it will let you know
anything you missed in the previous step, which you should go back and
address). The most basic invocation just provides the script one or more
structures you want to simulate, i.e. `prepareamber.py -s rec.pdb LIG.sdf`. All
the structures you provide will be simulated _together_, so if you want to
perform separate simulations of a set of ligands with a particular protein,
those will require separate invocations of the script. 

Additionally, note that separate structures are provided to the script as
separate files (so if you retrieve a protein-ligand complex from the PDB, you
will need to separate the protein and ligand into different files before
invoking the script). The reason this decision was made is that one of the
major things the script does is parametrize small molecules for which it does
not already have parameters, but proteins may themselves have residues for
which parameters may be missing, and it would be incorrect to use the same
process for parameter assignment for a protein as for a small molecule.
Therefore the script will decline to parameterize any undefined residues in a
structure it has identified as a protein, and will instead report an error with
the name of the undefined residue. 

Finally, it's important to pay attention to protonation states. By default, the
script strips out any hydrogens that are present and then adds them back to small molecules with
OpenBabel and to proteins with Leap. Pass `-noh` if you don't want it to touch
your hydrogens (e.g. if you have a ligand crystal structure from the PDB and
are confident in its protonation state), but keep in mind that because of Amber naming conventions for
hydrogens in proteins, in most cases you really do want the _protein's_
hydrogens to be removed before processing. In that case you can strip out the
protein hydrogens before beginning with OpenBabel, and pass the prepared
protein with ligand structures containing the desired hydrogens to
`prepareamber.py` with the `-noh` option. 

## Troubleshooting<a name="troubleshoot"></a>
1. [ImportError: Check that obabel is on your path](#obabel)
2. [ImportError: Check that AMBER binaries are on your path](#amber)
3. [ImportError: Check match\_atomname (one of the antechamber-related
   AmberTools packages) has been built \- starting with AmberTools18 it is not
   built by default, but can be built manually from
   $AMBERHOME/AmberTools/src/antechamber/](#matchatomname)
4. [Antechamber failed. Check XXX structure](#antechamber)

### ImportError: Check that obabel is on your path<a name="obabel"></a>
Find the `obabel` install location. If you cannot find it, [install it](https://openbabel.org/docs/dev/Installation/install.html).
Then add the directory it's in to your PATH if it isn't there already. [(Back)](#troubleshoot)

### ImportError: Check that AMBER binaries are on your path<a name="amber"></a>
Find the AMBER install location. On the cluster, look in `/usr/local`, and on a
group-owned workstation, try `/home/dkoes/build`. Set `AMBERHOME=/path/to/amber`, `source $AMBERHOME/amber.sh`, and `echo "source $AMBERHOME/amber.sh" >> ~/.bashrc`. Confirm that `which pmemd.cuda` returns the executable in `$AMBERHOME/bin`. [(Back)](#troubleshoot)

### ImportError: Check match\_atomname has been built<a name="matchatomname"></a>
See [this thread](http://archive.ambermd.org/201905/0313.html) for more information. 
Patch things up by doing the following: `cd $AMBERHOME/AmberTools/src/antechamber`. Then `make match_atomname` and `cp match_atomname $AMBERHOME/bin`. If your environment variables have been properly set up, `which match_atomname` should now return that executable. [(Back)](#troubleshoot)

### Antechamber failed. Check XXX structure.<a name="antehamber"></a>
Antechamber is the Amber program that is used to parametrize small molecules.
It can fail for various reasons. 

- First, verify that the file it failed with actually contains a small molecule -
if it's a peptide, a nonstandard residue that's part of a protein, a nucleic
acid, etc., it probably shouldn't be parametrized with antechamber in the first
place. If it's a peptide, check the initial file to see why it isn't being
correctly identified as one (for example, sometimes the residue names are
missing). If it's anything other than a normal small molecule or a normal
protein, you may need to get parameters from somewhere, and provide them to the
script via the `--libs` argument. If you're simulating a structure that our
group has simulated before, check on Slack - somebody should have the
parameters you need. A good first place to check for missing
parameters is [The University of Manchester's AMBER parameter
database](http://research.bmh.manchester.ac.uk/bryce/amber). Querying the
literature is also an option. In some cases, doing an ab initio calculation
with Gaussian (available on the cluster) may be required. 

- If you are working with a small molecule and really want to parametrize it with
  antechamber, a common problem is making sure it's been protonated correctly
  and the correct net charge has been provided to antechamber. By default, the
  script strips out hydrogens present in the input, and adds them back to the
  ligand with OpenBabel. It attempts to calculate a net charge to pass to
  antechamber by calculating Gasteiger charges, and if that fails, it attempts
  to rerun antechamber with common net charge values. If you are confident that
  your starting hydrogens are correct, you should run the script with `-noh`
  (with the caveat that you might want to strip out the protein hydrogens first
  if you are simulating a complex, e.g. with `pdb4amber`). You can also pass a
  desired net charge to antechamber with `--net_charge`. 

- Always sanity check your structures. If antechamber fails, visualize it and
  make sure things look normal. This is especially true if all of the above
  fails, but as a rule you should __always check your data__, and that's
  exactly what your structure is. Another common problem is incorrect bonding
  or geometry, which can be introduced as the structure is processed by
  different pieces of software. For example, some software infers bonds based
  on distance, and may incorrectly introduce a bond when the ligand's geometry
  is strained. It's often easier to find these kinds of problems quickly by
  looking at the structure in PyMol than by staring at the file in your text
  editor.  
[(Back)](#troubleshoot)

# PBS MD Jobs
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
