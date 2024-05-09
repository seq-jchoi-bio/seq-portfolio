Alignment with Reference Genome using Bowtie2
============================================
**Copyright 2020, Janghyun Choi, All rights reserved.**

![Platform](https://img.shields.io/badge/TestPlatform-linux_64,osx_64-orange) [![Original](https://img.shields.io/badge/Original-Bowtie2-DarkSeaGreen)](https://github.com/BenLangmead/bowtie2) [![GitHub release](https://img.shields.io/github/v/release/BenLangmead/bowtie2)](https://github.com/BenLangmead/bowtie2/releases) ![Random Tests](https://github.com/BenLangmead/bowtie2/actions/workflows/random-tests.yml/badge.svg) ![Simple Tests](https://github.com/BenLangmead/bowtie2/actions/workflows/simple-tests.yml/badge.svg)

Introduction
------------
Bowtie2 that is an ultrafast and memory-efficient tool coded in python can be aligned sequencing reads to long reference genomes. Notably, **Bowtie is suitable for ChIP-seq or ATAC-seq, but not RNA-seq.** If you have to analyze transcriptome, go to the section on RNA-seq using HISAT2 tool. Bowtie can run on any computer installed on Linux or macOS and works in python version > 2.6. This protocol was created based on **Bowtie2 version 2.5.1** running on a system equipped with an Intel 10th generation i9-10910 processor and 48GB of memory. The test environment includes **Python version 3.8.5, SciPy version 1.6.2, NumPy version 1.20.1, and pySam version 0.16.0.1 under macOS 12.4 OS environment. This course takes very long time and spends a lot of RAM and CPU resource.**

# Installation bowtie2
To install bowtie2, follow these steps:

**Install bowtie2 via homebrew**
```python 
$ brew install bowtie2
```

# Establish genome builder
`Bowtie2-build` builds a bowtie index from a set of DNA sequence. This builder consists of a set of 6 files with suffixes .1.bt2, .2.bt2, .3.bt2, .4.bt2, .rev.1.bt2, and .rev.2.bt2. These files together constitute the index for the alignment reads to that reference. You can download from the index section of official website all the files for a given assembly as a single zip file, or as 6 seperate bt2 files. There are two types of genome builder method; manual and automatic.
1. Automatic method: uncompress builder files and uses 'bowtie2-build' or 'make_XX_.sh' syntax. For more information, refer to Bowtie2's manual.
2. Manual method: uncompress builder files and moves them to the desired path.

**REMEMBER,** The build step is a prerequisite for the main sequence.
Unlike "HISAT2", this tool provides index files for most species from the index zone of official site.

# Running bowtie2 (Usage)
Use the following command to perform mapping to the genome with bowtie2:
```python
# Usage for single-end
$ bowtie2 -x <build genome> -U <reads.fq> -S <output.sam> --local --very-fast-local -p <INT>
# Usage for pair-end
$ bowtie2 -x <build genome> -1 <forward_reads.fq> -2 <reverse_reads.fq> \
        -S <output.sam> --local --very-fast-local -p <INT>
```
In these commands,
- `-x` option: specifies reference genomes as builder format.
- `-U` option (only SE mode): specifies input read sequence.
- `-1` or -2 options (only PE mode): specifies forward input read and reverse input read, respectively.
- `-S` option: specifies output file.
- `--local` option: Bowtie algorithm serches for alignments involving all of the read characters. This is also called an 'untrimmed' or 'unclipped' alignment. In the local mode, Bowtie 2 might 'trim' or 'clip' some read characters from one or both ends of the alignment if doing so maximizes the alignment score. Default is `end-to-end` mode.
- `--very-fast-local` option: preset options (only local mode), more information, refer to Bowtie2's manual.
- `-p [INT]`: number of processors to use in addition to main thread [INT].

## Example code
Here is an example command to perform alignment with the mouse mm10 genome on trimmed fastq files:
```python
$ bowtie2 -x /Users/jchoi/Desktop/mm10/mm10 \
    -1 /Users/jchoi/Desktop/Trim/Trim_pair_NFIB_1.fq.gz \
    -2 /Users/jchoi/Desktop/Trim/Trim_pair_NFIB_2.fq.gz \
    -S /Users/jchoi/Desktop/NFIB.sam --local --very-fast-local -p 20
```

## Output
When bowtie2 finishes running, it prints messages summarizing what happened.
```plaintext
23156585 reads; of these:
  23156585 (100.00%) were paired; of these:
    2044536 (8.83%) aligned concordantly 0 times
    11769262 (50.82%) aligned concordantly exactly 1 time
    9342787 (40.35%) aligned concordantly >1 times
    ----
    2044536 pairs aligned concordantly 0 times; of these:
      686243 (33.56%) aligned discordantly 1 time
    ----
    1358293 pairs aligned 0 times concordantly or discordantly; of these:
      2716586 mates make up the pairs; of these:
        1327258 (48.86%) aligned 0 times
        453087 (16.68%) aligned exactly 1 time
        936241 (34.46%) aligned >1 times
97.13% overall alignment rate
```
**This message can be additionally outputted by `samtools`, noting that the subsequent process utilizes the resulting 'SAM' file**