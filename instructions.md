# Running the container

# Request an interactive session
# Without --mem 0 notebook hangs on a particular code cell due to the lack of memory
qsub -I -l walltime=6:00:00 -q casper -l select=1:ngpus=1 -l gpu_type=v100 -l select=1:mem=0 -A ntdd0002


# Load necessary modules 
# module load cuda (not required) 
module load singularity 

# Enable port forwarding between interactive session host and the local using in another terminal 
# hostname can be found by running "hostname" in your interactive session.
SSH -N -L 8889:<casper_hostname>:8889 <username>@<casper_hostname>.ucar.edu				

Example: SSH -N -L 8889:casper16:8889 kchemudu@casper16.ucar.edu				

# rapids.sif has three directories that can be bound
#	/mnt/input — directory with input files
#	/mnt/output — directory with output files to be created 
#	/mnt/notebooks — git repo or dir with all ipy notebooks
#
# run the container using this command:
#	--nv lets the GPUs be accessible within the container 
#	--bind <path_host>:<path_singularity> binds the two directories so files within the host 
#		directory are available inside the singularity container 
#	We can bind the output directory as well if required 

singularity run --nv \
--bind /glade/work/kchemudu/rapids_singularity/GPU_data_analysis_rapids:/mnt/notebooks/ \
--bind /glade/scratch/jsauer/ForPeople/ForCISL/ForSIPARCS/CBL_5m/output:/mnt/input/ \
rapids.sif

#Note: Make sure the port number given to you by casper is the same as the one in the "SSH" command above. 
# Change path in cells of the ipynb file, from “/glade/scratch/jsauer/ForPeople/ForCISL/ForSIPARCS/CBL_5m/output”
# 	to “/mnt/input/“ due to the directory binding. 
