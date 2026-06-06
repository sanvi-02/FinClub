# Yield Curve Reconstruction using the Cox-Ingersoll-Ross (CIR) Model

## Overview

This project implements and evaluates the **Cox-Ingersoll-Ross (CIR) interest rate model** for yield curve reconstruction using historical zero-coupon yield data.

The objective is to calibrate the CIR model using historical yield observations and reconstruct the yield curve using only the observed **3-Month yield** as a proxy for the short rate. Model performance is evaluated on out-of-sample data using standard regression metrics.

The project was completed as part of the **Finance Club Quantitative Finance Problem Statement**.

---

## Objectives

- Perform exploratory analysis of historical yield curve data.
- Calibrate the CIR model parameters:
  - κ (speed of mean reversion)
  - θ (long-run mean rate)
  - σ (volatility)
- Reconstruct the yield curve from the short rate.
- Evaluate predictive performance using out-of-sample testing.
- Explore the CIR++ extension and compare results.
- Benchmark CIR against a simple Linear Regression model.

---

## Dataset

The dataset consists of historical zero-coupon yields across multiple maturities:

| Column | Maturity |
|----------|----------|
| ZC025YR | 3 Months |
| ZC050YR | 6 Months |
| ZC075YR | 9 Months |
| ZC100YR | 1 Year |
| ZC200YR | 2 Years |
| ZC500YR | 5 Years |
| ZC1000YR | 10 Years |
| ZC2000YR | 20 Years |
| ZC3000YR | 30 Years |

The training dataset contains historical observations used for calibration, while the test dataset is used for out-of-sample evaluation.

---

## Methodology

### 1. Data Cleaning

The dataset was inspected for:

- Missing values
- Duplicate observations
- Data type consistency
- Outliers

No missing values or duplicate records were found.

---

### 2. Exploratory Data Analysis

The following analyses were performed:

- Summary statistics
- Correlation analysis
- Yield curve evolution across maturities
- Interest rate regime identification
- Yield curve snapshots
- Rolling correlation analysis

These analyses confirmed strong relationships across maturities and supported the use of the 3-Month yield as a short-rate proxy.

---

### 3. CIR Model

The short rate follows the CIR stochastic differential equation:

\[
dr_t = \kappa(\theta-r_t)dt + \sigma\sqrt{r_t}dW_t
\]

where:

- κ = speed of mean reversion
- θ = long-run mean rate
- σ = volatility
- \(W_t\) = standard Brownian motion

The model satisfies the Feller condition:

\[
2\kappa\theta \ge \sigma^2
\]

which ensures positivity of interest rates.

---

### 4. Calibration

Instead of calibrating only to the short-rate series, the model was calibrated using the full yield curve from:

- 6 Months
- 9 Months
- 1 Year
- 2 Years
- 5 Years
- 10 Years
- 20 Years
- 30 Years

A weighted mean squared error objective function was minimized using the **L-BFGS-B** optimization algorithm while enforcing positivity constraints and the Feller condition.

---

### 5. Yield Curve Reconstruction

Using the calibrated parameters and the observed 3-Month yield:

1. Zero-coupon bond prices were generated using the CIR bond pricing formula.
2. Bond prices were converted into continuously compounded yields.
3. Predicted yields were compared against actual market observations.

---

### 6. CIR++ Extension

A deterministic-shift CIR++ model was implemented:

\[
y^{CIR++}(T)=y^{CIR}(T)+s(T)
\]

where \(s(T)\) is a maturity-specific deterministic shift estimated from historical yield spreads.

The extension was evaluated but did not outperform the calibrated CIR model.

---

## Results

### CIR Model Performance

| Maturity | R² |
|-----------|-----------|
| 6 Months | 0.9944 |
| 9 Months | 0.9675 |
| 1 Year | 0.9104 |
| 2 Years | 0.3965 |

### Overall Performance

\[
\boxed{R^2 = 0.8939}
\]

The model exceeded the project benchmark of **0.85**, demonstrating strong predictive performance.

---

## Model Comparison

The CIR model was compared against a simple Linear Regression benchmark.

The CIR framework outperformed Linear Regression at shorter maturities while providing economically meaningful interpretations through:

- Mean reversion
- Stochastic interest rate dynamics
- Positivity constraints

---

## Key Findings

- Full-curve calibration significantly improved performance.
- The CIR model successfully captured the level and slope of the yield curve.
- Performance was strongest for shorter maturities.
- The deterministic CIR++ extension did not improve predictive accuracy.
- The calibrated CIR model achieved the best balance between predictive performance and financial interpretability.

---

## Repository Structure

```text
.
├── FinClubPS1.ipynb
├── README.md
├── data/
│   ├── train_data.csv
│   ├── test_data.csv
│   └── test_data_3M.csv
└── figures/
```

---

## Technologies Used

- Python
- NumPy
- Pandas
- Matplotlib
- Seaborn
- SciPy
- Scikit-Learn
- Google Colab

---

## References

1. Cox, J. C., Ingersoll, J. E., & Ross, S. A. (1985).
   *A Theory of the Term Structure of Interest Rates.*
   Econometrica, 53(2), 385–407.

2. Brigo, D., & Mercurio, F. (2006).
   *Interest Rate Models – Theory and Practice.*

3. Longstaff, F. A., & Schwartz, E. S. (1992).
   *Interest Rate Volatility and the Term Structure: A Two-Factor General Equilibrium Model.*

---

## Author

**Grivann Patwa**  
Civil Engineering, IIT Roorkee  
Finance Club Quantitative Finance Project
