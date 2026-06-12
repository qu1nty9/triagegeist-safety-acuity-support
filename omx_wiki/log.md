# Triagegeist Wiki Log

## [2026-06-12] ingest | Local competition files

- Read `docs/Triagegeist - hackathon description.docx`.
- Read `docs/Dataset Description - Triagegeist.docx`.
- Profiled CSV files under `triagegeist-data/`.
- Created initial LLM-wiki structure in `omx_wiki/`.

Key findings:

- Submission is judged as a Kaggle Notebook plus Kaggle Writeup, with no leaderboard/public-score optimization loop.
- Rubric weights: clinical relevance 25, technical quality 30, documentation/writeup 20, insight/findings 15, novelty/impact 10.
- Dataset is synthetic and non-commercial; all rows are simulated.
- Target is `triage_acuity`, ESI level 1-5 where 1 is most urgent.
- `disposition` and `ed_los_hours` exist only in train and must not be used as model features for test submission.

## [2026-06-12] experiment | Initial sparse baseline

- Built first local baseline using system Python `sklearn 1.7.2`.
- Model: `ColumnTransformer` with numeric imputation/scaling, categorical one-hot encoding, TF-IDF chief complaint text, and `SGDClassifier(loss="log_loss", class_weight="balanced")`.
- Wrote `outputs/baseline_metrics.json`.
- Wrote `outputs/baseline_sgd_tfidf_submission.csv`.
- Wrote `outputs/ablation_metrics.csv`.

Important result:

- Full valid feature baseline reached 0.9946 holdout accuracy and 0.9840 macro-F1 in the Kaggle-verified run.
- `main_table_only` reached 0.7487 accuracy.
- Adding history without text reached 0.7646 accuracy.
- Text-enabled variants were near deterministic, so the writeup must emphasize ablation, safety metrics, and synthetic-data limitations rather than overclaiming.

## [2026-06-13] submission package | Safety layer and writeup draft

- Added high-risk alert analysis to the notebook using `P(acuity <= 2)`.
- Generated `outputs/high_risk_threshold_grid.csv`.
- Generated `outputs/high_risk_operating_points.csv`.
- Generated writeup figures in `outputs/figures/`.
- Generated strict 640 x 360 Media Gallery cover image: `outputs/media_gallery/01_cover.png`.
- Added `README.md` for public project-link readiness.
- Added `requirements.txt`.
- Added Kaggle writeup draft: `KAGGLE_WRITEUP_DRAFT.md`.

Important operating point:

- At threshold 0.50, the high-risk alert captures 100% of true acuity 1-2 cases on validation, misses 0 high-acuity cases, creates 1 false alert, and alerts 20.8% of patients.

Submission-readiness checks:

- `KAGGLE_WRITEUP_DRAFT.md` is 813 words, below the 2,000-word limit.
- `outputs/submission.csv` has 20,000 predictions plus header.
- Media Gallery image dimensions are exactly 640 x 360 px.

## [2026-06-13] validation | CV and error review

- Added 5-fold stratified cross-validation to the notebook.
- Added validation error-review table.
- Wrote `outputs/cv_metrics.csv` and `outputs/cv_summary.csv`.
- Wrote `outputs/error_review.csv` and `outputs/error_summary.csv`.

Initial local CV results were superseded by the later Kaggle run below.

## [2026-06-13] Kaggle run | Notebook verified in target environment

- User ran the notebook on Kaggle.
- Runtime: 3m 27s.
- The attached Kaggle output folder contained all expected artifacts.
- Local `outputs/` was synchronized with the Kaggle-run outputs.

Kaggle-run key results:

- Holdout `all_valid_features`: accuracy 0.9946, macro-F1 0.9840, QWK 0.9975, severe under-triage 0.0000.
- 5-fold CV mean accuracy: 0.9813.
- 5-fold CV mean macro-F1: 0.9564.
- 5-fold CV mean QWK: 0.9918.
- 5-fold CV mean severe under-triage: 0.00024.
- Fixed validation split: 87 total errors, 0 severe under-triage errors.
- High-risk alert at threshold 0.50: 100% recall for true acuity 1-2, 0 missed high-acuity cases, 1 false alert, 20.8% alert rate.

## [2026-06-13] polish | Final judge-facing package

- Polished notebook narrative with executive summary, rubric mapping, clinical interpretation, and final takeaways.
- Added final writeup text: `KAGGLE_WRITEUP_FINAL.md`.
- Added rubric mapping: `JUDGING_RUBRIC_MAPPING.md`.
- Added final checklist: `FINAL_SUBMISSION_CHECKLIST.md`.
- Updated README to point to final submission assets.
- Added `outputs/media_gallery/` with five 640 x 360 images for Kaggle Media Gallery.

Positioning:

- The project is now framed as a reproducible clinical safety layer against under-triage, not as a metric-only classifier.
