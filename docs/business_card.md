# Business Framing (Phase 1) — Template

- **Problem**: Estimate probability of default (PD) for consumer credit.
- **Target**: `default` (binary), horizon: (document from dataset).
- **KPIs**: ROC-AUC, PR-AUC, KS, calibration (Brier/ECE). Secondary: profit curve.
- **Decision Policy**: threshold(s) based on FN vs FP costs; risk buckets.
- **Fairness**: evaluate demographic parity / equalized odds gaps by group(s).
- **Risks**: label delay, leakage, sample bias, drift.