# GrubHub Sales Prediction – Indiana Memorial Union (The Vault)

A data mining and regression project that predicts item-level **Total Merchant Sales** for a campus food venue using real Grubhub transaction data. The pipeline covers preprocessing, feature engineering, multi-model evaluation, and cross-month generalization testing.

## Project Structure

| File | Description |
|------|-------------|
| `DM_project.ipynb` | Training pipeline on September 2024 sales data |
| `DM_project_test.ipynb` | Inference pipeline on October 2024 sales data |
| `trained_xgboost_model.json` | Saved XGBoost model (generated after training) |
| `weighted_model_evaluation_results1.csv` | Model comparison results (generated after training) |
| `X_scaled.csv` | Scaled October features for inference (generated from test notebook) |
| `y_total_merchant_sales.csv` | October ground truth target values (generated from test notebook) |

## Dataset

Raw data sourced from Grubhub's ProductMix export for **The Vault** at Indiana Memorial Union:
- `ProductMix_2024-09-01-2024-09-30.xlsx` — Training data
- `ProductMix_2024-10-01-2024-10-31.xlsx` — Test/inference data

Key columns used: `Item`, `Modifier`, `Modifier Group`, `Category`, `Time Interval`, `Order Date`, `Total Merchant Sale`

## Pipeline Overview

### Preprocessing
- Currency string cleaning (`$` removal, cast to float)
- Datetime parsing for `Order Date` and `Time Interval`
- Item/modifier filtering (removal of low-frequency or irrelevant entries)
- Missing value imputation using per-item mode for `Modifier` and `Modifier Group`
- Removal of negative sales entries (refunds/voids)

### Feature Engineering
- `Hour` — extracted from `Time Interval`
- `day_of_week` — extracted from `Order Date`
- `is_weekend` — binary flag
- `lag_1`, `lag_2` — lagged sales values
- `rolling_mean_3`, `rolling_mean_5` — rolling averages over 3 and 5 transactions

### Encoding
- **Target Encoding** — `Item`, `Modifier` (encoded against `Total Merchant Sale`)
- **One-Hot Encoding** — `Category`, `Modifier Group`
- **Standard Scaling** — applied to all features post-encoding

### Train/Test Split
Time-based split: training on September 1–24, held-out test on September 25–30. Cross-month generalization evaluated using October data.

## Models Evaluated

| Model | Notes |
|-------|-------|
| Linear Regression | Baseline |
| Ridge Regression | L2 regularization |
| Lasso Regression | L1 regularization |
| Random Forest | With balanced sample weights for zero/non-zero sales |
| Gradient Boosting | Sklearn implementation |
| XGBoost | Custom loss penalizing non-zero sales; **selected as final model** |
| LightGBM | Custom loss variant |

Evaluation used a **weighted metric scheme** to address class imbalance between zero-sales and non-zero-sales transactions.

## Evaluation Metrics

- Weighted RMSE / MAE (higher penalty on non-zero sales)
- Separate RMSE / MAE for zero-sales and non-zero-sales subsets
- R² Score
- F1 Score, Precision, Recall, Accuracy (via binary threshold: predicted > 0)

## Requirements

```bash
pip install pandas numpy matplotlib seaborn scikit-learn xgboost lightgbm category_encoders openpyxl
```

## Usage

**Train:**
```python
# Run DM_project.ipynb
# Outputs: trained_xgboost_model.json, weighted_model_evaluation_results1.csv
```

**Inference on new month's data:**
```python
# Run DM_project_test.ipynb
# Outputs: X_scaled.csv, y_total_merchant_sales.csv

# Then in DM_project.ipynb, load the saved model and run the evaluation cells
model.load_model("trained_xgboost_model.json")
y_pred = model.predict(test_data)
```

## Key Design Decisions

- **Time-based splitting** preserves temporal order and prevents data leakage from lag/rolling features.
- **Weighted loss and sample weights** compensate for the high proportion of $0.00 entries (cancelled or zero-price items) in the Grubhub export.
- **Target encoding** for high-cardinality item/modifier columns avoids the dimensionality explosion of one-hot encoding on 50+ unique values.
