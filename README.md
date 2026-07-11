# Multimodal Non-Invasive Diabetes Dataset — EDA & Data in Brief

[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)
[![Data in Brief](https://img.shields.io/badge/Prepared%20for-Data%20in%20Brief-blue)]()
[![Python](https://img.shields.io/badge/Python-3.x-blue)]()

Exploratory Data Analysis (EDA) and a companion feature-analysis report for a **multimodal, non-invasive diabetes screening dataset**, combining near-infrared (NIR) optical sensor signals, wearable physiological signals, and demographic data.

This repository contains the reproducible analysis code, the full report, and dataset documentation supporting a **Data in Brief** submission and the corresponding dataset deposit on **Mendeley Data**.

---

## 📌 Overview

Diabetes is conventionally diagnosed via invasive blood tests. This dataset was built to support research into **non-invasive alternatives** — estimating diabetes status from signals that can be captured externally, such as light absorption through the skin (NIR optics) or wearable-sensor vitals (heart rate, blood volume pulse, skin conductance).

The dataset aggregates **15,720 measurement sessions** across **57 columns**, combining real-world sensor recordings with statistically modeled synthetic data, from **five underlying sources**.

| Metric | Value |
|---|---|
| Total sessions (rows) | 15,720 |
| Total columns | 57 |
| Diabetic sessions | 10,480 (66.7%) |
| Non-diabetic sessions | 5,240 (33.3%) |
| Real-world data | 8,826 rows (56.1%) |
| Synthetic data | 6,894 rows (43.9%) |
| Train / Validation / Test split | 70% / 15% / 15% (stratified) |
| Strongest non-invasive feature | NIR @ 940nm (Pearson r = −0.72) |

---

## 🗂️ Dataset Composition

| Source Dataset | Rows | Type |
|---|---|---|
| PhysioCGM | 6,993 | Real |
| Nature Scientific Reports – NIR Glucose | 5,920 | Synthetic |
| Kaggle Raman Diabetes | 974 | Synthetic |
| Raman Sugars | 933 | Real |
| NTNU NIR Glucose | 900 | Real |

**Ground-truth label:** `final_label__is_diabetic` — derived from blood glucose measurement (1 = glucose > 126 mg/dL / diabetic, 0 = normal). Diabetic group mean glucose ≈ 186.4 mg/dL vs. 108.6 mg/dL for non-diabetic.

---

## 🔬 Feature Groups

Features are organized into three tiers based on correlation strength with the diabetic label:

### Tier 1 — Primary Optical Signal (`|r| = 0.49–0.72`)
Dual-wavelength NIR sensor (660nm red light + 940nm infrared):
- `feature__nir_signal_intensity_at_660nm...` (r = −0.67)
- `feature__nir_signal_intensity_at_940nm...` (r = −0.72) — strongest single predictor
- `feature__optical_ratio_660nm_divided_by_940nm...` (r = +0.49) — cancels skin tone/thickness effects

### Tier 2 — Supporting Wearable Signals (`0.05 < |r| < 0.4`)
Photoplethysmography (PPG) and electrodermal activity (EDA) from consumer wearables:
- `heart_rate_avg_bpm`, `heart_rate_max_bpm`, `heart_rate_min_bpm`
- `bvp_variability`, `bvp_max`, `bvp_min`
- `movement_intensity_peak`
- `skin_conductance_avg_uS`
- `heart_rate_variability_bpm`

### Tier 3 — Negligible Signal (in this dataset)
Demographic and environmental features showing near-zero correlation with the label here (despite being clinically relevant in the broader literature):
- `demographic__body_mass_index...`
- `demographic__volunteer_age_years...`
- `physiological...SpO2...`
- `physiological...skin_surface_temperature...`
- `environmental...ambient_room_temperature...`
- `wrist_temp_avg_C`, `movement_intensity_avg`

> ⚠️ See [Limitations](#-limitations--caveats) — Tier 3's negligible correlation is likely an artifact of the synthetic data generation, not a real-world finding.

---

## 📁 Repository Contents

```
├── Multimodal_Diabetes_Dataset_EDA_DataInBrief.ipynb   # Full reproducible EDA notebook
├── Diabetes_NonInvasive_Prediction_Report.pdf           # Companion feature-analysis report
├── About_the_Dataset.png                                # Visual dataset overview / dashboard
├── data/                                                # Dataset CSV (see Mendeley Data link below)
└── README.md
```

---

## 🚀 Getting Started

### Requirements
```bash
pandas
numpy
matplotlib
seaborn
scipy
```
All dependencies are standard and pre-installed in Google Colab — no additional installation required. For a local environment:
```bash
pip install pandas numpy matplotlib seaborn scipy
```

### Steps to Reproduce

1. Download the dataset CSV file(s) (see [Dataset Access](#-dataset-access) below).
2. Open `Multimodal_Diabetes_Dataset_EDA_DataInBrief.ipynb` in Jupyter Notebook or Google Colab.
3. Run **Step 0** to import required libraries.
4. Run **Step 1** to load the dataset CSV (or a ZIP file containing it).
5. Run the **Column Auto-Detection** step — this automatically identifies relevant column groups (target label, source dataset, glucose value, Tier 1/2/3 features) by keyword matching, and prints matched columns for verification.
6. Run **Steps 2–17** sequentially to reproduce the full analysis:
   - Data preview, shape, and column category breakdown
   - Data type summary
   - Missing value and duplicate checks
   - Auto-generated data dictionary
   - Descriptive statistics (overall and grouped by class)
   - Composition dashboard (class balance, real vs. synthetic split, source breakdown, train/val/test split)
   - Pearson correlation analysis and feature tiering
   - Univariate distribution plots (histograms + boxplots by class)
   - Bivariate scatter plots
   - Group mean comparison with t-tests and Cohen's d effect size
   - IQR-based outlier detection
   - Cardinality checks
   - Internal consistency checks (e.g., label vs. 126 mg/dL glucose threshold)
7. Run **Step 18** to export all generated figures (PNG) and tables (CSV) to the working directory.

All numerical results reported in the companion report are computed directly within this notebook and are traceable to their corresponding code cell.

---

## 📊 Key Findings

- **NIR optical signals** (660nm, 940nm, and their ratio) show the strongest relationship with diabetes status in this dataset, making them the most promising candidates for a non-invasive screening device.
- **Wearable heart-rate features** provide a moderate, complementary signal — diabetic sessions show ~6 bpm higher average heart rate and reduced blood volume pulse variability.
- **Demographic features** (age, BMI) — well-established risk factors in the clinical literature — show negligible correlation *within this specific dataset*, most likely due to how the synthetic portion was generated.
- A **Logistic Regression** and **Random Forest / Gradient Boosted Trees** baseline are recommended using the Tier 1 + Tier 2 feature set, with class weighting to address the 67/33 class imbalance, evaluated using sensitivity, specificity, ROC-AUC, and PR-AUC.

See [`Diabetes_NonInvasive_Prediction_Report.pdf`](./Diabetes_NonInvasive_Prediction_Report.pdf) for the full analysis, methodology, and proposed modeling plan.

---

## ⚠️ Limitations & Caveats

- ~44% of the dataset is **synthetic**, generated to be statistically consistent with published correlations. Findings should be validated on real-world data before drawing clinical conclusions.
- The label is derived from a **single blood glucose threshold** (126 mg/dL) and does not capture other diagnostic criteria (e.g., HbA1c, oral glucose tolerance test).
- **Correlation does not imply causation** — relationships described here are statistical associations within this dataset, not validated physiological mechanisms.
- The negligible age/BMI correlation contradicts general clinical literature and likely reflects synthetic-data generation limitations rather than a true absence of relationship in real populations.
- This analysis is exploratory and intended to guide feature selection for a modeling phase — it is **not a validated diagnostic tool**.

---

## 📥 Dataset Access

The full dataset (CSV) is hosted on **Mendeley Data**:
🔗 *[Add your Mendeley Data DOI/link here once published]*

---

## 👥 Authors

**Md Irfanul Kabir Hira**
Department of Computer Science and Engineering, National Institute of Textile Engineering and Research (NITER), Constituent Institute of the University of Dhaka, Savar, Dhaka-1350, Bangladesh
ORCID: [0009-0004-6718-1632](https://orcid.org/0009-0004-6718-1632)

**Anichur Rahman**
School of Computing, Georgia Southern University, Statesboro, GA 30458, USA
Email: ar36248@georgiasouthern.edu
ORCID: [0000-0002-2691-1748](https://orcid.org/0000-0002-2691-1748)

### Author Contributions (CRediT)
- **Md Irfanul Kabir Hira:** Conceptualization, Data curation, Formal analysis, Investigation, Methodology, Software, Visualization, Writing – original draft.
- **Anichur Rahman:** Conceptualization, Supervision, Validation, Writing – review & editing.

---

## 📄 Citation

If you use this dataset or code in your research, please cite:

```bibtex
@dataset{hira_rahman_diabetes_multimodal,
  author    = {Hira, Md Irfanul Kabir and Rahman, Anichur},
  title     = {Multimodal Non-Invasive Diabetes Dataset: NIR Optical, Wearable Physiological, and Demographic Signals},
  year      = {2026},
  publisher = {Mendeley Data},
  doi       = {ADD_DOI_HERE}
}
```

---

## 📜 License

This project is licensed under the **Creative Commons Attribution 4.0 International (CC BY 4.0)** license.
You are free to share and adapt this material for any purpose, including commercially, as long as appropriate credit is given.

See [LICENSE](./LICENSE) or [creativecommons.org/licenses/by/4.0](https://creativecommons.org/licenses/by/4.0/) for full terms.

---

## 🏷️ Categories / Keywords

`Diabetes` · `Health Informatics` · `Biomedical Engineering` · `Machine Learning` · `Wearable Sensors` · `Signal Processing` · `Non-Invasive Glucose Monitoring` · `Near-Infrared Spectroscopy`
