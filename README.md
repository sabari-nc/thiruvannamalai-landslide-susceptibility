# Arunachala Hill Landslide Susceptibility - Project Repository

Slope-unit-based landslide susceptibility mapping for Arunachala Hill, Tiruvannamalai, combining a statistical (Frequency Ratio) model and a physics-based (TRIGRS) model, validated against a 10-polygon landslide inventory including the December 2024 debris-flow event.

## Structure

data/raw          - Original, unmodified source files
data/processed    - Reprojected (EPSG:32644), derived, analysis-ready data
data/external     - Third-party reference data (Chandrasekaran et al. raw lab data)
scripts/01_preprocessing     - Reprojection, DEM derivatives
scripts/02_slope_units       - GRASS r.slopeunits delineation, gap-free splitting
scripts/03_predictors        - Per-unit predictor table construction
scripts/04_statistical_model - Frequency Ratio model, LOOCV, VIF check
scripts/05_trigrs            - Physics-based model (grids, run, validation)
scripts/06_figures           - Publication figure generation
outputs/figures   - Final publication figures (PDF + TIFF/PNG)
outputs/tables    - Publication tables (Excel)
docs              - Methodology notes, architecture diagram

External dependency: TRIGRS (USGS) compiled separately at ~/trigrs_src/landslides-trigrs - scripts in 05_trigrs assume this location and must be run from that repo's root directory.

## Run order

1. 01_preprocessing/reproject_data.py
2. 01_preprocessing/make_dem_derivatives.py
3. 02_slope_units/delineate_slope_units.py (requires GRASS GIS with r.slopeunits addon)
4. 03_predictors/build_predictor_table.py
5. 04_statistical_model/check_vif.py then frequency_ratio_and_loocv.py
6. 05_trigrs/build_hillwide_grids.py (run from the TRIGRS repo root)
7. Copy 05_trigrs/tr_in_hillwide.txt to tr_in.txt in the TRIGRS repo root, then run ./src/TRIGRS/trg
8. 05_trigrs/run_hillwide_and_validate.py (run from the TRIGRS repo root)

## Key validated results

Statistical model (91 slope units, 5 predictors): in-sample 9/9, leave-one-out cross-validation 9/9 (100 percent)
TRIGRS model (two-zone, field-calibrated): in-sample 7/10, leave-one-out cross-validation 1/10 (10 percent)

The TRIGRS leave-one-out result is low because the weak-soil zone is built from the landslide inventory itself (100 m buffer, a field-confirmed spatial scale). This is a genuine data limitation, a single geotechnical sample site, not a modeling error. The statistical model is the primary, independently-validated hill-wide susceptibility product. TRIGRS confirms physical plausibility at the calibration site.

## Known corrections to the published source, Chandrasekaran et al. 2025

Theta_r and van Genuchten m in the paper's Table 4 are numerically identical, both 0.065. Refitting the author-provided raw SWCC data gives theta_r = 0.029 and m = 0.37. This project uses the refitted values throughout. The published paper is treated as a starting reference. Raw data and direct author confirmation take priority throughout this project.
