© 2022 Janghyun Choi<br>This work is licensed under a [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-nc-sa/4.0/).<br>![Docer](https://img.shields.io/badge/Revised%20version-5%2E15%2E24-green?style=flat&logo=Markdown&colorA=black) [![CC BY-NC-SA 4.0](https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-green?labelColor=black)](http://creativecommons.org/licenses/by-nc-sa/4.0/)

# Remove Adaptor and Low-Quality Reads with Trimmomatic
Removing adapters and low-quality sequences from sequencing data is essential for ensuring data accuracy and quality. Most sequencers employ a sequencing adapter to generate raw data; however, these artificial sequences must be removed during data processing. This not only eliminates the adapter but also improves the overall quality of the data by removing errors or low-quality read ends that may occur during the sequencing process. Trimmomatic, a multithreaded tool based on the JavaScript platform, is highly effective in performing these tasks, making it a valuable resource in bioinformatics for obtaining high-quality data. This protocol was created based on **Trimmomatic version 0.39** running on a system equipped with an Intel 10th generation i9-10910 processor and 48GB of memory. The test environment includes **Python version 3.8.5 and Java version 16.0.1 under macOS 12.4 environment.**

# Installation Trimmomatic
![EnvTest](https://img.shields.io/badge/macOS-000000?style=flat&logo=apple&logoColor=white) ![EnvTest](https://img.shields.io/badge/Ubuntu-E95420?style=flat&logo=ubuntu&logoColor=white) [![gith](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github&logoColor=white)](https://github.com/usadellab/Trimmomatic) [![web](https://img.shields.io/badge/Official-e34c26?style=flat&logo=html5&logoColor=white)](http://www.usadellab.org/cms/?page=trimmomatic) [![GitHub release](https://img.shields.io/github/v/release/usadellab/Trimmomatic?labelColor=black)](https://github.com/usadellab/Trimmomatic/releases) ![Status](https://img.shields.io/badge/status-stable-DarkSeaGreen?labelColor=black)

Download the file from the [official website](http://www.usadellab.org/cms/?page=trimmomatic) and unzip it to a folder of your choice.

#### Requirments
To run Trimmomatic, you need Java version 7 or higher. Use the following command to check Java version:

```bash
$ java -version
java version "16.0.1" 2020-04-20
Java(TM) SE Runtime Environment (build 16.0.1+9-24)
Java HotSpot(TM) 64-Bit Server VM (build 16.0.1+9-24, mixed mode, sharing)
```

# Running Trimmomatic
Use the following command to perform Trimmomatic via Java machine:
**Trimmomatic and its related command-lines must be typed in directory of trimmomatic.**

```bash
$ java -var Trimmomatic-0.39.jar <mode> -<score> -threads <int> <input1> ... <inputN> \
	<PairedOutput1.fq.gz> <UnpairOutput1.fq.gz> ... <PairedOutputN.fq.gz> <UnpairOutputN.fq.gz> \
	<OptionalParameters>

# e.g. Full commands for pair-end mode
$ java -var Trimmomatic-0.39.jar <mode> -<score> -threads <int> <input1> <input2> \
	<PairedOutput1.fq.gz> <UnpairOutput1.fq.gz> <PairedOutput2.fq.gz> <UnpairOutput2.fq.gz> \
	ILLUMINACLIP:[adaptor file.fa]:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36 

# e.g. Full commands for single-end mode
$ java -var Trimmomatic-0.39.jar <mode> -<score> -threads <int> <input1> <PairedOutput1.fq.gz> \
	ILLUMINACLIP:[adaptor file.fa]:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36 
```
In these commands:
| Parameter | Description |
|----|----|
| `java -var Trimmomatic-0.39.jar` | Executes `trimmomatic` code |
| `<mode>` option | Specifies sequence type from either pair-end (`PE`) or single-end (`SE`)|
| `-<score>` option | Determines quality score whether this score is based on 33 (`phred33`) or 64 (`phred64`). |
| `-threads <int>` | Specifies thread numbers <int> for this operation. |
| `<input1> ... <inputN>` | Specifies forward and reverse input files, they are separated by **spaces**. |
| `<PairedOutputN.fq.gz> <UnpairOutputN.fq.gz>` | Specifies output files. In `PE` mode, each input file will result in the creation of two output files, arranged in the specific order of **"pair"** and **"unpair"**. **It is imperative to preserve this order to prevent any discrepancies.** In `SE` mode, each input file will generate a single output file, irrespective of any pairing configuration. |

- The `<OptionalParameters>` offer a wide range of variables as bellow, see the Trimmomatic's manual for details (<i>For more information such as global parameter, refers to Trimmomatic's guidelines.</i>).

	| Parameter | Description |
	|----|----|
	| `ILLUMINACLIP:<path/to/CLIP.fa>` | Specifies a adaptor clip file. Trimmomtic provide this universal sequence clip in `/adapters` folder. `ILLUMINACLIP` would be assigned relative path. |
	| `:<seed mismatches>:<palindrome clip threshold>:<simple clip threshold>` | Specifies the maximum mismatch, how accurate the match between the two adapter ligated reads, and how accurate the match between any adapter, respectively. This parameter should come after the `ILLUMINACLIP`, most commonly `:2:30:10`. |
	| `LEADING:<quality>` | Remove low quality bases from the beginning. Specifies the minimum quality required to keep a base, most commonly `LEADING:3`.  |
	| `TRAILING:<quality>` | Remove low quality bases from the end. Specifies the minimum quality required to keep a base, most commonly `TRAILING:3` |
	| `SLIDINGWINDOW:<size:<quality>` | Perform a sliding window trimming, cutting once the average quality (`quality`) within the window (`size`)falls below a threshold, most commonly `SLIDINGWINDOW:4:15`. |
	| `MINLEN:<length>` | This module removes reads that fall below the specified minimal length. Specifies the minimum length of reads to be kept, most commonly `MINLEN:36`.  |

## Example Code with explanation
Here is an example command with footnote for explanation to perform Trimmomatic on QCed fastq files:
```bash
# Example for PE mode
java -jar trimmomatic-0.39.jar PE -threads 20 -phred33 \ # Run a Trimmomatic, consist pair-end and base on ASCII 33, using 20 threads.
	~/Desktop/Bone_fastq/shC_D0_1.fastq.gz ~/Desktop/Bone_fastq/shC_D0_2.fastq.gz \ # Speficies forward and reverse input files, respectively.		
	~/Desktop/Bone_Trim/Trim_shC_D0_1.fastq.gz ~/Desktop/Bone_Trim/unTrim_shC_D0_1.fastq.gz \ # Output for forward
	~/Desktop/Bone_Trim/Trim_shC_D0_2.fastq.gz ~/Desktop/Bone_Trim/unTrim_shC_D0_2.fastq.gz \ # Output for reverse
	ILLUMINACLIP:adapters/TruSeq3-PE.fa:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36 # Specify adaptor sequence and global parameters

# Example for SE mode
java -jar trimmomatic-0.39.jar SE -threads 20 -phred33 \ # Run a Trimmomatic with SE mode
	~/Desktop/ChIP_seq/H3K4me3.fastq.gz TRIM_H3K4me3.fq.gz \ # Input and ouput
	ILLUMINACLIP:adapters/TruSeq3-SE.fa:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36 # global parameters
```

## Output
When Trimmomatic finishes running, it prints messages summarizing what happened.
```
	TrimmomaticPE: Started with arguments:
 	-threads 20 -phred33 /Users/jchoi/Desktop/Bone_fastq/shC_D0_1.fastq.gz /Users/jchoi/Desktop/Bone_fastq/shC_D0_2.fastq.gz /Users/jchoi/Desktop/Bone_Trim/Trim_shC_D0_1.fastq.gz /Users/jchoi/Desktop/Bone_Trim/unTrim_shC_D0_1.fastq.gz /Users/jchoi/Desktop/Bone_Trim/Trim_shC_D0_2.fastq.gz /Users/jchoi/Desktop/Bone_Trim/unTrim_shC_D0_2.fastq.gz ILLUMINACLIP:adapters/TruSeq3-PE.fa:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36
	Using PrefixPair: 'TACACTCTTTCCCTACACGACGCTCTTCCGATCT' and 'GTGACTGGAGTTCAGACGTGTGCTCTTCCGATCT'
	ILLUMINACLIP: Using 1 prefix pairs, 0 forward/reverse sequences, 0 forward only sequences, 0 reverse only sequences
	Input Read Pairs: 41429555 Both Surviving: 40573408 (97.93%) Forward Only Surviving: 646381 (1.56%) Reverse Only Surviving: 168217 (0.41%) Dropped: 41549 (0.10%)
	TrimmomaticPE: Completed successfully
```

# Citation
### Trimmomatic
**Bolger, A. M.**, Lohse, M., & Usadel, B. (2014). **Trimmomatic: a flexible trimmer for Illumina sequence data**. *Bioinformatics*, 30(15), 2114-2120. [DOI](https://doi.org/10.1093/bioinformatics/btu170)