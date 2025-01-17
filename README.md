# GWAS-gene-discovery
Tool for discovering potential genes of interest from output of GWAS analysis software.




## Overview
The instructions below provides users with a copy of this program which can be directly used or adapted for their own project. 
The first component of the program searches Ensembl database using the [species overlap API](https://rest.ensembl.org/documentation/info/overlap_region) and returns IDs of genes positioned within a designated base-pair distance around each SNP statistically associated to a phenotype. As indicated by API description in the link, ID of genes partially overlapping with but not fully inside the region will also be recovered.

The second component then input the gene IDs into Knetminer in addition to key word descriptions of the phenotypes contained within a plain text file. This will return for each gene a hyperlink to the network visualisations displaying QTLs, orthologs, publications etc related to the gene therefore accelerating and generating new insights for research.




## Prerequisites
* The script can run on any computer with access to Python2.7 and above or any versions of Python3. 

* The program does not require heavy computational resources. However, as the user may prefer high performance computing the instructions on how to set up and run the program on a node managed by the Easybuild framework has been included below. The User should read set up instructions specific to any other HPC frameworks.

* Python virtual environments, e.g. virtualenv for python2 or pyvenv for python3. If the user does not have root permission on Easybuild a virtual environment is required for installation of requests, numpy and pandas through pip. See **4.Installing python request library** in **Instructions** on how to do this.




## Tutorial and usage instructions
This is a quick tutorial to get the user started by reproducing the outputs of map_snp_to_gene.py for 2 different GWAS result spreadsheets, GAPIT.MLM.DTF.GWAS.Results.csv and GAPIT.MLM.blupWidth.GWAS.Results.csv as seen in the 2 directories of the same names. The tutorial generally assumes the user is using linux managed by Easybuild.

#### 1.Downloading the repository
Clone this repository with the GitHub URL using either Git or a Git GUI. The user should obtain a directory named gwas-gene-discovery containing identical contents to the GitHub repository.

#### 2. Accessing compute node on Easybuild
The user can check available compute nodes by the command:
```
sinfo 
```
If available, login to a standard compute node on Rothhpc4 using:
```
srun --pty bash -i
```

#### 3.Setting up a virtual environment on Easybuild
A virtual environment is required for pip installation of numpy, pandas and requests.
Check all the available versions of python currently on cluster:
```
module avail Python
```
After the user has decided on a version of Python2 that is 2.7 and above or Python3 execute the commands below.

```
module load <Python version>
virtualenv <name of Python virtual environment>
source </path to env>/bin/activate/
```
The user can return to the virtual environment in a new session after logging out by:
```
module load <Python version>
source </path to env>/bin/activate/
```
  
#### 4.Installing python request library
Requests is needed for the steps that send HTTP request protocols found in the script while Numpy and Pandas are required for tabulating information from Knetminer API. Pandas library includes Numpy therefore use pip to install the 2 libraries:
```
pip install requests
pip install pandas
```

#### 5.Execution of script
The command:
```
python map_snp_to_gene.py -h
```
Returns the usage of the script as the following:
```
usage: map_snp_to_gene.py [-h] [--logP LOGP] [--distance DISTANCE]
                          file list species
```
The **mandatory arguments** are:
* File. A spreadsheet containing the results of GWAS analysis. The columns of spreadsheet should be arranged in the order below (examples being 2 csv files in repository).:

  SNP [integer], Chromosome [integer], Position [integer], P.value [float]

* List. A plain text file containing one or more short description of the phenotype or phenotypes genes of interest are suspected to influence, listed line by line. An example list, mock_keyword_list.txt can be found in the repository.

* Species. The species of organism subjected to gwas which the script will return gene IDs specific to. The options are currently 3 species represented by an integer value as shown below:


     * 1 represents rice (Japonica variety only)
     * 2 represents wheat
     * 3 represents arabidopsis


The **optional arguments** are:
* logPthreshold: -log10(p-value of SNPs). It is used to extract SNPs strongly associated to phenotypes of interest as indicated by the association test of GWAS. The default value is 6 as per standard of majority of GWAS research papers. This can be provided but as an integer. e.g --logP 7 for 7 as the threshold.
* Distance: The distance in base-pairs upstream and downstream from a SNP exceeding loPthreshold. All genes positioned within this genomic length are returned. 1kbp is the default value and this means a SNP occurence is upstream, downstream or within gene or genes located in 2kbp region window. This again can be provided but must be an integer. e.g. --distance 2000 for 2kbp.

If the User has either a standard or Easybuild terminal set up with requests and pandas installed, they can run the following commands. This will reproduce the directories: /MLM.blupWidth.GWAS.Results and /MLM.DTF.GWAS.Results which are example outputs produced with all optional parameters set to default for the 2 case study spreadsheets in repository. The 2 spreadsheets came from a GWAS study on Japonica variety of rice.

```
python map_snp_to_gene.py GAPIT.MLM.DTF.GWAS.Results.csv mock_keyword_list.txt 1
```
```
python map_snp_to_gene.py GAPIT.MLM.DTF.GWAS.Results.csv mock_keyword_list.txt 1
```

#### 6. Output information
Inspect directories /MLM.blupWidth.GWAS.Results and /MLM.DTF.GWAS.Results. The script has produced a directory of the same name for each input spreadhseet. Within each directory, 3 files can be found:
* filtered_snps.txt. This lists all the significant SNPs incrementally named from 1. Use the row number in spreadsheet to track the exact SNP ID. e.g. In /MLM.blupWidth.GWAS.Results, SNPnum 36524 in filtered_snps.txt is the 36524th SNP found in the input CSV spreadsheet, its ID is 23974957.
* summary_genes_discovered.txt. This contains the significant SNPs and the geneIDs found within the user-defined or default distance around the SNP. Additional information include for each gene a Knetscore and a genepage URL to Knetminer's network view . The Knetscore column assesses the relevance of the gene to the trait or traits of interest provided in the keyword list file. The higher the knetscore, the more likely the gene is to influence the trait. The network view column contains URL addresses to networks of Knetminer which displays related orthologues, other traits encoded by the gene and publications etc. 

Rerunning the script will overwrite the previous results in the directory.


## External tools included
Ensembl rest server. [Overlap species API](https://rest.ensembl.org/documentation/info/overlap_region)


[Knetminer V3.0](https://knetminer.rothamsted.ac.uk/KnetMiner/)



## Authors
Keywan-Hassani Pak


Colin Li



## Acknowledgement
Ensembl variation resources
Database Volume 2018


Bioinformatics Department, Computational and Analytical Sciences, Rothamsted Research


