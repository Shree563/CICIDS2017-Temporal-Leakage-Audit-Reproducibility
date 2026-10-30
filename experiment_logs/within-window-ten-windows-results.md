# CELL 38 results (original pipeline, 10 extra-window/ main windows; 10 seeds), file cic2017_orig_pipeline_extra_runs.csv
Window means (pp, random-chron F1, mean of RF/LGBM/XGB):
captured: SSH +1.01, Hulk +1.62, Bot +0.32, PortScan +0.11, DDoS +0.09, GoldenEye +0.36, Slowloris +3.40, Slowhttptest -0.39, WebBruteForce -0.31, WebXSS +1.35
dedup:    SSH +2.74, Hulk +1.80, Bot -1.47, PortScan +5.30, DDoS +0.10, GoldenEye +0.37, Slowloris +4.00, Slowhttptest +0.38, WebBruteForce -0.23, WebXSS +1.30
captured: pooled +0.76 [0.66,0.85]; WINDOW-LEVEL n=10 df9 +0.76 [-0.06,+1.58]; 8 of 10 windows positive
dedup:    pooled +1.43 [1.30,1.56]; WINDOW-LEVEL n=10 +1.43 [-0.05,+2.91]; 8 of 10 positive
Cells (captured): GoldenEye .19/.66/.22 (att 6559/3734, dup .160); Slowloris 5.27/3.13/1.79 (4063/1733, dup .079); Slowhttptest -0.73/+1.03/-1.47 (4391/1108, dup .098); WebBF RF -0.75 [-1.91,.40] (1167/340); XSS (528/124, dup .080)
Cells (dedup): GoldenEye .21/.79/.12 (6259/4027); Slowloris 5.99/4.23/1.77 (3817/1568); Slowhttptest RF +0.65 [-0.65,1.95], LGBM +1.39 [.35,..]; XSS 1.93/1.07/0.90 (527/125)
Earlier 5 windows: see results_cell37.md (PortScan dedup RF 4.92 [4.83,5.00], LGBM 5.13 [5.05,5.20], XGB 5.84).
Note: window-level CI barely includes zero; no window where leakage effect is significantly negative except Bot dedup.

# CELL 39 Bot diagnostics (original pipeline, file cic2017_bot_diagnostics_runs.csv)
captured: block 166,951 rows, attack 1,966; attack flows per decile [2,10,102,148,477,735,78,64,91,259] (one burst in deciles 3-6); attack rate train 1.21% / test 1.05%; test attacks nearest train attack distance median 0.000 (chron and random: near-identical flows).
 chron seed42: RF F1m .7713 prec .381 rec .997 FPR .0172 (567 FP of 33,026 benign); LGBM .9964 prec .989 FPR .0001 (4 FP); XGB .9964 prec .986 (5 FP)
 random seed42: RF .7728 prec .385 FPR .0189 (624 FP); LGBM .9910 rec .987; XGB .9903 rec .977
 forward-chaining (train first f, test next 20% slice; seeds 42-44; att tr/te): f.5 739/813 RF .8356 L .9603 X .9585; f.6 1474/142 RF .4940 L .4989 X .4989; f.7 1552/155 RF .6759 L .9903 X .9898; f.8 1616/349 .7682/.9952/.9952; f.9 1707/259 .7876/.9964/.9977; random 80/20 RF .7780 L .9916 X .9909
dedup: 154,329 rows, attack 1,441; attack rate train 0.878% / test 1.157%
 chron: RF .8427 prec .528 FPR .0104 (318 FP/30,502); LGBM .9937 prec .978 (8 FP); XGB .9965 prec .989 (4 FP)
 random: RF .8161 prec .469 FPR .0105 (321 FP); LGBM .9904 rec .990; XGB .9867 rec .972; NN dist p90 .027 random vs 0 chron
 forward-chaining dedup: f.5 443/576 RF .8209 L .9527 X .9419; f.6 946/138 RF .4998 L .5198 X .5189; f.7 1019/161 .7330/.9902/.9917; f.8 1084/356 .8232/.9925/.9929; f.9 1180/261 .8362/.9945/.9971; random RF .7791 L .9882 X .9874
Interpretation: RF F1m ~0.77-0.84 is driven by ~300-600 false positives in BOTH splits (precision .38-.53, recall ~.997); the Bot sign reversal after dedup comes from RF FP count and LGBM/XGB differences of 0.4-0.5 pp (recall of random split lower, 97-99%), not from temporal advantage. Cut at f=0.6 gives F1m ~0.50 (attacks in slice 0.6-0.8 missed) -> within-window result depends strongly on where the cut falls.

# CELL 40 per-cell table (dF1 mean [95% CI]; RF / LGBM / XGB; dup random/chron %; att tr/te; test rows)
cap Hulk +2.57[2.20,2.94] +1.07[0.99,1.14] +1.23[1.21,1.25] dup 23.9/22.2 att 192003/38121
cap SSH +2.31[1.10,3.52] +0.03[-0.00,0.06] +0.68[0.48,0.88] dup 15.5/17.8 att 4918/979
cap PortScan +0.11 +0.11 +0.10 dup 60.7/52.6 att 131613/27191
cap DDoS +0.12[0.11,0.14] +0.07 +0.08 dup 1.2/1.4 att 104980/23045
cap Bot +1.42[0.59,2.25] -0.24[-0.41,-0.07] -0.21[-0.44,0.01] dup 9.6/9.3 att 1606/350
cap GoldenEye +0.19[0.16,0.22] +0.66[0.54,0.78] +0.22[0.12,0.33] dup 13.4/16.0 att 6559/3734
cap Slowloris +5.27[5.21,5.33] +3.13[2.65,3.60] +1.79[1.31,2.28] dup 8.0/7.9 att 4063/1733
cap Slowhttptest -0.73[-1.48,0.03] +1.03[-0.11,2.18] -1.47[-3.01,0.06] dup 5.5/9.8 att 4391/1108
cap WebBF -0.75[-1.91,0.40] -0.09[-0.19,0.02] -0.10[-0.21,0.01] dup 7.5/7.4 att 1167/340
cap XSS +2.12[1.78,2.47] +0.89[0.67,1.11] +1.05[0.84,1.26] dup 8.0/8.0 att 528/124
ded Hulk +3.05[2.87,3.23] +1.12[1.05,1.19] +1.22[1.18,1.26] att 145770/27076
ded SSH +5.07[3.58,6.57] +2.16[2.12,2.21] +0.98[0.91,1.04] att 2648/571
ded PortScan +4.92[4.83,5.00] +5.13[5.05,5.20] +5.84[5.01,6.67] att 1616/340
ded DDoS +0.14[0.13,0.16] +0.08[0.07,0.08] +0.08[0.07,0.09] att 104693/23321
ded Bot -3.54[-4.63,-2.45] -0.43[-0.64,-0.21] -0.45[-0.90,0.01] att 1080/357
ded GoldenEye +0.21[0.18,0.23] +0.79[0.72,0.86] +0.12[0.07,0.18] att 6259/4027
ded Slowloris +5.99[5.85,6.13] +4.23[4.15,4.31] +1.77[1.47,2.07] att 3817/1568
ded Slowhttptest +0.65[-0.65,1.95] +1.39[0.35,2.43] -0.90[-2.71,0.90] att 4174/1054
ded WebBF -0.39[-1.35,0.58] -0.11[-0.26,0.03] -0.18[-0.34,-0.02] att 1140/330
ded XSS +1.93[1.50,2.36] +1.07[0.83,1.30] +0.90[0.57,1.22] att 527/125
NOTE: 77 common numeric features sentence belongs to CSE-CIC-IDS 2018 (Week1/Week2) paragraph, not CIC-IDS 2017.
