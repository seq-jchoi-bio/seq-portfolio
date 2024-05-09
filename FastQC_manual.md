Quality Check (QC) on Sequencing Files
=======================================
**Copyright 2020, Janghyun Choi, All rights reserved.**

![Platform](https://img.shields.io/badge/TestPlatform-linux_64,osx_64-orange) [![Original](https://img.shields.io/badge/Original-FastQC-DarkSeaGreen)](https://github.com/s-andrews/FastQC) [![GitHub release](https://img.shields.io/github/v/release/s-andrews/FastQC)](https://github.com/s-andrews/FastQC/releases) [![Build Status](https://img.shields.io/travis/s-andrews/FastQC/master)](https://travis-ci.org/s-andrews/FastQC)

Introduction
------------
This part performs the quality check (QC) on fastq sequencing files, which are received from the sequencing facility. The fastq files store raw sequencing data, capturing both the nucleotide sequence and its corresponding quality score. Various QC tools have been developed and modified, although we still recommend using FastQC originated by Babraham Bioinformatics. As FastQC is based on GUI, it can be easily accessible and intuitive. However, GUI-based FastQC provides single-core processing. To operate with multithreaded processing, python-coded FastQC is required. The following examples were established using Python-based FastQC version 0.11.9.

# Viewing fastq files
To understand the structure of fastq files, you can use the following commands:
    
    $ gzip -cd <filenames.gz> | head -<int> # -<int>: default is 5

**for example**

    $ gzip -cd shCon_Unt_1.fastq.gz | head -6
    $ gzip -cd shCon_Unt_1.fastq.gz | tail -10
    
## Output
When gzip finished typing, it prints messages showing fastq file look like this:
```python
$ gzip -cd shCon_Unt_1.fastq.gz | head -4
@A00718:626:HH5N7DSXC:3:1101:2067:1000 1:N:0:TTCTATAC+GGTTCCAA                                          # Line 1
NCCGCCGCCGACCACCAGAATACAGCTGTACATCTTACGCTTCGTCTCGTCCGACGCACAGGAGTCGATGCTGTGCAGGATGGCTTTATCGATGCCCAGAG   # Line 2
+                                                                                                       # Line 3
#FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF   # Line 4
```
## Description
- Line 1 specifies **“Header Line”** that contains the sequencing information, including platform ID, flow cell ID/coordinated, filtering, multiplex sequence.
- Line 2 specifies **“Sequence Line”** that represents the actual cDNA sequence. The letter “N” is used when a specific base cannot be determined.
- Line 3 specifies **“Plus Line”** that contains just a “+”, and this is ignored.
- Line 4 specifies **“Quality Score Line”** that displays the quality scores for each base as ASCII characters.

# Installation FastQC
To install FastQC and generate QC reports, follow these steps:

**Install FastQC via homebrew**
    
    $ brew install fastqc

**Install multiqc (to generate QC report)**
    
    $ pip install multiqc

# Usage FastQC
Use the following command to perform QC with FastQC:
    
```python
$ fastqc -o <output_folder> -f <format> -t <int> <input file-1> <input file-X>
```

In these commands:
- `-o`: specifies the output folder
- `-f`: specifies input file types
- `-t`: specifies the number of threads used in this operation
Multiple input files must be separated by spaces.

## Example code
Here is an example command to perform QC on multiple fastq files:
```python
$ fastqc -o ~/Desktop/Fastq/FastQC/ -f fastq -t 10 shCon_H2O2_1.fastq.gz shCon_Unt_1.fastq.gz shMLL1_NP_1.fastq.gz shNRF2_H2O2_1.fastq.gz shNRF2_Unt_1.fastq.gz shUTX_NP_1.fastq.gz shCon_H2O2_2.fastq.gz shCon_Unt_2.fastq.gz shMLL1_NP_2.fastq.gz shNRF2_H2O2_2.fastq.gz shNRF2_Unt_2.fastq.gz shUTX_NP_2.fastq.gz shCon_NP_1.fastq.gz shMLL1_H2O2_1.fastq.gz shMLL1_Unt_1.fastq.gz shNRF2_NP_1.fastq.gz shUTX_H2O2_1.fastq.gz shUTX_Unt_1.fastq.gz shCon_NP_2.fastq.gz shMLL1_H2O2_2.fastq.gz shMLL1_Unt_2.fastq.gz shNRF2_NP_2.fastq.gz shUTX_H2O2_2.fastq.gz shUTX_Unt_2.fastq.gz
```

# Export report via multiqc
To export the QC report, use the following command:

    $ multiqc ./

This command can only be executed in the desired directory and cannot run with an absolute path.
