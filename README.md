This project applies **conformal prediction** techniques to the California housing dataset to explore and approximate **conditional coverage**, going beyond standard marginal prediction intervals. The goal is to demonstrate how interval calibration behaves across subgroups of the input space, and to test whether we can maintain reliable coverage guarantees under various conditions.

The project was realised in collaboration with @Leo Paggen. (Data Science Masters Student)
There are not may availbale applications of conditional coverage so we decided to come up with this work, applying also the **studentized residuals**, making our more distingusbale. 

---

##  Background

Conformal prediction offers a distribution-free way to construct **prediction intervals** with a user-defined coverage level (e.g., 90%). However, these guarantees are  **marginal**, meaning that the 90% coverage is valid on average over the data distribution—not necessarily for every subpopulation or region of the input space.

This project demonstrates:
- The difference between **marginal** and **conditional coverage**
- How to approximate conditional coverage using **subgroup-based conditioning**
- The value of **studentized residuals** for adapting to prediction difficulty so basically accouting for **variance in the prediction**. 

---

## Dataset: California Housing

We use the built-in California Housing dataset from `sklearn`, which is a cleaned and numerical-only dataset, similar to the Boston dataset in R. The target variable is the **median house value** (in $100,000s), capped at 5.0 due to original data collection constraints.

- **Target (y)**: Median house value
- **Features (X)**: Housing and demographic variables such as `AveRooms`, `Population`, etc.

---

## Methodology

### 1. **Exchangeability Check**
Conformal prediction assumes the data is exchangeable (i.e., no time order). We shuffle the data and split it into training and calibration sets, then verify that both sets retain similar distributions.

### 2. **Residual-Based Interval Estimation**
Two types of residuals are used:
- **Absolute residuals**: Traditional method.
- **Studentized residuals**: Scales the error by an estimate of its standard deviation (adaptive to "local" uncertainty).

### 3. **Prediction Pipeline**
- Train a regression model (e.g., LinearRegression)
- Fit a secondary model to predict residual spread
- Calibrate conformal prediction intervals on the calibration set
- Construct intervals on test data using both methods

---

## Marginal vs Conditional Coverage

### Marginal Coverage
We first evaluate whether the prediction intervals capture **90% of the true values** on average across the test set.

### Conditional Coverage (Approximation)
Since exact conditional coverage is theoretically impossible (shown by Lei and Wasserman, 2014), we **approximate** it by conditioning on **feature bins**:

#### Conditioned on:
- **AveRooms**: Split into 3 bins (low, medium, high average rooms)
- **Population**: Split into 5 quantile bins

We measure:
- The coverage rate within each bin
- Deviations from the desired 90% coverage

---

## Results

- Marginal coverage is reliably close to 90% using both residual methods.
- Conditional coverage approximation shows **slight under-coverage** in some bins (especially for medium `AveRooms` and low `Population`).
- Studentized intervals better adapt to areas with higher uncertainty and prediction hardness.

---

## Key Insights

- Standard conformal intervals **do not guarantee** per-group or per-instance validity. This is really cool to show in actual real practice examples, as based on the thoery, those guarantees would have a higer probability. 
- **Studentized residuals** provide more flexible interval construction by adjusting for local error variance.
