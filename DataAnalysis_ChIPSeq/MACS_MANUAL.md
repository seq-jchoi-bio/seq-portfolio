© 2022 Janghyun Choi
This work is licensed under a [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-nc-sa/4.0/).
![Docer](https://img.shields.io/badge/Revised%20version-5%2E15%2E24-green?style=flat&logo=Markdown&colorA=black) [![CC BY-NC-SA 4.0](https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-green?labelColor=black)](http://creativecommons.org/licenses/by-nc-sa/4.0/)

# Model-based Analysis for ChIP-Seq (MACS)
MACS3 is a computational tool used for the analysis of ChIP-Seq data. This tool is designed to identify transcription factor binding sites and regions of chromatin modifications across the genome with high precision and accuracy. The main features of MACS3 include the ability to handle different sequencing depths and conditions without user-defined parameters, an enhanced model-building strategy to better capture the nuances of peak distributions (`callpeak`). MACS3 also supports differential binding analysis (`bdgdiff`), facilitating the comparison between different samples to identify changes in protein-DNA interaction patterns. This protocol was created based on **MACS3 version 3.0.0a6** running on a system equipped with an Intel 10th generation i9-10910 processor and 48GB of memory. The test environment includes **Python version 3.8.5, SciPy version 1.6.2, NumPy version 1.20.1, and Cython version 0.29.30 under macOS 12.4 environment.**

# Installation MACS3
![EnvTest](https://img.shields.io/badge/macOS-000000?style=flat&logo=apple&logoColor=white) ![EnvTest](https://img.shields.io/badge/Ubuntu-E95420?style=flat&logo=ubuntu&logoColor=white) [![gith](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github&logoColor=white)](https://github.com/macs3-project/MACS) [![Github Release](https://img.shields.io/github/v/release/macs3-project/MACS?labelColor=black)](https://github.com/macs3-project/MACS/releases) ![Status](https://img.shields.io/pypi/status/macs3.svg?labelColor=black)

To install MACS3 via Pypi, use the following commands:
```bash
$ pip3 install macs3
```

##### Requirements for operating MACS3 version 3.0.1 (lastly released)
To use `macs3` you need:
-  `Python > 3.0` and its libraries `scipy >= 1.12`, `numpy >= 1.25`, `pytest >= 7.0`, and `scikit-learn>=1.3`
-  **Note**: `Cython ~= 3.0` and its wrapper `cykhash >= 2.0, < 3.0`
- **Disclaimer**: The requirements for running the latest version have changed significantly from the original specifications, incorporating numerous additional packages and updates to existing packages to newer versions. I have not tested MACS3 with this version, so I cannot ensure complete compatibility or functionality. Consequently, I advise beginners to manually install version 3.0.0a6 (alpha 6 released) from the source code.

# Running MACS3
Use the following command to perform peak-call analysis with `macs3`:
```bash
$ macs3 callpeak -t <TargetFile> -c <ControlFile> -f <type> -g <genomeSize> -n <NAME> \
	-B --nomodel --extsize <int> --shift <int> -q <int> --broad --broad-cutoff <int> 
```

In these commands,

| Parameter | Description |
|----|----|
| `-t <TargetFile>` | Specifies the target data.  |
| `-c <ControlFile>` | Specifies the control data (input).  |
| `-f <type>` | Specifies the file type. Possible data type is BAM (recommended), BED, ELANDMULTI, BAMPE, or BEDPE. |
| `-g <genomeSize>` | It is the mappable genome size or effective genome size which is defined as the genome size which can be sequenced. Possible values for size are `hs` (2913022398/GRCh38), `mm` (2652783500/GRCm38), `dm`(142573017/dm6), or `<int>`. Genome sizes for other species are available from `deeptools`. <br>**Note**: It is important to understand that this parameter means the size of the genome, not the species.|
| `-n <NAME>` | Determine the prefix to apply to the output files. |
| `-B` option | Specifies that store the fragment pileup, control lambda in bedGraph files.  |
| `--nomodel` option | While on, MACS3 will bypass building the shifting model. Please combine the usage of `--extsize` and `--shift` to achieve the effect you expect.  |
| `--extsize <int>` | While this option is set, MACS3 uses this parameter to extend reads in forward direction to fix-sized fragments.<br>*For example, if the size of the binding region for target TF is 200 bp, this parameter can be set as 200.* |
| `--shift <int>` | Specifies a starting point for reading in reads. **Start reading at a position `<int>` away from the 5' read. The size of the read is determined by `<--extsize>`.** When this value is negative, the cutting ends (5') will be moved toward 3'->5' direction, otherwise 5'->3' direction. <br>This parameter is a very useful option for **DNase or ATAC-seq**. <u> For example </u>, <br> 1. To find enriched cutting sites in DNAse-seq, all 5' ends of sequenced reads should be extended in both directions (approx. 200 bp) to smooth the pileup signals as following commands: `--nomodel --shift -100 --extsize 200` (shift =  -1 * [a half of extsize]).<br> 2. For nucleosome-seq data, need to pile up the centers of nucleosomes using a half-nucleosome size for NPS algorithm as the following command (DNA wrapped on nucleosome is about 147 bp): `--nomodel --shift 37 --extsize 73`.|
| `-q <int>` | Specifies an FDR cutoff to call significant regions (Default: 0.05). |
| `--broad` option | This option, along with the bdgbroadcall command, facilitates broad peak calling, producing results in the UCSC gappedPeak format which encapsulates a nested structure of peaks. |
| `--broad-cutoff <int>` | Specifies cutoff for the broad region. This option is not available unless `--broad` is set (Default: 0.1). |

### Example Code
Here is an example command to find significant peaks for histone ChIP-seq and TF ChIP-seq:
```bash
# Histone ChIP-seq
$ macs3 callpeak -t Sort_shControl.bam -c Sort_Input.bam -f BAM -g mm \
-n /home/jh/Desktop/MACS/shControl --broad --broad-cutoff 0.1

# TF ChIP-seq, with basal parameters
$ macs3 callpeak -t Sort_shNFIB_MLL1.bam -c Sort_Input.bam -f BAM -g mm \
-n /home/jh/Desktop/MACS/shNFIB_MLL1 -B -q 0.05

# TF ChIP-seq, with advanced parameters
$ macs3 callpeak -t Sort_Unt_Control.bam -c Sort_Input_Control.bam -f BAM -g mm \
-n /home/jh/Desktop/MACS/HaCaT_Unt_MLL1 -B --nomodel --extsize 150 -q 0.05
```

### Output
When running `callpeak`, it will print out the progress as shown below:
```
	INFO  @ Sun, 02 May 2021 11:55:38: 
	# Command line: callpeak -t /home/jh/Desktop/Bam/Sort_shNFIB.bam -c /home/jh/Desktop/Bam/Sort_Input.bam --broad -B -g mm --broad-cutoff 0.1 -n /home/jh/Desktop/MACS/shNFIB
	# ARGUMENTS LIST:
	# name = /home/jh/Desktop/MACS/shNFIB
	# format = AUTO
	# ChIP-seq file = ['/home/jh/Desktop/Bam/Sort_shNFIB.bam']
	# control file = ['/home/jh/Desktop/Bam/Sort_Input.bam']
	# effective genome size = 1.87e+09
	# band width = 300
	# model fold = [5, 50]
	# qvalue cutoff for narrow/strong regions = 5.00e-02
	# qvalue cutoff for broad/weak regions = 1.00e-01
	# The maximum gap between significant sites is assigned as the read length/tag size.
	# The minimum length of peaks is assigned as the predicted fragment length "d".
	# Larger dataset will be scaled towards smaller dataset.
	# Range for calculating regional lambda is: 1000 bps and 10000 bps
	# Broad region calling is on
	# Paired-End mode is off
	
	INFO  @ Sun, 02 May 2021 11:55:38: #1 read tag files... 
	INFO  @ Sun, 02 May 2021 11:55:38: #1 read treatment tags... 
	INFO  @ Sun, 02 May 2021 11:55:38: Detected format is: BAM 
	INFO  @ Sun, 02 May 2021 11:55:38: * Input file is gzipped. 
	INFO  @ Sun, 02 May 2021 11:55:40:  1000000 reads parsed 
	INFO  @ Sun, 02 May 2021 11:56:09:  13000000 reads parsed 
	INFO  @ Sun, 02 May 2021 11:56:12: 13369442 reads have been read. 
	INFO  @ Sun, 02 May 2021 11:56:12: #1.2 read input tags... 
	INFO  @ Sun, 02 May 2021 11:56:12: Detected format is: BAM 
	INFO  @ Sun, 02 May 2021 11:56:12: * Input file is gzipped. 
	INFO  @ Sun, 02 May 2021 11:56:16:  1000000 reads parsed 
	INFO  @ Sun, 02 May 2021 11:57:03:  13000000 reads parsed 
	INFO  @ Sun, 02 May 2021 11:57:07: 13700548 reads have been read. 
	INFO  @ Sun, 02 May 2021 11:57:07: #1 tag size is determined as 101 bps 
	INFO  @ Sun, 02 May 2021 11:57:07: #1 tag size = 101.0 
	INFO  @ Sun, 02 May 2021 11:57:07: #1  total tags in treatment: 13369442 
	INFO  @ Sun, 02 May 2021 11:57:07: #1 user defined the maximum tags... 
	INFO  @ Sun, 02 May 2021 11:57:07: #1 filter out redundant tags at the same location and the same strand by allowing at most 1 tag(s) 
	INFO  @ Sun, 02 May 2021 11:57:08: #1  tags after filtering in treatment: 12297927 
	INFO  @ Sun, 02 May 2021 11:57:08: #1  Redundant rate of treatment: 0.08 
	INFO  @ Sun, 02 May 2021 11:57:08: #1  total tags in control: 13700548 
	INFO  @ Sun, 02 May 2021 11:57:08: #1 user defined the maximum tags... 
	INFO  @ Sun, 02 May 2021 11:57:08: #1 filter out redundant tags at the same location and the same strand by allowing at most 1 tag(s) 
	INFO  @ Sun, 02 May 2021 11:57:08: #1  tags after filtering in control: 11744178 
	INFO  @ Sun, 02 May 2021 11:57:08: #1  Redundant rate of control: 0.14 
	INFO  @ Sun, 02 May 2021 11:57:08: #1 finished! 
	INFO  @ Sun, 02 May 2021 11:57:08: #2 Build Peak Model... 
	INFO  @ Sun, 02 May 2021 11:57:08: #2 looking for paired plus/minus strand peaks... 
	INFO  @ Sun, 02 May 2021 11:57:12: #2 Total number of paired peaks: 10769 
	INFO  @ Sun, 02 May 2021 11:57:12: #2 Model building with cross-correlation: Done 
	INFO  @ Sun, 02 May 2021 11:57:12: #2 finished! 
	INFO  @ Sun, 02 May 2021 11:57:12: #2 predicted fragment length is 207 bps 
	INFO  @ Sun, 02 May 2021 11:57:12: #2 alternative fragment length(s) may be 207 bps 
	INFO  @ Sun, 02 May 2021 11:57:12: #2.2 Generate R script for model : /home/jh/Desktop/MACS/shNFIB_model.r 
	INFO  @ Sun, 02 May 2021 11:57:12: #3 Call peaks... 
	INFO  @ Sun, 02 May 2021 11:57:12: #3 Call broad peaks with given level1 -log10qvalue cutoff and level2: 1.301030, 1.000000... 
	INFO  @ Sun, 02 May 2021 11:57:12: #3 Pre-compute pvalue-qvalue table... 
	INFO  @ Sun, 02 May 2021 11:58:19: #3 In the peak calling step, the following will be performed simultaneously: 
	INFO  @ Sun, 02 May 2021 11:58:19: #3   Write bedGraph files for treatment pileup (after scaling if necessary)... /home/jh/Desktop/MACS/shNFIB_treat_pileup.bdg 
	INFO  @ Sun, 02 May 2021 11:58:19: #3   Write bedGraph files for control lambda (after scaling if necessary)... /home/jh/Desktop/MACS/shNFIB_control_lambda.bdg 
	INFO  @ Sun, 02 May 2021 11:58:19: #3 Call peaks for each chromosome... 
	INFO  @ Sun, 02 May 2021 11:59:01: #4 Write output xls file... /home/jh/Desktop/MACS/shNFIB_peaks.xls 
	INFO  @ Sun, 02 May 2021 11:59:01: #4 Write broad peak in broadPeak format file... /home/jh/Desktop/MACS/shNFIB_peaks.broadPeak 
	INFO  @ Sun, 02 May 2021 11:59:01: #4 Write broad peak in bed12/gappedPeak format file... /home/jh/Desktop/MACS/shNFIB_peaks.gappedPeak 
	INFO  @ Sun, 02 May 2021 11:59:01: Done! 
```

### *Important Comments*
Similar to SICER2, MACS3 also makes it important for users to adjust the settings of the algorithm to suit their particular experiment. For specific conditions below, I recommend applying the following parameters  to increase efficiency and save the time:

1. Typically, use the `--broad` option for detecting broad peaks associated with histone activity markers. For identifying more variable peaks, such as histone repression markers and TFs, it is advisable not to use this option.

2. For a more detailed analysis, use `--nomodel` mode, where the crucial factor is the estimated read size (`--extsize`). You can determine this using the `predictd` subcommand of MACS3. Below is the method to utilize this subcommand.
	```bash
	$ macs3 predictd -i <InputFile.bam> -g <genomeSize> --rfile <output.R>
	```

	In these commands,
	- `-i <InputFile.bam>`: Specifies input file. Possible data type is BAM (recommended), BED, ELANDMULTI, BAMPE, or BEDPE.
	- `-g <genomeSize>`: Specifies the genome size. This parameter is the same as the `callpeak` function.
	- `--rfile <output.R>`: Specifies output file as peak model plot.
	>
	Here is an example command to perform `predictd`:
	```python
	$ macs3 predictd -i /Users/jchoi/Desktop/Final.sort.Unt_MLL1.bam -g hs --rfile /Users/jchoi/Desktop/predict_model_MLL1.R
	```
	When running `predictd`, it will print out the progress as shown below:
	```
		INFO  @ Fri, 10 May 2024 14:18:31: # read alignment files...
		INFO  @ Fri, 10 May 2024 14:18:31: # read treatment tags...
		INFO  @ Fri, 10 May 2024 14:18:31: Detected format is: BAM
		INFO  @ Fri, 10 May 2024 14:18:31:  * Input file is gzipped.*
		INFO  @ Fri, 10 May 2024 14:18:34:  1000000 reads parsed
		INFO  @ Fri, 10 May 2024 14:18:36:  2000000 reads parsed
		INFO  @ Fri, 10 May 2024 14:18:38:  3000000 reads parsed
		INFO  @ Fri, 10 May 2024 14:18:40:  4000000 reads parsed
		INFO  @ Fri, 10 May 2024 14:18:43:  5000000 reads parsed
		INFO  @ Fri, 10 May 2024 14:18:45:  6000000 reads parsed
		INFO  @ Fri, 10 May 2024 14:18:47:  7000000 reads parsed
		INFO  @ Fri, 10 May 2024 14:18:49:  8000000 reads parsed
		INFO  @ Fri, 10 May 2024 14:18:51:  9000000 reads parsed
		INFO  @ Fri, 10 May 2024 14:18:54:  10000000 reads parsed
		INFO  @ Fri, 10 May 2024 14:18:56:  11000000 reads parsed
		INFO  @ Fri, 10 May 2024 14:18:58:  12000000 reads parsed
		INFO  @ Fri, 10 May 2024 14:19:00:  13000000 reads parsed
		INFO  @ Fri, 10 May 2024 14:19:02:  14000000 reads parsed
		INFO  @ Fri, 10 May 2024 14:19:05:  15000000 reads parsed
		INFO  @ Fri, 10 May 2024 14:19:07:  16000000 reads parsed
		INFO  @ Fri, 10 May 2024 14:19:09:  17000000 reads parsed
		INFO  @ Fri, 10 May 2024 14:19:11:  18000000 reads parsed
		INFO  @ Fri, 10 May 2024 14:19:13:  19000000 reads parsed
		INFO  @ Fri, 10 May 2024 14:19:16:  20000000 reads parsed
		INFO  @ Fri, 10 May 2024 14:19:20: 20958780 reads have been read.
		INFO  @ Fri, 10 May 2024 14:19:20: tag size is determined as 93 bps
		INFO  @ Fri, 10 May 2024 14:19:20: # tag size = 93
		INFO  @ Fri, 10 May 2024 14:19:20: # total tags in alignment file: 20958780
		INFO  @ Fri, 10 May 2024 14:19:20: # Build Peak Model...
		INFO  @ Fri, 10 May 2024 14:19:20: #2 looking for paired plus/minus strand peaks...
		INFO  @ Fri, 10 May 2024 14:19:24: #2 Total number of paired peaks: 1407
		INFO  @ Fri, 10 May 2024 14:19:25: #2 Model building with cross-correlation: Done
		INFO  @ Fri, 10 May 2024 14:19:25: # finished!
		**INFO  @ Fri, 10 May 2024 14:19:25: # predicted fragment length is 195 bps**
		INFO  @ Fri, 10 May 2024 14:19:25: # alternative fragment length(s) may be 175,195 bps
		INFO  @ Fri, 10 May 2024 14:19:25: # Generate R script for model : /Users/jchoi/Desktop/predict_model_MLL1.R
	```
	- You can use the number of base pair printed in **predicted fragment length** on **line 34** as the `--extsize` option (i.e. *INFO  @ Fri, 10 May 2024 14:19:25: # predicted fragment length is 195 bps*)
	- The output model file can be output as a PDF image in the terminal with the following command:
		```bash
		$ Rscript <output.R> 
		```

## Understanding `callpeak` Output
The result of the `callpeak` will be 3 – 6 files in the specified folder (`-n` option), according to the specific parameters. These output files are described below:

1. **`<name>`_peaks.xls** (e.g. shCtrl_MLL1_peaks.xls): This file is a tabular file which contains information about called peaks. It is in “chromosome name, start, end, length of peak region, absolute peal summit, pileup height, -log10(pvalue), fold enrichment, -log10(qvalue)” format.
>
2. **`<name>`_peaks.narrowPeak** (e.g. shCtrl_MLL1_peaks.narrowPeak): This file is BED6+4 format file which contains the peak locations together with peak summit, p-value, and q-value. **It is can be loaded directly to the IGV or UCSC brower.**
>
3. **`<name>`_summits.bed** (e.g. shCtrl_MLL1_summits.bed): This file is in BED format, which contains the peak summits locations for every peak. The 5th column in this file is the same as what is in the narrowPeak file. If you want to find the motifs at the binding sites, this file is recommended. **It is can be loaded directly to the IGV or UCSC brower.**
>
4. **`<name>`_peaks.broadPeak** (e.g. shCtrl_MLL1_peaks.broadPeak): This file is in BED6+3 format which is similar to narrowPeak file, except for missing the 10th column for annotating peak summits. It is only generated **when the `--broad` option is on**. For more information on each column, refer to MACS3's github. **It is can be loaded directly to the IGV or UCSC brower.**
>
5. **`<name>`_peaks.gappedPeak** (e.g. shCtrl_MLL1_peaks.gappedPeak): This file is in BED12+3 format which contains both the broad region and narrow peaks. It is only generated **when the `--broad` option is on**. For more information on each column, refer to MACS3's github. **It is can be loaded directly to the IGV or UCSC brower.**
>
6. **`<name>`_treat_pileup.bdg** and **`<name>`_control_lambda.bdg**: The `<name>`_treat_pileup.bdg contains the pileup signals from **ChIP/treatment samples**. The `<name>`_control_lambda.bdg contains local biases estimated for each genomic location from **the control/input samples**, or from treatment sample when the control sample is absent. **when the `-B` option is on**. 

# Running `bdgdiff` for Differential Peak Calling
The `bdgdiff` command takes **four** input bedGraph files (two targets and two control files) and produces three output files with differential peaks called. Users should provide paired four bedgraph files: **for each condition, a treatment pileup signal track in bedGraph format, and a control lambda track in bedGraph format**. This differential calling can only handle one replicate per condition. The method we use to define the differential peaks is based on multiple likelihood tests, based on the poisson distribution. The likelihood function we used while comparing two conditions: ChIP (enrichment) or control (chromatin bias) is:
$$ln(LR) = x*(ln(x)-ln(y)) + y - x$$

Here $LR$ is the likelihood ratio, x is the signal (fragment pileup) observed in condition 1, and y is the signal in condition 2. And $ln$ is the natural logarithm. 
Use the following command to perform differential peak calling analysis with `bdgdiff`:
```bash
$ macs3 bdgdiff --t1 <cond1_treat_pileup.bdg> --c1 <cond1_control_lambda.bdg> \
	--t1 <cond2_treat_pileup.bdg> --c1 <cond2_control_lambda.bdg> \
	-C <int> -l <int> -g <int> --d1 <cond1_depth> --d2 <cond2_depth> \
	--outdir <output_path> --o-prefix <name>
```

In these commands,

| Parameter | Description |
|----|----|
| `--t1 <cond1_treat_pileup.bdg>` | Specifies the MACS pileup bdg file for condition 1.  |
| `--c1 <cond1_control_lambda.bdg>` | Specifies the MACS control lambda bdg file for condition 1.  |
| `--t2 <cond2_treat_pileup.bdg>` | Specifies the MACS pileup bdg file for condition 2.  |
| `--c2 <cond2_control_lambda.bdg>` | Specifies the MACS control lambda bdg file for condition 2.|
| `-C <int>` | Determine log10LR cutoff. Regions with signals lower than the cutoff will not be considered as enriched regions (DEFAULT: 3, likelihood ratio = 1000). |
| `-l <int>` | Specifies minimum length of the differential region. Try a bigger value to remove small regions (DEFAULT: 200).  |
| `-g <int>` | Specifies maximum gap to merge nearby differential regions. Consider a wider gap for broad marks (DEFAULT: 100). The maximum gap should be smaller than the minimum length (`-l`).   |
| `--d1 <cond1_depth>` | Specifies sequencing depth for condition 1. |
| `--d2 <cond1_depth>` | Specifies sequencing depth for condition 2.|
| `--outdir <output_path>` | Specifies output folder. All output files will be written to that directory. |
| `---o-prefix <name>` | Specifies output file prefix. Actual files will be named as PREFIX_cond1.bed, PREFIX_cond2.bed, and PREFIX_common.bed. |

### Step-by-step Manual (Example Code and Output)
#### Step 1. Determine fix-sized fragment using the `predictd` funtion.
To get a uniform size for running `callpeak`, run `predictd` as following example:
```bash
$ macs3 predictd -i Unt_H3K4me3.bam
$ macs3 predictd -i H2O2_H3K4me3.bam
```
**NOTE**: An easy solution is to use the average of two 'fragment size' predicted in callpeak, however any reasonable value will work. For example, you can use **200** for most ChIP-seq datasets for transcription factors, or **147** for most histone modification ChIP-seq. 

Anyway, I got the result below with the above commands:
```
# For Unt_H3K4me3.bam
	INFO  @ Tue, 14 May 2024 09:36:56: 17993682 reads have been read.
	INFO  @ Tue, 14 May 2024 09:36:56: tag size is determined as 85 bps
	INFO  @ Tue, 14 May 2024 09:36:56: # tag size = 85
	INFO  @ Tue, 14 May 2024 09:36:56: # total tags in alignment file: 17993682
	INFO  @ Tue, 14 May 2024 09:36:56: # Build Peak Model...
	INFO  @ Tue, 14 May 2024 09:36:56: #2 looking for paired plus/minus strand peaks...
	INFO  @ Tue, 14 May 2024 09:37:00: #2 Total number of paired peaks: 14653
	INFO  @ Tue, 14 May 2024 09:37:01: #2 Model building with cross-correlation: Done
	INFO  @ Tue, 14 May 2024 09:37:01: # finished!
	**INFO  @ Tue, 14 May 2024 09:37:01: # predicted fragment length is 219 bps**
	INFO  @ Tue, 14 May 2024 09:37:01: # alternative fragment length(s) may be 219 bps
	INFO  @ Tue, 14 May 2024 09:37:01: # Generate R script for model : predictd

# For H2O2_H3K4me3.bam
	INFO  @ Tue, 14 May 2024 09:39:00: 18880466 reads have been read.
	INFO  @ Tue, 14 May 2024 09:39:00: tag size is determined as 85 bps
	INFO  @ Tue, 14 May 2024 09:39:00: # tag size = 85
	INFO  @ Tue, 14 May 2024 09:39:00: # total tags in alignment file: 18880466
	INFO  @ Tue, 14 May 2024 09:39:00: # Build Peak Model...
	INFO  @ Tue, 14 May 2024 09:39:00: #2 looking for paired plus/minus strand peaks...
	INFO  @ Tue, 14 May 2024 09:39:05: #2 Total number of paired peaks: 15129
	INFO  @ Tue, 14 May 2024 09:39:05: #2 Model building with cross-correlation: Done
	INFO  @ Tue, 14 May 2024 09:39:05: # finished!
	**INFO  @ Tue, 14 May 2024 09:39:05: # predicted fragment length is 224 bps**
	INFO  @ Tue, 14 May 2024 09:39:05: # alternative fragment length(s) may be 224 bps
	INFO  @ Tue, 14 May 2024 09:39:05: # Generate R script for model : predictd
```

#### Step 2. Prepare bdg files corresponding to each sample using the `callpeak` funtion.
Using the size of the fragment calculated above for the `--extsize` option, perform the `callpeak` function as shown below. I will not describe the output (refer to the section of basal MACS3).
```bash
$ macs3 macs3 callpeak -B -t Unt_H3K4me3.bam -c IPT.bam -g hs -n Unt --nomodel --extsize 219
$ macs3 callpeak -B -t H2O2_H3K4me3.bam -c IPT.bam -g hs -n H2O2 --nomodel --extsize 224
```

#### Step 3. Determine seqeuncing depth.
Extract the "tags after filtering in treatment" and "tags after filtering in control" lines from the `<name>`_peaks.xls to see the effective sequencing depths for both conditions. The effective sequencing depth is the smaller number of treatment and control. For example, use the `egrep` function in the terminal as the following commands:
```bash
$ egrep "tags after filtering in treatment|tags after filtering in control" Unt_peaks.xls
# tags after filtering in treatment: 14060436
# tags after filtering in control: 16211598

$ egrep "tags after filtering in treatment|tags after filtering in control" H2O2_peaks.xls
# tags after filtering in treatment: 14486767
# tags after filtering in control: 16211598
```
Then actual effective depths of condition 1 (Unt) and 2 (H2O2) are **14060436** and **14486767**, respectively.

#### Step 4. Running the `bdgdiff` function.
The purpose of this step is to do a three ways comparisons to find out where in the genome has differential enrichment between two conditions.  A log10 likelihood ratio cutoff (`-C` option) will be applied in this step. Three types of differential regions will be reported: <u>PREFIX_cond1.bed (cond1 > cond2), PREFIX_cond2.bed (cond1 < cond2), and PREFIX_common.bed (cond1 = cond2)</u>.
```bash
$ macs3 bdgdiff --t1 Unt_treat_pileup.bdg --c1 Unt_control_lambda.bdg \
	--t2 H2O2_treat_pileup.bdg --c2 H2O2_control_lambda.bdg \
	--d1 14060436 --d2 14486767 -g 60 -l 120 --o-prefix diffPeaks
```

When running `bdgdiff`, it will print out the progress as shown below:
```
	INFO  @ Tue, 14 May 2024 10:03:32: Read and build treatment 1 bedGraph...
	INFO  @ Tue, 14 May 2024 10:03:52: Read and build control 1 bedGraph...
	INFO  @ Tue, 14 May 2024 10:04:18: Read and build treatment 2 bedGraph...
	INFO  @ Tue, 14 May 2024 10:04:38: Read and build control 2 bedGraph...
	INFO  @ Tue, 14 May 2024 10:06:51: Write peaks...
	INFO  @ Tue, 14 May 2024 10:06:51: Done
```

# Citation
- MACS
**Zhang, Y.**, Liu, T., Meyer, C. A., Eeckhoute, J., Johnson, D. S., Bernstein, B. E., ... & Liu, X. S. (2008).**Model-based analysis of ChIP-Seq (MACS)**. *Genome biology*, 9, 1-9. [DOI](https://doi.org/10.1186/gb-2008-9-9-r137)