Autonomous PV String Health Monitoring via Unsupervised Learning
Capstone Project Overview
This project develops a robust, unsupervised diagnostic framework for 5-kW solar string inverter systems. By leveraging a 1.3-million-row dataset (Lazzaretti et al., 2020), the study compares the efficacy of K-Means (Partition-based) and Gaussian Mixture Models (Distribution-based) in identifying module-level faults without prior labeling.

1. Research Objectives
Feature Engineering: Transform raw electrical and environmental sensors into physically meaningful ratios (Power Difference, Relative Current Mismatch).
Dimensionality Reduction: Utilize PCA to compress 20+ variables into 8 Principal Components (95% variance).
Competitive Benchmarking: Evaluate K-Means vs. GMM using internal (Silhouette/DBI) and external (Adjusted Rand Index) metrics.
Operational Insight: Identify the most accurate model for detecting "Subtle" faults like Degradation and Short-Circuits.

2. Technical Pipeline
A. Preprocessing & Feature Engineering
Raw data from DC current ($I_{dc}$), Voltage ($V_{dc}$), Irradiance ($Irr$), and Temperature ($T$) are processed into:
$p\_diff$: Absolute power imbalance between strings.
$i\_rel\_diff$: Current mismatch relative to total generation.
$v\_rel\_diff$: Voltage deviation indicative of bypassed modules.
B. Dimensionality Reduction (PCA)
Standardized features are projected into an 8-dimensional space. This step de-correlates the features and isolates "Fault Signatures" from environmental "Noise."
C. Clustering Models
Model
Strategy
Configuration
K-Means
Hard Clustering
$K=5$, K-Means++, $n\_init=20$
GMM
Soft Clustering
$K=5$, Full Covariance, EM Algorithm


3. Key Findings
Model Performance Summary
Metric
K-Means
GMM
Result
Silhouette Score
0.5144
0.3560
K-Means is more cohesive.
Davies-Bouldin Index
0.8482
1.3053
K-Means is more dense.
Adjusted Rand Index (ARI)
0.1498
0.2478
GMM is 65% more accurate.

The "Diagnostic Paradox"
While K-Means produced "prettier" clusters (higher Silhouette), GMM was the superior diagnostic tool. GMM’s ability to model elliptical shapes allowed it to identify Short-Circuits and Degradation that K-Means ignored. Both models achieved 100% accuracy in detecting Open-Circuit faults.

4. File Structure
data_preparation.py: Cleaning, normalization, and feature engineering.
pca_analysis.ipynb: Variance retention and dimensionality reduction logic.
kmeans_implementation.ipynb: Grid search, fitting, and confusion matrix.
gmm_implementation.ipynb: BIC/AIC optimization and probabilistic clustering.
results_visualization.py: Scripts for plotting scatter maps and evaluation metrics.

5. How to Run
Dependencies: Install via pip install pandas scikit-learn matplotlib seaborn.
Data: Ensure pca_8_components.csv and labels.csv are in the root directory.
Execution: Run the Jupyter notebooks in sequence: Preparation $\rightarrow$ PCA $\rightarrow$ Clustering.

6. Ethical AI & Limitations
Interpretability: Uses SHAP/LIME logic to map abstract clusters back to physical units.
Bias Mitigation: Implemented nocturnal filtering and Z-score scaling to prevent sensor-scale bias.
Scope: Optimized for 5-kW string configurations; performance may vary on central utility-scale inverters.

Author
King Joshua D. Arzadon
Capstone Project - PGD for AI/ML - AIM
March 2026
