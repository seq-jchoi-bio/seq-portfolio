© 2022 Janghyun Choi<br>This work is licensed under a [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-nc-sa/4.0/).<br>![Docer](https://img.shields.io/badge/Revised%20version-5%2E15%2E24-green?style=flat&logo=Markdown&colorA=black) [![CC BY-NC-SA 4.0](https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-green?labelColor=black)](http://creativecommons.org/licenses/by-nc-sa/4.0/)

# Genozip: a lossless Compressor for Sequencing Data
Genozip is a powerful tool specifically designed for the compression of life sciences data. It excels in efficiently handling and optimizing storage space for large genomic sequence data files. Genozip delivers high compression ratios and speeds, simplifying the transfer and storage of large and complex datasets. Additionally, it supports users in managing their data more quickly and economically, facilitating research and data analysis. While Genozip is a commercial product, it is available for free for academic use, ensuring accessibility for research purposes. This protocol was created based on **Genozip version 13.0.16** running on a system equipped with an Intel 10th generation i9-10910 processor and 48GB of memory. The test environment includes **Python version 3.8.5 and conda version 22.9.0 under macOS 12.4 environment.**

**Disclaimer**: <u>The author does not assume any responsibility for problems that may arise due to the copyright restrictions of Genozip, which is provided free of charge for academic use only. Users are encouraged to ensure compliance with all applicable terms and conditions.</u> **[License](https://www.genozip.com/license)**

# Installation Genozip
![EnvTest](https://img.shields.io/badge/macOS-000000?style=flat&logo=apple&logoColor=white) ![EnvTest](https://img.shields.io/badge/Ubuntu-E95420?style=flat&logo=ubuntu&logoColor=white) [![gith](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github&logoColor=white)](https://github.com/divonlan/genozip) [![web](https://img.shields.io/badge/Official-e34c26?style=flat&logo=html5&logoColor=white)](https://www.genozip.com) [![Version](https://img.shields.io/github/v/release/divonlan/genozip?style=flat&labelColor=black)](https://github.com/divonlan/genozip/releases/latest) ![Status](https://img.shields.io/badge/status-stable-DarkSeaGreen?labelColor=black)

To install Genozip, use the following Conda commands to add the necessary channel and install the software:
```bash 
$ conda config --add channels conda-forge
$ conda install genozip
```

# Step-by-Step Guide by File Extension
All output is generated in the form of `.genozip`.
### 1. FASTQ Files
FASTQ files are typically provided in gzipped format, which already offers an efficient compression ratio. Since these files represent the raw sequence data commonly published on ENA (European Nucleotide Archive) or GEO (Gene Expression Omnibus), it is essential to maintain their integrity. Therefore, to ensure the preservation of the original data quality and integrity, it is advisable to continue using `gzip` compression for FASTQ files rather than switching to Genozip. Gzip not only provides satisfactory compression but also guarantees that the data remains in a widely recognized and compatible format for these databases.

### 2. Trimmomatic FASTQ Files
Trimmomatic processing results in paired and unpaired forward/reverse FASTQ files. For transcriptome analysis focusing on differential gene expression, it is practical to compress only the paired sets.
- **Prepare Reference Genome**: First, create a reference genome for efficient compression:
    ```bash
    # Guide
    $ genozip --make-reference <referenceGenome.fa.gz>
    
    # For example
    $ genozip --make-reference GRCh38.primary_assembly.genome.fa.gz
    ```
(Reference genome can be downloaded from GENCODE or Ensembl as annotation fasta databases.)
- **Compress Paired FASTQ Sets**: Use the following commands to compress paired FASTQ files:
    ```bash
    # Guide
    $ genozip --reference <reference_.genozip> --pair <pair1.fq.gz> <pair2.fq.gz>

    # For exmaple
    $ genozip --reference GRCh38.primary_assembly.genome.ref.genozip --pair Trim_shCon_H2O2_1.fq.gz Trim_shCon_H2O2_2.fq.gz
    $ genozip --reference GRCh38.primary_assembly.genome.ref.genozip --pair Trim_shCon_NP_1.fq.gz Trim_shCon_NP_2.fq.gz
    $ genozip --reference GRCh38.primary_assembly.genome.ref.genozip --pair Trim_shCon_Unt_1.fq.gz Trim_shCon_Unt_2.fq.gz
    ```
- **Storage**: Move the resulting .genozip files to an external HDD.


### 3. SAM Files
The resulting SAM files from sequencing mapping can be prohibitively large, often presenting substantial challenges in terms of storage and handling. Genozip addresses this issue adeptly, providing an effective compression solution that significantly reduces the size of SAM files. By utilizing Genozip, researchers can achieve a more manageable file size, enhancing the efficiency of data storage and transfer processes. This compression capability ensures that extensive genomic data can be stored more economically and accessed more readily.

- Compress SAM files using the following command and then transfer the .genozip files to an external HDD:
    ```bash
    # Guide
    $ genozip <input_samfile.sam>

    # For exmaple
    $ genozip shCon_H2O2.sam
    $ genozip shCon_NP.sam
    $ genozip shCon_Unt.sam
    ```

### 4. BAM Files
BAM files, while being compressed forms of SAM files, still possess considerable size. The complexity and size of BAM files further increase as they undergo various processing stages of the `samtools` such as `view`, `rmdup`, and `sort`, which generate multiple individual files. Although compressing BAM files using Genozip alone might not yield optimal efficiency, combining Genozip compression with `tar` archiving significantly reduces the overall file size. This method effectively decreases the storage footprint, thereby enhancing the manageability of genomic data.

- Compress SAM files using the following command and then transfer the .genozip files to an external HDD:

    ```bash
    # Guide
    $ genozip --tar <output.tar> <input1.bam> <input2.bam> ... <inputN.bam>

    # For example
    $ genozip --tar viewBAM.tar shCon_D0.bam shCon_D7.bam shNFIB_D0.bam shNFIB_D7.bam shMLL1_D0.bam shMLL1_D7.bam
    $ genozip --tar rmdupBAM.tar rmdup.shCon_D0.bam rmdup.shNFIB_D0.bam rmdup.shMLL1_D0.bam rmdup.shCon_D7.bam rmdup.shNFIB_D7.bam rmdup.shMLL1_D7.bam
    ```