# Smoke and Strife: Wildfire Exposure and Social Conflict in Indonesia

Research project estimating the causal effect of wildfire-driven air pollution on social conflict in Indonesia, using a wind-direction instrument to isolate exogenous variation in local smoke exposure.

The current draft working paper is in this repository: [`paper/main.pdf`](paper/main.pdf) ([source](paper/main.tex)).

---

## Overview

Indonesia experiences recurring, large-scale peat and forest fires, driven by land-clearing practices and intensified during El Niño-induced dry seasons, that generate severe transboundary haze across the western archipelago. This project asks whether short-run exposure to that fire-driven air pollution causally affects the incidence of social conflict at the district-month level.

Local fire activity is plausibly endogenous to local conflict: land disputes, weak governance, and economic distress that drive conflict may also drive land-clearing fires. The project therefore uses **upwind fire radiative power**, fire activity in locations whose smoke the prevailing wind would carry toward a district, as a source of variation driven by wind rather than by local conditions. The reduced form (conflict on upwind fire) is the primary evidence; instrumental-variables estimates scale it by the district's own fire intensity or, more directly, by reanalysis surface PM2.5.

---

## Headline Findings

- **Full sample** (481 districts, Feb. 2015–Jun. 2025): no robust average effect of upwind fire on conflict counts in the baseline specification.
- **Conflict-active districts** (conflict in ≥30% of months): more upwind fire reduces monthly conflict events (reduced form −0.026, p = 0.002). The result is unchanged when fires inside the district are excluded from the instrument or when local rainfall and temperature are controlled for, and it survives Conley standard errors and the weak-instrument-robust Anderson–Rubin test.
- **Pollution directly:** instrumenting MERRA-2 surface PM2.5 with upwind fire, a 10% increase in PM2.5 reduces conflict events in conflict-active districts by about 8% of the mean (Anderson–Rubin p = 0.004), with or without controls for the district's own fires. The first stage is strong (effective F = 41 in the full panel, 26 in the conflict-active subsample). A check restricted to districts containing their own MERRA-2 grid point is uninformative, because only 25 conflict-active districts qualify; the reduced form is equally imprecise there, so the loss of precision reflects the smaller sample rather than the pollution measure.
- **Observed pollution does not confirm the first stage:** satellite aerosol optical depth from MODIS MAIAC (5 km) does not respond detectably to upwind fire (effective F 2–9), while MERRA-2 does on the same district-months. Likely reasons are that clouds and thick smoke remove the haziest days from the satellite record, and that MERRA-2's fire emissions are built from satellite fire detections. The pollution IV is therefore treated as suggestive of a pollution channel, and the reduced form remains the primary evidence.
- **Not an artifact of the 30% cutoff:** using conflict propensity measured in 2015–2017, before the estimation window, the effect is near zero in districts with no prior conflict and grows steadily with prior conflict. In proportional (Poisson) terms the effect does not vary detectably with propensity, which suggests a similar small percentage effect everywhere that is only measurable where conflict is common.
- **Identification checks:** next month's upwind fire does not predict this month's conflict (lead coefficient p = 0.985); the results strengthen on 2018–2025, after ACLED's Indonesia coverage expands; and the all-events result survives a false-discovery-rate correction across the four outcomes.
- **Mechanisms:** the evidence points to two channels. Protests fall with smoke from fires in *other* provinces, which cannot draw on local security forces, consistent with a direct effect of smoke. Events carried out by state security forces fall about five times as much as protests in proportional terms, and significantly only with fires in the district's *own* province, whose fire task force the same forces staff (though this contrast is not statistically decisive). Fatal events fall more than other events, which argues against a reporting explanation, and conflict does not rebound in the following months.
- **Event types:** violence against civilians, most often committed by state security forces, is the most robust component. Political violence and Riots/VAC are suggestive only, since they weaken under the Anderson–Rubin test. Protests account for most of the decline in all events but do not survive the stricter checks.
- No robust evidence that fire exposure affects conflict-related **fatalities**.

See [`paper/main.pdf`](paper/main.pdf) for the full results and robustness analysis.

---

## Data

- **Fire detections:** NASA VIIRS active fire product (Suomi-NPP, 375m resolution), via NASA FIRMS.
- **Wind and weather:** ERA5 monthly reanalysis (Copernicus Climate Data Store): 10m wind components, total precipitation, and 2m temperature.
- **Air pollution:** MERRA-2 monthly aerosol diagnostics (M2TMNXAER), from NASA GES DISC (requires a free NASA Earthdata account): surface PM2.5 and aerosol optical depth. MODIS MAIAC aerosol optical depth (MCD19A2CMG v061, daily 0.05°), from NASA LP DAAC with the same account. The processed district-month files are in `data/pollution/`; the monthly MAIAC pixel cache is rebuilt by the notebook and not committed.
- **Conflict events:** ACLED (Armed Conflict Location & Event Data Project), Indonesia.
- **Administrative boundaries:** GADM v4.1, Level 2 (kabupaten/kota).

See the Data Availability Statement in the paper for details on reconstructing the raw inputs, some of which exceed GitHub's file-size limits and are linked to their public sources rather than hosted directly.

---

## Methodology

- **Upwind instrument:** for each district-month, the total fire radiative power of detections within 300 km and within 45° of the direction the wind blows from.
- **Reduced form and two-stage least squares** with district and year-month fixed effects and province-clustered standard errors. The endogenous variable is either the district's own fire intensity (log FRP), surface PM2.5 or aerosol optical depth from MERRA-2, or satellite aerosol optical depth from MODIS MAIAC.
- **Weak-instrument diagnostics:** Montiel Olea–Pflueger effective F and Anderson–Rubin tests.
- **Mechanisms:** decomposition by ACLED actor, sub-event type, source scale and precision; city and severe-fire-year heterogeneity; temporal and spatial displacement.
- **Robustness:** conflict-activity heterogeneity and a pre-determined continuous conflict-propensity measure; event-type decompositions; placebo instruments and lead (placebo-in-time) tests; province × calendar-month and province × year-month fixed effects; own-fire-excluded instrument; local weather controls; Conley standard errors; a fully instrumented distributed-lag specification; a 2018–2025 sample; and sharpened q-values for multiple testing.

---

## 📁 Repository Structure

```text
climate-conflict/
├── paper/             # Working paper (main.tex is the current draft), references.bib, figures/
├── analysis/          # Jupyter notebooks (see below) and output/ (result tables and figures)
├── data/              # Raw and processed datasets (administrative, climate, pollution, unrest, wellbeing)
├── literature/        # Background papers cited in the project, plus lit-review.xlsx (annotated source list)
├── publications/      # Conference poster and abstract materials
└── info.md            # Project notes
```

---

## Contents

### `paper/`
The current draft, `main.tex`/`.pdf`, with its bibliography (`references.bib`) and figures (`figures/`).

### `analysis/`
Run `wind-IV.ipynb` first; the other notebooks read the panel it writes (`iv_panel_district.csv`).

| Notebook | Contents |
|---|---|
| `wind-IV.ipynb` | Main analysis: panel construction, upwind instrument, first and second stages, and most robustness checks |
| `weather-controls.ipynb` | ERA5 precipitation and temperature controls; Anderson–Rubin tests |
| `pollution-iv.ipynb` | MERRA-2 PM2.5 and aerosol optical depth, and MODIS MAIAC aerosol optical depth, as the endogenous variable, including the own-grid-point check and first-stage diagnostics (the MAIAC download takes 2–3 hours and needs the `pyhdf` package) |
| `conflict-propensity.ipynb` | Continuous and binned conflict-propensity heterogeneity |
| `identification-checks.ipynb` | Lead placebo, 2018–2025 sample, sharpened q-values |
| `state-capacity-province.ipynb` | Rebuilds the upwind instrument split into same-province and other-province fire, to separate smoke from fire-response (state-capacity) channels |
| `mechanisms.ipynb` | Tests of avoidance, state-capacity diversion and reporting explanations by event type, actor and source; temporal and spatial displacement |
| `fixed-effects.ipynb` | Preliminary, non-instrumented correlational models |

`generate_maps.py` produces the geographic figures used in the paper.

### `data/`
Raw and processed datasets: fire detections, wind and weather fields, pollution, conflict events, administrative boundaries, and supplementary socioeconomic data.

### `literature/`
PDFs of the papers cited in the paper's Related Literature section and elsewhere, plus `lit-review.xlsx`, an annotated spreadsheet of each source's findings and relevance to this project.

### `publications/`
Poster and abstract materials from prior conference presentations of this work.

---

## Author

**Hariaksha Gunda**
University of Alabama

Repository: [https://github.com/Hariaksha/wildfire-conflict](https://github.com/Hariaksha/wildfire-conflict)

---

## Citation

If you use or reference this project, please cite it as an unpublished working paper:

> Gunda, H. *Smoke and Strife: Fire Exposure and Social Conflict in Indonesia.* Working paper, [paper/main.pdf](paper/main.pdf).
