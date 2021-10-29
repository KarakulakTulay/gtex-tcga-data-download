## Download GTEx and TCGA Controlled RNA-Seq Data

To access controlled data in GTEx and TCGA, one first shoukd apply for data access. More information how you can apply for data access can be found in this page: https://dbgap.ncbi.nlm.nih.gov/aa/wga.cgi?page=login

The following steps assumes that you already have access to both databases and you want to download them into your server.

## GTEx
GTEx data are stored on the database of Genotypes and Phenotypes (dbGaP)
### Download SRA and BAM RNA-Seq controlled data via DBGaP
### Steps
1. Login into your account (https://dbgap.ncbi.nlm.nih.gov/aa/wga.cgi?page=login).
2. Choose 'My Project' under 'Authorized Access Button'.
3. Click on 'run selector' next to the project you want to work on.
4. Check box 'DATASTORE filetype' under 'Filters List' --> Check boxes 'SRA' & 'BAM' under 'DATASTORE filetype'.
5. Check 'Assay Type' and click on 'rna-seq'.
6. Search for data (e.g Prostate, Skin, SRR id etc), Select items you want to download (you can select all files).
7. Download Metadata - Metadata includes SRR ids in the first column (which will be used to download data later).
7. Download repository key: Get it by clicking 'get dbGaP repository key' next to your project under 'My Projects' section.
8. Download sra-toolkit lastest version:
- GitHub Page for sra-toolkit: https://github.com/ncbi/sra-tools
- Download Page for sra-toolkit: https://github.com/ncbi/sra-tools/wiki/01.-Downloading-SRA-Toolkit
- User's Guide: https://www.ncbi.nlm.nih.gov/sra/docs/sra-dbgap-download/

9. Command lines to download the data

9.1 Take SRR ids from Metadata file
less -S SraRunTable.txt | sed 's/,/ /g' | awk '{print $1}' | less > SRR_Acc_List.txt

9.2 Fetch the data from dbGaP
for srr in $(cat SRR_Acc_List.txt); do sratoolkit.2.11.2-ubuntu64/bin/prefetch --max-size 24G --ngc prj.ngc $srr; done >& out.txt &
This command will download the sra files.

9.3 Convert SRA data into fastq files & compress them
for srr in $(cat SRR_Acc_List.txt); do cd $srr; sratoolkit.2.11.2-ubuntu64/bin/fasterq-dump *.sra -e 8 --ngc prj.ngc; gzip *fastq; cd ..; done >& out_conversion.txt &
This command will generate two fastq files if the run is paired-end. Otherwise one fastq file will be generated.

## TCGA
TCGA data are stored on the Genomic Data Common Data Portal (GDC Data Portal).
Documentation on how to download and upload data: https://docs.gdc.cancer.gov/Data_Transfer_Tool/Users_Guide/Preparing_for_Data_Download_and_Upload/

### Steps
1. Login into your account: https://portal.gdc.cancer.gov/
2. Download token -  Dropdown menu under your name when you login. Token file is necessary to be able to download controlled data on your server
3. Search for data you have access and you want to work on in the search box on the main page.
4. Choose 'RNA-Seq' under experimental strategy section
5. Go to 'Files' section on the left-hand side of the page and select 'bam' file under 'Data Format' section
6. Select 'Files' in the main page and add everything (or part of data) into the Cart
7. Click on the Cart (right-corner of the page)
8. Under 'Download' section, download the manifest file.
9. Download GDC Data Transfer Tool
- Download Page for GDC-Data-Transfer-Tool: https://gdc.cancer.gov/access-data/gdc-data-transfer-tool
- User's Guide: https://docs.gdc.cancer.gov/Data_Transfer_Tool/Users_Guide/Getting_Started/
10. Command Line to download bam Files
gdc-client download -m manifest_file -t tokenfile
token_file: see step 2
manifest_file: see step step 8
