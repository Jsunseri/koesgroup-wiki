https://www.csb.pitt.edu/using-the-cluster/

## Useful Commands
### Launch an interactive session
`qsub -I -q any_gpu -l nodes=1:ppn=1:gpus=1,walltime=1:00:00`
Change any flag such as `-q`, `nodes`, `walltime`, etc. as needed.

### Launch jobs 1-24 of an array job script
`qsub -t 1-24 model_2.pbs`