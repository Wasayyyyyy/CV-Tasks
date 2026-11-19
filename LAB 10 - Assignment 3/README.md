# Comparative Implementation of ML Regression Algorithms

**Name:** Abdul Wasay
**Reg No:** FA23-BAI-043
**Notebook:** `A3_Regression_Comparison_FA23_BAI_043.ipynb`

Six regression algorithms are compared on the **Diabetes dataset** (442 patients, 10 features), using the same features, target and 80:20 split (353 train / 89 test) for every model.

## Results (test set)

| Rank | Model | MAE | MSE | RMSE | R² |
|---|---|---|---|---|---|
| 1 | **XGBoost** | 42.48 | **2760.70** | **52.54** | **0.479** |
| 2 | Random Forest | 42.72 | 2772.07 | 52.65 | 0.477 |
| 3 | Neural Network | **41.74** | 2876.34 | 53.63 | 0.457 |
| 4 | Linear Regression | 42.79 | 2900.19 | 53.85 | 0.453 |
| 5 | KNN | 44.87 | 2964.46 | 54.45 | 0.441 |
| 6 | Decision Tree | 49.37 | 3735.50 | 61.12 | 0.295 |

## Key points

- **Best model: XGBoost.** It is first on MSE, RMSE and R².
- **The top 5 are very close.** They are within 1.9 RMSE points of each other, and only the Decision Tree is significantly worse.
- **The Neural Network has the lowest MAE**, but it makes a few larger errors, which raises its RMSE.
- **The Decision Tree is weakest.** Its depth-2 tree can output only 4 different values.
