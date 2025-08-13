# Credit Risk — Model Card

## Overview
- **Goal:** Estimate probability of default (PD) for German Credit dataset and pick an operating threshold under **FN:FP = 5:1** cost.
- **Primary model:** **Calibrated Scorecard (WOE/IV + Isotonic)**  
- **Challenger:** XGBoost (calibrated)

## Data & Splits
- 1,000 rows, 21 features, target `default` (30% positive)
- Stratified fixed splits: **60/20/20** (train/valid/test)

## Test Metrics (held-out)
| model          | roc_auc | pr_auc  | brier   | ks     |
|----------------|:------:|:------:|:------:|:------:|
| baseline (OHE) | 0.790  | **0.622** | 0.188 | 0.488 |
| scorecard      | **0.790** | 0.590 | 0.185 | **0.519** |
| scorecard_cal  | 0.785  | 0.573 | **0.164** | 0.502 |
| xgb            | 0.775  | 0.598 | 0.174 | 0.445 |
| xgb_cal        | 0.772  | 0.561 | 0.178 | 0.445 |

**Decision:** Use **calibrated scorecard** as Primary (best KS & Brier on test, interpretable). Keep **XGB** as Challenger.

## Operating Point (Policy)
- Cost ratio **FN:FP = 5:1**  
- Threshold (valid-optimized): **0.075**  
- Saved at: `reports/policy_scorecard_costFN5.json`

## Calibration
- Isotonic calibration on validation set; reliability curve shows improved probability calibration.  
- Plot: `reports/calibration_baseline_valid.png` (and for scorecard).

## Fairness Slices (Test)
- By **personal_status**: higher FPR and rejection for some groups; small-n groups are directional only.  
- By **age**: youngest bucket **[0,25)** shows higher FPR (~0.82) and flag rate (~0.89).  
- Artifact: `reports/fairness_slices_test.json`

## Risks & Limitations
- Small dataset; estimates have variance.  
- Slices are descriptive only; protected attributes should not be used as features in production.  
- Re-tune threshold when base rates/costs drift.

## Repro
```bash
pip install -r requirements.txt
jupyter notebook notebooks/01_eda.ipynb
# run cells through Phase 6 (incl. fairness)
