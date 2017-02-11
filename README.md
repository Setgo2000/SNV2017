# SNV2017
export PERL5LIB=/home/bitnami/vcftools_0.1.13/perl
export PATH=$PATH:/home/bitnami/vcftools_0.1.13/bin:/home/bitnami/DNAseq/tabix-0.2.6
export PATH=$PATH:/home/bitnami/DNAseq/plink-1.07-x86_64
export PATH=$PATH:/home/bitnami/DNAseq/rvtests/executable
export PATH=$PATH:/home/bitnami/DNAseq/plinkseq-0.10
export PATH=$PATH:/home/bitnami/DNAseq/tools/annovar

annotate_variation.pl -downdb -buildver hg19 -webfrom annovar refGene humandb
annotate_variation.pl -out HN_DSB2genes_anno -build hg19 HN_DSB2genes.avinput -webfrom annovar humandb/

brca1=/home/bitnami/DNAseq/DSB_vcf/HN_BRCA1_Rare005_miss02.recode.vcf
vcftools --vcf $brca1 --positions BRCA1_funcSNV.txt --recode --out HN_BRCA1_Rare005_annoSNV
(grep ^"#" HN_BRCA1_Rare005_annoSNV.recode.vcf; grep -v ^"#" HN_BRCA1_Rare005_annoSNV.recode.vcf | sed 's:^chr::ig' | sort -k1,1n -k2,2n) | bgzip -c > HN_BRCA1_Rare005_annoSNV.recode.vcf.gz
tabix -f -p vcf HN_BRCA1_Rare005_annoSNV.recode.vcf.gz

rad51=/home/bitnami/DNAseq/DSB_vcf/HN_RAD51_Rare005_miss02.recode.vcf
vcftools --vcf $rad51 --positions RAD51_funcSNV.txt --recode --out HN_RAD51_Rare005_annoSNV
(grep ^"#" HN_RAD51_Rare005_annoSNV.recode.vcf; grep -v ^"#" HN_RAD51_Rare005_annoSNV.recode.vcf | sed 's:^chr::ig' | sort -k1,1n -k2,2n) | bgzip -c > HN_RAD51_Rare005_annoSNV.recode.vcf.gz
tabix -f -p vcf HN_RAD51_Rare005_annoSNV.recode.vcf.gz

#risk association
vcf=HN_RAD51_Rare005_annoSNV.recode.vcf.gz

pheno=HN_DRC_apo_192Samples_pheno.txt
gene=/home/bitnami/DNAseq/refFlat_hg19_target_gene.txt
rvtest --inVcf $vcf --pheno $pheno --pheno-name status --kernel skat[nPerm=100:alpha=0.01:beta1=1:beta2=20] --geneFile $gene --covar $pheno --covar-name sex,age,smk,alco --out HN_status_RAD51_Rare005_skat_assoc
#rvtest --inVcf $vcf --pheno $pheno --pheno-name status --burden cmcWald --geneFile $geno --covar $pheno --covar-name sex,age,smk,alco --out HN_status_RAD51_Rare005_burdenCMC_assoc
rvtest --inVcf $vcf --pheno HN_DRC_apo_192Samples_pheno.txt --pheno-name status --burden zeggini --out HN_status_RAD51_Rare005

vcftools --gzvcf $vcf --chr 15 --plink --out HN_RAD51_Rare005_annoSNV
plink --file HN_RAD51_Rare005_annoSNV --recodeA --tab --noweb --out HN_RAD51_Rare005_annoSNV_4sas
 
vcf=HN_BRCA1_Rare005_annoSNV.recode.vcf.gz
vcftools --gzvcf $vcf --chr 17 --plink --out HN_BRCA1_Rare005_annoSNV
plink --file HN_BRCA1_Rare005_annoSNV --recodeA --tab --noweb --out HN_BRCA1_Rare005_annoSNV_4sas

