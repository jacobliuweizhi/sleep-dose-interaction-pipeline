# Sleep–Dose Interaction Pipeline

A de-identified analysis scaffold that mirrors the workflow used in the study “Exercise Frequency Is Associated With Greater Susceptibility to Sleep Disruption From Bedtime Smartphone Use: A Multi-University Cross-Sectional Study.”

This repository helps readers understand the full analysis path for phone use at bedtime, exercise dose, chronotype, sleep quality, and psychological symptoms. It is intentionally not a drop-in runnable package. Sensitive paths, private mappings, and raw data are not included.

---

## Highlights

* Scope: 4,730 Chinese university students during a non-examination period
* Core question: does sleep quality mediate the link between bedtime smartphone use and anxiety or depression, and does weekly exercise dose moderate these associations with attention to chronotype
* Main results: sleep quality mediates the associations of bedtime smartphone use with anxiety and depression. Higher weekly exercise dose amplifies the adverse association between bedtime smartphone use and sleep quality. Frequency is the dominant contributor within the composite exercise dose. Effects are stronger among evening types.

---

## What this repo provides

* An analysis skeleton that documents each step: standardization, interaction modeling, FDR control, simple slopes and optional Johnson–Neyman search, sensitivity analyses, optional SEM and optional GAM, and a transparent description of outputs and figure conventions
* Clean captions and table reading guides that align with journal expectations
* Supplementary captions for STROBE reporting, SEM paths, mediation decomposition, and interpretation points
* A license model that allows reading and learning while discouraging copy-paste reuse

---

## Directory layout

Suggested layout when you initialize the repo.

```
/src
  analysis_skeleton.py        # the de-identified workflow scaffold

/docs
  captions_and_reading_guide.md
  figures_overview.md

/supplement
  S1_STROBE_checklist.docx
  S2_SEM_paths.xlsx
  S3_mediation_decomposition.xlsx
  S4_mediation_interpretation_keys.xlsx

/LICENSE                      # AGPL-3.0-or-later for code
/LICENSE-docs                 # CC BY-NC-ND 4.0 for docs and figures
/NOTICE
```

> The file names for S2 to S4 correspond to your private spreadsheets. If you do not wish to commit the actual spreadsheets, keep the placeholders and publish derived CSV summaries only.

---

## Quick start for readers

1. Open `/src/analysis_skeleton.py` to see the end-to-end structure.
2. Review `/docs/captions_and_reading_guide.md` to understand how to read model tables and figures.
3. If you need to adapt the workflow privately, implement a secure `Workflow.load()` that returns a DataFrame and define private EF, ED, EI mappings. Do not commit these to the repository.

---

## Methods overview

### Variables and construction

* **Bedtime smartphone use**: minutes per night during the past week.
* **Exercise dose**: multiplicative composite of intensity, duration, and frequency. The exact value anchors are intentionally omitted in the public code.
* **Chronotype**: MEQ score.
* **Sleep quality**: PSQI global score.
* **Psychological symptoms**: SAS and SDS scores.
* **Covariates**: sex, degree program, major category, study time, academic stress, family economic status, interpersonal relationships, and institution where applicable.

All continuous predictors are Z-standardized before modeling. Categorical covariates are modeled with reference groups as indicated in the result tables.

### Main models

* **Sleep model**:
  `PSQI_z ~ Phone_z * MEQ_z * Dose_z + covariates`
  Fitted with OLS or MixedLM depending on the presence of a grouping factor such as university.

* **Psychological models**:
  `SAS_z ~ Phone_z * MEQ_z * Dose_z + PSQI_z + covariates`
  `SDS_z ~ Phone_z * MEQ_z * Dose_z + PSQI_z + covariates`

### Sensitivity models

* Replace `Dose_z` with `Dose_zsum` which is the z-score sum of intensity, duration, and frequency.
* Optional robustness checks with alternative encodings for exercise components.

### Multiple testing

* Within each model block, p values are adjusted using Benjamini–Hochberg false discovery rate control.

### Simple slopes and optional Johnson–Neyman

* The slope of `Phone_z` on `PSQI_z` is evaluated across a grid of `Dose_z`.
* Where variance–covariance information is available, a Johnson–Neyman search identifies moderator values at which the `Phone_z` effect becomes significant.

### Optional components

* **SEM**: compact path models with standardized estimates to examine indirect pathways through `PSQI`.
* **GAM**: smooth relationships for exploratory visualization, including a minimum effective dose estimate where relevant.
* **Dose decomposition**: log-domain identity for intensity, duration, and frequency, followed by a share or Shapley-style apportionment of the composite dose contribution.

---

## Outputs and how to read them

The scaffold is designed to produce publication-ready CSVs and vector figures when executed in a private environment. The public code shows file names and structure only.

### Key output tables

* `T_main_models_FDR.csv`
  Pooled tidy results for all primary and sensitivity models
  Columns include model, term, estimate, standard error, p value, and FDR-adjusted p value

* `T_simple_slopes_continuous.csv`
  Simple slopes of `Phone_z` on `PSQI_z` across the `Dose_z` grid with confidence bands if available

* `T_johnson_neyman_phone_on_PSQI.csv`
  Johnson–Neyman bounds for the `Phone_z` effect across `Dose_z` where estimable

* `T_sem_paths.csv` and `T_sem_fit.csv`
  Standardized path coefficients and model fit indices when SEM is enabled privately

* `rev_T_mediation_points.csv`
  Point estimates for a, b, c′, indirect effect a×b, approximate total effect, and proportion mediated

* `rev_T_dimension_shapley_logDoseZ.csv`
  Share of composite dose contribution by intensity, duration, and frequency in the log domain

### How to interpret common terms

* `Phone_z`: one SD increase in bedtime smartphone use
* `MEQ_z`: one SD increase in morningness preference
* `Dose_z`: one SD increase in weekly exercise dose
* `Phone_z:MEQ_z`: moderation by chronotype
* `Phone_z:Dose_z`: moderation by exercise dose
* `Phone_z:MEQ_z:Dose_z`: three-way interaction across phone use, chronotype, and exercise dose
* Positive coefficients on PSQI indicate worse sleep quality. Positive coefficients on SAS or SDS indicate higher symptom burden.

---

## Supplementary materials and captions

* **Supplementary Table S1** STROBE checklist for cross-sectional reporting.
  Completeness of reporting for design, setting, participants, variables, bias, study size, quantitative variables, statistical methods, results, discussion, and funding.

* **Supplementary Table S2** Standardized path coefficients from structural equation models.
  Standardized estimates for paths among bedtime smartphone use, chronotype, exercise dose, sleep quality, anxiety, and depression. Fit indices are reported when available.

* **Supplementary Table S3** Decomposition of direct and indirect effects in mediation models.
  a, b, c′, indirect effect a×b, approximate total effect, and proportion mediated for SAS and SDS outcomes.

* **Supplementary Table S4** Key points for mediation model interpretation.
  Reading guide for indirect-only vs partial mediation, proportion mediated, and practical interpretation for student populations.

---

## Abbreviations

PSQI Pittsburgh Sleep Quality Index
MEQ Morningness–Eveningness Questionnaire
SAS Zung Self-Rating Anxiety Scale
SDS Zung Self-Rating Depression Scale
EF Exercise Frequency
ED Exercise Duration
EI Exercise Intensity
SEM Structural Equation Modeling
GAM Generalized Additive Model
OLS Ordinary Least Squares
FDR False Discovery Rate
CI Confidence Interval
SD Standard Deviation
MET Metabolic Equivalent of Task
JN Johnson–Neyman

---

## Data and ethics

* **Data availability**: raw data are not included. De-identified aggregates may be shared upon reasonable request and require ethics approval.
* **Ethics**: approved by the Ethics Committee of West China Hospital, Sichuan University, Approval No. 2022(830).
* **Funding**: Science and Technology Innovation 2030 program of the Ministry of Science and Technology of the People’s Republic of China, Project No. 2021ZD0201901 and Sub-project No. 2021ZD0201901-5.

---

## License

* **Code**: GNU Affero General Public License v3.0 or later
  SPDX: AGPL-3.0-or-later
* **Documentation and figures**: Creative Commons BY-NC-ND 4.0
  SPDX: CC-BY-NC-ND-4.0
* **Data**: not included

This setup allows reading and learning from the workflow. Redistribution of modified or service-based derivatives requires compliance with copyleft obligations for code. Non-commercial, no-derivatives terms apply to documentation and figures.

---

## How to cite

If you reference this repository or reuse ideas from the workflow, please cite:

Liu W, Lei K, Chen Y, Li S, Li J, Tang X, Qiu C. Exercise Frequency Is Associated With Greater Susceptibility to Sleep Disruption From Bedtime Smartphone Use: A Multi-University Cross-Sectional Study.

---

## Contact

Correspondence: Chen Qiu, [cqiu@scu.edu.cn](mailto:cqiu@scu.edu.cn)
First author: Weizhi Liu, [weizhi_L_sportsmed@163.com](mailto:weizhi_L_sportsmed@163.com)

---

## Reproducibility note

This repository is designed for transparency without exposing private or identifiable content. To run the pipeline on your own data, implement a private loader, supply your own variable mappings, and keep all sensitive resources outside of version control.
