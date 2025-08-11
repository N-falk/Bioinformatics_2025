# Bioinformatics 2025 – FASTQ Quality and Processing Exercises

This repository contains exercises for working with FASTQ files on the **Deepthought** HPC via JupyterHub.  
You will learn how to navigate HPC directories, work with Git, run Python scripts, and use quality control tools like **FastQC** and **fastp**.

---

## 1. Log into Deepthought

Open JupyterHub at:  
[http://deepteachweb.flinders.edu.au/jupyter](http://deepteachweb.flinders.edu.au/jupyter)  

By default, you will be in your **home directory**.  
We want to work in the **scratch directory** (more space is available here).

---

## 2. Link your scratch directory to your home directory

```bash
ln -s /scratch/user/$USER scratch

rm scratch

cd /scratch/user/USERNAME
mkdir my_test_directory
cd my_test_directory
