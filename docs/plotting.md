# Plotting

This page will help you get started with plotting in R using Jupyter notebook. I will be going through some tutorials to create useful plots for ancestry inference.

## Getting Started with Jupyter Notebook

First you need to get jupyter notebook and R through conda using these commands:

    //jupyterlab
        conda install -c conda-forge jupyterlab

    //R
        conda install -c r r-essentials r-irkernel


To use a Jupyter notebook through a remote server, follow the steps in this link. https://docs.anaconda.com/free/anaconda/jupyter-notebooks/remote-jupyter-notebook/

If this does not work, it is also possible to connect to a remote server using Visual Studio Code and edit the Jupyter notebook directly. More instructions on that here: https://code.visualstudio.com/docs/remote/ssh

## PCA Plots

Once in Jupyter Notebook, here is a quick example on how to create a PCA plot in R. Here I will be using a subset of the 1000 Genomes Project reference dataset that only includes European, African, and East Asian samples. This dataset is merged with American admixed populations, meaning their genetic makeup is the result of a mixture of different reference populations.

First we have to load our libraries:

    library("plyr")
    library('dplyr')
    library('ggplot2')
    library('readr')
    library('stats')

Next, we can load in our table(s). In this case, it is just one file with the eigenvector data obtained from my plink files:

    eigenvec = read.table("../Data/Sample1KGP/sampleData1KGP.PCA.eigenvec", header = FALSE, sep = " ")

The next thing I did is reformatted the table to make plotting easier:

    //Rowname changed from 1, 2, etc. to the specific sample identifier (ACB1, ACB2, ESN1, etc.)
        rownames(eigenvec) <- eigenvec[,2]

    //Cutting out starting rows, leaving only eigenvector data
        eigenvec <- eigenvec[,3:ncol(eigenvec)]

    //Formatting table to only include first two PCs, changing column headers
        colnames(eigenvec) <- paste('PC', c(1:20), sep = '')
        eigenvec$IndividualID = row.names(eigenvec)
        eigenvec = eigenvec[c("IndividualID", "PC1", "PC2")]
        row.names(eigenvec) <- NULL

    //Adding a column named PopGroup, that includes the population group of each sample as a column
        eigenvec = transform(eigenvec, PopGroup = substr(IndividualID, start = 1, stop = 3))

Now we can move on to plotting. Using the package ggplot2, we can create a PCA plot from our eigenvector data:

    //Color List

        colors  <- c("YRI" = "blue", "ACB" = "dark red", "ASW" = "dark red", "CEU" = "orange", "CHB" = "dark green", "ESN" = "blue", "GBR" = "orange", "JPT" = "dark green")

    //Plotting PCA plot for the top two PCs.

        options(repr.plot.width=12, repr.plot.height=12)
        ggplot(eigenvec, aes(x=PC1, y=PC2, fill=PopGroup)) + scale_fill_manual(values=colors) +
            geom_point(size = 7, pch = 21, color="black") + theme_classic() + theme(axis.title=element_text(size=23)) + theme(axis.text.x=element_text(size=14)) + theme(axis.text.y=element_text(size=14)) +
            theme(legend.key.size = unit(1, 'cm'), #change legend key size
                legend.key.height = unit(1, 'cm'), #change legend key height
                legend.key.width = unit(1, 'cm'), #change legend key width
                legend.title = element_text(size=14), #change legend title font size
                legend.text = element_text(size=14)) #change legend text font size

Running this will create a plot similar to this, which is our working PCA plot.

![Alt text](<PCA plot-1.PNG>)

## Admixture Plots

The following will be an example on how to make an admixture plot, with the same example data used for the PCA plot example above.

First we need to read in our ancestry estimates (obtained using Admixture or Rye):

    ancestryEstimates1 = read.table("../bin/rye/Sample1KGP_out-20.3.Q")
    ancestryEstimates2 = read.table("../bin/rye/Sample1KGP_out-20.3.Q")
    ancestryEstimates3 = read.table("../bin/rye/Sample1KGP_out-20.3.Q")

    //Here I import 3 duplicate tables because of the way I want to format the data. There is likely an easier method, however this is how I did it




![Alt text](Admixture_Plot.PNG)


