# Temporal Leakage Bias in CIC-IDS 2017: A Multi-Model Evaluation Audit

Reproducibility artifact for the paper *"Temporal Leakage Bias in CIC-IDS 2017: A Multi-Model Evaluation Audit and Corrective Protocol"* (under review).

## Summary

This repository contains the complete preprocessing pipeline, evaluation notebooks, and result files supporting a statistically validated, multi-model, multi-dataset audit of the **Temporal Leakage Bias** — the systematic performance inflation that occurs when network intrusion detection datasets with temporally segregated attack classes are evaluated using random train-test splits instead of temporally faithful splits.

**Key findings:**
- Random splitting inflates F1-macro by 34–63 percentage points and attack recall by 63–98 percentage points on CIC-IDS 2017, across Random Forest, LightGBM, XGBoost, and a multilayer perceptron (Wilcoxon signed-rank p = 0.002 for all nine model–metric pairs, Cliff's δ = 1.000).
- Replication on CIC-IDS 2018 shows the bias not only generalises but intensifies: temporal-split attack recall collapses to exactly 0.0000 across all three tree-based models.
- A formal theoretical treatment (Section 4.5 of the paper) proves random splitting leaks with near-certainty for any day-exclusive attack class of practical size, and establishes a structural upper bound on achievable temporal-split performance.

## Repository Structure
