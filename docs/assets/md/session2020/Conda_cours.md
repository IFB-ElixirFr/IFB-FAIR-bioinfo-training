# Conda cours

[TOC]

## Environment manager

### Conda concepts, objetcs

* Environment: a set of packages/tools in a directory (added to our
    PATH)

* Conda: an open source package + a general-purpose environment
    management system (installation, execution, upgrade). For any
    programming language, multi-platform (Windows, MacOS, Linux).

* Conda package: a compressed tarball of a tool

### Why using an environment manager?

* avoid compilation and dependencies problems: an environment manager
    will take care of everything!

* have several environments in parallel each with their own set of
    tools

* useful when cross-tools dependencies are incompatible with each
    other

## Access

### Conda distribution

* Anaconda: a data science platform, comes with a lot of packages

* Miniconda: come without installed packages

Anconda cloud, the \"conda hub\"

* [Anaconda cloud](http://anaconda.org/) (private company) relies on the community of developers, concerns many domains (Machine Learning, Data Visualization,Dashboarding-web, Image Processing, Natural Language Processing, etc)

* Anaconda cloud: made up of channels/owners. Each channels contains one or more conda packages

* be careful when downloading any packages from an untrusted source,
 always inspect before installation
 
 > See exo Conda Setup

## About channels

### Some conda channels

* defaults

* bioconda: bioinformaticians contributions

* conda-forge: many popular python packages (analogous to PyPI but
    with a unified, automated build infrastructure and more peer review
    of recipes)

    r: for packages in R language

### Channels list order

* when different channels have the same package $\Rightarrow$ collisions

* collisions resolved following the order of your channels list     $\Rightarrow$ put supplemental channels at the bottom of your
    channel list

## Command

### simple commands

```bash
conda create env -n myenv # creation of a conda environment 
conda info --envs # list environments (* for the active one) 
conda activate myenv # active the myenv environment
conda deactivate # inactivate the environment
conda list # list packages (only in an active environment)
conda install package # installation of a tool/package
conda remove package # suppress the tool from the environment
conda env remove -n myenv # suppress the myenv environment
```

### miniconda3

* With the miniconda3 distribution and by default, environments
are installed in a miniconda3/envs/ repository

## 2 modes

### interactive

* create an environment

* activate the environment

* install some conda packages

### configuration file

* list all conda packages in a configuration file (yaml or json
    format)

* create the environment based on the configuration file (option `-f`)

* activate the environment

 > See exo Conda configuration


### reproducibility

* good practice: use a configuration file

* specify a precise version of a package:
    `<channel>::<package>=<version>`
