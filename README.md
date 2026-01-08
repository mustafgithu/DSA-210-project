# DSA-210-project

# Introduction
In this project, I analyze the key factors that drive a country's **frequency of global mentions**. I investigate why some nations dominate the global news cycle and diplomatic reports while others receive little attention, specifically focusing on whether this visibility is driven by "Hard Power" or "Soft Power."

# Project Proposal
This project aims to uncover the underlying determinants of **international visibility**. By integrating large-scale text frequency data with major global indicators, I explore which characteristics—**Economic Wealth (GDP)**, **Military Strength**, **Fragile State Index** or **Democratic Status**—best predict how often a country is reported on by the rest of the world.

# Data Sources
The main source of data is the **Diplomatic text dataset** from GlobalDiplomacyNet project (https://dataverse.harvard.edu/dataset.xhtml?persistentId=doi%3A10.7910/DVN/HYJDE0)

Other sources combined with this dataset:
1) **GDP by country dataset** --> from Our World in Data (https://ourworldindata.org/grapher/gdp-worldbank-constant-usd) (average of last available 10 years of a country)
2) **Democracy index by country dataset** --> from kaggle (https://www.kaggle.com/datasets/shreyasur965/democracy-index)
3) **Military Ranking** --> from Global Firepower Index (https://www.globalfirepower.com/countries-listing.php)
   * *Note:* Official website only hosts 2025 data. Historical data (2017-2024) was retrieved from archives to create a robust average:
     * https://www.scribd.com/document/433674720/2019-Military-Strength-Ranking
     * https://www.kaggle.com/datasets/mingookkim/world-military-power-2020
     * https://www.kaggle.com/datasets/sleymanzeynul/military-strengths-of-countries-2021
     * https://www.kaggle.com/datasets/mickaelandrieu/global-fire-power-ranking-2022
     * https://www.kaggle.com/datasets/hanif13/global-firepower-2023
     * https://www.kaggle.com/datasets/chayanonc/military-strength-by-country-2024
4) **Fragile States Index** --> from Fragile States Index (https://fragilestatesindex.org/global-data/) (average of last  10 years )

# Main Hypotheses 
* **H1:** There is a relation between **GDP** of a country and number of mentions of country by other countries.
* **H2:** There is a relation between **Democracy index** of a country and number of mentions of country by other countries.
* **H3:** There is a relation between **Military power** of a country and number of mentions of country by other countries.
* **H4:** There is a relation between **Fragile State Index** of a country and number of mentions of country by other countries. 

# Methodology
## 1. Data Processing
* **News Frequency:** Processed raw `news.jsonl` files, filtering out countries with fewer than **1,000 mentions** to remove noise.
* **Name Normalization:** Manually mapped inconsistent country names (e.g., "Turkey" ↔ "Türkiye", "Ivory Coast" ↔ "Côte d'Ivoire") to ensure accurate merging across datasets.

## 2. Data Cleaning & Imputation
To ensure a comprehensive analysis, we addressed two main challenges in the raw data: **Naming Inconsistencies** and **Missing Values** for key geopolitical entities.

### A. Naming Harmonization
Country names were standardized across all 5 datasets to match the master "News Frequency" list.
* **Examples:** "Turkey" $\rightarrow$ "Türkiye", "Ivory Coast" $\rightarrow$ "Côte d'Ivoire", "Russian Federation" $\rightarrow$ "Russia".

### B. Strategic Imputation
Standard international datasets (like the World Bank or UN) often exclude non-member entities like **Taiwan**, **Hong Kong**, and **Kosovo**. However, these are high-visibility actors in global news.

Instead of dropping them (which would bias the "News Frequency" analysis), we imputed their missing values using reasoned estimates from regional peers and alternative reports:

* **GDP:** Imputed for **Taiwan** using official national statistical data (since it is excluded from World Bank data).
* **Democracy Index:** Imputed for **Hong Kong** and small island nations using the average scores of their respective regions.
* **Fragile States Index (FSI):** Imputed for **Taiwan**, **Hong Kong**, and **Kosovo** by referencing the scores of comparable regional peers (e.g., South Korea, Singapore, and Balkan neighbors).
* **Military Power:** Imputed for entities missing from the GlobalFirePower index by referencing similar-sized military powers in their region.

# Analysis & Results
## 1. Visual Analysis
Below are the key visualizations illustrating the relationship between national indicators and global news coverage.

### A. Economic Power (GDP) vs. Attention
![GDP vs Frequency](images/gdp_visualization_output.png)
> **Figure 1:** The relationship between GDP and News Frequency (Log-Log Scale). There is a clear, strong linear trend ($r \approx 0.61$), indicating that a country's economic size is a primary driver of its global visibility.

### B. Military Strength vs. Attention
![Military vs Frequency](images/military_visualization_output.png)
> **Figure 2:** Military Power vs. News Frequency. This indicator demonstrated the **strongest correlation** ($r \approx 0.65$) of all variables tested. The "Hard Power" bias is evident, as nations with higher military capabilities dominate the news cycle.

### C. State Fragility (FSI) vs. Attention
![FSI vs Frequency](images/fsi_visualization_output.png)
> **Figure 3:** Fragile States Index vs. News Frequency. Contrary to the popular "if it bleeds, it leads" assumption, our data shows a **negative correlation** ($r \approx -0.33$). Highly fragile, unstable states often receive *less* coverage than stable, powerful nations, unless they are strategically important.

### D. Democracy vs. Attention
![Democracy vs Frequency](images/democracy_visualization_output.png)
> **Figure 4:** Democracy Index vs. News Frequency. While there is a slight preference for democratic nations, the relationship is weak ($r \approx 0.29$) and highly scattered compared to economic and military indicators.

## 2. Hypothesis Testing
To rigorously determine the drivers of news coverage, we performed two types of statistical tests.

### A. Spearman Rank Correlation
We used Spearman's rank correlation (instead of Pearson) to handle extreme outliers like the USA and China.
* **Military Power:** Strongest correlation ($r \approx 0.65$, $p < 0.001$).
* **GDP:** Strong correlation ($r \approx 0.61$, $p < 0.001$).
* **Democracy:** Weak correlation ($r \approx 0.29$).
* **FSI:** Strong correlation ($r \approx -0.338$, $p < 0.001$).

### B. Two-Sample T-Test
We split countries into "High" (Top 50%) and "Low" (Bottom 50%) groups based on the median of each variable to see if the means differed significantly.
* **Military Power:** The "Strong Military" group receives **2.5x more news mentions** than the weak group ($p < 0.001$).
* **GDP:** The "High GDP" group receives significantly more mentions than the low group ($p < 0.001$).
* **Democracy:** The "High Democracy" group receives more mentions than the low group, but the difference is much smaller compared to Military and GDP.
* **FSI:** No significant difference between the groups


# 3. Machine Learning Results & Analysis

We trained three distinct models to predict a country's **News Frequency** based on its national indicators (GDP, Military Power, Democracy, Fragility).

### A. Feature Correlation
First, we analyzed how our features correlate with the target variable (`Log_Frequency`).
![Correlation Matrix](images/correlation_matrix.png)
* **Key Finding:** Economic Power and Military Strength show the strongest positive correlation (> 0.67), suggesting they are the primary drivers of attention.

---

### B. Model Performance Comparison (LOOCV)

We evaluated models using **Leave-One-Out Cross-Validation (LOOCV)** to ensure robust testing on our dataset of ~150 countries.

| Model | RMSE (Error) 📉 | $R^2$ Score 📈 | Interpretation |
| :--- | :--- | :--- | :--- |
| **Linear Regression** | **0.669** | **0.462** |  **Best Model.** Successfully captures the direct "Power Law" relationship. |
| **Random Forest** | 0.710 | 0.390 | **Overfitting.** The model complexity outweighed the signal in the small dataset. |
| **Gradient Boosting** | 0.780 | 0.270 | **Poor Fit.** Struggled to generalize; highest error rate. |

---

### C. Visualizing the Predictions

Below are the **Actual vs. Predicted** plots for each model. A perfect model would align all blue dots on the red diagonal line.

####  Model A: Linear Regression (The Winner)
![Linear Regression Results](images/linear_regression_results.png)
* **Observation:** The linear model follows the diagonal trend reasonably well, explaining why it has the highest accuracy.

####  Model B: Random Forest
![Random Forest Results](images/random_forest_results.png)
* **Observation:** While it captures the general trend, the points are more scattered compared to the linear model, indicating higher variance (overfitting).

####  Model C: Gradient Boosting
![Gradient Boosting Results](images/gradient_boosting_results.png)
* **Observation:** This model shows significant scattering, proving it is too complex for this specific sample size.

---

##  Final Verdict: Linear Regression

The **Linear Regression** model is the clear winner for this project.

**Why did the simplest model win?**
1.  **The "Power Law" Reality:** Global news attention follows a power law (a few countries get *massive* attention, most get little). After we applied log-transformations, this relationship became linear.
2.  **Sample Size:** With only ~150 data points (countries), complex models like Gradient Boosting tend to "memorize" noise rather than learning general rules.
3.  **Conclusion:** A country's visibility in the global news cycle is primarily determined by a straightforward, linear combination of its **Hard Power** (GDP and Military Strength).

# 4. Final Conclusion

By combining statistical hypothesis testing with machine learning models, we have reached a robust conclusion about what drives global visibility.

### A. Hard Power is the Primary Driver
Both our statistical tests and our ML feature analysis agree: **Hard Power dominates.**
* **Evidence:** In hypothesis testing, countries with high **GDP** and **Military Strength** received significantly more news mentions ($p < 0.001$).
* **Confirmation:** The Machine Learning correlation matrix showed that GDP and Military Strength had the strongest positive correlations (> 0.67) with news frequency, far outweighing Democracy or Fragility.

### B. The "Power Law" of Attention
Our Machine Learning experiment revealed the *nature* of this relationship.
* **Linear Model Win:** The fact that **Linear Regression** outperformed complex models (like Gradient Boosting) proves that the relationship is direct and proportional.
* **Interpretation:** We observed a clear **Power Law**. As a nation's "Hard Power" increases exponentially, its global news coverage increases exponentially. There are no complex, hidden non-linear rules—it is a straightforward hierarchy of power.

### C. Stability Over Fragility
Contrary to the belief that "bad news travels fast," our data suggests that **Stability** attracts more consistent global attention than Fragility.
* The **Fragile State Index (FSI)** had a negative correlation with news frequency.
* Global diplomacy and news cycles prioritize powerful, stable actors over fragile states, likely due to their central role in the global economy and security architecture.

###  Summary
If a country wants to increase its global "share of voice," **Economic Wealth (GDP)** and **Military Power** are the decisive factors. Democratic status and internal fragility play a minor secondary role compared to the sheer weight of Hard Power.

### ⚠️ Limitations & Technical Note
While our analysis provides strong evidence for the dominance of Hard Power, we identified significant **multicollinearity** between our two main predictors:
* **GDP and Military Power** share a correlation of **0.87**.
* **Implication:** This indicates that economically wealthy nations almost universally possess strong military capabilities. While we retained both features to test distinct theoretical hypotheses ("Economic Power" vs. "Military Power"), their high overlap means the linear regression model may struggle to statistically disentangle their individual contributions. The "weight" assigned to one versus the other should be interpreted with this redundancy in mind.
