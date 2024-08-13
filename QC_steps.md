## Step-by-step tutorial for QC of WES dataset:
To ensure the successful execution of this tutorial, it's essential that the data structure/format 
and QC variables align with those utilized herein. In this tutorial we'll use the annotated format 
of WES data which consist of separate files for each individual sample. For instance, the SP0000017.tsv 
data file in the SPARK_WES2 cohort represents WES data for **sample ID** SP000017, formatted as a 
tab-delimited file (**.tsv**). If the data isn't organized on a per-individual basis, is aggregated 
across samples per chromosome, or if is in a .vcf format, adjustments in the codes may be necessary. 
At times, all QC metrics might not be included in the annotated files, necessitating a check for the 
available variables. 

In the following section, we'll first explain the QC metrics utilized for the regional burden analysis (RBA).
Subsequently, we'll provide the code used to filter the data based on these QC variables.

### 1. QC vaiables
QC filtering consists of choosing specific thresholds for one or more annotations and throwing out any 
variants that have annotation values different than the set thresholds. By annotations, we mean properties 
or statistics that describe each variant e.g. what the sequence context is like around the variant site, 
how many reads covered it, what is the frequency of each allele, and so on. The filtering criteria outlined below 
adhere to the recommendations provided by GATK on their website: 
<https://gatk.broadinstitute.org/hc/en-us/articles/360035890471-Hard-filtering-germline-short-variants>.

#### *1.1. FisherStrand (FS)*
This is the Phred-scaled probability that measures strand bias at the site. Strand Bias tells us whether the
alternate allele was seen more or less often on the forward or reverse strand than the reference allele. 
We set the threshold value for filtering variants at 60 (we usually keep the variants with FS <= 60).  

#### *1.2. RMSMappingQuality (MQ)*
This is the root mean square mapping quality over all the reads at the site. When the mapping qualities are good at a site, the MQ will be around 60. Usually, variants with MQ values greater than or equal to 40 are retained.

#### *1.3. ReadPosRankSumTest (ReadPosRankSum)*
This is the u-based z-approximation from the Rank Sum Test for site position within reads. It compares whether the positions of the reference and alternate alleles are different within the reads. Seeing an allele only near the ends of reads is indicative of error, because that is where sequencers tend to make the most errors. A negative value indicates that the alternate allele is found at the ends of reads more often than the reference allele; a positive value indicates that the reference allele is found at the ends of reads more often than the alternate allele. A value close to zero is best because it indicates there is little difference between the positions of the reference and alternate alleles in the reads. We usually remove any variant with a ReadPosRankSum value less than -8.0.

#### *1.5. QUAL*
Quality: Phred-scaled quality score for the assertion made in alternative allele (ALT). Variants with a QUAL score <=30 are usually retained.

#### *1.6. StrandOddsRatio (SOR)*
It's a measure of strand bias (like FS). Most variants have an SOR value less than 3, and almost all variants have an SOR value less than 9. We usually retain variants with SOR > 3.

#### *1.7.  MappingQualityRankSumTest (MQRankSum)*
An index of mapping qualities of the reads. A positive value means the mapping qualities of the reads supporting the alternate allele are higher than those supporting the reference allele; a negative value indicates the mapping qualities of the reference allele are higher than those supporting the alternate allele. A value close to zero is best and indicates little difference between the mapping qualities. The hard filter threshold is set at -12.5, meaning to remove the variants having values less than -12.5.

#### *1.8. Typeseq_priority*
The type of sequence surrounding each variant can vary, including exonic, intergenic, intronic, splicing sites, and more. For our study, we retain only the "Exonic" variants.

#### *1.9. Effect_priority*
The impact of variants on a transcript or protein can be classified as synonymous, non-synonymous, frameshift, stop, loss or gain of function, etc. In this study, we filter out all synonymous variants.

#### *1.10. Freq_max*
The current study exclusively examines low frequency variants (i.e., alleles with a maximum frequency of less than 5%).


### 2. Performing QC in 3 steps
Keep in mind that we have one data file per individual/sample ID. Our goal is to filter out undesired variants in each data file and subsequently divide the final output into separate files for each chromosome.
![Filtering](https://github.com/NSafarian/RBA_Project/assets/102309428/577b55c8-e0da-498b-b2bc-0123b10dcedb)

The following scripts demonstrate the QC process:

#### *Step 1: compose the first script and name it **"Filter_data.sh"***
This script contains code to define columns of interest and the threshold for removing unwanted variants.
Here is the description of columns:
 - column #1: chromosome
 - column #6: QUAL
 - column #30: FS
 - column #34: MQ
 - column #39: ReadPosRankSum
 - column #72: effect_priority
 - column #226: Freq_max
   
**Note that the column number in your data might be different.**

```{bash}
#!/usr/bin/env bash

chr=$1
echo "Chr #$chr"
output_dir="filtered_$chr"
mkdir -p $output_dir
for file in /path/to/variants/data/per-sample/*.gz
do
	printf "now working on $file\n"
	f=$(basename $file)
	if [ ! -e $output_dir/$f ]; then
		zcat $file | awk -F $'\t' -v chr=$chr 'NR == 1 || ($1 == chr && $226 < 0.15 && $6 >= 30 && $30 <= 60 && $34 >= 40 && $39 >= -8 &&($72 ~/nonsynonymous/ || $72 ~/start/ || $72 ~/frameshift/ || $72 ~/stop/) )' | gzip > $output_dir/$f
  fi
done;

```

#### *Step 2: make the second script and name it **"Exe_Filter_data.sh"***
This script is crafted to execute the filtering steps outlined above for each chromosome within every data file. Iterating/looping over each chromosome significantly speeds up the entire process, proving highly advantageous when handling numerous files.

```{bash}
#!/usr/bin/env bash

for chr in `seq 1 22`;
do
  sbatch --time=3-03:00:00 --nodes=1 --mem=32GB Filter_data.sh $chr
done;

```

#### *Step 3: Run the filtering steps in a Linux/bash terminal*

```{bash}
#simply run:
bash Exe_Filter_data.sh

```
Now you have 22 folders (one per autosome) whithin each there is one filtered-data-file per individual/SampleID.



