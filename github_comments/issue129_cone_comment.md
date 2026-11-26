## Cone results: all 12 cases done ✅

All 12 cone cases (f = 3, 5, 7 × Mach 1.5, 2, 3, 5) are finished in Ansys Fluent (2D axisymmetric Euler, inviscid). **Every case is within 0.3 % of the exact Taylor–Maccoll solution, and 10 of the 12 are within 0.02 %** (issue tolerance: 2 %).

The candidate cone formula over-predicts the drag in **every** case, by **+7.5 % to +50 %** (worst for slender noses at low Mach), so it should not be used as it stands. The values below can be used to fit a replacement for #75.

📄 **Full report** (method, mesh study, mesh quality, and every CD, residual, Mach and Cp plot): **[YAADO #129 – Cone CFD report (PDF)](https://drive.google.com/file/d/1tM2wQFL13i8EXfFnTO2lHPkbLV6Kdb0p/view?usp=drivesdk)**

<!-- Drag cone_summary_all12.png into the comment box here -->

### Results

CD = nose pressure drag only, reference area π·R².

| f = L/D | Mach | CD exact (Taylor–Maccoll) | **CD Fluent** | Fluent error | CD candidate formula | Formula error |
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| 3 | 1.5 | 0.113151 | **0.113159** | +0.007 % | 0.13028 | +15.1 % |
| 3 | 2 | 0.095529 | **0.095536** | +0.007 % | 0.10421 | +9.1 % |
| 3 | 3 | 0.079836 | **0.079795** | −0.051 % | 0.08582 | +7.5 % |
| 3 | 5 | 0.067867 | **0.067672** | −0.288 % | 0.07354 | +8.4 % |
| 5 | 1.5 | 0.049448 | **0.049452** | +0.009 % | 0.06529 | +32.0 % |
| 5 | 2 | 0.042165 | **0.042170** | +0.013 % | 0.04952 | +17.4 % |
| 5 | 3 | 0.035023 | **0.035028** | +0.014 % | 0.03838 | +9.6 % |
| 5 | 5 | 0.028720 | **0.028703** | −0.060 % | 0.03095 | +7.8 % |
| 7 | 1.5 | 0.028344 | **0.028347** | +0.012 % | 0.04252 | +50.0 % |
| 7 | 2 | 0.024397 | **0.024400** | +0.012 % | 0.03123 | +28.0 % |
| 7 | 3 | 0.020368 | **0.020372** | +0.016 % | 0.02325 | +14.2 % |
| 7 | 5 | 0.016558 | **0.016559** | +0.011 % | 0.01793 | +8.3 % |

The exact values come from a Taylor–Maccoll solver, which reproduces the issue's reference value of 0.0955 for f = 3, Mach 2.

<details>
<summary>Raw data (CSV) for fitting the formula in #75</summary>

```csv
f,mach,cone_half_angle_deg,cd_exact_taylor_maccoll,cd_fluent,fluent_error_pct,cd_candidate_formula,formula_error_pct
3,1.5,9.462,0.113151,0.113159,0.007,0.13028,15.1
3,2.0,9.462,0.095529,0.095536,0.007,0.10421,9.1
3,3.0,9.462,0.079836,0.079795,-0.051,0.08582,7.5
3,5.0,9.462,0.067867,0.067672,-0.288,0.07354,8.4
5,1.5,5.711,0.049448,0.049452,0.009,0.06529,32.0
5,2.0,5.711,0.042165,0.042170,0.013,0.04952,17.4
5,3.0,5.711,0.035023,0.035028,0.014,0.03838,9.6
5,5.0,5.711,0.028720,0.028703,-0.060,0.03095,7.8
7,1.5,4.086,0.028344,0.028347,0.012,0.04252,50.0
7,2.0,4.086,0.024397,0.024400,0.012,0.03123,28.0
7,3.0,4.086,0.020368,0.020372,0.016,0.02325,14.2
7,5.0,4.086,0.016558,0.016559,0.011,0.01793,8.3
```
</details>

### Verification

**Mesh study** (cone f = 3, Mach 2; exact value 0.095529):

| Mesh | Global / nose + axis size / growth | Cells | Skewness max | Orth. quality min | CD | Error |
|---|---|---|---|---|---|---|
| Coarse | 4 mm / 1 mm / 1.1 | 25,266 | 0.686 | 0.528 | 0.095608 | +0.083 % |
| Medium | 2 mm / 0.5 mm / 1.1 | 94,774 | 0.664 | 0.615 | 0.095552 | +0.024 % |
| Fine | 2 mm / 0.25 mm / 1.05 | 119,142 | 0.747 | 0.520 | 0.095536 | +0.007 % |

- Medium → fine change: **0.017 %** (requirement < 1 %). Observed order of accuracy ≈ 1.8.
- ⚠️ The fine mesh refines the **nose and axis region only**; the global size stays at 2 mm, because a uniform 1 mm mesh was too slow to generate on the Student licence. All production runs use the fine-mesh settings.
- Production mesh cells: f = 3: 119,142 · f = 5: 206,903 · f = 7: 313,753 (skewness max ≤ 0.75, orthogonal quality min ≥ 0.29).

**Physics checks**

- Shock angle at f = 3, Mach 2: about 30–31° (theory 31.0°).
- Mach number on the cone surface at f = 3, Mach 2: 1.84 (exact 1.847).
- Cp along each nose at Mach 5 is flat at the CD value, as cone theory predicts.
- At Mach 1.5 the shock leaves through the outlet, not the top boundary, so the domain is large enough.
- The largest error (f = 3, Mach 5, −0.29 %) comes from the thin shock layer at high Mach, which spans fewer cells.

### Setup (please use the same for the ogive and von Kármán, so results are comparable)

- **Domain** (mm, D = 100): 100 ahead of the tip; nose L = 300 / 500 / 700; cylinder 200 (2D) running out through the outlet; box 600×600 / 800×800 / 1000×1000. Sized so that the Mach 1.5 shock exits through the outlet.
- **Mesh:** Quadrilateral Dominant, all quad, 2 mm global, **0.25 mm (Hard) on the nose and the axis ahead of it**, growth rate 1.05.
- **Fluent:** density-based, implicit, Roe-FDS, least squares, steady, **2D axisymmetric, inviscid**, air as an ideal gas (γ = 1.4), **operating pressure 0**.
- **Boundaries:** pressure far-field (101325 Pa, 288.15 K, Mach M along the axis); pressure outlet (101325 Pa, backflow total temperature = T0: 417.8 / 518.7 / 806.8 / 1728.9 K for Mach 1.5 / 2 / 3 / 5); axis; walls.
- **CD:** drag coefficient on `nose_wall` only. Reference values computed from the far-field **for each Mach number**, area 0.007853982 m².
- **Run:** first-order upwind to 1e-6, then second-order upwind to 1e-8.

<details>
<summary>Lessons learned (pitfalls to avoid)</summary>

- Recompute the reference values from the far-field after every Mach change. Otherwise CD is off by (M_new / M_old)². I hit this once: 2.25× too high.
- After changing the geometry, named selections and sizings can lose their scoping, and recreated zones arrive in Fluent with default types (the far-field came in as a wall). Re-check every boundary.
- No special characters (á) or OneDrive in Ansys project paths, otherwise Fluent cannot find its mesh file.
- For curved noses: drawing the nose as an arc tangent to the cylinder gives a **tangent ogive**, not a von Kármán. Import the von Kármán curve from a point file and check the exported `nose_wall` coordinates against the formula.

</details>
