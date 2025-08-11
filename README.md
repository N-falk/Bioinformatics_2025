#Log into deepthought via jupyter hub: http://deepteachweb.flinders.edu.au/jupyter

#By default, you will be in your home directory on deepthought when you log in via jupyter; we want to work out of the scratch directory as there is more space here.

#To link your scratch directory to the home directory in your file browser:
ln -s /scratch/user/$USER scratch

#Now you will see your scratch directory and files in the file browser in the jupyter hub, which is more intuitive.

#To undo this, use: 
#rm scratch. 
#This command will delete the symbolic link to scratch without affecting the original directory /scratch/user/$USER or its contents. The removal only deletes the link, not the data it points to.
#to remove a file use rm file_name OR for a directory use rm -rf directory_name

# EXERCISE: subsam.py and count_and_qual.py test

#Step 1: Make a directory in scratch for the test files:
cd /scratch/user/USERNAME
#or click on the scratch folder
#mkdir directory_name
#cd directory_name
#to remove a file use rm file_name OR for a directory use rm -rf directory_name

#OR

#Step 2: Transfer the files (some fastq.gz and the two python scripts?):
#Use git clone to get the files
#First see if you have git installed:
git version
#Install git packages if required:
#sudo apt update
#sudo apt install git
#Use git clone to get the files:
git clone https://github.com/N-falk/Bioinformatics_2025

#Step 3: navigate to the fastq_fun directory:
cd Bioinformatics_2025/fastq_fun
#Look at the details of the files in the current directory:
ls -lh
#unzip the .gz files and look at them:
gunzip *.fastq.gz
#This will unzip all .gz fastq files to just .fastq files

Step 4:  Check python version and then for python scripts, will need to first install biopython for the scripts we will test and run:

python --version

pip install biopython

#you can run the .txt versions of the script, but let's make them .py files first:
nano count_and_qual.py
#copy and paste the info from count_and_qual.txt into count_and_qual.py and ctrlX, Y, and then Enter
nano subsamp.py
#copy and paste the info from subsamp.txt into subsamp.py and ctrlX, Y, and then Enter
#for your understanding, copy and paste the instructions from count_and_qual.py and/or subsamp.py into your prefereed AI and ask what each part of the code does!
#Now we can run the scripts by using the following commands which will run on all .fastq files in the directory:
python count_and_qual.py
python subsamp.py
#However, it is more efficient for deepthought for us to make a bash script so that we can run these scripts as submitted jobs, and deepthought's slurm manager can take car ot it:
#Open the file subsamp.slurm, and copy and paste in the following, starting with the line #!/bin/bash and ending with the line python subsamp.py:

#!/bin/bash

#SBATCH --job-name=subsamp_USERNAME
#SBATCH --output=%x-%j.out.txt
#SBATCH --error=%x-%j.err.txt
#SBATCH --partition=high-capacity
#SBATCH --qos=hc-concurrent-jobs
#SBATCH --time=4-0
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=16
#SBATCH --mem=10G

python subsamp.py

#for your understanding, copy and paste the text that is now in subsamp.slurm into your prefereed AI and ask what each part of the instruction does!
#Now run subsamp.py as a job submission by the following command:
sbatch subsamp.slurm
#and check the status of your job by:
squeue --me

#We will do the same for count_and_qual.py:
#Open the file count_and_qual.slurm, and copy and paste in the following:

#!/bin/bash

#SBATCH --job-name=countqual_USERNAME
#SBATCH --output=%x-%j.out.txt
#SBATCH --error=%x-%j.err.txt
#SBATCH --partition=high-capacity
#SBATCH --qos=hc-concurrent-jobs
#SBATCH --time=4-0
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=16
#SBATCH --mem=10G

python count_and_qual.py

#for your understanding, you can again copy and paste the text that is now in count_and_qual.slurm into your prefereed AI and ask what each part of the instruction does!
#Now run subsamp.py as a job submission by the following command:
sbatch count_and_qual.slurm
#and check the status of your job by:
squeue --me

#############################################################################################################################################################################################
#fastqc and fastp exercises#
#Navigate to the directory FastQC_fastp that's in Bioinformatics_2025. You can try this on your own or use the code below from whereever you are, replacing USER with your user FAN, and make sure you are in the correct relative path:
cd /scratch/user/USER/Bioinformatics_2025/FastQC_fastp

#we will use conda to install and manage program installations. Conda is a package manager, and helps partition programs and installations and keeps them nice and tidy, without interference from other programs.
#check the conda version that is (hopefully) installed on Deepthought
conda --version

#If you get "command not found", it means conda isn’t installed or isn’t in your PATH. On an HPC, you might need to first load it
module load miniconda3
conda init bash

#logout of terminal and start a new one

#create a conda environment to install fastqc:
conda create --name fastqc_env
#type y (as in yes) to accept the install.
#if you need to remove a conda environment use:
conda env remove --name myenv
#activate the fastqc conda environment:
conda activate fastqc_env
#Install FastQC: With your environment set up, install FastQC by running:
conda install -c bioconda fastqc
#Verification: Verify the installation by executing:
fastqc --help
#You should see a list of the fastQC instructions

#Run fastqc:
fastqc -o ./ ./*.fastq
#This command will run fastqz and generate QC reports for all .fastq files in your current location. Replace *.fastq with the a specific file name to run an individual file.
#You can make this a .slurm script to submit fastqc as a job, following the same instructions as above for subsamp.slurm or count_and_qual.slurm

#Deactivate the fastqc environment:
conda deactivate

#Create a new conda environment to install fastp:
conda create --name fastp_env
#activate the fastp conda environment:
conda activate fastp_env
#Install fastp: With your environment set up, install fastp by running:
conda install -c bioconda fastp
#Verification: Verify the installation by executing:
fastp --help

#Run fastp example:
#fastp -i Sample1.fastq -o Sample1_fastp.fastq -q 27 -f 30

#This code takes Sample1.fastq and makes a cleaned version called Sample1_fastp.fastq,then it chops off the first 30 bases of every read (maybe because of adapters or poor-quality sequence at the start).
#Then, at both read ends, trims bases with a quality score below Q27 (~0.2% error rate).

#You can make this a .slurm script to submit fastqc as a job, following the same instructions as above for subsamp.slurm or count_and_qual.slurm
#activate the fastqc_env environment again, and run fastqc on the file you just created with fastp:
fastqc -o ./ ./Sample1_fastp.fastq
#compare the fastqc reports (the .html files) for before and after applying fastp filtering to Sample1; you'll notice that fastp also outputs a small report (the fastp.html file)




