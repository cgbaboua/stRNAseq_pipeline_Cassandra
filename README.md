# Spatial Transcriptomics Pipeline

## Table of Contents

- [Overview](#overview)
- [System Requirements](#system-requirements)
- [Installation](#installation)
  - [Clone the Repository](#clone-the-repository)
- [stRNAseq Pipeline Description](#stRNAseq-Pipeline-Description)
  - [Quality Control](#quality-control)
  - [Normalization](#normalization)
  - [Dimensionality Reduction](#dimensionality-reduction)
  - [Clustering](#clustering)
  - [Cell Type Annotation](#cell-type-annotation)
- [Usage Instructions](#usage-instructions)
- [Dependencies](#dependencies)
- [Additional Resources](#additional-resources)
- [Acknowledgments](#acknowledgments)

## Overview

The Spatial Transcriptomics pipeline is designed to process and analyze spatial transcriptomics data through a series of structured R Markdown scripts. Each script corresponds to a stage in the data processing workflow, from quality control to clustering and cell type annotation.
The pipeline operates on Colon data (ColonN_1215002B and CRC_24676A1) and includes specialized scripts for transforming, normalizing, and analyzing spatial RNA-seq data.
The reference data for cell annotation was found on [TISCH DB](http://tisch.comp-genomics.org/gallery/?cancer=CRC) and the CRC_GSE166555 data.

## System Requirements

- R version 4.3.2 or higher
- Compatible with Unix-based operating systems; Windows users are advised to use the Windows Subsystem for Linux (WSL) or AXE 
- Adequate storage to handle large datasets typically associated with spatial transcriptomics

## Installation

### Clone the Repository

To obtain the latest version of the pipeline, clone the repository using the following command:

```bash
git clone https://github.com/servier-github/Projet_Long_stRNAsq.git
```
## stRNAseq Pipeline Description

### Quality Control

[`01_QC_t.qmd`](https://github.com/servier-github/Projet_Long_stRNAsq/blob/e09bd6f10b305957bd8e408d4bcd88ce3b9fda73/stRNAseq_pipeline_Cassandra/01_QC_t.qmd): This script performs initial data quality assessments, filters out low-quality data, and prepares data for further analysis.

### Normalization

[`02_Normalization_t.qmd`](https://github.com/servier-github/Projet_Long_stRNAsq/blob/e09bd6f10b305957bd8e408d4bcd88ce3b9fda73/stRNAseq_pipeline_Cassandra/02_Normalization_t.qmd): Normalizes data using the `SCTransform()` function to correct for technical variations in the data.

### Dimensionality Reduction

[`03_Dim_reduction_t.qmd`](https://github.com/servier-github/Projet_Long_stRNAsq/blob/e09bd6f10b305957bd8e408d4bcd88ce3b9fda73/stRNAseq_pipeline_Cassandra/03_Dim_Reduction_t.qmd): Applies PCA, t-SNE, and UMAP techniques to reduce the dimensionality of the dataset, facilitating downstream analysis such as clustering.

### Clustering 

[`04_Clustering_t.qmd`](https://github.com/servier-github/Projet_Long_stRNAsq/blob/e09bd6f10b305957bd8e408d4bcd88ce3b9fda73/stRNAseq_pipeline_Cassandra/04_Clustering_t.qmd)  : The script identify distinct cell populations with clustering. 

### Cell Type Annotation
[`05_Annot_test.qmd`](https://github.com/servier-github/Projet_Long_stRNAsq/blob/e09bd6f10b305957bd8e408d4bcd88ce3b9fda73/stRNAseq_pipeline_Cassandra/05_Annot_test.qmd): Annotate cell types based on gene expression patterns and known markers.

## Usage Instructions

1. **Set up your environment**: Update the directory paths (`directory`, `data_dir`, `save_dir`, `out_dir`) in each script to match your local environment.
2. **Run scripts in sequence**: Start with `01_QC_t.qmd` and proceed through the pipeline by following the numbering of the scripts.
3. **Review results**: Check the output files and logs for each script to understand the analysis outcomes and insights.

## Dependencies

- **R packages**: Seurat (5.0.1), ggplot2 (3.4.4), DESeq2 (1.42.0), and others.
- A full list of dependencies is included at the start of each script for easy reference.

## Additional Resources
For additional help and resources, please refer to the [Spatial Transcriptomics with Seurat](https://satijalab.org/seurat/articles/pbmc3k_tutorial).

## Acknowledgments

This pipeline was developed by Cassandra Gbaboua, drawing inspiration from Rossana Cervesato's work and heavily based on tutorials from Seurat. support in the development of this project.


