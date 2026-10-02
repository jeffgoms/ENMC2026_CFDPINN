# 2D Buckley–Leverett Waterflood: A PINN Tutorial

This self-contained English PyTorch tutorial is the second case in the university PINN series. It models immiscible water displacement of oil in a homogeneous $300\,\mathrm{m}\times150\,\mathrm{m}$ rectangular reservoir and compares the PINN against an analytical Buckley–Leverett entropy solution.

The flow direction is oblique, so saturation depends on both spatial coordinates. The benchmark remains planar in the flow-normal coordinate, which makes rigorous shock validation possible. It is not a heterogeneous, well-driven, field-scale 2D reservoir model.

## Run in Google Colab

Open `2D_Buckley_Leverett_PINN_Colab.ipynb` with Google Colaboratory and choose:

`Runtime → Change runtime type → T4 GPU`

Then choose `Runtime → Run all`. The notebook performs a real CUDA allocation and arithmetic probe. If no usable GPU is available or CUDA initialization fails, it prints the reason and immediately switches to the CPU.

No ICFERST installation, external data, mesh file, or additional `pip install` command is required. A standard Colab runtime already includes the required PyTorch, NumPy, and Matplotlib packages.

## Training modes

- `FAST_MODE = True`: classroom configuration with 3,000 Adam iterations.
- `ACCURATE_MODE`: select this by setting `FAST_MODE = False`; it uses more points and 10,000 iterations.

Use `FAST_MODE` for the first run. Colab GPU type and availability vary, and CPU execution is slower.

## Physics and learning goals

The notebook covers effective water saturation, Corey relative permeability, nonlinear fractional flow, characteristics, the Rankine–Hugoniot tangent condition, rarefaction, and shock propagation. The PINN loss combines a conservative pointwise residual with initial, boundary, global mass-balance, monotonicity, and Rankine–Hugoniot shock-speed terms. The shock-speed term is derived from conservation and supplies no interior saturation labels.

The analytical entropy solution supplies initial and boundary values and an independent held-out reference. It is never used as an interior collocation label. Reported outputs include:

- fractional-flow and characteristic-speed curves;
- uniform and front-aware point samples;
- loss-component histories;
- analytical, predicted, and absolute-error saturation fields at three times;
- relative $L_2$, mean absolute, front-position, and domain-average mass errors;
- predicted physical saturation bounds.

## How to interpret the result

The physical water-saturation range is $0.20\le S_w\le0.80$. The sigmoid network output enforces this range by construction. Because the continuous PINN smears the discontinuity, its numerical front is defined by the steepest saturation descent, with sub-grid interpolation, rather than by the left shock-state contour. The front-position error is measured normal to the analytical shock plane; a dimensionless error of 0.05 corresponds to 7.5 m.

A finite `tanh` PINN is continuous whereas the entropy solution contains a discontinuity. The predicted front is therefore smoothed. This is expected and is discussed explicitly rather than hidden behind a single aggregate error. Front position and mass conservation should be examined alongside relative $L_2$ error.

## Assumptions and limitations

The example neglects gravity, capillary pressure, compressibility, wells, spatial heterogeneity, pressure coupling, and parameter calibration. The oblique planar construction is genuinely evaluated on a 2D rectangle but reduces along the flow-normal coordinate. It is a controlled teaching benchmark, not a claim of field-scale predictive capability.

## Local files

- `2D_Buckley_Leverett_PINN_Colab.ipynb`: output-free student notebook.
- `scripts/build_buckley_leverett_notebook.py`: deterministic notebook builder.
- `tests/test_buckley_leverett_tutorial.py`: physics, training, and artifact tests.
- `validation/buckley_leverett_validation_report.md`: measured runtime and accuracy evidence.

Rebuild the notebook with:

```bash
python3 scripts/build_buckley_leverett_notebook.py
```
