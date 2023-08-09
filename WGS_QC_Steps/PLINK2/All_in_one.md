# Step-by-step tutorial to QC WGS dataset:
#It is essential for the execution of the tutorial that that all scripts belonging to this tutorial are in 
#the same directory on your UNIX workstation.

## Data Preparation
#The variant call format of WGS data might come as separate files per individual smaple or chromosome. 
#In case you need to work on a specific set of cromosomes (e.g., autosomes), you may choose to concatenate those
#separate files into one single file and run the QC steps on that file.

### Concatenate separate .vcf.gz files into one file run:
```{bash}
bcftools concat path/to/WGSData.chr{1..22}.vcf.gz -Oz -o /Destination/path/WGS_auto.vcf.gz
```

### Convert the .vcf.gz file into PLINK2 binary files:
#PLINK2 is able to handle multiallelic issue, unlike previous PLINK versions. To convert a .vcf.gz data file run:
```{bash}
plink2 --vcf WGS_auto.vcf.gz --make-pgen --out WGS_autosomes
```
#this code will generate three binary files (.psam, .pvar, .pgen) in your directory along with a .log file. Use the 
#binary files to run the QC steps.

## Qulity Control

### STEP 1) check for missingness
```{bash}
plink2 --pfile WGS_autosomes --missing
#output: plink.smiss and plink.vmiss, these files show respectively sample-based and variant-based missingness.

#Generate plots to visualize the missingness results.
Rscript --no-save hist_miss.R

#Delete SNPs and individuals with high levels of missingness,
plink2 --pfile WGS_autosomes --mind 0.02 --geno 0.2 --make-pgen --out WGS_auto_QCed1
```
### Step 2) Retain SNPs with a low minor allele frequency (MAF)
```{r}
# Generate a plot of the MAF distribution.
plink2 --pfile WGS_auto_QCed1 --freq --out MAF_check

Rscript --no-save MAF_check.R

# Remove SNPs with a High MAF frequency.
plink2 --pfile WGS_auto_QCed1 --max-maf 0.20 --make-pgen --out WGS_auto_QCed2
```
















