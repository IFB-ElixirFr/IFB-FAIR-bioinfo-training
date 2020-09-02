

# Conda TP

[TOC]

## Conda setup
### How to access conda?

* Conda is so used that it could even be installed by default to your
    machine. To test this: `conda --version`

* if not, may install it or got it by a docker image:

```bash
docker run -i -t -v ${PWD}:/data continuumio/miniconda3
```
on the IFB cluster, with modules: `module load conda`

### Conda environment 
* We have already (blindly) use a conda configuration
file in the workflow session:

    ```
    conda env create -n envfair -f envfair.yml
    conda activate envfair
    ```

We will next detail the content of the configuration file, `envfair.yml`

## Example of a conda configuration file

**envfair.yml**

```python
channels:
  - conda-forge
  - bioconda
  - main
  - default
dependencies:
  - python=3.7.6 # specify python version (not required but can help with downstream conflicts)
  - snakemake-minimal=5.10.0 # workflow manager
  - graphviz=2.42.3 # for visualisation
  - xorg-libxrender
  - xorg-libxpm
  - wget=1.20.1 # for downloading files
  - fastqc=0.11.9 # for the RNAseq analysis
  - bowtie2=2.4.1
  - samtools=1.10
  - subread=2.0.1
```

## How to access tools?

### Manage Conda environment

1. create the working environment:

        conda create env -n myenv

2. activate it:

        conda activate myenv

3. if not yet done, install packages (specify the channel):

        conda install -c bioconda bowtie2

4. work with the tools

5. quite the environment:

        conda deactivate

## 1. Install Snakemake

### Objective Create a conda configuration file to install the snakemake
tool.

### Hint

- Search its channel in the Anaconda cloud web pages

- the \"minimal\" environment is sufficient

## 2. Install Snakemake

* condaEnvSnakemake.yml

    ```
    channels:
      - conda-forge
      - bioconda
      - main
    dependencies:
      - snakemake-minimal=5.10.0
    ```
---
**NOTE**

It works with almost all markdown flavours (the below blank line matters).

---



* run

    ```bash 
    conda create env -n condaEnvSnakemake -f condaEnvSnakemake.yml
    conda activate condaEnvSnakemake
    snakemake ...
    ```
