# Triagegeist: Safety-Focused Acuity Support for Emergency Triage

## Clinical Problem Statement

Emergency triage is a safety-critical workflow. Nurses and physicians must identify which patients need immediate care using incomplete information, noisy symptoms, and limited time. In this setting, the most important model failure is not simply an incorrect class label. It is **under-triage**: assigning a truly urgent patient to a less urgent category and potentially delaying treatment.

This project targets a narrow workflow gap: surfacing likely high-acuity patients for immediate clinician review. The model predicts `triage_acuity` levels 1-5, where 1 is most urgent and 5 is least urgent, but the evaluation focuses on whether true acuity 1-2 patients are mistakenly sent into lower-priority classes 3-5.

I frame the system as an auditable safety layer, not an autonomous triage replacement. Its practical output is a high-risk alert based on `P(acuity <= 2)`.

## Approach and Methodology

I used the synthetic Triagegeist emergency department dataset provided for the hackathon: 80,000 training records, 20,000 test records, structured intake fields, vitals, demographics, free-text chief complaint narratives, and binary comorbidity history flags.

All tables are joined by `patient_id`. I explicitly exclude `disposition` and `ed_los_hours` from modeling because they are train-only post-triage outcomes. A triage support model must use triage-time information, not downstream outcomes unavailable at the moment of decision.

The notebook uses one shared preprocessing pipeline for validation and final test prediction:

- numeric vitals are median-imputed with missingness indicators;
- `pain_score == -1` is treated as not recorded;
- categorical intake fields are one-hot encoded;
- chief complaint text is vectorized with TF-IDF over unigrams and bigrams;
- simple clinical flags are added for hypotension, hypoxia, tachycardia, fever, and altered mental status.

The primary model is a balanced sparse linear classifier using `SGDClassifier(loss="log_loss")`. This is intentionally pragmatic: it handles structured and text features, runs quickly in Kaggle, and allows inspection of learned signals.

Evaluation includes holdout validation, ablations, 5-fold cross-validation, confusion matrix review, high-risk threshold analysis, subgroup safety checks, and error review. The key clinical metric is severe under-triage: true acuity 1-2 predicted as 3-5.

## Results and Findings

The full notebook ran in Kaggle in **3 minutes 27 seconds**, including ablations, cross-validation, error review, figure generation, and final output creation.

The majority-class baseline achieved 0.3615 accuracy and 0.1062 macro-F1. Structured intake data alone was useful but not sufficient for safety: the main-table-only model reached 0.7805 accuracy and 0.7994 macro-F1, while severely under-triaging 8.1% of true acuity 1-2 cases.

The full valid-feature model achieved:

- holdout accuracy: 0.9946;
- holdout macro-F1: 0.9840;
- holdout quadratic weighted kappa: 0.9975;
- holdout severe under-triage: 0.0000.

Five-fold cross-validation was strong but not perfectly uniform:

- mean accuracy: 0.9813;
- mean macro-F1: 0.9564;
- mean quadratic weighted kappa: 0.9918;
- mean severe under-triage: 0.00024.

The fixed validation split had 87 total class errors and zero severe under-triage errors. The main boundary mistakes were acuity 1 predicted as 2 and acuity 2 predicted as 1. Clinically, these remain in the high-risk zone and are less dangerous than missing an urgent patient entirely.

The high-risk alert mode is the strongest practical result. At threshold 0.50, the model flagged 20.8% of validation patients, captured 100% of true acuity 1-2 patients, missed 0 high-acuity cases, and produced 1 false alert among 16,000 validation records.

Chief complaint text was the dominant signal in this synthetic dataset. Text-enabled models were much stronger than structured-only baselines, suggesting that triage narratives encode urgency terms such as shock, overdose, meningitis, anaphylaxis, collapse, and similar high-risk presentations.

## Limitations and Future Work

This is a research prototype, not a deployable medical device. The dataset is synthetic, text patterns may be cleaner than real triage notes, and internal validation is not a substitute for prospective clinical evaluation.

Before real-world use, this approach would require external validation across hospitals, calibration monitoring, subgroup safety audits, prospective silent-mode testing, workflow evaluation, governance review, and explicit human-in-the-loop escalation rules.

Future work should test robustness on real triage notes, calibrate high-risk alert thresholds by hospital capacity, and evaluate whether clinician review of flagged patients reduces dangerous delays without creating excessive alert fatigue.

## Reproducibility Notes

Dataset: the synthetic Triagegeist Kaggle dataset attached to the hackathon. Raw files are not redistributed in the public repository because the dataset license prohibits redistribution outside Kaggle.

The Kaggle notebook runs end-to-end without errors, with runtime verified at 3 minutes 27 seconds. It fixes `SEED = 42`, joins all tables by `patient_id`, excludes train-only outcomes, writes validation and cross-validation artifacts, generates figures, and creates final outputs.

Core packages: pandas, numpy, scikit-learn, matplotlib, and Jupyter. No GPU is required.

