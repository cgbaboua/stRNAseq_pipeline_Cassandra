# Spatial Transcriptomics Pipeline

## Table of Contents
- [Pipeline description](#pipeline-description)
  - [Quality Control](#quality-control)
  - [Normalization](#normalization)
  - [Dimensionality Reduction](#dimensionality-reduction)
  - [Clustering](#clustering)
  - [Cell Type Annotation](#cell-type-annotation)

## Pipeline Description

### Quality Control

#### Detailed Description of `01_QC_t.qmd`

**Purpose:**  
The script is designed to ensure that only high-quality Spatial Transcriptomics (ST) RNA-seq data progresses through the analysis pipeline. It provides a rigorous statistical assessment and filters the data based on predefined quality metrics.

**Inputs:**  
The primary input is the directory containing filtered feature barcode matrices typically obtained from Space Ranger's output:
- **Directory (`filtered_feature_bc_matrix/`):** Contains three essential files: `matrix.mtx.gz`, `features.tsv.gz`, and `barcodes.tsv.gz` that represent the filtered spatial gene expression data.

**Processing Steps:**  
1. **Data Loading:** The script starts by loading the ST data from the `filtered_feature_bc_matrix/` directory.
2. **Initial Data Overview:** Generates statistical summaries of the dataset, helping in understanding the distribution of gene expressions, mitochondrial content, and potential contamination.
3. **Quality Control Metrics:** Applies filters based on mitochondrial DNA content, ribosomal protein gene expression, and hemoglobin gene presence to remove low-quality spots.
4. **Data Visualization:** Produces several plots that visually represent the quality metrics, including mitochondrial content, gene count distributions, and spatial feature distributions.

**Outputs:**  
- **Seurat Objects:** Two RDS files representing the raw (`sparse_matrix`) and QC-filtered (`QC_matrix`) Seurat objects.
- **Quality Metrics:** A `QC_stat.csv` file that logs the QC metrics before and after data filtering.
- **Plots:** Saved in the specified output directory (`stRNAseq_01_plots`), these plots provide a visual overview of the data quality and characteristics.

**Usage Notes:**  
- Users are encouraged to review and adjust the filtering thresholds based on specific experimental conditions or data characteristics.
- Ensure that the path to the `filtered_feature_bc_matrix/` directory is correctly set up in the script to match the data storage structure.

### Normalization

#### Detailed Description of `02_Normalization_t.qmd`

**Purpose:**  
The script carries out essential normalization and data scaling processes on Spatial Transcriptomics (ST) RNA-seq data. The normalization step is crucial for mitigating technical variances, allowing for accurate downstream analyses such as cell cycle scoring and sample sex determination.

**Inputs:**  
The script utilizes quality-controlled data in the form of Seurat objects, which have passed through the QC pipeline:
- **Directory (`rds_data/01_QC/`):** Contains the Seurat objects in RDS format, representing QC-filtered data for each sample.

**Processing Steps:**  
1. **Data Importation:** The script begins by loading the pre-processed Seurat objects from the QC step.
2. **Normalization:** Employs the `NormalizeData` function from the Seurat package to normalize the gene expression data.
3. **Cell Cycle Scoring:** Assigns cell cycle phases to cells using predefined gene sets and annotates cells with scores reflecting their cell cycle phases.
4. **Sex Determination:** Estimates the sex of the sample by analyzing the expression levels of Y chromosome genes and XIST.
5. **SCTransform Normalization:** Applies the SCTransform method, a variance-stabilizing transformation, to further normalize the data and identify variable features.

**Outputs:**  
- **Normalized Seurat Objects:** RDS files for each sample post-SCTransform normalization (`*_SCT_matrix.rds`).
- **Cell Cycle Data:** RDS files containing Seurat objects split by cell cycle phase (`*_cell_cycle_split_matrix.rds`).
- **Plots:** Graphical representations of cell cycle distributions and sex determination results, stored in the specified directory (`stRNAseq_02_plots`).
- **Log Files:** Detailed logs of the normalization process and results saved in a subdirectory (`log/02_SCT/`).

**Usage Notes:**  
- The normalization step is highly customizable, and parameters within the SCTransform function may need adjustments based on the dataset characteristics.
- It is recommended to verify the set directories and the names of input files to align with the current data structure and ensure the script functions correctly.
- Review the resulting plots and logs to confirm the successful execution of normalization and to understand the data characteristics before proceeding to further analysis steps.

### Dimensionality Reduction

#### Detailed Description of `03_Dim_Reduction_t.qmd`

**Purpose:**  
The script takes the normalized Spatial Transcriptomics (ST) RNA-seq data through principal component analysis (PCA) and other non-linear dimensionality reduction methods such as t-SNE and UMAP. These techniques are fundamental for visualizing high-dimensional data in a lower-dimensional space, which is critical for identifying patterns and clusters that represent biological variation within the data.

**Inputs:**  
The script operates on SCT-normalized Seurat objects prepared in the preceding normalization step:
- **Directory (`rds_data/02_SCT/`):** This directory should contain RDS files of the SCT-normalized data (`*_SCT_matrix.rds`), which will be used for the dimensionality reduction analysis.

**Processing Steps:**  
1. **Data Loading:** Begins with importing SCT-transformed Seurat objects for each sample.
2. **PCA Computation:** Executes PCA to reduce dimensionality while preserving as much data variance as possible.
3. **Visualization and Interpretation:** Various visualization techniques such as DimPlot, FeaturePlot, and ElbowPlot are used to interpret and analyze PCA results.
4. **Non-linear Dimensionality Reduction:** Further processes the data with t-SNE and UMAP to capture non-linear relationships between data points.
5. **Cluster Identification:** Employs clustering algorithms to classify data points into distinct groups based on their similarity in the reduced-dimensional space.

**Outputs:**  
- **Dimensionality Reduction Data Files:** RDS files for PCA, t-SNE, and UMAP results saved in the `rds_data/03_DR/` directory.
- **Plots:** Generates plots illustrating the distribution of data points in the PCA, t-SNE, and UMAP reduced space, saved in `plots/03_DR/`.
- **Spatial Plots:** Creates SpatialDimPlots to visualize the spatial distribution of clusters or cell types based on the reduced dimensions.
- **Logs:** Detailed records of the dimensionality reduction process stored in `log/03_DR/`.

**Usage Notes:**  
- The number of principal components retained can significantly affect the analysis outcome. Users may need to adjust this based on the variance explained by each component.
- Careful examination of cluster plots and spatial plots is essential to validate the biological relevance of the identified clusters.
- Ensure that the directories for input files and output locations are correctly set in the script, matching the organization of your data and analysis environment.

### Clustering

#### Detailed Description of `04_Clustering_t.qmd`

**Purpose:**  
The script is pivotal in the stRNAseq analysis pipeline, aiming to group cells or spatial transcriptomics data spots into clusters based on their gene expression profiles. Clustering allows the identification of cellular heterogeneity within the samples, uncovering various cell types or states present in the tissue.

**Inputs:**  
The script requires pre-processed and dimensionally reduced data as input, typically in the form of a UMAP matrix:
- **Directory (`rds_data/03_DR/`):** Stores RDS files (`*_UMAP_matrix.rds`) with the UMAP reduced data, which are used for clustering.

**Processing Steps:**  
1. **Data Preparation:** Begins by loading UMAP data for the subsequent clustering process.
2. **Graph-Based Clustering:** Utilizes the shared nearest neighbor (SNN) approach to detect communities or clusters within the data.
3. **K-means Clustering:** Implements k-means to partition spots into k distinct clusters based on PCA embeddings.
4. **Hierarchical Clustering:** Applies hierarchical clustering using different distance measures to reveal the hierarchical structure of data groupings.
5. **Cluster Validation:** Employs various visualization tools like clustree, UMAP, violin plots, and frequency plots to validate and interpret the clustering results.
6. **Marker Identification:** Determines marker genes for each cluster to characterize the unique gene expression profile of each group.

**Outputs:**  
- **Clustering Data Files:** Saves RDS files for each clustering result (`_Snn_matrix.rds`, `_kmeans_matrix.rds`, `_hc_matrix.rds`) in the `rds_data/04_CL/` directory.
- **Visualization Plots:** A variety of plots are generated to visualize and assess the quality of clusters in the `plots/04_CL/`.
- **Markers:** A list of identified marker genes for each cluster is saved, providing insights into the biological characteristics of each group.
- **Logs:** A comprehensive record of the clustering process is maintained in `log/04_CL/`.

**Usage Notes:**  
- Users should critically assess the number of clusters generated and may need to adjust parameters according to the complexity of the data.
- The interpretability of clusters should be validated with biological knowledge to ensure that the clusters represent meaningful biological entities.
- Directory paths for input and output should align with the user's file system structure and data organization.

### Annotation and Cell Type Identification

#### Detailed Description of `05_Annot_test.qmd`

**Purpose:**  
The script is a crucial component of the stRNAseq analysis workflow, dedicated to the annotation and identification of cell types from the clustered spatial transcriptomics data. Annotation leverages known gene signatures to assign biological meaning to the previously defined clusters, facilitating the understanding of the tissue composition and cell-type distribution within the sample.

**Inputs:**  
The script operates on clustered data, which should be available in the following format:
- **Directory (`rds_data/04_CL/`):** This directory should contain RDS files with clustering results (`*_Snn_matrix.rds`, `*_kmeans_matrix.rds`, `*_hc_matrix.rds`), providing the base for cell type annotation.

**Processing Steps:**  
1. **Data Retrieval:** Loads the clustering result files for each sample to start the annotation process.
2. **Marker Gene Association:** Utilizes the markers identified from clustering to match with known cell type-specific gene signatures.
3. **Cell Type Prediction:** Applies algorithms and scoring systems to predict the most likely cell type for each cluster based on gene expression patterns.
4. **Annotation Validation:** Uses visual aids such as UMAP plots overlaid with predicted cell types and marker expression profiles to validate annotations.
5. **Differential Expression Analysis:** Conducts tests to find genes differentially expressed between predicted cell types, aiding in the refinement of annotations.

**Outputs:**  
- **Annotated Data Files:** Stores the annotated Seurat objects with cell type predictions as RDS files in the `rds_data/AA_Seurat/` directory.
- **Visualizations:** Generates plots to display cell type distributions and marker expressions in the `plots/AA_Seurat/`.
- **Cell Type Markers:** Records a list of cell type-associated markers used for annotations, alongside differential expression results.
- **Logs:** Detailed logs of the annotation process are saved in `log/AA_Seurat/` for review and reproducibility.

**Usage Notes:**  
- Annotations should be corroborated with external datasets or literature to ensure accuracy.
- Users may need to integrate additional gene signature databases or adjust scoring thresholds to improve the precision of cell type identification.
- It's recommended to iteratively refine annotations by incorporating expert knowledge and biological context.
- Ensure the file paths used for input and output are consistent with the local data storage organization.

