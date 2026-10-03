# Telomere Discordance in NHANES

This project investigates disagreement between two telomere-related measures in NHANES:

- qPCR-measured leukocyte telomere length
- `HorvathTelo`, a DNA methylation-derived telomere length estimator

The main question is:

> Can common clinical and demographic characteristics help explain or predict why these two measures disagree at the individual level?

## Overview

Although qPCR telomere length and DNA methylation-derived telomere estimates are related, they do not provide identical information for individual participants.

This project examines two forms of discordance:

- **Signed discordance**: whether qPCR telomere length is relatively higher or lower than the methylation-derived estimate
- **Absolute discordance**: how large the disagreement is, regardless of direction

The analysis includes exploratory statistics and machine-learning models to test whether common clinical characteristics contain useful predictive information about this disagreement.

## Data

Data were obtained from the public-use National Health and Nutrition Examination Survey (NHANES), primarily from the 1999–2002 survey cycles.

The merged analysis dataset contains approximately 2,530 participants aged 50–85.

Variables used include:

- qPCR leukocyte telomere length
- DNA methylation-derived telomere estimate (`HorvathTelo`)
- age
- sex
- smoking status
- white blood cell count
- leukocyte differential percentages
- C-reactive protein (CRP)
- BMI
- waist circumference
- HbA1c

See [`data/README.md`](data/README.md) for additional information about the data files.

## Defining Telomere Discordance

Because both telomere measures are strongly related to age, discordance was constructed after age adjustment.

For each measure:

1. A linear age model was fitted.
2. The age-adjusted residual was calculated.
3. Residuals were standardized using the training-set mean and standard deviation.

The two standardized residuals were then compared:

```text
signed discordance = qPCR_z - DNAm_z

absolute discordance = |signed discordance|
```

A positive signed value indicates that qPCR telomere length is relatively higher than the methylation-derived estimate after age adjustment.

For machine-learning analysis, all age-adjustment and standardization parameters were estimated using the training set only to avoid data leakage.

## Exploratory Findings

Most common clinical variables showed little association with the magnitude of telomere discordance.

Sex and smoking status, however, showed group-level associations with the **direction** of discordance.

For smoking status, the average signed discordance followed the general pattern:

```text
never < former < current
```

This pattern remained visible after examining men and women separately, suggesting that it was not explained solely by differences in sex composition between smoking groups.

However, these group-level differences did not translate into strong individual-level prediction.

## Machine Learning

Three regression approaches were compared:

- Dummy mean predictor
- Ridge regression
- Small multilayer perceptron (MLP)

The data were divided into approximately:

- 64% training
- 16% validation
- 20% test

All preprocessing steps were fitted using training data only, including:

- median imputation for continuous variables
- missing-category imputation for categorical variables
- one-hot encoding
- feature scaling
- age-adjustment models used to construct the targets

The MLP was trained with early stopping and evaluated across multiple random seeds.

## Test Results

| Outcome | Model | Test MAE | Test R² |
| --- | --- | ---: | ---: |
| Signed | Dummy | 0.9518 | -0.0046 |
| Signed | Ridge | 0.9529 | -0.0049 |
| Signed | MLP | 0.9565 | -0.0100 |
| Absolute | Dummy | 0.5921 | -0.0024 |
| Absolute | Ridge | 0.5951 | -0.0148 |
| Absolute | MLP | 0.5991 | -0.0669 |

MLP values represent the mean performance across multiple random seeds.

Neither linear nor nonlinear models produced meaningful improvement over the simple baseline.

## Interpretation

Common demographic and clinical characteristics appear to contain little out-of-sample information for predicting qPCR–DNAm telomere discordance at the individual level.

The analysis suggests an important distinction:

> Group-level statistical associations do not necessarily imply useful individual-level predictive ability.

Sex and smoking status were associated with the direction of discordance in exploratory analyses, but this structure was not strong enough to support accurate prediction for individual participants.

The magnitude of discordance was particularly difficult to predict.

Possible sources of unexplained variation may include measurement noise, more detailed blood-cell composition, genetics, disease or medication history, methylation-specific biology, or other factors not represented in the current feature set.

These possibilities were not directly tested in this project.

## Repository Structure

```text
.
├── data/
│   ├── README.md
│   └── ...
├── notebooks/
│   ├── 01_explore_data.ipynb
│   ├── 02_clinical_features.ipynb
│   └── 03_modeling.ipynb
├── .gitignore
├── .gitattributes
└── README.md
```

### Notebooks

`01_explore_data.ipynb`  
Explores the telomere datasets, constructs initial discordance measures, and examines measurement-related QC variables.

`02_clinical_features.ipynb`  
Merges clinical variables and explores their relationships with signed and absolute discordance.

`03_modeling.ipynb`  
Builds the leakage-safe machine-learning pipeline and evaluates Dummy, Ridge, and MLP models on an independent test set.

## Limitations

- The analysis uses a restricted set of common clinical variables and does not capture all possible biological determinants of telomere measurements.
- `HorvathTelo` is a DNA methylation-derived estimator and should not be interpreted as a direct measurement of telomere length.
- NHANES survey weights were not incorporated into the predictive modeling workflow, so results should not be interpreted as population-level estimates for the U.S. population.
- The analysis is observational and does not establish causal relationships.

## Tools

The project uses Python libraries including:

- pandas
- NumPy
- SciPy
- scikit-learn
- PyTorch
- Matplotlib

## Conclusion

In this NHANES sample, common clinical and demographic variables showed limited ability to predict individual-level disagreement between qPCR-measured and DNA methylation-derived telomere measures.

The clearest result was not the success of a complex predictive model, but rather the consistency of the negative result: increasing model complexity did not meaningfully improve generalization beyond a simple baseline.
