# Approach Plan

## Goal

Produce a high-quality Kaggle Notebook and Writeup for Triagegeist that scores well on clinical relevance, technical quality, documentation, insight, and impact.

## Winning Framing

Working title:

`Triagegeist: A Safety-Focused Acuity Support Baseline for Emergency Department Triage`

Core claim:

An AI triage assistant should not simply maximize accuracy. It should minimize dangerous under-triage, remain reproducible, expose its assumptions, and be interpretable enough for clinicians to audit.

## Phase 1: Reproducible Data Foundation

Tasks:

- Load all CSV files.
- Validate row counts and `patient_id` uniqueness.
- Join train/test with chief complaints and patient history.
- Drop train-only outcome features from modeling.
- Convert `pain_score == -1` to missing plus a missingness indicator.
- Add missingness indicators for vitals.
- Add simple clinical flags:
  - hypotension
  - hypoxia
  - tachycardia
  - fever
  - altered mental status

Deliverables:

- Clean preprocessing function shared by training and submission.
- Data profile table in notebook.
- Leakage check section.

## Phase 2: Baseline And Validation

Metrics:

- Accuracy.
- Macro-F1.
- Weighted-F1.
- Ordinal MAE.
- Quadratic weighted kappa.
- Confusion matrix.
- Overall under-triage rate.
- Severe under-triage rate: true acuity 1-2 predicted as 3-5.

Baselines:

- Majority class baseline.
- Main table only.
- Main table plus history, no text.
- Full valid feature model with chief complaint text.
- No composite-score ablation.
- Minimal clinical-intake ablation.

Validation:

- Stratified train/validation split.
- Keep the split fixed with a seed.
- Later: add 5-fold stratified CV if time permits.

## Phase 3: Modeling Roadmap

Initial model:

- Sparse scikit-learn pipeline:
  - numeric: median imputation, missing indicators, scaling
  - categorical: one-hot encoding
  - text: TF-IDF over chief complaint raw text
  - classifier: balanced linear classifier

Next candidates:

- `HistGradientBoostingClassifier` or tree model on structured features only.
- Calibrated one-vs-rest classifier for risk thresholds.
- Ordinal framing:
  - predict `acuity <= 2` as high-risk binary flag
  - predict full 1-5 acuity as secondary output
- Ensemble:
  - structured model
  - text model
  - safety override layer for obvious high-risk vitals

Do not prioritize complex neural models unless there is a clear benefit for the writeup. The rubric rewards clinical credibility and reproducibility more than architectural novelty.

## Phase 4: Interpretability And Insight

Notebook sections:

- Feature coefficient or permutation-style importance for high-acuity predictions.
- Error review of under-triaged cases.
- Compare performance by:
  - age group
  - sex
  - language
  - insurance type
  - arrival mode
  - site
- Show whether severe under-triage concentrates in any subgroup.

## Phase 5: Writeup And Submission

The final Kaggle writeup should be under 2,000 words and map directly to the judging rubric.

Submission package:

- Public Kaggle Notebook.
- Kaggle Writeup.
- Media Gallery images 640 x 360 px.
- Public project link.
- Optional generated submission CSV.

## Risks

- The dataset is synthetic and appears partly near-deterministic when chief complaint text is used.
- Overclaiming clinical deployment value would weaken credibility.
- Using train-only outcomes as features would invalidate the modeling section.
- A metric-only focus will underperform the judging rubric.

## Current Recommendation

Proceed with a safety-focused, reproducible notebook:

1. Establish no-leakage preprocessing.
2. Present ablation table.
3. Pick full valid sparse model as benchmark.
4. Add fairness/safety subgroup analysis.
5. Write up the project as a clinical decision-support proof of concept, not a production medical device.
