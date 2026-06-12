# Triagegeist Judging Rubric Mapping

This file maps the final project package to the stated Triagegeist judging rubric.

## 1. Clinical Relevance - 25 points

Claim: the project addresses under-triage, the safety-critical failure mode in emergency department triage.

Evidence:

- Notebook executive summary frames the task as high-acuity safety, not generic classification.
- Severe under-triage is defined as true acuity 1-2 predicted as 3-5.
- High-risk alert mode uses `P(acuity <= 2)` to surface patients for immediate clinician review.
- Error review shows 0 severe under-triage errors on the fixed validation split.

Primary artifacts:

- `output/jupyter-notebook/00-triagegeist-safety-baseline.ipynb`
- `outputs/high_risk_operating_points.csv`
- `outputs/error_summary.csv`
- `KAGGLE_WRITEUP_FINAL.md`

## 2. Technical Quality - 30 points

Claim: the modeling workflow is reproducible, no-leakage, and appropriately evaluated.

Evidence:

- All tables join by `patient_id`.
- Train-only post-triage outcomes `disposition` and `ed_los_hours` are excluded.
- The same preprocessing path is used for validation and final test prediction.
- Numeric, categorical, text, missingness, and clinical-flag features are handled explicitly.
- Evaluation includes holdout, ablations, 5-fold CV, confusion matrix, threshold analysis, and error review.
- Kaggle runtime was 3m 27s.

Primary artifacts:

- `outputs/ablation_metrics.csv`
- `outputs/cv_summary.csv`
- `outputs/error_summary.csv`
- `outputs/submission.csv`

## 3. Documentation And Writeup Quality - 20 points

Claim: the project can be understood and reproduced by a clinician or data scientist.

Evidence:

- Notebook contains an executive summary, rubric map, leakage explanation, clinical interpretation, and final takeaways.
- README explains reproduction and licensing constraints.
- Final writeup is under 2,000 words and follows the required sections: problem, data, method, results, insights, limitations, reproducibility, impact.

Primary artifacts:

- `README.md`
- `KAGGLE_WRITEUP_FINAL.md`
- `FINAL_SUBMISSION_CHECKLIST.md`

## 4. Insight And Findings - 15 points

Claim: the project produces meaningful findings beyond a single score.

Evidence:

- Chief complaint text is shown to be the dominant acuity signal through ablations.
- Missingness of vitals is treated as clinically informative.
- Error review distinguishes dangerous under-triage from high-risk boundary errors.
- Interpretable coefficient table shows clinically plausible signals.
- Subgroup safety analysis checks for concentration of severe under-triage.

Primary artifacts:

- `outputs/top_linear_features.csv`
- `outputs/subgroup_safety.csv`
- `outputs/error_summary.csv`
- `outputs/figures/ablation_metrics.png`

## 5. Novelty And Impact Potential - 10 points

Claim: the work reframes acuity prediction as an auditable safety layer.

Evidence:

- High-risk alert mode gives a practical clinician-in-the-loop use case.
- The project avoids claiming autonomous triage replacement.
- Limitations and deployment requirements are clearly stated.
- The public project package is reproducible without redistributing the raw dataset.

Primary artifacts:

- `KAGGLE_WRITEUP_FINAL.md`
- `outputs/figures/high_risk_threshold_tradeoff.png`
- `outputs/figures/cover_image_560x280.png`

Note: patient-level outputs such as `outputs/error_review.csv` and `outputs/submission.csv` are generated in Kaggle but intentionally excluded from the public repository.
