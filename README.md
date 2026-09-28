# NKPI 7.4 — SOURCE progression domains

Replaces the previous A/B/C direction count with **SOURCE progression domains · Both eyes**, in the same closed-by-default dropdown at the bottom. The existing NKPI calculation, model coefficients, extractor, maps, projections and input workflow are preserved.

## The new panel

| Biological domain | Threshold for crossing |
| --- | --- |
| Anterior curvature | ARC decreases >0.106 mm **or** Kmax increases >1.0 D |
| Posterior curvature | PRC decreases >0.085 mm |
| Pachymetric | Thinnest thickness decreases >2% of baseline **or** ARTmax decreases >44.24 units |
| Composite deviation | BAD-D increases >0.42 units |

- At most **one point per domain**, even if both component measurements cross.
- **At least 2 of 4** meets the numerical SOURCE domain criterion. The secondary **2 of 3 structural domains** check excludes BAD-D, which overlaps the structural measurements.
- The primary count is always out of four, never six measurements. The structural check is available inside the measurement details.
- Exact decimal comparisons use the entered values before display rounding. Strict greater-than cutoffs mean equality does **not** cross. Thickness percent decrease is `100 × (baseline − latest) / baseline`.
- All six reliable values are required at both visits. Missing, invalid, unverified, changed or unreliable inputs never become a negative vote or a stable result. An explicit scan-quality warning withholds the domain calculation.
- ARC/Kmax disagreement stays visible. Each component shows its actual change, threshold and crossing result.
- Age and interval do not contribute domain points. They contribute only to comparison-scope checks.
- The panel is independent of the NKPI score. A recorded prior-CXL hold can show numerical crossings with an explicit pretreatment-protocol limitation, while the existing NKPI hold remains in place. Other held assessments are not bypassed.

### SOURCE eligibility and interpretation

The rules are from **SOURCE_P0175_Resubmission_Answers.docx, pages 3–4** (2026-09-28). They are prespecified study criteria, not a validated diagnostic rule, progression probability or treatment recommendation.

SOURCE requires baseline age 18–50, verified same-eye pretreatment measurements, acceptable scan quality, and examinations at least **six calendar months** apart. The app retains interval days, not original examination dates: fewer than 181 days is too short, 181–183 days requires checking the dates, and 184 days or more establishes the interval minimum. No 180-day approximation is silently substituted.

If age, interval, quality or recorded treatment status fails or is unknown, the panel displays **SOURCE classification not assigned**, while preserving any valid numerical threshold findings. When these recorded requirements are met, it says **Recorded comparison requirements met**; it does not certify full study enrollment eligibility. The protocol also requires source-record review for scarring, procedure history and other exclusions. Contact-lens washout and scarring are not assessed by this panel; missing information is never treated as absence. The protocol's single-site research enrollment restrictions are not checked by the calculator.

A 0–1 count means the numerical criterion is not met; it does not establish stability. The rule is not validated here for post-CXL monitoring. No new clinical accuracy claim is made.

The dropdown uses only the verified current result snapshot. Edits, source changes and patient reset clear old findings. PDF and audited JSON exports retain their existing content; this new domain summary is currently displayed in the webpage only.

## Retained from 7.2 — Extraction integrity and reduced interval weight

This complete Streamlit / GitHub release strengthens OCR extraction and source review for the six required measurements in both eyes. It also applies the requested 50% scan-interval coefficient to the two-visit model.

## New in 7.2

- Repeated OCR passes of the same printed field count as one source. Conflicting values cannot receive an automatic agreement badge.
- **Reports agree**, **Conflict**, **Review source**, and **Source verified** distinguish OCR corroboration from human verification. Every field has an expandable source crop; uncertain fields open automatically.
- Label-anchored ARTmax/PPImax reading follows the printed labels and retries different image channels. The label itself cannot contribute a spurious digit to its measurement.
- When direct ARTmax OCR is missing or weak and inconsistent, an explicitly approximate C/PPImax candidate may be populated. It requires source review, retains the conflicting readings, and is not presented as independently verified. No values are imputed from cohort averages or another eye.
- Extraction bounds now match calculator bounds, including ARTmax below 50 and BAD-D above 20. Wider ranges do not establish eligibility or scan quality.
- Progression-table corroboration requires a matching examination date, with exact time for repeated same-day candidates. No nearest-date or last-row substitution.
- OD/OS discovery, missing-eye visibility and the explicit both-eye submission gate are retained. Missing or unreadable values are not fabricated to reach 6/6.
- The two-visit artifact uses interval coefficient **−0.45876435066849985**, exactly half **−0.9175287013369997**. Its center, scale, intercept and other coefficients are unchanged. The adjustment halves the interval term on the logit scale, not necessarily its displayed point effect.
- Model release **3.1.0-interval-half** is an unvalidated fixed amendment, not a retrained/calibrated future-risk model. Parent two-visit AUROC/calibration metrics are not presented as new-model performance.

Read [verification and limitations](docs/TEST_REPORT.md) before interpreting extraction completeness or model performance.

## Import repair

- Restore automatic legacy OD/OS/date filename association, including shorter
  history report sets. Identify report titles rather than guessing page order.
- Process current, legacy and decoded report pages together. A recognized eye
  no longer prevents the other eye's partial/unassigned reports from appearing.
- Look for printed eye labels below the title when header OCR cannot read them.
- Show OD and OS, with every available earlier/later input grid. Missing sources
  have an explicit recovery card; the other visit's measurements remain visible.
- Default to **Both eyes**. Missing eye/visit data block bilateral submission.
  A single-eye calculation requires an explicit OD-only or OS-only selection.
- Reopen source matching for one uploaded visit without a second upload or
  clearing the other visit. Unchanged verified edits are retained.
- Never infer OS simply because a second OD scan exists. Ambiguous repeat scans
  and unknown eyes need source confirmation.

See [bilateral import notes and test evidence](docs/BILATERAL_IMPORT_FIX.md).

## What it calculates

- Observed change and annualized rate for A, B, thickness, Kmax, BAD-D and ARTmax.
- Conditional 6-, 12- and 24-month measurement values if each observed rate
  continues; a second line holds measurements constant as a reference.
- Optional NKPI score scenarios obtained by evaluating those projected inputs
  using the existing cohort-trained **single-visit** classifier.
- Actual future age and a separate, opt-in age-association sensitivity. Age is
  held at the latest-visit age in the score scenario by default, isolating changes
  in the measurements. Advancing age never changes the physical measurement rates.
- Descriptive cohort rate context calculated from 179 eligible Northwestern eyes
  in 101 patients. The original 426-eye workbook was source-reconstructed before
  this subset was selected; no patient records are included in this ZIP.
- Numeric scenario export, kept separate from audited original NKPI results.

**These are conditional scenarios, not validated forecasts, future progression
probabilities or treatment-effect estimates.** The dashed future points are
calculated from actual inputs and explicit assumptions, not handcrafted example
risk percentages. The same calculations work for different entered patients.

## Why the lines are not labelled CXL versus no CXL

The available selected scans do not provide the post-CXL outcomes or complete
therapy timelines needed for that comparison. The unchanged-measurement line is
not a CXL prediction, and the continuation line is not an identified untreated
counterfactual. A two-visit slope cannot establish that its rate will persist.

We tested cohort-based first-to-second-visit prediction with nested,
patient-grouped validation. It did not consistently outperform carrying the
last measurement forward. The candidate forecast is therefore **not promoted**
into the website. See [evidence and methods](docs/TRAJECTORY_METHODS.md).

## Install

1. Extract `NKPI_SOURCE_Domains_v7_4_GitHub.zip`.
2. Upload the extracted contents to the repository root, replacing matching
   files and preserving the folder structure.
3. Keep `kcn_dual_calculator_app.py` as the Streamlit main file. Python deployment
   requirements are unchanged.
4. Reboot Streamlit, reload the browser, and confirm the footer version
   **7.4.0-source-domains**.
5. Import and verify two visits, then calculate as before. At the bottom, open
   **SOURCE progression domains · Both eyes**.
6. Open **Measurements, structural check & SOURCE rules** for all six values,
   the 2-of-3 sensitivity check and interpretation. Existing projections remain available.

The separate, unchanged projection engine requires a verified pair at least 90 days apart. This interval rule
is an operational safeguard, not an evidence-based progression threshold. A
recorded scan-quality warning or prior treatment blocks scenarios. Unknown
quality/treatment history remains explicitly disclosed, never marked normal.

Missing values remain unavailable. Future values outside the calculator's
input bounds, and future scores outside its development ranges, are withheld
rather than clipped. The two-visit NKPI above uses the explicitly amended interval weight; the scenario plots retain the single-visit model.

## Checks

```bash
python tools/verify_package.py
node tests/test_progression_summary.cjs
node tests/test_extraction_integrity.cjs
python tests/test_model_interval.py
node tests/test_bilateral_import.cjs
node tests/test_bilateral_review.cjs
node tests/test_trajectory.cjs
node tests/test_trajectory_ui.cjs
```

The chart/UI test uses a mock DOM; a full browser or live Streamlit run was not
available in the build environment. See [test report](docs/TEST_REPORT.md).
The `research` folder contains reproducible analysis code and aggregate results,
not patient data. It is not used by the deployed app. Older documents are retained
as historical background, not the description of this release's model.

ARTmax identity reference: [OCULUS Pentacam Interpretation Guide](https://www.pentacam.com/fileadmin/user_upload/pentacam.de/downloads/interpretations-leitfaden/Pentacam_Interpretation_Guide_Ophth_EN_0624.pdf). Dividing rounded printed values can differ from the instrument’s internal calculation.
