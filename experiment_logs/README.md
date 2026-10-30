# Experiment logs

Plain-text records of the experiment runs behind the CIC-IDS 2017 / CSE-CIC-IDS 2018 / UNSW-NB15 / CIC-DDoS2019 analyses: counts, settings, library versions, per-condition results and the checks made against earlier runs.

The per-run result files are in `../results/` (final CIC-IDS 2017 run in `results/final_run_v10/`). Parquet intermediates and prediction dumps are not included in this repository.

| File | Content |
|---|---|
| `table6-dedup-rerun-results.md` | binary-protocol and multiclass reruns after duplicate removal, benign-only detectors, CSE-CIC-IDS 2018 feature-set control and related checks |
| `within-window-ten-windows-results.md` | ten within-window controls, Bot diagnostics, per-cell results |

These two logs record the earlier experiment batch (E1–E7). Where the final run (`results/final_run_v10/`) covers the same quantity, use the final-run files.
