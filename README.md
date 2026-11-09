# Acute Sepsis 30-Day Readmission & MS-DRG Financial Analytics Pipeline

[![SQL](https://img.shields.io/badge/Language-SQL%20%2F%20DuckDB-blue.svg)](#)
[![Python](https://img.shields.io/badge/Python-3.10%2B-green.svg)](#)
[![Tableau](https://img.shields.io/badge/Visualization-Tableau-orange.svg)](#)
[![Data Source](https://img.shields.io/badge/Dataset-MIMIC--IV%20v2.2-red.svg)](#)

## Project Overview
Hospital readmissions within 30 days for acute sepsis pose significant clinical risks and severe financial penalties under Medicare's Hospital Readmissions Reduction Program (HRRP) and value-based care frameworks. 

This project engineers an end-to-end analytical pipeline using **MIMIC-IV** clinical data to:
1. Extract and standardize a cohort of adult sepsis admissions using dual-condition clinical criteria (ICD-9/10 diagnostic codes and acute organ dysfunction markers).
2. Classify reimbursement risk by mapping clinical severity to **Medicare Severity Diagnosis Related Groups (MS-DRG)** severity tiers (MS-DRG 870, 871, 872).
3. Evaluate independent clinical and procedural risk drivers for 30-day all-cause readmissions using multivariate logistic regression.
4. Deliver executive-ready Tableau visual analytics (including Odds Ratio Forest Plots) for clinical leadership and financial stakeholders.

---

## Tech Stack & Key Methodologies
* **Database & Querying:** SQL (PostgreSQL / DuckDB) — CTEs, Window Functions, Complex Joins, Data Cleaning.
* **Data Processing & Statistical Modeling:** Python (`pandas`, `numpy`, `statsmodels`) — Feature Engineering, Multivariate Logistic Regression, Odds Ratio (OR) & 95% Confidence Interval (CI) Calculation.
* **Domain Frameworks & Coding Systems:** ICD-9-CM, ICD-10-CM/PCS, MS-DRG Tiers, 30-Day Readmission Logic, Hospital Cost & Reimbursement Risk.
* **Data Visualization:** Tableau Desktop — Dual-Axis Forest Plots, Gantt Reference Scaling, Executive Dashboards.

---

## Repository Structure
```text
├── data/
│   ├── raw/                   # Schema documentation & MIMIC-IV field definitions (GitIgnored)
│   └── processed/             # Analytical cohort output CSVs
├── sql&notebook/
│   ├── 01_cohort_extraction.ipynb   # Primary SQL extraction with dual-condition sepsis criteria
│   └── 02_drg_feature_engineering.ipynb # Standardizing MS-DRG tiers & invasive procedures
│   └── 03_sepsis_readmission_modeling.ipynb  # Statistical modeling, statsmodels regression, & export
├── dashboards/
│   └── Sepsis_Readmission_Executive_Dashboard.twbx # Tableau packaged workbook
├── README.md                  # Executive project documentation
└── requirements.txt           # Python dependencies

Key Findings & Strategic Insights
MS-DRG Severity Risk Gradient: Patients admitted under MS-DRG 871 (Septicemia with MV >96 hours) demonstrated a 0.07 higher odds of 30-day readmission compared to MS-DRG 207.
High-Cost Invasive Procedures: Mechanical ventilation and acute dialysis were identified as primary independent procedural predictors for 30-day readmission, controlling for age and baseline comorbidity scores.


1. SQL Cohort Extraction Logic
The cohort was defined using admissions, diagnoses_icd, procedures_icd, and drgcodes tables from MIMIC-IV:
Inclusion: Adult patients ($\ge$ 18 years) with primary or secondary ICD-9/10 sepsis codes paired with acute organ dysfunction markers.
Readmission Logic: Utilized LEAD() window functions over admissions.admittime and dischtime to flag subsequent index admissions occurring within $\le$ 30 days.

2. Risk Adjustment & Regression Modeling
To evaluate independent risk factors, a multivariate logistic regression was executed in statsmodels:
   $$\operatorname{logit}(P(\text{Readmission} = 1)) = \beta_0 + \beta_1(\text{MS-DRG Tier}) + \beta_2(\text{Mech Vent}) + \beta_3(\text{Age}) + \varepsilon$$Transformed regression coefficients into Odds Ratios ($e^\beta$) and 95% Confidence Intervals.
Standardized high-cost procedure flags to prevent model multicollinearity.   

Dashboard & Visualizations
Executive Forest Plot (Tableau)
The primary deliverable features a customized Forest Plot communicating adjusted Odds Ratios and 95% Confidence Intervals across clinical and procedural predictors:
Circle Marks: Point estimates for Odds Ratios.
Dual-Axis Gantt Bars: 95% Confidence Interval spans across a reference line set at $\text{OR} = 1.0$,

How to Run This Repository
Prerequisites
Python 3.10+
DuckDB environment with access to MIMIC-IV v2.2 hosp module tables.
Quick Start
Clone the repository:
git clone https://github.com/your-username/sepsis-readmission-analytics.git
cd sepsis-readmission-analytics

Set up the virtual environment & install dependencies:
python -m venv venv
source venv/bin/activate  # On Windows use: venv\Scripts\activate
pip install -r requirements.txt

## Execute SQL Pipeline:
Run sql&notebook/01_cohort_extraction.ipynb against your MIMIC-IV database instance to generate the primary cohort table.
Run sql&notebook/02_drg_feature_engineering.ipynb against your MIMIC-IV database instance to merge DRG procedure information for the cohort
Run Statistical Analysis:
Open and execute notebooks/03_sepsis_readmission_modeling.ipynb to output regression tables and exported CSVs for Tableau.
