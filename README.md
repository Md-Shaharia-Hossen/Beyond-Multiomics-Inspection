# Supplementary Materials
## License
Copyright © 2026 **Md Shaharia Hossen**.

This project is licensed under the **MIT License**. See the [LICENSE](LICENSE) file for the full license text.

The MIT License permits use, copying, modification, merging, publishing, distribution, sublicensing, and sale of copies of the software, subject to the license conditions.

```markdown
# Results

# Density-Aware Characterization of Low-Density Samples in BRCA Multi-Omics

## Experimental Parameters

### Dataset and Preprocessing

| Parameter              | DNA Methylation |           mRNA |          miRNA |
| ---------------------- | --------------: | -------------: | -------------: |
| Samples                |             875 |            875 |            875 |
| Features               |           1,000 |          1,000 |            503 |
| Standardization        |  StandardScaler | StandardScaler | StandardScaler |
| PCA Variance Threshold |             95% |            95% |            95% |
| PCA Components         |             229 |            239 |            222 |
| Matched PCA Rank       |              14 |             15 |             29 |
| Discovery Samples      |             612 |            612 |            612 |
| Inference Samples      |             263 |            263 |            263 |

---

## Denoising Autoencoder Parameters

| Parameter                |            Value |
| ------------------------ | ---------------: |
| Architecture             |  Encoder–Decoder |
| Latent Dimension         |               64 |
| Hidden Activation        |             ReLU |
| Batch Size               |               64 |
| Epochs                   |              500 |
| Optimizer                |             Adam |
| Initial Learning Rate    | $1\times10^{-4}$ |
| Final Learning Rate      | $1\times10^{-5}$ |
| Learning Rate Schedule   | Cosine Annealing |
| Dropout                  |             0.30 |
| L2 Regularization        | $1\times10^{-6}$ |
| Latent L1 Regularization | $1\times10^{-5}$ |
| Reconstruction Losses    | MSE, Huber, MSLE |
| Primary DAE              |          DAE-MSE |

---

## HDBSCAN Grid Search

HDBSCAN parameters were optimized independently for each modality using a 121-combination grid.

### `min_cluster_size`

```text
5, 10, 20, 30, 40, 50, 60, 70, 80, 90, 100
```

### `min_samples`

```text
1, 2, 5, 10, 15, 20, 25, 30, 35, 40, 45
```

### Search Configuration

| Parameter                | Value                       |
| ------------------------ | --------------------------- |
| Total Combinations       | 121                         |
| Representation           | PCA                         |
| PCA Variance             | 95%                         |
| Minimum Valid Clusters   | 2                           |
| Noise Label              | -1                          |
| Silhouette Weight        | 0.40                        |
| Calinski–Harabasz Weight | 0.30                        |
| Davies–Bouldin Weight    | 0.30                        |
| Optimization             | Maximum composite score $Q$ |

The composite score was calculated as:

```text
Q = 0.40 × Silhouette_norm
  + 0.30 × CH_norm
  + 0.30 × DB_norm
```

where the Davies–Bouldin component was inverted because lower Davies–Bouldin values indicate better cluster separation.

---

## Optimized HDBSCAN Parameters and Results

| Modality        | PCA Components | `min_cluster_size` ($c^{**}$) | `min_samples` ($m^{**}$) | Clusters | Noise | Noise (%) | Silhouette |     DB |      CH |      Q |
| --------------- | -------------: | ----------------------------: | -----------------------: | -------: | ----: | --------: | ---------: | -----: | ------: | -----: |
| DNA Methylation |            229 |                            10 |                       10 |        2 |   469 |    53.60% |     0.2971 | 0.8735 | 32.8441 | 0.7825 |
| mRNA            |            239 |                             5 |                        1 |        3 |   160 |    18.29% |     0.3935 | 1.6744 | 17.0638 | 0.5070 |
| miRNA           |            222 |                             5 |                        5 |        2 |   629 |    71.89% |     0.2250 | 1.3466 | 32.4012 | 0.9185 |

---

## Density Characterization Parameters

| Parameter                      | Definition                                  |
| ------------------------------ | ------------------------------------------- |
| Membership Probability ($p_i$) | HDBSCAN membership confidence               |
| GLOSH ($g_i$)                  | Global-Local Outlier Score from Hierarchies |
| NCS                            | Noise Confidence Score                      |
| NCS Formula                    | $NCS_i=(1-p_i)g_i$                          |
| $p_i$                          | HDBSCAN membership probability              |
| $g_i$                          | GLOSH outlier score                         |

### GLOSH Categories

| GLOSH Score | Category |
| ----------: | -------- |
|      < 0.20 | Normal   |
|   0.20–0.40 | Weak     |
|   0.40–0.60 | Moderate |
|   0.60–0.80 | Strong   |
|      ≥ 0.80 | Extreme  |

---

# Reconstruction Performance

## DNA Methylation

| Method    |      MAE |      MSE |     RMSE |    MaxAE |       R² |
| --------- | -------: | -------: | -------: | -------: | -------: |
| DAE-MSE   | 0.410768 | 0.348172 | 0.590061 | 6.509339 | 0.667135 |
| DAE-Huber | 0.411283 | 0.356264 | 0.596878 | 8.069833 | 0.659765 |
| DAE-MSLE  | 0.102010 | 0.017898 | 0.133782 | 0.894236 | 0.313860 |
| PCA-14    | 0.381481 | 0.301530 | 0.549118 | 6.559938 | 0.710423 |

## mRNA

| Method    |      MAE |      MSE |     RMSE |    MaxAE |       R² |
| --------- | -------: | -------: | -------: | -------: | -------: |
| DAE-MSE   | 0.453642 | 0.393874 | 0.627594 | 5.281567 | 0.609823 |
| DAE-Huber | 0.458973 | 0.403344 | 0.635093 | 5.109358 | 0.600541 |
| DAE-MSLE  | 0.097792 | 0.017203 | 0.131161 | 0.945226 | 0.389226 |
| PCA-15    | 0.416471 | 0.334150 | 0.578057 | 5.674675 | 0.668266 |

## miRNA

| Method    |      MAE |      MSE |     RMSE |     MaxAE |        R² |
| --------- | -------: | -------: | -------: | --------: | --------: |
| DAE-MSE   | 0.556055 | 0.591813 | 0.769294 | 10.467917 |  0.430031 |
| DAE-Huber | 0.561290 | 0.636429 | 0.797765 | 10.796426 |  0.399917 |
| DAE-MSLE  | 0.190014 | 0.094306 | 0.307093 |  2.347193 | -5.032233 |
| PCA-29    | 0.481559 | 0.447528 | 0.668975 |  9.504422 |  0.565749 |

---

# Matched-Rank PCA vs DAE Density Characterization

| Modality    | Representation   | Clusters | Noise | Noise (%) | Mean Probability | Mean GLOSH |
| ----------- | ---------------- | -------: | ----: | --------: | ---------------: | ---------: |
| Methylation | PCA-14           |        2 |    74 |    56.06% |           0.4146 |     0.2431 |
| Methylation | DAE-MSE → PCA-14 |        2 |    45 |    34.09% |           0.5776 |     0.2867 |
| mRNA        | PCA-15           |        2 |     4 |     3.03% |           0.9562 |     0.2132 |
| mRNA        | DAE-MSE → PCA-15 |        3 |     2 |     1.52% |           0.9222 |     0.2644 |
| miRNA       | PCA-29           |        2 |    83 |    62.88% |           0.3653 |     0.2178 |
| miRNA       | DAE-MSE → PCA-29 |        2 |    36 |    27.27% |           0.6346 |     0.2838 |

---

# Discovery–Inference Analysis

| Modality        | Discovery Samples | Discovery Clusters | Discovery Noise | Inference Samples | Inference Noise | Inference Clustered | Inference Noise (%) |
| --------------- | ----------------: | -----------------: | --------------: | ----------------: | --------------: | ------------------: | ------------------: |
| DNA Methylation |               612 |                  2 |             505 |               263 |             230 |                  33 |              87.45% |
| mRNA            |               612 |                  8 |             117 |               263 |              54 |                 209 |              20.53% |
| miRNA           |               612 |                  3 |             316 |               263 |             114 |                 149 |              43.35% |

---

# Differential Molecular Analysis

| Modality        | Total Features | Significant | Upregulated | Downregulated | Non-significant |
| --------------- | -------------: | ----------: | ----------: | ------------: | --------------: |
| DNA Methylation |          1,000 |           4 |           4 |             0 |             996 |
| mRNA            |          1,000 |          24 |          20 |             4 |             976 |
| miRNA           |            503 |          40 |          40 |             0 |             463 |

Significance threshold:

```text
FDR < 0.05
```

---

# Top Differential Features

## DNA Methylation

| Feature  | Direction |
| -------- | --------- |
| MIR124-2 | Up        |
| PSAT1    | Up        |
| POU4F1   | Up        |
| DOK5     | Up        |

## mRNA

| Feature  | Direction |    Effect |    p-value |        FDR |
| -------- | --------- | --------: | ---------: | ---------: |
| SCN7A    | Down      | -1.349707 | 1.68×10⁻¹⁵ | 1.68×10⁻¹² |
| SCN2B    | Down      |         — |          — | 1.05×10⁻¹⁰ |
| CLDN19   | Down      |         — |          — | 6.35×10⁻¹⁰ |
| ANKRD30A | Down      |         — |          — |  5.90×10⁻⁸ |
| TLX3     | Up        |    2.3065 |  8.96×10⁻⁸ |  4.57×10⁻⁷ |
| DMRT1    | Up        |    1.6953 |  4.19×10⁻⁷ |  1.65×10⁻⁶ |
| PRDM13   | Up        |    2.1871 |  1.05×10⁻⁵ |  2.97×10⁻⁵ |

## miRNA

| Feature      | Direction |   Effect |    p-value |        FDR |
| ------------ | --------- | -------: | ---------: | ---------: |
| hsa-mir-877  | Up        | 1.193182 | 3.77×10⁻¹³ | 3.16×10⁻¹¹ |
| hsa-mir-3940 | Up        | 1.115360 |          — |  1.75×10⁻⁹ |
| hsa-mir-1229 | Up        | 1.293703 |          — |  1.34×10⁻⁸ |
| hsa-mir-301b | Up        | 1.239940 |          — |  5.77×10⁻⁸ |
| hsa-mir-520b | Up        | 2.374791 |          — |  5.31×10⁻⁶ |

---

# Cancer-Associated Annotation

| Modality        | Annotation Records | Unique Features/Genes | Categories |
| --------------- | -----------------: | --------------------: | ---------: |
| DNA Methylation |                  4 |                     2 |          — |
| mRNA            |                 70 |                    23 |         19 |
| miRNA           |                 88 |                    40 |         20 |

---

# Functional Enrichment

## DNA Methylation — GO

Significant adjusted p-values:

```text
0.013771 – 0.019795
```

## DNA Methylation — KEGG

| Pathway                                  |  p-value | Adjusted p-value |
| ---------------------------------------- | -------: | ---------------: |
| Vitamin B6 metabolism                    |   0.0012 |         0.004798 |
| Glycine, serine and threonine metabolism | 0.007977 |         0.013284 |
| Cysteine and methionine metabolism       | 0.009963 |         0.013284 |

## mRNA

| Analysis | FDR < 0.05 |
| -------- | ---------- |
| GO       | No         |
| KEGG     | No         |

## miRNA

| Analysis | Enriched |
| -------- | -------- |
| GO       | No       |
| KEGG     | No       |

---

# Controlled Technical-Noise Experiment

Gaussian perturbations were introduced at four noise levels.

| Gaussian $\sigma$ | Methylation Mean MAE | mRNA Mean MAE | miRNA Mean MAE |
| ----------------: | -------------------: | ------------: | -------------: |
|              0.10 |             0.421909 |      0.461856 |       0.562563 |
|              0.25 |             0.469215 |      0.502348 |       0.594864 |
|              0.50 |             0.595384 |      0.619095 |       0.695288 |
|              1.00 |             0.923746 |      0.936190 |       0.989116 |

### Median MAE

| Gaussian $\sigma$ | Methylation |     mRNA |    miRNA |
| ----------------: | ----------: | -------: | -------: |
|              0.10 |    0.413542 | 0.476109 | 0.533822 |
|              0.25 |    0.457157 | 0.516643 | 0.563012 |
|              0.50 |    0.581756 | 0.622135 | 0.665127 |
|              1.00 |    0.908509 | 0.928163 | 0.965263 |

### Maximum MAE

| Gaussian $\sigma$ | Methylation |     mRNA |    miRNA |
| ----------------: | ----------: | -------: | -------: |
|              0.10 |    0.986248 | 0.862151 | 1.091633 |
|              0.25 |    1.008841 | 0.881504 | 1.114717 |
|              0.50 |    1.078694 | 0.943461 | 1.179524 |
|              1.00 |    1.316851 | 1.174191 | 1.389649 |

---

# HDBSCAN vs Alternative Outlier Detectors

| Modality        | HDBSCAN Noise | HDBSCAN (%) | LOF Outliers | LOF (%) | Isolation Forest | IF (%) |
| --------------- | ------------: | ----------: | -----------: | ------: | ---------------: | -----: |
| DNA Methylation |            74 |      56.06% |            3 |   2.27% |               14 | 10.61% |
| mRNA            |             4 |       3.03% |            1 |   0.76% |               14 | 10.61% |
| miRNA           |            83 |      62.88% |            5 |   3.79% |               11 |  8.33% |

---

# HDBSCAN Label Association

## DNA Methylation

|     Label |  Noise | Clustered |
| --------: | -----: | --------: |
|         0 |      6 |         7 |
|         1 |     18 |         0 |
|         2 |      3 |         1 |
|         3 |      9 |        67 |
|         4 |      9 |        12 |
| **Total** | **45** |    **87** |

χ² = 56.0828, df = 4, p = 1.93×10⁻¹¹, Cramer's V = 0.6304.

## mRNA

|     Label | Noise | Clustered |
| --------: | ----: | --------: |
|         0 |     0 |        13 |
|         1 |     1 |        17 |
|         2 |     1 |         3 |
|         3 |     0 |        76 |
|         4 |     0 |        21 |
| **Total** | **2** |   **130** |

χ² = 18.4462, df = 4, p = 1.01×10⁻³, Cramer's V = 0.3317.

## miRNA

|     Label |  Noise | Clustered |
| --------: | -----: | --------: |
|         0 |      3 |        10 |
|         1 |      9 |         9 |
|         2 |      0 |         4 |
|         3 |     11 |        65 |
|         4 |      6 |        15 |
| **Total** | **29** |   **103** |

χ² = 12.4102, df = 4, p = 0.01455, Cramer's V = 0.2529.

---

# Residual Statistics

| Modality        | Mean MAE | Median MAE |   SD MAE |  Min MAE |  Max MAE |
| --------------- | -------: | ---------: | -------: | -------: | -------: |
| DNA Methylation | 0.213937 |   0.209784 | 0.070156 | 0.079866 | 0.403155 |
| mRNA            | 0.230708 |   0.220124 | 0.067634 | 0.104455 | 0.454712 |
| miRNA           | 0.330487 |   0.315969 | 0.096035 | 0.184467 | 0.677967 |

### RMSE

| Modality        |     Mean |   Median |       SD |      Min |      Max |
| --------------- | -------: | -------: | -------: | -------: | -------: |
| DNA Methylation | 0.269079 | 0.264103 | 0.088269 | 0.101919 | 0.509607 |
| mRNA            | 0.289209 | 0.276246 | 0.084216 | 0.129965 | 0.569005 |
| miRNA           | 0.419625 | 0.400220 | 0.121568 | 0.230163 | 0.840455 |

### Maximum Feature Error

| Modality        |     Mean |  Maximum |
| --------------- | -------: | -------: |
| DNA Methylation | 0.930586 | 1.817833 |
| mRNA            | 0.986129 | 2.083496 |
| miRNA           | 1.447269 | 3.110385 |

---

# Residual–GLOSH Correlation

| Modality        | MAE vs GLOSH ρ |    p-value | RMSE vs GLOSH ρ |    p-value |
| --------------- | -------------: | ---------: | --------------: | ---------: |
| DNA Methylation |       0.586728 | 1.44×10⁻¹³ |        0.598974 | 3.29×10⁻¹⁴ |
| mRNA            |       0.297814 |  5.24×10⁻⁴ |        0.293503 |  6.37×10⁻⁴ |
| miRNA           |       0.600381 | 2.76×10⁻¹⁴ |        0.609015 | 9.35×10⁻¹⁵ |

---




## 1. Reconstruction Performance

### DNA Methylation

| Method | MAE | MSE | RMSE | MaxAE | R² |
|---|---:|---:|---:|---:|---:|
| DAE-MSE | 0.410768 | 0.348172 | 0.590061 | 6.509339 | 0.667135 |
| DAE-Huber | 0.411283 | 0.356264 | 0.596878 | 8.069833 | 0.659765 |
| DAE-MSLE | 0.102010 | 0.017898 | 0.133782 | 0.894236 | 0.313860 |
| PCA-14 | 0.381481 | 0.301530 | 0.549118 | 6.559938 | 0.710423 |

### mRNA

| Method | MAE | MSE | RMSE | MaxAE | R² |
|---|---:|---:|---:|---:|---:|
| DAE-MSE | 0.453642 | 0.393874 | 0.627594 | 5.281567 | 0.609823 |
| DAE-Huber | 0.458973 | 0.403344 | 0.635093 | 5.109358 | 0.600541 |
| DAE-MSLE | 0.097792 | 0.017203 | 0.131161 | 0.945226 | 0.389226 |
| PCA-15 | 0.416471 | 0.334150 | 0.578057 | 5.674675 | 0.668266 |

### miRNA

| Method | MAE | MSE | RMSE | MaxAE | R² |
|---|---:|---:|---:|---:|---:|
| DAE-MSE | 0.556055 | 0.591813 | 0.769294 | 10.467917 | 0.430031 |
| DAE-Huber | 0.561290 | 0.636429 | 0.797765 | 10.796426 | 0.399917 |
| DAE-MSLE | 0.190014 | 0.094306 | 0.307093 | 2.347193 | -5.032233 |
| PCA-29 | 0.481559 | 0.447528 | 0.668975 | 9.504422 | 0.565749 |

---

## 2. Matched-Rank PCA vs DAE Density Characterization

| Modality | Representation | Clusters | Noise | Noise % | Mean Probability | Mean GLOSH |
|---|---|---:|---:|---:|---:|---:|
| Methylation | PCA-14 | 2 | 74 | 56.06% | 0.4146 | 0.2431 |
| Methylation | DAE-MSE → PCA-14 | 2 | 45 | 34.09% | 0.5776 | 0.2867 |
| mRNA | PCA-15 | 2 | 4 | 3.03% | 0.9562 | 0.2132 |
| mRNA | DAE-MSE → PCA-15 | 3 | 2 | 1.52% | 0.9222 | 0.2644 |
| miRNA | PCA-29 | 2 | 83 | 62.88% | 0.3653 | 0.2178 |
| miRNA | DAE-MSE → PCA-29 | 2 | 36 | 27.27% | 0.6346 | 0.2838 |

---

## 3. HDBSCAN Grid Optimization

| Modality | Samples | Features | PCA Components | min_cluster_size | min_samples | Clusters | Noise | Noise % | Silhouette | DB | CH | Q |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| Methylation | 875 | 1000 | 229 | 10 | 10 | 2 | 469 | 53.60% | 0.2971 | 0.8735 | 32.8441 | 0.7825 |
| mRNA | 875 | 1000 | 239 | 5 | 1 | 3 | 160 | 18.29% | 0.3935 | 1.6744 | 17.0638 | 0.5070 |
| miRNA | 875 | 503 | 222 | 5 | 5 | 2 | 629 | 71.89% | 0.2250 | 1.3466 | 32.4012 | 0.9185 |

---

## 4. Discovery–Inference Density Characterization

| Modality | Discovery Samples | Discovery Clusters | Discovery Noise | Inference Samples | Inference Noise | Inference Clustered | Inference Noise % |
|---|---:|---:|---:|---:|---:|---:|---:|
| Methylation | 612 | 2 | 505 | 263 | 230 | 33 | 87.45% |
| mRNA | 612 | 8 | 117 | 263 | 54 | 209 | 20.53% |
| miRNA | 612 | 3 | 316 | 263 | 114 | 149 | 43.35% |

---

## 5. Differential Molecular Analysis

| Modality | Total Features | Significant | Upregulated | Downregulated | Non-significant |
|---|---:|---:|---:|---:|---:|
| Methylation | 1000 | 4 | 4 | 0 | 996 |
| mRNA | 1000 | 24 | 20 | 4 | 976 |
| miRNA | 503 | 40 | 40 | 0 | 463 |

---

## 6. Top Differential Features

### DNA Methylation

| Gene | Direction |
|---|---|
| MIR124-2 | Up |
| PSAT1 | Up |
| POU4F1 | Up |
| DOK5 | Up |

### mRNA

| Gene | Direction | Effect | P-value | FDR |
|---|---|---:|---:|---:|
| SCN7A | Down | -1.349707 | 1.68e-15 | 1.68e-12 |
| SCN2B | Down | — | — | 1.05e-10 |
| CLDN19 | — | — | — | 6.35e-10 |
| ANKRD30A | — | — | — | 5.90e-08 |
| TLX3 | Up | 2.3065 | 8.96e-08 | 4.57e-07 |
| DMRT1 | Up | 1.6953 | 4.19e-07 | 1.65e-06 |
| PRDM13 | Up | 2.1871 | 1.05e-05 | 2.97e-05 |

### miRNA

| miRNA | Effect | P-value | FDR |
|---|---:|---:|---:|
| hsa-mir-877 | 1.193182 | 3.77e-13 | 3.16e-11 |
| hsa-mir-3940 | 1.115360 | — | 1.75e-09 |
| hsa-mir-1229 | 1.293703 | — | 1.34e-08 |
| hsa-mir-301b | 1.239940 | — | 5.77e-08 |
| hsa-mir-520b | 2.374791 | — | 5.31e-06 |

---

## 7. Cancer-Associated Annotation

| Modality | Annotation Records | Unique Features | Categories |
|---|---:|---:|---:|
| Methylation | 4 | 2 | — |
| mRNA | 70 | 23 | 19 |
| miRNA | 88 | 40 | 20 |

---

## 8. Functional Enrichment

### DNA Methylation — GO

| Term | Adjusted P-value |
|---|---:|
| Significant GO terms | 0.013771–0.019795 |

### DNA Methylation — KEGG

| Pathway | P-value | Adjusted P-value |
|---|---:|---:|
| Vitamin B6 metabolism | 0.001200 | 0.004798 |
| Glycine, serine and threonine metabolism | 0.007977 | 0.013284 |
| Cysteine and methionine metabolism | 0.009963 | 0.013284 |

### mRNA

| Analysis | Result |
|---|---|
| GO | No term with FDR < 0.05 |
| KEGG | No pathway with FDR < 0.05 |

### miRNA

| Analysis | Result |
|---|---|
| GO | No enriched terms |
| KEGG | No enriched pathways |

---

## 9. Controlled Technical-Noise Experiment

### Mean Reconstruction MAE

| Noise Level | Methylation | mRNA | miRNA |
|---:|---:|---:|---:|
| 0.10 | 0.421909 | 0.461856 | 0.562563 |
| 0.25 | 0.469215 | 0.502348 | 0.594864 |
| 0.50 | 0.595384 | 0.619095 | 0.695288 |
| 1.00 | 0.923746 | 0.936190 | 0.989116 |

### Median Reconstruction MAE

| Noise Level | Methylation | mRNA | miRNA |
|---:|---:|---:|---:|
| 0.10 | 0.413542 | 0.476109 | 0.533822 |
| 0.25 | 0.457157 | 0.516643 | 0.563012 |
| 0.50 | 0.581756 | 0.622135 | 0.665127 |
| 1.00 | 0.908509 | 0.928163 | 0.965263 |

---

## 10. Residual Statistics

| Modality | Mean MAE | Median MAE | SD | Min | Max |
|---|---:|---:|---:|---:|---:|
| Methylation | 0.213937 | 0.209784 | 0.070156 | 0.079866 | 0.403155 |
| mRNA | 0.230708 | 0.220124 | 0.067634 | 0.104455 | 0.454712 |
| miRNA | 0.330487 | 0.315969 | 0.096035 | 0.184467 | 0.677967 |

### RMSE

| Modality | Mean RMSE | Median RMSE | SD | Min | Max |
|---|---:|---:|---:|---:|---:|
| Methylation | 0.269079 | 0.264103 | 0.088269 | 0.101919 | 0.509607 |
| mRNA | 0.289209 | 0.276246 | 0.084216 | 0.129965 | 0.569005 |
| miRNA | 0.419625 | 0.400220 | 0.121568 | 0.230163 | 0.840455 |

---

## 11. Residual–GLOSH Correlation

| Modality | Spearman ρ (MAE) | P-value | Spearman ρ (RMSE) | P-value |
|---|---:|---:|---:|---:|
| Methylation | 0.586728 | 1.44e-13 | 0.598974 | 3.29e-14 |
| mRNA | 0.297814 | 5.24e-04 | 0.293503 | 6.37e-04 |
| miRNA | 0.600381 | 2.76e-14 | 0.609015 | 9.35e-15 |

---

## 12. HDBSCAN vs LOF vs Isolation Forest

| Modality | HDBSCAN Noise | HDBSCAN Noise % | LOF Outliers | LOF % | Isolation Forest | IF % |
|---|---:|---:|---:|---:|---:|---:|
| Methylation | 74 | 56.06% | 3 | 2.27% | 14 | 10.61% |
| mRNA | 4 | 3.03% | 1 | 0.76% | 14 | 10.61% |
| miRNA | 83 | 62.88% | 5 | 3.79% | 11 | 8.33% |
```
```markdown
## 10. Ablation Study

### 10.1 Methylation Ablation Results

| Method | Noise Samples | Noise % | LOF Outliers | LOF % | Isolation Forest | IF % |
|---|---:|---:|---:|---:|---:|---:|
| HDBSCAN | 74 | 56.06% | — | — | — | — |
| LOF | — | — | 3 | 2.27% | — | — |
| Isolation Forest | — | — | — | — | 14 | 10.61% |

### 10.2 mRNA Ablation Results

| Method | Noise Samples | Noise % | LOF Outliers | LOF % | Isolation Forest | IF % |
|---|---:|---:|---:|---:|---:|---:|
| HDBSCAN | 4 | 3.03% | — | — | — | — |
| LOF | — | — | 1 | 0.76% | — | — |
| Isolation Forest | — | — | — | — | 14 | 10.61% |

### 10.3 miRNA Ablation Results

| Method | Noise Samples | Noise % | LOF Outliers | LOF % | Isolation Forest | IF % |
|---|---:|---:|---:|---:|---:|---:|
| HDBSCAN | 83 | 62.88% | — | — | — | — |
| LOF | — | — | 5 | 3.79% | — | — |
| Isolation Forest | — | — | — | — | 11 | 8.33% |

---

### 10.4 Controlled Technical-Noise Ablation

#### Methylation

| Gaussian Noise (σ) | Mean MAE | Median MAE | Max MAE |
|---:|---:|---:|---:|
| 0.10 | 0.421909 | 0.413542 | 0.986248 |
| 0.25 | 0.469215 | 0.457157 | 1.008841 |
| 0.50 | 0.595384 | 0.581756 | 1.078694 |
| 1.00 | 0.923746 | 0.908509 | 1.316851 |

#### mRNA

| Gaussian Noise (σ) | Mean MAE | Median MAE | Max MAE |
|---:|---:|---:|---:|
| 0.10 | 0.461856 | 0.476109 | 0.862151 |
| 0.25 | 0.502348 | 0.516643 | 0.881504 |
| 0.50 | 0.619095 | 0.622135 | 0.943461 |
| 1.00 | 0.936190 | 0.928163 | 1.174191 |

#### miRNA

| Gaussian Noise (σ) | Mean MAE | Median MAE | Max MAE |
|---:|---:|---:|---:|
| 0.10 | 0.562563 | 0.533822 | 1.091633 |
| 0.25 | 0.594864 | 0.563012 | 1.114717 |
| 0.50 | 0.695288 | 0.665127 | 1.179524 |
| 1.00 | 0.989116 | 0.965263 | 1.389649 |

---

### 10.5 Noise-Level Ablation: RMSE

| Gaussian Noise (σ) | Methylation | mRNA | miRNA |
|---:|---:|---:|---:|
| 0.10 | 0.269079 | 0.289209 | 0.419625 |
| 0.25 | — | — | — |
| 0.50 | — | — | — |
| 1.00 | — | — | — |

---

### 10.6 HDBSCAN Noise-Label Association

| Modality | Noise Samples | Clustered Samples | χ² | df | P-value | Cramér's V |
|---|---:|---:|---:|---:|---:|---:|
| Methylation | 45 | 87 | 56.0828 | 4 | 1.93e-11 | 0.6304 |
| mRNA | 2 | 130 | 18.4462 | 4 | 1.01e-03 | 0.3317 |
| miRNA | 29 | 103 | 12.4102 | 4 | 1.45e-02 | 0.2529 |

---

### 10.7 Fisher's Exact Test for Label-Specific Associations

| Modality | Label | Adjusted P-value |
|---|---|---:|
| Methylation | Label 1 | 0 |
| Methylation | Label 3 | 0 |
| mRNA | None | > 0.05 |
| miRNA | Label 1 | 0.022327 |
| miRNA | Label 3 | 0.048850 |

---

### 10.8 Residual–GLOSH Ablation

| Modality | MAE–GLOSH ρ | P-value | RMSE–GLOSH ρ | P-value |
|---|---:|---:|---:|---:|
| Methylation | 0.586728 | 1.44e-13 | 0.598974 | 3.29e-14 |
| mRNA | 0.297814 | 5.24e-04 | 0.293503 | 6.37e-04 |
| miRNA | 0.600381 | 2.76e-14 | 0.609015 | 9.35e-15 |

---

### 10.9 Ablation Residual Statistics

| Modality | Mean MAE | Median MAE | SD | Min | Max |
|---|---:|---:|---:|---:|---:|
| Methylation | 0.213937 | 0.209784 | 0.070156 | 0.079866 | 0.403155 |
| mRNA | 0.230708 | 0.220124 | 0.067634 | 0.104455 | 0.454712 |
| miRNA | 0.330487 | 0.315969 | 0.096035 | 0.184467 | 0.677967 |

### 10.10 Ablation RMSE Statistics

| Modality | Mean RMSE | Median RMSE | SD | Min | Max |
|---|---:|---:|---:|---:|---:|
| Methylation | 0.269079 | 0.264103 | 0.088269 | 0.101919 | 0.509607 |
| mRNA | 0.289209 | 0.276246 | 0.084216 | 0.129965 | 0.569005 |
| miRNA | 0.419625 | 0.400220 | 0.121568 | 0.230163 | 0.840455 |

---

### 10.11 Maximum Feature Error

| Modality | Mean Max Feature Error | Maximum |
|---|---:|---:|
| Methylation | 0.930586 | 1.817833 |
| mRNA | 0.986129 | 2.083496 |
| miRNA | 1.447269 | 3.110385 |
```

