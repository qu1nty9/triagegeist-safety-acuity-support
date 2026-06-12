# Final Submission Checklist

Use this checklist immediately before submitting the Triagegeist Kaggle Writeup.

## Required Kaggle Assets

- [ ] Kaggle Notebook is public.
- [ ] Kaggle Notebook runs end-to-end in Kaggle.
- [ ] Kaggle Notebook output contains `outputs/submission.csv`.
- [ ] Kaggle Writeup is submitted through the competition Writeups tab.
- [ ] Media Gallery cover image is attached: `outputs/media_gallery/01_cover.png`.
- [ ] All Media Gallery images are 640 x 360 px.
- [ ] Public project link is attached.
- [ ] The public project link does not expose raw `triagegeist-data/` CSV files.
- [ ] The public project link does not expose patient-level `outputs/submission.csv` or `outputs/error_review.csv`.

## Final Notebook Checks

- [x] Kaggle runtime verified: 3m 27s.
- [x] 5-fold CV kept in notebook because runtime is acceptable.
- [x] No train-only outcomes are used as model features.
- [x] Notebook includes leakage explanation.
- [x] Notebook includes ablations.
- [x] Notebook includes high-risk threshold analysis.
- [x] Notebook includes error review.
- [x] Notebook includes subgroup safety check.
- [x] Notebook generates figures and final submission.

## Final Writeup Checks

- [x] Uses clinical framing: under-triage safety.
- [x] Explains dataset is synthetic.
- [x] Reports holdout and CV metrics.
- [x] Reports severe under-triage.
- [x] Explains high-risk alert workflow.
- [x] Includes limitations and deployment caveats.
- [x] Stays under 2,000 words.

## Files To Attach Or Reference

- Notebook: `output/jupyter-notebook/00-triagegeist-safety-baseline.ipynb`
- Writeup text: `KAGGLE_WRITEUP_FINAL.md`
- Media Gallery cover image: `outputs/media_gallery/01_cover.png`
- Public project README: `README.md`
- Final output in Kaggle: `outputs/submission.csv`

## Do Not Publish

- Do not publish `triagegeist-data/`.
- Do not publish patient-level outputs such as `outputs/submission.csv` or `outputs/error_review.csv`.
- Do not publish private Kaggle-only links as the project link.
- Do not claim clinical deployment readiness.
