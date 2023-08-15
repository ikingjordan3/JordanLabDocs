# Using Rye

### This is a tutorial for how to use Rye to compute ancestry estimates

## Installing Rye

Execute the following line of code in your desired directory

    //Downloading the repository
    git clone https://github.com/healthdisparities/rye

    //Making sure permissions are correct
    cd rye

    //If they aren't ->
    chmod +x rye.R

    //Checking if the installation happened correctly
    ./rye.R -h

## General Usage

### Running an example (if needed)
The repository already comes with an example that is helpful. Execute the following code into the command line

    ./rye.R --eigenvec=examples/example.eigenvec --eigenval=examples/example.eigenval --pop2group=examples/pop2group.txt --rounds=5 --threads=2 --iter=5 --out=out

### Options

    --eigenval=<EVAL_FILE>
            Eigenvalue file [REQUIRED]

    --eigenvec=<EVEC_FILE>
            Eigenvector file [REQUIRED]

    --pop2group=<P2G_FILE>
            Population-to-group mapping file [REQUIRED]

    --output=<OUTPUT_PREFIX>
            Output prefix (Default = output)

    --threads=<THREADS>
            Number of threads to use (Default = 4)

    --pcs=<#PCS>
            Number of PCs to use (Default = 20)

    --rounds=<OPTIM-ROUNDS>
            Number of rounds to use for optimization (higher number = more accurate but slower; Default=200)

    --iter=<OPTIM-ITERS>
            Number of iterations to use for optimization (higher number = more accurate but slower; Default=100)
    
    --attempts=<ATTEMPTS>
            Number of attempts to find the optimum values (Default = 4)

    -h, --help
            Show this help message and exit

## Steps for Success

### **Step 1: Ensure PCs are calculated**

Before we can run rye, we need to make sure that we already ran Principal Component Analysis (PCA) on the datset. If this has not already been done, stop here and run PCA first

### **Step 2: Create a population to group mapping file**

A population to group mapping file simply provides instructions on how to aggregate populations into groups (e.g., GBR and CEU into European or Western European groups).

This is a tab-separated file with headers and two mandatory columns - Pop and Group - rest of the columns are ignored:

    Pop Group
    CEU European
    GBR	European
    YRI African
    GWD African
    CHB Asian
    JPT Asian

The name of the file is inconsequential and the user can decide a name that is intuitive to them.

### **Step 3: Estimating ancestry through Rye**

Finally, run the following command:

    ./rye.R --eigenval=input.eigenval --eigenvec=input.eigenvec --pop2group=pop2group.txt --output=rye_output

This will create two output files:
1. rye_outputM.N.Q: An admixture style file with `M = number of PCs` and `N = number of groups in the pop2group.txt file`
2. rye_outputM.fam: A FAM file containing the mapping of the individuals

## Fine tuning
Rye currently supports the following options for fine tuning of the results:
1. `pcs`
2. `rounds`
3. `iter`
4. `attempts`

In general, the top 20 PCs are usually sufficient for assessing accurate results.