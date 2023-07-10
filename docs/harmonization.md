# Data Harmonization

### **Note: These commands have only been tested on the Gigalab server and not the Beast. Additionally, the commands were run with PLINK v2**

Data harmonization is imporant, because it ensures that the reference file and the query / test file have the same variants and merges them together. This is a crucial step, becuase it then allows us to run PCA and Rye/Admixture.

## Commands

Below are the commands necessary to harmonize and merge a reference and query dataset:

    plink --bfile {query dataset} --extract {Reference Variant List} --make-bed --out {out1} --set-all-var-ids chr@:#:$a:$r --snps-only

    plink --bfile {out1} --bmerge {all 3 reference panel files} --extract {overlapVariantList} --make-bed --out {merged file}

    awk '{print $2}' {out1.bim}
    >overlapVariantList

This will create multiple files:

1. merged dataset
    
    this is the merged dataset that combines the reference and query datsets

2. overlap variant list

    This is the list of the overlapping variants between the query and reference datasets


