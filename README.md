# Poisson_Regression
Teaching Poisson Regression in my Machine Learning class. This Project presentation is meant to show the class how to evaluate data using poisson function

# Poisson Regression: Lecture on Count Data Modeling

A 30‑minute graduate‑level lecture on Poisson regression, developed for CMDA 4654 at Virginia Tech. The lecture covers theory, motivation, baby examples, and a real‑world application predicting cloudy days from weather data.

## Lecture Overview

- **Topic**: Poisson Regression (Generalized Linear Model for count data)
- **Format**: 30+ slide presentation (Beamer, PDF/HTML)
- **Team**: Jason Bruno Terceros, Rishab Desai, Jordan Levy, Nate Williams
- **Date**: April 22, 2025
- **Course**: CMDA 4654 – Statistical/Machine Learning (Prof. Lucero)

## Contents

- What is Poisson regression and why it’s needed
- Poisson distribution (PMF, CDF, visualizations)
- Model formulation: \( \log(\lambda_i) = \beta_0 + \beta_1 x_i \)
- Coefficient interpretation (multiplicative effect on count)
- Baby examples:
  - Horseshoe crab satellites (`crabs` dataset)
  - Insect spray effectiveness (`InsectSprays` dataset)
- Real‑world application: predicting cloudy days per week using capital city weather data
- Poisson regression for rates: offsets and exposure
- Key takeaways and references

## Baby Examples

### 1. Crabs Dataset
Predict the number of male satellites (`Satellites`) attracted to a female horseshoe crab based on her carapace `Width`.  
**Model**: \(\lambda = \exp(-3.30 + 0.164 \cdot \text{Width})\)

### 2. InsectSprays Dataset
Compare the effectiveness of six insect sprays (A–F).  
**Result**: Sprays C, D, and E are most effective (lowest insect counts); boxplot and Poisson regression show significant differences.

## Real‑World Application: Cloudy Days Prediction

We collected daily weather data from **248 capital cities** (August 2023 – present, over 60,000 records).  
**Goal**: Predict the number of “cloudy days” (cloud cover = 100%) in a week.

### Feature Engineering
- Averaged temperature, humidity, pressure, wind speed per week
- Matched each city to its **Koppen climate classification** (e.g., tropical, arid, temperate) to capture long‑term climate patterns without overfitting location

### Model Specification

We fit a Poisson regression model to predict the expected number of cloudy days per week using climate classification and averaged weather variables. The model uses a log link function to ensure non‑negative predictions.

### R Code

```r
model_weekly_pois <- glm(
  CloudyCount ~ 
    kg_group + 
    kg_subgroup1 + 
    avg_humidity + 
    avg_wind_kph + 
    avg_gust_mph,
  data   = weekly_data,
  family = poisson(link = "log")
)
```

## Mathematical Form

\[
\log(\lambda_i) = \beta_0 + \beta_1(\text{kg\_group}_i) + \beta_2(\text{kg\_subgroup1}_i) + \beta_3(\text{avg\_humidity}_i) + \beta_4(\text{avg\_wind\_kph}_i) + \beta_5(\text{avg\_gust\_mph}_i)
\]

where:
- \(\lambda_i\) = expected number of cloudy days in week \(i\)
- `kg_group` = broad Koppen climate group (categorical)
- `kg_subgroup1` = finer climate subtype (categorical)
- `avg_humidity` = weekly average relative humidity (%)
- `avg_wind_kph` = weekly average wind speed (km/h)
- `avg_gust_mph` = weekly average gust speed (mph)

---

## Why Poisson with Log Link?

- The log link guarantees \(\lambda_i > 0\).
- Coefficients represent **log‑rate changes**; exponentiating gives multiplicative effects on the expected count.
- This model assumes equidispersion (mean = variance). We check this via the dispersion ratio (see Results).

---

## Model Output Summary (abridged)
| Coefficients | Estimate | Std. Error | z value | Pr(>\|z\|) |
|--------------|----------|------------|---------|-------------|
| (Intercept)  | -12.58458 | 0.68412    | -18.396 | < 2e-16 *** |
| kg_groupT    | 0.23417   | 0.10987    | 2.131   | 0.03308 *   |
| kg_subgroup1s | -0.92434 | 0.18214    | -5.075  | 3.87e-07 *** |
| kg_subgroup1m | 0.50338  | 0.17865    | 2.818   | 0.00483 **  |
| avg_humidity | 0.03439   | 0.00729    | 4.718   | 2.38e-06 *** |
| avg_wind_kph | 0.12760   | 0.02384    | 5.352   | 8.70e-08 *** |
| avg_gust_mph | -0.13990  | 0.04214    | -3.320  | 0.00090 *** |


# Poisson Regression Lecture – Key Sections

## Results

The real‑world weather application (predicting cloudy days per week from 248 capital cities) produced the following model fit:

| Metric | Value |
|--------|-------|
| Null deviance | 2227.7 on 875 df |
| Residual deviance | 1210.6 on 859 df |
| Explained deviance | **45.7%** |
| Dispersion ratio | 1.23 (slight overdispersion, but model remains valid) |

The model captures nearly half the variation in weekly cloudy‑day counts using climate zone and weather variables.

---

## Coefficient Interpretation (Selected)

Poisson coefficients represent the **log change** in the expected count. Exponentiating gives the multiplicative effect.

| Predictor | Change in Expected Cloudy Days |
|-----------|--------------------------------|
| `avg_humidity` (+1% RH) | +3.5% |
| `avg_wind_kph` (+1 km/h) | +13.6% |
| `avg_gust_mph` (+1 mph) | –13.0% |
| `kg_subgroup1s` (dry‑summer climate) | ~60% fewer cloudy days |
| `kg_subgroup1m` (monsoon climate) | ~66% more cloudy days |

> **Example interpretation:** A 1 km/h increase in average wind speed is associated with a 13.6% increase in the expected number of cloudy days per week, holding other factors constant.

---

## Poisson Regression for Rates (Offset)

When the response is a **rate** (events per unit exposure), we include an offset term:

\[
\log(\lambda_i) = \beta_0 + \beta_1 x_i + \log(\text{exposure}_i)
\]

### Simulated Disease Example

A dataset of 100 regions with varying population sizes (exposure) and pollution levels was created.

- **Naive model** (ignores exposure): produces biased estimates.
- **Offset model** using `offset(log(population))`: correctly estimates the disease rate per capita.

This demonstrates how offsets adjust for different observation windows (time, area, population).

---

## Repository Structure

poisson-regression-lecture/
├── poisson_regression_lecture.Rmd   # R Markdown source (Beamer)
├── poisson_regression_lecture.pdf   # Rendered slides (PDF)
├── poisson_regression_lecture.html  # Rendered slides (HTML)
├── README.md                        # This file
├── img/
    ├── clouds.jpg                   # Images used in slides
    ├── image.png
    ├── koppenmap.jpg                # Images used in slides
    └── vt_logo.png                  # Virginia Tech logo for slides
└── data/
    ├── weekly_weather_data.csv      # Aggregated weekly data (example)
    └── capital_cities_weather.csv   # Raw daily data (if shareable)

