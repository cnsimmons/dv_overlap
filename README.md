# dv_overlap
Whole-brain connectivity analyses of dorsal and ventral object-selective regions (Simmons, Behrmann &amp; Ayzenberg, Communications Biology 2026).

# Large-scale functional overlap between dorsal and ventral object-responsive networks

Analysis code for Simmons, Behrmann & Ayzenberg, *Communications Biology* (2026).

Data: https://doi.org/10.1184/R1/31459006

---

## Figure → script

| Figure | Panels | Script |
|---|---|---|
| 1 | A–B | Stimuli; no analysis code |
| 2 | A–C | `exp_1/fc_ppi/fc_ppi.py` → `native2mni.py` → `threshold_results.py`; surfaces via `exp_1/results/figures/plot_surface.py` |
| 2 | D | `paper_content/revision_work.ipynb` |
| 2 | E–G | `exp_1/network/merged_atlas.ipynb` |
| 3 | A–C | `exp_1/fc_ppi/fc_ppi.py` (PPI) → `native2mni.py` → `threshold_results.py` |
| 3 | D | `paper_content/revision_work.ipynb` |
| 3 | E–G | `exp_1/network/merged_atlas.ipynb` |
| 4 | A–C | `exp_1/fc_ppi/residuals/partial_residual.py` → `native2mni_residual.py` → `threshold_results.py` |
| 4 | D | `paper_content/revision_work.ipynb` |
| 4 | E–G | `exp_1/network/merged_atlas.ipynb` |
| 5 | A–B | `exp_1/gca/gca_searchlight.py` → `combine_and_transform2mni_searchlight.py` → `threshold_results.py`; plots via `gca_viz.ipynb` |
| 6 | A–F | `exp_2/fc_ppi/fc_ppi.py` → `native2mni.py` → `threshold_results.py` |
| 7 | — | `exp_2/fc_ppi/ppi_tools_vs_nontools.py` → `native2mni_toolsvnon.py` → `threshold_tools_vs_nontools.py` |
| Supp 1 | — | Anatomical parcels; `exp_1/results/figures/plot_surface.py` |
| Supp 2 | — | `exp_1/fc_ppi/acompcor/compcor_fc_ppi_arg.py` → `native2mni_compcor.py` → `threshold_results_compcor.py` → `dice_compare_compcor.py` |
| Supp 3 | — | `exp_1/fc_ppi/acompcor/dice_figure_stats.py` (V1 control) |
| Supp 4 | — | `exp_1/fc_ppi/acompcor/supp_fig_pfs_fig3d.py` (pFS control) |

Source data workbook for the panels above: `paper_content/pull_excel.py`

---

## Pipeline order

1. **Preprocessing** — `preprocess.py` (motion outliers, brain extraction; FSL FEAT run separately)
2. **Seed definition** — `create_rois.py`, then peak-voxel sphere coordinates per participant
3. **Connectivity** — `fc_ppi.py` (functional connectivity and PPI), `partial_residual.py` (partial correlation), `gca_searchlight.py` (Granger causality)
4. **Normalization** — `native2mni*.py` (native space → MNI 2 mm)
5. **Thresholding** — `threshold_results*.py` (z-standardization, FDR *p* < 0.05, ≥5 contiguous voxels)
6. **Quantification** — `merged_atlas.ipynb` (Schaefer-Wang-Julian parcel analyses), `revision_work.ipynb` (Dice coefficients and statistics)

`ptoc_params.py` holds shared paths and parameters and is imported by most scripts. Paths point to the original compute environment and will need editing.

---

## Layout

```
ptoc_params.py              shared paths, subject lists, parameters
preprocess.py               motion outliers, skull stripping
create_rois.py              anatomical parcel → functional ROI
sub_info.csv                Experiment 1 participants
sub_info_tool.csv           Experiment 2 participants
task_info.csv               task/condition/cope mapping

exp_1/
  fc_ppi/                   functional connectivity and PPI
    residuals/              partial correlation (one pathway regressed out)
    acompcor/               aCompCor replication, V1 and pFS controls
  gca/                      Granger causality searchlight
  network/                  merged atlas, parcel-level analyses
  subtraction/              overlap/difference maps
  activation/               ROI visualization
  results/figures/          surface rendering (pycortex)

exp_2/
  fc_ppi/                   functional connectivity, PPI, tools vs non-tools
  gca/                      Granger causality searchlight
  activation/              registration and ROI checks

paper_content/
  revision_work.ipynb       Dice panels and statistics (Figures 2D, 3D, 4D)
  pull_excel.py             source data workbook
  sim+diff_*.ipynb          similarity/difference analyses
```

---

## Environments

Two conda environments were used:

- **Connectivity analyses** — Python 3.9.16, nibabel 5.2.1, nilearn 0.10.4, numpy 1.26.4, scipy 1.10.1, statsmodels 0.14.0
- **Granger causality** — Python 3.7.12, brainiak 0.11, scipy 1.7.3, statsmodels 0.13.2, scikit-learn 1.0.2

FSL 6.0.1 for preprocessing and first-level modeling. Surface visualization uses pycortex with the `fsaverage` subject and `atlas_2mm` transform.

---

## Notes

Scripts were written to run on a SLURM cluster with the directory structure defined in `ptoc_params.py`; several take a participant ID as a command-line argument and were submitted per participant. They are provided as a record of the analyses reported in the paper rather than as a turnkey pipeline.
