# A/B Testing — Retail Malls (Istanbul)

## Project overview

This project implements a reproducible A/B testing workflow that evaluates the effect of increasing the **Average Order Value (AOV)** in retail malls. Using a transactional dataset of customer purchases across 10 shopping malls in Istanbul (2021–2023), the notebook performs data cleaning, exploratory analysis, principled group assignment (using Jensen–Shannon divergence), power/sample-size reasoning, hierarchical bootstrap simulations, and end-to-end A/A and A/B experiments (simulation-based) to produce business-relevant conclusions.

## Dataset

* **Source:** Kaggle — [Customer Shopping Dataset - Retail Sales Data](https://www.kaggle.com/datasets/mehmettahiraslan/customer-shopping-dataset).
* **Coverage:** Transactions across 10 shopping malls in Istanbul, 2021–2023.

## Business hypothesis & metric

* **Hypothesis:** A new pricing model will increase the Average Order Value (AOV).
* **Primary metric:** AOV — average total amount per transaction (daily aggregation and per-store aggregation where appropriate).
* **Decision rule:** If the test group’s AOV is statistically significantly higher (95% CI excludes zero and effect direction positive), recommend rollout.

## What was done

1. **Data cleaning & filtering**

   * Split data into *historical* and *future* windows: last 6 months were treated as future (simulated experiment window).

2. **Exploratory data analysis**

   * Distribution of daily AOV across all malls.
   * Relative breakdowns by `gender`, `category`, `payment_method`, age distributions and per-mall transaction counts.

3. **Group assignment (balanced by distribution)**

   * Method: compute per-store daily AOV distributions, enumerate possible splits and **minimize Jensen–Shannon (JS) divergence** between the two groups’ aggregated daily-AOV distributions.

4. **Analytical power/sample-size reasoning**

   * Calculated between-store variance of the per-store daily-AOV metric and derived the classic required number of stores formula:
   $$n = \frac{2\,(Z_{1-\alpha/2} + Z_{1-\beta})^2\,\sigma^2}{\Delta^2}$$
   * Also derived required days given intra-store transaction variance, arriving at a large analytic minimal duration.

5. **Empirical hierarchical bootstrap**

   * Implemented cluster-aware bootstrap (resampling stores and days within stores) to estimate the sampling distribution of the group A–B difference.
   * Simulated a 7% uplift in AOV in the test group to find the minimal number of days needed to detect that effect.

6. **A/A validation**

   * Conducted an A/A bootstrap test for 150 days to verify the method is well-calibrated (95% CI contains zero).

7. **A/B simulation**

   * Applied randomized price perturbation to simulate new pricing model in test group (randomly between −2% and +12% per transaction) and ran the hierarchical bootstrap over the experiment window.

## Key methods & statistics

* **Metric aggregation:** daily per-store Average Order Value (AOV).
* **Group balancing:** Jensen–Shannon divergence between aggregated group distributions of daily AOV.
* **Outlier handling:** exclude transaction amounts above the 99th percentile when estimating intra-store variance.
* **Analytical calculations:** variance of per-store metrics and sample-size formulas for required number of stores and days.
* **Empirical inference:** hierarchical (clustered) bootstrap that resamples stores with replacement and days-within-store with replacement; used to compute empirical distribution of the effect and 95% CI.
* **Validation:** A/A test to ensure calibration; A/B bootstrap for final inference.

## Technologies & libraries

* Language / environment: **Python**, **Jupyter Notebook**
* Data processing: **pandas**, **numpy**
* Visualization: **matplotlib**, **seaborn**
* Statistics: **scipy** (for entropy / divergence)
* Utilities: `kagglehub` (used in notebook to download the dataset from Kaggle)
