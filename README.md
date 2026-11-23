# MiniCroppkins: Crop Yield Prediction for Punjab

This project uses supervised machine learning to predict crop yield in Punjab from historical agronomic, climate, fertilizer, land-use, soil, and farm-size data. The complete project is documented in [Report.pdf](Report.pdf), with the reproducible analysis in [Crop_Yield_Prediction_Model copy.ipynb](Crop_Yield_Prediction_Model%20copy.ipynb).

## Highlights

- Built an end-to-end data-cleaning and model-training workflow with pandas and scikit-learn.
- Combined district-level crop production, rainfall, fertilizer use, land-use, farm-size, and soil data.
- Engineered seasonal rainfall features and crop yield as tonnes per hectare.
- Compared Linear Regression, K-Nearest Neighbors, Decision Tree, and Random Forest regressors.
- Identified Kharif rainfall and sugarcane crop type as the strongest predictors.
- Selected a tuned Random Forest model as the best and most robust solution.

## Results

The baseline model comparison on the 20% test set was:

| Model | RMSE | R-squared |
|---|---:|---:|
| Random Forest | 1.3849 | 0.9887 |
| Decision Tree | 2.0489 | 0.9753 |
| K-Nearest Neighbors | 1548.7738 | -14,113.8433 |
| Linear Regression | 1612.9136 | -15,307.1346 |

Hyperparameter tuning confirmed that Random Forest generalizes better than a single Decision Tree:

| Tuned model | Cross-validation R-squared | Optimal parameters |
|---|---:|---|
| Random Forest | 0.7647 | `max_depth=10`, `min_samples_split=5`, `n_estimators=100` |
| Decision Tree | 0.7990 | `max_depth=20`, `min_samples_leaf=4`, `min_samples_split=2` |

The tuned Decision Tree performed poorly on the unseen test set and showed strong overfitting (RMSE 374.0297; R-squared -822.2131). Although its cross-validation score was higher, the tuned Random Forest was retained as the champion model because its ensemble averaging is more stable.

### Feature importance

The Random Forest identified these features as the most influential:

1. Kharif rainfall (June-September): 0.7986
2. Sugarcane crop indicator: 0.0942
3. Fazilka district indicator: 0.0504
4. Pathankot district indicator: 0.0281
5. Annual district rainfall: 0.0100

These results reinforce the importance of monsoon rainfall for Punjab agriculture.

## Repository structure

```text
.
├── Crop_Yield_Prediction_Model copy.ipynb  # Data preparation and model training
├── Report.pdf                               # Project report and findings
└── data_sets/
    ├── crop_production.csv                  # District, crop, area, production
    ├── crop_data.csv                        # Soil and crop reference data
    ├── 32_13AnnualAverageRainFall (3).csv  # Annual district rainfall
    ├── Rainfall_Data_LL.csv                 # State monthly rainfall
    ├── Table_4.24_NConsumption.csv         # Nitrogen consumption
    ├── Table_4.24_P2O5_Consumption.csv     # Phosphorus consumption
    ├── Table_4.24_K2O_Consumption.csv      # Potassium consumption
    ├── Table_4.1_areaunderbarrenland.csv   # Barren land area
    ├── Table_4.9_Area_KharifandRabi_foodcrops.csv
    ├── Table_4.15_Large.csv
    └── Table_4.15_Medium.csv
```

## Methodology

1. **Load and clean data**
   - Standardize district names and convert year columns from wide to long format.
   - Handle missing values and inconsistent labels such as `NAWANSHAHR` and `SBS NAGAR`.
2. **Integrate datasets**
   - Start with crop production records and progressively merge rainfall, fertilizer, land-use, soil, and farm-size features.
3. **Engineer features**
   - Calculate yield as production divided by crop area.
   - Create Kharif rainfall (June-September) and Rabi rainfall (October-March) totals.
4. **Clean the target**
   - Remove physically impossible yield values greater than 100 tonnes/hectare.
5. **Prepare features**
   - One-hot encode district, season, crop, and soil type.
   - Standardize numerical features.
   - Split the data into 80% training and 20% test sets.
6. **Train and tune models**
   - Compare baseline regressors.
   - Tune the Decision Tree and Random Forest with 3-fold cross-validated `GridSearchCV`.

## Setup

### Requirements

- Python 3.9 or later
- pandas
- NumPy
- scikit-learn
- matplotlib
- seaborn
- Jupyter Notebook

Install the Python dependencies:

```bash
python -m pip install pandas numpy scikit-learn matplotlib seaborn jupyter
```

### Run the notebook

```bash
git clone https://github.com/Sneharaj7645653/minicroppkins_supervised_ml.git
cd minicroppkins_supervised_ml
jupyter notebook "Crop_Yield_Prediction_Model copy.ipynb"
```

> **Notebook path note:** the notebook currently uses source filenames without the `data_sets/` prefix and writes generated intermediate CSV files to its working directory. Run it from a directory where the source CSV files are available, or update the notebook's file-path variables to point to `data_sets/<filename>`. The notebook also creates `final_master_dataset.csv` as an intermediate artifact; this file is not included in the repository.

## Generated workflow artifacts

The notebook is designed to generate and use:

- `merged_rainfall_data.csv`
- `cleaned_agronomic_data.csv`
- `agronomic_plus_rainfall_data.csv`
- `master_data_v1.csv`
- `master_data_v2.csv`
- `final_master_dataset.csv`
- `yield_distribution.png`
- `yield_vs_numerical_features.png`
- `yield_vs_top_crops.png`
- `correlation_heatmap.png`
- `feature_importances.png`

## Data limitations

- Temperature data could not be obtained consistently at the district-year level and is not included.
- Irrigation data was excluded because its units and values were inconsistent across available sources.
- State-wide rainfall is used for seasonal features, while the annual rainfall feature is district-level.
- District names, reporting formats, and missing values vary across government datasets and require continued normalization.

## Future work

- Add high-quality district-level temperature observations.
- Extend the model into a crop recommendation system using forecast weather.
- Explore gradient boosting methods such as XGBoost or LightGBM.
- Add rainfall deviation and drought/flood indicators.
- Add cross-validation and model serialization for deployment.

## Project report

The [full project report](Report.pdf) includes the problem statement, data sources and challenges, methodology, model analysis, conclusions, and recommendations.
