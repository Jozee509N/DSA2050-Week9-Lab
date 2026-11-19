# DSA 2050A — Week 9: Model selection and defensible evaluation

**Group name/number:** [Existing course group]  
**Members and student IDs:** [List all group members]  
**Submission:** One shared GitHub repository link per group  
**Course:** DSA 2050A Data Science Methodology  
**Week:** 9

## Group responsibilities
Complete the lab **in your existing groups**. Work together on one notebook and one repository. Record each member's concrete contribution in the table below. Each member should contribute visibly through Git commits or documented co-authored work.

| Group member | Student ID | Work completed | GitHub username or commit reference |
|---|---|---|---|
| [Name] | [ID] | [Example: evaluation pipeline] | [Username] |
| Veronicah Mwichigi | 673730 | Model Evaluation and Limitations | Veronicahmwichigi |
|John Lyomoki | 673396 | Part C and D | lyosticles-github |
| Joseph Uthasyo | 674146 | Setup and repo | Jozee509N |
| [Name] | [ID] | [Example: error analysis and README] | [Username] |
| [Name] | [ID] | [Work completed] | [Username] |

## Study objective
Explain the customer churn decision and why model selection must use development data only.

## Data statement
Synthetic fictional customer data created for teaching. No real customer records.

## Setup and reproduction
1. Open this folder in VS Code.
2. Create/select a Python environment (3.10–3.13 recommended).
3. In **Terminal → New Terminal**, run `python -m pip install -r requirements.txt`.
4. Open `notebooks/Week9_Model_Selection_Lab.ipynb` and **Select Kernel**.
5. Run each cell in order. Complete the rationale before the final-test cell.

## Prediction-time features and excluded fields
- Target: `churn` (1 = left; 0 = stayed).
- Excluded: `customer_id` (identifier), `cancellation_confirmed` (post-outcome leakage).
- Allowed features: [Explain]

## Experiments and model selection
- Primary metric chosen before comparison: **F1**.
- CV: **5-fold stratified, shuffle=True, seed=42**.
| Model | Best setting | Mean F1 | F1 std | Fold 1 | Fold 2 | Fold 3 | Fold 4 | Fold 5 | Mean precision | Mean recall |
|---|---|---|---|---|---|---|---|---|---|---|
| Logistic regression | C = 1 | 0.470 | 0.053 | 0.391 | 0.534 | 0.472 | 0.434 | 0.519 | 0.624 | 0.378 |
| Decision tree | max_depth = 3 | 0.398 | 0.049 | 0.392 | 0.457 | 0.316 | 0.437 | 0.391 | 0.566 | 0.318 |

- Candidate comparison table from `reports/experiment_log.csv`:

| Model | Settings | Mean F1 | F1 std | Mean precision | Mean recall |
|---|---|---|---|---|---|
| Logistic regression | C = 0.1 | 0.465 | 0.054 | 0.625 | 0.371 |
| Logistic regression | C = 1 | 0.470 | 0.053 | 0.624 | 0.378 |
| Logistic regression | C = 10 | 0.470 | 0.053 | 0.624 | 0.378 |
| Decision tree | max_depth = 3 | 0.398 | 0.049 | 0.566 | 0.318 |
| Decision tree | max_depth = 5 | 0.382 | 0.023 | 0.583 | 0.287 |
| Decision tree | max_depth = 8 | 0.372 | 0.051 | 0.495 | 0.300 |

Baseline (majority-class dummy): mean accuracy 0.665, mean F1 0.000.

- **Chosen model:** Logistic regression
- **Tuned hyperparameters:** C = 1 (`model__C = 1`)
- **Mean/SD CV F1:** 0.470 / 0.053
- **Why this choice:** We chose logistic regression (C = 1) because it had the highest mean F1 under the same 5-fold stratified CV used for every candidate (0.470 vs 0.398 for the best decision tree, max_depth = 3). The 0.072 gap is larger than the fold standard deviation of either model (0.053 and 0.049), so it is unlikely to be noise, and the two models had similar variability. Logistic regression also had higher mean recall (0.378 vs 0.318) and higher mean precision (0.624 vs 0.566), so it caught more churners with a better share of correct alerts. All candidates beat the majority-class baseline, which looked good on accuracy (0.665) but scored 0.0 F1 because it never predicts churn. The depth-5 tree was the most stable (std 0.023), but its mean F1 was lower (0.382), so we did not trade performance for stability. Logistic regression is also simpler and easier to explain. C = 1 and C = 10 tied, so we kept C = 1, the more regularised setting.

## Error and subgroup analysis

1. Development Out-of-Fold Confusion Matrix

Actual / Predicted| Stay| Churn
Stay| 1,605| 205
Churn| 566| 344

The model correctly classified 1,605 customers who stayed (true negatives) and 344 customers who churned (true positives). It also produced 205 false positives and 566 false negatives.

2. False Positives and False Negatives

- False Positives (205): These are customers predicted to churn but who actually stayed. This means the model may incorrectly identify customers for retention interventions.
- False Negatives (566): These are customers predicted to stay but who actually churned. This means the model failed to identify these customers as potential churners, resulting in missed opportunities for intervention.

3. New vs Established Customer Subgroup Analysis

The subgroup analysis was conducted to determine whether churn recall is worse for new customers than established customers.

Metric| New Customers| Established Customers
Subgroup sample size| 452| 2,268
Actual churners| 261| 649
Recall| 78.16%| 21.57%

The results show that recall is considerably lower for established customers (21.57%) than for new customers (78.16%). Therefore, the model identifies actual churners more effectively among new customers than established customers.

For new customers, the model identifies approximately 78 out of every 100 actual churners, compared to only 22 out of every 100 among established customers.

These findings should be interpreted cautiously because the subgroup sizes differ considerably. The established customer subgroup contains 2,268 customers, while the new customer subgroup contains 452. The observed difference in recall does not necessarily explain why the model performs differently across the two groups.

## Permutation importance
- Top two features and approximate importance values.
- Explain why association is not causation.

## Final test (run once only)
- F1: [Fill] ; precision: [Fill] ; recall: [Fill]
- Compare CV vs test and discuss whether the difference is concerning.

## Limitations and ethics
- Provide at least two realistic limitations (dataset is synthetic; hidden customer dynamics; temporal drift, etc.).
- Note monitoring / human review needs before real use.

## Evidence checklist
- [ ] Executed notebook with explanations
- [ ] `reports/experiment_log.csv`
- [ ] `reports/subgroup_metrics.csv`
- [ ] `reports/permutation_importance.csv`
- [ ] `reports/frozen_selection.json` created before test
- [ ] `reports/final_metrics.json` generated once
- [ ] Two generated PNG charts
- [ ] Meaningful commits or co-authorship evidence for all group members
- [ ] Completed group contribution table with names and student IDs

## Final group review
- [ ] One team member verified the notebook runs from start to finish
- [ ] Group discussed and approved the written model-selection rationale
- [ ] No one used final-test scores to revise the model
- [ ] One representative will submit the shared URL to the course LMS

## GitHub repository URL
[Paste URL here]
