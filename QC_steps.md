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

### 1.2. RMSMappingQuality (MQ)

### 1.3. ReadPosRankSumTest (ReadPosRankSum)

### 1.4. StrandOddsRatio (SOR)

### 1.5. QUAL

### 1.6. Typeseq_priority

### 1.7. Effect_priority

### 1.8. Freq_max








