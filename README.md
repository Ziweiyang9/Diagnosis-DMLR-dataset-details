# Diagnosis-DMLR-dataset-details

***Data Preprocessing Documentation***

**Processing Raw Sequencing Data**

The initial raw FASTQ files were converted into a format compatible with the Cell Ranger pipeline using custom Perl scripts. Following this transformation, each FASTQ file underwent processing through a modified version of the Cell Ranger count pipeline, utilizing STAR (v2.5.3) (Dobin et al., 2013) for alignment against the GRCh38.p5 human reference genome. During this step, cDNA reads were mapped, and the resulting alignments were filtered to retain only valid cell barcodes and unique molecular identifiers (UMIs), generating gene-cell count matrices for downstream analysis.

**Processing and Quality Control of Gene-Cell Matrices**

In addition to standard gene expression count matrices, intronic read-aligned count matrices were also generated to facilitate computational trajectory inference.

To ensure data quality, a series of filtering steps were applied to exclude low-quality cells and potential technical artifacts:

* Cells with fewer than 1,000 UMIs or more than 15,000 UMIs were removed to filter out empty droplets and highly multiplet-prone cells.

* Cells exhibiting >20% of reads from mitochondrial genes or ribosomal RNA genes (RNA18S5 or RNA28S5) were excluded, as high mitochondrial content often indicates cellular stress or apoptosis.

* To eliminate multiplets, cells expressing more than 75 genes per 100 UMIs were discarded, as this suggests multiple cells being captured in a single droplet.

* Genes detected in fewer than 10 cells were removed to reduce noise from rare or low-confidence transcripts.

**Final Data Filtering Criteria**

After initial filtering, additional thresholds were applied to refine the dataset:

* Cells with unusually high or low total RNA counts were excluded to remove extreme outliers.

* Only cells containing between 300 and 6,500 unique features were retained, ensuring the inclusion of biologically relevant single cells while removing doublets or insufficiently sequenced cells.

* Cells with more than 10% mitochondrial RNA content were removed to minimize artifacts related to damaged or dying cells.

This preprocessing strategy ensured a high-quality dataset by reducing technical noise while preserving meaningful biological variation for downstream analysis.


***Benchmarking results***

**Table 1: Clustering performance on Zhu and Waickman datasets. No batch correction was performed unless indicated.**

| **Training Dataset** | **Method** | **ARI** | **NMI** | **S-score** | **Accuracy (K=1)** | **Accuracy (K=5)** |
|-------------|-------------|-------------|-------------|-------------|-------------|-------------|
|  T cell only | PCA  | 0.3038  | 0.4243  | 0.1060  | 0.83  | 0.87  |
|  T cell only | SAVER  | 0.2418  | 0.4900  | 0.2106  | N/A  | N/A  |
|  T cell only | scGPT  | 0.2396  | 0.4246  | 0.2388  | 0.87  | 0.89  |
|  T cell only | contrastiveVI  | 0.3001  | 0.5084  | 0.3145  | 0.87  | 0.89  |
|  Full PBMC | scGPT -> contrastiveVI  | 0.3581  | 0.5357  | 0.3421  | 0.86  | 0.89  |
|  Full PBMC | contrastiveVI -> contrastiveVI | 0.1642  | 0.2188  | 0.0949  | 0.56  | 0.61  |

*Note: SAVER model nearest-neighbor accuracy is not included as it does not produce a latent space representation.*

*Note: scGPT -> contrastiveVI: Full PBMC data was batch corrected using scGPT before being utilized for training with contrastiveVI.*

*Note: contrastiveVI -> contrastiveVI: Full PBMC data was batch corrected using contrastiveVI’s built-in function before being utilized for training with contrastiveVI.*


**Table 2: Clustering performance of various models on the Zhu and Waickman datasets. No batch correction was performed.**

| **Training Dataset** | **Method** | **ARI** | **NMI** | **S-score** | **Accuracy (K=1)** | **Accuracy (K=5)** |
|-------------|-------------|-------------|-------------|-------------|-------------|-------------|
|  Zhu + Waickman | contrastiveVI  | 0.3871  | 0.5597  | 0.3594  | 0.88  | 0.90  |
|  Zhu + Waickman | scVI  | 0.3822  | 0.5574  | 0.3594  | 0.85  | 0.86  |
|  Zhu + Waickman | SAVER  | 0.1884  | 0.4245  | 0.0973  | N/A  | N/A  |
|  Zhu + Waickman | PCA  | 0.2374  | 0.3985  | -0.0744  | 0.83  | 0.87  |
|  Zhu + Waickman | scGPT  | 0.1982  | 0.3789  | 0.1224  | 0.86  | 0.88  |

*Note: SAVER model nearest-neighbor accuracy is not included as it does not produce a latent space representation.*



***Manuscript Dataset Links***:

Zhu Dataset Link:       https://db.cngb.org/search/project/CNP0001102/

Waikman Dataset Link:   https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE154386

Blish Dataset Link:     https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE150728
