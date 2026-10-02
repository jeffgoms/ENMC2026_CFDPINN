# Validation report: 2D Buckley–Leverett waterflood PINN tutorial

Validation date: 2026-09-22 (Europe/London)

## Result

The output-free student notebook completed every cell successfully in its default `FAST_MODE` classroom configuration. The final run met the predefined maximum front-position error criterion of 0.05. The local host did not provide a working CUDA device, so the notebook correctly selected CPU; no local GPU performance is claimed.

## Environment

- Python: 3.8.10
- PyTorch: 2.4.1+cpu
- NumPy: 1.24.3
- Matplotlib: 3.1.2
- nbformat: 5.10.4
- nbclient: 0.10.1
- selected device: `cpu`
- deterministic seed: 42
- dimensionless domain: `[0, 2] x [0, 1] x [0, 1]`

Temporary validation dependencies were isolated under `/tmp/pinn-simplecase-deps`; system packages were not modified.

## Physics checks

| Quantity | Verified value |
|---|---:|
| Shock effective saturation | 0.57735027 |
| Shock physical water saturation | 0.54641014 |
| Dimensionless shock speed | 1.36602540 |
| Rankine–Hugoniot tangent mismatch | 4.441e-16 |
| Maximum smooth-region PDE residual | 1.687e-09 |

The entropy evaluator handles `t=0` separately, uses bounded bisection only in the rarefaction, and remains finite at all saturation endpoints. Analytical saturation values are absent from interior collocation losses.

## Sampling and shock treatment

Uniform PDE points cover the full domain. Additional front-aware points lie within a normal-distance band of 0.04 around the analytical shock but exclude the central half-band where the inviscid entropy solution is discontinuous. Those points use a label-free Rankine–Hugoniot kinematic loss that enforces the derived shock speed. They do not receive analytical saturation labels.

The PINN is continuous, so its numerical shock is defined by the steepest saturation descent with sub-grid quadratic interpolation. A regression test using a manufactured smooth moving front demonstrated that this locator recovers the known front within 0.005. Using the left shock-state contour instead would systematically identify the rear edge of the smeared transition and is therefore not used as the front-position metric.

## End-to-end smoke execution

A temporary notebook copy used 64 uniform PDE, 32 front-aware, 32 initial, and 16 points per boundary for five Adam steps. All 27 notebook cells executed with `allow_errors=False`; four PNG figures and the final metrics table were produced. The smoke run validated execution order and plotting only, not solution accuracy.

## Default classroom execution

Configuration:

```text
n_pde=1200
n_front=400
n_ic=300
n_bc_each=100
front_band=0.04
iterations=3000
learning_rate=1e-3
network=4 hidden layers x 64 neurons
weights={pde:1, ic:10, bc:10, mass:1, mono:0.1, rh:1}
```

Observed CPU training time: `30.8 s` with four OpenMP/MKL threads.

| Loss component | Iteration 1 | Iteration 3000 |
|---|---:|---:|
| Total | 4.330e+00 | 1.474e-01 |
| PDE | 1.985e-04 | 6.647e-02 |
| Initial condition | 2.305e-01 | 3.330e-03 |
| Boundary condition | 2.023e-01 | 4.054e-03 |
| Mass balance | 9.832e-04 | 5.495e-05 |
| Monotonicity | 0.000e+00 | 0.000e+00 |
| Rankine–Hugoniot kinematics | 4.900e-04 | 7.026e-03 |

Fresh random samples are drawn each iteration, so individual losses fluctuate and final sampled losses need not be their historical minima.

## Held-out results

The evaluation grid contained `161 x 81` points at each time.

| Time | Relative L2 | MAE | Front error | Mean saturation error | Predicted min | Predicted max |
|---:|---:|---:|---:|---:|---:|---:|
| 0.25 | 0.14322 | 0.020341 | 0.006129 | 0.009451 | 0.2014 | 0.7948 |
| 0.60 | 0.11387 | 0.021446 | 0.022467 | 0.007781 | 0.2013 | 0.7952 |
| 1.00 | 0.095737 | 0.022355 | 0.029631 | 0.005994 | 0.2015 | 0.7947 |

The maximum front-position error was `0.029631`, below the required `0.05` dimensionless threshold. With the 150 m length scale, this corresponds to approximately 4.44 m and is below the 7.5 m acceptance limit. Predictions remained within the physical `[0.20, 0.80]` range by construction.

## Visual inspection

The following extracted figures were inspected at original resolution:

- `buckley_leverett_figures/fractional_flow.png`
- `buckley_leverett_figures/sampling_points.png`
- `buckley_leverett_figures/training_history.png`
- `buckley_leverett_figures/solution_comparison.png`

Labels and legends are readable, the spatial panels use the intended 2:1 aspect ratio, exact and predicted saturation panels share `[0.20, 0.80]` color limits, error panels have separate scales, and the analytical shock plane and predicted steepest-descent front are visibly distinguishable. No titles, axes, or colorbars are clipped.

## Reproducibility and artifacts

- Student notebook: `2D_Buckley_Leverett_PINN_Colab.ipynb` (output-free, deterministic builder output)
- Executed evidence: `validation/2D_Buckley_Leverett_PINN_fast_executed.ipynb`
- Builder: `scripts/build_buckley_leverett_notebook.py`
- Tests: `tests/test_buckley_leverett_tutorial.py`

The final test suite checks physics, entropy branches, sampling geometry, the front exclusion gap, CUDA-failure fallback, saturation bounds, residual shapes, loss validation, short training, non-finite guards, front-location semantics, notebook structure, deterministic rebuilding, held-out evaluation, and English-only student artifacts.

## Limitations

- GPU speed was not measured locally; Colab GPU availability remains controlled by Google.
- A finite `tanh` network smooths an inviscid shock even when its location and mass are accurate.
- The oblique planar solution is a controlled 2D benchmark, not a heterogeneous well-pattern simulation.
- Gravity, capillary pressure, compressibility, wells, pressure coupling, and calibrated field data are outside this tutorial's scope.
