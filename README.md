# Settlement Morphology and Rural Population Undercount in East Africa

Independent research project quantifying whether global gridded population datasets (WorldPop, GHS-POP) systematically undercount people living in dispersed rural settlements, using a fine-tuned geospatial foundation model across five East African countries: **Kenya, Tanzania, Uganda, Rwanda, and Burundi**.

Motivated by Láng-Ritter et al. (2025, *Nature Communications*), which showed gridded population products undercount rural populations globally but did not identify *which kinds* of rural settlements drive the error. This project tests the hypothesis that the undercount concentrates in low building-density ("isolated") settlement morphologies — scattered homesteads that satellite-derived building products struggle to see — relative to clustered villages.

## Key Findings

- **Direction-consistent morphology gap in 5/5 countries.** Stratifying rural DHS survey clusters by Google Open Buildings density terciles, gridded population products underrepresent low-density (isolated) settlement areas relative to clustered ones, with country-level bootstrap confidence intervals excluding zero against all three independent reference products.
- **Circularity diagnostic.** Because reference population products partially depend on building footprints, the gap could in principle be by-construction. A scaling test was designed: if circularity drove the result, bias should scale monotonically with each product's footprint-dependence. Observed deviations ran in the opposite direction.
- **Scope of claims.** Absolute bias magnitudes are sensitive to dwelling-area calibration assumptions, so claims are restricted to the direction and consistency of the gap, not effect sizes.

## Pipeline

| Notebook | Purpose |
|---|---|
| `00_setup_auth.ipynb` | Google Earth Engine + HuggingFace authentication, Drive setup |
| `01_sentinel_preprocessing.ipynb` | Continental-scale GEE preprocessing: 8-channel Sentinel-1 SAR + Sentinel-2 optical composites (dry + wet season), patch export |
| `02_model_finetuning.ipynb` | Prithvi-EO-1.0-100M (NASA/IBM) semantic segmentation fine-tuning: frozen encoder, ~31.5M trainable decoder params, weighted cross-entropy for extreme class imbalance (settlements <1% of pixels); crash-resumable tiled inference |
| `03_crossvalidation.ipynb` | Cross-validation of predictions against three independent building footprint products (Google Open Buildings, HOT OSM, Microsoft GlobalML) at 500 m grid aggregation |
| `04_confidence_population.ipynb` | Random Forest confidence surface (12-feature cell vectors); population bias quantification at DHS cluster locations, stratified by settlement morphology |
| `05_figures.ipynb` | Manuscript figures |

**Model:** three-class settlement maps (background / isolated / clustered) at 10 m resolution; ~175K labeled 256×256 training patches; ~96–100% cell-level recall at the 500 m analysis scale.

**Statistical machinery:** bootstrap confidence intervals, spatial-block cross-validation design, variogram-based leakage bounds, log-ratio robustness checks.

## Data Sources

- Sentinel-1 SAR & Sentinel-2 optical imagery (Google Earth Engine)
- Google Open Buildings v3, HOT OpenStreetMap, Microsoft GlobalML building footprints
- WorldPop (constrained & unconstrained), GHS-POP gridded population products
- GHS-SMOD settlement classification (rural stratification)
- DHS survey cluster locations and household occupancy

## Running

Notebooks are designed for Google Colab with an A100 GPU and Google Drive persistent storage. You will need your own Google Earth Engine project ID and a HuggingFace access token (placeholders are marked in `00` and `02`). Large intermediate artifacts (GeoTIFFs, patch caches, prediction rasters) are not included in this repository.

```
pip install -r requirements.txt
```

## Status

Research complete. The accompanying manuscript was taken through eight revision cycles to submission-ready state as independent undergraduate research (Jackson School of Geosciences, UT Austin).

## Reference

Láng-Ritter, J., et al. (2025). Global gridded population datasets systematically underrepresent rural population. *Nature Communications*.

## License

MIT — see [LICENSE](LICENSE).
