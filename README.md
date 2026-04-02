# Autonomous PV String Health Monitoring via Unsupervised Learning

This project develops a robust, unsupervised diagnostic framework for solar string inverter systems. By leveraging a 1.3-million-row dataset (Lazzaretti et al., 2020), the study compares the efficiency of **K-Means** (Partition-based) and **Gaussian Mixture Models** (Distribution-based) in identifying module-level faults without prior labeling.

Data Citation & Acknowledgments**
The raw data utilized in this study is sourced from the public repository:
* **Dataset:** *Dataset for Photovoltaic Systems Monitoring and Fault Detection*
* **Authors:** Lazzaretti, R. L., Costa, C. H., Rodrigues, M. P., et al.
* **Source:** [Mendeley Data, V1 (2020)](https://data.mendeley.com/datasets/p9nt9635jm/1)
* **License:** CC BY 4.0

This research acknowledges the contribution of the original authors in providing a high-frequency, multi-scenario dataset that enables the benchmarking of advanced clustering algorithms.

## **1. Research Objectives**
* **Feature Engineering:** Transform raw electrical and environmental sensors into physically meaningful ratios (Power Difference, Relative Current Mismatch).
* **Dimensionality Reduction:** Utilize **PCA** to compress 20+ variables into 8 Principal Components maintaining 95% variance.
* **Competitive Benchmarking:** Evaluate K-Means vs. GMM using internal (Silhouette/DBI) and external (Adjusted Rand Index) metrics.
* **Operational Insight:** Identify the most accurate model for detecting "Subtle" faults like Degradation and Short-Circuits.

## **2. Technical Pipeline**

### **A. Preprocessing & Feature Engineering**
Raw data from DC current ($I_{dc}$), Voltage ($V_{dc}$), Irradiance ($Irr$), and Temperature ($T$) are processed into:
* **$p\_diff$**: Absolute power imbalance between strings.
* **$i\_rel\_diff$**: Current mismatch relative to total generation.
* **$v\_rel\_diff$**: Voltage deviation indicative of bypassed modules.

### **B. Dimensionality Reduction (PCA)**
Standardized features are projected into an 8-dimensional space. This step de-correlates the features and isolates "Fault Signatures" from environmental "Noise."

### **C. Clustering Models**
| Model | Strategy | Configuration |
| :--- | :--- | :--- |
| **K-Means** | Hard Clustering | $K=5$, K-Means++, $n\_init=20$ |
| **GMM** | Soft Clustering | $K=5$, Full Covariance, EM Algorithm |

## **3. Key Findings**

### **Model Performance Summary**
| Metric | K-Means | GMM | Result |
| :--- | :--- | :--- | :--- |
| **Silhouette Score** | 0.5144 | 0.3560 | K-Means is more cohesive. |
| **Davies-Bouldin Index** | 0.8482 | 1.3053 | K-Means is more dense. |
| **Adjusted Rand Index (ARI)** | 0.1498 | **0.2478** | **GMM is 65% more accurate.** |

### **The "Diagnostic Paradox"**
While K-Means produced better clusters as evidence by higher Silhouette, **GMM was the superior diagnostic tool.** GMM’s ability to model elliptical shapes allowed it to identify **Short-Circuits** and **Degradation** that K-Means ignored. Both models achieved **100% accuracy** in detecting **Open-Circuit** faults.

## **4. File Structure**
* `raw_data/`: Contains the original `.mat` source files from the Lazzaretti study.
* `data/`: Processed `.csv` files used during PCA and clustering phases.
* `models/`: Serialized `.pkl` files for the PCA scaler and trained clustering models.
* `images/`: Generated plots, confusion matrices, SHAP summaries, and PCA loadings heatmaps.
* `data_preparation.py`: Cleaning, normalization, and feature engineering.
* `pca_analysis.ipynb`: Variance retention and dimensionality reduction logic.
* `KMeans_implementation.ipynb`: Grid search, fitting, and confusion matrix.
* `GMM_implementation.ipynb`: BIC/AIC optimization and probabilistic clustering.
* `bias_analysis.ipynb`: Understand bias of GMM implementation.
* * `requirements.txt`: List of Python library dependencies.

## **5. How to Run**
1.  **Install Dependencies:**
    ```bash
    pip install -r requirements.txt
    ```
2.  **Environment Setup:** Ensure the `raw_data/` folder is populated. The scripts are configured to output processed files to `data/` and saved models to `models/`.
3.  **Execution Order:**
    - Run `data_preparation.py` to process raw `.mat` files.
    - Execute `pca_analysis.ipynb` to generate the 8-component feature set.
    - Run the `KMeans_implementation.ipynb` and `GMM_implementation.ipynb` notebooks for model training and evaluation.
    - Run the `bias_analysis.ipynb` for analysing the bias of the GMM.


---

**Author** King Joshua D. Arzadon  
Capstone Project - PGD for AI/ML - AIM 
March 2026
