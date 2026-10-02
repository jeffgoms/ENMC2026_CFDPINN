# 2D CO2 Displacement of Brine: an ICFERST-informed PINN

This English university tutorial uses a verified ICFERST simulation as reference data for a compact two-output physics-informed neural network. It is not a calibrated storage-site model.

## Run in Google Colab

Open `2D_CO2_Brine_ICFERST_PINN_Colab.ipynb`, choose **Runtime → Change runtime type → T4 GPU**, and run all cells. The notebook performs a real CUDA tensor operation before selecting a GPU. If CUDA is unavailable or initialization fails, it reports the reason and immediately continues on CPU.

The notebook downloads `co2_brine_icferst_teaching.npz` from Google Drive and verifies its SHA-256 before loading. A Colab upload-widget fallback is provided if direct download is unavailable.

ICFERST is **not run during the class**. The prepared NPZ is loaded directly for PINN training and validation. The original ICFERST mesh, MPML deck, metadata, checksum manifest, and Host B validation evidence are distributed as reproducibility attachments.

## What students learn

- ICFERST reference fields are simulation results, not an analytical solution.
- Sparse observation rows carry pressure and CO2-saturation labels.
- Unlabeled collocation points carry coordinates only and enforce two phase mass balances.
- Four complete simulation times are held out from training to prevent leakage.
- A hard initial-state gate and a Buckley–Leverett similarity feature resolve the narrow early plume without using held-out labels.
- Front position, inventory, saturation, and pressure-drop errors reveal different model weaknesses.

`FAST_MODE=True` is the default classroom configuration. Set it to `False` for the higher-accuracy configuration. Runtime depends on Colab GPU availability; CPU execution is supported but slower.

## Fixed teaching problem

CO2 displaces brine in a homogeneous 300 m × 150 m rectangle for 300 days. The frozen model uses porosity 0.20, permeability 1e-12 m², fixed densities and viscosities, Corey relative permeability, a nonuniform left-edge inlet, and a 10 MPa outlet.

The simplifications are important: the case neglects gravity, capillary pressure, dissolution, compressibility, heterogeneity, wells, geochemistry, and parameter uncertainty. It teaches PINN construction and validation; it does not claim site-scale predictive capability.

The MPML applies inlet phase fractions weakly. Exported discontinuous-Galerkin values at `x=0` are cell-field values rather than the exact boundary trace, so the notebook distinguishes those reference values from the prescribed phase-flux boundary physics.

For an optional extension on pressure-, temperature-, and salinity-dependent CO2–NaCl interfacial tension, see Liaqat, Preston & Schaefer (2025), [*Predicting the interfacial tension of CO2 and NaCl aqueous solution with machine learning*](https://doi.org/10.1038/s41598-025-10274-w). That closure is not mixed into this deliberately zero-capillarity benchmark.

## Included artifacts

- `2D_CO2_Brine_ICFERST_PINN_Colab.ipynb`: output-free student notebook.
- `data/co2_brine_icferst_teaching.npz`: compact arrays and immutable split indices.
- `data/co2_brine_icferst_teaching.json`: units, parameters, field mapping, hashes, and validation.
- `inputs/co2_brine_icferst/`: frozen ICFERST mesh, MPML deck, and job provenance.
- `validation/co2_brine/`: accepted metrics, figures, executed notebook, and report.
- `scripts/build_co2_brine_notebook.py`: deterministic notebook builder.

Rebuild locally with `python3 scripts/build_co2_brine_notebook.py`.
