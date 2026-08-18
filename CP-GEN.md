# CP-GEN Framework Pipeline

## Overview

This study presents CP-GEN, a cluster-aware genomic framework for characterizing
variant burden across global populations. The framework integrates multiple
open-source datasets, identifies population clusters based on genetic similarity,
and builds cluster-aware machine learning models to estimate genome-level SNP
counts for target genes within a disease-associated gene set. It then extends to a
related disease by reusing the population cluster structure and using genes shared
between the two diseases as a feature bridge.

CP-GEN is evaluated on two disease pairs:

| Case study | Clustering gene set | Bridge genes | Target genes | Notebooks |
|---|---|---|---|---|
| ALS → FTD (neurodegenerative) | 32 ALS feature genes | VCP, TBK1, OPTN, CCNF, SQSTM1, TIA1 | GRN, MAPT, CHMP2B | `notebooks/als-ftd/` |
| CD → AS (inflammatory) | 25 CD feature genes | IL12B, CARD9, TNF, GPR35, ZMIZ1, KIF21B | STAT3, HLA-B, ERAP1 | `notebooks/cd-as/` |

In each case study the three in-disease target genes are excluded from population
clustering and from the feature set, so clustering uses only the feature genes
(32 of 35 ALS genes; 25 of 28 CD genes).

Across both case studies, 1,350 machine learning models were trained and evaluated
using variant data from 25 global populations and more than 3,000 genomes. Each of
the six experiments below contributes 225 models
(3 ML algorithms × 3 target genes × 25 populations): the in-disease and
cross-disease runs for each of the two case studies, plus their baseline
comparisons.

---

## Step 1 — Data Collection

We collected open-source genomic datasets from several sources:

- hg38 (GRCh38) variant data from the 1000 Genomes Project
- 25 global populations from NCBI
- Each population includes approximately 60–180 genomes
- Variant positions across the disease-associated gene sets from Coriell Institute

These datasets provide a diverse representation of global disease-related genetic
variation.

### Populations (Region-Colored)

| Region     | Population Name                                 | Code | Samples | Country      |
|------------|--------------------------------------------------|------|---------|--------------|
| 🟩 Africa  | Esan in Nigeria                                 | ESN  | 173     | Nigeria      |
| 🟩 Africa  | Gambian in Western Division – Mandinka          | GWD  | 179     | Gambia       |
| 🟩 Africa  | Luhya in Webuye, Kenya                          | LWK  | 120     | Kenya        |
| 🟩 Africa  | Mende in Sierra Leone                           | MSL  | 128     | Sierra Leone |
| 🟩 Africa  | Yoruba in Ibadan, Nigeria                       | YRI  | 120     | Nigeria      |
| 🟦 Americas| African Ancestry in SW USA                      | ASW  | 62      | USA          |
| 🟦 Americas| African Caribbean in Barbados                   | ACB  | 120     | Barbados     |
| 🟦 Americas| Colombian in Medellín, Colombia                 | CLM  | 136     | Colombia     |
| 🟦 Americas| Mexican Ancestry in Los Angeles, USA            | MXL  | 71      | USA          |
| 🟦 Americas| Peruvian in Lima, Peru                          | PEL  | 122     | Peru         |
| 🟦 Americas| Puerto Rican in Puerto Rico                     | PUR  | 139     | Puerto Rico  |
| 🟧 East Asia| Chinese Dai in Xishuangbanna, China             | CDX  | 102     | China        |
| 🟧 East Asia| Han Chinese in Beijing, China                   | CHB  | 120     | China        |
| 🟧 East Asia| Han Chinese South                               | CHS  | 163     | China        |
| 🟧 East Asia| Japanese in Tokyo, Japan                        | JPT  | 120     | Japan        |
| 🟧 East Asia| Kinh in Ho Chi Minh City, Vietnam               | KHV  | 124     | Vietnam      |
| 🟪 Europe  | British From England and Scotland               | GBR  | 100     | UK           |
| 🟪 Europe  | Finnish in Finland                              | FIN  | 103     | Finland      |
| 🟪 Europe  | Iberian Populations in Spain                    | IBS  | 157     | Spain        |
| 🟪 Europe  | Toscani in Italia                               | TSI  | 114     | Italy        |
| 🟨 South Asia| Bengali in Bangladesh                           | BEB  | 144     | Bangladesh   |
| 🟨 South Asia| Gujarati Indians in Houston, USA                | GIH  | 109     | USA          |
| 🟨 South Asia| Indian Telugu in the U.K.                       | ITU  | 118     | UK           |
| 🟨 South Asia| Punjabi in Lahore, Pakistan                     | PJL  | 158     | Pakistan     |
| 🟨 South Asia| Sri Lankan Tamil in the UK                      | STU  | 128     | UK           |


**Reference**: https://www.coriell.org/1/NHGRI/Collections/1000-Genomes-Project-Collection/1000-Genomes-Project 


---

## Step 2 — Data Preprocessing

The collected datasets were cleaned, standardized, and merged into a unified
structure. Only SNPs are retained — INDELs are filtered out — and genotypes are
binarized to 0/1 per genome.

The final dataset forms a three-dimensional variant data cube:

Population × Genome × Gene

This cube represents variant counts for each gene across all genomes and
populations.

---

## Step 3 — Population Clustering

To identify genetically similar populations, we applied three clustering
algorithms:

- KMeans
- Gaussian Mixture Models
- Agglomerative Clustering

Clustering was performed with k = 3, 5, and 7 clusters.

The input to each clustering algorithm was a two-dimensional matrix:

- Rows represent 25 populations
- Columns represent the disease feature genes (32 for ALS, 25 for CD — the three
  target genes of each disease are excluded)
- Each cell contains the mean variant count per gene.

The selected assignments are saved per case study as
`*_population2cluster_AggCluster_k5.csv`.

---

## Step 4 — Cluster Validation

Cluster quality was evaluated using both objective metrics and visualization
methods.

### Objective Validation

Cluster quality was measured using the Silhouette Coefficient, a standard
metric widely used in data mining.

The highest Silhouette score was obtained when k = 5, indicating optimal
cluster structure.

### Visualization Validation

We performed validation at three biological scales.

**Population Level:**

t-SNE was used to project the variant cube into a 25 × 2 matrix for
visualization.

**Genome Level:**

PCA was used to project genomes within populations into two-dimensional
space, allowing visualization of genome distributions across populations.

**Gene Level:**

t-tests were performed for the major genes of each disease (SOD1, FUS, and
TARDBP for ALS; NOD2, IL23R, and ATG16L1 for CD), and visualized as gene-level
statistical heatmaps.

Gene-level comparisons showed significant differences between clusters.

### Validation Outcome

All validation analyses showed that populations within the same cluster share
strong genetic similarity, while populations in different clusters are clearly
separated.

Based on these results, k = 5 clusters were selected for downstream modeling.

---

## Step 5 — In-Disease Machine Learning Models

Within each cluster, machine learning models were developed to estimate
genome-level SNP counts for target genes of the first disease.

**Models used:**

- Linear Regression
- Random Forest Regressor
- Support Vector Regressor

**Training Strategy:**

Within each cluster, all populations except one were used as the training set.
The remaining population was used as the test set. This process was repeated
so each population served as a test population (leave-one-population-out
within cluster).

**Target Variables:**

- ALS: SOD1, FUS, TARDBP
- CD: NOD2, IL23R, ATG16L1

**Features:**

For one genome, the variant counts across the feature genes of the disease — the
32 remaining ALS genes, or the 27 remaining CD genes. All three target genes of the
disease are held out of the feature set (and out of clustering) so that no target
gene contributes to estimating another.

**Performance Metrics:**

- RMSE
- MAE

**Method Comparison:**

Performance of CP-GEN was compared against three baselines:

- Cluster-Pair: One cluster used for training and another for testing
- Leave-One-Cluster-Out (LOCO): All clusters except one used for training
- GroupMean: Predicts the mean variant count of the target gene across all
  training genomes in the same cluster — a single constant per population

CP-GEN significantly outperforms Cluster-Pair and LOCO. Against GroupMean it
achieves comparable mean RMSE with lower variance, while additionally providing
per-genome estimates where GroupMean can only supply one constant for every
genome in the target population.

Dot plots with 95% confidence intervals were used to visualize performance.

---

## Step 6 — Cross-Disease Transfer Learning

To evaluate cross-disease predictive power, transfer learning experiments
were performed by reusing the population clusters of the first disease.

Machine learning models were trained to estimate variant counts of the target
genes of the second disease:

- ALS → FTD: MAPT, GRN, CHMP2B
- CD → AS: STAT3, HLA-B, ERAP1

Features consisted of variant counts of the genes shared between the two
diseases (the bridge genes listed in the Overview).

**Training Strategy:**

Within each cluster, all populations except one were used for training and
the remaining population was used for testing.

**Performance Metrics:**

- RMSE
- MAE

Dot plots with 95% confidence intervals were used to evaluate model
performance.

---

## Conclusions

The CP-GEN framework demonstrates that clustering populations based on genetic
similarity supports genome-level characterization of variant burden within a
disease gene set.

The framework also shows transferability between related diseases — ALS to FTD
and CD to AS — suggesting shared genetic architecture within each disease pair,
and offering a route to estimating variant burden for understudied genes in
underrepresented populations.
