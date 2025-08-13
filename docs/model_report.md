# Credit Risk — Model Selection Summary

## Data & Splits
German Credit (1,000 rows). Fixed stratified splits: 60/20/20 (train/valid/test).

## Models Evaluated
- **Baseline Logistic (OHE)**
- **Scorecard Logistic (WOE/IV) + Isotonic calibration**
- **XGBoost (Booster API)** (+ optional isotonic)

## Key Metrics (Test)
- **Scorecard (calibrated)**: AUC ~0.79, **KS ~0.519 (best)**, **Brier ~0.164 (best)**, PR-AUC ~0.59  
- **XGBoost**: AUC ~0.775, KS ~0.445, Brier ~0.174, PR-AUC ~0.598  
- Baseline: similar AUC, lower KS, higher Brier.

## Decision
Choose **Scorecard (calibrated)** as the **Primary** model for its superior separation (KS) and calibrated PD quality, plus clear interpretability. **XGBoost** kept as a **Challenger**.

## Policy (FN:FP = 5:1)
Cost-optimized threshold (from validation) yields ~63–64% flag rate on test; monotonic risk buckets show rising default rates up to ~49% in “High”.

## Repro
```bash
pip install -r requirements.txt
jupyter notebook notebooks/01_eda.ipynb
# run cells through Phase 5
