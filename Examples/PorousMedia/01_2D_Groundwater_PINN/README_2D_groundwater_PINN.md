# 2D Groundwater Contaminant Transport: A PINN Tutorial

This self-contained PyTorch tutorial is designed for undergraduate students and learners new to physics-informed neural networks (PINNs). It solves the linear advection–diffusion equation in a two-dimensional rectangular, homogeneous aquifer and validates the PINN against a translating Gaussian analytical solution.

The physical domain is $200\,\mathrm{m}\times100\,\mathrm{m}$ and the simulation covers 100 days. Using 100 m and 100 days as characteristic scales gives the nondimensional domain $[0,2]\times[0,1]\times[0,1]$. The default parameters represent a groundwater velocity of $(0.60,0.15)\,\mathrm{m/day}$, a dispersion coefficient of $1.0\,\mathrm{m^2/day}$, and an initial Gaussian plume centred at $(45,35)\,\mathrm{m}$.

## Run in Google Colab

The main file is `2D_groundwater_PINN_Colab.ipynb`. Select it in Google Drive, open it with Google Colaboratory, and choose:

`Runtime → Change runtime type → T4 GPU`

After selecting the GPU, choose `Runtime → Run all`. The notebook performs a real CUDA tensor operation before using the GPU. If Colab does not allocate a GPU or CUDA initialization fails, the code reports the reason and immediately falls back to the CPU; it never reports CPU execution as GPU execution.

No ICFERST installation, mesh files, external datasets, or additional `pip install` commands are required. The PyTorch, NumPy, and Matplotlib packages included in a standard Colab runtime are sufficient.

## Training modes

- `FAST_MODE = True`: classroom demonstration mode with 2,000 Adam iterations by default.
- `FAST_MODE = False`: higher-accuracy mode with more collocation points and 10,000 iterations; it takes substantially longer.

Keep `FAST_MODE = True` for the first run. Free Colab GPU models and availability vary. The notebook also runs on a CPU, although training will be slower.

## Learning path and outputs

The notebook explains, in order:

1. the physics of groundwater contaminant transport;
2. the 2D advection–diffusion equation and translating Gaussian solution;
3. interior, initial-condition, and boundary-condition sampling;
4. a fully connected network and PyTorch automatic differentiation;
5. PDE, initial-condition, and boundary-condition losses;
6. the training-loss history;
7. analytical, PINN, and absolute-error fields at three times; and
8. relative $L_2$ errors on held-out evaluation grids.

The analytical solution is not used to label interior collocation points. It is used only to impose the initial and boundary conditions and to evaluate the trained model independently.

## Physical and teaching limitations

The finite rectangular domain uses time-dependent Dirichlet boundary values taken from the analytical Gaussian solution. This provides a rigorous check for students, but it is not a universal boundary model for real aquifers. The medium is assumed homogeneous and isotropic; velocity and dispersion are constant; and adsorption, reactions, and source or sink terms are neglected.

This example does not model free-phase CO₂ displacing brine or oil displacement. A separate follow-up case will introduce the nonlinear Buckley–Leverett fractional-flow equation for water flooding, including the saturation front, shock formation, and the resulting PINN training difficulty.

## Repository files

- `2D_groundwater_PINN_Colab.ipynb`: self-contained student notebook.
- `scripts/build_notebook.py`: deterministic notebook builder.
- `tests/test_tutorial.py`: physics, sampling, training, and artifact-structure tests.
- `validation/validation_report.md`: recorded execution environment, runtime, and errors.

Rebuild the notebook with:

```bash
python3 scripts/build_notebook.py
```
