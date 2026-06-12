# Triagegeist: Safety-Focused Acuity Support

This repository contains a Kaggle-ready emergency department triage notebook for the Triagegeist hackathon.

The project is framed as a clinical decision-support prototype, not just a metric-first classifier. It predicts ESI-style `triage_acuity` levels 1-5 and explicitly measures dangerous under-triage of true acuity 1-2 patients.

## Project Structure

- `output/jupyter-notebook/00-triagegeist-safety-baseline.ipynb` - main Kaggle notebook.
- `outputs/ablation_metrics.csv` - validation ablation table.
- `outputs/cv_summary.csv` - 5-fold cross-validation summary.
- `outputs/error_summary.csv` - aggregated validation error summary.
- `outputs/high_risk_operating_points.csv` - high-risk alert thresholds.
- `outputs/figures/` - figures and 560 x 280 cover image.
- `omx_wiki/` - maintained project knowledge base following the LLM-wiki workflow.
- `KAGGLE_WRITEUP_FINAL.md` - final writeup text for Kaggle.
- `PROJECT_DESCRIPTION.md` - project description helper matching the Kaggle template.
- `MEDIA_GALLERY.md` - media gallery recommendations.
- `JUDGING_RUBRIC_MAPPING.md` - explicit mapping to the judging rubric.
- `FINAL_SUBMISSION_CHECKLIST.md` - final asset checklist.

The public repository intentionally excludes raw CSV data and patient-level outputs. Generate those inside Kaggle by running the notebook.

## Method Summary

The notebook:

- joins all tables on `patient_id`;
- excludes train-only outcome columns `disposition` and `ed_los_hours`;
- treats `pain_score == -1` as missing plus a missingness indicator;
- adds clinically interpretable flags for hypotension, hypoxia, tachycardia, fever, and altered mental status;
- vectorizes chief complaint text with TF-IDF;
- trains a balanced sparse linear classifier;
- reports accuracy, macro-F1, ordinal MAE, quadratic weighted kappa, confusion matrix, and severe under-triage.

## Current Validation Results

20% stratified validation split:

| Variant | Accuracy | Macro-F1 | QWK | Severe under-triage |
|---|---:|---:|---:|---:|
| Majority class | 0.3615 | 0.1062 | 0.0000 | 1.0000 |
| Main table only | 0.7805 | 0.7994 | 0.8978 | 0.0810 |
| Main + history, no text | 0.7668 | 0.8004 | 0.8891 | 0.0954 |
| All valid features | 0.9946 | 0.9840 | 0.9975 | 0.0000 |
| No composite scores | 0.9958 | 0.9871 | 0.9980 | 0.0000 |
| Minimal clinical intake | 0.9962 | 0.9882 | 0.9982 | 0.0000 |

High-risk alert mode uses `P(acuity <= 2)`. At a 0.50 threshold, the validation split showed 100% recall for true acuity 1-2, 0 missed high-acuity cases, 1 false alert, and a 20.8% alert rate.

Five-fold cross-validation for the full valid-feature model showed mean accuracy 0.9813, mean macro-F1 0.9564, mean ordinal MAE 0.0188, mean QWK 0.9918, and mean severe under-triage 0.00024. One fold was notably weaker, so the final writeup reports both mean and variance rather than overclaiming a single split.

## Reproducing Locally

Download or attach the Triagegeist dataset through Kaggle. Do not commit or redistribute the raw CSV files outside Kaggle.

Install dependencies:

```bash
pip install -r requirements.txt
```

Run the notebook from the repository root:

```bash
jupyter nbconvert --to notebook --execute --inplace output/jupyter-notebook/00-triagegeist-safety-baseline.ipynb
```

If `nbconvert` is unavailable, open the notebook in Jupyter or Kaggle and run all cells. The notebook writes outputs to `outputs/`.

## Kaggle Submission Assets

- Notebook: `output/jupyter-notebook/00-triagegeist-safety-baseline.ipynb`
- Submission CSV: generated inside Kaggle as `outputs/submission.csv`
- Cover image: `outputs/figures/cover_image_560x280.png`
- Writeup text: `KAGGLE_WRITEUP_FINAL.md`
- Project description helper: `PROJECT_DESCRIPTION.md`
- Media gallery guide: `MEDIA_GALLERY.md`
- Rubric map: `JUDGING_RUBRIC_MAPPING.md`
- Final checklist: `FINAL_SUBMISSION_CHECKLIST.md`

## Limitations

The dataset is synthetic. The results should be interpreted as a reproducible research prototype, not evidence of clinical deployment readiness. Real-world use would require external validation, calibration, prospective evaluation, clinician workflow testing, safety monitoring, and governance.
