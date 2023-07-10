# Performing Principal Component Analysis (PCA) with Plink

Before we can perform ancestry inference, we need to perform PCA with our data. This allows us to simplify the complexity of the data and create vectors that explain the majority of the variance within the dataset.

## File Types

We will be using 3 different file types together to run PCA.

1. BED file
    
    A bed file is a binary biallelic genotype table. We are unable to read it but Plink uses it for PCA and other operations. [More Information](https://www.cog-genomics.org/plink/1.9/formats#bed)

2. BIM file

    A BIM file contains information on every variant in a given datset, with data such as chromosome and variant identifier. [More Information](https://www.cog-genomics.org/plink/1.9/formats#bim)

3. FAM

    A FAM file is a text file containing information on each person in the dataset. [More Information](https://www.cog-genomics.org/plink/1.9/formats#fam)

## Running PCA

Here is a script to run a simple PCA:

    plink --bfile {myfile} --pca 20 --out {output}

    //Outputs
        - output.eigenvec
        - output.eigenval

**Note**: bfile refers to all three plink files (BED, BIM, FAM), and they must have the same file header

### Options


