# California Wildfire Containment Time + Property Value Exposure (CA)

## Why this project
California wildfires create urgent operational and economic decisions. In this project, I built two practical models:
1) **Containment time prediction** — estimate how long a wildfire may take to contain.
2) **Property value exposure estimation** — quantify baseline housing value at risk using historical home values by region.

The goal is a decision-support workflow that could help **homeowners**, **insurers**, **real estate investors**, and **public-sector planners** evaluate risk and respond faster.

---

## What I built

### 1) Wildfire containment time model (regression)
- **Target:** containment duration (days) = `containment_date - alarm_date`
- **Data:** CAL FIRE historical incident records (focused on years with consistent date fields)
- **Modeling:** feature engineering + categorical encoding + regression models  
- **Primary model:** Random Forest Regressor

### 2) Housing value exposure model (regression)
- **Target:** Zillow home value (baseline value proxy)
- **Data:** Zillow housing values by region and time (large-scale dataset)
- **Modeling:** heavy preprocessing + encoding + model selection  
- **Primary model:** Random Forest Regressor

---

## Key results (high-level)

### Wildfire containment time (historical test set)
- **Best model:** Random Forest with 10 selected features
- **Performance:** **R² ≈ 0.60**, **RMSE ≈ 18.3 days**
- Most important driver: **GIS acres (fire size)**, plus agency/unit/cause and seasonality features.

### Wildfire containment time (2025 sanity check)
When applying the model to a small 2025 sample, it performed well on smaller fires but struggled on a large fire case—suggesting **generalization / data-drift risk** and missing predictors (e.g., weather, resources).

### Housing value modeling
- **Best model:** Random Forest
- **Performance:** **R² ≈ 0.998**, **RMSE ≈ $11.0k**
- Strong predictors included **region/metro and time** (year), consistent with how real estate values behave.

> Notes: The housing model estimates **baseline property value exposure**, not the post-fire loss amount (post-event losses would require additional claims/sales/impact data).

---

## Data & preprocessing (what mattered in practice)

### Wildfire dataset
- Combined multiple year tabs, standardized column names, parsed dates
- Removed duplicates and obvious duration outliers (negative / extreme durations)
- Reduced high-cardinality categorical noise by grouping rare categories into **“Other”**
- Encoded categoricals using one-hot encoding with safe handling for new categories
- Added time features: **alarm month** and **day-of-year**

### Zillow dataset
- Converted time-series format into row-based format for modeling (large-scale)
- Removed non-informative IDs and high-missing rows
- Extracted **year/month/day** from record date
- Used a mix of **label encoding** (very high-cardinality fields like city/metro/county) and **one-hot encoding** (state)

---

## Repository contents
- `Project Python Notebook.ipynb` — end-to-end analysis (EDA → preprocessing → modeling → evaluation)
- `Fires_100.xlsx` — wildfire dataset (multi-tab)
- `Project Summary Paper.docx` — project write-up / narrative context

---

## How to run locally

### 1) Clone the repo
```bash
git clone https://github.com/chance-analytics/california-wildfire-containment-housing-exposure-modeling.git
cd california-wildfire-containment-housing-exposure-modeling