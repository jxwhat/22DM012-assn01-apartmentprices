| File | Commit | Model / change | Public score |
|---|---|---|---|
| `sub01_ols-fs6_2026-10-25T0149.csv` | – (not committed yet) | Naive: OLS on log price, 6 forward-selected numeric features, raw data. Submitted 2026-10-25 01:54 | 0.15889 |
| `sub02_olsplus-psf-fs10-avg_2026-10-27T0048.csv` | – (not committed yet) | Naive+: OLS on psf, 10 forward-selected features, cleaned + outliers dropped + one-hot, 5-fold averaged weights (CV 0.161). Submitted 2026-10-27 00:50 | 0.15121 |
| `sub03_olspp-logpsf-fs10-refit_2026-10-27T1944.csv` | – (not committed yet) | Naive++: OLS on **log** psf, 10 forward-selected features, cleaned + outliers dropped + one-hot, final model refit on 100% (CV 0.152). Submitted 2026-10-27 19:44 | 0.14903 |
| `sub04_olspp-logpsf-fs10-fe-refit_2026-10-27T2152.csv` | – (not committed yet) | Naive++ rerun with engineered features + outlier decision B, refit on 100% (nested CV 0.1403). Submitted 2026-10-27 21:52 | 0.14120 |
| `sub05_lasso-logpsf-all_2026-10-27T2152.csv` | – (not committed yet) | Lasso, log psf, all engineered features, α 0.000505, refit on 100% (nested CV 0.1272). Submitted 2026-10-27 21:52 | 0.12949 |
| `sub06_ridge-logpsf-all_2026-10-27T2152.csv` | – (not committed yet) | Ridge, log psf, all engineered features, α 21.6, refit on 100% (nested CV 0.1270). Submitted 2026-10-27 21:52 | 0.12450 |
| `sub07_enet-logpsf-all_2026-10-27T2152.csv` | – (not committed yet) | Elastic Net, log psf, all engineered features, λ₁ 0.000524, λ₂ 4.8e-07 (≈ Lasso), refit on 100% (nested CV 0.1278). Submitted 2026-10-27 21:52 | 0.12946 |
| `sub08_olspp-logpsf-fs10-nolivarea-rs42_2026-10-27T2337.csv` | – (not committed yet) | naive++, no living-area inputs, random_state 42 (nested CV 0.1531). Submitted 2026-10-27 23:38 | 0.15408 |
| `sub09_lasso-logpsf-nolivarea-rs42_2026-10-27T2337.csv` | – (not committed yet) | Lasso, α 0.000392 (nested CV 0.1387). Submitted 2026-10-27 23:38 | 0.14092 |
| `sub10_ridge-logpsf-nolivarea-rs42_2026-10-27T2337.csv` | – (not committed yet) | **Ridge, FINAL MODEL**, α 7.75 (nested CV 0.1378); identical to `22DM012-assn01-jx_ridge.csv`. Submitted 2026-10-27 23:38 | 0.13809 |
| `sub11_enet-blend-logpsf-nolivarea-rs42_2026-10-27T2337.csv` | – (not committed yet) | Elastic Net blend, λ₁ 0.000135, λ₂ 0.0026 (nested CV 0.1389). Submitted 2026-10-27 23:38 | 0.13895 |

**2026-10-27: CV evaluation changed (outlier decision B).** The CV numbers above (0.161, 0.152) were on the 1,442 houses left after the psf rule. CV now scores all 1,460 training houses, with outliers dropped inside each fit (round 1: huge partial sales 524/1299; round 2: psf whiskers). Re-scored: naive+ 0.1618 ± 0.018, naive++ 0.1532 ± 0.023. Compare new models with these.

**2026-10-27: re-scored with the engineered features** (`add_features`; not resubmitted): naive+ 0.1493 ± 0.026, naive++ 0.1403 ± 0.030 (all 1,460 houses, same folds). The current baseline for the penalised models.

**2026-10-27: Lasso and Ridge** (log psf, all engineered features, α tuned in two levels of 20 log-steps; not submitted). Nested CV: Lasso 0.1272 ± 0.033 (α ≈ 0.0005, 97/272 non-zero), Ridge 0.1270 ± 0.030 (α ≈ 21.6). Tuning-curve minima 0.1266 / 0.1267. Both beat naive++ (0.1403) in all 5 folds; Lasso vs Ridge within noise.

**2026-10-27: Elastic Net** (log psf, all engineered features, (λ₁, λ₂) by two-level random search: 1,000 + 500 log-uniform pairs; not submitted). Nested CV 0.1278 ± 0.031: no gain over Lasso / Ridge. The tuning surface is a flat L-shaped valley (Lasso end vs Ridge end, both ≈ 0.1266–0.1268). The full-data search picked the Lasso end (`l1_ratio` 1.00); 4 of 5 nested inner searches picked the Ridge end.

**2026-10-27: `GrLivArea` removed as a model input** (`ui_drop_grlivarea = True`; `log_GrLivArea` gone, raw `GrLivArea` kept for the target/conversion; `1stFlrSF`/`2ndFlrSF`/`LowQualFinSF` still in; not submitted). Nested CV, same folds: naive++ 0.1452 (was 0.1403), Lasso 0.1255 (was 0.1272), Ridge 0.1275 (was 0.1270), Elastic Net 0.1257 (was 0.1278). Kaggle scores sub04–sub07 are for the earlier version.

**2026-10-27: `1stFlrSF`, `2ndFlrSF`, `LowQualFinSF` also removed as model inputs** (`ui_drop_area_parts = True`; they sum to `GrLivArea`; not submitted). Nested CV: naive++ 0.1523 (was 0.1452), Lasso 0.1404 (was 0.1255), Ridge 0.1410 (was 0.1275), Elastic Net 0.1405 (was 0.1257).

**2026-10-27: submission notebook only — `random_state` 42 + blended Elastic Net** (EN level 2 searched around the blend centre; not submitted). Nested CV: naive++ 0.1531, Lasso 0.1387, Ridge 0.1378, Elastic Net (blend, l1_ratio 0.05) 0.1389. Final model: Ridge. The ranking vs the random_state 0 run (Lasso first) flipped: the three are within noise.
