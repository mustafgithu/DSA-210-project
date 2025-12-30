# DSA-210-project

# Introduction
In this project, I analyze the key factors that drive a country's **frequency of global mentions**. I investigate why some nations dominate the global news cycle and diplomatic reports while others receive little attention, specifically focusing on whether this visibility is driven by "Hard Power" or "Soft Power."

# Project Proposal
This project aims to uncover the underlying determinants of **international visibility**. By integrating large-scale text frequency data with major global indicators, I explore which characteristics—**Economic Wealth (GDP)**, **Military Strength**,**Fragile State Index** or **Democratic Status**—best predict how often a country is reported on by the rest of the world.

# Data Sources
The main source of data is the **Diplomatic text dataset** from GlobalDiplomacyNet project (https://dataverse.harvard.edu/dataset.xhtml?persistentId=doi%3A10.7910/DVN/HYJDE0)

Other sources combined with this dataset:
1) **GDP by country dataset** --> from Our World in Data (https://ourworldindata.org/grapher/gdp-worldbank-constant-usd)
2) **Democracy index by country dataset** --> from kaggle (https://www.kaggle.com/datasets/shreyasur965/democracy-index)
3) **Military Ranking** --> from Global Firepower Index (https://www.globalfirepower.com/countries-listing.php)
   * *Note:* Official website only hosts 2025 data. Historical data (2017-2024) was retrieved from archives to create a robust average:
     * https://www.scribd.com/document/433674720/2019-Military-Strength-Ranking
     * https://www.kaggle.com/datasets/mingookkim/world-military-power-2020
     * https://www.kaggle.com/datasets/sleymanzeynul/military-strengths-of-countries-2021
     * https://www.kaggle.com/datasets/mickaelandrieu/global-fire-power-ranking-2022
     * https://www.kaggle.com/datasets/hanif13/global-firepower-2023
     * https://www.kaggle.com/datasets/chayanonc/military-strength-by-country-2024
4) **Fragile States Index** --> from Fragile States Index (https://fragilestatesindex.org/global-data/)

# Main Hypotheses 
* **H1:** There is a relation between **GDP** of a country and number of mentions of country by other countries.
* **H2:** There is a relation between **Democracy index** of a country and number of mentions of country by other countries.
* **H3:** There is a relation between **Military power** of a country and number of mentions of country by other countries.
* **H4:** There is a relation between **Fragile State Index** of a country and number of mentions of country by other countries. 

# Methodology
## 1. Data Processing
* **News Frequency:** Processed raw `news.jsonl` files, filtering out countries with fewer than **1,000 mentions** to remove noise.
* **Name Normalization:** Manually mapped inconsistent country names (e.g., "Turkey" ↔ "Türkiye", "Ivory Coast" ↔ "Côte d'Ivoire") to ensure accurate merging across datasets.

## 2. Imputation & Transformation
* **Military Data:** Since **Hong Kong** and **Palestine** appear frequently in news but lack sovereign military rankings, their scores were imputed using the dataset's **minimum strength (weakest) value + 10% penalty**.
* **Variable Transformation:** The Global Firepower Index uses a "lower is better" score (0.00 is perfect). For analysis, this was inverted ($1/PowerIndex$) so that higher values represent stronger militaries.

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

# Conclusion
* The analysis supports **H3 (Military)** and **H1 (GDP)** most strongly.
"Hard Power" (Military and Economic strength) drives global news coverage significantly more than "Soft Power" (Democracy).
* Additionally, we found that **Stability** is preferred over Fragility: highly stable, powerful nations receive significantly more attention than fragile, conflict-prone states.
