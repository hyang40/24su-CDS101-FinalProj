# Honey Bee Colony Loss, Carbon Emissions & Tornado Frequency in the U.S. (2015–2021)

State-level analysis linking three public datasets (USDA, U.S. CO₂ emissions, NOAA) to test whether rising carbon emissions and tornado activity are associated with honey bee colony loss.

> CDS 101 · Introduction to Computational and Data Sciences · George Mason University · Summer 2024
> Team project (5 members) · R / tidyverse

---

## Research Question

> Is the increase in honey bee colony loss across U.S. states from 2015 to 2021 associated with higher carbon emissions and more frequent tornado occurrences?

Honey bees are among the most important pollinators for crops and wild plants. This project looks for **external, environmental factors** that move together with colony loss, as a complement to the internal factors (Varroa mites, Nosema, pesticides) usually studied.

## Datasets


| Dataset                            | Source                                  | Variables used                                             |
| ---------------------------------- | --------------------------------------- | ---------------------------------------------------------- |
| **Save the Bees** (2015–2022)      | USDA colony survey, curated on Kaggle   | `state`, `year`, `quarter`, `max_colonies`, `percent_lost` |
| **CO₂ Emissions USA**              | U.S. state-level emissions by fuel type | `year`, `state`, `fuel_name`, `value`                      |
| **US Tornado Dataset** (1950–2021) | NOAA Storm Prediction Center            | `yr`, `st`                                                 |


1. Save the Bees (2015–2022) [https://www.kaggle.com/datasets/m000sey/save-the-honey-bees]
   (https://www.kaggle.com/datasets/m000sey/save-the-honey-bees)
2. CO₂ Emissions USA [https://www.kaggle.com/datasets/abdelrahman16/co2-emissions-usa]
   (https://www.kaggle.com/datasets/abdelrahman16/co2-emissions-usa) 
3. US Tornado Dataset (1950–2021) [https://www.kaggle.com/datasets/danbraswell/us-tornado-dataset-1950-2021]
   (https://www.kaggle.com/datasets/danbraswell/us-tornado-dataset-1950-2021)

All three were aligned to a common **2015–2021** window and a **state × year** grain.

## Analytical Methods

**1. Data integration**

- Reshaped quarterly bee data (`pivot_wider`) and computed yearly averages of max colonies and percent lost, handling a missing quarter in 2019
- Aggregated CO₂ emissions by fuel type (all fuels, natural gas, etc.)
- Counted tornadoes per state-year and filled zero-tornado state-years explicitly (`complete()`)
- Harmonized state names vs. state codes and merged the three sources into analysis tables

**2. Exploratory data analysis**

- Summary statistics (mean, median, SD, min, max) for colonies, emissions and tornado counts
- Faceted bar charts, box plots and violin plots by state and year; scatter plots with linear trends

**3. Hypothesis testing** (simulation-based, `infer`)

- Permutation tests (1,000 reps) on the correlation between colony loss and (a) total emissions, (b) tornado count

**4. Regression modeling**

- Model 1: simple linear regression, `avg_lost_col ~ natural_gas`
- Model 2: multiple linear regression with interaction, `avg_lost_col ~ natural_gas * tornado_count`
- Diagnostics: observed-vs-predicted, residual histogram, Q–Q plot, residuals vs. fitted



## Key Results


| Test / Model                                      | Result                                                                                  |
| ------------------------------------------------- | --------------------------------------------------------------------------------------- |
| Permutation test: colony loss vs. total emissions | r ≈ 0.10, **p = 0.043**                                                                 |
| Permutation test: colony loss vs. tornado count   | r ≈ 0.12, **p = 0.015**                                                                 |
| Model 1: loss ~ natural gas                       | slope = 0.074 (p < 0.001), **R² = 0.235**                                               |
| Model 2: loss ~ natural gas × tornadoes           | **R² = 0.298** (adj. 0.270); natural gas significant, tornado and interaction terms not |


- Both environmental variables show a **statistically significant but weak positive association** with colony loss.
- Natural gas emissions were the strongest single predictor; after controlling for emissions, tornado count added little explanatory power.
- About 70% of the variance in colony loss remains unexplained, which is consistent with internal factors (parasites, disease, pesticides, habitat loss) driving most of it.



## Limitations

- **Association, not causation.** The tests and regressions are observational; they cannot show that emissions or tornadoes cause colony loss.
- **Weak effect sizes.** Correlations around 0.1 are significant mainly because of sample size, not because the relationship is strong.
- **Selection in modeling.** Models were fit on the 11 states where colony loss increased over the period, which may inflate the observed relationship.
- **Coarse grain.** State-year aggregation hides local weather events and within-state variation.



## Repository Contents


| File                                                 | Description                                                                   |
| ---------------------------------------------------- | ----------------------------------------------------------------------------- |
| `HoneyBee_Fin.Rmd`                                   | Final analysis: data integration, EDA, hypothesis tests and regression models |
| `HoneyBee.Rmd`                                       | Data cleaning, merging and exploratory analysis                               |
| `HoneyBee_modeling.Rmd`                              | Regression modeling and diagnostics                                           |
| `CDS 101 final project.pdf`                          | Final presentation slides                                                     |
| `save_the_bees.csv`                                  | USDA honey bee colony data, 2015–2022                                         |
| `emissions.csv`                                      | U.S. state-level CO₂ emissions by fuel type                                   |
| `us_tornado_dataset_1950_2021.csv`                   | NOAA tornado records, 1950–2021                                               |
| `emissions_value.csv` / `.xlsx`, `tornado_count.csv` | Intermediate aggregated tables                                                |




## My Role



## Tech Stack

R · tidyverse (dplyr, tidyr, ggplot2) · infer · modelr · broom · R Markdown
