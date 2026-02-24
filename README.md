# California Wildfire Containment Time + Property Value Exposure (CA)

## Why this project
California wildfires create urgent operational and economic decisions. In this project, I built two practical models:

1) **Containment time prediction** — estimate how long a wildfire may take to contain.  
2) **Property value exposure estimation** — quantify baseline housing value at risk using historical home values by region.

The goal is a decision-support workflow that could help **homeowners**, **insurers**, **real estate investors**, and **public-sector planners** evaluate risk and respond faster.

---

## What I built

### 1) Wildfire containment time model (regression)
- **Target:** containment duration (days) = `CONT_DATE - ALARM_DATE`
- **Data:** CAL FIRE historical incident records (2015–2017 used for consistent date fields)
- **Preprocessing:** deduplication, outlier removal, rare-category grouping, one-hot encoding, time features
- **Modeling:** baseline Linear Regression vs. Random Forest; feature selection with mutual information (SelectKBest)
- **Primary model:** Random Forest Regressor

### 2) Housing value exposure model (regression)
- **Target:** Zillow home value (baseline value proxy)
- **Data:** Zillow housing values by region and time (large-scale dataset)
- **Preprocessing:** pivot/reshape time series into row-based format; handle missingness; time features; encoding strategy for high-cardinality fields
- **Modeling:** Random Forest vs. PCA variants; feature selection (f_regression)
- **Primary model:** Random Forest Regressor

---

## Key results (job-interview friendly)

### Wildfire containment time (historical test set)
- **Best model:** Random Forest using 10 selected features
- **Performance:** **R² = 0.5989**, **RMSE ≈ 18.28 days**  
- **Top driver:** **GIS_ACRES (fire size)**; other signal from agency/unit/cause + seasonality features

### Wildfire containment time (2025 sanity check)
Applied the trained model to three 2025 fires:
- **23,707 acres:** Actual 44 days vs. Predicted 86.41 (error +42.41)  
- **1,052 acres:** Actual 3 days vs. Predicted 3.52 (error +0.52)  
- **799 acres:** Actual 8 days vs. Predicted 3.28 (error −4.72)  

Overall for this small 2025 sample: **R² = −0.8199**, **RMSE ≈ 24.64 days** — strong on small fires, weak on large fires (drift / missing predictors).

### Housing value modeling (baseline exposure proxy)
- **Best model:** Random Forest
- **Performance:** **R² = 0.9977**, **RMSE ≈ $10,984.60**
- Strong predictors included **RegionName / Metro** and **Year** (location + time)

> Note: This housing model estimates **baseline property value exposure**, not post-fire loss. Estimating realized loss would require claims, FEMA assistance, or post-event sales/assessment data.

---

## Data & preprocessing (what mattered in practice)

### Wildfire dataset
- Merged multiple Excel tabs and standardized column names
- Focused on **STATE = California**
- Removed duplicates and outliers (negative or extreme durations)
- Dropped fields that were not useful or could leak information (e.g., containment date)
- Grouped rare categories into **“Other”**
- Encoded categoricals with one-hot encoding (`handle_unknown='ignore'`)
- Engineered time features: **ALARM_MONTH**, **ALARM_DAY_OF_YEAR**

### Zillow dataset
- Reshaped the time series dataset into a row-based modeling table (millions of rows)
- Dropped non-informative IDs and heavily missing records (instead of noisy imputation)
- Built time features: **Year / Month / Day**
- Encoding strategy:
  - **Label encoding** for high-cardinality fields (City/Metro/County)
  - **One-hot encoding** for State

---

## Repository contents
- `Project Python Notebook.ipynb` — end-to-end analysis (EDA → preprocessing → modeling → evaluation)
- `Fires_100.xlsx` — wildfire dataset (multi-tab)
- `Project Summary Paper.docx` — project narrative + results

---

## How to run locally

### 1) Clone the repo
```bash
git clone https://github.com/chance-analytics/california-wildfire-containment-housing-exposure-modeling.git
cd california-wildfire-containment-housing-exposure-modeling
```

### 2) Create an environment (example)
```bash
conda create -n wildfire python=3.11 -y
conda activate wildfire
```

### 3) Install dependencies
```bash
pip install pandas numpy matplotlib seaborn scikit-learn openpyxl jupyter
```

### 4) Run the notebook
```bash
jupyter notebook
```
Open `Project Python Notebook.ipynb` and run top-to-bottom.

---

## What I would improve next
- Add missing predictors for containment time: **weather (wind/humidity)**, **terrain**, **fuel**, and **resource deployment**
- Expand training data beyond 2017 and validate across newer regimes (drift-aware evaluation)
- Use **time-based cross-validation** and compare against gradient boosting (XGBoost/LightGBM)
- Connect fires to dollars more directly using **claims / FEMA assistance / post-event transactions**

---

## Skills demonstrated
- End-to-end ML workflow: EDA → cleaning → feature engineering → modeling → evaluation
- Regression modeling: Linear Regression, Random Forest, feature selection, PCA comparison
- High-cardinality categorical handling and encoding strategy
- Translating model results into business risk framing (operational + financial)

---

## Author
**Zicheng (Chance) Xu**  
GitHub: https://github.com/chance-analytics
