# Experiment Log

## Baseline: Sparse SGD + TF-IDF

Date: 2026-06-12.

Data:

- Train rows: 80,000.
- Holdout: 20% stratified split.
- Test rows: 20,000.
- Joined chief complaint text and history flags by `patient_id`.
- Excluded `disposition` and `ed_los_hours`.

Preprocessing:

- Numeric median imputation with missing indicators.
- Categorical one-hot encoding.
- Chief complaint TF-IDF, 1-2 grams, max 3,000 features.
- Pain score `-1` converted to missing plus indicator.
- Added simple clinical flags.

Model:

- `SGDClassifier(loss="log_loss", class_weight="balanced")`.

Primary full-feature holdout metrics:

| Metric | Value |
|---|---:|
| Accuracy | 0.9946 |
| Macro-F1 | 0.9840 |
| Weighted-F1 | 0.9947 |
| Ordinal MAE | 0.0054 |
| Quadratic weighted kappa | 0.9975 |
| Overall under-triage rate | 0.0008 |
| Severe under-triage, true 1-2 to predicted 3-5 | 0.0000 |

Artifacts:

- `outputs/baseline_metrics.json`
- `outputs/baseline_sgd_tfidf_submission.csv`
- `outputs/ablation_metrics.csv`

## Ablation Results

| Variant | Raw features | Accuracy | Macro-F1 | Ordinal MAE | QWK | Severe under-triage |
|---|---:|---:|---:|---:|---:|---:|
| Majority class 3 | 0 | 0.3615 | 0.1062 | 0.8212 | 0.0000 | 1.0000 |
| Main table only | 49 | 0.7805 | 0.7994 | 0.2251 | 0.8978 | 0.0810 |
| Main + history, no text | 74 | 0.7668 | 0.8004 | 0.2431 | 0.8891 | 0.0954 |
| All valid features | 75 | 0.9946 | 0.9840 | 0.0054 | 0.9975 | 0.0000 |
| No composite scores | 69 | 0.9958 | 0.9871 | 0.0043 | 0.9980 | 0.0000 |
| Minimal clinical intake | 49 | 0.9962 | 0.9882 | 0.0038 | 0.9982 | 0.0000 |

## Interpretation

The text-enabled variants are near deterministic on holdout. This suggests the synthetic chief complaint narratives encode strong acuity signals. The writeup should use this as an insight rather than hide it:

- Structured vitals/history alone are useful but not perfect.
- Chief complaint text dramatically improves high-acuity recognition.
- A safety metric is more clinically meaningful than accuracy alone.
- Synthetic-data limitations must be stated clearly.

## Next Experiments

- Extract top text n-grams and clinical coefficients for acuity 1 and 2.
- Run subgroup severe-undertriage analysis.
- Compare a structured-only tree model against the sparse text model.
- Add calibration or thresholding for a high-risk `acuity <= 2` flag.

## High-Risk Alert Analysis

Date: 2026-06-13.

Clinical framing:

- A triage support model can be used as an alerting layer rather than a full replacement for clinician scoring.
- The alert score is `P(acuity <= 2)`.
- The key operating tradeoff is high-acuity recall versus alert burden.

Selected operating points:

| Target recall | Threshold | Alert rate | Actual recall | Alert precision | Missed high-acuity |
|---:|---:|---:|---:|---:|---:|
| 0.95 | 0.9999 | 0.1979 | 0.9502 | 1.0000 | 166 |
| 0.98 | 0.9977 | 0.2041 | 0.9802 | 1.0000 | 66 |
| 0.99 | 0.9888 | 0.2062 | 0.9901 | 1.0000 | 33 |
| 1.00 | 0.5579 | 0.2083 | 1.0000 | 1.0000 | 0 |

Practical writeup point:

- At a threshold of 0.50, validation recall for true acuity 1-2 was 100%, with 0 missed high-acuity cases, 1 false alert, and 20.8% alert rate.

Artifacts:

- `outputs/high_risk_threshold_grid.csv`
- `outputs/high_risk_operating_points.csv`
- `outputs/subgroup_safety.csv`
- `outputs/top_linear_features.csv`

## Figures

Generated writeup-ready figures:

- `outputs/figures/target_distribution.png`
- `outputs/figures/ablation_metrics.png`
- `outputs/figures/high_risk_threshold_tradeoff.png`
- `outputs/figures/confusion_matrix.png`
- `outputs/media_gallery/01_cover.png`

The Media Gallery images have been verified at exactly 640 x 360 px.

## Cross-Validation And Error Review

Date: 2026-06-13.

Added 5-fold stratified CV for the full valid-feature model. The local first run was later superseded by the Kaggle-run output.

CV summary:

| Metric | Mean | Std |
|---|---:|---:|
| Accuracy | 0.9813 | 0.0281 |
| Macro-F1 | 0.9564 | 0.0562 |
| Weighted-F1 | 0.9827 | 0.0250 |
| Ordinal MAE | 0.0188 | 0.0280 |
| QWK | 0.9918 | 0.0114 |
| Under-triage rate | 0.0024 | 0.0030 |
| Severe under-triage | 0.0002 | 0.0005 |

Validation error review:

- Total validation errors on the fixed split: 87.
- Severe under-triage errors on the fixed split: 0.
- Most common clinically relevant boundaries: true acuity 2 predicted as acuity 1 and true acuity 1 predicted as acuity 2. These remain in the high-risk zone and are much less dangerous than acuity 1-2 predicted as 3-5.

Kaggle runtime:

- Full public-notebook run completed in 3m 27s, so 5-fold CV can stay in the final notebook.

Artifacts:

- `outputs/cv_metrics.csv`
- `outputs/cv_summary.csv`
- `outputs/error_review.csv`
- `outputs/error_summary.csv`
