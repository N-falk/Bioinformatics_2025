# Bioinformatics 2025: Deepthought JupyterHub & FASTQ Processing Exercises

---

## Logging into Deepthought via JupyterHub

Open your browser and go to:  
[http://deepteachweb.flinders.edu.au/jupyter](http://deepteachweb.flinders.edu.au/jupyter)

By default, when you log in, you will be placed in your **home directory**.  
However, we recommend working in the **scratch directory** because it has more storage space.

---

## Linking Scratch Directory in Jupyter File Browser

Create a symbolic link in your home directory to your scratch directory to make it easier to navigate:

```bash
ln -s /scratch/user/$USER scratch
```
Now the scratch directory will appear in your Jupyter file browser.
To undo/remove this link without deleting your actual scratch files, use:

```bash
rm scratch
```
Note:
This only deletes the symbolic link, not the original files.

To delete a file: rm file_name

To delete a directory and its contents: rm -rf directory_name

## Exercise: Testing subsamp.py and count_and_qual.py

Step 1: Create a directory in your scratch for the test files

```bash
cd /scratch/user/USERNAME
mkdir directory_name
cd directory_name
```
Alternatively, navigate in the Jupyter file browser to your scratch folder and create a directory.

Step 2: Obtain the files uing Git

The files are located on Github at https://github.com/N-falk/Bioinformatics_2025, and we will use the Git command to copy them to your scratch directory on Deepthought.

Check if git is installed:

```bash
git version
```

If not installed, on systems where you have permissions:

```bash
sudo apt update
sudo apt install git
```
Clone the repository containing the files:

```bash
git clone https://github.com/N-falk/Bioinformatics_2025
```

Step 3: Navigate to the fastq_fun directory and prepare files

```bash
cd Bioinformatics_2025/fastq_fun
ls -lh
gunzip *.fastq.gz
```
This unzips all .fastq.gz files to .fastq.

Step 4: Prepare Python environment and scripts
Check Python version:

```bash
python --version
```
Install Biopython (required by the Python scripts):

```bash
pip install biopython
```

Convert .txt scripts to .py files for execution. You can view the .txt versions of the script, but let's make them .py files first:

```bash
nano count_and_qual.py
# Paste contents of count_and_qual.txt, then save (Ctrl+X, Y, Enter)

nano subsamp.py
# Paste contents of subsamp.txt, then save
```

For better understanding, copy-paste the script contents into your preferred AI assistant and ask about each part of the code.

Step 5: Run the Python scripts

```bash
python count_and_qual.py
python subsamp.py
```

It is more efficient for deepthought to make a bash script so that we can run these python codes as submitted jobs to dedicated nodes, and deepthought's slurm manager can take car ot it.
Open the file subsamp.slurm, and copy and paste in the following, starting with the line #!/bin/bash and ending with the line python subsamp.py:

```bash
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
```
Submit the job to Deepthought using the "sbatch" command:

```bash
sbatch subsamp.slurm
```

Check job status:

```bash
squeue --me
```

Repeat the same process for count_and_qual.py using a count_and_qual.slurm file with this content:

```bash
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
```

Submit and monitor similarly:

```bash
sbatch count_and_qual.slurm
squeue --me
```
