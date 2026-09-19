# Large-scale functional overlap between dorsal and ventral object-responsive networks

Analysis code for Simmons, Behrmann & Ayzenberg, *Communications Biology* (2026).

Data: https://doi.org/10.1184/R1/31459006

---

## Figure → script

| Figure | Panels | Script |
|---|---|---|
| 1 | A–B | Stimuli only; no analysis code |
| 2 | A–C | `exp1/connectivity/fc_ppi.py` → `native2mni.py` → `threshold_results.py`; surfaces via `figures/plot_surface.py` |
| 2 | D | `figures/revision_work.ipynb` |
| 2 | E–G | `exp1/parcels/merged_atlas.ipynb`, `exp1_connectivity_fingerprint_viz_pearsonr.py` |
| 3 | A–C | `exp1/connectivity/fc_ppi.py` (PPI) → `native2mni.py` → `threshold_results.py` |
| 3 | D | `figures/revision_work.ipynb` |
| 3 | E–G | `exp1/parcels/merged_atlas.ipynb` |
| 4 | A–C | `exp1/connectivity/partial_residual.py` → `native2mni_residual.py` → `threshold_results_residual.py`; overlap subtraction via `exp1/subtraction/` |
| 4 | D | `figures/revision_work.ipynb` |
| 4 | E–G | `exp1/parcels/merged_atlas.ipynb` |
| 5 | A–B | `exp1/gca/gca_searchlight.py` → `combine_and_transform2mni_searchlight.py` → `threshold_results.py` |
| 6 | A–F | `exp2/connectivity/fc_ppi.py` → `native2mni.py` → `threshold_results.py` |
| 7 | — | `exp2/connectivity/ppi_tools_vs_nontools.py` → `native2mni_toolsvnon.py` → `threshold_tools_vs_nontools.py` |
| Supp 1 | — | Anatomical parcels; `figures/plot_surface.py` |
| Supp 2 | — | `exp1/acompcor/compcor_fc_ppi_arg.py` → `native2mni_compcor.py` → `threshold_results_compcor.py` → `dice_compare_compcor.py` |
| Supp 3 | — | `exp1/acompcor/dice_figure_stats.py` (V1 control) |
| Supp 4 | — | `exp1/acompcor/supp_fig_pfs_fig3d.py` (pFS control) |

Source data workbook for the plotted panels: `figures/pull_excel.py`

One-back task performance (Methods): `behavioral/score_oneback.py`,
`behavioral/oneback_standalone.html`

---

## Pipeline order

1. **Preprocessing** — see `preprocessing/README.md` for the full sequence:
   covariate files from `events.tsv`, brain extraction, motion outliers, the
   FEAT first-level GLM, and registration into the spaces the connectivity
   analyses expect. Seed parcels are built with `create_rois.py` and
   `generate_v1_coords.py`.
2. **aCompCor** (robustness check, Supplementary Figure 2) — see
   `preprocessing/acompcor/README.md`. Adds 5 CSF + 5 white-matter principal
   components as confound regressors and re-runs the GLM on a parallel path.
3. **Connectivity** — functional connectivity and PPI (`fc_ppi.py`), partial
   correlation with the other pathway regressed out (`partial_residual.py`),
   Granger causality searchlight (`gca_searchlight.py`).
4. **Normalization** — `native2mni*.py`, native space → MNI 2 mm.
5. **Thresholding** — `threshold_results*.py`: z-standardization,
   FDR *p* < 0.05, minimum five contiguous voxels.
6. **Quantification** — `exp1/parcels/merged_atlas.ipynb` for the
   Schaefer-Wang-Julian parcel analyses; `figures/revision_work.ipynb` for
   Dice coefficients and the associated statistics.

---

## Layout

```
ptoc_params.py              shared paths and parameters; imported by most scripts
sub_info.csv                Experiment 1 participants
sub_info_tool.csv           Experiment 2 participants
task_info.csv               task / condition / cope mapping

preprocessing/              raw data → registered first-level output
  acompcor/                 aCompCor variant of the same pipeline

exp1/
  connectivity/             functional connectivity, PPI, partial correlation
  acompcor/                 aCompCor replication; V1 and pFS control analyses
  gca/                      Granger causality searchlight
  parcels/                  merged-atlas and parcel-level analyses
  subtraction/              overlap / difference maps

exp2/
  connectivity/             functional connectivity, PPI, tools vs non-tools

behavioral/                 one-back task and scoring
figures/                    figure panels, surface rendering, source data
```

---

## Environments

Two conda environments were used:

- **Connectivity analyses** — Python 3.9.16, nibabel 5.2.1, nilearn 0.10.4,
  numpy 1.26.4, scipy 1.10.1, statsmodels 0.14.0
- **Granger causality** — Python 3.7.12, brainiak 0.11, scipy 1.7.3,
  statsmodels 0.13.2, scikit-learn 1.0.2

FSL 6.0.1 for preprocessing and first-level modeling. Surface visualization
uses pycortex with the `fsaverage` subject and the `atlas_2mm` transform.

---

## Notes

Scripts were written for a SLURM cluster with the directory structure defined in
`ptoc_params.py`; several take a participant ID as a command-line argument and
were submitted per participant. `raw_dir`, `data_dir`, `results_dir`, and
`fig_dir` in `ptoc_params.py` point to the original compute environment and need
editing to run elsewhere. The code is provided as a record of the analyses
reported in the paper rather than as a turnkey pipeline.
