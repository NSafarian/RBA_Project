# Step-by-step tutorial for QC of WES dataset:
To ensure the successful execution of this tutorial, it's essential that the data structure/format 
and QC variables align with those utilized herein. In this tutorial we'll use the annotated format 
of WES data which consist of separate files for each individual sample. For instance, the SP0000017.tsv 
data file in the SPARK_WES2 cohort represents WES data for **sample ID** SP000017, formatted as a 
tab-delimited file (**.tsv**). If the data isn't organized on a per-individual basis, is aggregated 
across samples per chromosome, or if is in a .vcf format, adjustments in the codes may be necessary. 
At times, all QC metrics might not be included in the annotated files, necessitating a check for the 
available variables. 

## 1. QC vaiables
QC filtering consists of choosing specific thresholds for one or more annotations and throwing out any 
variants that have annotation values above or below the set thresholds. By annotations, we mean properties 
or statistics that describe for each variant e.g. what type of sequence (like exonic, intronic, intergenic) is
around the variant site, or what is the frequency of variants, and so on.The filtering criteria outlined below 
adhere to the recommendations provided by GATK on their website: 
<https://gatk.broadinstitute.org/hc/en-us/articles/360035890471-Hard-filtering-germline-short-variants>.

### 1.1. FisherStrand (FS)
This is the Phred-scaled probability that measures strand bias at the site. Strand Bias tells us whether the
alternate allele was seen more or less often on the forward or reverse strand than the reference allele. The threshold value for filtering variants is set at 60 (we keep the variants with FS <= 60).  

### 1.2. RMSMappingQuality (MQ)
This is the root mean square mapping quality over all the reads at the site. When the mapping qualities are good at a site, the MQ will be around 60. We keep the variants with MQ >= 40.  

### 1.3. ReadPosRankSumTest (ReadPosRankSum)
This is the u-based z-approximation from the Rank Sum Test for site position within reads. It compares whether the positions of the reference and alternate alleles are different within the reads. Seeing an allele only near the ends of reads is indicative of error, because that is where sequencers tend to make the most errors. A negative value indicates that the alternate allele is found at the ends of reads more often than the reference allele; a positive value indicates that the reference allele is found at the ends of reads more often than the alternate allele. A value close to zero is best because it indicates there is little difference between the positions of the reference and alternate alleles in the reads. We remove any variant with a ReadPosRankSum value less than -8.0.

### 1.5. QUAL
Quality: Phred-scaled quality score for the assertion made in alternative allele (ALT). We keep variants with a QUAL score <=30.

### 1.6. Typeseq_priority
We keep only "Exonic" variants.

### 1.7. Effect_priority
We  filter out all synonymous variants and keep all non-synonymous, stop, loss, gain, frameshift mutations. 

### 1.8. Freq_max
We selected only variants with freq_max <5%. 


## 2. Perform QC 

Remember, we have one data file per individual/sample ID. The objective is to filter out undesired variants in each data file and and then segment the final output into separate files for each chromosome. 
![Filtering](https://github.com/NSafarian/RBA_Project/assets/102309428/577b55c8-e0da-498b-b2bc-0123b10dcedb)

The following scripts are designed for that purpose:
### Script 1: we name it **"Filter_data.sh"**
```{bash}

```
### Script 2: we name it **"Exe_Filter_data.sh"**
```{bash}

```

To run the script in bash/Linux you need to type 
```{bash}

```




