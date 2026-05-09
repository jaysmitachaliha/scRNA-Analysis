# Single-Cell RNA-seq Analysis: GSE184880

**Author:** [Jaysmita Chaliha](https://github.com/jaysmitachaliha)

A Scanpy-based scRNA-seq analysis pipeline applied to the publicly available dataset **GSE184880**, which profiles high-grade serous ovarian cancer (HGSOC) and matched normal ovarian tissue samples.

> **New here?** Start by running **Section 0** in the notebook — it handles organising the raw data automatically.

---

## Overview

This notebook walks through a complete single-cell transcriptomics workflow:

| Step | Description |
|------|-------------|
| 0. Data Setup | Organise downloaded GEO files into per-sample directories |
| 1. Data Import & QC | Load 10x Genomics MTX files, filter low-quality cells |
| 2. Cell Cycle Regression | Score and regress out S/G2M phase effects |
| 3. Dimensionality Reduction | PCA → neighbour graph → UMAP → Leiden clustering |
| 4. Cell Type Annotation | Marker-based broad lineage + cluster-state labels |
| 5. Differential Expression | Tumour vs Immune; Cancer vs Normal (Wilcoxon) |
| 6. Immune Subclustering | Re-cluster immune compartment; functional state scoring |
| 7. Immune Functional Interpretation | Exhaustion, cytotoxicity, and checkpoint scoring |
| 8. Pathway Enrichment | Enrichr and GSEA against Hallmark / KEGG / Reactome / GO |
| 9. Final Save | Export AnnData objects and metadata CSVs |

---

## Dataset

- **Accession:** [GSE184880](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE184880)
- **Title:** Single-cell RNA sequencing reveals tissue architecture during human high-grade serous ovarian cancer progression
- **Tissue:** Ovarian — HGSOC tumour and matched normal ovarian tissue
- **Conditions:** Cancer (HGSOC, early and late stage) vs Normal (age-matched non-malignant ovarian samples)
- **Samples:** 12 total — 5 Normal, 7 Cancer (HGSOC1–7)
- **Cells:** 59,324 total
- **Platform:** Illumina NovaSeq 6000 via 10x Genomics Chromium
- **Citation:** Xu et al. (2022) — Women's Hospital, Zhejiang University School of Medicine

---

## Requirements

Install all dependencies via pip using the provided requirements file:

```bash
pip install -r requirements.txt
```

**Key packages:**

| Package | Purpose |
|---------|---------|
| `scanpy` | Core scRNA-seq analysis |
| `leidenalg` | Leiden community detection |
| `igraph` | Graph backend for clustering |
| `gseapy` | Pathway enrichment (Enrichr / GSEA) |
| `pandas`, `numpy` | Data manipulation |
| `matplotlib` | Plotting |

---

## Usage

1. **Install dependencies:**

    ```bash
    pip install -r requirements.txt
    ```

2. **Download the raw data** from [GEO: GSE184880](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE184880) and place all downloaded `.gz` files into a folder on your machine.

3. **Open the notebook** and run **Section 0 (Data Setup)** — set `raw_dir` to the folder containing your downloaded files. This script will automatically organise them into the expected `data/` structure:

    ```
    data/
    ├── GSM5599220_Norm1/
    │   ├── matrix.mtx.gz
    │   ├── barcodes.tsv.gz
    │   └── features.tsv.gz
    ├── GSM5599225_Cancer1/
    │   └── ...
    ```

4. **Run the notebook** top to bottom:

    ```bash
    jupyter notebook scRNA_Analysis_Executed.ipynb
    ```

> The `data/` folder and all output files are excluded from version control via `.gitignore`. Only the notebook and README are tracked.

---

## Outputs

| File | Description |
|------|-------------|
| `GSE184880_scanpy_FULL_ANALYSIS.h5ad` | Full AnnData object |
| `adata_tumour_only.h5ad` | Tumour epithelial subset |
| `adata_immune_only.h5ad` | Immune cell subset |
| `GSE184880_scanpy_immune_subclusters.h5ad` | Immune subclustering AnnData |
| `GSE184880_scanpy_leiden_cc_regressed_markers.csv` | Leiden cluster marker genes |
| `tumour_vs_immune_DEGs.csv` | Tumour vs Immune DEGs |
| `GSE184880_tumour_Cancer_vs_Normal_DEGs.csv` | Cancer vs Normal DEGs (tumour only) |
| `GSE184880_immune_cycling_vs_noncycling_DEGs.csv` | Cycling vs Non-cycling immune DEGs |
| `immune_subcluster_markers.csv` | Immune subcluster marker genes |
| `tumour_vs_immune_GSEA_Hallmark.csv` | GSEA results — Tumour vs Immune (Hallmark) |
| `tumour_cancer_vs_normal_GSEA_Hallmark.csv` | GSEA results — Cancer vs Normal (Hallmark) |
| `cell_metadata.csv` | Per-cell metadata |
| `gene_metadata.csv` | Per-gene metadata |
| `Analysis_Parameters.txt` | Summary of key analysis parameters |

---

## Notes

- Cell cycle genes are sourced from [Tirosh et al. (2016)](https://www.science.org/doi/10.1126/science.aad0501).
- Clustering uses Leiden at `resolution=0.3`; adjust as needed for your data.
- GSEA permutation count is set to 100 (sufficient for scRNA-seq ranking; increase to 1000 for publication-grade FDR estimates).
- All figures are generated inline via Scanpy's `sc.pl` functions.
