# GBS vignette part 4

## quality filtering

You now have a file in your /working/guest#/split_fastqs/sam_sai/ directory that is named variants_rawfiltered.vcf. This file contains all of the information about variable sites (i.e. SNPs) in your dataset. Use less and tail to investigate the format of the vcf file. Then use grep to count how many variable sites are found in your raw file.

For a lot of different reasons, we do not want to use all of the variant sites in the raw vcf file. Instead, we will be using vcftools to filter out some loci that don’t meet certain thresholds that we will set.

      $ module load bcftools/1.3
      $ module load vcftools/0.1.14

First we need to format our file using bcftools. To do so, we will use some Unix commands to make an ids file (sheep_ids_col.txt).

      $ ls *sorted.bam > bams.txt

      $ sed "s/aln_//" bams.txt | sed -s "s/.sorted.bam//" > sheep_ids_col.txt
      
      $ bcftools reheader -s sheep_ids_col.txt variants_rawfiltered.vcf -o rehead_variants_rawfiltered.vcf

For our first filtering step, we will remove all variant sites that have minor allele frequencies (maf) less than 0.05. These loci could result from sequencing errors, and are often removed from next-generation sequencing datasets.

      $ vcftools --vcf rehead_variants_rawfiltered.vcf --out variants_maf0.05 --remove-filtered-all --maf 0.05 --recode --thin 90

This should remove a huge proportion of your loci, which is ok. We still have a lot of loci to work with. Next we will remove all loci that did not have a read sequenced in at least 40% of the individuals.

      $ vcftools --vcf variants_maf0.05.recode.vcf --out variants_maf0.05_miss40 --remove-filtered-all --max-missing 0.6 --recode

Now, we want to remove any individuals that have a lot of missing data. Specifically, we will remove individuals that do not have information at over 50% of the loci in our dataset.

      $ vcftools --vcf variants_maf0.05_miss40.recode.vcf --missing-indv

This last command created a file named out.imiss. Use less to inspect this file. The last column lists the fraction of loci that have missing data for an individual. We are going to make a list of individuals to remove using unix commands, and then use vcftools to actually remove them.

      $ mawk '$5 > 0.5' out.imiss | cut -f1 > lowDP.indv
      
      $ vcftools --vcf variants_maf0.05_miss40.recode.vcf --remove lowDP.indv --recode --recode-INFO-all --out variants_maf0.05_miss40_noBadInds

After these filtering steps, you should now have a dataset of 66 individuals and 13,958 loci. There are a lot of other filtering steps that you would complete if you were actually working on this dataset, but hopefully this gives you some idea of what that process looks like.

## population genetic analyses

To investigate patterns of genetic structure in our dataset, we will work with two files that I have prepared for you. These files can be found in the /working/guest#/pca directory. The first file (gprobs.txt) contains genotype probabilities for 55 individuals at 17,095 loci. The second file (pops.txt) contains the population information for the 55 individuals. Use less to inspect these files.

We will use R on your laptops (not on ponderosa) to conduct a principal components analysis (pca) on the dataset to ask if individuals from different populations are genetically differentiated. Use the following commands to conduct pca.

      $ gprobs <- read.delim("gprobs.txt", header=F, sep=" ")
      $ dim(gprobs)
      
      $ pops <- read.delim("pops.txt", header=F)
      $ dim(pops)
      
      $ pca <- prcomp(gprobs, center=TRUE, scale=FALSE)

You now have created an object (pca) that contains all of the results from the pca. Use the following commands to summarize the analysis and create

      $ summary(pca)
      
      $ plot(pca$x[,1], pca$x[,3], xlab="PC 1 (13.5% variance explained)", ylab="PC 3 (5.9% variance explained)", type="n")
      $ points(pca$x[pops=="DB_212", 1], pca$x[pops=="DB_212", 3], pch=21, bg="#7c67f2", cex=2, lwd=2)
      $ points(pca$x[pops=="DB_271", 1], pca$x[pops=="DB_271", 3], pch=21, bg="#edc213", cex=2, lwd=2)
      $ points(pca$x[pops=="DB_269", 1], pca$x[pops=="DB_269", 3], pch=21, bg="#007ea8", cex=2, lwd=2)
      $ points(pca$x[pops=="DB_268", 1], pca$x[pops=="DB_268", 3], pch=21, bg="#a05000", cex=2, lwd=2)
      $ legend("topleft", legend=c("Mormon Mtns. (271)", "Muddy Mtns. (268)", "Lone Mtn. (212)", "River Mtns. (269)"), pch=21, pt.bg=c("#edc213", "#a05000", "#7c67f2", "#007ea8"), cex=1.25, pt.cex=2, pt.lwd=2)

Congrats! You’ve made it through the entire module. You can see that there is strong genetic differentiation among the different populations.

REQUIRED TASK TO BE COMPLETED

Email Tom 1) a pdf of the PCA plot that you just made.


