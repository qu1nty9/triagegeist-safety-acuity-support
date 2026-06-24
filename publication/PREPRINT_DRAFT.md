# A Safety-Focused Evaluation Framework for Emergency Triage Acuity Prediction on Synthetic ED Data

**Authors:** qu1nty9  
**Status:** preprint draft / research artifact manuscript  
**Repository:** https://github.com/qu1nty9/triagegeist-safety-acuity-support

## Abstract

Emergency department triage is a safety-critical workflow where clinicians must rapidly prioritize patients under incomplete information and operational pressure. Conventional model evaluation for triage acuity prediction often emphasizes aggregate multiclass performance, but the most clinically important failure mode is asymmetric: under-triage of high-acuity patients can delay urgent care. This work presents a reproducible safety-focused evaluation framework for synthetic emergency triage acuity prediction using the Triagegeist dataset. The pipeline predicts ESI-style acuity levels 1-5 from structured intake fields, vitals, demographics, chief complaint text, and comorbidity flags while explicitly excluding post-triage outcome leakage. In addition to accuracy, macro-F1, ordinal MAE, and quadratic weighted kappa, the evaluation reports severe under-triage, defined as true acuity 1-2 predicted as 3-5. A high-risk alert mode based on `P(acuity <= 2)` is evaluated as a clinician-in-the-loop safety layer. In the Kaggle-verified run, the full valid-feature model achieved 0.9946 holdout accuracy, 0.9840 macro-F1, and 0.9975 quadratic weighted kappa, with zero severe under-triage errors on the fixed validation split. Five-fold cross-validation showed mean macro-F1 0.9564 and mean severe under-triage 0.00024. At an alert threshold of 0.50, the model captured 100% of high-acuity validation cases with one false alert among 16,000 validation records. Because the dataset is synthetic, these results should be interpreted as a reproducible case study rather than clinical validation.

## 1. Introduction

Emergency departments operate under uncertainty, crowding, and time pressure. Triage systems such as Emergency Severity Index (ESI)-style acuity scoring are intended to prioritize patients according to urgency, but triage remains vulnerable to variability, incomplete observations, and cognitive overload. In this environment, the consequences of model errors are not symmetric. Over-triage can increase workload and resource use; under-triage can delay care for patients who need immediate attention.

Most machine-learning summaries for classification tasks emphasize aggregate metrics such as accuracy, macro-F1, or area under the curve. Those metrics are informative but incomplete for triage. A clinically useful triage assistant should be evaluated around the failure mode that matters most: high-acuity patients mistakenly assigned to low-priority categories.

This project develops a reproducible evaluation framework for synthetic ED triage acuity prediction. The model is deliberately simple and auditable: a sparse linear classifier over structured features and chief complaint text. The primary contribution is not a novel algorithm. The contribution is a safety-first framing:

1. exclude train-only post-triage outcomes to prevent leakage;
2. report ablations to show which data sources drive safety;
3. evaluate severe under-triage separately from aggregate accuracy;
4. treat `P(acuity <= 2)` as a high-risk alert for clinician review;
5. provide reproducible artifacts, figures, and code.

## 2. Dataset

The project uses the synthetic Triagegeist emergency department triage dataset. It includes 80,000 training records and 20,000 test records, with structured intake fields, vitals, demographics, free-text chief complaint narratives, and binary comorbidity history flags. The target is `triage_acuity`, an ESI-style ordinal label from 1 to 5, where 1 is most urgent and 5 is least urgent.

All tables are joined by `patient_id`. The public repository intentionally does not redistribute raw CSV files because the dataset is governed by Kaggle competition terms. Users must access the data through Kaggle.

Two train-only fields, `disposition` and `ed_los_hours`, are explicitly excluded from modeling. They are post-triage outcomes and are not available in the test set. Including them would create outcome leakage and would not represent a clinically realistic triage-time decision support model.

## 3. Methods

### 3.1 Preprocessing

The notebook uses one preprocessing path for validation, cross-validation, and final test-set prediction. Numeric vital signs are median-imputed, and missingness indicators are retained because missing observations can reflect workflow and acuity patterns. The `pain_score` value of `-1` is treated as not recorded. Categorical intake fields are one-hot encoded. Free-text chief complaints are vectorized with TF-IDF over unigrams and bigrams.

Simple clinical flags are added for hypotension, hypoxia, tachycardia, fever, and altered mental status. These features are not intended to replace clinical scoring systems; they provide transparent signals that can be audited by readers.

### 3.2 Model

The primary model is a balanced sparse linear classifier using `SGDClassifier(loss="log_loss")`. This choice is pragmatic. It handles the mix of sparse text and structured features, runs quickly in Kaggle, and allows inspection of learned coefficients. More complex models could improve some metrics, but a transparent baseline is better aligned with a safety-focused clinical case study.

### 3.3 Evaluation

The evaluation includes:

- fixed 20% stratified validation split;
- 5-fold stratified cross-validation;
- majority-class and feature ablation baselines;
- confusion matrix;
- high-risk alert threshold analysis;
- subgroup safety check;
- error review.

Metrics include accuracy, macro-F1, weighted-F1, ordinal MAE, quadratic weighted kappa, under-triage rate, and severe under-triage. Severe under-triage is defined as true acuity 1-2 predicted as 3-5.

## 4. Results

The full notebook ran end-to-end in Kaggle in 3 minutes 27 seconds.

The majority-class baseline achieved 0.3615 accuracy and 0.1062 macro-F1. Structured intake data alone reached 0.7805 accuracy and 0.7994 macro-F1, but still severely under-triaged 8.1% of true acuity 1-2 cases. This demonstrates why aggregate performance alone is insufficient.

The full valid-feature model achieved:

| Metric | Holdout value |
|---|---:|
| Accuracy | 0.9946 |
| Macro-F1 | 0.9840 |
| Quadratic weighted kappa | 0.9975 |
| Severe under-triage | 0.0000 |

Five-fold cross-validation showed:

| Metric | Mean | Std |
|---|---:|---:|
| Accuracy | 0.9813 | 0.0281 |
| Macro-F1 | 0.9564 | 0.0562 |
| Quadratic weighted kappa | 0.9918 | 0.0114 |
| Severe under-triage | 0.00024 | 0.00054 |

The fold variance indicates that a single split can overstate model stability. Reporting cross-validation variance improves the credibility of the safety analysis.

## 5. High-Risk Alert Mode

The most plausible operational workflow is not automatic replacement of triage staff. Instead, the model can provide a high-risk alert. The alert score is `P(acuity <= 2)`. If the score exceeds a threshold, the patient is surfaced for immediate clinician review.

At threshold 0.50 on the fixed validation split:

- alert rate: 20.8%;
- high-acuity recall: 100%;
- missed high-acuity cases: 0;
- false alerts: 1 among 16,000 validation records.

This result is clinically interpretable: the model can act as a safety net that minimizes missed urgent patients while keeping alert burden bounded.

## 6. Error Review and Interpretability

The fixed validation split had 87 total class errors and zero severe under-triage errors. The most common boundary errors were true acuity 1 predicted as 2 and true acuity 2 predicted as 1. These remain within the high-risk region and are less dangerous than assigning true acuity 1-2 patients to lower-priority classes.

Coefficient inspection showed clinically plausible signals for high acuity: unresponsive or altered mental status, high pain, respiratory abnormalities, fever, shock-related language, overdose, meningitis-like terms, anaphylaxis-like terms, and collapse-related complaints.

Chief complaint text was the dominant signal in this synthetic dataset. This is an important finding, but it is also a limitation: synthetic complaint narratives may encode severity more cleanly than real triage notes.

## 7. Limitations

This work is not clinical validation. The data are synthetic, the model is internally evaluated, and the text distribution may be more regular than real emergency department documentation. The project does not evaluate calibration under real hospital prevalence shifts, clinician interaction effects, alert fatigue, prospective outcomes, or external generalization.

The model should not be deployed as a medical device. Before any clinical use, the workflow would require external validation, prospective silent-mode testing, subgroup safety monitoring, calibration analysis, governance review, and explicit human-in-the-loop escalation policies.

## 8. Reproducibility

The public repository contains the notebook, aggregated metrics, figures, media assets, writeup materials, and metadata. Raw dataset files and patient-level outputs are intentionally excluded. Users can reproduce the run by attaching the Triagegeist dataset in Kaggle and running the notebook end-to-end.

Core packages are pandas, numpy, scikit-learn, matplotlib, and Jupyter. No GPU is required.

## 9. Conclusion

This project demonstrates a safety-focused approach to emergency triage acuity prediction on synthetic data. The central idea is that a clinically meaningful AI assistant should be judged not only by multiclass accuracy, but by whether it avoids dangerous under-triage and supports auditable clinician review. The resulting artifact is a reproducible case study that can serve as a template for future triage AI evaluation, provided its synthetic-data limitations are kept explicit.

