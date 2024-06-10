File Conversion and Management with SAMtools
============================================
**Copyright 2020, Janghyun Choi, All rights reserved.**

![Platform](https://img.shields.io/badge/TestPlatform-linux_64,osx_64-orange) [![Original](https://img.shields.io/badge/Original-samtools-DarkSeaGreen)](https://github.com/samtools/samtools) [![GitHub release](https://img.shields.io/github/v/release/samtools/samtools)](https://github.com/samtools/samtools/releases) [![Build status](https://ci.appveyor.com/api/projects/status/enujqi06jlqw493t/branch/develop?svg=true)](https://ci.appveyor.com/project/samtools/samtools/branch/develop) 

Introduction
------------
The reads mapped onto the reference genome generate substantial data files in SAM format, ranging from 20 to 30 GB per sample, which could result in prolonged further analysis durations. `SAMtools` that is coded in python can be converted SAM into BAM to fast access the sequence data and analyze the sequence depth. This protocol was created based on **SAMtools version 1.12** running on a system equipped with an Intel 10th generation i9-10910 processor and 48GB of memory. The test environment includes **Python version 3.8.5, SciPy version 1.6.2, NumPy version 1.20.1, and pySam version 0.16.0.1 under macOS 12.4 OS environment.** The tool offers a variety of functions to analyze sequencing data, but this part will cover only the most commonly used features.

# Installation samtools
To install samtools, follow these steps:

**Install samtools via homebrew**
```python 
$ brew install Samtools
```

# Stats: Check the mapping rate
SAMtools `stats` collects statistics from SAM/BAM files and outputs in a text format. The output can be visualized graphically using `multiqc`. Use the following command to perform the `stats` function of samtools:
```python 
$ samtools stats -@ <int> <input sam file.sam> > <output file.stats>
```
In these commands,
- `-@ <int>`: specifies core numbers used in this work.
- `<input.sam>`: specifies input file as a SAM format.
- `> <output.stats>`: specifies output file as a stats format. '>' symbol must appear before the `<output file.txt>` syntax.

### Example code (Results are not printed)
```python
$ samtools stats -@ 10 /Users/jchoi/Desktop/SAM/Unt_UTX.sam > /Users/jchoi/Desktop/SAM/Unt_UTX.sam.stats 
```
Stats files can be opened text editor and it consists of alignment information.
```plaintext
# This file was produced by samtools stats (1.12+htslib-1.12) and can be plotted using plot-bamstats
# This file contains statistics for all reads.
# The command line was:  stats -@ 10 /Users/jchoi/Desktop/SAM/Unt_UTX.sam
# CHK, Checksum	[2]Read Names	[3]Sequences	[4]Qualities
# CHK, CRC32 of reads which passed filtering followed by addition (32bit overflow)
CHK	6b2cf2a8	0ec11f5c	a187d603
# Summary Numbers. Use `grep ^SN | cut -f 2-` to extract this part.
SN	raw total sequences:	45639340	# excluding supplementary and secondary reads
SN	filtered sequences:	0
SN	sequences:	45639340
SN	is sorted:	0
SN	1st fragments:	22819670
SN	last fragments:	22819670
SN	reads mapped:	40370690
SN	reads mapped and paired:	40243620	# paired-end technology bit set + both mates mapped
SN	reads unmapped:	5268650
SN	reads properly paired:	36659322	# proper-pair bit set
SN	reads paired:	45639340	# paired-end technology bit set
SN	reads duplicated:	0	# PCR or optical duplicate bit set
SN	reads MQ0:	227872	# mapped and MQ=0
SN	reads QC failed:	0
SN	non-primary alignments:	0
SN	supplementary alignments:	0
SN	total length:	4562450957	# ignores clipping
SN	total first fragment length:	2281908536	# ignores clipping
SN	total last fragment length:	2280542421	# ignores clipping
SN	bases mapped:	4038619347	# ignores clipping
SN	bases mapped (cigar):	3942644569	# more accurate
...................
```
### Export stats report via multiqc
The resulting stats files under the presence of the same folder can be compared and analyzed collectively with `multiqc`, use the following command:
```python
$ multiqc ./
```

# View: Convert to BAM file or View SAM or BAM file
SAMtools `view`, with no options or regions specified, prints all alignments in the specified input alignment file (in SAM, BAM, or CRAM format) to standard output in SAM format (with no header). **Ultimately,** the `view` function can convert text-format SAM files into binary BAM files and vice versa, enabling faster and more detailed manipulation of sequencing reads. Use the following command to perform the `view` function of samtools:
```python
# For explore the strucure of them.
$ samtools view <SAM or BAM files> | head -<int> ## Usage is the same as the 'gzip' and 'cat' commands.
 
 # For converting BAM file.
$ samtools view -b -q <int> -f <FLAG> -F <FLAG> -@ <int> <input.sam> > <output.bam>
```
In these commands,

| Parameter | Description |
|----|----|
| `-b` option | specifies output file format as a SAM. |
| `-q <int>` option | skip alignments with MAPQ smaller than <int>. <br>**Critical option,** A MAPQ > 30 is considered the highest quality.|
| `-f <FLAG>` option | only output alignments with all bits set in `<FLAG>`. |
| `-F <FLAG>` option | Do not include output alignments with any bits set in `<FLAG>`. <br>**FLAG field refers as bellow table.** <br> `-f` and `-F` options cannot be used at the same time (mutually exclusive). |
| `-@ <int>` option | specifies thread numbers `<int>`. |
| `<input.sam>` | specifies input sam file. |
| `> <output.bam>` | specifies output bam file. '>' symbol must appear before the `<output.bam>` syntax. |

There are no parameters when viewing or converting a specific region, only the following format would be used: `chr:start-end`
<i>* For more information such as global parameter, refers to samtools' guidelines.</i>

#### FLAG field table
| Bit | Flag | Description |
| --- | ---- | ----------- |
| 0   | 0x1  | read paired |
| 1   | 0x2  | read mapped in proper pair |
| 2   | 0x4  | read unmapped |
| 3   | 0x8  | mate unmapped |
| 4   | 0x10 | read reverse strand |
| 5   | 0x20 | mate reverse strand |
| 6   | 0x40 | first in pair |
| 7   | 0x80 | second in pair |
| 8   | 0x100 | not primary alignment |
| 9   | 0x200 | read fails platform/vendor quality checks |
| 10  | 0x400 | read is PCR or optical duplicate |
| 11  | 0x800 | supplementary alignment |

### Example code (Results are not printed)
```python
$ samtools view -b -@ 20 shCon_Unt.sam > ~/Desktop/BAM/shCon_Unt.bam
$ samtools view -b -@ 20 shCon_H2O2.sam > ~/Desktop/BAM/shCon_H2O2.bam
$ samtools view -b -@ 20 shCon_NP.sam > ~/Desktop/BAM/shCon_NP.bam

# Convert to BAM file, which has a MAPQ score > 40 and marked FLAG field corresponded to 0x1 and 0x2.
$ samtools view -b -q 40 -f 0x1,0x2 -@ 20 shControl.sam > shControl.bam 

# Convert to BAM file from SAM file, which has a MAPQ score > 30 and excepts FLAG field corresponded to 0x8.
$ samtools view -b -q 30 -F 0x8 -@ 20 shNFIB.sam > shNFIB.bam 
```

# Rmdup: Remove duplicative reads
SAMtools `rmdup` removes potential PCR duplicates (only retain the pair with highest mapping quality). The official manual describes **"This command is obsolete. Use markdup instead."**, so it probably not operate in lastly versions. Use the following command to perform the `rmdup` function of samtools:
```python
$ samtools rmdup -s <input.bam> <output.bam>
```
In these commands,
- `-s/-S` option: `-s` is responsible for pair-end mode, whereas `-S` is for single-end mode.
- `<input.bam>`: specifies input data.
- `<output.bam>`: specifies output data.
**_rmdup does not provide multi-threads processing._**

### Example code and output
```python
$ samtools rmdup -s shCon_Unt.bam rmdup.shCon_Unt.bam
$ samtools rmdup -s shCon_H2O2.bam rmdup.shCon_H2O2.bam
$ samtools rmdup -s shCon_NP.bam rmdup.shCon_NP.bam
```
When `rmdup` finishes running, it prints messages summarizing what happened.
```plaintext 
After operation of rmdup, terminal appears to as follows,
    [bam_rmdupse_core] 590 / 46146253 = 0.0000 in library
```

# Sort: Sort reads by position or name
SAMtools `sort` function sorts BAM files by leftmost coordinates of references (chromosome position, no option) or by read name (`-n`option). In general, RNA-seq for feature counting (`htseq-count`) uses the `-n` option to sort by read name, while ChIP-seq for visualization or a BED file conversion does not use the `-n` option to sort by position. Use the following command to perform the `sort` function of samtools:
```python
$ samtools sort -n -@ <int> <input.bam> -o <output.bam>
```
In these commands,
- `-n` option: sort by name, if want to sort by position, remove `-n`.
- `-@` option: specifies thread numbers `<int>`.
- `<input.bam>`: specifies input file.
- `-o <output.bam>`: specifies output file.

### Example code and output
```python
# with -n option
$ samtools sort -n -@ 20 rmdup.shCon_Unt.bam -o sort.shCon_Unt.bam
$ samtools sort -n -@ 20 rmdup.shCon_H2O2.bam -o sort.shCon_H2O2.bam
$ samtools sort -n -@ 20 rmdup.shCon_NP.bam -o sort.shCon_NP.bam

# with no option
$ samtools sort -@ 20 /home/jh/Desktop/Bam/Rmdup_shControl.bam -o /home/jh/Desktop/Bam/Sort_shControl.bam
$ samtools sort -@ 20 /home/jh/Desktop/Bam/Rmdup_shNFIB.bam -o /home/jh/Desktop/Bam/Sort_shNFIB.bam
```
When `sort` finishes running, it prints messages summarizing what happened.
```plaintext 
After operation of sort, terminal appears to as follows,
	[bam_sort_core] merging from 20 files and 1 in-memory blocks...
```

# Index: for ChIP-seq analysis
SAMtools `index` function Indexes a coordinate-sorted BGZIP-compressed SAM, BAM, or CRAM file for fast random access. **This function requires a BAM file sorted by position.** Use the following command to perform the `index` function of samtools:
```python
$ samtools index <input.bam>
```
In these commands,
- `<input.bam>`: specifies input file.

This command has no parameters that specifically define the output. The resulting bai file is gennerated in the input folder. **Preferably, the sorted BAM file and the bai file should exist in the same directory for further analysis.**

### Example code (Results are not printed)
```python
$ samtools index /home/jh/Desktop/Bam/Sort_input.bam
$ samtools index /home/jh/Desktop/Bam/Sort_shControl.bam
```

# Coverage: Check the sequencing depth
SAMtools `coverage` function computes the coverage at each position or region and draws an ASCII-art histogram or tabulated text. The coverage is defined as the percentage of positions within each bin with at least one base aligned against it. **This function requires a BAM file sorted by position and a bai file.** Use the following command to perform the `coverage` function of samtools:
```python
$ samtools coverage -A -r <region> <input.bam>
```
In these commands,
- `-A` option: Show only ASCII characters in histogram using colon and fullstop for full and half height characters.
- `-r <region>`: Show specified region. Format: chr:start-end.
- `<input.bam>`: specifies input file.

### Example code and output
```python
# Coverage across all chromosome without ASCII histogram
$ samtools coverage sort_MA_coverage.bam

# Coverage with ASCII histogram on chr1
$ samtools coverage -A -r chr1 sort_MA_coverage.bam
```
When `coverage` with no option finishes running, it prints messages summarizing what happened.
```plaintext
#rname	numreads	covbases	coverage	meandepth	meanbaseq	meanmapq
1	1582614	6662248	11.1823	2.66237	36.3	53.9
2	1847826	7551970	12.6625	3.10498	36.3	56.5
3	2100124	8084882	12.9093	3.36035	36.3	54.9
4	1195390	9339824	11.9598	1.5328	36.3	54.2
5	2054896	8817714	12.1623	2.84217	36.3	45
6	1548488	6936500	11.509	2.57578	36.3	53
7	1837930	8069184	10.8628	2.48051	36.3	54.7
8	1513826	6812219	12.5444	2.79332	36.3	54.4
9	1194217	6236630	11.0461	2.1184	36.3	53.5
10	1347613	5541625	12.2006	2.972	36.3	52.1
11	1352685	5446330	11.9739	2.97988	36.3	55.7
12	1125210	5623379	11.4336	2.29375	36.3	52.8
13	1427718	6164305	11.8122	2.74218	36.3	54.2
14	1581018	5558226	10.5549	3.00721	36.3	51.7
15	1194286	5929981	12.3437	2.49079	36.3	55.8
16	1721170	6981746	12.6329	3.12217	36.3	56.2
17	1239184	6120904	11.4493	2.32304	36.3	54.6
18	1010858	5168646	10.1299	1.98516	36.3	51.8
19	2085292	6031878	12.4498	4.31301	36.3	57.3
20	1839414	6841016	12.3928	3.33999	36.3	57.2
21	1744680	6044533	13.1592	3.80718	36.3	57.4
22	1028090	5238163	13.3855	2.63309	36.3	54.9
23	1436699	5811855	12.5734	3.11603	36.3	51.5
24	911126	4395211	10.4219	2.1645	36.3	55.8
25	1158715	4763344	12.7016	3.09503	36.3	55.1
MT	3248278	16596	100	19679.7	36.3	60
```
When `coverage` with `-A` option finishes running, it prints messages summarizing what happened.
```plaintext
1 (59.58Mbp)
>  25.52% |.                                :                                                                       :                       :          | Number of reads: 1582614
>  22.69% |:                                :                                 :                                     : :  .                  :          |     (288307 filtered)
>  19.85% |:                                :                               . :                                     : : .:                 .:       .  | Covered bases:   6.66Mbp
>  17.02% |::   .                     :     ::      .  .                 .  : :                                     : : ::        :.     :.::      .:::| Percent covered: 11.18%
>  14.18% |::   :        :     .      :.    ::      :  : .       .       :  : ::                        .:         .::: ::     :. ::. : :::::      ::::| Mean coverage:   2.66x
>  11.34% |::  ::        :  .:.:  .   ::    ::   .  :  :.:       :     :::  : ::    ..   .              ::  :.    ::::: ::     ::.:::.: :::::      ::::| Mean baseQ:      36.3
>   8.51% |::  ::      : :  ::::.::.: ::    ::   :  :  :::  ..: ::    ::::  ::::: : ::   :    ..  .     ::  ::    :::::.::     :::::::: ::::: :  . ::::| Mean mapQ:       53.9
>   5.67% |::  ::      :.:: ::::::::: ::   .:::  :. : .:::  ::: ::... ::::  :::::.::::.  :.:: ::.::  :. :::.::   :::::::::::   :::::::: ::::: :. ::::::|
>   2.84% |:::.:: . .. ::::.:::::::::.:::..::::.::: : ::::..::::::::: ::::: :::::::::::: :::::::::: ::: ::::::   :::::::::::...::::::::::::::.:: ::::::| Histo bin width: 425.6Kbp
>   0.00% |:::::::::::.:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::.::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::| Histo max bin:   28.359%
          1       4.26M     8.51M     12.77M    17.02M    21.28M    25.53M    29.79M    34.04M    38.30M    42.56M    46.81M    51.07M    55.32M     59.58M
```

# Merge
`merge` function combines multiple sorted alignment files, producing a single sorted output file that contains all the input records and maintains the existing sort order. Use the following command to perform the `merge` function of samtools:
```python
$ samtools merge -@ <int> <output.bam> <input-1.bam>...<input-N.bam>
```
In these commands,
- `-@` option: specifies thread numbers `<int>`.
- `<output.bam>`: specifies output file.
- `<input.bam>...<input-N.bam>`: specifies input file.

### Example code (Results are not printed)
```python
$ samtools merge -@ 20 merged_H3K4me3.bam sort.H3K4me3_rep1.bam sort.H3K4me3_rep2.bam sort.H3K4me3_rep3.bam
# After merge, performs sort and index.
$ samtools sort -@ 20 merged_H3K4me3.bam -o sort.merged_H3K4me3.bam
$ samtools index sort.merged_H3K4me3.bam
```

# Citation
- Samtools
**Li, H.**, Handsaker, B., Wysoker, A., Fennell, T., Ruan, J., Homer, N., ... & 1000 Genome Project Data Processing Subgroup. (2009). **The sequence alignment/map format and SAMtools**. *bioinformatics*, 25(16), 2078-2079. [DOI](https://doi.org/10.1093/bioinformatics/btp352)