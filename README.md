# HR Attrition Risk Analysis

## Problem Statement
Is attrition concentrated in specific departments/roles, and is it explained by
controllable factors (overtime, pay, tenure) that HR could act on, or by factors
HR can't easily influence?

## Dataset
- Source: [IBM HR Analytics Employee Attrition & Performance](https://www.kaggle.com/datasets/pavansubhasht/ibm-hr-analytics-attrition-dataset)
- 1,470 employee records, 35 features (demographics, compensation, role, satisfaction scores)

## Approach
1. Exploratory Data Analysis — attrition rate broken down by department, role,
   overtime, income, tenure
2. Feature engineering — encoding, leakage checks, class imbalance handling
3. Modeling — Logistic Regression (baseline, interpretable) vs. Random Forest
4. Evaluation — precision/recall/ROC-AUC (accuracy is misleading given ~16% positive class)
5. Power BI dashboard — retention-risk view by department/role

## Key Findings
- **Overtime is the strongest signal in the data**: employees working overtime leave at
  30.5% vs 10.4% for those who don't — a ~3x gap.
- **Income shows a real but weaker gradient**: lowest income quartile attrits at 29.3%
  vs 10.3% for the highest quartile.
- **Attrition concentrates by role, not evenly spread**: Sales Representatives attrit
  at 39.8%, more than double any other role. Research Director sits at just 2.5%.
- **Sales (20.6%) and HR (19.0%) departments run hotter than R&D (13.8%)** — consistent
  with the role-level finding since Sales Reps sit in the Sales department.
- No missing values in the dataset — unusual for "real" data, worth noting explicitly.

## Model Results
| Model | Precision (leavers) | Recall (leavers) | ROC-AUC |
|---|---|---|---|
| Logistic Regression | 0.30 | 0.66 | 0.777 |
| Random Forest | 0.50 | 0.09 | 0.783 |

Top predictors (Logistic Regression coefficients): OverTime (+0.54, strongest),
StockOptionLevel (−0.39), MaritalStatus_Single (+0.32), EnvironmentSatisfaction (−0.31).

## Honest Conclusion
- Both models rank attrition risk almost equally well (ROC-AUC 0.777 LR vs 0.783 RF) —
  the underlying signal in the data is comparable. The real difference is in each
  model's default decision threshold, not its ability to detect risk.
- At default thresholds, Random Forest is too conservative for a retention use case:
  it only flags 4 of 47 actual leavers (9% recall), even though its precision (50%) is
  higher when it does flag someone. Logistic Regression flags more people overall,
  catching 66% of actual leavers (31/47) at the cost of more false positives.
- For a retention tool, recall matters more than precision — missing an at-risk
  employee is more costly than one extra unnecessary conversation with HR. On that
  basis, Logistic Regression is the more useful model as configured, though Random
  Forest's threshold could likely be adjusted (e.g. lowering the classification
  cutoff) to close this gap — a natural next step beyond this analysis.
- What HR can act on: overtime policy and stock option allocation are levers the
  company controls. Marital status is a proxy worth noting, not something to build
  policy around.

## Dashboard
_(screenshot or link to .pbix file in /dashboards)_

## Tools
Python (pandas, scikit-learn) · Power BI · Git/GitHub

## Repo Structure
```
data/raw/        - original dataset
notebooks/       - EDA and modeling notebook
src/             - reusable Python functions
dashboards/      - Power BI file + screenshot
```