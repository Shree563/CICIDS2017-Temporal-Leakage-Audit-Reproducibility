# CIC-IDS 2017 Temporal Leakage Audit — Reproducibility Repository

Code, notebooks, and result files for the paper *"Temporal Leakage Bias in CIC-IDS 2017: A Multi-Model Evaluation Audit and Corrective Protocol"* (under review).

## What this repo contains

- Preprocessing and evaluation scripts for a random-split vs. temporal-split comparison on CIC-IDS 2017
- A replication of the same protocol on CIC-IDS 2018
- Statistical significance testing (Wilcoxon signed-rank, paired t-test) and Cliff's Delta effect size across 10 seeds
- All raw result CSVs and the three figures used in the paper

Full methodology, results, and discussion are in the paper — see citation below. This repo is for reproducing the numbers, not for re-explaining them.

## Running the notebooks

1. Download the datasets directly from the official sources (not redistributed here):
   - CIC-IDS 2017: https://www.unb.ca/cic/datasets/ids-2017.html
   - CIC-IDS 2018: https://www.unb.ca/cic/datasets/ids-2018.html

2. Install dependencies:
```bash
   pip install -r requirements.txt
```

3. Run notebooks `01` through `05` in order. Each saves its outputs to `results_csv/`, consumed by later notebooks.

## Notes for anyone re-running this

- Port-number columns (`Destination Port`, `Source Port`) are dropped before scaling — leaving them in destabilises LightGBM specifically. See `02_multiclass_audit.ipynb` for the diagnostic.
- Classes with fewer than 10 real samples are excluded from multiclass evaluation (SMOTE is unreliable at that scale).
- CIC-IDS 2018's numeric columns are sometimes string-typed in the source CSVs — coerce to numeric *before* dropping non-numeric columns, or you'll silently lose real features.

## Citation

```bibtex
@article{[CITEKEY],
  title   = {Temporal Leakage Bias in CIC-IDS 2017: A Multi-Model Evaluation Audit and Corrective Protocol},
}
```

## License

Code: MIT License (see `LICENSE`). CIC-IDS 2017/2018 datasets are governed by UNB's own terms of use, not redistributed here.

## Contact

[Your Name] — [your.email@university.edu]
