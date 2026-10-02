# Case 3 validation report

Mode: `accurate`  
Device: `cuda`  
Runtime: `459.2 s`  
Verdict: `PASS`

| Held-out metric | Measured | Frozen limit | Pass |
|---|---:|---:|:---:|
| saturation_rel_l2_max | 0.130803 | 0.2 | yes |
| saturation_mae_max | 0.0217565 | 0.06 | yes |
| front_error_mean_m | 4.41335 | 24 | yes |
| inventory_error_max | 0.0343497 | 0.08 | yes |
| pressure_drop_mae_max | 0.033581 | 0.1 | yes |

All metrics use complete held-out ICFERST times. ICFERST is a numerical reference, not an analytical solution.
