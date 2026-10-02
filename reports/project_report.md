# CalCOFI Water Temperature Prediction — Project Report

## 1. Objective

The objective of this project is to investigate relationships between oceanographic measurements and water temperature and to develop machine learning models capable of predicting water temperature from available measurements.

The project focuses on a practical regression problem using real-world oceanographic data rather than a synthetic dataset.

## 2. Research Question

Can water temperature (`T_degC`) be accurately predicted using depth (`Depthm`), salinity (`Salnty`), dissolved oxygen (`O2ml_L`), and engineered features?

## 3. Dataset

The project uses the CalCOFI dataset, containing more than 60 years of oceanographic observations collected off the coast of California.

The initial modeling dataset contains 864,863 observations for the selected variables:

- `Depthm`
- `Salnty`
- `T_degC`
- `O2ml_L`

Missing values are present in several variables. In the selected subset, missingness is approximately:

- Salinity: 5.48%
- Temperature: 1.27%
- Dissolved oxygen: 19.50%

Observations without the target value (`T_degC`) are removed for supervised learning. Missing predictor values are subsequently handled through median imputation inside the modeling pipelines.

## 4. Exploratory Data Analysis

The first stage examined the distributions and relationships among the selected variables.

For salinity and temperature, Pearson correlation was calculated after removing observations with missing values in either variable.

The correlation coefficient was approximately:

**r = -0.5053**

This indicates a moderate negative linear association. However, the scatter and regression plots show that the relationship is not purely linear. This provided motivation for testing non-linear models.

## 5. Data Splitting

The data were divided into:

- 70% training data
- 15% validation data
- 15% test data

The validation set was used during model development and selection. The test set was reserved for the final evaluation.

## 6. Baseline and Model Development

Several approaches were investigated.

### Linear regression

Linear regression was used as a simple baseline. Its purpose was not to provide the final model, but to establish a reference point for more complex approaches.

### Polynomial regression

Polynomial features were introduced to model non-linear relationships between predictors and temperature.

Polynomial regression improved upon the simple linear relationship but remained limited in its ability to capture the more complex structure of the data.

### XGBoost

XGBoost regression was then evaluated with different tree depths.

The model substantially improved predictive performance compared with the linear and polynomial approaches.

### Feature engineering

Additional transformations and derived features were investigated to provide the models with more information about the structure of the oceanographic variables.

### K-means clustering

K-means clustering was introduced as a feature-engineering technique.

The clustering model was fitted using training data, and the resulting cluster assignments were added as a categorical feature for the supervised model.

This allowed the XGBoost model to distinguish observations belonging to different regions of the feature space.

### Neural networks

Neural-network regressors were also tested using normalized input features and early stopping.

The neural networks achieved competitive results but did not outperform the final XGBoost model on the validation set.

## 7. Final Model

The selected model is an XGBoost regressor with a K-means-derived cluster feature.

Configuration:

```text
n_estimators     = 800
max_depth        = 11
learning_rate    = 0.01
subsample        = 0.8
colsample_bytree = 0.8
random_state     = 42
```

Median imputation is performed before the XGBoost estimator.

## 8. Final Evaluation

The final model was evaluated on the training, validation, and previously unseen test sets.

| Dataset | R² | RMSE (°C) | MAE (°C) |
|---|---:|---:|---:|
| Train | 0.9177 | 1.2178 | 0.7722 |
| Validation | 0.9077 | 1.2895 | 0.8111 |
| Test | 0.9075 | 1.2178 | 0.8095 |

The test R² of approximately 0.9075 indicates that the model explains a large proportion of the variance in water temperature within this dataset.

The test MAE of approximately 0.8095°C means that the average absolute difference between predicted and observed temperature is about 0.81°C.

The small difference between validation and test performance provides evidence that the selected model performs consistently on unseen observations.

## 9. Residual Analysis

Residual plots were used to examine the final model's errors.

The residual distribution is approximately centered around zero, with no obvious large systematic bias toward overprediction or underprediction.

However, the residual-vs-predicted plot shows some curved structure in part of the temperature range. This suggests that additional non-linear relationships or omitted variables may still be present.

Therefore, the model should not be interpreted as a complete physical representation of the oceanographic system. It is a predictive model based on a limited set of measured variables.

## 10. Conclusions

The project demonstrates that water temperature can be predicted reasonably well from a limited set of oceanographic measurements.

The analysis shows that:

1. Salinity has a meaningful but non-linear relationship with temperature.
2. Depth and dissolved oxygen provide additional predictive information.
3. Non-linear ensemble methods outperform simple linear approaches for this problem.
4. Feature engineering, including K-means clustering, can improve the representation of the feature space.
5. The final XGBoost model achieved approximately 0.907 R² and 0.812°C MAE on the held-out test set.
6. Residual analysis indicates that some structure remains unexplained.

## 11. Limitations and Future Work

The final model uses only a subset of the available CalCOFI information.

Potential improvements include adding:

- geographic coordinates;
- station identifiers;
- sampling date and season;
- pressure;
- nutrients;
- chlorophyll and biological measurements;
- additional physical and chemical variables.

A more rigorous follow-up study could also use temporal or spatial validation rather than a purely random split. This would help assess how well the model generalizes to future observations or previously unseen geographic locations.

