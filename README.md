```
# ml-driven-yield-curve-forecasting
**Forecast yield curves using ML + a smooth yield surface.**  
This notebook demonstrates a pipeline that cleans GLC spot-curve Excel files, engineers lagged + rolling features and PDE-like surface derivatives, fits Ridge / Random Forest / XGBoost models, and performs iterative multi-day forecasting while updating features with previous predictions.

---

## Highlights / What I did
- Loaded and combined two Excel data files (spot-curve sheet `"4. spot curve"`).
- Transformed data to long format (Time, Maturity, Rate) and sorted chronologically.
- Modeled a smooth bivariate surface `Rate = f(time, maturity)` using `SmoothBivariateSpline`.
  - Chose smoothing factor `s = len(rate_values) / 150`.
  - Computed and reported RMSE and R² of the surface.
- Calculated **partial derivatives** of the surface:
  - `Partial_Derivative_Time` (∂Rate/∂Time)
  - `Partial_Derivative_Maturity` (∂Rate/∂Maturity)
  - Added these derivatives as model features.
- Engineered time-series features:
  - `Rate_lag_1`, `Rate_lag_5`, `Rate_lag_20`
  - `Rate_rolling_mean_5`, `Rate_rolling_std_20`
- Models implemented and evaluated (RMSE & R²):
  - **Ridge** (alpha=1.0)
  - **Random Forest** (n_estimators=100, random_state=42)
  - **XGBoost** (n_estimators=100, learning_rate=0.1, max_depth=3, random_state=42)
- Train / test split: chronological (80% train / 20% test).
- Trained on full data and performed **iterative multi-day forecasting** (default `days_ahead=365` in notebook):
  - Created a future grid with the same maturities and appended the last 20 historical days as context.
  - Iteratively predicted the next day, then updated lag/rolling features for the following day.
- Visualization:
  - Yield curve evolution (multiple dates)
  - 3D yield surface (observed vs forecast)
  - Time series per maturity

---

## Project structure (suggested)
```
