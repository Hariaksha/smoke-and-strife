# Replication guide

This guide explains how to reproduce every table and figure in [`paper/main.pdf`](paper/main.pdf), *Smoke and Strife: Fire Exposure and Social Conflict in Indonesia*.

## 1. Software

- Python 3.14.2. Install the pinned packages with:

  ```bash
  pip install -r requirements.txt
  ```

- LaTeX: the paper compiles with [tectonic](https://tectonic-typesetting.github.io/) (`tectonic paper/main.tex`). Any standard TeX distribution with BibTeX/natbib also works.
- `pyhdf` is only needed to rebuild the MODIS MAIAC cache (Section 8 of `pollution-iv.ipynb`). `cdsapi` is only needed to re-download ERA5 data.

**Run every notebook from the `analysis/` folder.** Paths are set relative to the repository root (`ROOT = Path.cwd().parent`), so the code works on any machine as long as the working directory is `analysis/`.

## 2. Data

| Input | Location in repo | In git? | How to obtain |
|---|---|---|---|
| ACLED conflict events, Indonesia, 1 January 2015 to 10 June 2025 (downloaded 10 June 2026) | `data/unrest/ACLED Data_2026-06-10.xlsx` | **No** (ACLED's licence does not allow redistributing raw data) | [acleddata.com](https://acleddata.com): create an account and use the Data Export Tool to export all event types for Indonesia, 1 January 2015 to 10 June 2025 (21,703 events in the export used here). Save the Excel export at this path. Results were produced with the export downloaded on 10 June 2026; later exports may differ slightly because ACLED revises past events. |
| VIIRS S-NPP 375 m active fire archive | `data/climate/fire/DL_FIRE_SUOMI-VIIRS-C2_718931_Nov2000-Feb2026_buffer0km-csv/fire_archive_SV-C2_718931.csv` | **No** (too large) | NASA FIRMS [archive download](https://firms.modaps.eosdis.nasa.gov/download/): Suomi-NPP VIIRS, Indonesia, CSV |
| GADM v4.1 administrative boundaries, Indonesia | `data/administrative/gadm41_IDN_shp/` | **No** (GADM licence does not allow redistribution) | [gadm.org](https://gadm.org/download_country.html): Indonesia, shapefile |
| ERA5 monthly 10 m wind (u, v) | `data/climate/wind/era5_wind_indonesia_2015_2025.nc` | Yes | Copernicus CDS; `wind-IV.ipynb` downloads it if missing (set the `CDSAPI_KEY` environment variable) |
| ERA5 monthly precipitation and 2 m temperature | `data/climate/weather/era5_weather_indonesia_2015_2025.nc` | Yes | Copernicus CDS; `weather-controls.ipynb` downloads it if missing |
| MERRA-2 monthly aerosol diagnostics (M2TMNXAER), cropped to Indonesia | `data/pollution/merra2_aer_indonesia_2015_2025.nc`, `merra2_pm25_district_monthly.csv` | Yes | NASA GES DISC (free Earthdata account, credentials in `~/.netrc`); `pollution-iv.ipynb` downloads it if missing |
| MODIS MAIAC AOD (MCD19A2CMG v061), district-month aggregate | `data/pollution/maiac_aod_district_monthly.csv` | Yes | NASA LP DAAC (same Earthdata account); the daily files are rebuilt into a local monthly cache (`data/pollution/maiac_monthly_cache/`, not in git) by `pollution-iv.ipynb` |
| Presidential Instructions 11/2015 and 3/2020 (cited, not used as data) | `literature/Inpres *.pdf` | Yes | FAO FAOLEX |

Credentials are never stored in the notebooks: ERA5 downloads use the `CDSAPI_KEY` environment variable (`wind-IV.ipynb`) or `~/.cdsapirc` (`weather-controls.ipynb`), and NASA Earthdata downloads use `~/.netrc`.

## 3. Run order

Run `wind-IV.ipynb` first. It builds the district-month panel (`analysis/iv_panel_district.csv`) that every other notebook reads. The other notebooks can then be run in any order, except that `papua.ipynb` reads the instrument file written by `state-capacity-province.ipynb`.

| Order | Notebook | What it produces | Approx. runtime |
|---|---|---|---|
| 1 | `wind-IV.ipynb` | Panel, upwind instrument, first stage, main IV, conflict-activity heterogeneity, event-type decompositions, Poisson control function, fatalities, placebo, seasonal fixed effects, Conley SEs, fully instrumented lags, regional/own-fire checks, effective F | Several hours (instrument construction and Conley SEs) |
| 2 | `weather-controls.ipynb` | ERA5 weather controls, Anderson–Rubin tests | ~10 min |
| 3 | `conflict-propensity.ipynb` | Pre-period conflict-propensity gradient | ~10 min |
| 4 | `identification-checks.ipynb` | Lead placebo, 2018–2025 sample, sharpened q-values | ~5 min |
| 5 | `pollution-iv.ipynb` | MERRA-2 PM2.5/AOD IV, own-grid-point check, MAIAC AOD | Minutes with cached data; 2–3 hours to rebuild the MAIAC cache |
| 6 | `mechanisms.ipynb` | Event-type, actor and reporting decomposition; city, El Niño and Papua heterogeneity; temporal and spatial displacement | ~5 min |
| 7 | `state-capacity-province.ipynb` | Upwind instrument split into same- and other-province fire | ~15 min |
| 8 | `papua.ipynb` | Main result and mechanisms excluding Papua, and within Papua | ~2 min |
| – | `generate_maps.py` | Conflict and fire maps (`python generate_maps.py`) | ~5 min |

## 4. Tables and figures

| Paper item | Produced by | Output file(s) in `analysis/` |
|---|---|---|
| Table 1 (first stage), main IV, conflict-activity thresholds | `wind-IV.ipynb` | `iv_panel_district.csv`, `iv_active_district_robustness.csv`, `iv_lag_coefs_district.csv`, `output/effective_f_results.csv` |
| Event-type decomposition (two- and four-way) | `wind-IV.ipynb` | `output/fourway_eventtype_results.csv`, `output/eventtype_strict_checks.csv` |
| Mechanisms table and figure | `mechanisms.ipynb` | `output/mechanisms_results.csv`, `output/mechanisms_coefplot.png` |
| Same- vs other-province table and figure | `state-capacity-province.ipynb` | `output/province_split_results.csv`, `output/province_split_coefplot.png` |
| Conflict-propensity figure | `conflict-propensity.ipynb` | `output/conflict_propensity_results.csv`, `output/conflict_propensity_bins.png` |
| Pollution IV, own-grid-point and MAIAC tables | `pollution-iv.ipynb` | `output/pollution_iv_results.csv`, `output/pollution_owngrid_reduced_form.csv`, `output/maiac_first_stage_diagnostics.csv` |
| Summary statistics | computed from the panel built by `wind-IV.ipynb` | `iv_panel_district.csv` |
| Conflict and fire maps (appendix) | `generate_maps.py` | `paper/figures/conflict_events_map.png`, `paper/figures/wildfire_events_map.png` |
| Preliminary correlational models (appendix) | `fixed-effects.ipynb` | Early exploratory notebook; uses older ACLED and fire extracts not needed elsewhere |
| Poisson control function, fatalities, placebo, seasonal FE, Conley, fully instrumented lags, regional/own-fire | `wind-IV.ipynb` | `output/placebo_test_results.csv`, `output/seasonal_robustness_results.csv`, `output/conley_se_results.csv`, `output/fully_instrumented_iv_results.csv`, `output/regional_ownfire_checks.csv`, `output/iv_fatalities_results.csv`, `output/rolling_exposure_results.csv`, `output/precision_robustness_results.csv` |
| Weather controls and Anderson–Rubin tables | `weather-controls.ipynb` | `output/weather_controls_results.csv`, `output/anderson_rubin_results.csv` |
| Excluding Papua | `papua.ipynb` | `output/papua_results.csv` |
| Lead placebo, 2018–2025 sample, q-values | `identification-checks.ipynb` | `output/identification_checks_results.csv` |

Figures used in the paper are copied to `paper/figures/` by the notebooks that make them.

## 5. Compiling the paper

```bash
cd paper
tectonic main.tex
```
