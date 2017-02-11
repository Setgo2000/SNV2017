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

