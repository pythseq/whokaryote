# Whokaryote

Whokaryote uses a random forest classifier that uses gene-structure based features and optionally Tiara
(https://github.com/ibe-uw/tiara) predictions to predict whether a contig is from a eukaryote or from a prokaryote.

You can use Whokaryote to determine which contigs need eukaryotic gene prediction and which need prokaryotic gene prediction.

Issues can be created via GitHub: https://github.com/LottePronk/whokaryote

## Cite
If you have used Whokaryote in your work, please cite our paper:
Pronk, Lotte J U, and Marnix H Medema. “Whokaryote: distinguishing eukaryotic and prokaryotic contigs in metagenomes based on gene structure.” _Microbial genomics_ vol. 8,5 (2022): https://doi.org/10.1099/mgen.0.000823

---

## Installation

Whokaryote was developed to run from the commandline on a UNIX-based system such as Ubuntu or MacOS. 
If you want to use Whokaryote on your Windows PC, we recommend you install Windows Subsystem for Linux (WSL). See
https://docs.microsoft.com/en-us/windows/wsl/about

### Recommended installation (Linux/Mac):

You can now easily install whokaryote via conda (or mamba).

[![install with bioconda](https://img.shields.io/badge/install%20with-bioconda-brightgreen.svg?style=flat)](http://bioconda.github.io/recipes/whokaryote/README.html)

Simply use the following command while in a (dedicated) conda environment:

`conda create -c bioconda -n whokaryote whokaryote`

Are you new to using conda?
You can download miniconda here: https://docs.conda.io/en/latest/miniconda.html


### Alternative installation for Windows:
Alternatively, you can install Whokaryote and its dependencies in a conda environment in Windows, see the instructions.
You can download miniconda here: https://docs.conda.io/en/latest/miniconda.html

1. Make a new and empty conda environment, and activate it:
   - `conda create -n whokaryote python==3.8`
   - `conda activate whokaryote`

2. Install dependencies with these commands:
   - `python -m pip install tiara` (see: https://github.com/ibe-uw/tiara)
   - download the windows binary of prodigal from the following link:
https://github.com/hyattpd/Prodigal/releases/tag/v2.6.3
   - change the file name: `move prodigal.windows.exe prodigal.exe`
   - move the file to the directory from where you run whokaryote. 

3. Install whokaryote:
   - Navigate to a directory you want to install whokaryote in.
   - Clone whokaryote to this directory: `git clone https://git.wur.nl/lotte.pronk/whokaryote.git `
   (or `git clone https://github.com/LottePronk/whokaryote.git` )
   - Go to the whokaryote directory: `cd whokaryote`
   - Install whokaryote in your (conda) environment with`python setup.py install`

---

## Using whokaryote

Use `whokaryote.py --help` to see all the options:
```
-h, --help            show this help message and exit
--contigs CONTIGS     The path to your contigs file. It should be one (multi)fasta (DNA).
--outdir OUTDIR       Specify the path to your preferred output directory. No / at the end.
--prodigal_file PRODIGAL_FILE
                        If you already have prodigal gene predictions, specify path to the .genes or .gff file
--f                   If you want new multifastas with only eukaryotes and only prokaryotes. This can take a long time.
--minsize MINSIZE     Select a minimum contig size in bp, default = 5000. Accuracy on contigs below 5000 is lower.
--model MODEL         Choose the stand-alone model or the tiara-integrated model: S or T. Option 'T' only works with argument --contigs
```

Note: When using Windows, you may need to run whokaryote from the commandline as follows:
`python whokaryote\bin\whokaryote.py [options]` 
### Example:

```
whokaryote.py --contigs contigs.fasta --outdir whokaryote_output --prodigal_file contigs_genes.gff
```
This is the standard way to run Whokaryote. If you don't specify --model, it will run the model that integrates 
Tiara predictions. This is recommended. 

If you already have gene annotation files (.gff or gene coordinates file from prodigal), 
use the --prodigal_file option. 

If you don't have an annotation file yet, you can use only
--contigs and --outdir. Prodigal will then be run on your contigs file, which may take a while.

With the example command, you will get the following output files:

- A file with all contig headers that were classified as eukaryotic (eukaryote_contig_headers.txt),
and a similar file for prokaryotic contig headers (prokaryote_contig_headers.txt)
- A fasta file with only contigs that were longer than 5000 bp, called contigs5000.fasta
- A gene coordinates file generated by prodigal called contigs_genes.genes
- A .csv file with all the calculated features called featuretable.csv
- A file with the tiara predictions called tiara_pred.txt
- A .csv file with the calculated features and the predictions called **featuretable_predictions_[model].tsv**
- A .tsv file with only the contig ID and the predictions called **whokaryote_predictions_[model].tsv**

Note that contigs with less than 2 genes or shorter than the minimum size are not classified and do not appear in the output.
