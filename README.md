# Titanic Survival Analysis

## Problem Statement

What passenger characteristics most strongly predicted survival on the Titanic?

Analyzed using statistical hypothesis testing (chi-square, t-test) and exploratory data visualization across 6 questions.

## Dataset

Kaggle Titanic dataset — 891 passengers, 12 features (train.csv)

## Tools Used

Python, Pandas, Matplotlib, Seaborn, SciPy

## Project Structure

    titanic-survival-analysis/
    ├── data/
    │   └── raw/          ← original train.csv (unmodified)
    ├── notebooks/
    │   └── analysis.ipynb
    ├── visuals/          ← all chart PNGs
    ├── requirements.txt
    └── README.md

## Key Findings

| Question | Finding | Test | Result |
|---|---|---|---|
| Overall survival | 38.38% of passengers survived | — | — |
| Survival by Sex | Females: 74.2% vs Males: 18.9% | Chi-square | χ²=260.72, p≈0.000 |
| Survival by Class | 1st: 63.0% → 2nd: 47.3% → 3rd: 24.2% | Chi-square | χ²=102.89, p≈0.000 |
| Age vs Survival | Survivors avg 2 yrs younger | Two-sample t-test | t=−2.07, p=0.039 |
| Fare vs Survival | Survivor median fare 148% higher (£26 vs £10.50) | Descriptive | — |
| Correlation | Fare & Pclass strongest numeric predictors | Pearson r | r=0.26, r=−0.34 |

**Overall conclusion:** Sex was the strongest predictor of survival, followed by passenger class and fare — both proxies for socioeconomic status. Age had a statistically significant but weaker effect.

## Visuals

| Chart | What it shows |
|---|---|
| `survival_by_sex.png` | Bar chart — survival rate by gender |
| `survival_by_pclass.png` | Bar chart — survival rate by passenger class |
| `age_distribution.png` | Overlapping histograms — age of survivors vs non-survivors |
| `fare_boxplot.png` | Box plot — fare distribution by survival status |
| `correlation_matrix.png` | Heatmap — Pearson correlations between all numeric features |

## Statistical Methods Used

- **Chi-square test of independence** — for categorical variables (Sex, Pclass)
- **Two-sample independent t-test** — for continuous variable comparison (Age)
- **Pearson correlation matrix** — for pairwise relationships between numeric features
- **Descriptive statistics** — median comparison for Fare

## How to Run

    pip install -r requirements.txt

Open `notebooks/analysis.ipynb` and run all cells top to bottom.

## Author

**Abrar Jawad** — EEE Student, BUET
[LinkedIn](https://www.linkedin.com/in/abrar-jawad-09910b206/)

*Project 01 of my Data Analysis learning roadmap — May 2026*
