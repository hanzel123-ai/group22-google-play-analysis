# Beyond Raw Ratings: Interpreting Google Play App Performance

**Group 22 · CA6002 — AI UX & Data Visualisation Design Principles**

This repository presents a collaborative group assignment investigating how Google Play Store metadata can explain application ratings. We follow a visually guided analytical workflow covering dataset exploration and preprocessing, regression model benchmarking, random forest hyperparameter tuning, learning curve diagnosis, and prediction error analysis.

Visualisations—including error bar charts and hyperparameter comparison tiles in the presentation—support model selection and the interpretation of model complexity and generalisation. Their design draws on visual perception and Gestalt principles to communicate patterns and trade-offs clearly. Our findings suggest that public store metadata has limited explanatory power for app ratings. This work demonstrates how visualisation supports interpretable regression modelling and communicates its limitations alongside predictive performance.

**This assignment was completed collaboratively by Group 22. The materials represent the group's collective work, rather than the work of a single repository owner.** See [group attribution](docs/GROUP_ATTRIBUTION.md).

## Start here

- [Group presentation](presentation/group22_assignment.pptx) — original 19-slide deck, preserved unchanged.
- [Exploration notebook](notebooks/google_play_eda.ipynb) — supplied EDA and preprocessing notebook, with outputs cleared and two execution-order issues corrected.
- [Review notes](docs/REVIEW_NOTES.md) — differences between the notebook, presentation, and v5 results.
- [Chinese upload guide](docs/UPLOAD_GUIDE_ZH.md) — instructions for publishing this folder manually.

## Research questions

1. How are ratings distributed across app types and categories?
2. How much predictive information is available in public metadata?
3. Where does the model fail, and how should ratings be interpreted alongside reviews and installs?

## Dataset and workflow

The supplied Google Play Store dataset contains **10,841 records and 13 variables**. The v5 results report one malformed row and 483 exact duplicate rows removed, leaving **10,357 cleaned records**. Of these, **8,892 records have observed ratings** and are used for modelling. A separate dataset of **8,196 unique rated apps** supports app-level storytelling; these counts refer to different analytical units.

The workflow conditions reviews, installs, prices, sizes and dates, explores rating patterns, and compares regression models using the supplied tables labelled as grouped cross-validation. It examines log transforms for long-tailed variables, tunes random forest complexity, and uses learning curves and segmented error analysis to understand generalisation. The original training implementation was not included in the supplied files, so its exact split and fitting procedures have not been independently audited.

## Main results

The following results are read from the supplied v5 tables, not from a new training run.

| Model | 5-fold grouped CV RMSE | RMSE standard deviation |
| --- | ---: | ---: |
| HistGradientBoosting | 0.4761 | 0.0220 |
| Random Forest | 0.4781 | 0.0196 |
| Ridge | 0.4797 | 0.0184 |
| Linear Regression | 0.4797 | 0.0184 |
| Extra Trees | 0.4804 | 0.0212 |
| KNN | 0.4880 | 0.0197 |

HistGradientBoosting has the lowest benchmark mean RMSE. The group selected Random Forest for further analysis; it should not be described as the outright benchmark winner. The CV standard deviations describe fold variation, not confidence intervals.

The CV-selected random forest uses **max_depth = 10** and **min_samples_leaf = 7**. Its final holdout results are:

| Metric | Value |
| --- | ---: |
| MAE | 0.3317 |
| RMSE | 0.4946 |
| R² | 0.1558 |
| Training records | 7,094 |
| Test records | 1,798 |

Source: [final holdout metrics](results/slide_tables/S11_final_rf_holdout_metrics.csv). The separate `verified_holdout_model_comparison.csv` contains another Random Forest result; see the review notes before combining those experiments.

![Model benchmark](figures/S08_model_benchmark_rmse_errorbar.png)

![Holdout predictions](figures/S11_actual_vs_predicted_hexbin.png)

## Interpretation

The final model explains about 15.6% of the holdout variation under this evaluation. Predictions concentrate near the middle of the rating distribution. For apps rated at or below 3.5, the observed mean is about 2.99 while the predicted mean is about 4.03, with MAE around 1.04.

Reviews and installs provide important predictive signals, but predictive importance does not establish causation. Low-review and low-install groups have larger errors. Perfect five-star ratings are concentrated among apps with little review evidence in this dataset. The group therefore recommends interpreting **Rating, Reviews and Installs together** as satisfaction, evidence strength and market coverage.

## Repository layout

```text
data/raw/                  Supplied source CSV
data/processed/            v5 cleaned and derived datasets
notebooks/                 Supplied exploration notebook, lightly repaired
results/slide_tables/      Tables used for presentation evidence
results/model_experiments/ Additional experiment exports

figures/                   Supplied PNG figures
presentation/              Original group PowerPoint
docs/                      Attribution, provenance and review notes
```

## Run the exploration notebook

Use Python 3.10 or newer. From the repository root:

```bash
python -m venv .venv
# Windows PowerShell
.venv\Scripts\Activate.ps1
# macOS/Linux: source .venv/bin/activate
python -m pip install -r requirements.txt
jupyter notebook notebooks/google_play_eda.ipynb
```

Run cells in order. The notebook reads `data/raw/googleplaystore.csv`; it also supports being launched from the `notebooks/` directory. It explores and conditions data but **does not regenerate the v5 model metrics or figures**. Its outputs were cleared, and execution has not been verified in this preparation environment. Dependencies are not version-locked.

## Provenance and sharing

The supplied v5 archive is the source for data, tables and figures. [Source manifest](docs/source_manifest.csv) records their original paths and SHA-256 hashes. The PPT was copied unchanged. The internal presentation handoff DOCX is excluded from this repository package.

The exact original dataset download URL and redistribution licence were not established from the supplied files. The CSV files are retained here as part of the local assignment package; confirm their redistribution terms before publishing them. No software or dataset licence has been assigned by this repository preparation. The original deck contains team names and university email addresses on its title slide.

## Source acknowledgement

This Group 22 assignment draws on and adapts content from data13's Kaggle notebook [Machine Learning Model to Predict App Rating (94%)](https://www.kaggle.com/code/data13/machine-learning-model-to-predict-app-rating-94). We acknowledge this reference for the assignment's analysis and modelling approach. Credit for borrowed content belongs to the original author; the assignment should not be presented as wholly original work by Group 22. The exact extent of reuse has not been mapped to individual code cells, text or figures. The 94% in the source title is not a performance claim for this project.
