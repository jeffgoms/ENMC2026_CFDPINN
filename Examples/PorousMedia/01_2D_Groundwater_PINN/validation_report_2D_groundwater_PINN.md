# Validation report: 2D groundwater PINN tutorial

Validation date: 2026-09-21 (Europe/London)

## Result

The self-contained notebook completed every cell successfully in its default classroom configuration. The actual local validation device was CPU because the host NVIDIA driver was unavailable. The notebook's CUDA allocation probe and CPU fallback branch were both tested; no local GPU runtime is claimed.

## Environment

- Python: 3.8.10
- PyTorch: 2.4.1+cpu (temporary isolated install under `/tmp`)
- NumPy: 1.24.3
- Matplotlib: 3.1.2
- nbformat: 5.10.4
- nbclient: 0.10.1
- device selected by notebook: `cpu`
- domain: `[0, 2] x [0, 1] x [0, 1]`
- seed: 42

The PyTorch and Jupyter validation dependencies were installed into `/tmp/pinn-simplecase-deps`; system Python packages were not modified.

## Mathematical and unit checks

Command:

```bash
PYTHONPATH=/tmp/pinn-simplecase-deps python3 -m pytest tests/test_tutorial.py -v
```

Result before end-to-end execution: 12 passed. After adding the nbformat cell-ID regression check, the final suite contains 13 tests.

Key checks include:

- the analytical Gaussian's maximum automatic-differentiation PDE residual was `8.196e-08` in float32;
- interior, initial, and four-edge boundary samples satisfy their geometric contracts;
- a simulated CUDA allocation failure selects CPU and reports the failure reason;
- model output and PDE residual shapes are `(N, 1)` and remain finite;
- non-finite training losses stop immediately with the iteration number;
- analytical interior values are absent from the training loss;
- relative L2 error uses the reference-field norm and rejects a zero reference;
- the notebook is valid, self-contained JSON with unique nbformat 4.5 cell IDs.

## End-to-end smoke execution

A temporary notebook copy overrode the training configuration to 5 iterations with 64 PDE, 32 initial, and 16 points per boundary. Every notebook cell executed, including all three plots and the three-time evaluation. This smoke run validates notebook ordering and execution mechanics; its solution error is not used as a quality result.

## Default classroom execution

Configuration:

```text
n_pde=1000
n_ic=256
n_bc_each=128
iterations=2000
learning_rate=1e-3
network=3 hidden layers x 48 neurons
```

Observed CPU training time: `125.5 s` in the final inline-figure run.

| Quantity | Initial | Final |
|---|---:|---:|
| Total loss | 2.705e-01 | 9.460e-03 |
| PDE loss | 3.437e-03 | 5.024e-03 |
| Initial-condition loss | 2.534e-02 | 4.037e-04 |
| Boundary-condition loss | 1.369e-03 | 3.979e-05 |

Because points are resampled each iteration, individual component losses need not decrease monotonically. The total loss decreased by a factor of approximately 28.6.

Held-out `161 x 81` grid results:

| Nondimensional time | Relative L2 error |
|---:|---:|
| 0.0 | 0.14245 |
| 0.5 | 0.17006 |
| 1.0 | 0.22338 |

The final prediction reproduces the plume position, broadening, and peak reduction at all three times. Errors grow with forecast time, which is an appropriate point for classroom discussion rather than a hidden limitation.

## Visual inspection

The following figures were extracted from the final 2,000-iteration execution and inspected:

- `figures/sampling_points.png`
- `figures/training_history.png`
- `figures/solution_comparison.png`

The figures have readable labels, a 2:1 spatial aspect ratio, shared exact/prediction concentration limits at each time, separate absolute-error colorbars, and no clipped titles or legends.

The executed validation artifact is `validation/2D_groundwater_PINN_fast_executed.ipynb`. The student-facing notebook remains output-free and deterministically reproducible from `scripts/build_notebook.py`.

## Limitations

- Local GPU performance was not measured because this host had no working NVIDIA driver. Colab GPU preference is metadata plus runtime device probing, not a locally verified speed claim.
- Free Colab GPU assignment is controlled by Google and is not guaranteed.
- Time-dependent analytical Dirichlet data provide a clean verification problem but are not a universal aquifer boundary model.
- This linear example deliberately excludes multiphase relative permeability and Buckley–Leverett shocks; those belong to the separate waterflood tutorial.
