# Triagegeist Kaggle Work Plan

This project should be run as a safety-focused clinical AI notebook, not as a generic classification exercise.

## Best Submission Strategy

Build a public Kaggle Notebook that:

- predicts `triage_acuity` 1-5 from intake data,
- explicitly excludes train-only outcome leakage,
- joins chief complaint text and patient history,
- evaluates severe under-triage of true ESI 1-2 cases,
- includes ablation experiments,
- explains limitations of synthetic data,
- ends with a valid submission CSV.

The Kaggle Writeup should use the same framing:

`A reproducible emergency triage decision-support prototype focused on avoiding dangerous under-triage.`

## Current Completed Work

- Read both local `.docx` task descriptions.
- Profiled all local CSV files.
- Created `omx_wiki/` as the project knowledge base, following the LLM-wiki principle.
- Ran a first no-leakage sparse baseline with TF-IDF chief complaint text.
- Generated:
  - `outputs/baseline_metrics.json`
  - `outputs/ablation_metrics.csv`
  - `outputs/baseline_sgd_tfidf_submission.csv`
- Added high-risk alert operating-point analysis:
  - `outputs/high_risk_threshold_grid.csv`
  - `outputs/high_risk_operating_points.csv`
- Added robustness and error-review analysis:
  - `outputs/cv_metrics.csv`
  - `outputs/cv_summary.csv`
  - `outputs/error_review.csv`
  - `outputs/error_summary.csv`
- Added writeup figures and cover image:
  - `outputs/figures/target_distribution.png`
  - `outputs/figures/ablation_metrics.png`
  - `outputs/figures/high_risk_threshold_tradeoff.png`
  - `outputs/figures/confusion_matrix.png`
- Added Kaggle Media Gallery images, all 640 x 360:
  - `outputs/media_gallery/01_cover.png`
  - `outputs/media_gallery/02_high_risk_threshold_tradeoff.png`
  - `outputs/media_gallery/03_ablation_metrics.png`
  - `outputs/media_gallery/04_confusion_matrix.png`
  - `outputs/media_gallery/05_target_distribution.png`
- Added submission-readiness files:
  - `README.md`
  - `requirements.txt`
  - `KAGGLE_WRITEUP_DRAFT.md`
  - `KAGGLE_WRITEUP_FINAL.md`
  - `JUDGING_RUBRIC_MAPPING.md`
  - `FINAL_SUBMISSION_CHECKLIST.md`

## Current Baseline

Holdout: 20% stratified validation.

| Variant | Accuracy | Macro-F1 | QWK | Severe under-triage |
|---|---:|---:|---:|---:|
| Majority class | 0.3615 | 0.1062 | 0.0000 | 1.0000 |
| Main table only | 0.7805 | 0.7994 | 0.8978 | 0.0810 |
| Main + history, no text | 0.7668 | 0.8004 | 0.8891 | 0.0954 |
| All valid features | 0.9946 | 0.9840 | 0.9975 | 0.0000 |
| No composite scores | 0.9958 | 0.9871 | 0.9980 | 0.0000 |
| Minimal clinical intake | 0.9962 | 0.9882 | 0.9982 | 0.0000 |

Kaggle runtime:

- Full notebook completed in 3m 27s.
- 5-fold CV is acceptable to keep in the public notebook.

Kaggle CV summary for the full valid-feature model:

| Metric | Mean | Std |
|---|---:|---:|
| Accuracy | 0.9813 | 0.0281 |
| Macro-F1 | 0.9564 | 0.0562 |
| QWK | 0.9918 | 0.0114 |
| Severe under-triage | 0.00024 | 0.00054 |

## Remaining Submission Actions

1. Paste `KAGGLE_WRITEUP_FINAL.md` into Kaggle Writeups and attach:
   - public notebook,
   - public project link,
   - `outputs/media_gallery/01_cover.png`.
2. Attach the Kaggle-run aggregate output artifacts and figures in the writeup/project link.
3. Keep 5-fold CV in the final notebook because Kaggle runtime is acceptable.
4. Confirm the public project link excludes `triagegeist-data/`.
5. Confirm the public project link excludes patient-level outputs such as `outputs/submission.csv` and `outputs/error_review.csv`.
