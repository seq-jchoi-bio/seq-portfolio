© 2022 Janghyun Choi<br>This work is licensed under a [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-nc-sa/4.0/).<br>![Docer](https://img.shields.io/badge/Revised%20version-5%2E15%2E24-green?style=flat&logo=Markdown&colorA=black) [![CC BY-NC-SA 4.0](https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-green?labelColor=black)](http://creativecommons.org/licenses/by-nc-sa/4.0/)


# Exploring Deep-sequencing Data with deepTools
The deepTools effectively manages the vast data volumes routinely produced from sequencing platforms. This tool suite includes versatile modules designed to process mapped reads, performing numerous quality assessments. It also generates normalized coverage files in standard bedGraph and bigWig formats, facilitating comparisons between various datasets, such as treatment and control groups. This protocol was created based on **deepTools version 3.5.1** running on a system equipped with an Intel 10th generation i9-10910 processor and 48GB of memory. The test environment includes **Python version 3.8.5, SciPy version 1.6.2, NumPy version 1.20.1, and conda version 22.9.0 under macOS 12.4 environment.**

# Installation deepTools
![EnvTest](https://img.shields.io/badge/macOS-000000?style=flat&logo=apple&logoColor=white) ![EnvTest](https://img.shields.io/badge/Ubuntu-E95420?style=flat&logo=ubuntu&logoColor=white) [![gith](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github&logoColor=white)](https://github.com/deeptools/deepTools) [![githio](https://img.shields.io/badge/GitHub.io-181717?style=flat&logo=github&logoColor=yellow)](https://deeptools.readthedocs.io/en/latest/index.html) ![test](https://github.com/deeptools/deepTools/actions/workflows/test.yml/badge.svg?labelColor=black) [![PyPI Version](https://img.shields.io/pypi/v/deeptools.svg?style=flat&labelColor=black)](https://pypi.org/project/deepTools/)

To install deepTools using Anaconda, use the following commands:
```bash 
$ conda install -c conda-forge -c bioconda deeptools
```
### Troubleshooting in Linux Environment
1. If error occurs for freeze environment, update conda as follow;
    ```bash 
    $ sudo apt-get update
    $ conda update xz
    ```
2. If error occurs for repository, remove it as follow;
    ```bash  
    $ sudo add-apt-repository --remove ppa:[Dir]/[Sub-Dir]
    
    # For example
    $ sudo add-apt-repository --remove ppa:webupd8team/sublime-text-3
    ```
3. If deeptools cannot be run due to `libcryto.so.1.0.0` source error, refer to document of 'phyton setting'

# BamCoverage: Convert BAM into bigWig
BamCoverage module takes an alignment of reads or fragments as input (BAM file) and generates a coverage track (bigWig or bedGraph) as output. **This module requires a BAM file sorted by position and a bai file in the same folder.** Use the following command to convert BAM to BigWig with BamCoverage:
```bash
$ bamCoverage -b <input.bam> -o <output.bigwig> -of <outputType> -bs <int> \
    --normalizeUsing <Norm> --effectiveGenomeSize <int> --smoothLength <int> \
    --region <region> -p <int> -e
``` 
In these commands,

| Parameter | Description |
|----|----|
| `-b <input.bam>` | Specifies the input data. |
| `-o <output.bigwig>` | Specifies output file.|
| `-of <outputType>` | Specifies format of the output data. Possible values are bigWig or bdg. |
| `-bs <int>` | Size of the bins, in bases, for the output of the bigwig/bedgraph file (Default: 50). <br>**`-bs 1` setting is recommended for publication.** The smaller the value of `-bs`, the more computation. |
| `--normalizeUsing <Norm>` | Specifies whether to apply a method to normalize the number of reads per bin.<br>Possible values for `<Norm>` are `RPCG`, `RPKM`, `CPM`, or `none` (default: `none`). |
| `--effectiveGenomeSize <int>` | The effective genome size (EGS) is the portion of the genome that is mappable. this parameter is couple with `RPGC` normalization. <br>human: 2.7e9 (2700000000), mouse: 1.87e9 (1870000000).  |
| `--smoothLength <int>` | The smooth length defines a window, larger than the binSize, to average the number of reads. For example, if the –binSize is set to 20 and the –smoothLength is set to 60, then, for each bin, the average of the bin and its left and right neighbors is considered. <br>**For Publication, `-bs 1, --smoothLength 10`settting is recommended.** <br>The larger the value of `---smoothLength`, the more computation.|
| `--region <region>` | Region of the genome to limit the operation to - **this is useful when testing parameters to reduce the computing time.**<br>The format is chr:start:end, for example `--region chr2:178104147:178154424`.  |
| `-p <int>` | Specifies thread numbers `<int>` |
| `-e` option | This parameter allows the extension of reads to fragment size. If set, each read is extended, without exception. **Can not apply to RNA-seq data.** |

### Example code
Here is an example command to perform converting a bigWig file from the BAM file:
```bash
# Coded to quickly identify track patterns.
$ bamcoverage -b Final.sort.Unt_IPT.bam -o /Users/jchoi/desktop/Unt_IPT.bigwig \
    -bs 100 --normalizeUsing RPGC --effectiveGenomeSize 2700000000 -e -p 10

# For GEO submission, it takes a very long time.
$ bamcoverage -b Final.sort.Unt_IPT.bam -o /Users/jchoi/desktop/Unt_IPT.bigwig -bs 1 \
    --normalizeUsing RPGC --effectiveGenomeSize 2700000000 --smoothLength 10 -e -p 10
$ bamcoverage -b Final.sort.Unt_IPT.bam -o /Users/jchoi/desktop/Unt_IPT.bigwig -bs 5 \
    --normalizeUsing RPGC --effectiveGenomeSize 2700000000 --smoothLength 20 -e -p 10

# This code makes the bigWig file by specifying a target gene region as wide, which can be a time saver.
$ bamcoverage -b Final.sort.Unt_MLL1.bam -o /Users/jchoi/desktop/Unt_MLL1_NRF2.bigwig -bs 1 \
    --normalizeUsing RPGC --effectiveGenomeSize 2700000000 --region chr2:178104147:178154424 \ 
    --smoothLength 20 -e
```

### Output
When `BamCoverage` running, it prints messages printing as the follow:
```
        bamFilesList: ['Final.sort.Unt_MLL1.bam']
                binLength: 1
                numberOfSamples: None
                blackListFileName: None
                skipZeroOverZero: False
                bed_and_bin: False
                genomeChunkSize: None
                defaultFragmentLength: 254
                numberOfProcessors: 2
                verbose: False
                region: None
                bedFile: None
                minMappingQuality: None
                ignoreDuplicates: False
                chrsToSkip: []
                stepSize: 1
                center_read: False
                samFlag_include: None
                samFlag_exclude: None
                minFragmentLength: 0
                maxFragmentLength: 0
                zerosToNans: False
                smoothLength: None
                save_data: False
                out_file_for_raw_data: None
                maxPairedFragmentLength: 1016
```

# bigwigCompare and bamCompare
`bigwigCompare` compares two bigWig files based on the number of mapped reads. To compare the bigWig files, the genome is partitioned into bins of equal size, then the number of reads found in each BAM file are counted per bin and finally a summary value is reported. Similarly, `bamCompare` can be used to generate a bigWig or bedGraph file based on two BAM files that are compared to each other while being simultaneously normalized for sequencing depth. Use the following command to compare two bigWigs or two BAMs:
```bash
# bigwigCompare
$ bigwigCompare -b1 <target.bigwig> -b2 <control.bigwig> --operation <ratio> \
    -o <output.bigwig> -p <INT>

# bamCompare
$ bamCompare -b1 <target.bam> -b2 <control.bam> --operation <ratio> \
    -o <output.bigwig> -p <INT>
``` 
In these commands,

| Parameter | Description |
|----|----|
| `-b1 <target.bigwig>` | Specifies the bigwig or the sorted BAM file for **the target/treatment.** |
| `-b2 <control.bigwig>` | Specifies the bigwig or the sorted BAM file for **the control.**|
| `---operation <ratio>` | Specifies how to determine two values are compared.<br>Possible values for `<ratio>` are `log2`, `ratio`, `substrate`, `add`, or `mean` (default: `log2`). |
| `-o <output.bigwig>` | Specifies output file. |
| `-p <int>` | Specifies thread numbers `<int>` |

### Example code (Results are not printed)
Here is an example command to perform `bigwigCompare` and `bamCompare`:
```bash
# bigwigCompare
$ bigwigCompare -b1 /Users/jchoi/desktop/Unt_H3K4me3.bigwig \
    -b2 /Users/jchoi/desktop/Unt_IPT.bigwig --operation log2 \
    -o /Users/jchoi/desktop/Matrix/Unt_IPT_H3K4me3.bigwig -p 10
$ bigwigCompare -b1 /Users/jchoi/desktop/Unt_H3K27me3.bigwig \
    -b2 /Users/jchoi/desktop/Unt_IPT.bigwig --operation log2 \
    -o /Users/jchoi/desktop/Matrix/Unt_IPT_H3K27me3.bigwig -p 10

# bamCompare
$ bamCompare -b1 /Users/jchoi/desktop/BAM/Sorted.Unt_NFIB.bam \
    -b2 /Users/jchoi/desktop/BAM/Sorted.Unt_IPT.bam --operation log2 \
    -o /Users/jchoi/desktop/BAM/Matrix/Unt_IPT_NFIB.bigwig -p 10
$ bamCompare -b1 /Users/jchoi/desktop/BAM/Sorted.Unt_MLL1.bam \
    -b2 /Users/jchoi/desktop/BAM/Sorted.Unt_IPT.bam --operation log2 \
    -o /Users/jchoi/desktop/BAM/Matrix/Unt_IPT_MLL1.bigwig -p 10
```

# computeMatrix: Prepare matrix data
`computeMatrix` calculates scores per genome regions and prepares an intermediate file that can be used with `plotHeatmap` and `plotProfiles`. `computeMatrix` accepts multiple score files (bigWig format) and multiple regions files (BED format). This tool can also be used to filter and sort regions according to their score. Use the following command to make a matrix data using bigWig files generated from `bigwigCompare`:
```bash
$ computeMatrix <mode> --referencePoint <point> -b <int> -a <int> -R <referenceGenome> \
    -S <bigwigCompared_1.bigwig>...<bigwigCompared_N.bigwig> \
    --skipZeros --missingDataAsZero -o <matrix file.gz> -p <int>
``` 
In these commands,
| Parameter | Description |
|----|----|
| `<mode>` | Possible values are `reference-point` or `scale-regions`.<br> The `reference-point` mode plots genomic positions upstream and downstream from a specified position within a genome region, such as the starting point. Conversely, the `scale-regions` mode adjusts all regions in the genome file to a user-defined length, stretching or shrinking them as necessary. |
| `--referencePoint <point>` | Specifies a reference point. The reference point for the plotting could be either the region start (`TSS`), the region end (`TES`) or the `center` of the region. |
| `-b <int>` | Distance upstream of the reference-point selected. |
| `-a <int>` | Distance downstream of the reference-point selected. |
| `-R <referenceGenome>` | File name or names, in BED or GTF format, containing the regions to plot. <br>It is recommend to use the GTF file obtained in the htseeq-count step. |
| `-S <bigwigCompared_1.bigwig>...<bigwigCompared_N.bigwig>` | specifies input data, it is separated by spaces. <br> **These files should be obtained by using the `bamCoverage` or `bamCompare` modules.**  |
| `--skipZeros` option | Whether regions with only scores of zero should be included or not. |
| `--missingDataAsZero` option | If set, missing data (NAs) will be treated as zeros. |
| `-o <matrix file.gz>` | Specifies output file. The matrix file must have a 'gz' extension. |
| `-p <int>` | Specifies thread numbers `<int>` |

### Example code 
Here is an example command to prepare a matrix data with `computeMatrix`:
```bash
# Mouse, reference-point mode, TSS, TF ChIP-seq
$ computeMatrix reference-point --referencePoint TSS -b 3000 -a 3000 \
    -R /Users/jchoi/desktop/RefGene/mm10.refGene.gtf
    -S IPT_shCtrl_rep1.bigwig IPT_shCtrl_rep2.bigwig IPT_shNFIB_rep1.bigwig IPT_shNFIB_rep2.bigwig \
    --skipZeros --missingDataAsZero -o /Users/jchoi/desktop/bigWig/Matrix/matrix_TSS.gz -p 10

# Rice, reference-point mode, TES, histone ChIP-seq
$ computeMatrix reference-point --referencePoint TES -b 3000 -a 3000 \
    -R /Users/jchoi/Desktop/ProfYoon/O_Sativa_Genome/RGAP_Used/all.gtf \
    -S /Users/jchoi/Desktop/ProfYoon/ChIPSeq_Track/LowBin_WTe.bigwig /Users/jchoi/Desktop/ProfYoon/ChIPSeq_Track/LowBin_ehd1e.bigwig \
    --skipZeros --missingDataAsZero -o /Users/jchoi/Desktop/matrix_TES.gz -p 10
```

### Output
This code **does not output any results or progress**, but sometimes you will see a message like the one below:
```  
Skipping XX_XXXXXX_XX, due to being absent in the computeMatrix output.
Skipping NR_164356_11, due to being absent in the computeMatrix output. 
```
Ignore these mentions because they mean missing data and has been processed by `--skipZeros` and `--missingDataAsZero` commands.

# plotHeatmap
`plotHeatmap` creates a heatmap for scores associated with genomic regions. The program requires a matrix file generated by the `computeMatrix`. Use the following command to make a heatmap:
```bash
$ plotHeatmap -m <matrixFile.gz> -out <output.extention> --colorMap <colorCode> --colorList <"colorCode"> \
    --whatToShow <'type'> --zMin <group1 ... groupN> --zMax <group1 ... groupN> \
    --yMin <group1 ... groupN> --yMax <group1 ... groupN> \
    --kmeans <int> --perGroup --dpi <int> 
```
In these commands,
| Parameter | Description |
|----|----|
| `-m <matrixFile.gz>` | Specifies matrix file. |
| `-out <output.extention>` | Specifies output file. Possible values for file extention are `png`, `eps`, `pdf`, and `svg` (recommended). |
| `--colorMap <colorCode>` | Color map to use for the heatmap. If more than one heatmap is being plotted the color of each heatmap can be enter individually _(e.g. --colorMap Reds Blues)_. Available values can be seen here: http://matplotlib.org/users/colormaps.html  |
| `--colorList <colorCode>` | List of colors to use to create a colormap. For example, if –colorList black,yellow,blue is set (colors separated by comas) then a color map that starts with black, continues to yellow and finishes in blue is created. You can also use hexagonal color codes (#000000). |
| `--whatToShow <'type'>` | The default is to include a summary or profile plot on top of the heatmap and a heatmap colorbar. Other options are `'plot and heatmap'`, `'heatmap only'`, and `'heatmap and colorbar'`. <br>Not recommended for use. |
| `--zMin/--zMax` option | adjust heatmap intensities. If more than one heatmap is being plotted the intensity of each heatmap can be enter individually. |
| `--yMin/--yMax` option | adjust plot range of the y-axis. If more than one heatmap is being plotted the intensity of each heatmap can be enter individually.  |
| `--kmeans <int>` | Number of clusters to compute. When this option is set, the matrix is split into clusters using the k-means algorithm. `--hclust`option is also possible for Hierarchical clustering.|
| `--perGroup` option | The default is to plot all groups of regions by sample. Using this option instead plots all samples by group of regions. |
| `--dpi <int>` | Determine plot resolution. **This function does not seem to be working since version 3.5.2.**|

### Example Code (Results are not printed)
Here is an example command to plot heatmap with `plotHeatmap`:
```bash
$ plotHeatmap -m /Users/jchoi/desktop/bigWig/Matrix/matrix_TSS.gz \
    -out /Users/jchoi/desktop/Distribution.svg --colorMap RdBu
    --zMin -1 --zMax 6 --yMin 0 --yMax 4 --dpi 300

$ plotHeatmap -m /Users/jchoi/desktop/bigWig/Matrix/matrix_TSS.gz \
    -out /Users/jchoi/desktop/Distribution_heatmap.svg --colorMap RdBu
    --whatToShow 'heatmap only' --zMin -1 --zMax 6
    --kmeans 3 --dpi 300

$ plotHeatmap -m /Users/jchoi/Desktop/matrix_TSS.gz 
    -out /Users/jchoi/Desktop/Rice_H3ac.svg --colorList "white,#bd1829" 
    --yMin 20 --yMax 50 --zMin 40 
```

# plotProfile
`plotProfile` creates a profile plot for scores over sets of genomic regions. This is a simplified version of the `plotHeatmap` provided above that only plots individual line graphs. The parameters for this function are quite similar to others, so I will keep the explanation brief without example code. **Unfortunately, `plotProfile` is currently not used by many users.**
```bash
$ plotProfile -m <matrix file.gz> -out <output.extention> --plotType=<select>
    --color <'color code'> --numPlotsPerRow <int> --plotTitle <"Test data profile">
    --kmeans <int> --perGroup --dpi <int>
```
In these commands,
- `--plotType=<select>`: Possible values are `lines`, `fill`, `se`, `std`, `overlapped_lines`, and `heatmap`.
- `--color <'color code'>`: List of colors to use for the plotted lines (color names or hex strings). For example `--colors '#e89b81' '#959dc8' '#f6f0a8'`
- `--numPlotsPerRow <int>`: Number of plots per row

**Overall,** the `plotHeatmap` and `plotProfile` functions, powered by `computeMatrix`, are commonly used to visualize the tag distribution of peaks and are featured prominently in numerous publications. However, for more detailed analysis, I recommend using <u>Python-based `ngs.plot.r`</u> or the <u>`ChIPseeker` R package </u>, which offer enhanced detail and additional analytical capabilities.

# multiBigwigSummary and multiBamSummary
`multiBigwigSummary` and `multiBamSummary` compute the average score/read coverages for genomic regions for two or more bigWig and BAM files, respectively. This analysis is performed for the entire genome by running the program in bins mode, or for certain user selected regions in BED-file mode. Most commonly, the default output of `multiBigwigSummary` (a compressed numpy array, **.npz**) is used by other tools such as `plotCorrelation` or `plotPCA` for visualization and diagnostic purposes. Use the following command to make a summary matrix from bigWig files with `multiBigwigSummary`:
```bash
$ multiBigwigSummary bins -b <file1.bigwig>...<fileN.bigwig> -l <file1>...<fileN> -o <results.npz>
$ multiBamSummary bins -b <file1.bam>...<fileN.bam> -l <file1>...<fileN> -e -o <results.npz>
``` 
In these commands,
- `-b <file1.bigwig>...<fileN.bigwig>`: Specifies input data, it is separated by spaces.
- `-l <file1>...<fileN>`: User defined labels instead of default labels from file names. Multiple labels have to be separated by a space.
- `-o <results.npz>`: Specifies output data as npz format.
- `-e`option: **only `multiBamSummary`**, allows the extension of reads to fragment size.

### Example Code (Results are not printed)
Here is an example command to make a coverage matrix with `multiBigwigSummary`:
```bash
$ multiBigwigSummary bins -b shControl_Input.bigwig shControl_H3K4me3.bigwig shNFIB_H3K4me3.bigwig shMLL1_H3K4me3.bigwig \
    -l Input shControl shNFIB shMLL1 -o /Users/jchoi/Desktop/CorrMatrix.npz
```

# plotCorrelation
`plotCorrelation`analyzes and visualizes sample correlations based on the output of `multiBamSummary` or `multiBigwigSummary`. Pearson or Spearman methods are available to compute correlation coefficients. Use the following command to perform plotting for correlation with `plotCorrelation`:
```bash
$ plotCorrelation -in <CorrMatrix.npz> --corMethod <select> --skipZeros --whatToPlot <type> \
    --removeOutliers --colorMap <color> --plotNumbers -o <output.extention> --outFileCorMatrix <matrix_name.tab>
```
In these commands,
| Parameter | Description |
|----|----|
| `-in <CorrMatrix.npz>` | Specifies a coverage matrix file. |
| `---corMethod <select>` | Specifies a correlation method. Possible values for `<select>` are `spearman` and `pearson`. |
| `--skipZeros` option | By setting this option, genomic regions that have zero or missing (NaN) values in all samples are excluded.  |
| `--whatToPlot <type>` | Choose between a heatmap or pairwise scatter plots. Possible values for `<type>` are `heatmap` and `scatterplot`.  |
| `--removeOutliers` option | If set, bins with very large counts are removed (only `pearson`). |
| `--colorMap <color>` | Color map to use for the heatmap. |
| `--zMin/--zMax` option | adjust heatmap intensities. If not specified, the value is set automatically. |
| `--xRange/--yRange` option | adjust plot range of the x/y-axis. If not specified, the value is set automatically.  |
| `--plotNumbers` option | If set, then the correlation number is plotted on top of the heatmap (only `heatmap`). |
| `-o <output.extention>` | Specifies output file. Possible values for file extention are `png`, `eps`, `pdf`, and `svg` (recommended). |
| `--outFileCorMatrix <matrix_name.tab>` | Save matrix with pairwise correlation values to a tab-separated file. |

### Example Code (Results are not printed)
Here is an example command to make a correlation plot with `plotCorrelation`:
```bash
# Heatmap, Pearson r
$ plotCorrelation -in /Users/jchoi/Desktop/CorrMatrix.npz --corMethod pearson --skipZeros \
    --whatToPlot heatmap --removeOutliers --colorMap RdYlBu --plotNumbers \
    -o /Users/jchoi/Desktop/heatmap_PearsonCorr.svg --outFileCorMatrix /Users/jchoi/Desktop/heatmap_PearsonCorr.tab

# Scatter plot, Spearman r
$ plotCorrelation -in /Users/jchoi/Desktop/CorrMatrix.npz --corMethod spearman --skipZeros \
    --whatToPlot scatterplot --colorMap RdYlBu -o /Users/jchoi/Desktop/heatmap_SpearmanCorr.svg \
    --outFileCorMatrix /Users/jchoi/Desktop/heatmap_SpearmanCorr.tab
```

# Citation
### deepTools
**Ramírez, F.**, Dündar, F., Diehl, S., Grüning, B. A., & Manke, T. (2014). **deepTools: a flexible platform for exploring deep-sequencing data**. *Nucleic acids research*, 42(W1), W187-W191. [DOI](https://doi.org/10.1093/nar/gku365)