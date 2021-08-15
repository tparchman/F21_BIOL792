# GBS vignette part 3

## Summarizing reference-based assembly

We left off using the program bwa to perform reference-based alignment of our sequences to the domestic sheep genome. The results of this alignment can be found in /working/guest#/split_fastqs/sam_sai. The program generates two types of files for each individual, a SAM file and a SAI file. These file formats are extremely large and bulky, so I have used the perl script sam2bam_v1.3.pl to convert these files into compressed versions that we will be working with (*.bam, *.sorted.bam, *.bai). However, you can find one of the *.sam files in the /working/guest#/example_sam_file directory. Sequence Alignment/Map (SAM) files are very useful files that contain a wealth of information about the quality of our alignment. As an added bonus, they are readily parsible using the Perl tools that we have been using in previous weeks of class. You can learn a lot about the format of SAM files here: https://samtools.github.io/hts-specs/SAMv1.pdf

REQUIRED TASK TO BE COMPLETED

Once you are familiar with the format of the *sam file, make a Perl script to summarize your reference-based assembly. Specifically, you should generate two output files. The first output file will simply list how many reads were mapped to each of the scaffolds in the genome (sorted). The second file will list how many reads mapped to each genomic position on each of the scaffolds (sorted). You will probably want to create a hash with two keys to make the second file, and use ++ to increase the value each time you encounter each scaffold/position pair.

## Calling variants

We will be using the programs samtools and bcftools to call variants (i.e., find SNPs) in our dataset. This process will take some time, but will ultimately construct a *.vcf file that will contain information about the genotypes of individual sheep at every variable position in the genome that we sampled in our dataset. Before next class, please become familiar with the format of *.vcf files, as we will be using them extensively to perform quality filtering on our dataset. Use the following commands to call variants. You will need to be in the /working/guest#/split_fastqs/sam_sai directory. You will probably want to place the job in the background after you know it is working appropriately.

      $ module load samtools/1.3
      $ module load bcftools/1.3
      
      $ samtools mpileup -P ILLUMINA --BCF --max-depth 100 --adjust-MQ 50 --min-BQ 20 --min-MQ 20 --skip-indels --output-tags AD,DP --fasta-ref ../aries_genome.fa aln*sorted.bam | bcftools call -m --variants-only --format-fields GQ --skip-variants indels | bcftools filter --set-GTs . -i 'QUAL > 19 && FMT/GQ >9' | bcftools view -m 2 -M 2 -v snps --apply-filter "PASS" --output-type v --output-file variants_rawfiltered.vcf

