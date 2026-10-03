# Data

This folder contains the public-use NHANES data files used in this project, together with a cleaned analysis dataset.

## Source

Data were obtained from the U.S. National Health and Nutrition Examination Survey (NHANES), from the 1999–2002 survey cycles.

The analysis combines several NHANES components, including:

- qPCR-measured leukocyte telomere length
- DNA methylation-derived telomere estimates (`HorvathTelo`)
- demographics, including age and sex
- complete blood count / leukocyte differential variables
- C-reactive protein (CRP)
- body mass index and waist circumference
- smoking history and current smoking status
- HbA1c

NHANES public-use data are available from the CDC/NCHS website.

## Processed dataset

data_clean is the folder for cleaned and merged datasets used for the downstream exploratory analysis and machine-learning workflow.

Records were merged using the NHANES participant identifier (`SEQN`).

The dataset includes the variables required to construct the study outcomes and the clinical features used as predictors.

The main telomere discordance outcomes are constructed in the notebooks rather than treated as raw NHANES variables.

## Notes

- `HorvathTelo` is a DNA methylation-derived telomere length estimator and should not be interpreted as a direct telomere length measurement.
- The qPCR and methylation-derived telomere measures were age-adjusted using models fitted only on the training data during machine-learning analysis to avoid data leakage.
- These data are used for exploratory and predictive analysis and are not intended for population-level NHANES inference.