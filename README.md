# credit-risk-scorecard

Credit risk modeling project with a production-style workflow: fixed data splits, a baseline logistic model (OHE), a **scorecard model (WOE/IV + isotonic calibration)**, and an **XGBoost challenger**.  
**Dataset:** German Credit (1,000 rows). Target: `default` (1 = default).

## Project Phases
1. Business framing → `docs/business_card.md`  
2. EDA + feature schema + fixed splits (60/20/20)  
3. Baseline logistic (OHE) + validation/test metrics  
4. Scorecard (WOE/IV), calibration, risk buckets, and cost-based policy  
5. Challenger model (XGBoost) + side-by-side comparison  
6. Final model selection → `docs/model_report.md`

## How to run
```bash
pip install -r requirements.txt
jupyter notebook notebooks/01_eda.ipynb
# run cells through Phase 5 (scorecard + challenger)
```

## Results (test)
| model              |  roc\_auc  |   pr\_auc  |    brier   |     ks     |
| ------------------ | :--------: | :--------: | :--------: | :--------: |
| baseline           |   0.7901   | **0.6217** |   0.1878   |   0.4881   |
| scorecard          | **0.7900** |   0.5903   |   0.1853   | **0.5190** |
| **scorecard\_cal** |   0.7845   |   0.5728   | **0.1637** |   0.5024   |
| xgb                |   0.7750   |   0.5977   |   0.1736   |   0.4452   |
| xgb\_cal           |   0.7723   |   0.5613   |   0.1778   |   0.4452   |

# Decision: 
Use calibrated scorecard as the primary model (best KS and Brier on test; interpretable). Keep XGBoost as the challenger.
Full summary: docs/model_report.md.

## Risk buckets (test, calibrated scorecard)
Monotonic default rates with “High” bucket ≈49% default; details in reports/risk_buckets_test.csv.

## Repo structure
```bash
data/raw/                 # german_credit.csv
docs/                     # business_card.md, model_report.md
models/                   # baseline_logreg.pkl, scorecard_logreg.pkl, xgb_challenger.pkl
notebooks/01_eda.ipynb    # all phases in one notebook
reports/                  # metrics_*.json, calibration plots, binning.json, iv_table.csv, policy_*.json
splits/                   # train_idx.csv, valid_idx.csv, test_idx.csv
src/                      # (optional) helpers if extracted from notebook
```

```bash
If you want, add a tiny `.gitignore` (root of repo) so notebook scratch folders don’t get committed:

```bash
printf "notebooks/.ipynb_checkpoints/\nnotebooks/data/\nnotebooks/models/\nnotebooks/reports/\n" >> .gitignore
git add .gitignore
git commit -m "chore: ignore transient notebook artifacts"
git push
```