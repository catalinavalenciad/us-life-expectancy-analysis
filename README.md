# Determinants of Life Expectancy Across U.S. Counties

## Overview
This project examines the relationship between health conditions, healthcare access, and county-level life expectancy across the United States. Using pooled county-year data from 2019–2023 across three public health data sources, the analysis covers the full modeling workflow from data acquisition and merging through exploratory analysis, variable selection, regression modeling, and cross-validated evaluation.

The project is structured as an end-to-end **regression and classification pipeline** applied to real-world public health data, with an emphasis on identifying which health and insurance factors most reliably predict life expectancy outcomes once multicollinearity is accounted for.

This analysis was completed as part of a larger group research project, in which each member independently analyzed a different domain of predictors (socioeconomic, environmental, healthcare, mobility, and food systems) before the findings were synthesized into a comparative paper. This repository documents my individual contribution — the health conditions and healthcare access analysis — including my code, data processing, and findings. The full group report is included in this repository under `paper/`.

## Business Problem
County of residence is often a stronger predictor of lifespan than genetics, and these geographic gaps in life expectancy have widened even as national health spending has climbed:

- Disease burden and healthcare access vary widely across U.S. counties
- Public health planners and policymakers need to understand which determinants carry the most predictive weight once correlated factors are held constant

The goal is to identify whether disease prevalence and insurance coverage, independent of other socioeconomic and environmental factors, can reliably predict county-level life expectancy — and to quantify how much of that variation these factors alone explain.

## Data Sources
- **CDC PLACES: Local Data for Better Health** (2022, 2023, 2024 releases)
- **County Health Rankings** — Life Expectancy Data (2019–2023)
- **U.S. Census Bureau** — Healthcare Coverage, 2023 5-Year Estimates

**Dataset characteristics:**
- 15,359 county-year observations across 5 years
- 20 variables after merging
- Response variable: `Life_Expectancy` (county-level, in years)
- Predictors include: Stroke, COPD, Diabetes, Depression, Coronary Heart Disease, Obesity, Physical Inactivity, Annual Checkup rate, and insurance coverage variables

## Tech Stack
- **Language:** R
- **Environment:** Jupyter Notebook (R kernel)
- **Key Libraries:**
  - `dplyr`, `tidyr` (data manipulation)
  - `ggplot2`, `reshape2` (visualization)
  - `glmnet` (Lasso variable selection)
  - `car` (VIF / multicollinearity diagnostics)
  - `pROC` (ROC curve and AUC evaluation)
  - `caret` (cross-validation)
  - `sf`, `tigris` (geographic mapping)

## Methodology

### 1. Data Integration
- Merged three raw datasets (PLACES, County Health Rankings, Census healthcare coverage) into a single county-year analytical dataset
- Standardized column names and joined on FIPS code and year

### 2. Exploratory Data Analysis
- Computed summary statistics and screened for outliers
- Ran correlation analysis to identify the strongest candidate predictors
- Mapped the top predictor (Stroke prevalence) geographically at the county level

### 3. Variable Selection
- Applied **Lasso regression** with cross-validation to select predictors
- Reviewed the cross-validation plot to confirm the optimal lambda

### 4. Multiple Linear Regression
- Fit an OLS model using the Lasso-selected predictors
- Ran goodness-of-fit diagnostics (residuals vs. fitted, Q-Q plot)
- Checked multicollinearity via VIF

### 5. Logistic Regression
- Modeled a binary outcome (above/below national average life expectancy)
- Evaluated discriminative ability via ROC curve and AUC

### 6. Cross-Validation
- Ran 10-fold cross-validation on both models to confirm stability of performance metrics

## Results & Key Insights

| Model | Metric | Value |
|---|---|---|
| Linear Regression | R² | 0.616 |
| Linear Regression | RMSE (test) | 1.777 |
| Logistic Regression | Accuracy (test) | 81.43% |
| Logistic Regression | AUC (test) | 0.897 |

- **Stroke and COPD** are the strongest predictors of reduced life expectancy, consistent across both the linear and logistic models
- **Lack of insurance coverage** (Uninsured_Total) has a meaningful negative effect independent of disease burden
- **Annual Checkup** rate was the only non-significant predictor in the linear model once disease burden and insurance coverage were controlled for
- **Coronary Heart Disease** showed a counterintuitive positive coefficient in both models, attributed to multicollinearity with Stroke and COPD (confirmed via VIF)
- Agreement between the independently derived linear and logistic models strengthens confidence in the findings

## Repository Structure

```
us-life-expectancy-analysis/
├── data/
│ ├── raw/
│ │ ├── Healthcare_Coverage_2023_5-year_Data.csv
│ │ ├── Life_Expectancy_Data_2019.csv
│ │ ├── Life_Expectancy_Data_2020.csv
│ │ ├── Life_Expectancy_Data_2021.csv
│ │ ├── Life_Expectancy_Data_2022.csv
│ │ ├── Life_Expectancy_Data_2023.csv
│ │ └── health_conditions_datasets.zip
│ └── processed/
│ └── merged_data.csv

├── notebooks/
│ └── life_expectancy_analysis.ipynb

├── visualizations/
│ ├── 01_life_expectancy_distribution.png
│ ├── 02_life_expectancy_by_year.png
│ ├── 03_scatter_plots.png
│ ├── 04_correlation_heatmap.png
│ ├── 05_choropleth_stroke.png
│ ├── 06_gof_diagnostics.png
│ ├── 07_roc_curve.png
│ └── 08_lasso_cv.png

├── report/
│ └── Determinants_of_Life_Expectancy_Across_US_Counties.pdf

├── docs/
│ ├── index.html
│ └── data_processing_and_analysis.html

└── README.md
```

## Notes
This project prioritizes methodological transparency and honest reporting over any single "best" metric. Both a linear and logistic framing of the outcome were tested independently, and their agreement on the same key predictors is treated as a stronger form of evidence than either model's performance alone. Limitations include missing health condition data for 2019 and 2022, and Stroke prevalence data unavailable for six states in the CDC PLACES dataset.

---

## Contact
- **Name:** Catalina Valencia
- **Email:** catalinavalenciad@gmail.com
- **LinkedIn:** https://www.linkedin.com/in/catalina-valencia/
