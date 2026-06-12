# Triagegeist Task Brief

## Objective

Build an AI-powered tool or analytical system that assists clinicians in emergency department triage.

The most direct path for this folder is an acuity prediction notebook:

- Input: structured intake data, vitals, demographics, chief complaint text, and comorbidity history.
- Target: `triage_acuity`, ESI-style level 1-5.
- Clinical focus: reduce missed high-acuity patients, especially true ESI 1-2 cases that would be predicted as 3-5.

## Required Submission

The local competition description requires:

- Public Kaggle Notebook that runs end-to-end without errors.
- Kaggle Project Writeup, max 2,000 words.
- Media Gallery image, currently prepared as 640 x 360 px.
- Attached public notebook.
- Attached public project link, such as a public repository or working demo.

## Judging Rubric

Total: 100 points.

- Clinical relevance: 25.
- Technical quality: 30.
- Documentation and writeup quality: 20.
- Insight and findings: 15.
- Novelty and impact potential: 10.

## Strategic Implication

The best submission should not be framed as a generic classifier. It should be framed as a clinically useful triage support study:

- A clear problem: under-triage is dangerous because urgent patients may wait too long.
- A reproducible model: transparent feature processing, leakage checks, and robust validation.
- A safety evaluation: severe under-triage rate for true acuity 1-2.
- Interpretability: show which clinical signals drive high-acuity predictions.
- Limitations: synthetic data, no prospective validation, no deployment claim.

## Public Repository Constraint

The dataset description states that redistribution outside Kaggle is prohibited. A public project link should include code, notebook, figures, README, and writeup, but should not include raw files under `triagegeist-data/`.
