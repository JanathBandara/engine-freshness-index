# Engine Freshness Index (EFI)

An interpretable machine-learning framework for continuous environmental assessment of petrol-vehicle emissions using real-world vehicle-inspection measurements.

## Overview

Conventional vehicle-emission inspection primarily produces a binary pass/fail outcome. This project develops the **Engine Freshness Index (EFI)** as a continuous multivariate score derived from emission and operating measurements collected during petrol-vehicle inspection.

The methodology combines:

- data-quality and compliance-consistency filtering;
- standardized multivariate emission representation;
- K-means clustering for emission-profile discovery;
- a distance-based EFI formulation referenced to an empirically identified clean cluster;
- empirical calibration of the EFI scale;
- supervised regression for efficient EFI approximation;
- feature-importance and SHAP-based explainability; and
- temporal evaluation using an independently held-out month.

The complete research workflow is documented in:

`notebooks/EFI_Methodology.ipynb`

## Data

The study uses vehicle-emission inspection records provided through the Sri Lankan vehicle-emission testing programme.

The analysis is organized as:

- **Development data:** January and February 2025
- **Temporal evaluation:** March 2025

The raw dataset is **not included in this repository** because the records were provided for research use and are not redistributed publicly.

Authorized users can place the monthly files locally under:

```text
data/
├── January2025.csv
├── February2025.csv
└── March2025.csv
```

The `data/` directory is excluded from version control.

## EFI Input Variables

EFI is constructed from 12 continuous measurements available for petrol-powered motor cars under acceleration and idle test conditions:

| Operating condition | Measurements |
| --- | --- |
| Acceleration | HC, CO, CO₂, O₂, Lambda, RPM |
| Idle | HC, CO, CO₂, O₂, Lambda, RPM |

These variables represent the continuous emission and operating measurements available in the inspection dataset for the selected vehicle category.

## Methodology

### 1. Preprocessing

The workflow retains petrol-powered motor cars, removes incomplete records, requires positive RPM measurements, and applies a compliance-consistency check to failing inspection records using the relevant HC and CO thresholds represented in the study workflow.

### 2. Standardization

The 12 EFI variables are standardized before clustering and distance calculation because they are measured on different numerical scales. This prevents variables with larger raw magnitudes from dominating Euclidean-distance calculations.

### 3. Unsupervised emission-profile discovery

K-means clustering is evaluated for candidate values of `k` using both within-cluster sum of squares (SSE) and silhouette score. A three-cluster solution is retained as a balance between statistical separation and practical interpretability of broad emission profiles.

### 4. Clean-reference profile

The three cluster profiles are compared using their mean emission characteristics. Cluster 2 is selected as the empirical clean-reference cluster because it exhibits the most favorable combined emission profile within the observed data, including comparatively low HC and CO values under both acceleration and idle conditions.

### 5. EFI formulation

The centroid of the clean-reference cluster represents the empirical clean-emission state in standardized feature space.

For vehicle $i$, with standardized feature vector $z_i$ and clean-reference centroid $c_{\mathrm{clean}}$:

$$
d_i = \lVert z_i - c_{\mathrm{clean}} \rVert_2
$$

A smaller distance indicates greater similarity to the clean reference, while a larger distance indicates greater multivariate deviation.

### 6. Empirical scale calibration

The sorted distance distribution contains a small extreme upper tail. During EFI development, the transition into this tail was identified at approximately a distance-derived score of 22, representing about 0.35% of the development observations.

Several candidate distance-reference values were evaluated. A reference value of **13** was retained because it reproduced approximately the same 0.35% extreme-tail proportion outside the nominal scale before inversion.

The value 13 is therefore an **empirically calibrated scale parameter**, not a statutory emissions threshold or physical pollutant limit.

### 7. Score orientation

The scaled distance is inverted so that higher EFI values represent greater similarity to the clean-reference profile:

$$
EFI_i = 100 - \left(\frac{d_i}{13}\right) \times 100
$$

The score is not clipped at zero. Extreme observations can therefore receive negative EFI values, preserving information about the magnitude of their deviation from the clean reference.

### 8. Supervised EFI Learning

The following regressors are evaluated as prediction models for learning the derived EFI directly from the raw emission measurements:

- Linear Regression
- Decision Tree
- Random Forest
- Random Forest Lite
- XGBoost
- LightGBM

The supervised models approximate a **derived EFI target**; EFI is not an independently measured engine-health ground-truth variable.

### 9. Explainability

The selected Random Forest Lite model is examined using tree-based feature importance and SHAP global feature-importance and summary analyses.

### 10. Temporal evaluation

March 2025 is kept outside the January-February EFI-development workflow and is used for temporal prediction and distribution analysis. The evaluation compares the development EFI distribution with March predicted EFI using descriptive statistics, density analysis, and a two-sample Kolmogorov-Smirnov test.


## Running the Notebook

1. Clone the repository.
2. Create and activate a Python environment.
3. Install the dependencies:

```bash
pip install -r requirements.txt
```

4. Place authorized monthly CSV files in a local `data/` directory at the repository root.
5. Open and run `notebooks/EFI_Methodology.ipynb`.

Because the raw dataset is not distributed publicly, the full notebook cannot be reproduced without authorized access to the underlying inspection records.

## Research Scope

This repository documents the computational methodology for EFI construction, prediction  modelling, explainability, and temporal evaluation. It is intended to provide transparent access to the research implementation while respecting restrictions on redistribution of the underlying government-provided data.

## Author

**Janath Bandara**  
B.Sc. (Hons.) in Computation and Management  
University of Peradeniya, Sri Lanka
