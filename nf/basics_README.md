# reqs
Configuration file (with SLURM executor so slurm jobs are fired away for each process)  
Container with environment (apptainer; formerly singularity)  
# Structure
modules directory:  
  defines processes, inputs, outputs  
  defines HPC resource usage  
main.nf  
  pipeline file  
  pulls processes from modules directory (e.g.  `include { process } from './modules/myProcess.nf')`  
  defines primary inputs (e.g. file names, metadata files, reference files etc.)  
  defines workflow  
