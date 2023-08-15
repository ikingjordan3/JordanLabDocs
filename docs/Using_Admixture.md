# Using Admixture for Ancestry Inference

This is a tutorial on how to use Admixture, which is an ancestry inference tool.



## Installations

Note: Jupyter notebook, R, and plink should already be installed in your conda environment at this point. If you do not, visit the pca and plotting tabs

VEP tool:
    
    conda install -c bioconda ensembl-vep

ADMIXTURE:
    
    conda install -c bioconda admixture

## Using Admixture

Admixture is a fairly easy tool to use. The terminal command is as follows:

    admixture <Bed File Path> K

- K is the number of theoretical source populations
- The BED file is combined to contain both query and reference datasets

