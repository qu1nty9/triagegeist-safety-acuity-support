# Release Notes

## v0.1.0 - Research Artifact Release

Initial public research artifact for the Triagegeist safety-focused emergency triage acuity support project.

Included:

- Kaggle-ready notebook.
- Final project writeup.
- Public article and preprint drafts.
- Aggregated validation metrics.
- 640 x 360 media gallery assets.
- Judging rubric mapping.
- Reproducibility checklist.
- Citation and Zenodo metadata.

Excluded:

- Raw Triagegeist CSV files.
- Patient-level outputs such as `outputs/submission.csv` and `outputs/error_review.csv`.
- Local Kaggle/hackathon document files.

Key Kaggle-verified results:

- Runtime: 3m 27s.
- Holdout macro-F1: 0.9840.
- Holdout severe under-triage: 0.0000.
- 5-fold CV mean macro-F1: 0.9564.
- 5-fold CV mean severe under-triage: 0.00024.
- High-risk alert at threshold 0.50: 100% high-acuity recall, 0 missed high-acuity cases, 1 false alert.

