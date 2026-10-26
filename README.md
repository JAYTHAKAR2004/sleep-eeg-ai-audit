# Sleep EEG Shortcut Audit

**Auditing shortcut effects in sleep-based AI models for cognitive impairment**

> A research project investigating whether sleep EEG models learn meaningful signal patterns or rely on demographic characteristics, hospital metadata, recording dates, missingness patterns, or signal-quality artifacts.

## Overview

Sleep-based machine-learning models may appear to predict cognitive impairment while relying partly on information unrelated to the underlying EEG signal. This project audits that risk by comparing demographic, EEG-only, and combined models, alongside metadata-only baselines and missingness checks.

The current work is a **preliminary research pilot**, not a clinically validated dementia prediction system. The target used in the current analysis is `Cognitive_Impairment`; it should not be interpreted as a confirmed dementia diagnosis.

## Research Questions

1. **EEG signal value:** Does sleep EEG contain predictive information beyond demographic variables?
2. **Metadata shortcuts:** Can hospital site or recording date predict the target, suggesting possible shortcut effects?
3. **Missingness and leakage:** Do missing-value patterns or metadata reveal the label directly?
4. **Evaluation reliability:** Do results remain credible under patient-level evaluation and, eventually, cross-hospital validation?

## Current Dataset and Evaluation

The preliminary demographic analysis contains **1,103 patients**:

- Negative target cases: 1,019
- Positive target cases: 84
- Positive-class prevalence: approximately 7.6%

The EEG pilot contains **38 eligible recordings from one site**, with **35 negative and 3 positive cases**. Six bipolar EEG channels were processed using 30-second epochs.

### Current results

| Experiment | Evaluation size | ROC-AUC | Average Precision |
|---|---:|---:|---:|
| Demographics-only (patient-level LOOCV) | 38 patients, 3 positive | 0.7714 | 0.4879 |
| EEG-only (patient-level LOOCV) | 38 patients, 3 positive | 0.5048 | 0.1124 |
| Combined demographics + EEG (patient-level LOOCV) | 38 patients, 3 positive | 0.6857 | 0.2135 |
| Creation-year-only baseline | Earlier metadata analysis | 0.6725 | — |
| Site-only baseline | Earlier metadata analysis | 0.5195 ROC-AUC; 0.7464 accuracy | — |
| EEG band-power feature group | 38-patient pilot | 0.2190 | 0.0658 |
| EEG amplitude and zero-fraction feature group | 38-patient pilot | 0.5905 | 0.1693 |

**Important:** The EEG pilot has only three positive patients, so its model comparisons are highly uncertain. These values should be treated as exploratory and not as evidence that EEG is definitively uninformative.

## Key Audit Finding: Missingness

In the demographic dataset, `Time_to_Event` missingness aligns exactly with the target in the current cohort:

- All 1,019 negative cases have a missing `Time_to_Event`.
- All 84 positive cases have a non-missing `Time_to_Event`.

A model using this pattern can appear to perform perfectly because the missingness itself reveals the label. This feature must not be treated as a valid predictor until the data-generation process and label definition are investigated. The observed relationship is a **leakage warning**, not proof of the underlying cause.

## EEG Processing Summary

- Six bipolar channels: `F3-M2`, `F4-M1`, `C3-M2`, `C4-M1`, `O1-M2`, `O2-M1`
- Epoch length: 30 seconds
- Eligible recordings processed: 38 of 38
- Total epochs processed: 36,054
- Patient-level feature matrix: 38 rows × 144 EEG features
- Preliminary amplitude review: 18,213 epochs (about 50.5%) exceeded a peak-amplitude threshold of 100 µV.

The 100 µV threshold is currently a **review heuristic**, not a clinically validated artifact detector. It should not be interpreted as proof that flagged epochs are unusable.

## Repository Goals

- Compare demographics-only, EEG-only, and combined models.
- Establish metadata-only baselines for site and recording date.
- Detect potential leakage caused by missingness and metadata.
- Inspect preliminary EEG feature groups and signal-quality indicators.
- Present results through a reproducible analysis pipeline and an interactive React dashboard.

## Planned Application Architecture

The planned dashboard may use:

- **Frontend:** React + Vite
- **UI styling:** Tailwind CSS
- **Charts:** Recharts
- **Backend:** Python + FastAPI
- **Analysis:** Pandas, NumPy, and scikit-learn
- **EEG processing:** Existing Python feature-extraction pipeline

The dashboard is a planned interface; update this section as implementation progresses. Do not claim an API or dashboard feature is complete until it has been implemented and tested.

## Project Status

### Completed / implemented in the current pilot

- Demographic dataset exploration and baseline models.
- Metadata-only site and recording-year baselines.
- Identification of the `Time_to_Event` missingness/leakage warning.
- Initial EEG cohort preparation and patient-label matching.
- Extraction of patient-level EEG features from the pilot recordings.
- Exploratory patient-level LOOCV comparisons.
- Preliminary feature-group and signal-quality analyses.

### Remaining work

- Investigate the origin and meaning of `Time_to_Event` and its missingness.
- Increase the EEG cohort and, if feasible, the number of positive cases.
- Add uncertainty analysis and sensitivity checks suitable for the small sample.
- Validate generalization on a held-out hospital/site.
- Re-run the complete workflow from a clean environment and document exact dependencies.
- Build and test the React dashboard and connect it to the analysis outputs.
- Verify dataset documentation, citations, and all reported metrics before final publication.

## Reproducibility and Data

This README summarizes the current reported results. Exact commands and dependency versions should be added once the project structure and environment files are finalized.

The underlying sleep-study data may be large and subject to the dataset provider's access terms. **Do not commit restricted, sensitive, or very large raw data files to GitHub.** Follow the dataset license and access conditions, and provide instructions for obtaining the data from its official source where permitted. Commit only data that you are authorized to share.

Recommended repository structure:

```text
sleep-eeg-shortcut-audit/
├── README.md
├── data/                  # Keep raw/restricted data out of Git
├── notebooks/              # EDA and experiments
├── src/
│   ├── preprocessing/
│   ├── features/
│   ├── models/
│   └── evaluation/
├── dashboard/              # React application (planned/in progress)
├── api/                    # FastAPI service (planned/in progress)
├── reports/
├── figures/
├── requirements.txt
└── .gitignore
```

Adapt this tree to match the actual repository; remove folders that are not present yet.

## Ethical and Scientific Limitations

- The outcome analyzed is `Cognitive_Impairment`, not necessarily a clinically confirmed dementia diagnosis.
- The EEG pilot is small and contains only three positive patients.
- The pilot recordings are from one site, so cross-hospital generalization has not been tested.
- Results are exploratory and may change substantially with more data, revised cohort definitions, and independent validation.
- The signal-amplitude flag is a preliminary heuristic and requires validation.
- No clinical use or individual diagnosis is supported by this project.

## Disclaimer

This project is for academic research and educational purposes only. It is not a medical device and must not be used to diagnose, screen, or guide treatment for dementia or any other condition.

## Acknowledgment

This work is being developed as an M.Tech research/innovative assignment on auditing shortcut effects in sleep-based AI models. Add the dataset citation, institution details, supervisor acknowledgment, and relevant publications here after verifying the required wording.

---

**Research principle:** A high model score is not enough. We must investigate what information the model uses, whether that information is valid, and whether performance generalizes beyond the data used for development.
