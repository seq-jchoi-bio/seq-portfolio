© 2022 Janghyun Choi
This work is licensed under a [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-nc-sa/4.0/).
![Docer](https://img.shields.io/badge/Revised%20version-5%2E15%2E24-green?style=flat&logo=Markdown&colorA=black) [![CC BY-NC-SA 4.0](https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-green?labelColor=black)](http://creativecommons.org/licenses/by-nc-sa/4.0/)


# Broad Peak Detection in ChIP-seq Data Using SICER2
Chromatin immunoprecipitation combined with high-throughput sequencing (ChIP-seq) can be used to map binding sites of a protein of interest in the genome. The resulting peaks data usually occupy broad chromatin domains and result in diffuse patterns that make it difficult to identify signal enrichment. SICER, a spatial clustering approach for the identification of peaks-enriched regions, was developed for calling broad peaks from ChIP-seq data. This tool is used to find peaks whether target binds to the interest genome loci by using `sicer` command and also can be used to compare peaks between two groups by using `sicdr_df` command. This protocol was created based on **SICER2 version 1.0.3** running on a system equipped with an Intel 10th generation i9-10910 processor and 48GB of memory. The test environment includes **Python version 3.8.5, SciPy version 1.6.2, NumPy version 1.20.1, and Xcode Command Line Tools version 2406 under macOS 12.4 OS environment.**

# Installation SICER2
![EnvTest](https://img.shields.io/badge/macOS-000000?style=flat&logo=apple&logoColor=white) [![gith](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github&logoColor=white)](https://github.com/zanglab/SICER2) [![githio](https://img.shields.io/badge/GitHub.io-181717?style=flat&logo=github&logoColor=yellow)](https://zanglab.github.io/SICER2/) [![Version](https://img.shields.io/badge/release-1.0.3-blue?labelColor=black)](https://github.com/zanglab/SICER2) [![Status](https://img.shields.io/badge/build-passing-DarkSeaGreen?labelColor=black)](https://travis-ci.com/zanglab/SICER2)
To install SICER2 via Pypi, use the following commands:
```bash
$ pip install sicer2
```

#### Requirements
To use `sicer2` you need:
-  `Python > 3.0` and its libraries `scipy` and `numpy`.
-  `C compiler` via Xcode command line tools (in macOS)
-  BedTools
![EnvTest](https://img.shields.io/badge/macOS-000000?style=flat&logo=apple&logoColor=white) ![EnvTest](https://img.shields.io/badge/Ubuntu-E95420?style=flat&logo=ubuntu&logoColor=white) [![gith](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github&logoColor=white)](https://github.com/arq5x/bedtools2) [![GitHub release](https://img.shields.io/github/v/release/arq5x/bedtools2?labelColor=black)](https://github.com/arq5x/bedtools2)
**Install BedTools via homebrew**
    ```bash 
    $ brew install bedtools
    ```

# bamToBed: Convert BAM into BED
`bamtobed` function of the `bedtools` is a conversion utility that converts sequence alignments in BAM format into BED, BED12, and/or BEDPE records. The BED is the most commonly used file format for peak-call analysis. Use the following command to convert BAM into BED with `bamtobed`:
```bash 
$ bedtools bamtobed -i <input.BAM> > <output.bed>
```

In these commands,

- `-i option`: Running command
- `<input.BAM>`: Specifies input bam file.
- `> <output.bed>`: Specifies output bed file. `>` symbol must appear before the `<output.bed>` syntax.

### Example Code (Results are not printed)
Here is an example command to perform `bamtobed`:
```bash
$ bedtools bamtobed -i Sort_IPT.bam > /Users/jchoi/Desktop/BED/Sort_IPT.bed
$ bedtools bamtobed -i Sort_shCtrl.bam > /Users/jchoi/Desktop/BED/Sort_shCtrl.bed
```

# Running SICER2
Use the following command to perform peak-call analysis with `sicer2`:
```bash
$ sicer -t <TargetFile.bed> -c <ControlFile.bed> -s <speciesBuild> -o <outputFolder> \
    -w <int> -f <int> -g <int> -e <int> -fdr <int> -cpu <int>
```
In these commands,

| Parameter | Description |
|----|----|
| `-t <TargetFile.bed>` | Specifies the target data. Possible data type is BAM or BED (recommended). |
| `-c <ControlFile.bed>` | Specifies the control data (input). Possible data type is BAM or BED (recommended). |
| `-s <speciesBuild>` | Specifies the reference genome of the species to apply to the analysis (e.g. `-s hg38`, `-s mm10`).<br>See the `GenomeData.py` file for available species. <br>If no species is available, you can add own genome data (see **Adding Your Own Species**). |
| `-o <outputFolder>` | Specifies output folder, which includes narrow peaks, score, and peaks BED. |
| `-w <int>` | Specifies window size (bp) to determine SICER resolution (Default: 200). |
| `-f <int>` | Specifies the amount of shift from the beginning of a read to the center of the DNA fragment (bp) represented by the read (Default: 150).  |
| `-g <int>` | Specifies the minimum length of a gap such that neighboring window is an island. <br> the `-f` value must be a multiple of the window size (Default: 600).|
| `-e <int>` | Specifies E-value (when no control library is provided) (Default: 1000). |
| `-fdr <int>` | Specifies an FDR cutoff (Default: 0.05) |
| `-cpu <int>` | The number of CPU cores SICER program will use when executing multi-processing tasks. |

### *Important Comments*
The efficiency of ChIP-seq is highly dependent on the quality of the antibodies, but even with high-quality antibodies, results often show substantial noise and background interference. Under such situations, **it is important for users to adjust the settings of the SICER algorithm to fit their specific experiments.** Although these adjustments are not universally obligatory, it is generally advisable to adopt certain parameter settings in particular scenarios to enhance efficiency and reduce processing time as follows:

1. If the bigwig file exhibits low noise levels and strong peak intensities, it is advisable to use the default parameters, particularly when analyzing antibodies related to the **histone activation**. However, for more detailed and precise observations, **adjusting the parameters to include `-w 50 -g 100` is recommended**. This adjustment helps enhance the resolution of the data while minimizing the inclusion of noise.

2. Conversely, if the bigwig file displays strong noise and weak peaks, it is likely associated with ChIP-seq studies of **histone repression markers or transcription factors**. In such cases, I recommend **initializing your analysis with `-w 30 -g 60`**. Subsequently, adjust these parameters to identify significant peaks that do not contain noise. **Note**, the `-g`value must be a multiple of the `-w`value.

### Example Code
Here is an example command to find significant peaks for histone ChIP-seq and TF ChIP-seq:
```bash
# ChIP-seq with H3K4me3 (active) in mouse genome
    $ sicer -t shCtrl_H3K4me3.bed -c IPT.bed -s mm10 -o /Users/jchoi/Desktop/shCtrl_H3K4me3_peaks \
    -w 50 -g 100 -fdr 0.05 --cpu 10
    $ sicer -t shNFIB_H3K4me3.bed -c IPT.bed -s mm10 -o /Users/jchoi/Desktop/shNFIB_H3K4me3_peaks \
    -w 50 -g 100 -fdr 0.05 --cpu 10
    $ sicer -t shNFIB_H3K4me3.bed -c IPT.bed -s mm10 -o /Users/jchoi/Desktop/High_shNFIB_H3K4me3_peaks \
    -w 50 -f 100 -g 100 -e 2000 -fdr 0.01 --cpu 10 # with higher tolerence

# ChIP-seq with H3K27me3 (repressive) and TF in human genome
    $ sicer -t Unt_H3K27me3.bed -c IPT.bed -s hg19 -o /Users/jchoi/Desktop/Unt_H3K27me3_peaks \
    -w 30 -g 60 -fdr 0.05 --cpu 10
    $ sicer -t Unt_UTX.bed -c IPT.bed -s hg19 -o /Users/jchoi/Desktop/Unt_UTX_peaks \
    -w 30 -g 60 -fdr 0.05 --cpu 10
```

### Output
When running SICER2, it will print out the progress as shown below:
```
    Running SICER with given arguments
    Preprocess the shCtrl_H3K4me3.bed file to remove redundancy with threshold of 1
    ---------------------------------------------------------------------------------------------------------
    chrom    Total plus reads        Retained plus reads       Total minus reads      Retained minus reads
    ---------------------------------------------------------------------------------------------------------
    chr1          1766092                  1431884                  1764980                  1430659
    chr2          1485557                  1161343                  1483041                  1158281
    chr3          960345                   826762                   960396                   826969
    chr4          1107496                  739794                   1107511                  739038
    chr5          721228                   629217                   720988                   629623
    chr6          864932                   732032                   864943                   733150
    chr7          1230572                  1004239                  1250754                  1004871
    chr8          786604                   668198                   786734                   668881
    chr9          1083606                  912150                   1084044                  912697
    chr10         1498563                  867551                   1494950                  871312
    chr11         1018302                  861238                   1018015                  862009
    chr12         771400                   665668                   771318                   665672
    chr13         414481                   361807                   416283                   362262
    chr14         523068                   451730                   542582                   452155
    chr15         608521                   524778                   608300                   525030
    chr16         1044556                  762083                   1045318                  761785
    chr17         872194                   716843                   872088                   716647
    chr18         350855                   282315                   351234                   282808
    chr19         810430                   664801                   810497                   665854
    chr20         693667                   579901                   694499                   580665
    chr21         240195                   188495                   240046                   187773
    chr22         362922                   310091                   362330                   310141
    chrX          532160                   463854                   532601                   464199
    chrY          208555                   117472                   208730                   117706
    chrM            432                      374                      437                      375

    Preprocess the IPT.bed file to remove redundancy with threshold of 1

    ---------------------------------------------------------------------------------------------------------
    chrom    Total plus reads        Retained plus reads       Total minus reads      Retained minus reads
    ---------------------------------------------------------------------------------------------------------
    chr1          1793368                  1659879                  1796390                  1661774
    chr2          1566262                  1454864                  1566440                  1456232
    chr3          1208586                  1144070                  1210949                  1146078
    chr4          855230                   782676                   857044                   784808
    chr5          891684                   845550                   893595                   847589
    chr6          1028974                  965932                   1031353                  968165
    chr7          1267501                  1178355                  1288957                  1178840
    chr8          938191                   875990                   940666                   878220
    chr9          1149407                  1083731                  1151828                  1086072
    chr10         1037858                  881262                   1033323                  887184
    chr11         1097897                  1031119                  1098541                  1032543
    chr12         833473                   789165                   835172                   790914
    chr13         545134                   516610                   547243                   517319
    chr14         557451                   527226                   576925                   528392
    chr15         617089                   584246                   618031                   585426
    chr16         745122                   677722                   746904                   676697
    chr17         682054                   637534                   682023                   637789
    chr18         405730                   378666                   406858                   379855
    chr19         457734                   421102                   458917                   422443
    chr20         693802                   653221                   694222                   653968
    chr21         184156                   169868                   182588                   168812
    chr22         302711                   286166                   302785                   286096
    chrX          623799                   590397                   625882                   591782
    chrY           63643                    50065                    61350                    48868
    chrM           23001                    15339                    22962                    14331


    Partition the genome in windows and generate summary files...

    Total count of chr1 tags: 2862543
    Total count of chr2 tags: 2319624
    Total count of chr3 tags: 1653731
    Total count of chr4 tags: 1478832
    Total count of chr5 tags: 1258840
    Total count of chr6 tags: 1465182
    Total count of chr7 tags: 2009110
    Total count of chr8 tags: 1337079
    Total count of chr9 tags: 1824847
    Total count of chr10 tags: 1738863
    Total count of chr11 tags: 1723247
    Total count of chr12 tags: 1331340
    Total count of chr13 tags: 724069
    Total count of chr14 tags: 903885
    Total count of chr15 tags: 1049808
    Total count of chr16 tags: 1523868
    Total count of chr17 tags: 1433490
    Total count of chr18 tags: 565123
    Total count of chr19 tags: 1330655
    Total count of chr20 tags: 1160566
    Total count of chr21 tags: 376268
    Total count of chr22 tags: 620232
    Total count of chrX tags: 928053
    Total count of chrY tags: 235178
    Total count of chrM tags: 747


    Normalizing graphs by total island filitered reads per million and generating summary WIG file...

    Finding candidate islands exhibiting clustering...

    Species:  hg19
    Window_size:  50
    Gap size:  100
    E value is: 1000
    Total read count: 31855179
    Genome Length:  3095693983
    Effective genome Length:  2290813547
    Window average: 1.3905618395576915
    Window pvalue: 0.2
    Minimum num of tags in a qualified window:  3

    Determining the score threshold from random background...
    The score threshold is: 26.159
    Generating the enriched probscore summary graph and filtering the summary graph to eliminate ineligible windows...
    Total number of islands:  46740


    Calculating significance of candidate islands using the control library...

    ChIP library read count: 31855182
    Control library read count: 36430952
    Total number of chip reads on islands is: 12431267
    Total number of control reads on islands is: 3296520
    Identify significant islands using FDR criterion

    Given significance 0.05 , there are 41044 significant islands
    Out of the  31855182  reads in  H2O2_H3K4me3.bed ,  12080466  reads are in significant islands
```

# Understanding SICER2 Output
Unless you use special parameters(`--significant_reads`, No explanation is provided for this parameter), the result of the SICER2 will be four files in the specified folder (`-o` option). The four files are described below:
1. **[`<targetFile.bed>`Name]-[`-w <int>`]-[`-g <int>`].scoreisland** (e.g. shCtrl_H3K4me3-W50-G100.scoreisland): This file is delineation of significant islands controlled by E-value of 1000 (`-e 1000`is default). It is in “chrom start end score” format.
2. **[`<targetFile.bed>`Name]-[`-w <int>`]-normalized.wig** (e.g. shCtrl_H3K4me3-W50.normalized.wig): this file can be used to visualize the windows generated by SICER2. Read count is normalized by library size per million. **It is can be loaded directly to the IGV or UCSC brower.**
3. **[`<targetFile.bed>`Name]-[`-w <int>`]-[`-g <int>`]-islands-summary** (e.g. shCtrl_H3K4me3-W50-G100-islands-summary): summary of all candidate islands with their statistical significance. It has the following format: *chrom, start, end, ChIP_island_read_count, CONTROL_island_read_count, p_value, fold_change, FDR_threshold.* **This file will be used for further analysis. Note that it has no extension.**
4. **[`<targetFile.bed>`Name]-[`-w <int>`]-[`-g <int>`]-[`-fdr <int>`]-island.bed** (e.g. shCtrl_H3K4me3-W50-G100-FDR0.05-island.bed): This file indicates delineation of significant islands filtered by FDR. It has the following format: chrom, start, end, read-count. "FDR0.01" represents the FDR parameter. **It is can be loaded directly to the IGV or UCSC brower.**
    ```bash
    > jchoi:RAW_SICER2/ $ ll
    total 469984
    -rw-r--r--  1 jchoi  staff   1.5M Dec 12  2022 shCtrl_H3K4me3-W50-G100-FDR0.05-island.bed
    -rw-r--r--@ 1 jchoi  staff   5.0M Dec 12  2022 shCtrl_H3K4me3-W50-G100-islands-summary
    -rw-r--r--  1 jchoi  staff   2.4M Dec 12  2022 shCtrl_H3K4me3-W50-G100.scoreisland
    -rw-r--r--  1 jchoi  staff   221M Dec 12  2022 shCtrl_H3K4me3-W50-normalized.wig
    ```

# Adding Your Own Species
The necessary reference genomes for running SICER2 are listed in the `GenomeData.py` file. This section provides guidance on incorporating your own genome data for species not already included. For example, I will detail the process of adding genome information for 'Oryza sativa'.

1. Obtain the DNA sequence of the desired genome (Oryza sativa for this guide) from Ensembl in FASTA format (fa extention).
2. Calculate the chromosome sizes of this genome using the command provided below:
    ```bash
    faSize -detailed /Users/jchoi/Desktop/Oryza_sativa.IRGSP-1.0.dna.toplevel.fa > /Users/jchoi/Desktop/chrom.sizes
    ```
3. Open the resulting chromosome size file (`chrom.sizes`) with a text editor such as vim and nano:
    ```
    Chr1	43270923
    Chr2	35937250
    Chr3	36413819
    Chr4	35502694
    Chr5	29958434
    Chr6	31248787
    Chr7	29697621
    Chr8	28443022
    Chr9	23012720
    Chr10	23207287
    Chr11	29021106
    Chr12	27531856
    ChrUn	633585
    ChrSy	592136
    ```
4. Use the chromosome size data to update the `GenomeData.py` file as demonstrated in the example below (**see the footnote in the following contexts**):
    ```python
    GenomeDataError = "Error in GenomeData class"

    bg_number_chroms = 1
    bg_length_of_chrom = 100000000

    background_chroms = []
    background_chrom_lengths = {}
    for i in range(0, bg_number_chroms):
        background_chroms.append('chr' + str(i + 1))
        background_chrom_lengths['chr' + str(i + 1)] = bg_length_of_chrom

    mm8_chroms = ['chr1', 'chr2', 'chr3', 'chr4', 'chr5', 'chr6', 'chr7', 'chr8', 'chr9',
                'chr10', 'chr11', 'chr12', 'chr13', 'chr14', 'chr15', 'chr16', 'chr17',
                'chr18', 'chr19', 'chrX', 'chrY', 'chrM']

    # =========================================(skip)=========================================
    # Add the following lines (irsgp_chroms).
    irsgp_chroms = ['Chr1', 'Chr2', 'Chr3', 'Chr4', 'Chr5', 'Chr6', 'Chr7', 'Chr8', 'Chr9',
                'Chr10', 'Chr11', 'Chr12', 'ChrUn', 'ChrSy']

    mm8_chrom_lengths = {'chr1': 197069962, 'chr2': 181976762, 'chr3': 159872112,
                        'chr4': 155029701, 'chr5': 152003063, 'chr6': 149525685,
                        'chr7': 145134094, 'chr8': 132085098, 'chr9': 124000669,
                        'chr10': 129959148, 'chr11': 121798632, 'chr12': 120463159,
                        'chr13': 120614378, 'chr14': 123978870, 'chr15': 103492577,
                        'chr16': 98252459, 'chr17': 95177420, 'chr18': 90736837,
                        'chr19': 61321190, 'chrX': 165556469, 'chrY': 16029404,
                        'chrM': 16299}

    # =========================================(skip)=========================================
    # Add the following lines (irsgp_chrom_lengths).
    irsgp_chrom_lengths = {'Chr1': 43270923, 'Chr2': 35937250, 'Chr3': 36413819,
                        'Chr4': 35502694, 'Chr5': 29958434, 'Chr6': 31248787,
                        'Chr7': 29697621, 'Chr8': 28443022, 'Chr9': 23012720,
                        'Chr10': 23207287, 'Chr11': 29021106, 'Chr12': 27531856,
                        'ChrUn': 633585, 'ChrSy': 592136}

    species_chroms = {'mm8': mm8_chroms,
                    'mm9': mm9_chroms,
    # =========================================(skip)=========================================
                    'tair8': tair8_chroms,
                    'irsgp': irsgp_chroms, # Add the following command.
                    'background': background_chroms}

    species_chrom_lengths = {'mm8': mm8_chrom_lengths,
                            'mm9': mm9_chrom_lengths,
    # =========================================(skip)=========================================
                            'tair8': tair8_chrom_lengths,
                            'irsgp': irsgp_chrom_lengths, # Add the following command.
                            'background': background_chrom_lengths}
    ```

5. Once finished with editing `GenomeData.py`, run `pip install -e` . in the top directory of the repo. This should install the user's local version of SICER2.
6. You can now perform peak-call analysis for oryza sativa with `-s irsgp` option.

# Running `sicer-df` for Differential Peak Calling
Use the following command to perform differentially peak-call analysis with `sicer-df`:
```bash
$ sicer-df -t <CompareFiles.bed> -c <InputFiles.bed> -s <speciesBuild> -o <outputFolder> \
    -w <int> -f <int> -g <int> -e <int> -fdr_df <int> -cpu <int>
```
The fundamental command line remains consistent with that of `sicer`. In these commands,
- `-t <CompareFiles.bed>`: Two files must be given as input. **The first file must be the target (KO/KD/Treated) file and the second file must be the control (WT/shCtrl/Untreated) file.** Possible data type is BAM or BED (recommended).
- `-c <InputFiles.bed>`: **(Optional parameter)** Two files must be given as input if you decide to provide the input. The first file must be the control library corresponding to the target and the second file must be the control library corresponding to the control. 
- `-fdr_df <int>`: Specifies an FDR cutoff (Default: 0.05), Do not confuse `-fdr <int>` for `sicer`.

### Example Code & Output
Here is an example command to find differentially significant peaks between two samples:
```bash
$ sicer_df -t H2O2_H3K4me3.bed Unt_H3K4me3.bed -s hg19 -w 50 -g 100 -fdr_df 0.05 --cpu 10 \
-o /Users/jchoi/Desktop/sicer_df/H3K4me3
$ sicer_df -t H2O2_MLL1.bed Unt_MLL1.bed -s hg19 -w 30 -g 60 -fdr_df 0.05 --cpu 10 \
-o /Users/jchoi/Desktop/sicer_df/MLL1
```
The output will be the same as the format of `sicer`.