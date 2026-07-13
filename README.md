[Readme.md](https://github.com/user-attachments/files/29955114/Readme.md)

# Interpretable Machine-Learning Surrogates for Simplified WQI Assessment

This repository provides the trained machine-learning surrogate models and de-identified independent test datasets associated with the manuscript:

**Interpretable Machine-Learning Surrogates for Simplified WQI Assessment of Surface-Water and Groundwater Sources**

The models were developed to approximate validated water quality indices (WQIs) for surface-water and groundwater drinking-water sources and to support monitoring simplification under different levels of data availability.

## Repository scope

This repository contains:

- trained surrogate model files for surface-water and groundwater WQI assessment;
- de-identified independent test datasets used for temporal validation;
- model metadata and feature-order files, where applicable.

The repository does **not** contain the original monitoring records, model-development datasets, or raw site-level identifiers.

## Models

The study developed three model tiers for each water type:

| Water type | Model | Description |
|---|---|---|
| Surface water | `SW-FULL` | Full-indicator surrogate model |
| Surface water | `SW-16` | Water-type-specific reduced model using 16 indicators |
| Surface water | `F4-SW` | Four-indicator common model for simplified assessment |
| Groundwater | `GW-FULL` | Full-indicator surrogate model |
| Groundwater | `GW-14` | Water-type-specific reduced model using 14 indicators |
| Groundwater | `F4-GW` | Four-indicator common model for simplified assessment |

The four indicators shared by the compact surface-water and groundwater models are:

- sulfate (`SO4²⁻`);
- chloride (`Cl⁻`);
- fluoride (`F⁻`);
- nitrate nitrogen (`NO3⁻-N`).

The released model files are intended for WQI reconstruction, model verification, and methodological reference. They should not be used as independent replacements for regulatory water-quality assessment.

## Suggested repository structure

```text
.
├── README.md
├── LICENSE
├── CITATION.cff
├── models/
│   ├── surface_water/
│   │   ├── SW-FULL.cbm
│   │   ├── SW-16.cbm
│   │   └── F4-SW.cbm
│   └── groundwater/
│       ├── GW-FULL.cbm
│       ├── GW-14.cbm
│       └── F4-GW.cbm
├── metadata/
│   ├── model_information.json
│   └── feature_columns.json
└── data/
    ├── surface_water_independent_test_deidentified.csv
    └── groundwater_independent_test_deidentified.csv
```

File names may differ slightly from those shown above. Please refer to the metadata files for the corresponding model type, feature names, and required feature order.

## Independent test datasets

The released test datasets correspond to the independent temporal validation data and were not used for model training, hyperparameter optimization, feature-importance analysis, or indicator selection.

To protect restricted monitoring information:

- direct water-source identifiers have been removed;
- administrative and site-identifying fields have been removed or generalized;
- sample records have been randomly reordered;
- only variables required for model evaluation have been retained.

The released files are therefore intended to support model verification without disclosing restricted source-level monitoring information.

## Data availability and sharing restrictions

The original monitoring records were obtained from the relevant water-resources management authorities and are subject to data-sharing restrictions. Consequently, the raw monitoring data, complete model-development datasets, and identifiable site-level records cannot be made publicly available.

To improve transparency and enable partial verification of the reported results, this repository provides:

1. the trained model artifacts; and  
2. de-identified independent test datasets with direct identifiers removed and record order randomized.

Access to the original data may only be considered subject to authorization from the data-owning management authority. Reasonable requests may be directed to the corresponding author, but access cannot be guaranteed.

## Model loading

The CatBoost model files can be loaded using Python:

```python
from catboost import CatBoostRegressor
import pandas as pd

model = CatBoostRegressor()
model.load_model("models/surface_water/SW-FULL.cbm")

test_data = pd.read_csv(
    "data/surface_water_independent_test_deidentified.csv"
)

# Use the exact feature names and order documented in the metadata file.
feature_names = model.feature_names_
X_test = test_data[feature_names]

predicted_wqi = model.predict(X_test)
```

Where an observed WQI column is included in the released test dataset, model performance can be evaluated using standard regression metrics:

```python
from sklearn.metrics import mean_absolute_error, mean_squared_error, r2_score

y_test = test_data["WQI"]
r2 = r2_score(y_test, predicted_wqi)
rmse = mean_squared_error(y_test, predicted_wqi) ** 0.5
mae = mean_absolute_error(y_test, predicted_wqi)

print({"R2": r2, "RMSE": rmse, "MAE": mae})
```

Before prediction, confirm that:

- the correct model is matched to the correct water type;
- all required variables are present;
- feature names and feature order match the model metadata;
- no additional preprocessing is applied unless explicitly documented.

## Software requirements

The model files were generated in Python using CatBoost and related scientific-computing libraries. A minimal environment is:

```text
python
catboost
pandas
numpy
scikit-learn
```

For best compatibility, the software versions reported in the model metadata should be used.

## Limitations

This repository is designed to support verification of trained models rather than complete end-to-end reproduction of model development. Full retraining cannot be reproduced from this repository because the original model-development data are not publicly shareable.

The models were developed for the water-quality conditions and monitoring framework represented in the study. Application to other regions, monitoring programs, or regulatory systems should be preceded by independent validation and, where necessary, recalibration.

## Citation

Please cite the associated article when using the models or test data:

> Yan, H., Zhou, C., Sun, J., Mao, Y., Jiang, S., and Cheng, X. Interpretable Machine-Learning Surrogates for Simplified WQI Assessment of Surface-Water and Groundwater Sources. *Water Research*. [Manuscript details to be updated after publication.]

A DOI and complete citation will be added after publication.

## Contact

**Prof. Xiuwen Cheng**  
College of Earth and Environmental Sciences  
Lanzhou University  
Lanzhou 730000, P. R. China  
Email: chengxw@lzu.edu.cn

## License

The recommended license for the repository should be selected according to the data-sharing agreement and institutional requirements. The license should distinguish between:

- permitted use of the trained model files; and
- permitted use of the de-identified test datasets.

Do not apply an unrestricted data license unless it is consistent with the requirements of the data-owning management authority.
