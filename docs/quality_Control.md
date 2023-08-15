# Quality Control

Quality control is a process that occurs before and after harmonization/merging of data, and is a vital step to ensure that the data is viable for ancestry inference.

There are several quality control parameters that can be used with plink to ensure that our dataset is good to go:

## Variant Level QC

Variant level QC is quality control done on specific variants, or on a variant level.

### HWE Filter

Excludes variants that have Hardy-Weinberg equilibrium exact test p-value below the given threshold

    --hwe <threshold>

    //Threshold format in scientific notation (e.g. 1e-30)

### Indel Filter

Removes insertions (variants with length greater than 1)

    --exclude <txt file with list of excluded variants>


### LD Pruning

LD pruning is the process of filtering out variants above a certain LD threshold. More on linkage disequilibrium here: https://www.nature.com/articles/nrg2361

    --indep-pairwise <window size> <step size> <r^2 threshold>

### Geno Filter

Removes SNPs with > a given % of missingness

    --geno <missingness % threshold>

### MAF Filter

Only include SNPs with > given allele frequency

    --maf <allele frequency threshold>

## Sample Level QC

Sample level QC is quality control done on the entire sample / dataset, and not just on specific variants

### Mind Filter

Removes individuals with a given percentage of missing genotpyes

    --mind <percentage missing genotypes>

### QUAL Filter

Excludes variants under a given quality threshold

    --qual-threshold <quality threshold>