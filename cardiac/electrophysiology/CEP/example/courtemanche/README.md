# Courtemanche–Ramirez–Nattel (CRN) human atrial myocyte

Courtemanche M, Ramirez RJ, Nattel S. *Am J Physiol* 275:H301–H321, 1998.
Model keyword: `crn` (or `courtemanche`). nX = 6, nG = 15.

Run:

```
../../bin/cep.exe in_CRN.dat        # writes log_CRN.txt (26 columns)
```

`in_CRN.dat` reproduces the paper's stimulus (2-ms, −2 nA pulses at
1000 ms) for a single 600-ms beat from rest; set
`Number of time steps: 2400000` for the paper's full 12-s protocol.
`plot_CRN.m` documents the log column map.

## Units — read this before editing the deck

The three human myocyte models in this solver use **three different unit
systems**. Stimulus blocks, time steps and conductivities are *not*
portable between their decks:

|                    | **CRN** (this)   | **NYG**        | **TTP**            |
|--------------------|------------------|----------------|--------------------|
| time base          | **ms**           | **s**          | ms                 |
| currents           | absolute pA      | absolute pA    | densities pA/pF    |
| Cm                 | 100 pF           | 0.05 nF        | 0.185 µF           |
| dV/dt              | −ΣI/Cm           | −ΣI/Cm         | −ΣI (no Cm)        |
| stimulus amplitude | **pA**           | **pA**         | **pA/pF**          |
| example deck       | −2000, dt 0.005  | −280, dt 1e−4  | −38, dt 0.1–0.2    |

Why: `pA/pF = mV/ms` fixes CRN's clock to milliseconds, `pA/nF = mV/s`
fixes NYG's to seconds, and TTP's currents are already densities
(= mV/ms) so its dV/dt carries no Cm at all. A TTP amplitude of −38 is a
*density* (≈ −3800 pA absolute); the −2000 here is absolute pA. For
tissue simulation, conductivity follows the clock: mm²/ms for CRN vs
mm²/s for NYG.

CRN must stay in absolute pA: Eq. 68's `Fn` thresholds (SR release
trigger) only fire with absolute currents — a pA/pF port silently
disables calcium release. Details: `include/PARAMS_CRN.f` (UNIT SYSTEM
block) and the `Fn_rel_scale` comment there for the one deliberate
deviation from the published equations (openCARP's Fig. 15 encoding,
default 0.01; set 1.0 in `params_crn.in` for Eq. 68 as published).

## Figures

Generated from this Fortran implementation (steady state, 12 s of
pacing), overlaid with the validated Python reference (black dashed)
and, where available, points digitised from the published figures:

| file | contents |
|---|---|
| `fig14_ap_currents.png` / `fig14_overlay.png` | AP + membrane currents (paper Fig. 14) |
| `fig15_ca_handling.png` / `fig15_overlay.png` | Ca dynamics (paper Fig. 15; overlay flux panel: model ×1/100 onto the published axis — the published panel is mis-scaled by 100×) |
| `fig16_rate_dependence.png` | BCL 1 / 0.6 / 0.3 s (paper Fig. 16) |
| `plot_CRN_12s_table3.png` | 12-s protocol + biomarkers vs paper Table 3 |

Fortran vs Python agreement: all 26 logged quantities identical to the
log's full 10-significant-digit precision at every step (max |ΔV| =
5e-9 mV), for FE, RK4 and both `Fn_rel_scale` settings.
