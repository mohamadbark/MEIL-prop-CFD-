# YAADO #129 — Cone nose wave drag (Fluent, 2D axisymmetric Euler)

All 12 cone cases: f = L/D ∈ {3, 5, 7} × Mach ∈ {1.5, 2, 3, 5}.
Every case is within **0.3 %** of the exact inviscid (Taylor–Maccoll) value; 10 of 12 are within 0.02 %. Issue tolerance: 2 %.

## Results

| f | Mach | CD exact (Taylor–Maccoll) | **CD Fluent** | CFD error | Candidate formula | formula error |
|---|---|---|---|---|---|---|
| 3 | 1.5 | 0.113151 | **0.113159** | +0.007 % | 0.13028 | +15.1 % |
| 3 | 2   | 0.095529 | **0.095536** | +0.007 % | 0.10421 | +9.1 % |
| 3 | 3   | 0.079836 | **0.079795** | −0.051 % | 0.08582 | +7.5 % |
| 3 | 5   | 0.067867 | **0.067672** | −0.288 % | 0.07354 | +8.4 % |
| 5 | 1.5 | 0.049448 | **0.049452** | +0.009 % | 0.06529 | +32.0 % |
| 5 | 2   | 0.042165 | **0.042170** | +0.013 % | 0.04952 | +17.4 % |
| 5 | 3   | 0.035023 | **0.035028** | +0.014 % | 0.03838 | +9.6 % |
| 5 | 5   | 0.028720 | **0.028703** | −0.060 % | 0.03095 | +7.8 % |
| 7 | 1.5 | 0.028344 | **0.028347** | +0.012 % | 0.04252 | +50.0 % |
| 7 | 2   | 0.024397 | **0.024400** | +0.012 % | 0.03123 | +28.0 % |
| 7 | 3   | 0.020368 | **0.020372** | +0.016 % | 0.02325 | +14.2 % |
| 7 | 5   | 0.016558 | **0.016559** | +0.011 % | 0.01793 | +8.3 % |

Findings:
- The Fluent setup reproduces the exact cone drag in every case.
- The largest error is at f = 3, Mach 5 (−0.29 %). At high Mach the shock lies close to the cone, so the thin shock layer is covered by fewer cells.
- The issue's candidate cone formula over-predicts in every case, by +7.5 % up to +50 % (worst for slender noses at low Mach), so it should be replaced.
- Cp along the nose is flat at the CD value (cone theory), see `*_M5_cp.png`.

## Mesh study (f = 3, Mach 2)

| Mesh | Global / nose+axis edge size / growth | Cells | Skew max | Orth. quality min | CD | error |
|---|---|---|---|---|---|---|
| Coarse | 4 mm / 1 mm / 1.1 | 25,266 | 0.686 | 0.528 | 0.095608 | +0.083 % |
| Medium | 2 mm / 0.5 mm / 1.1 | 94,774 | 0.664 | 0.615 | 0.095552 | +0.024 % |
| Fine | 2 mm / 0.25 mm / 1.05 | 119,142 | 0.747 | 0.520 | 0.095536 | +0.007 % |

Medium → fine change: 0.017 % (requirement < 1 %). Note: the fine mesh refines the nose/axis region only (global size kept at 2 mm), because a uniform 1 mm mesh was too slow to generate.

All production runs use the fine-mesh settings:

| Production mesh | Cells | Skewness max | Orth. quality min | Aspect ratio max |
|---|---|---|---|---|
| f = 3 | 119,142 | 0.747 | 0.520 | — |
| f = 5 | 206,903 | 0.678 | 0.293 | 6.3 |
| f = 7 | 313,753 | 0.552 | 0.353 | 5.3 |

(f = 3 from Ansys Meshing; f = 5 and f = 7 from Fluent: Report Quality and the max of Cell Equiangle Skew.)

## Setup
- Geometry (mm, D = 100): space before tip 100, nose length 300 / 500 / 700, cylinder 200 (2D), box width 600 / 800 / 1000, height 600 / 800 / 1000. The box is sized so the Mach 1.5 shock leaves through the outlet.
- Mesh: Quadrilateral Dominant (All Quad), 2 mm global, 0.25 mm (Hard) on nose + axis, growth rate 1.05.
- Fluent: density-based, steady, 2D axisymmetric, inviscid, air as ideal gas (γ = 1.4), operating pressure 0.
- Boundaries: pressure far-field (101325 Pa, 288.15 K, Mach M) on the top and left edges; pressure outlet (101325 Pa, backflow T0) on the right edge; axis; walls.
- Reference area π·0.05² = 0.007853982 m², reference values computed from the far-field. CD = drag on `nose_wall` only.
- Roe-FDS, implicit, least squares; first-order upwind start, then second-order upwind until residuals reach 1e-8.

## Files
- `pictures/summary/`: overview plot of all 12 cases, results CSV, mesh-convergence plot
- `pictures/mesh_study_f3_M2/`: CD, residuals, Mach contour and mesh pictures for coarse / medium / fine
- `pictures/f3/`, `pictures/f5/`, `pictures/f7/`: per Mach number `_cd`, `_residuals`, `_mach`, plus `_M5_cp` (Cp along the nose) and `_mesh_full` / `_mesh_tip` for each production mesh

## Optional
- A picture of the coarse mesh (the mesh study has pictures of the medium and fine meshes only)
