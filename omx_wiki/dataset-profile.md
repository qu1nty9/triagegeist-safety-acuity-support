# Dataset Profile

## Files

All tables join on `patient_id`.

| File | Rows | Columns | Role |
|---|---:|---:|---|
| `train.csv` | 80,000 | 40 | Main training data with target and train-only outcomes |
| `test.csv` | 20,000 | 37 | Test data without target |
| `chief_complaints.csv` | 100,000 | 3 | Free-text complaint narratives |
| `patient_history.csv` | 100,000 | 26 | Binary comorbidity flags |
| `sample_submission.csv` | 20,000 | 2 | Required submission shape |

## Target

`triage_acuity`: ESI level 1-5, where 1 is most urgent and 5 is least urgent.

Holdout-relevant target distribution in train:

| Acuity | Count | Rate |
|---:|---:|---:|
| 1 | 3,222 | 4.03% |
| 2 | 13,439 | 16.80% |
| 3 | 28,921 | 36.15% |
| 4 | 23,020 | 28.78% |
| 5 | 11,398 | 14.25% |

## Leakage And Compatibility Notes

Train-only columns:

- `triage_acuity`: target.
- `disposition`: outcome after care.
- `ed_los_hours`: outcome after care.

`disposition` and `ed_los_hours` are not present in test. They must be excluded from any model intended for submission. They can be used only for secondary exploratory analysis if clearly labeled as post-triage outcomes.

## Missingness

Missing values occur in:

- `systolic_bp`
- `diastolic_bp`
- `mean_arterial_pressure`
- `pulse_pressure`
- `shock_index`
- `respiratory_rate`
- `temperature_c`

`pain_score` uses `-1` for not recorded and should be handled as missing plus a missingness indicator.

Missingness is informative:

- Vitals are almost never missing for acuity 1-3.
- Missing systolic/diastolic/MAP/pulse pressure/shock index is around 12% for acuity 4-5.
- Respiratory rate missingness is around 9% for acuity 4-5.

## Early Clinical Signals

Mean values by acuity show a strong clinical gradient:

- Acuity 1 has much lower systolic BP, higher heart rate, higher respiratory rate, higher temperature, lower SpO2, lower GCS, higher pain score, higher shock index, and higher NEWS2.
- Mental status is highly discriminative: true acuity 1 includes many unresponsive and drowsy patients; acuity 5 is mostly alert.
- Arrival mode is not strongly discriminative in the first profile.

## Train/Test Shift

Initial mean-shift checks showed very small standardized shifts between train and test for numeric columns. No obvious distribution shift was detected from the quick profile.

