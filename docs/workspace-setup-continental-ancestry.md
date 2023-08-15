# **Workspace Setup**

Below are the steps to create a proper workspace to do ancestry estimation with rye or admixture

## Conda Environment

First we need to create a conda environment. The first step to do this is to download miniconda.

You can download miniconda at this link here: 

[Setting up miniconda](https://help.jasmin.ac.uk/article/5075-creating-and-using-miniconda-environments)

### Creating and activating a conda environment
    conda create -n {name}

    conda activate {name}

## Dependencies and Installations

Now we need to install the correct dependencies and packages to be able to do everything needed. Activate your conda environment and type the following code blocks into the command line

### Plink

    conda install -c bioconda plink

### Installations to use Rye

    //Execute commands individually
    conda install -c conda-forge r-base
    conda install -c conda-forge r-hmisc
    conda install -c bioconda r-optparse

There are also R packages that need to be installed on the system:

    //First activate R environment
    R

    //Install packages individually
    install.packages('nnls')
    install.packages('crayon')
    install.packages('parallel')
    install.packages('hmisc')
