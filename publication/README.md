# Publication Package

This folder turns the Triagegeist Kaggle project into reusable publication material.

## Recommended Publication Path

1. **GitHub repository**
   - Current canonical code and artifact repository.
   - Does not include raw dataset files or patient-level outputs.

2. **Zenodo release**
   - Create a GitHub release.
   - Connect the repository to Zenodo.
   - Use `.zenodo.json` metadata.
   - Zenodo will mint a DOI for the release.

3. **Public article**
   - Use `HABR_ARTICLE_RU.md` for a Russian-language article.
   - A shorter English Medium/Substack version can be derived from it.

4. **Preprint**
   - Use `PREPRINT_DRAFT.md` as the starting point.
   - Before arXiv submission, add a stronger related-work section and external references.

## Files

- `PREPRINT_DRAFT.md` - arXiv-style manuscript draft.
- `HABR_ARTICLE_RU.md` - Russian public article draft.
- `RELEASE_NOTES.md` - release notes for GitHub/Zenodo.

## Publication Positioning

Preferred project framing:

> A reproducible safety-focused clinical AI case study for emergency triage.

Avoid framing this as:

- a deployable medical device;
- proof of real-world clinical performance;
- a new state-of-the-art model;
- a benchmark result on real patient data.

## Data Constraint

The raw Triagegeist dataset is not redistributed in this repository. The notebook expects users to access the dataset through Kaggle according to the competition terms.

