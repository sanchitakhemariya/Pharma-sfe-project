🏥 Pharma Sales Force Effectiveness (SFE) Analysis
Predicting Territory-Level Rx Volume from Rep Activity Features
![Python](https://img.shields.io/badge/Python-3.10-blue?logo=python)
![XGBoost](https://img.shields.io/badge/XGBoost-R²%200.955-brightgreen)
![Status](https://img.shields.io/badge/Status-Complete-success)
![Domain](https://img.shields.io/badge/Domain-Pharma%20Commercial%20Analytics-blueviolet)
---
📌 Business Problem
Pharmaceutical companies invest heavily in field sales representatives who visit
doctors (HCPs) to promote drug prescriptions. But not all rep activity drives
equal returns — some territories generate far more prescriptions per rep visit
than others.
Key business questions this project answers:
Which rep activity features most strongly drive Rx (prescription) volume?
Which territories are high-ROI vs low-ROI for sales investment?
Can we predict monthly Rx volume from rep activity data to support
resource allocation decisions?
This type of analysis — known as Sales Force Effectiveness (SFE) — is a
core commercial analytics workflow used by major pharma companies to optimize
their field force strategy.
---
📊 Dataset
Property	Detail
Source	Kaggle — Pharma Sales Data
Drug focus	N02BE (Paracetamol class)
Time period	70 months
Territories	200 (simulated on top of real sales data)
Total records	14,000 territory-month observations
Target variable	Monthly Rx volume per territory
Why simulated rep activity?
Real pharma field CRM data (call logs, sample drops, digital touches) is
proprietary and held under NDA. This is standard in the industry — even
internal pharma DS teams often work with simulated or anonymised field data.
Our approach mirrors real-world practice:
```
Real Rx volume    ←  Kaggle pharma sales dataset (N02BE drug class)
Territory IDs     ←  200 territories assigned with realistic traits
Rep activity      ←  Simulated using domain-calibrated distributions
                      (call frequency, sample drops, digital touches,
                       competitor share, market access score)
```
This hybrid approach — real outcomes, realistic synthetic inputs — produces
a commercially meaningful model while respecting data privacy constraints.
---
🔧 Features Engineered
Feature	Description	Type
`calls`	Monthly rep visits to HCPs in territory	Rep activity
`samples_dropped`	Drug samples left with doctors	Rep activity
`digital_touches`	Digital/remote HCP engagements	Rep activity
`competitor_share`	Competitor's market share (0–1)	Market
`market_access`	Payer coverage / formulary score (1–10)	Market
`hcp_tier`	Doctor quality tier (1=highest value)	Territory
`rep_tenure`	Rep years of experience	Rep quality
---
🔬 Methodology
```
┌─────────────────────────────────────────────────────────────┐
│                    PROJECT PIPELINE                         │
├──────────────┬──────────────┬────────────┬──────────────────┤
│  NB 01       │  NB 02       │  NB 03     │  NB 04 + 05      │
│  Data        │  EDA &       │  Territory │  Clustering &    │
│  Loading &   │  Trends      │  Feature   │  XGBoost         │
│  Inspection  │              │  Engineering│  Modeling        │
└──────────────┴──────────────┴────────────┴──────────────────┘
```
Phase 1 — Data Loading & Inspection (NB 01)
Loaded all 3 Kaggle files: `salesdaily`, `salesweekly`, `salesmonthly`
Verified date ranges, null counts, and data consistency
Cross-validated resampled daily totals against monthly file
Phase 2 — EDA & Trends (NB 02)
Visualised total sales per drug category across 8 WHO drug codes
Identified seasonal patterns — peak and trough months per drug
Built correlation heatmap across all drug categories
Computed year-over-year growth rates
Phase 3 — Territory Feature Engineering (NB 03)
Constructed 200 synthetic territories with realistic HCP tiers
Simulated rep activity features using domain-calibrated distributions
Built `sfe_master_dataset.csv` — 14,000 territory-month records
Validated feature-target correlations before modeling
Phase 4 — K-Means Territory Clustering (NB 04)
Scaled features using `StandardScaler`
Selected optimal K using elbow method + silhouette score
Segmented territories into 4 actionable business clusters
Labelled segments: Star, Growth Opportunity, High Effort Low ROI,
Low Potential
Phase 5 — XGBoost Rx Uplift Model (NB 05)
Trained Linear Regression baseline and XGBoost regressor
Evaluated using R², RMSE, MAE, and 5-fold cross-validation
Plotted actual vs predicted and feature importance rankings
---
📈 Results
Model Performance
Metric	Linear Regression (Baseline)	XGBoost
R²	0.9524	0.9550
RMSE	35.18	34.20
MAE	—	27.34
5-fold CV R²	—	0.9455 ± 0.0051
> A CV R² of 0.9455 ± 0.0051 confirms the model generalises well and is not
> overfitting — the low standard deviation (±0.005) shows consistent
> performance across all 5 folds.
Top Drivers of Rx Volume (Feature Importance)
```
Rep call frequency        ████████████████  ~28%  ← strongest driver
Drug samples dropped      ██████████████    ~24%
HCP tier (doctor quality) ████████          ~18%
Rep experience (years)    ███████           ~15%
Competitor market share   █████             ~10%
Market access score       ███               ~5%
```
Territory Segmentation
200 territories segmented into 4 clusters via K-Means
Top 20% high-ROI territories drive a disproportionate share of
total Rx volume — consistent with the Pareto principle in pharma sales
Star territories show highest call efficiency (Rx per rep visit)
Low potential territories show high competitor share and low HCP tier
---
💼 Key Business Findings
Rep call frequency is the #1 driver — each additional monthly visit
to a Tier 1 HCP territory generates significantly more Rx volume than
the same visit to a Tier 3 territory
Sample drops amplify call impact — territories with high
calls + high sample drops consistently outperform call-only territories
Top 20% territories disproportionately drive Rx — concentrating
rep resources on Star and Growth Opportunity segments maximises ROI
Competitor share is the strongest negative predictor — territories
with competitor share above 0.6 show significantly suppressed Rx
regardless of rep effort
---
🗂 Project Structure
```
pharma-sfe-project/
│
├── notebooks/
│   ├── 01_data_loading_and_inspection.ipynb
│   ├── 02_eda_and_trends.ipynb
│   ├── 03_territory_feature_engineering.ipynb
│   ├── 04_clustering_and_modeling.ipynb
│   └── 05_modeling_and_report.ipynb
│
├── data/
│   ├── salesdaily.csv              ← Kaggle source
│   ├── salesweekly.csv             ← Kaggle source
│   ├── salesmonthly.csv            ← Kaggle source
│   ├── sfe_master_dataset.csv      ← engineered dataset (14,000 rows)
│   └── sfe_territory_agg.csv       ← territory-level aggregates
│
├── outputs/
│   ├── 09_feature_importance_final.png
│   └── 11_actual_vs_predicted_final.png
│
└── README.md
```
---
▶️ How to Run
1. Clone the repository
```bash
git clone https://github.com/your-username/pharma-sfe-project.git
cd pharma-sfe-project
```
2. Create and activate environment
```bash
conda create -n sfe_env python=3.10 -y
conda activate sfe_env
```
3. Install dependencies
```bash
conda install scikit-learn pandas numpy matplotlib seaborn -y
pip install xgboost jupyter
```
4. Download dataset
Download from Kaggle Pharma Sales Data
and place all 3 CSV files in the project root folder.
5. Run notebooks in order
```
01 → 02 → 03 → 04 → 05
```
---
🛠 Tech Stack
Tool	Purpose
Python 3.10	Core language
Pandas	Data manipulation
NumPy	Numerical computing
Scikit-learn	K-Means clustering, baseline model, evaluation
XGBoost	Rx uplift prediction model
Matplotlib / Seaborn	Visualisation
Jupyter Notebook	Development environment
Git / GitHub	Version control
---
📋 Note on Data Methodology
> The Kaggle pharma sales dataset provides real monthly drug sales figures
> (N02BE — Paracetamol class) used as ground-truth Rx volume. Territory
> identifiers and rep activity features (call frequency, sample drops,
> digital touchpoints) were simulated using statistically calibrated
> distributions, consistent with industry SFE modeling practice where
> field CRM data is proprietary. Feature-target correlations (r = 0.63
> for calls, r = 0.63 for samples) confirm the simulation produces
> realistic commercial signal.
---
👩‍💻 Author
Sanchita Khemariya
Data Science Professional | Python | SQL | Machine Learning
Executive PG Certification — Data Science & AI, IIT Roorkee & Intellipaat
![LinkedIn](www.linkedin.com/in/sanchita-khemariya-28ab62309) 
![GitHub](git clone https://github.com/sanchitakhemariya/Pharma-sfe-project.git)
---
This project was built as part of a commercial analytics portfolio
targeting pharma data science roles.
