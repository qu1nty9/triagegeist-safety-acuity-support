# Media Gallery Assets

All recommended Media Gallery images are exactly **640 x 360 px** and live in `outputs/media_gallery/`.

Recommended Kaggle Media Gallery order:

1. `outputs/media_gallery/01_cover.png`
   - Primary cover image.
   - Summarizes the project as safety-focused acuity support.

2. `outputs/media_gallery/02_high_risk_threshold_tradeoff.png`
   - Best supporting figure for the clinical story.
   - Shows alert threshold tradeoff: high-acuity recall, precision, and alert burden.

3. `outputs/media_gallery/03_ablation_metrics.png`
   - Best technical figure.
   - Shows how text-enabled models improve both macro-F1 and high-acuity safety.

4. `outputs/media_gallery/04_confusion_matrix.png`
   - Best error-analysis figure.
   - Shows no severe under-triage on the fixed validation split.

5. `outputs/media_gallery/05_target_distribution.png`
   - Optional context figure.
   - Shows class balance across ESI-style acuity levels.

The older `outputs/figures/` images are kept as notebook-generated artifacts, but the Kaggle Media Gallery should use `outputs/media_gallery/`.

