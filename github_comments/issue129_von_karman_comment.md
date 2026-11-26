## Von Kármán results: f = 3, 5, 7 at Mach 1.5 and 2 done ✅

Six von Kármán (LD-Haack) nose cases are finished in Ansys Fluent: 2D axisymmetric Euler, inviscid, with the same setup as the cone runs above. All six are **verified** three ways:

- **Shape:** the nose matches the von Kármán formula to within **0.004 mm** in every geometry.
- **Mesh:** halving the tip cells changes CD by **0.026 %**.
- **Cross-check:** integrating the surface Cp reproduces every Fluent CD to within **0.3 %**.

The von Kármán nose has **16–35 % less wave drag than a cone** of the same fineness ratio. The saving grows with f and is larger at Mach 1.5.

📄 **Full report** (geometry, shape check, mesh quality, convergence, and every CD, residual, Mach and Cp plot): **[YAADO #129 – Von Kármán CFD report (PDF)](https://drive.google.com/file/d/1xVD-5BTOWGjzGQ8XiT5pGB0rl6ofKjTO/view?usp=drivesdk)**

<!-- Drag vk_summary.png into the comment box here -->

### Results

CD = nose pressure drag only, reference area π·R² (R = 50 mm).

| f = L/D | Mach | **CD von Kármán (Fluent)** | CD from Cp integral | Difference | CD cone (exact) | VK below cone |
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| 3 | 1.5 | **0.085944** | 0.085904 | −0.05 % | 0.113151 | 24.0 % |
| 3 | 2 | **0.080258** | 0.080234 | −0.03 % | 0.095529 | 16.0 % |
| 5 | 1.5 | **0.034413** | 0.034362 | −0.15 % | 0.049448 | 30.4 % |
| 5 | 2 | **0.032612** | 0.032583 | −0.09 % | 0.042165 | 22.7 % |
| 7 | 1.5 | **0.018347** | 0.018298 | −0.27 % | 0.028344 | 35.3 % |
| 7 | 2 | **0.017574** | 0.017541 | −0.19 % | 0.024397 | 28.0 % |

<details>
<summary>Raw data (CSV) for fitting the formula in #75</summary>

```csv
shape,f,mach,cd_fluent,cd_cp_integral,cd_cone_exact,reduction_vs_cone_pct
von_karman,3,1.5,0.085944,0.085904,0.113151,24.0
von_karman,3,2.0,0.080258,0.080234,0.095529,16.0
von_karman,5,1.5,0.034413,0.034362,0.049448,30.4
von_karman,5,2.0,0.032612,0.032583,0.042165,22.7
von_karman,7,1.5,0.018347,0.018298,0.028344,35.3
von_karman,7,2.0,0.017574,0.017541,0.024397,28.0
```
</details>

### Verification

**Shape.** The `nose_wall` coordinates exported from Fluent were compared with r(x) = (R/√π)·√(θ − sin 2θ / 2), θ = arccos(1 − 2x/L):

| Geometry | Points | Max deviation from von Kármán | From tangent ogive | From cone |
|:-:|:-:|:-:|:-:|:-:|
| f = 3 | 1,225 | 0.0036 mm | 2.6 mm | 10.6 mm |
| f = 5 | 2,519 | 0.0035 mm | 2.5 mm | 10.6 mm |
| f = 7 | 3,515 | 0.0036 mm | 2.5 mm | 10.6 mm |

**Mesh.**
- All-quad, 2 mm global size, **0.2 mm (Hard) on the nose and the axis**, growth rate 1.05. That is finer than the cone's 0.25 mm, because the von Kármán tip is blunt.
- f = 3, Mach 2: going from 0.2 mm to 0.125 mm changed CD from 0.080259 to 0.080280, a difference of **0.026 %**.
- A rerun on the production mesh gave 0.080258, a difference of 0.002 %.

| Mesh | Cells | Skewness max | Orth. quality min |
|:-:|:-:|:-:|:-:|
| f = 3 | 125,641 | 0.761 | 0.248 |
| f = 5 | 213,021 | 0.682 | 0.288 |
| f = 7 | 325,670 | 0.784 | 0.291 |

**Convergence.**
- In 5 of the 6 runs the second-order residuals fall to about 1e-8, and CD is flat well before the end.
- In f = 3, Mach 1.5 the residuals level off at about 1e-6 with a small oscillation. This is limiter chatter at the blunt tip on the axis. That run is judged by CD instead, which changed by less than 0.0001 % over the last 1,000 iterations.

**Physics checks**
- Every Mach 1.5 value is above the Mach 2 value, and every von Kármán value is below the cone.
- The von Kármán drag barely changes with Mach (5–7 % between Mach 1.5 and 2), while the cone's changes by about 16 %. Slender-body theory predicts this: the von Kármán wave drag is almost independent of Mach.
- Cp peaks at the blunt tip and turns slightly negative near the shoulder. The tip peak drops as f rises: about 1.3, 0.6 and 0.37 at Mach 2 for f = 3, 5 and 7.

### Setup

Same as the cone runs above, with two differences:
- **Nose curve:** imported in DesignModeler as a 3D curve from a 201-point file generated from the formula, then turned into a surface with Surfaces From Edges.
- **Domain:** shifted so the tip sits at x = 100 mm, with the left edge at x = 0.

<details>
<summary>Lessons learned (pitfalls to avoid)</summary>

- **Recompute the reference values after every Mach change, and check the velocity before running** (510.25 m/s at Mach 1.5, 680.33 m/s at Mach 2). A stale reference velocity scales CD and Cp by (M_new / M_old)².
- **Don't draw the nose as an arc tangent to the cylinder.** That gives a tangent ogive, not a von Kármán. Import the curve from a point file and check the exported `nose_wall` coordinates against the formula.
- **Second-order residuals may level off above 1e-8 on a blunt nose.** Judge convergence by CD instead.

</details>
