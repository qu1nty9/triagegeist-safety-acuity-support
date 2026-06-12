# Triagegeist Wiki Index

This wiki follows the LLM-wiki pattern: raw sources remain immutable, while project knowledge is compiled into maintained markdown pages. Use `log.md` for chronological updates and this index for navigation.

## Project Pages

- [[task-brief]] - Competition objective, submission requirements, judging rubric, and constraints.
- [[dataset-profile]] - Dataset structure, leakage notes, target balance, missingness, and early clinical signals.
- [[approach-plan]] - End-to-end Kaggle plan from problem framing to modeling, validation, and submission.
- [[experiment-log]] - Baseline runs, ablations, metrics, and decisions.
- [[writeup-outline]] - Kaggle writeup structure designed around the judging rubric.

## Current Direction

Primary framing: a clinically grounded emergency triage decision-support notebook that predicts ESI-style acuity 1-5, emphasizes safety around under-triage of true ESI 1-2 cases, and explains model behavior transparently.

Core modeling stance:

- Exclude train-only outcome columns: `disposition`, `ed_los_hours`.
- Join all tables on `patient_id`.
- Treat missing vitals and `pain_score == -1` as clinically informative missingness.
- Use structured intake data, patient history flags, and chief complaint text.
- Report not only accuracy, but macro-F1, ordinal MAE, quadratic weighted kappa, confusion matrix, and severe under-triage rate.
- Include a high-risk alert mode using `P(acuity <= 2)` so the project reads as clinical decision support rather than just multiclass classification.

## Submission Package

- Notebook: `output/jupyter-notebook/00-triagegeist-safety-baseline.ipynb`
- Submission CSV: `outputs/submission.csv`
- Media Gallery cover image: `outputs/media_gallery/01_cover.png`
- Writeup draft: `KAGGLE_WRITEUP_DRAFT.md`
- Public project README: `README.md`

Final judge-facing files:

- `KAGGLE_WRITEUP_FINAL.md`
- `JUDGING_RUBRIC_MAPPING.md`
- `FINAL_SUBMISSION_CHECKLIST.md`
