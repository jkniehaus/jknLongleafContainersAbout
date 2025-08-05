# reqs
Configuration file (with SLURM executor so slurm jobs are fired away for each process)\n
Container with environment (apptainer; formerly singularity)
# Structure
modules directory:\n
  defines processes, inputs, outputs\n
  defines HPC resource usage\n
main.nf
  pipeline file
  pulls processes from modules directory (e.g.  `include { process } from './modules/myProcess.nf')`
  defines primary inputs (e.g. file names, metadata files, reference files etc.)
  defines workflow
