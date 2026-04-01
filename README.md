# Autonomous PV String Health Monitoring via Unsupervised Learning

This project develops a robust, unsupervised diagnostic framework for solar string inverter systems. By leveraging a 1.3-million-row dataset (Lazzaretti et al., 2020), the study compares the efficiency of **K-Means** (Partition-based) and **Gaussian Mixture Models** (Distribution-based) in identifying module-level faults without prior labeling.

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

## **6. Ethical AI & Interpretability**

### **A. Explainability & Traceback Logic**
To move beyond the "Black Box" nature of unsupervised learning, this project utilizes **SHAP (SHapley Additive exPlanations)**. This allows for a direct "Traceback" from abstract Principal Components to physical sensor data. For example, the model's reliance on **PC4** was traced back to **Voltage-Temperature Ratios**, providing a physically-grounded justification for maintenance alerts.

### **B. Human-in-the-Loop (HITL) Philosophy**
This framework is designed as a **Decision-Support Tool**, not a replacement for human expertise. 
* **Triage vs. Action:** The GMM identifies anomalies and provides "Confidence Scores" via probabilistic soft-clustering, filtering rows of noise into actionable insights.
* **Expert Validation:** The ethical responsibility for final maintenance actions—such as module replacement or string isolation—remains with the human operator, who uses the model's transparency (PCA Loadings) to verify the diagnosis before dispatching crews.

### **C. Bias Mitigation & Fairness**
* **Environmental Bias:** Implemented nocturnal filtering (clipping irradiance < 10 W/m²) to prevent the model from learning physically impossible "Nighttime Faults."
* **Measurement Bias:** Utilized Z-score scaling and relative feature ratios ($p\_diff$, $i\_rel\_diff$) to ensure the AI remains sensitive to string asymmetry rather than fluctuating weather patterns.
* **Waste Reduction:** By accurately distinguishing between **Partial Shading** and **Permanent Degradation**, the model prevents the premature disposal of healthy PV modules, supporting the circular economy of the green energy sector.

---

**Author** King Joshua D. Arzadon  
Capstone Project - PGD for AI/ML - AIM 
March 2026
