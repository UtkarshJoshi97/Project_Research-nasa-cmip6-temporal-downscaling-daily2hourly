# nasa-cmip6-temporal-downscaling-daily2hourly

### About This Repository

> **Note:** This repository documents and centralizes **my personal contributions** to a larger team-based project involving **6 collaborators**. The project explored various techniques for temporal downscaling of NASA CMIP6 climate simulations, with a particular focus on **statistical methods**, **machine learning (ML)**, and **hybrid approaches**.

While the full project scope extends beyond the contents of this repository—and includes components subject to a **corporate problem statement under a signed Non-Disclosure Agreement (NDA)**—everything presented here is:
-  **Open source**
-  **Rooted in academic research**
-  **Free from any commercial intent**
-  Intended solely for **educational and knowledge-sharing purposes**
The data, code, models, and research references in this repository are part of my **individual academic journey** and are shared to contribute to the broader scientific and open-source communities.
Feel free to explore, fork, or contribute — and please cite appropriately if you build on this work.
---

# Temporal Downscaling of CMIP6 Climate Data: A Methodological Analysis

---

## 1. Project Overview

This repository documents the process of developing, validating, and diagnosing a statistical model for **temporal downscaling** of daily **NASA NEX-GDDP-CMIP6 climate data** to an **hourly resolution**.

The project follows a full data science lifecycle — from data ingestion and baseline modeling to deep-dive diagnostics — and ultimately leads to a richer understanding of structural bias and model misalignment.

**Primary Objective:**  
To generate accurate, high-resolution **hourly climate data** suitable for **localized impact assessments**, using **ERA5 reanalysis data** as ground truth.

---

## Repository Structure
/
|-- data/
| |-- (Raw and processed ERA5 and NASA data files)

|-- notebooks/
| |-- 01_DataProcessing_NASA.ipynb
| |-- 02_DataProcessing_ERA5.ipynb
| |-- 03_DownScaling_Baseline.ipynb
| |-- 04_BiasCorrection_Delta.ipynb
| |-- 05_BiasCorrection_Quantile.ipynb
| |-- 06_Anomaly_Diagnosis_and_Validation.ipynb

|-- reports/ (may be not going the publish the saved plots on local)
| |-- (Generated plots and summary documents)

|-- README.md

---


## 3. The Analysis Workflow: A Narrative of Discovery

This project followed a **non-linear, iterative workflow**, where each step’s insights informed the next. It unfolded as a real-world investigation, not a pre-scripted experiment.

### Step 1: Data Processing

**Scripts:**  
- `01_DataProcessing_NASA.ipynb`  
- `02_DataProcessing_ERA5.ipynb`

**Action:**  
- NASA NEX-GDDP-CMIP6 daily data was fetched from AWS S3.  
- ERA5 hourly data was locally extracted from GRIB files.  
- Data was cleaned, standardized, and formatted for modeling.

**Notes:**  
NASA data = daily stats (`tasmin`, `tasmax`)  
ERA5 data = hourly "ground truth"


### Step 2: Initial Downscaling Model

**Script:**  
- `03_DownScaling_Baseline.ipynb`

**Action:**  
- Built a baseline model using **24 independent linear regressions** per variable (one per hour).  
- Goal: Predict a full 24-hour cycle from a single day's summary data.

**Assumption:**  
The model would follow the correct diurnal pattern, matching real-world temperature cycles.


### Step 3: Bias Correction Experiments

**Scripts:**  
- `04_BiasCorrection_Delta.ipynb`  
- `05_BiasCorrection_Quantile.ipynb`

**Action:**  
Implemented two well-known bias correction techniques _before_ feeding NASA data to the downscaling model:

- **Delta Change Method:** Adjusted future predictions based on average historical monthly bias.
- **Quantile Mapping:** Aligned the entire distribution of NASA data with that of ERA5 via non-linear mapping.

**Goal:**  
Lower final downscaled errors (RMSE, MAE) by correcting input bias before modeling.


### Step 4: Anomaly Discovery and Diagnosis (The Turning Point)

**Script:**  
- `06_Anomaly_Diagnosis_and_Validation.ipynb`

**Discovery:**  
Visual validation exposed a major anomaly — the **diurnal temperature cycle was inverted**:
- Warm at night  
- Cold during the day

**Diagnosis:**  
Not a bug, but a **12-hour functional offset**, caused by the model learning to align daily inputs to hourly outputs without knowing _where_ in the 24-hour cycle it belonged.

**Fix:**  
Programmatically shifting output **by -12 hours** corrected the issue entirely.


### Step 5: Deeper Analysis of Corrected Data

**Action:**  
Validated the corrected model across **four years (2021–2024)**.

**Findings:**

-  **Seasonal Bias:** The corrected model still showed **systematic warm bias in winter**.
-  **Inter-annual Variability:** Error patterns changed dramatically year-to-year.
  - Example: 2024 had an opposite pattern to 2021.

>  Conclusion: A simple correction like Delta or Quantile Mapping does not generalize across seasons or years.


## 4. Overall Conclusion

What began as a basic modeling task became a rigorous diagnostic deep-dive.

### Key Takeaways:

1. **Functional Error:**  
   The model had a **12-hour time offset** due to how inputs and outputs were paired — this was fixable with post-processing.

2. **Complex Seasonal Bias:**  
   The remaining error varied **seasonally** and **inter-annually**, exposing the **limitations of static bias correction**.

> **Implication:** The model is not fundamentally broken — but static corrections are insufficient for real-world deployment.

---

## 5. Next Steps

The project’s findings provide strong, data-driven motivation for moving to more sophisticated, adaptive models.

### Proposed Next Model: **SARIMax-LSTM**

- **SARIMax** will handle **seasonality and exogenous predictors**
- **LSTM** will capture **non-linear, temporal dynamics** (ML part is being done by other team members)

This hybrid model will be capable of learning the **complex, non-stationary patterns** found in this downscaling problem, including:
- Seasonal errors
- Temperature-dependent bias
- Year-specific anomalies

---

📁 _This work is part of the ongoing `nasa-cmip6-temporal-downscaling-daily2hourly` project. Contributions and collaborations are welcomed._
