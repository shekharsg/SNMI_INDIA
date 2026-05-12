# Sustainable Nitrogen Management Index (SNMI) India Code and Data

This repository contains the data and analysis notebooks used to reproduce the main and supplementary figures for the manuscript:

**Regional policies outweigh agroclimatic conditions in achieving sustainable nitrogen management across Indian croplands**

The code calculates district-level nitrogen input, harvested nitrogen, nitrogen-use efficiency (NUE), nitrogen surplus and the Sustainable Nitrogen Management Index (SNMI) for Indian agriculture, and generates the manuscript figures and supplementary analyses.

---

## Repository contents

```text
Data_Code_submission/
├── nsurplus_district_comp.csv
├── fig1.ipynb
├── fig2.ipynb
├── fig3.ipynb
├── fig4.ipynb
├── supplementary.ipynb
├── policy_multi_effect.ipynb
├── AppliedN_MP_region9.csv
├── AppliedN_MH_region9.csv
├── HarvestedN_MP_region9.csv
├── HarvestedN_MH_region9.csv
├── AGRO_CLIMATE_SHP/
│   └── Agroclimatic_regions.*
├── district_shape_1966_clim_zone/
│   └── india_1966_clim_zone.*
└── India-State-and-Country-Shapefile-Updated-Jan-2020-master (1)/
    └── India-State-and-Country-Shapefile-Updated-Jan-2020-master/
        ├── India_State_Boundary.*
        └── India_Country_Boundary.*
```

### Main data files

| File | Description |
|---|---|
| `nsurplus_district_comp.csv` | Main annual district-level nitrogen-budget dataset. Includes crop nitrogen removal, nitrogen inputs, nitrogen surplus estimates, land-use variables, irrigation, cropping intensity and related indicators. |
| `AppliedN_MP_region9.csv`, `AppliedN_MH_region9.csv` | Annual nitrogen-input time series for selected Madhya Pradesh and Maharashtra districts in the Western Plateau and Hills region. |
| `HarvestedN_MP_region9.csv`, `HarvestedN_MH_region9.csv` | Annual crop-specific harvested nitrogen time series for selected Madhya Pradesh and Maharashtra districts. |
| `district_shape_1966_clim_zone/india_1966_clim_zone.*` | District boundary shapefile with agro-climatic-zone information. |
| `AGRO_CLIMATE_SHP/Agroclimatic_regions.*` | Agro-climatic-zone boundary shapefile. |
| `India_State_Boundary.*` | State boundary shapefile used for map overlays. |

---

## Notebook overview

| Notebook | Purpose | Main outputs |
|---|---|---|
| `fig1.ipynb` | Produces national and district-level time-series panels for nitrogen input, harvested nitrogen, NUE and SNMI. | `fig1_SNMI_national_panels.png/pdf`, `fig1_panel_timeseries_statistics.csv`, `fig1_district_metrics_with_SNMI.csv` |
| `fig2.ipynb` | Produces spatial maps of N harvest, N applied, NUE and SNMI for the 1970, 1995 and 2015 time windows. | `Figure2_Nharvest_Ninput_NUE_SNMI.png/pdf`, `Figure2_SNMI_calculated_data.csv` |
| `fig3.ipynb` | Analyses inter- and intra-agroclimatic-zone variability in SNMI, including trajectories in normalised yield--NUE space. | `Figure3_ACZ_SNMI_variability.png`, `Figure3_district_SNMI_data.csv`, `Figure3_ACZ_smoothed_SNMI_data.csv` |
| `fig4.ipynb` | Generates the Madhya Pradesh--Maharashtra comparison figure for selected districts in the Western Plateau and Hills region, including nitrogen inputs, harvested nitrogen, surplus and NUE. | `Figure_SNMI_MP_MH_fluxes_with_NUE.png/pdf`, `Figure_MP_MH_flux_NUE_annual.csv`, `Figure_MP_MH_flux_NUE_period_summary.csv` |
| `supplementary.ipynb` | Generates supplementary figures and tables, including decadal SNMI shifts, SNMI component decomposition, CV tests and reviewer-response analyses. | Supplementary figure PDFs/PNGs and CSV summary tables |
| `policy_multi_effect.ipynb` | Placeholder notebook for the policy/mixed-effects model workflow. The coefficient figure is included in the submitted figure set; add or update executable model code here if this analysis is rerun. | Not currently executable as provided |

---

## Software requirements

The notebooks were developed in Python and require standard scientific, geospatial and plotting libraries.

Recommended environment:

```bash
conda create -n snmi python=3.10 -y
conda activate snmi
conda install -c conda-forge numpy pandas geopandas matplotlib cartopy scipy seaborn jupyter -y
```

Alternatively, using `pip`:

```bash
pip install numpy pandas geopandas matplotlib cartopy scipy seaborn jupyter
```

`geopandas` and `cartopy` depend on geospatial libraries such as GDAL, PROJ and GEOS. A `conda-forge` environment is therefore recommended, especially on Windows.

---

## How to run the workflow

### 1. Unzip the repository

Unzip the archive and keep the folder structure unchanged:

```text
Data_Code_submission/
├── nsurplus_district_comp.csv
├── fig1.ipynb
├── fig2.ipynb
...
```

### 2. Update file paths in each notebook

The notebooks currently contain absolute Windows paths used during manuscript preparation. Before running the code, update the path block at the beginning of each notebook.

For example, replace paths such as:

```python
NSURPLUS_CSV = r"C:\collaboration\2025\rohini\SNMI\snmi work\data\nsurplus_district_comp.csv"
DISTRICT_SHP = r"Y:\Home\goyal\...\india_1966_clim_zone.shp"
```

with relative paths from the repository root:

```python
from pathlib import Path

ROOT = Path(".")
NSURPLUS_CSV = ROOT / "nsurplus_district_comp.csv"
DISTRICT_SHP = ROOT / "district_shape_1966_clim_zone" / "india_1966_clim_zone.shp"
AGROCLIM_SHP = ROOT / "AGRO_CLIMATE_SHP" / "Agroclimatic_regions.shp"
STATE_SHP = ROOT / "India-State-and-Country-Shapefile-Updated-Jan-2020-master (1)" / \
    "India-State-and-Country-Shapefile-Updated-Jan-2020-master" / \
    "India_State_Boundary.shp"
SAVE_DIR = ROOT / "outputs"
SAVE_DIR.mkdir(exist_ok=True)
```

### 3. Run notebooks in order

Recommended order:

1. `fig1.ipynb`
2. `fig2.ipynb`
3. `fig3.ipynb`
4. `fig4.ipynb`
5. `supplementary.ipynb`

The main notebooks are largely standalone and recompute required SNMI variables internally. Running them in the above order is still recommended because it follows the manuscript figure sequence.

---

## Method summary

### Nitrogen input

Total nitrogen input is calculated from the available nitrogen-input components:

```text
Total N input = synthetic fertiliser N + manure N + biological nitrogen fixation + atmospheric N deposition
```

Depending on the notebook and available column names, the code either uses a precomputed total-input column or constructs it from component columns.

### Harvested nitrogen

Harvested nitrogen is estimated as the mean of the two available nitrogen-removal estimates:

```text
N harvest = mean(n_removal_kg_ha, n_removal_kg_ha_fao)
```

### Nitrogen surplus

Nitrogen surplus is calculated or represented as the difference between nitrogen input and harvested nitrogen. Where multiple nitrogen-budget estimates are available, the notebooks use the mean of the `N_budjet_*_kg_ha` columns to represent uncertainty-averaged nitrogen surplus.

### Nitrogen-use efficiency

NUE is calculated as:

```text
NUE = harvested nitrogen / total nitrogen input
```

Implausible NUE values outside the expected range are treated as missing in the figure workflows.

### SNMI

SNMI is calculated as a Euclidean distance from the ideal nitrogen-management point in normalised nitrogen-yield and NUE space:

```text
SNMI = sqrt((1 - NYield*)^2 + (1 - NUE*)^2)
```

where:

- `NYield*` is normalised nitrogen yield, benchmarked against the agro-climatic-zone-specific 90th percentile of district-level maximum harvested nitrogen.
- `NUE*` is the normalised NUE component.
- Lower SNMI indicates improved nitrogen-management performance.

---

## Expected outputs

Each notebook writes figures and processed data tables to its configured `SAVE_DIR`.

Typical outputs include:

```text
fig1_outputs/
├── fig1_SNMI_national_panels.png
├── fig1_SNMI_national_panels.pdf
├── fig1_panel_timeseries_statistics.csv
└── fig1_district_metrics_with_SNMI.csv

fig2_output/
├── Figure2_Nharvest_Ninput_NUE_SNMI.png
├── Figure2_Nharvest_Ninput_NUE_SNMI.pdf
└── Figure2_SNMI_calculated_data.csv

fig_3/
├── Figure3_ACZ_SNMI_variability.png
├── Figure3_district_SNMI_data.csv
└── Figure3_ACZ_smoothed_SNMI_data.csv

fig_4/
├── Figure_SNMI_MP_MH_fluxes_with_NUE.png
├── Figure_SNMI_MP_MH_fluxes_with_NUE.pdf
├── Figure_MP_MH_flux_NUE_annual.csv
└── Figure_MP_MH_flux_NUE_period_summary.csv
```

---

## Notes for reproducibility

1. Keep shapefile sidecar files together. A shapefile requires `.shp`, `.shx`, `.dbf` and `.prj` files at minimum.
2. The notebooks use district and state names for merging. Minor spelling differences are handled in the code through name-cleaning functions, but unmatched districts should still be checked in the printed merge diagnostics.
3. Several notebooks recalculate SNMI internally. This improves standalone reproducibility but can lead to minor differences if the SNMI normalisation settings are changed in one notebook and not another.
4. The analysis uses annual district-level observational data. Policy-related statistical outputs should be interpreted as conditional associations, not causal estimates of individual interventions.
5. Files ending in `.lock` inside the shapefile directory are not required for analysis and can be removed safely if they cause warnings.

---

## Citation

If using this code or derived dataset, please cite the associated manuscript and dataset:

```text
Goyal, S. S., Bhatia, U., de Vries, W., and Kumar, R. Regional policies outweigh agroclimatic conditions in achieving sustainable nitrogen management across Indian croplands. Manuscript under review.
```

Please update this citation with the final journal reference after publication.

---

## Contact

For questions about the code or data structure, please contact:

**Shekhar Sharan Goyal**  
UFZ--Helmholtz Centre for Environmental Research, Leipzig, Germany  
Email: shekhar-sharan.goyal@ufz.de

