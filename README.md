# Benchmark validity in ML-based intrusion detection: temporal leakage versus unseen-attack emergence

Code, result files and run logs for the manuscript *"Benchmark Validity in ML-Based Intrusion Detection: Temporal Leakage versus Unseen-Attack Emergence under Chronological Evaluation"* (under review).

The repository holds the experiment notebooks and the per-run result CSVs behind the reported numbers. It does not hold the raw datasets or the manuscript.

## What the study measures

Intrusion-detection benchmarks are usually scored with a random train/test split. Under a chronological split the score drops sharply, and two different effects can cause the drop:

- **Temporal leakage:** under the random split, flows from the same capture window appear on both sides of the split, so the score is inflated.
- **Unseen-attack emergence:** under the chronological split, attack families that first appear in the test window are absent from training and cannot be learned.

The two effects are separated with the **novel-class fraction ρ**, the share of test-window attack flows that belong to families absent from training. For a closed-set multiclass classifier, accuracy cannot exceed the **ceiling 1 − ρ**, because every flow of an unseen family is necessarily an error. On CIC-IDS 2017 the Friday test window has ρ = 0.411 (ceiling 0.589) with no duplicate removal, 0.370 (0.630) under D1 and 0.275 (0.725) under D2.

Duplicate handling is a controlled variable, defined with pandas `duplicated(keep='first')` on float64 values in capture order:

| Condition | Rows treated as duplicates |
|---|---|
| D0 | none removed (as captured) |
| D1 | identical on all 78 features including Destination Port, plus label (primary definition) |
| D2 | identical on the 77 model features excluding Destination Port, plus label (sensitivity analysis) |

## Main results (CIC-IDS 2017, final run)

All numbers below can be recomputed from the CSVs in `results/final_run_v10/`. Seeds are 42–51 unless stated; intervals are 95% t-intervals over seeds.

- **Collapse under chronological evaluation.** F1-macro (random minus chronological) falls by 55.8 (Random Forest), 60.8 (LightGBM), 58.6 (XGBoost) and 37.6 (MLP) percentage points under D0. The gap is positive in 10 of 10 seeds for every model under D0, D1 and D2.
- **The ceiling holds.** Across 90 multiclass runs (3 tree models × 3 conditions × 10 seeds) no accuracy exceeds 1 − ρ. On average LightGBM and XGBoost stay within 0.15 percentage points of it in every condition (Random Forest within 1.7).
- **Capacity does not rescue the models.** In a 16-configuration capacity sweep (5 seeds) the normalised loss stays between 0.88 and 1.00, except for Random Forest at depth 6 (0.62), which underfits the random split.
- **Within-window leakage is small.** Ten single-attack windows give a random-minus-chronological gap of +0.76 (D0), +0.96 (D1) and +1.43 (D2) percentage points at an 80/20 cut. No cell survives Holm correction. The gap depends strongly on the cut point (window-level means from +0.7 to +9 points, individual cells up to +43), and it is never comparable to the collapse above.
- **Destination Port is a mixed effect.** Including it raises chronological F1 and recall in 10 of 10 seeds for all three tree models. It lowers chronological ROC-AUC for LightGBM and XGBoost but not significantly for Random Forest.
- **Benign-only detectors.** Isolation Forest stays within about 0.5 points of the alert-nothing F1 floor on Friday. The autoencoder gains only through DDoS recall.
- **Proximity is necessary but not sufficient.** Bot flows are never nearer to a seen attack than to benign traffic and are never detected. PortScan flows are nearer to a seen attack in about a third of cases (seed-dependent, 0.002 to 0.476) yet are detected in about 0.1%.
- **CSE-CIC-IDS 2018.** Chronological attack recall is at most 0.05 in every configuration (exactly 0.00 for Random Forest in the original configuration), with the F1-macro gap at 49–53 points. Equalising the feature sets between the two protocols changes the gap by at most about 3.3 points.

## Repository layout

```
notebooks/            experiment notebooks (see below)
results/
  final_run_v10/      per-run results of the final CIC-IDS 2017 run (26 files)
  cic2017_dedup/      earlier duplicate-definition reruns of the binary protocol
  cic2018/            CSE-CIC-IDS 2018 feature-set control, ten seeds
  ddos2019/           CIC-DDoS2019 results
  unsw_nb15/          UNSW-NB15 results
  *.csv               earlier audit outputs (binary audit, significance tests, Cliff's delta, 2018 replication)
experiment_logs/      plain-text logs of the later experiments
figures/              figures
requirements.txt        environment of the final run
requirements-legacy.txt environment of the earlier notebooks 01-05
```

### `results/final_run_v10/`

The final CIC-IDS 2017 run, produced in one notebook with one pinned environment (`environment.txt`) and one shared pipeline for sampling, filtering, standardisation, SMOTE and model fitting. Destination Port is excluded from the model features except in the explicit port check.

| Files | Content |
|---|---|
| `step0_mask_*.csv`, `feature_columns.json` | data checks, duplicate counts per condition and day, per-class counts, ρ and ceiling |
| `step1_runs.csv`, `step1_features.csv`, `step1_table6_D0/D1/D2.csv` | binary random-versus-chronological runs (RF, LightGBM, XGBoost, MLP; D0/D1/D2; 10 seeds) and the per-condition gap tables with Wilcoxon and Holm results |
| `step1_port_check.csv`, `step4_port_*.csv` | Destination Port check (seed 42, then ten seeds) |
| `step2_runs.csv` | multiclass chronological accuracy against the ceiling (90 runs) |
| `step3_runs.csv`, `step3_table18.csv` | capacity sweep (D0, seeds 42–46) |
| `step5a_*.csv` | ten within-window controls, duplicates removed within each block |
| `step5b_cut_sweep_runs.csv` | cut-point sweep of the within-window controls (seeds 42–44) |
| `step6_proximity_runs.csv` | Friday proximity analysis (nearest seen attack versus nearest benign) |
| `step7_benign_*.csv` | benign-only detectors (Isolation Forest, autoencoder) and the benign-shift control |
| `step8_table10_per_class.csv` | per-class precision and recall of the multiclass random-split run (seed 42) |
| `step9_classweight_runs.csv` | unweighted rows of the class-weighting control (the balanced rows are in `step1_runs.csv`) |
| `step10_closer_*.csv` | nearest seen attack family for flows that are closer to an attack than to benign traffic |
| `environment.txt` | full `pip freeze` and Python version of the run |

### Other result folders

- `results/cic2017_dedup/` — binary protocol rerun with duplicates removed (earlier runs; superseded by `step1_runs.csv` for the reported numbers).
- `results/cic2018/cic2018_e4_feature_confound_runs.csv` — three feature-set configurations (`orig`, `week1_fixed`, `pooled_fixed`) × random/temporal × RF/LightGBM/XGBoost × seeds 42–51 (180 runs).
- `results/ddos2019/` and `results/unsw_nb15/` — per-run results for CIC-DDoS2019 and UNSW-NB15 (multiclass ceiling, single-family hold-outs, SMOTE and family-removal controls). Column names are in each file's header.
- Top-level `results/*.csv` — outputs of the earlier audit (binary audit, significance tests, Cliff's δ, 2018 replication). Where the final run covers the same CIC-IDS 2017 quantity, the final-run files are the ones to use; small numeric differences between the two are expected because they come from different code paths.

## Notebooks

| Notebook | Content |
|---|---|
| `01_binary_audit` – `05_cicids2018_replication` | earlier audit: binary and multiclass audit, significance tests, Cliff's δ, CSE-CIC-IDS 2018 replication (`requirements-legacy.txt`) |
| `06_final_run_v10` | final run: duplicate masks, Steps 0 and 2–6 (the Step 1 cell is not included, see Known gaps), the CIC-DDoS2019 and UNSW-NB15 experiments, the benign-only baseline and the later controls (`requirements.txt`) |

The notebooks were written for Google Colab and read data from Google Drive (`/content/drive/MyDrive/...`). Adjust the path constants at the top of each notebook to run them elsewhere. Cell outputs are kept so that the printed results can be compared without rerunning.

## Known gaps

Four result groups are included as produced, but the notebook cells that generated them were overwritten or not carried over, and are **not** in `06_final_run_v10`:

- `step1_runs.csv`, `step1_features.csv`, `step1_port_check.csv` and `step1_table6_D0/D1/D2.csv` — the binary random-versus-chronological runs. Step 3 and the ten-seed port check reuse this pipeline (their cells assert agreement with the Step 1 rows), but the Step 1 cell itself is not in the notebook.

- `step8_table10_per_class.csv` — the multiclass random-split run. Its LightGBM rows come from a configuration that diverged (balanced class weights together with SMOTE gave accuracy 0.073). They are kept as a documented diagnostic and are not a reportable result.
- `step9_classweight_runs.csv` — the unweighted class-weighting control.
- `step10_closer_perseed.csv` and `step10_closer_nearest_family.csv` — the nearest-attack-family analysis. It follows the Step 6 proximity analysis (same feature space and reference rule, query subsets drawn with a different random state).

In addition, the Bot-window diagnostics (`cic2017_bot_diagnostics_runs.csv`, cells "CELL 38" and "CELL 39" of the notebook) were produced with helper functions defined in an earlier cell ("CELL 37") that no longer exists in the notebook. Their outputs are kept in the notebook and the numbers in the manuscript's Bot-window paragraph come from them, but those two cells cannot be rerun on their own.

These files can be checked against the logs but not regenerated from this repository. In `step8_table10_per_class.csv` the class names `Web Attack – Brute Force` and `Web Attack – XSS` appear with a replacement character because the source label contains a non-UTF-8 dash.

The parquet intermediates (cleaned data, duplicate masks) and the per-run prediction dumps are too large for the repository and are not included.

## Data sources

The raw datasets are not redistributed. Download them from their owners and keep the original per-day file order (the chronological protocols depend on it):

- CIC-IDS 2017 — Canadian Institute for Cybersecurity, University of New Brunswick: https://www.unb.ca/cic/datasets/ids-2017.html
- CSE-CIC-IDS 2018 — Canadian Institute for Cybersecurity: https://www.unb.ca/cic/datasets/ids-2018.html
- CIC-DDoS2019 — Canadian Institute for Cybersecurity: https://www.unb.ca/cic/datasets/ddos-2019.html
- UNSW-NB15 — UNSW Canberra Cyber: https://research.unsw.edu.au/projects/unsw-nb15-dataset

Please cite the original dataset papers when using them.

## License

Code and result files are released under the MIT License (`LICENSE`). The datasets keep their own licences.

## Contact

Shree Kantesh K H, JSS Science and Technology University, Mysuru.
