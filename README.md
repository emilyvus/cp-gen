# CP-GEN: Cluster-Aware Framework for Characterizing Variant Burden in Underrepresented Populations

Public genomic variant databases are disproportionately built from individuals of European ancestry, limiting the characterization of variant burden in underrepresented populations. Within these populations, variant data are often unevenly distributed across genes, leaving many genes understudied. We introduce CP-GEN, a characterization tool that leverages population-cluster structures and multigene variant profiles for genome-level estimation of target-gene Single Nucleotide Polymorphisms (SNP) counts within selected disease-associated gene sets. Specifically, CP-GEN clusters populations using genomic variant data and assesses cluster quality through the Silhouette Coefficient, complemented by visual validation at the population level (t-distributed Stochastic Neighbour Embedding), genome level (Principal Component Analysis), and gene level (statistical heatmaps). It then trains cluster-aware machine learning models to estimate genome-level SNP counts for target genes by exploiting variant patterns of genomes within the same population cluster. For related disease pairs, CP-GEN reuses the population cluster of the first disease, and uses genes shared between diseases as a feature bridge to estimate genome-level SNP counts for target genes in the second disease. CP-GEN was evaluated on two disease pairs, Amyotrophic Lateral Sclerosis and Frontotemporal Dementia (ALS/FTD), and Crohn’s Disease and Ankylosing Spondylitis (CD/AS), with 1,350 trained machine learning models using variant data from 25 global populations and more than 3,000 genomes. In both case studies, CP-GEN significantly outperforms Cluster-Pair and Leave-One-Cluster-Out baselines. CP-GEN also achieves comparable mean Root Mean Square Error with a lower variance against GroupMean baseline. Further, CP-GEN provides genome-level SNP count estimates while GroupMean can only use one constant estimate for all genomes in the target population. These results indicate that CP-GEN effectively leverages population-cluster structure and multigene SNP-count profiles for genome-level variant burden characterization.

## Publications & Presentations

- **CP-ALS: A Novel Cross-Population Framework for Amyotrophic Lateral Sclerosis Genomic Analysis & Modeling**, Emily Vu, Poster Presentation, 9th Annual HeLa: Ethics, Research and Access Conference and Research Symposium, New York Medical College, 02/2026.

## Documentation

- **[CP-GEN.md](CP-GEN.md)** — the framework pipeline end to end: data collection, preprocessing, population clustering, cluster validation, in-disease models, and cross-disease transfer learning.
- **[INSTALL.md](INSTALL.md)** — setting up the `cp-gen` conda environment, installing dependencies, registering the Jupyter kernel, and launching the notebooks.

## Layout

```
├── README.md                                             # this file: abstract, layout, notebook index
├── CP-GEN.md                                             # framework pipeline: data, clustering, validation, models
├── INSTALL.md                                            # environment setup and how to run the notebooks
├── pyproject.toml                                        # Python dependencies (pip install -e .)
└── notebooks/
    ├── als-ftd/                                          # Case study 1: ALS → FTD
    │   ├── als_clustering_silhouette_coefficient.ipynb
    │   ├── als_tsne.ipynb
    │   ├── als_pca.ipynb
    │   ├── als_statistical_heatmap.ipynb
    │   ├── als2als_cluster_aware_snp_count_estimation.ipynb
    │   ├── als2ftd_cross_disease_snp_count_estimation.ipynb
    │   ├── als_per-genome-cpgen_vs_groupmean.ipynb
    │   ├── als_population2cluster_AggCluster_k5.csv      # k=5 population→cluster assignments
    │   └── mylib.py                                      # helpers (genome IDs, variant counting, stats)
    └── cd-as/                                            # Case study 2: CD → AS
        ├── cd_clustering_silhouette_coefficent.ipynb
        ├── cd_tsne.ipynb
        ├── cd_pca.ipynb
        ├── cd_statistical_heatmap.ipynb
        ├── cd2cd_cluster_aware_snp_count_estimation.ipynb
        ├── cd2as_cross_disease_snp_count_estimation.ipynb
        ├── cd_per-genome-cpgen_vs_groupmean.ipynb
        ├── cd_population2cluster_AggCluster_k5.csv       # k=5 population→cluster assignments
        └── mylib.py                                      # helpers (local copy)
```

## Notebooks

| # | Notebook | Description |
|---|---|---|
| 1 | `als-ftd/als_clustering_silhouette_coefficient.ipynb` | Agglomerative clustering of the 25 populations on ALS-gene mean variant-count profiles. |
| 2 | `als-ftd/als_tsne.ipynb` | Population-level validation: t-SNE embedding of the ALS variant profiles to visually confirm that the k=5 population clusters separate. |
| 3 | `als-ftd/als_pca.ipynb` | Genome-level validation: PCA of the ALS variant profiles as a linear check on cluster structure. |
| 4 | `als-ftd/als_statistical_heatmap.ipynb` | Gene-level validation: Pairwise t-test heatmaps between population clusters show which ALS genes drive the separation. |
| 5 | `als-ftd/als2als_cluster_aware_snp_count_estimation.ipynb` | In-disease CP-GEN: cluster-aware ML models estimating genome-level SNP counts for the target ALS genes (SOD1, FUS, TARDBP), which are excluded from the features. |
| 6 | `als-ftd/als2ftd_cross_disease_snp_count_estimation.ipynb` | Cross-disease transfer: reuses the ALS population cluster structure and the shared bridge genes to estimate genome-level SNP counts for the FTD targets (MAPT, GRN, CHMP2B). |
| 7 | `als-ftd/als_per-genome-cpgen_vs_groupmean.ipynb` | Per-genome SNP count estimation: CP-GEN vs. the GroupMean baseline for the ALS case study. |
| 8 | `cd-as/cd_clustering_silhouette_coefficent.ipynb` | Agglomerative clustering of the 25 populations on CD-gene mean variant-count profiles, validated by Silhouette Coefficient. |
| 9 | `cd-as/cd_tsne.ipynb` | Population-level validation: t-SNE embedding of the CD variant profiles to visually confirm the k=5 CD population clusters. |
| 10 | `cd-as/cd_pca.ipynb` | Genome-level validation: PCA of the CD variant profiles as a linear validation of the CD cluster structure. |
| 11 | `cd-as/cd_statistical_heatmap.ipynb` | Gene-level validation: Pairwise t-test heatmaps between CD population clusters identify the CD genes that separate clusters. |
| 12 | `cd-as/cd2cd_cluster_aware_snp_count_estimation.ipynb` | In-disease CP-GEN for CD: cluster-aware ML models estimating genome-level SNP counts for the CD target genes (NOD2, IL23R, ATG16L1), which are excluded from the features. |
| 13 | `cd-as/cd2as_cross_disease_snp_count_estimation.ipynb` | Cross-disease transfer: reuses the CD cluster structure and the CD/AS shared bridge genes to estimate genome-level SNP counts for the AS targets (STAT3, HLA-B, ERAP1). |
| 14 | `cd-as/cd_per-genome-cpgen_vs_groupmean.ipynb` | Per-genome SNP count estimation: CP-GEN vs. the GroupMean baseline for the CD/AS case study. |

---------------

Contact: Emily Vu (emilyvu09@gmail.com)

