# CalCOFI Water Temperature Prediction

## Project Overview

This project uses the CalCOFI oceanographic dataset to investigate whether water temperature (`T_degC`) can be predicted from physical and chemical measurements collected off the California coast.

The project combines exploratory data analysis, feature engineering, unsupervised learning, and supervised regression models.

### Research question

> Can water temperature be accurately predicted using depth, salinity, dissolved oxygen, and engineered features derived from these measurements?

## Dataset

The project uses the CalCOFI dataset, which contains more than 60 years of oceanographic observations collected off the coast of California.

The main variables used in the final modeling stage are:

- `Depthm` — sampling depth in meters
- `Salnty` — salinity
- `O2ml_L` — dissolved oxygen
- `T_degC` — water temperature (target)

The original dataset contains substantial missingness, particularly in dissolved oxygen measurements. Missing predictor values are handled using median imputation within the modeling pipeline.

## Methodology

The analysis follows these main stages:

1. Exploratory data analysis
2. Missing-value analysis
3. Correlation and feature-relationship analysis
4. Train/validation/test split
5. Baseline linear regression
6. Polynomial regression
7. XGBoost regression
8. Feature engineering
9. K-means clustering as an additional feature
10. Neural-network regression
11. Model comparison
12. Final evaluation on an unseen test set
13. Residual analysis

The final model is an **XGBoost regressor using a K-means-derived cluster feature**.

### Final XGBoost configuration

- `n_estimators = 800`
- `max_depth = 11`
- `learning_rate = 0.01`
- `subsample = 0.8`
- `colsample_bytree = 0.8`
- `random_state = 42`

The clustering component is fitted using the training data only, helping prevent information from the validation/test sets from influencing the learned clusters.

## Final Results

| Dataset | R² | RMSE (°C) | MAE (°C) |
|---|---:|---:|---:|
| Train | 0.9177 | 1.2178 | 0.7722 |
| Validation | 0.9077 | 1.2895 | 0.8111 |
| Test | **0.9075** | **1.2872** | **0.8095** |

The close validation and test performance indicates that the final model maintains similar predictive performance on unseen data.

The test-set MAE of approximately **0.81°C** means that the model's average absolute prediction error is about 0.81°C on the held-out test observations.

## Key Findings

- Salinity has a moderate negative linear association with temperature (`Pearson r ≈ -0.505`), but the relationship is not purely linear.
- Depth and dissolved oxygen provide additional predictive information.
- Non-linear tree-based models capture the relationships more effectively than simple linear models.
- Adding engineered features and a K-means cluster feature improved the final predictive performance.
- The final XGBoost model achieved a test R² of approximately 0.907.
- Residual analysis indicates generally balanced errors, although the residual-vs-predicted plot suggests some remaining non-linear structure.

## Repository Structure

calcofi-water-temperature/
│
├── data/
│   └── README.md
│
├── notebooks/
│   └── calcofi_water_temperature.ipynb
│
├── models/
│   └── xgb_pipeline_kmeans_f_best.pkl
│
├── reports/
│   └── project_report.md
│
├── README.md
└── requirements.txt


The raw CalCOFI data are not included in the repository because of their size. The notebook documents how the dataset can be obtained.

## Technologies

- Python
- pandas
- NumPy
- SciPy
- scikit-learn
- XGBoost
- TensorFlow / Keras
- Matplotlib
- Seaborn
- Jupyter Notebook

## Limitations and Future Work

The model uses a relatively small set of oceanographic variables compared with the full CalCOFI dataset. Additional information such as geographic position, station, date/season, pressure, nutrients, and other biological measurements could potentially capture structure that remains in the residuals.

Future work could therefore investigate:

- temporal and spatial features;
- seasonal effects;
- station-level effects;
- additional oceanographic variables;
- systematic hyperparameter optimization;
- cross-validation strategies that explicitly account for spatial or temporal dependence.

## Author

**Maksim Zarnitsyn**

Data Science / Machine Learning portfolio project.
