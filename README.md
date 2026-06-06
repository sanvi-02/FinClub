# Yield Curve Reconstruction via the Cox-Ingersoll-Ross (CIR) Model

## Project Overview

This project reconstructs the term structure of interest rates using the **Cox-Ingersoll-Ross (CIR) short-rate model**. Given only the **3-Month zero-coupon yield**, the model estimates yields across multiple maturities, including 6-Month, 9-Month, 1-Year, and 2-Year tenors.

The model is calibrated on historical yield curve data spanning nine maturity buckets and evaluated on an out-of-sample test set. Performance is measured using standard regression metrics and compared against both a CIR++ extension and a baseline linear regression model.

This work was completed as part of the **Finance Club Quantitative Finance Problem Set**.

---

## Objectives

* Perform exploratory analysis of historical zero-coupon yield data.
* Calibrate CIR parameters ((\kappa), (\theta), (\sigma)) using full-curve information.
* Reconstruct out-of-sample yields from a single observable input (3-Month yield).
* Evaluate predictive performance using R², RMSE, and MAE.
* Compare the base CIR model with a CIR++ extension.
* Benchmark performance against a linear regression approach.

---

## Dataset

The dataset contains historical daily zero-coupon yields across nine maturities:

| Column   | Maturity |
| -------- | -------- |
| ZC025YR  | 3 Months |
| ZC050YR  | 6 Months |
| ZC075YR  | 9 Months |
| ZC100YR  | 1 Year   |
| ZC200YR  | 2 Years  |
| ZC500YR  | 5 Years  |
| ZC1000YR | 10 Years |
| ZC2000YR | 20 Years |
| ZC3000YR | 30 Years |

### Files

* `train_data.csv` – Training dataset (1,976 observations)
* `test_data.csv` – Ground-truth test dataset
* `test_data_3M.csv` – Test dataset containing only 3-Month yields

---

## Data Quality Assessment

The following checks were performed:

* Missing value detection
* Duplicate record identification
* Data-type verification
* Outlier inspection using boxplots

No significant data-quality issues were identified.

---

## Exploratory Data Analysis

The exploratory analysis included:

* Cross-maturity correlation heatmap
* Short-rate time-series analysis
* Yield co-movement visualization across maturities
* Distribution analysis of the 3-Month yield
* Yield curve snapshots across selected dates

Results indicated that a dominant common factor explains most yield curve variation, supporting the use of the short rate as the primary state variable.

---

## Cox-Ingersoll-Ross (CIR) Model

The short-rate process follows:

[dr_t = \kappa(\theta - r_t),dt + \sigma\sqrt{r_t},dW_t]

Where:

| Parameter | Description               |
| --------- | ------------------------- |
| κ         | Mean-reversion speed      |
| θ         | Long-run equilibrium rate |
| σ         | Volatility coefficient    |

To ensure non-negative interest rates, the **Feller condition** is enforced:

[
2\kappa\theta \geq \sigma^2
]

Zero-coupon bond prices are computed using the analytical CIR closed-form solution and converted into continuously compounded yields.

---

## Model Calibration

Parameters were estimated by minimizing a weighted mean-squared error across maturities using the **L-BFGS-B** optimization algorithm.

Higher weights were assigned to maturities up to 2 Years to align with the evaluation objective.

### Calibrated Parameters

| Parameter | Value  |
| --------- | ------ |
| κ         | 0.1725 |
| θ         | 0.0253 |
| σ         | 0.0319 |

The calibrated model satisfies the Feller condition.
---

## CIR++ Extension

A deterministic maturity-specific shift (s(\tau)) was introduced:

[y_{CIR++}(\tau)=y_{CIR}(\tau)+s(\tau)]

where (s(\tau)) represents the average in-sample spread between each maturity and the 3-Month yield.

While this adjustment improved in-sample fit, it did not improve out-of-sample predictive performance due to time-varying spread dynamics.

---

## Results

### CIR Model – Maturity-Level R²

| Maturity | R²     |
| -------- | ------ |
| 6 Months | 0.9944 |
| 9 Months | 0.9675 |
| 1 Year   | 0.9104 |
| 2 Years  | 0.3965 |

### Aggregate Performance

[
R^2 = 0.8939
]

The model exceeded the project benchmark of **0.85 R²**.

---

## Model Comparison

| Maturity | CIR R² |
| -------- | ------ |
| 6 Months | 0.9956 |
| 9 Months | 0.9785 |
| 1 Year   | 0.9114 |
| 2 Years  | 0.4065 |

The CIR framework provides both strong predictive performance and economically interpretable parameters, making it preferable to purely statistical alternatives.

---

## Key Findings

* Full-curve calibration significantly improved predictive accuracy.
* The CIR model accurately captures the level and slope of the yield curve at shorter maturities.
* Predictive accuracy declines for longer horizons, consistent with the limitations of single-factor interest-rate models.
* The CIR++ constant-shift extension did not outperform the base CIR model.
* The calibrated CIR model provided the best balance between interpretability and predictive performance.

---

## Repository Structure

```text
.
├── YieldCurve_CIR.ipynb
├── README.md
└── data/
    ├── train_data.csv
    ├── test_data.csv
    └── test_data_3M.csv
```

---

## Dependencies

```bash
pip install pandas numpy scipy scikit-learn matplotlib seaborn
```

### Required Libraries

* Python 3.x
* pandas
* NumPy
* SciPy
* scikit-learn
* Matplotlib
* Seaborn

---

## References

1. Cox, J. C., Ingersoll, J. E., & Ross, S. A. (1985). *A Theory of the Term Structure of Interest Rates*. Econometrica, 53(2), 385–407.
2. Brigo, D., & Mercurio, F. (2006). *Interest Rate Models: Theory and Practice*. Springer.
3. Longstaff, F. A., & Schwartz, E. S. (1992). *Interest Rate Volatility and the Term Structure: A Two-Factor General Equilibrium Model*. Journal of Finance, 47(4), 1259–1282.

