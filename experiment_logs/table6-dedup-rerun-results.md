# CELL 41: Table 6 rerun, exact duplicates removed before sampling/splitting (main pipeline, 10 seeds 42-51)
File: Drive UNSW-NB15/cic2017_table6_dedup_runs.csv. Finished 2026-10-26. (21 of 60 run-rows were already present when the cell started; resumed, same code.)
Duplicates (identical features excl. ports + label, first kept): 628,663 of 2,827,876 rows = 22.2%. By day: Mon 13.3%, Tue 15.0%, Wed 18.9%, Thu 28.5%, Fri 32.6%.
After removal: Mon-Thu 1,725,748 rows; Friday 473,465 rows (attack share 0.278): BENIGN 342,058, DDoS 128,014, PortScan 1,956, Bot 1,437.
rho (Friday classes absent from Mon-Thu) = 0.2775 -> ceiling 0.7225 (was rho 0.411, ceiling 0.589 before removal). PortScan drops almost entirely (heavily duplicated).
Gap random - temporal F1-macro (pp), 95% t-CI df 9:
RF 48.56 [44.62, 52.50] (random .9945, temporal .5089 sd .0550) ; before 54.38
LGBM 56.29 [54.60, 57.98] (random .9971, temporal .4343 sd .0238) ; before 60.75
XGB 50.29 [45.29, 55.28] (random .9979, temporal .4950 sd .0699) ; before 56.76
Reduction 5.8 / 4.5 / 6.5 pp; gap stays 48.6-56.3 pp.

# CELL 42: Table 20 after duplicate removal (multiclass, train Mon-Thu, test Friday, 10 seeds, no oversampling)
File: Drive UNSW-NB15/cic_multiclass_temporal_dedup_results.csv. Finished 2026-10-26.
rho 0.2775, ceiling 0.7225 (before: 0.411 / 0.589). Friday after removal: BENIGN 342,058, DDoS 128,014, PortScan 1,956, Bot 1,437.
RF accuracy 0.7155 slack 0.0070; LightGBM 0.7222 slack 0.0002; XGBoost 0.7210 slack 0.0014; violations 0 of 10 each.
(Before: RF 0.5726, LGBM 0.5888, XGB 0.5886 vs ceiling 0.589.) Ceiling still met to within 0.02-0.7 pp.

# CELL 43: benign-only detectors (trained on benign flows of training partition only; threshold = 99th pct of training benign scores; 10 seeds; same samples as Table 6)
File: Drive UNSW-NB15/cic2017_benign_only_runs.csv. Finished 2026-10-26. F1-macro random vs temporal, gap pp [95% CI df9]; ROC-AUC rnd/tmp; recall rnd/tmp; FPR rnd/tmp
captured IsolationForest: .4998/.3755 gap +12.43 [10.76,14.10]; AUC .733/.646; recall .060/.011; FPR .010/.030
captured Autoencoder:     .7275/.5487 gap +17.88 [17.24,18.52]; AUC .837/.774; recall .377/.218; FPR .010/.051
dedup    IsolationForest: .5130/.4338 gap  +7.92 [4.92,10.92];  AUC .859/.747; recall .063/.023; FPR .010/.033
dedup    Autoencoder:     .8440/.7137 gap +13.03 [12.36,13.70]; AUC .924/.854; recall .605/.438; FPR .010/.056

# E1 (2026-10-26): dedup count verification (no training). Files: Drive UNSW-NB15/e1_dedup_counts.csv. Libraries: pandas 2.2.3, numpy 2.1.3, scipy 1.16.3, sklearn 1.6.1
Variants: M = exact replication of CELL 41 (float64 cols cast to float32, int cols unchanged; ports excl) ; A = all cols float32 ; B = float64, ports excl ; C = float64, Destination Port INCLUDED
Removed: M 628,663 (22.2%) = paper; A 628,872; B 594,712 (21.0%); C 307,078 (10.9%).
Per-day dup share (M): Mon 13.3, Tue 15.0, Wed 18.9, Thu 28.5, Fri 32.6 (paper OK). B: 13.3/12.6/17.1/27.2/32.0. C: 5.1/5.4/12.9/13.1/15.2.
Friday (before; M; A; B; C): BENIGN 413,933; 342,058; 342,045; 346,384; 375,204 | PortScan 158,804; 1,956; 1,956; 1,956; 90,694 | DDoS 128,025; 128,014 (all) | Bot 1,956; 1,437; 1,437; 1,437; 1,948 | total 702,718; 473,465; 473,452; 477,791; 595,860.
rho: M 0.2775 (paper OK), A 0.2776, B 0.2750, C 0.3703 (ceiling 0.6297).
PortScan 158,804 -> 1,956 confirmed; with ports included 90,694 => port exclusion drives the PortScan collapse (scan flows differ mainly in destination port).
Bot: merged 1,956 (all Friday); whole-file dedup 1,437 (all variants except C: 1,948). Raw Friday block 1,966 Bot flows, 10 with NaN/inf; within-window dedup before NaN drop keeps 1,441, after NaN drop 1,437; dedup after NaN drop 1,437 => the 4-flow difference is NaN-containing rows hashed before they are dropped, not cross-day duplicates (Bot occurs only on Friday).
Other classes whole-file (before -> M): DoS Hulk 230,124->172,846; DDoS 128,025->128,014; GoldenEye 10,293->10,286; FTP-Patator 7,935->5,931; SSH-Patator 5,897->3,219; slowloris 5,796->5,385; Slowhttptest 5,499->5,228; WebBF 1,507->1,470; XSS 652; Infiltration 36; SQLi 21; Heartbleed 11. BENIGN 2,271,320 -> 1,862,721.

# E1c (2026-10-26): Table 6 rerun, duplicates defined WITH Destination Port (float64 hash; model features still ports-excluded; LightGBM deterministic=True, force_row_wise=True)
File: Drive UNSW-NB15/cic2017_table6_dedup_ports_runs.csv (60 of 60 runs). Libraries: sklearn 1.6.1, lightgbm 4.6.0, xgboost 3.4.1 (paper states 3.3.0), imblearn 0.14.2, scipy 1.16.3, pandas 2.2.3.
Removed 307,078 of 2,827,876 (10.86%). Friday 595,860 flows: BENIGN 375,204; DDoS 128,014; PortScan 90,694; Bot 1,948. rho 0.3703, ceiling 0.6297.
RF: random .9944 temporal .4785 gap 51.59 [51.02, 52.16]; LGBM: .9972 / .3885 gap 60.86 [60.78, 60.95]; XGB: .9976 / .4498 gap 54.78 [51.42, 58.14]. Wilcoxon exact p 0.00195, 10/10 positive pairs each.
Compare: as captured 54.38 / 60.75 / 56.76; ports-excluded dedup 48.56 / 56.29 / 50.29. Ports-included dedup reduces the gap by 2.8 / -0.1 / 2.0 pp only.

# E1d (2026-10-26): Table 20 multiclass rerun, duplicates WITH Destination Port (30 of 30 runs; LightGBM deterministic)
File: Drive UNSW-NB15/cic_multiclass_temporal_dedup_ports_results.csv. Friday 595,860 flows; rho 0.3703; ceiling 0.6297; 4 train-day classes known.
LightGBM acc 0.6295 (slack 0.0002); XGBoost 0.6282 (slack 0.0015); RandomForest 0.6249 (slack 0.0048). Violations 0 of 30.
Compare: ports-excluded dedup rho 0.2775, ceiling 0.7225 (CELL 42: accuracies RF .7155, LGBM .7222, XGB .7210); as captured rho 0.411, ceiling 0.589. Ceiling still met within 0.02-0.48 pp.

# E1e (2026-10-26): ten within-window controls, duplicates WITH Destination Port (600 of 600 runs; LightGBM deterministic; seeds 42-51; reduced pipeline)
File: Drive UNSW-NB15/cic2017_windows_dedup_ports_runs.csv. No windows skipped. Chron-test duplicate share 0.000 for all (by construction).
gap pp RF / LGBM / XGB (window mean; attack train/test):
SSH-Patator +2.44[1.97,2.91] / +2.09[1.95,2.22] / +1.07[1.00,1.14] (+1.87; 2676/543)
DoS Hulk +2.92[2.57,3.28] / +1.13[1.04,1.22] / +1.21[1.15,1.26] (+1.76; 146077/26769)
Bot +0.81[0.22,1.40] / -0.23[-0.34,-0.12] / -0.09[-0.41,0.23] (+0.16; 1591/357)
PortScan +0.17 / +0.17 / +0.17 (+0.17; 77485/13209; dedup removed 25.9%: 277,626->205,808)
DDoS +0.14[0.12,0.15] / +0.08 / +0.08 (+0.10; 104761/23253)
GoldenEye +0.21[0.19,0.22] / +0.52[0.39,0.65] / +0.14[0.08,0.20] (+0.29; 6390/3896)
Slowloris +5.90[5.78,6.02] / +4.25[4.22,4.29] / +1.78[1.25,2.31] (+3.98; 3815/1570)
Slowhttptest +0.65[-0.65,1.95] / +1.39[0.35,2.43] / -0.90[-2.71,0.90] (+0.38; 4174/1054)
WebBruteForce -0.50[-1.54,0.54] / -0.09[-0.17,-0.01] / -0.15[-0.27,-0.04] (-0.25; 1141/329)
WebXSS +1.78[1.45,2.11] / +0.80[0.56,1.05] / +0.95[0.64,1.26] (+1.18; 528/124)
WINDOW-LEVEL (n=10, df 9): +0.96 [+0.04, +1.88]; 9 of 10 windows positive. Ports-excluded dedup: +1.43 [-0.05,+2.91]; as captured +0.76 [-0.06,+1.58].
Key: ports-included dedup keeps PortScan at +0.17 (ports-excluded gave +5.30, an artefact of removing distinct probes). Interval now excludes zero (lower bound +0.04), barely.

# E2 (2026-10-26): benign-only baseline (IF 100 trees; AE 32-16-32, 30 epochs; threshold 99th pct training-benign scores), 80 of 80 runs, seeds 42-51, same samples as Table 6; dedup_ports = duplicates with Destination Port
Files: Drive UNSW-NB15/cic2017_e2_benign_only_runs.csv, cic2017_e2_benign_shift_control.csv. sklearn 1.6.1, scipy 1.16.3, pandas 2.2.3.
| variant | detector | F1 rnd | F1 tmp | gap pp | 95% CI | Wilcoxon p | recall rnd/tmp | FPR rnd/tmp | ROC-AUC rnd/tmp | PR-AUC rnd/tmp | never-alert F1 rnd/tmp |
| captured | IF | .4998 | .3755 | +12.43 | [10.76,14.10] | .00195 | .060/.011 | .010/.030 | .733/.646 | .411/.473 | .445/.371 |
| captured | AE | .7275 | .5487 | +17.88 | [17.24,18.52] | .00195 | .377/.218 | .010/.051 | .837/.774 | .648/.650 | .445/.371 |
| dedup_ports | IF | .5121 | .3914 | +12.06 | [10.00,14.12] | .00195 | .065/.013 | .010/.032 | .797/.658 | .452/.442 | .454/.386 |
| dedup_ports | AE | .7835 | .5973 | +18.61 | [17.65,19.58] | .00195 | .475/.271 | .010/.054 | .894/.778 | .725/.637 | .454/.386 |
Per-family recall temporal (Bot/DDoS/PortScan): captured IF .000/.025/.000; AE .024/.490/.000; dedup_ports IF .000/.021/.000; AE .027/.467/.001. Random: IF .004/.015/.000, AE .019/.441/.000 (captured); dedup_ports IF .000/.017/.000, AE .015/.404/.000.
Benign-shift control (FPR held-out Mon-Thu benign vs Friday benign): captured IF .0100 vs .0295; AE .0101 vs .0511; dedup_ports IF .0104 vs .0320; AE .0099 vs .0532.
Reading: IF temporal F1 (.376/.391) is within 0.5 pp of the never-alert reference (.371/.386) => IF is no better than never alerting on Friday; AE adds only through DDoS recall (~.47-.49). Temporal FPR 3-5x the 1% nominal and benign-shift control shows the benign distribution itself shifts (same detector 1% on held-out Mon-Thu benign vs 3-5% on Friday benign).

# E3 (2026-10-26): cut-point sensitivity, ten within-window controls, reduced pipeline; train = first f of block, test = rows f..min(f+0.2,1); random = stratified split of the same union (same train size); seeds 42-44; RF/LGBM/XGB (LGBM deterministic); 1764 runs
File: Drive UNSW-NB15/cic2017_e3_cut_sweep_runs.csv. Skips (attack flows < 100 on a side): captured WebXSS f=0.7 (454/88); dedup_ports WebXSS f=0.7 (451/91). All else included.
Mean gap pp (mean of 3 models, 3 seeds) [attack train/test]:
captured: 
 Bot  f.5 +1.00[732/810] f.6 +42.75[1464/142] f.7 +3.41[1542/155] f.8 -0.01[1606/349] f.9 -0.69[1697/259]
 DDoS 0.00 / 0.00 / +0.01 / +0.09 / +0.21
 DoS Hulk -0.02 / -0.01 / +0.18 / +1.64 / +1.28
 GoldenEye +13.10[1124/3562] / +10.18 / +3.19 / +0.31 / -0.00
 PortScan +1.89 / -0.01 / +0.06 / +0.11 / +0.02
 SSH-Patator +2.84 / +1.03 / +0.76 / +1.01 / +1.12
 Slowhttptest +24.65[2768/1064] / +15.80 / +13.24 / +0.13 / +27.14[4940/559]
 Slowloris -0.04 / -0.08 / -0.06 / +3.27 / +5.00
 WebBruteForce +0.87 / 0.00 / -0.93 / -0.63 / -1.26
 WebXSS +2.28 / -0.27 / skip / +1.28 / +1.50
 per f (window-level t): f.5 +4.66 [-1.09,10.40] 7/10; f.6 +6.94 [-2.89,16.77] 6/10; f.7 +2.21 [-1.17,5.59] 7/9; f.8 +0.72 [-0.08,1.53] 8/10; f.9 +3.43 [-2.65,9.51] 7/10
dedup_ports:
 Bot +0.74 / +41.29[1454/137] / +3.60 / -0.01 / -0.29
 DDoS 0.00 / +0.01 / +0.01 / +0.10 / +0.21
 DoS Hulk +0.01 / -0.07 / +0.11 / +1.74 / +1.42
 GoldenEye +12.93 / +9.68 / +4.62 / +0.29 / -0.05
 PortScan +1.72 / -0.00 / +0.05 / +0.17 / +0.03
 SSH-Patator +6.07 / +1.86 / +0.82 / +1.88 / +2.35
 Slowhttptest +43.06[2640/1002] / +32.56 / +14.98 / -0.36 / +37.45[4696/532]
 Slowloris -0.08 / -0.08 / -0.07 / +3.82 / +6.21
 WebBruteForce +1.30 / +0.25 / -0.26 / -0.33 / +0.43
 WebXSS +1.08 / +0.10 / skip / +1.15 / +1.62
 per f: f.5 +6.68 [-2.91,16.27] 9/10; f.6 +8.56 [-2.44,19.56] 7/10; f.7 +2.65 [-1.15,6.46] 7/9; f.8 +0.85 [-0.10,1.79] 7/10; f.9 +4.94 [-3.35,13.22] 8/10
f=0.8 reproduction vs earlier runs (seeds 42-51), window means: captured differences within 0.5 pp except Slowhttptest (+0.13 vs -0.39, diff +0.52 FLAG); dedup_ports Slowhttptest -0.36 vs +0.38 (diff -0.74 FLAG); all other cells |diff| <= 0.33.
Reading: the f=0.8 cut is among the quietest; other cuts give gaps up to +43 pp (Bot f=0.6, Slowhttptest f=0.5/0.9, GoldenEye f=0.5) where attack bursts fall on one side. The within-window gap depends strongly on the cut point; window-level intervals at f != 0.8 include zero and are wide.

## E4 CSE-CIC-IDS 2018 feature-set confound (180/180 runs; cic2018_e4_feature_confound_runs.csv)
Seed 42, config orig reproduces Table 12 exactly (41 features random / 43 temporal). week1_fixed (43/43): RF .9662/.4383 +52.79; LGBM .9725/.4383 +53.42; XGB .9733/.4383 +53.50. pooled_fixed (41/41): RF +52.80; LGBM +53.38; XGB +53.47. Recall rnd .9387/.9372/.9278, tmp 0.
All seeds (10), gap pp [95% t-CI]:
| config | model | gap | recall rnd/tmp |
|---|---|---|---|
| orig | RF | +52.70 [52.64, 52.76] | .9390/.0000 |
| orig | LGBM | +49.02 [42.76, 55.29] | .9399/.0481 |
| orig | XGB | +49.52 [43.52, 55.51] | .9279/.0459 |
| week1_fixed | RF | +52.70 [52.65, 52.76] | .9391/.0000 |
| week1_fixed | LGBM | +49.02 [42.76, 55.29] | .9398/.0481 |
| week1_fixed | XGB | +49.50 [43.51, 55.50] | .9280/.0459 |
| pooled_fixed | RF | +49.42 [44.38, 54.46] | .9390/.0367 |
| pooled_fixed | LGBM | +51.08 [46.34, 55.83] | .9399/.0241 |
| pooled_fixed | XGB | +51.50 [46.97, 56.04] | .9279/.0231 |
Conclusion: feature-set mismatch does not drive the 2018 gap (changes <= ~2.5 pp, all gaps 49-53 pp, temporal recall <= 5%).

## E5 XGBoost discrepancy diagnosis (seed 42; runtime L4 53 GB after an out-of-RAM crash on the 12.7 GB runtime; library versions sklearn 1.6.1, lightgbm 4.6.0, xgboost 3.4.1 (paper 3.3.0), imblearn 0.14.2)
Stage 1: per-day preprocessed files and all_days_merged.csv are identical (all 78 numeric columns, labels, row counts per day; Destination Port present in both).
Stage 2/3 F1-macro random | temporal (RF / LGBM / XGB), each step changes one thing:
| variant | random | temporal | features |
|---|---|---|---|
| A (CELL 16) | .9901/.9978/.9979 | .4294/.3721/.4488 | 43 |
| B +original sampling (binary groups, 200k/100k) | .9892/.9975/.9981 | .4253/.3852/.4378 | 43 |
| C +corr filter >= .95 | identical to B | identical | 43 |
| D +ATTACK=0 encoding | identical to B | identical | 43 |
| E original pipeline on per-day files | .9950/.9984/.9986 | .4399/.3734/.5040 | 44 |
Paper original: XGB .9987/.4960; RF .9950/.4399; LGBM .9984/.3734. Variant E reproduces RF and LGBM exactly; XGB temporal .5040 vs .4960 (diff +.008, same version caveat; learned base_score differs). First divergence of the re-implementation: the feature list. D -> E adds only 'Destination Port' (E keeps the port as a feature; the Table 6 re-implementation excludes it). Sampled rows of D and E are identical (share 1.0000), so the sampling is not the cause. Sampling step A -> B moves XGB .4488 -> .4378 only.
So the 0.449 vs 0.496 gap = Destination Port included in the original XGBoost run (not a bug in either; a feature-set difference). RF/LGBM insensitive to it in the temporal protocol, XGBoost sensitive.
Stage 4: Table 18 XGBoost settings on the original pipeline E, seeds 42-46 (gap pp [95% CI df 4]) vs paper Table 18:
| trees | depth | gap here | Table 18 |
|---|---|---|---|
| 50 | 6 | 52.90 [43.58, 62.23] | 60.31 [56.48, 64.14] |
| 100 | 6 | 54.53 [50.26, 58.80] | 59.85 [56.06, 63.65] |
| 200 | 6 | 55.12 [51.89, 58.35] | 59.31 [55.78, 62.84] |
| 100 | 3 | 43.47 [35.73, 51.21] | 55.25 [46.64, 63.87] |
| 100 | 9 | 54.14 [53.13, 55.14] | 58.52 [54.87, 62.16] |
All five reproduced means are lower than Table 18 (by 7.4, 5.3, 4.2, 11.8, 4.4 pp respectively), but every reproduced 95% CI (4 df) overlaps its Table 18 interval (the 100/9 setting only just: 55.14 vs 54.87). The qualitative conclusion (gap of 43-55 pp, depth-3 smallest) is unchanged; the Table 18 XGBoost point estimates are not reproduced and the cause is not located (candidates: xgboost 3.4.1 vs 3.3.0, unrecorded pipeline detail). Recommendation: report the reproduced values or flag Table 18 XGBoost rows as not reproduced.
Saved: cic2017_e5_variants_seed42.csv, cic2017_e5_xgb_sweep_aligned.csv.

## E6 Statistics pass (no training; script over archived runs; file cic_e6_statistics.csv). Exact two-sided Wilcoxon on paired seed differences (n=10, smallest possible p = 0.00195), Holm adjustment within each table/condition, share positive = fraction of seeds with random > chronological, rank-biserial = matched-pairs r (+1 = all pairs positive).
Archived-run caveat: the original (as-captured) Table 6 raw runs are not on Drive. significance_test_raw_runs.csv (30 runs) gives gaps LGBM 60.75 (matches paper), RF 55.69 (paper 54.38), XGB 59.47 (paper 56.76), i.e. an earlier run; the paper's RF/XGB Table 6 values cannot be recomputed from an archived file.
Table 6 (Holm family 9): all 9 cells p = 0.00195 raw, 0.01758 Holm, share positive 1.00, rank-biserial +1.00. Gaps: captured(sig file) LGBM +60.75 [59.17, 62.33], RF +55.69 [51.08, 60.30], XGB +59.47 [57.13, 61.82]; dedup ports-excl RF +48.56 [44.62, 52.50], LGBM +56.29 [54.60, 57.98], XGB +50.29 [45.29, 55.28]; dedup ports-incl RF +51.59 [51.02, 52.16], LGBM +60.86 [60.78, 60.95], XGB +54.78 [51.42, 58.14].
Table 7 (Tue/Wed within-window, Holm family 6): DoS Hulk LGBM +1.05 [0.98, 1.13], RF +2.59 [2.26, 2.93], XGB +1.22 [1.20, 1.24] (p .00195, Holm .01172); SSH-Patator LGBM +0.06 [0.02, 0.10] (p .01367, Holm .01367, share positive .90, r +.85), RF +2.18 [1.13, 3.23] (p .00391, Holm .01172, .90, r +.96), XGB +0.70 [0.46, 0.93] (p .00195, Holm .01172, 1.00). All six significant after Holm.
Table 8 (30 cells = 10 windows x 3 models, per condition): NO cell is significant after Holm in any condition (0 of 30 in captured, ports-excluded dedup, ports-included dedup). Structural: with n=10 the smallest exact p is 0.00195; x30 = 0.0586 > 0.05, so no cell can pass Holm over 30 tests even with all ten seeds positive. Cells with share positive > 0.5: captured 23/30, dedup ports-excl 22/30, dedup ports-incl 24/30. Example: Bot LGBM -0.24 [-0.41, -0.07] p .01953 Holm .176 share .10 r -.82; Bot RF +1.42 [0.59, 2.25] p .00586 Holm .0586 share .90 r +.93; DDoS LGBM +0.07 [0.07, 0.08] p .00195 Holm .0586 share 1.00.
Window-level (n = 10 windows, mean over models, t df 9): captured +0.76 [-0.06, +1.58], 8/10 windows positive (matches v9); dedup ports-excl +1.43 [-0.05, +2.91], 8/10 (matches v9); dedup ports-incl +0.96 [+0.04, +1.88], 9/10 (new; CI excludes zero narrowly).
Table 14 (UNSW-NB15, Holm family 3): RF random-temporal +0.19 [0.12, 0.26], LGBM +0.23 [0.15, 0.31], XGB +0.24 [0.17, 0.30]; p .00195, Holm .00586, share positive 1.00, r +1.00 (UNSW gaps are tiny: 0.2 pp but consistent).
Not recomputed: seed-42 rows in Table 4 format (Accuracy and ROC-AUC) because the archived Table 6 runs hold only F1-macro, recall and FPR (see seed-42 rows below).

## Seed-42 rows of the Table 6 reruns (Table 4 layout; archived runs hold only F1-macro, recall, FPR; Accuracy and ROC-AUC were not stored, so those two columns are not available without a rerun)
Ports-excluded dedup (cic2017_table6_dedup_runs.csv):
| Model | Split | F1-macro | Recall | FPR | features | SMOTE added |
|---|---|---|---|---|---|---|
| RF | random | .9928 | .9886 | .0023 | 41 | 5526 |
| RF | temporal | .4806 | .0609 | .0010 | 43 | 6236 |
| LGBM | random | .9965 | .9971 | .0016 | 41 | 5526 |
| LGBM | temporal | .4251 | .0055 | .0006 | 43 | 6236 |
| XGB | random | .9972 | .9946 | .0007 | 41 | 5526 |
| XGB | temporal | .4349 | .0148 | .0004 | 43 | 6236 |
Ports-included dedup (cic2017_table6_dedup_ports_runs.csv):
| RF | random | .9943 | .9889 | .0016 | 44 | 2984 |
| RF | temporal | .4755 | .0889 | .0013 | 43 | 8697 |
| LGBM | random | .9976 | .9996 | .0015 | 44 | 2984 |
| LGBM | temporal | .3888 | .0023 | .0006 | 43 | 8697 |
| XGB | random | .9978 | .9982 | .0011 | 44 | 2984 |
| XGB | temporal | .4813 | .0947 | .0003 | 43 | 8697 |
(The number of features after the variance and correlation filters differs between random (41/44) and temporal (43) protocols, as in the paper pipeline.)

## E7 LightGBM determinism check (sklearn 1.6.1, lightgbm 4.6.0, L4 runtime; files cic2017_e7_lgbm_determinism_runs.csv, cic2017_e7b_lgbm_window_determinism_runs.csv)
E7 (Table 6 pipeline, ports-included dedup, seeds 42-51, both protocols = 20 settings): LightGBM fitted twice with deterministic=True, force_row_wise=True: identical predictions and identical probabilities 20/20. Fitted twice with default settings: identical 20/20. Deterministic vs default: 0 test rows differ, max |F1 difference| 0.0000. The archived E1c LightGBM F1 (4 dp) is reproduced 20/20. Gap det 60.86 [60.78, 60.95] = default 60.86 [60.78, 60.95].
E7b (within-window pipeline, ports-included dedup, windows Slowhttptest, Bot, WebXSS; seeds 42-51, both splits = 20 fits each): deterministic fit twice identical 20/20 in every window; default fit twice identical 20/20; archived F1 reproduced 20/20; 0 rows differ between deterministic and default; max F1 difference 0.0000.
Conclusion: on this machine LightGBM shows no run-to-run or deterministic-vs-default difference, so the earlier non-deterministic runs are not a source of variation here. The E3 f=0.8 reproduction flags for Slowhttptest (captured +0.13 vs -0.39; dedup -0.36 vs +0.38) are therefore NOT caused by LightGBM nondeterminism; they come from using 3 seeds (42-44) in E3 against 10 seeds (42-51) before, and from the E3 split construction (random baseline = stratified split of the same row union, train size = f). Caveat: determinism was verified on a different runtime (L4 session, CPU thread count unknown) from the original captured runs; bitwise equality across other CPUs/thread counts is not guaranteed.
