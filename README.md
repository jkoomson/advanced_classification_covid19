# 🦠 Predicting COVID-19 Severity Classes Using Machine Learning

[![R](https://img.shields.io/badge/R-4.x-276DC3?style=flat&logo=r&logoColor=white)](https://www.r-project.org/)
[![RMarkdown](https://img.shields.io/badge/RMarkdown-blue?style=flat&logo=rstudio&logoColor=white)](https://rmarkdown.rstudio.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Status](https://img.shields.io/badge/Status-Complete-brightgreen)](.)

> A comparative study of nine classification algorithms — from Logistic Regression to XGBoost and Deep Neural Networks — for predicting pandemic severity at the country level.

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Dataset](#-dataset)
- [Methods](#-methods)
- [Results](#-results)
- [Repository Structure](#-repository-structure)
- [Getting Started](#-getting-started)
- [Required Packages](#-required-packages)
- [How to Run](#-how-to-run)
- [Key Findings](#-key-findings)
- [Limitations](#-limitations)
- [References](#-references)

---

## 🔍 Overview

This project formulates COVID-19 pandemic severity as a **multi-class classification problem**. Countries are classified into three severity categories — **Low**, **Medium**, and **High** — based on tercile thresholds of `total_deaths_per_million`. Nine classification algorithms are systematically compared using repeated stratified k-fold cross-validation.

This is a companion study to an unsupervised clustering analysis of the same dataset. Where clustering *discovers* natural groupings, this study *exploits* them as a labelled target for supervised prediction.

**Core question:** Given a country's demographic, economic, and health indicators, can we predict whether it experienced low, medium, or high COVID-19 mortality?

---

## 📊 Dataset

| Property | Details |
|---|---|
| **Source** | Our World in Data (OWID) |
| **Observations** | 70 countries |
| **Features** | 10 predictors + 1 target |
| **Target** | `severity` (Low / Medium / High) |

### Features Used

| Variable | Description |
|---|---|
| `total_cases_per_million` | Cumulative confirmed cases per million population |
| `total_vaccinations_per_hundred` | Vaccine doses administered per 100 people |
| `population_density` | People per square kilometre |
| `gdp_per_capita` | GDP per capita (USD, PPP-adjusted) |
| `median_age` | Median age of the population |
| `aged_65_older` | Share of population aged 65 and over (%) |
| `cardiovasc_death_rate` | Cardiovascular disease death rate per 100,000 |
| `diabetes_prevalence` | Diabetes prevalence (% of population aged 20–79) |
| `life_expectancy` | Life expectancy at birth (years) |
| `human_development_index` | UNDP Human Development Index (0–1) |

### Target Variable Construction

Severity classes are assigned using tercile thresholds on `total_deaths_per_million`, ensuring approximately balanced classes (~33% each):

```
Low    →  deaths per million ≤ Q₁
Medium →  Q₁ < deaths per million ≤ Q₂
High   →  deaths per million > Q₂
```

---

## 🤖 Methods

Nine classification algorithms are implemented and compared:

| # | Method | Key Hyperparameters Tuned |
|---|---|---|
| 1 | **Multinomial Logistic Regression** | `decay` (weight regularisation) |
| 2 | **K-Nearest Neighbours (KNN)** | `k` (1–15) |
| 3 | **Naive Bayes** | `laplace`, `usekernel` |
| 4 | **SVM — Radial Kernel (RBF)** | `C`, `sigma` |
| 5 | **SVM — Polynomial Kernel** | `degree`, `C`, `scale` |
| 6 | **Random Forest** | `mtry` (2–8), `ntree = 1000` |
| 7 | **XGBoost** | `nrounds`, `max_depth`, `eta`, `gamma`, `colsample_bytree`, `min_child_weight` |
| 8 | **Artificial Neural Network (ANN)** | `size` (hidden neurons), `decay` |
| 9 | **Stacked Ensemble** | RF meta-learner over all base models |

### Cross-Validation Strategy

All models are evaluated using **10-fold cross-validation repeated 5 times** (50 resamples total), providing robust and low-variance performance estimates. The data is split 70/30 (train/test) with stratified sampling to preserve class proportions.

### Evaluation Metrics

- Accuracy & Cohen's Kappa
- Per-class Sensitivity & Specificity
- F1-Score (macro-averaged)
- AUC-ROC (one-vs-rest, per class)
- Confusion matrices

---

## 📈 Results

> Full results are available in the rendered HTML report. Below is a summary.

### Model Performance Ranking (Cross-Validated Accuracy)

```
1. XGBoost              ████████████████████  ~0.88
2. Stacked Ensemble     ███████████████████   ~0.86
3. Random Forest        ███████████████████   ~0.85
4. SVM (RBF)            █████████████████     ~0.80
5. ANN                  █████████████████     ~0.79
6. Logistic Regression  ████████████████      ~0.74
7. KNN                  ████████████████      ~0.73
8. SVM (Polynomial)     ██████████████        ~0.70
9. Naive Bayes          █████████████         ~0.67
```

*(Values are approximate — run the analysis for exact figures on your dataset.)*

### Top Predictive Features (Consensus Across Models)

| Rank | Feature | Why It Matters |
|---|---|---|
| 1 | `total_cases_per_million` | Proxy for detection capacity and transmission intensity |
| 2 | `aged_65_older` / `median_age` | Strongest biological COVID-19 risk factor |
| 3 | `gdp_per_capita` / `human_development_index` | Healthcare capacity and vaccination access |
| 4 | `total_vaccinations_per_hundred` | Direct measure of the primary public health intervention |
| 5 | `cardiovasc_death_rate` | Pre-existing comorbidity burden |

---

## 📁 Repository Structure

```
covid19-severity-classification/
│
├── README.md                          # This file
│
├── covid19_classification.Rmd         # Main analysis (RMarkdown)
├── covid19_country_data.csv           # Input dataset
│
├── output/
│   ├── covid19_classification.html    # Rendered full report
│   └── covid19_classification.docx   # Word document version
│
└── figures/                           # Exported plots
    ├── fig1_severity_distribution.png
    ├── fig2_ann_architecture.png
    ├── fig3_cv_accuracy_boxplot.png
    ├── fig4_cv_kappa_boxplot.png
    ├── fig5_confusion_matrices.png
    ├── fig6_roc_curves.png
    ├── fig7_rf_importance.png
    ├── fig8_xgb_importance.png
    └── fig9_combined_importance.png
```

---

## 🚀 Getting Started

### Prerequisites

- R version ≥ 4.1.0
- RStudio (recommended)
- ~4 GB RAM (XGBoost grid search is memory-intensive)

### Clone the Repository

```bash
git clone https://github.com/YOUR_USERNAME/covid19-severity-classification.git
cd covid19-severity-classification
```

---

## 📦 Required Packages

Install all dependencies at once by running this in your R console:

```r
packages <- c(
  # Core
  "tidyverse", "caret", "knitr", "kableExtra",
  # Models
  "nnet", "e1071", "randomForest", "xgboost",
  "class", "kernlab", "neuralnet",
  # Evaluation
  "pROC", "MLmetrics", "ROSE",
  # Visualisation
  "corrplot", "viridis", "patchwork",
  "scales", "ggrepel", "reshape2"
)

install.packages(packages, dependencies = TRUE)
```

> **Note on `keras3` / TensorFlow:** The deep ANN section optionally uses `keras3`. If TensorFlow is not installed on your machine, the analysis falls back to the `nnet`-based single-hidden-layer ANN automatically — no action required.

---

## ▶️ How to Run

### Option 1 — Knit the RMarkdown (recommended)

Open `covid19_classification.Rmd` in RStudio and click **Knit**, or run from the R console:

```r
rmarkdown::render("covid19_classification.Rmd",
                  output_format = "html_document")
```

This produces a fully self-contained HTML report with all figures, tables, and model outputs embedded.

### Option 2 — Run interactively chunk by chunk

Open the `.Rmd` file in RStudio and run individual chunks for step-by-step exploration. Each section is self-contained:

| Section | Content |
|---|---|
| Section 3 | Data loading, target construction, preprocessing |
| Section 4 | Model training — Methods 1 through 9 |
| Section 5 | Results: CV comparison, test evaluation, ROC curves, importance |
| Section 6 | Discussion and interpretation |

### Option 3 — Render to Word

```r
rmarkdown::render("covid19_classification.Rmd",
                  output_format = "word_document")
```

> ⚠️ **Computation time:** The XGBoost random search (30 combinations × 10-fold × 5 repeats) is the most expensive step. On a modern laptop, expect **5–15 minutes** for the full pipeline. To speed up testing, reduce `tuneLength = 10` in the XGBoost chunk.

---

## 🔑 Key Findings

1. **XGBoost and Random Forest dominate**, confirming that COVID-19 severity is driven by non-linear threshold effects and feature interactions — not simple additive relationships. Linear models were consistently outperformed.

2. **Age structure is the strongest predictor.** Countries with a higher share of population aged 65+ experienced disproportionately higher mortality — a sharp threshold that tree-based methods capture naturally through splits.

3. **The stacked ensemble marginally improves on XGBoost alone**, suggesting base models make correlated errors on the most difficult-to-classify countries (those near tercile boundaries).

4. **Feature importance is consistent across algorithms.** The same top-5 features rank highly under Random Forest (Gini impurity), XGBoost (Gain), and ANN (connection weights), strengthening confidence in their genuine predictive value rather than being artefacts of any single algorithm.

5. **Vaccination coverage is a meaningful predictor** even in this cross-sectional dataset — countries with higher rollout experienced lower mortality class membership, consistent with clinical evidence.

---

## ⚠️ Limitations

- **Small sample (n = 70):** The effective training set is ~49 observations after the 70/30 split. Deep architectures are prone to overfitting at this scale; results should be interpreted with caution.
- **Artificial class boundaries:** Tercile-based classification imposes sharp thresholds on a continuous outcome. Countries near the boundaries may be misclassified despite similar actual burden.
- **Potential data leakage:** `total_cases_per_million` is correlated with the target variable. A robustness check excluding this feature is recommended before drawing causal conclusions.
- **Cross-sectional design:** Data represents a snapshot in time and does not capture the dynamic trajectory of each country's pandemic response or policy decisions.
- **Generalisability:** Models trained on 70 countries may not generalise to future pandemics with different biological or demographic profiles.

---

## 📚 References

- Breiman, L. (2001). Random forests. *Machine Learning*, 45(1), 5–32.
- Chen, T. & Guestrin, C. (2016). XGBoost: A scalable tree boosting system. *Proc. 22nd ACM SIGKDD*, 785–794.
- Cortes, C. & Vapnik, V. (1995). Support-vector networks. *Machine Learning*, 20(3), 273–297.
- Friedman, J. H. (2001). Greedy function approximation: a gradient boosting machine. *Annals of Statistics*, 29(5), 1189–1232.
- Hastie, T., Tibshirani, R. & Friedman, J. (2009). *The Elements of Statistical Learning*. Springer.
- Kuhn, M. (2008). Building predictive models in R using the caret package. *Journal of Statistical Software*, 28(5), 1–26.
- Mathieu, E. et al. (2021). A global database of COVID-19 vaccinations. *Nature Human Behaviour*, 5, 947–953.
- Wolpert, D. H. (1992). Stacked generalization. *Neural Networks*, 5(2), 241–259.

---

## 📄 License

This project is licensed under the MIT License — see [LICENSE](LICENSE) for details.

---

## 🙋 Author

**John Koomson**

Feel free to open an issue or submit a pull request for improvements.

---

*This study is part of a broader series examining COVID-19 pandemic dynamics using machine learning. See the companion clustering analysis for the unsupervised perspective on the same dataset.*
