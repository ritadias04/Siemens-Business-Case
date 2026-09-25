# Hierarchical Revenue Forecasting – Siemens Advanta Consulting

Business case developed at NOVA IMS in partnership with **Siemens Advanta Consulting** (Business Cases with Data Science, April 2026). Final grade: 16.75.

## Business Problem
Revenue planning relied on manual estimates across a large, multi-level business structure, which made forecasting slow, inconsistent and hard to scale. The goal was to build an automated, objective forecasting system that predicts revenue at every level of the hierarchy and gives the business a single source of truth for planning.

## Data
- **Internal sales data:** monthly revenue from April 2021 to September 2024, organised in a three-level hierarchy (Business Unit → Segment → Subsegment).
- **External market data:** monthly macroeconomic indicators for 8 economies (China, France, Germany, Italy, Japan, Switzerland, UK, US), including GDP, industrial production, inflation, interest rates, exports and steel production.

## Approach (CRISP-DM)
1. **Data preparation:** consistency checks between training and validation sets, forward fill and linear interpolation for missing market data, and removal of highly correlated features (|r| > 0.95).
2. **Feature engineering:** lags (1–4 periods), differences, rolling means and rolling standard deviations, all shifted to prevent data leakage. Aggregated global indicators were created from the country-level data.
3. **Stationarity-aware imputation:** each subsegment was classified as stationary or non-stationary, and missing values were filled with a strategy suited to each type.
4. **Bottom-up forecasting:** forecasts are produced at subsegment level and aggregated upwards, so all levels of the hierarchy stay consistent.
5. **Modelling:** LightGBM, XGBoost (full and feature-selected versions) and Random Forest, tuned with grid search.
6. **Evaluation:** a weighted RMSE that prioritises high-revenue areas, combining Subsegment (25%), Segment (25%) and Business Unit (50%) levels.

Time-based split: training on periods 1–36 (up to March 2024), validation on the following months.

## Results

| Model | Final Error | RMSE |
|---|---|---|
| LightGBM | 0.0420 | 3,537,926 € |
| XGBoost | 0.0346 | 2,970,130 € |
| XGBoost (feature-selected) | 0.0402 | 3,444,641 € |
| Random Forest | 0.0616 | 5,089,072 € |
| **Weighted Ensemble** | **0.0332** | **2,796,580 €** |

The final **weighted ensemble** (XGBoost 55%, feature-selected XGBoost 30%, Random Forest 10%, LightGBM 5%) outperformed every individual model, with a **2.9% forecast error** on the validation period, and closely tracks real revenue at the aggregate level.
