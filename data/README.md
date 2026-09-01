# Data

The raw vehicle-emission inspection records used in this project are **not included in this repository**.

## Data source and access

The study uses vehicle-emission inspection records provided through the Sri Lankan vehicle-emission testing programme for research purposes. The underlying row-level records are not redistributed publicly.

The methodology uses:

- **January 2025** and **February 2025** records for EFI development; and
- **March 2025** records as a temporally held-out dataset for evaluation.

## Expected local files

Authorized users who have access to the source data should place the following files in this directory. The CSV files are excluded from version control.

## Variables used for EFI

The EFI methodology uses 12 continuous emission and operating measurements available for petrol-powered motor cars:

| Variable | Description |
| --- | --- |
| `AccHC` | Hydrocarbon measurement under acceleration |
| `AccCO` | Carbon monoxide measurement under acceleration |
| `AccCO2` | Carbon dioxide measurement under acceleration |
| `AccO2` | Oxygen measurement under acceleration |
| `AccLambda` | Lambda measurement under acceleration |
| `AccRPM` | Engine speed under acceleration |
| `IdleHC` | Hydrocarbon measurement under idle conditions |
| `IdleCO` | Carbon monoxide measurement under idle conditions |
| `IdleCO2` | Carbon dioxide measurement under idle conditions |
| `IdleO2` | Oxygen measurement under idle conditions |
| `IdleLambda` | Lambda measurement under idle conditions |
| `IdleRPM` | Engine speed under idle conditions |

The preprocessing workflow also uses the following fields to select the study population and apply inspection-result consistency checks:

- `VehFuelType`
- `VehClass`
- `TestResult`

## Reproducibility note

The complete computational methodology is available in `notebooks/EFI_Methodology.ipynb`. The notebook documents preprocessing, feature standardization, K-means emission-profile discovery, clean-reference selection, EFI construction and calibration, supervised prediction modelling, explainability, and temporal evaluation.

Because the source inspection records are not publicly redistributed, full execution of the notebook requires authorized access to the underlying monthly CSV files.
