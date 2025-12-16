Viral-genomics variant calling work flow (SARS COV 2 - COVID 19)
# ENA --> Download rawdata
wget -nc ftp://ftp.sra.ebi.ac.uk/vol1/fastq/ERR574/003/ERR5743893/ERR5743893_1.fastq.gz
wget -nc ftp://ftp.sra.ebi.ac.uk/vol1/fastq/ERR574/003/ERR5743893/ERR5743893_2.fastq.gz
wget https://www.ebi.ac.uk/ena/browser/api/fasta/MN908947.3?download=true -O MN908947.fasta
# Install fastqc, bwa, SAM tools
sudo apt update
sudo apt upgrade
sudo apt install fastqc bwa samtools
# install freebayes
conda install -c bioconda freebayes
conda update -c bioconda freebayes
freebayes --version
# Run QC
fastqc ./ERR5743893_1.fastq.gz  ./ERR5743893_2.fastq.gz -0 qc_reports/
# index reference file and then Map/Align
bwa index ./MN908947.fasta
gunzip ./ERR5743893_1.fastq.gz ./ERR5743893_2.fastq.gz
bwa mem MN908947.fasta ERR5743893_1.fastq ERR5743893_2.fastq > ERR5743893.sam
#Post-alignment processing (samtools)
samtools view -@ 20 -S -b ERR5743893.sam > ERR5743893.bam
samtools sort -@ 4 -o ERR5743893.sorted.bam ERR5743893.bam
samtools index ./ERR5743893.sorted.bam
samtools faidx MN908947.fasta
# Variant calling (VCF) file and visualization
freebayes -f MN908947.fasta ERR5743893.sorted.bam  > ERR5743893.vcf
cat ./ERR5743893.vcf


# Read Viral genomice variant calling file in wiki page for more details
