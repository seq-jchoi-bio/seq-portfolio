© 2022 Janghyun Choi<br>This work is licensed under a [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-nc-sa/4.0/).<br>![Docer](https://img.shields.io/badge/Revised%20version-5%2E15%2E24-green?style=flat&logo=Markdown&colorA=black) [![CC BY-NC-SA 4.0](https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-green?labelColor=black)](http://creativecommons.org/licenses/by-nc-sa/4.0/)

# Alignment with Reference Genome using HISAT2
A first key step in RNA-seq is to align short reads to a reference genome. Several mappers have been developed according to various sample types and experimental conditions. HiSAT2 that is coded in python and a spcialized algorithm for transcriptome analysis can be fast and exactly maapped to a reference genome for whole-genome, transcriptome, and exome sequencing data. HiSAT2 can run on any computer installed on Linux or macOS and operates on python version > 2.6. As the alignment process takes a very long time and spends a lot of computational resources, the minimum spec of hardware is relatively higher than other bioinformatic tools (threads > 4, memory > 16 Gb ). This protocol was created based on **HISAT version 2.2.1** running on a system equipped with an Intel 10th generation i9-10910 processor and 48GB of memory. The test environment includes **Python version 3.8.5, SciPy version 1.6.2, NumPy version 1.20.1, and pySam version 0.16.0.1 under macOS 12.4 environment.**

# Installation HISAT2 
![EnvTest](https://img.shields.io/badge/macOS-000000?style=flat&logo=apple&logoColor=white) ![EnvTest](https://img.shields.io/badge/Ubuntu-E95420?style=flat&logo=ubuntu&logoColor=white) [![gith](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github&logoColor=white)](https://github.com/DaehwanKimLab/hisat2) [![githio](https://img.shields.io/badge/GitHub.io-181717?style=flat&logo=github&logoColor=yellow)](https://daehwankimlab.github.io/hisat2/) [![GitHub release](https://img.shields.io/github/v/release/DaehwanKimLab/hisat2?labelColor=black)](https://github.com/BenLangmead/bowtie2/releases) ![Status](https://img.shields.io/badge/status-stable-DarkSeaGreen?labelColor=black)

To install HISAT2 via anaconda, use the following commands:

```bash
$ conda install -c bioconda hisat2
# OR
$ conda install -c bioconda/label/cf201901 hisat2
```

### Establish Genome Builder
1. Download desired **index files** from official HISAT2 site.
2. Uncompress it and move desired folder.

### Prepare Genome Builder using `hisat2-build` if not available on suitable index file
`hisat2-build` builds a HISAT2 index from a set of DNA sequences. hisat2-build outputs a set of 6 files with suffixes <i>.1.ht2, .2.ht2, .3.ht2, .4.ht2, .5.ht2, .6.ht2, .7.ht2, and .8.ht2.</i> Use the following command to prepare the genome builder with `hisat2-build`:
```bash         
$ hisat2-build <genome sequence.fa> <output folder>
```    
**NOTE**, The genome sequence file downloads the fa format of the **'toplevel' (e.g. species.version.dna.toplevel.fa)** provided by the **ENSEMBL**.

- Here is an example command to perform hisat2-build on the reference file:
    ```bash    
    $ hisat2-build /Users/jchoi/Desktop/Build/Danio_rerio.GRCz11.dna.toplevel.fa \
        /Users/jchoi/Desktop/Build/Danio_rerio_index
    ```
- When hisat2-build finished typing, it prints messages processing genome builder file look like this:

    ```
        Settings:
            Output files: "/Users/jchoi/Desktop/Build/Danio_rerio_index.*.ht2l"
            Line rate: 7 (line is 128 bytes)
            Lines per side: 1 (side is 128 bytes)
            Offset rate: 4 (one in 16)
            FTable chars: 10
            Strings: unpacked
            Local offset rate: 3 (one in 8)
            Local fTable chars: 6
            Local sequence length: 57344
            Local sequence overlap between two consecutive indexes: 1024
            Endianness: little
            Actual local endianness: little
            Sanity checking: disabled
            Assertions: disabled
            Random seed: 0
            Sizeofs: void*:8, int:4, long:8, size_t:8
        Input files DNA, FASTA:
            /Users/jchoi/Desktop/Build/Danio_rerio.GRCz11.dna.toplevel.fa
        Reading reference sizes
            Time reading reference sizes: 00:02:37
        Calculating joined length
        Writing header
        Reserving space for joined string
        Joining reference sequences
            Time to join reference sequences: 00:03:47
            Time to read SNPs and splice sites: 00:00:00
        Using parameters --bmax 313970597 --dcv 1024
        Doing ahead-of-time memory usage test
        Passed!  Constructing with these parameters: --bmax 313970597 --dcv 1024
        Constructing suffix-array element generator
        Building DifferenceCoverSample
            Building sPrime
            Building sPrimeOrder
            V-Sorting samples
            V-Sorting samples time: 00:00:35
            Allocating rank array
            Ranking v-sort output
            Ranking v-sort output time: 00:00:07
            Invoking Larsson-Sadakane on ranks
            Invoking Larsson-Sadakane on ranks time: 00:00:14
            Sanity-checking and returning
        Building samples
        Reserving space for 12 sample suffixes
        Generating random suffixes
        QSorting 12 sample offsets, eliminating duplicates
        QSorting sample offsets, eliminating duplicates time: 00:00:00
        Multikey QSorting 12 samples
            (Using difference cover)
            Multikey QSorting samples time: 00:00:00
        Calculating bucket sizes
        Splitting and merging
            Splitting and merging time: 00:00:00
        Split 1, merged 6; iterating...
        Splitting and merging
            Splitting and merging time: 00:00:00
        Split 1, merged 0; iterating...
        Splitting and merging
            Splitting and merging time: 00:00:00
        Avg bucket size: 2.09314e+08 (target: 313970596)
        Converting suffix-array elements to index image
        Allocating ftab, absorbFtab
        Entering GFM loop
        Getting block 1 of 8
            Reserving size (313970597) for bucket 1
            Calculating Z arrays for bucket 1
            Entering block accumulator loop for bucket 1:
            bucket 1: 10%
            bucket 1: 20%
            bucket 1: 30%
            bucket 1: 40%
            bucket 1: 50%
            bucket 1: 60%
            bucket 1: 70%
            bucket 1: 80%
            bucket 1: 90%
            bucket 1: 100%
            Sorting block of length 194086053 for bucket 1
            (Using difference cover)
            Sorting block time: 00:01:59
        Returning block of 194086054 for bucket 1
        ...............
        Returning block of 197484859 for bucket 8
        Exited GFM loop
        fchr[A]: 0
        fchr[C]: 531052068
        fchr[G]: 837464927
        fchr[T]: 1143883918
        fchr[$]: 1674509851
        Exiting GFM::buildToDisk()
        Returning from initFromVector
        Wrote 567251040 bytes to primary GFM file: /Users/jchoi/Desktop/Build/Danio_rerio_index.1.ht2l
        Wrote 837254932 bytes to secondary GFM file: /Users/jchoi/Desktop/Build/Danio_rerio_index.2.ht2l
        Re-opening _in1 and _in2 as input streams
        Returning from GFM constructor
        Returning from initFromVector
        Wrote 777489661 bytes to primary GFM file: /Users/jchoi/Desktop/Build/Danio_rerio_index.5.ht2l
        Wrote 426205032 bytes to secondary GFM file: /Users/jchoi/Desktop/Build/Danio_rerio_index.6.ht2l
        Re-opening _in5 and _in5 as input streams
        Returning from HGFM constructor
        Headers:
            len: 1674509851
            gbwtLen: 1674509852
            nodes: 1674509852
            sz: 418627463
            gbwtSz: 418627464
            lineRate: 7
            offRate: 4
            offMask: 0xfffffffffffffff0
            ftabChars: 10
            eftabLen: 0
            eftabSz: 0
            ftabLen: 1048577
            ftabSz: 8388616
            offsLen: 104656866
            offsSz: 837254928
            lineSz: 128
            sideSz: 128
            sideGbwtSz: 96
            sideGbwtLen: 384
            numSides: 4360703
            numLines: 4360703
            gbwtTotLen: 558169984
            gbwtTotSz: 558169984
            reverse: 0
            linearFM: Yes
        Total time for call to driver() for forward index: 00:35:21
    ```

# Running HISAT2 (Usage)
Use the following command to perform mapping to the genome with `hisat2`:
```bash
$ hisat2 -x <GenoemeBuilder> -1 <forward> -2 <reverse> -S <output.sam> <OptionalParameters>
```
In these commands,
- **`-x <GenoemeBuilder>`**: Specifies the genome index. Since index files consist of `genome.X.ht2`, the builder command must be typed as follows; `/genome/builder/path/genome`
- **`-1, -2`**: Specifies the forward (`-1`) and reverse (`-2`) input files. Support gz commpressed fastq (fq) files.
- `-S <output.sam>`: Specifies the output file type and name.
<br>
- The `<OptionalParameters>` offer a wide range of variables as bellow, see the HISAT2's manual for details.

    | Parameter | Description |
    |----|----|
    | `-5/--trim5 <int`, `-3/--trim3 <int>` | Trim <int> bases from 5' (left) or 3' (right) end of each read before alignment (default: 0). |
    | `--mp MX,MN` | Sets the maximum (MX) and minimum (MN) mismatch penalties, both integers.  |
    | `--sp MX,MN` | Sets the maximum (MX) and minimum (MN) penalties for soft-clipping per base, both integers. |
    | `--np <int>` | Sets penalty for positions where the read, reference, or both, contain an ambiguous character such as N.  |
    | `--dta-cufflinks` option | Report alignments tailored specifically for Cufflinks. In addition to what HISAT2 does with the above option (--dta), With this option, HISAT2 looks for novel splice sites with three signals (GT/AG, GC/AG, AT/AC), but all user-provided splice sites are used irrespective of their signals. |
    | `-k <int>` | It searches for at most <int> distinct, primary alignments for each read. Primary alignments mean alignments whose alignment score is equal or higher than any other alignments (Default: 5).   |
    | `-p <int>` | The number of CPU threads HISAT program will use when executing multi-processing tasks.|


## Example Code
Here is an example command to perform alignment with the human hg19 genome on trimmed fastq files:
```bash
$ hisat2 -x /Users/jchoi/Desktop/RNA-seq/GenomeIndex/hg19_HiSat2Builder/genome \
    -1 /Users/jchoi/Desktop/Trim/Trim_shCon_H2O2_1.fq.gz \
    -2 /Users/jchoi/Desktop/Trim/Trim_shCon_H2O2_2.fq.gz \
    -S /Users/jchoi/Desktop/SAM/shCon_H2O2.sam -p 10 --dta-cufflinks -k 5
```

## Output
When HISAT2 finishes running, it prints messages summarizing what happened.
```
24403217 reads; of these:
  24403217 (100.00%) were paired; of these:
    3213764 (13.17%) aligned concordantly 0 times
    18006379 (73.79%) aligned concordantly exactly 1 time
    3183074 (13.04%) aligned concordantly >1 times
    ----
    3213764 pairs aligned concordantly 0 times; of these:
      316476 (9.85%) aligned discordantly 1 time
    ----
    2897288 pairs aligned 0 times concordantly or discordantly; of these:
      5794576 mates make up the pairs; of these:
        3288372 (56.75%) aligned 0 times
        1992820 (34.39%) aligned exactly 1 time
        513384 (8.86%) aligned >1 times
93.26% overall alignment rate
```
- This message can also be outputted by `samtools`. 
- **Note,** the subsequent process utilizes the resulting 'SAM' file.

# Citation
### HISAT2
**Kim, D.**, Paggi, J. M., Park, C., Bennett, C., & Salzberg, S. L. (2019). **Graph-based genome alignment and genotyping with HISAT2 and HISAT-genotype**. *Nature biotechnology*, 37(8), 907-915. [DOR](https://doi.org/10.1038/s41587-019-0201-4)