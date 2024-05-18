© 2022 Janghyun Choi<br>This work is licensed under a [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-nc-sa/4.0/).<br>![Docer](https://img.shields.io/badge/Revised%20version-5%2E15%2E24-green?style=flat&logo=Markdown&colorA=black) [![CC BY-NC-SA 4.0](https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-green?labelColor=black)](http://creativecommons.org/licenses/by-nc-sa/4.0/)

# Motif Enrichment Analysis of ChIP-seq

In the analysis of ChIP-seq data, motif analysis plays a critical role in understanding the binding patterns of DNA-associated proteins. Tools like HOMER and MEME are pivotal in this research. HOMER is particularly effective for discovering new, short motifs (8-12 bp) within large genomic datasets, operating with a suite of command-line tools in Perl and C++. MEME, meanwhile, excels at identifying motifs across unaligned sequences using statistical models, helping to elucidate potential biological functions. Both tools are instrumental for researchers studying gene regulation and protein-DNA interactions, providing insights into the underlying mechanisms of gene expression. This protocol was created based on **HOMER version 4.9.1** and **MEME version 5.5.5** running on a system equipped with an Intel 10th generation i9-10910 processor and 48GB of memory. The test environment includes **Python version 3.8.5, SciPy version 1.6.2, NumPy version 1.20.1, Xcode Command Line Tools version 2406, and perl version 5.26.2 under macOS 12.4 environment.**

# Installation HOMER
![EnvTest](https://img.shields.io/badge/macOS-000000?style=flat&logo=apple&logoColor=white) ![EnvTest](https://img.shields.io/badge/Ubuntu-E95420?style=flat&logo=ubuntu&logoColor=white) [![gith](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github&logoColor=white)](https://github.com/javrodriguez/HOMER) [![web](https://img.shields.io/badge/Official-e34c26?style=flat&logo=html5&logoColor=white)](http://homer.ucsd.edu/homer/) ![version](https://img.shields.io/badge/release-4.9.1-blue?labelColor=black) ![Status](https://img.shields.io/badge/status-unstable-red?labelColor=black)

To install HOMER using Anaconda, use one of the following commands:
  ```bash 
  $ conda install -c bioconda homer
  # OR
  $ conda install -c bioconda/label/cf201901 homer
  ```

### Installation Reference Genomes
To check the available reference genomes, enter the following commnad in terminal:
```bash
$ perl /Users/jchoi/opt/anaconda3/share/homer-4.10-0/configureHomer.pl -list
```
When running `configureHomer.pl`, it will print out the progress as shown below:
```
  Current base directory for HOMER is /Users/jchoi/opt/anaconda3/share/homer-4.10-0/

  --2024-05-14 15:19:49--  http://homer.ucsd.edu/homer/update.txt
  Connecting to homer.ucsd.edu (homer.ucsd.edu)|169.228.63.226|:80... connected.
  HTTP request sent, awaiting response... 200 OK
  Length: 17857 (17K) [text/plain]
  Saving to: ‘/Users/jchoi/opt/anaconda3/share/homer-4.10-0//update.txt’

  /Users/jchoi/opt/anaconda3/share/homer-4.10-0//updat 100%[====================================================================================================================>]  17.44K  --.-KB/s    in 0.1s

  2024-05-14 15:19:49 (122 KB/s) - ‘/Users/jchoi/opt/anaconda3/share/homer-4.10-0//update.txt’ saved [17857/17857]

    Updating Settings...
  Packages with name conflicts have a trailing -o, -p, or -g
  Version Installed	Package	Version	Description
  SOFTWARE
  v4.11.1	homer	v5.0.1	Code/Executables, ontologies, motifs for HOMER
  ORGANISMS
  =========================================(skip)=========================================
  +	zebrafinch	v6.3	Taeniopygia guttata (zebrafinch) accession and ontology information
  +	mushroom	v6.3	Agaricus bisporus (mushroom) accession and ontology information
  +	volvox	v6.3	Volvox carteri (volvox) accession and ontology information
  +	selaginella	v6.3	Selaginella moellendorffii (selaginella) accession and ontology information
  +	mouse-o	v6.3	Mus musculus (mouse) accession and ontology information
  =========================================(skip)=========================================
  +	rhesus	v6.3	Macaca mulatta (rhesus) accession and ontology information
  +	ascomycetes	v6.0	Neurospora crassa (ascomycetes) accession and ontology information
  +	fugu	v6.3	Takifugu rubripes (fugu) accession and ontology information
  +	rice	v6.3	Oryza sativa (rice) accession and ontology information
  =========================================(skip)=========================================
  PROMOTERS
  +	human-p	v5.5	human promoters (human)
  +	frog-p	v5.5	frog promoters (frog)
  +	worm-p	v5.5	worm promoters (worm)
  +	rat-p	v5.5	rat promoters (rat)
  +	fly-p	v5.5	fly promoters (fly)
  +	chicken-p	v5.5	chicken promoters (chicken)
  +	zebrafish-p	v5.5	zebrafish promoters (zebrafish)
  +	arabidopsis-p	v6.3	arabidopsis promoters (arabidopsis)
  +	mouse-p	v5.5	mouse promoters (mouse)
  +	yeast-p	v5.5	yeast promoters (yeast)
  GENOMES
  =========================================(skip)=========================================
  +	hg19	v6.4	human genome and annotation for UCSC hg19
  +	mm10	v6.4	mouse genome and annotation for UCSC mm10
  +	danRer7	v6.4	zebrafish genome and annotation for UCSC danRer7
  +	sacCer3	v6.4	yeast genome and annotation for UCSC sacCer3
  +	danRer11	v6.4	zebrafish genome and annotation for UCSC danRer11
  =========================================(skip)=========================================
  +	danRer10	v6.4	zebrafish genome and annotation for UCSC danRer10
  +	dm3	v6.0	fly genome and annotation for UCSC dm3
  +	rice.IRGSP-1.0	v5.10	rice genome and annotation (rice.IRGSP-1.0)
  +	panPan2	v6.4	human genome and annotation for UCSC panPan2
  +	gorGor4	v6.4	human genome and annotation for UCSC gorGor4
  =========================================(skip)=========================================
  +	hg38	v6.4	human genome and annotation for UCSC hg38
  +	panTro3	v6.4	human genome and annotation for UCSC panTro3
  +	galGal5	v6.4	chicken genome and annotation for UCSC galGal5
```

To install the desired reference genomes, promoters, or organisms, enter the following commnad in terminal:
```bash
$ perl /Users/jchoi/opt/anaconda3/share/homer-4.10-0/configureHomer.pl -install <specific-name>
```

### Accessing the 'HOMER' Server from the Office Network
Occasionally, users may encounter difficulties accessing the 'HOMER' server from their office network, despite the HOMER website being reachable. This issue typically arises due to specific ports or IP addresses being blocked by our organization's firewalls or network policies. Should you experience such connectivity issues, the following procedures are recommended:
    
**Option 1. Contact the IT Department**: This is the preferred and recommended approach. IT staff are equipped to assess and rectify connectivity issues while ensuring compliance with our security protocols.

**Option 2. Use a Bypass Program**: Utilizing a VPN or other bypass solutions can be considered if direct assistance from IT is not feasible. **Please note, employing such methods must comply with your company's IT policies.** 

**Option 3. Manual Configuration**: This involves manually adding the reference genome. Details on how to perform this configuration will be discussed as the following section.

**Disclaimer**: It is crucial to emphasize that while Option 1 is generally the most effective and secure method to resolve access issues, it might not always be immediately available. As for Option 2, please be aware that I cannot assume responsibility for any security violations or other repercussions that may occur within your organization as a result of using unauthorized bypass methods. Always seek approval from relevant authorities within the company prior to proceeding with such solutions.

### Critical Protocol for Adding Reference Genomes in HOMER (Option 3)
This protocol details the steps required to update genome files within the HOMER environment. Please follow these steps meticulously to ensure accurate data handling and integration:

**1. Verify Installation of HOMER**: Confirm that HOMER is properly installed on your system. You can locate the configuration file at `./opt/anaconda3/share/homer-XXX/config.txt`.

**2. Access Configuration File**: Navigate to the directory specified above and open the `config.txt` file using a suitable text editor.

**3. Locate Dataset Entry**: Within the configuration file, search for the entry corresponding to your desired dataset. This could include genome data, promoters, GO terms, or known transcription factors. Pay particular attention to the HTTP address provided in the **fourth column** of the file.

**4. Download Dataset**: Using the HTTP address noted, manually download the dataset of interest. Ensure the source is reliable and secure.

**5. Extract and Transfer Data**: After downloading, unzip the file and transfer its contents to the data folder as specified in the configuration file. The path to this data folder is typically noted in the **fifth column** of config.txt.

**6. Initiate Motif Analysis**: With the new data successfully integrated, you are now prepared to proceed with motif analysis using HOMER.

# Running HOMER
Use the following command to find enriched motifs in genomic regions with `findMotifsGenome`:
```bash
$ findMotifsGenome.pl <PeakFiles> <species> <OutputFolder> -size <int> -len <int> -p <int> 
```

In these commands,

| Parameter | Description |
|----|----|
| `<PeakFiles>` | Specifies the input file. Acceptable input files are peaks files, which have at minimum five columns (separated by tabs, peakID/chromosome/start/end/strand) and BED (recommended) formats.  |
| `<species>` | Specifies the reference genome. Check to the available genome in the `configureHomer.pl` file.   |
| `<OutputFolder>` | Specifies the output path.  |
| `-size <int>` | Determine region size. It is advisable to use `-size 200` option to identify both primary and co-enriched motifs in transcription factors, while regions marked by histones (methyl/acetyl) should be examined with `500-1000`. Using the option `-size given`, HOMER will use the exact regions that were used as input. |
| `-len <int>` | Specifies the length of motifs to be found. The length of time it takes to find motifs increases greatly with increasing size.  In general, it is best to try out enrichment with shorter lengths (less than `15`) before trying longer lengths.  |
| `-p <int>` |  The number of CPU cores HOMER will use when executing multi-processing tasks.  |

### Example Code
Here is an example command to find significant motifs for histone ChIP-seq and TF ChIP-seq:
*An example of the output is not provided, as this command sufficiently demonstrates the progress in detail.*
```bash
# Human, ChIP-seq with a TF antiboty
  $ findMotifsGenome.pl MLL1_summits.bed hg19 /Users/jchoi/Desktop/HOMER_peaks -size given -len 15 -p 10

# Mouse, ChIP-seq with a histone marker antibody
  $ findMotifsGenome.pl H3K4me3_summits.bed mm10 /Users/jchoi/Desktop/HOMER_peaks -size 500 -len 15 -p 10

# Plant, ChIP-seq with a epitope antibody
  $ findMotifsGenome.pl Obe-W50-G100-FDR0.05-island.bed rice.IRGSP-1.0 /Users/jchoi/Desktop/Obe_HOMER \
  -size given -len 12 -p 10	
```


# Installation MEME
![EnvTest](https://img.shields.io/badge/macOS-000000?style=flat&logo=apple&logoColor=white) [![gith](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github&logoColor=white)](https://github.com/cinquin/MEME) [![web](https://img.shields.io/badge/Official-e34c26?style=flat&logo=html5&logoColor=white)](https://meme-suite.org/meme/) ![version](https://img.shields.io/badge/release-5.5.5-blue?labelColor=black) ![Status](https://img.shields.io/badge/status-stable-DarkSeaGreen?labelColor=black)

Unlike HOMER, MEME is recommended for motif analysis in ChIP-seq data due to its flexibility in identifying multiple motifs simultaneously, its robust statistical methods that evaluate the reliability of detected motifs, and its customizable options that allow for precise adjustments to sequence variability.

### Requirements
To successfully use `meme`, the following prerequisites are necessary:

- **Python Environment**: Version 3.0 or higher along with the libraries `scipy` and `numpy`.
- **Compiler**: A `C compiler` provided by the Xcode command line tools on macOS.
- **Bioinformatics Tools**:
  - `bedtools` (version specified: 2.30.0).
  - `gs` (Ghostscript, version 10.02.1), `autoconf` (version 2.71), `automake` (version 1.16.5). These tools can be installed easily using Homebrew with the command `brew install <package name>`.

### Additional Installation Notes:
If an `XML::Parser::Expat` error occurs, the necessary parser package can be installed with the following commands in the terminal:
  ```bash
    $ cpan App::cpanminus
    $ cpan XML::Simple
    $ cpan
    $ install XML::Parser
  ```

### Step-by-Step Installation Guide for MEME Tools

1. **Download the Tools**:
   Obtain the appropriate version of the MEME tools from the official website.

2. **Unzip and Install**:
   Open your terminal and execute the following commands to unpack and install the tools:
   ```bash
   $ cd ~/downloads
   $ tar zxf meme-5.5.5.tar.gz
   $ cd meme-5.5.5
   $ ./configure --prefix=/Users/jchoi/meme --enable-build-libxml2 --enable-build-libxslt # --prefix=[/your/pathway]/meme
   $ make
   $ make test  # Optional, to verify installation
   $ make install
   ```

3. **Configure shell environment (based on `zsh`)**:
	```bash
	$ nano ~/.zshrc
	```

4. **Add MEME to your path**:
	Insert the following lines at the end of your `~/.zshrc` file to include MEME in your system PATH. Adjust the paths as necessary based on your installation location.
	```bash
	# MEME Suite Configuation NOTE: CASE SENSITIVITY!
	  export PATH=[YOUR PATH]/meme/bin:[YOUR PATH]/meme/libexec/meme-5.5.5:$PATH
	
	## For exmaple
	  export PATH=/Users/jchoi/meme/bin:/Users/jchoi/meme/libexec/meme-5.5.5:$PATH
	```

5. **Activate the configuration**:
	```bash
	$ source ~/.zshrc
	```

# Prepare Input Files for MEME
The MEME tool requires sequences in FASTA format as the standard input. This section describes how to convert a BED file from peak calling analysis into a FASTA format using the `getfasta` function of `bedtools`. 

### Convert BED to FASTA
Use the following command to convert a peak-call BED file to FASTA format:

```bash
$ bedtools getfasta -fi <referenceGenomeFiles> -bed <input.bed> -fo <output.fasta> 
```
In these commands,

- `-fi <referenceGenomeFiles>`: specifies the reference genome. Possible types are fasta or fa.
- `-bed <input.bed>`: specifies input bed file.
- `-fo <output.fasta>`: specifies output fasta file.

### Example Code (Results are not printed)
Here are example commands using `getfasta` for different BED files generated by peak calling tools like MACS3 and SICER2:
```bash
  # For MACS3 bed
  $ bedtools getfasta -fi RGAP_Used.fa -bed /Users/jchoi/Desktop/MACS/obe_IP_summits.bed \
  -fo /Users/jchoi/Desktop/obe_IP.fasta

  # For SICER2 bed
  $ bedtools getfasta -fi Mus_musculus.GRCm39.dna.toplevel.fa -bed /Users/jchoi/Desktop/SICER/Unt_MLL1-W30-G60-FDR0.05-island.bed \
  -fo /Users/jchoi/Desktop/Unt_MLL1.fasta
```

# Running `meme`
The meme tool is used to identify statistically significant motifs within a single set of DNA or protein sequences. It operates by analyzing how frequently each motif occurs within the sequences. Use the following command to find motif sites with `meme`:
```bash
$ meme <input.fasta> -<alphabet> -mod <type> -pal -revcomp -minw <int> -maxw <int> \
  -nmotifs <int> -p <int> -o <output>
```

In these commands,

| Parameter | Description |
|----|----|
| `<input.fasta>` | Specifies the input file in either fasta or fa format.  |
| `-<alphabet>` | Specifies the type of the input file. Possible types are `dna`, `rna`, and `protein`.  |
| `-mod <type>` | Defines the distribution model for motif sites, crucial for precise motif analysis. For more details, see the section **Algorithm for the Distribution of Motif Sites**.  |
| `-pal` option | Restricts the search to palindromes in datasets that allow complementary matches.  |
| `-revcomp` option | Expands the search to include both the original strand and its reverse complement in datasets with complementable alphabets.  |
| `-minw <int>` | Search for motifs with a width >= `minw`. |
| `-maxw <int>` | Search for motifs with a width =< `maxw`.  |
| `-nmotifs <int>` | Specifies the number of motif sites to identify.   |
| `-p <int>` | Allocates the number of CPU cores for parallel processing, optimizing the performance of the tool. |
| `-o <output>` | Specifies the directory for storing the output results. |

#### Algorithm for the Distribution of Motif Sites
| Value | Name | Description |
|----|----|----|
| `oops` | One Occurrence Per Sequence | MEME assumes that each sequence in the dataset contains exactly one occurrence of each motif. This option is the fastest and most sensitive but the motifs returned by MEME may be "blurry" if any of the primary sequences is missing them. |
| `zoops` | Zero or One Occurrence Per Sequence | MEME assumes that each sequence may contain at most one occurrence of each motif. This option is useful when you suspect that some motifs may be missing from some of the primary sequences. In that case, the motifs found will be more accurate than using the first option. **This option takes more computer time than the first option (about twice as much)** and is slightly less sensitive to weak motifs present in all of the primary sequences.|
| `anr` | Any Number of Repetitions | MEME assumes each sequence may contain any number of non-overlapping occurrences of each motif. This option is useful when you suspect that motifs repeat multiple times within a single sequence. In that case, the motifs found will be much more accurate than using one of the other options. This option can also be used to discover repeats within a single sequence. **This option takes the much more computer time than the first option (about ten times as much)** and is somewhat less sensitive to weak motifs which do not repeat within a single sequence than the other two options.|

### Example Code & Output
Here is an example command to find motif sites from ChIP-seq peaks with `meme`:
*An example of the output is not provided, as this command sufficiently demonstrates the progress in detail.*
```bash
$ meme obe_IP.fasta -dna -mod zoops -pal -minw 8 -maxw 20 -nmotifs 10 -p 10 -o /Users/jchoi/Desktop/Motif
$ meme obe_IP.fasta -dna -mod oops -pal -revcomp -minw 8 -maxw 20 -nmotifs 10 -p 10 -o /Users/jchoi/Desktop/Motif_oops
```

### Understanding MEME Output
The result of the `meme` will be several files in the specified folder (`-o` option), according to the specific parameters. These output files are described below:

1. `meme.html`: an HTML file that provides the results in an interactive, human-readable format.
2. `meme.txt`: a plain text file of the results for backwards compatibility with earlier versions of MEME.
3. `meme.xml`: an XML file that provides the results in a format designed for machine processing.
4. `logoN.png,.eps`: PNG and EPS images files containing sequence logos for each of the motifs found by MEME (where N is the motif number).
5. `logo_rcN.png,.eps`: (complementable alphabets only) PNG and EPS images files containing reverse-complement sequence logos for each of the motifs found by MEME (where N is the motif number).

# Running `meme-chip`
`meme-chip` is an integrated web service used for searching and comparing motifs across various related sequences, including ChIP-seq data. This tool automates motif discovery and analysis using multiple MEME-Suite algorithms. Use the following command to find motif sites from peaks with `meme-chip`:

```bash
$ meme-chip <input.fasta> -<alphabet> -meme-mod <type> -meme-pal -minw <int> -maxw <int> \
  -meme-nmotifs <int> -db <dbpath> -meme-p <int> -o <output>
```

The fundamental command line remains consistent with that of `meme`. In these commands:

| Parameter | Description |
|----|----|
| `<input.fasta>` | Specifies the input file in either fasta or fa format.  |
| `-<alphabet>` | Specifies the type of the input file. Possible types are `dna`, `rna`, and `protein`.  |
| `-meme-mod <type>` | Defines the distribution model for motif sites. Same parameter the `-mod`in `meme`.  |
| `-meme-pal` option | Restricts the search to palindromes in datasets that allow complementary matches. Same parameter the `-pal`in `meme`.  |
| `-minw <int>` | Search for motifs with a width >= `minw`. |
| `-maxw <int>` | Search for motifs with a width =< `maxw`.  |
| `-meme-nmotifs <int>` | Specifies the number of motif sites to identify. Same parameter the `-nmotif`in `meme`.  |
| `-db <dbpath>` | (**Recommended**) Specifies known motifs database for comparison. Users may choose any preferred database (such as JASPAR) beyond those provided by `meme` (in `meme/share/meme-5.5.5/db/motif_databases/`). Most of these motif files are available in the `*.meme` format.  |
| `-meme-p <int>` | Allocates the number of CPU cores for parallel processing, optimizing the performance of the tool. |
| `-o <output>` | Specifies the directory for storing the output results. |

### Example Code & Output
Here is an example command to find motif sites from ChIP-seq peaks with `meme-chip`:
*An example of the output is not provided, as this command sufficiently demonstrates the progress in detail.*
```bash
  # Use JASPAR core database 
  $ meme-chip obe_IP.fasta -meme-mod zoops -meme-nmotifs 10 -minw 8 -maxw 20 \
  -db /db/motif_databases/JASPAR/JASPAR2022_CORE_plants_non-redundant_v2.meme \
  -o /Users/jchoi/Desktop/JASPAR_Motif

  # Use cisBP database
  $ meme-chip obe_IP.fasta -meme-mod zoops -meme-nmotifs 10 -minw 6 -maxw 15 -meme-pal \
  -db db/motif_databases/CIS-BP_2.00/Oryza_sativa.meme -o /Users/jchoi/Desktop/CIS_Motif

  # Use specific motif data
  $ meme-chip obe_IP.fasta -meme-mod zoops -meme-nmotifs 10 -minw 6 -maxw 12 -meme-pal \
  -db /Users/jchoi/Desktop/JASPAR2022_Plant_WRKY.meme -o /Users/jchoi/Desktop/WRKY_Motif
```
