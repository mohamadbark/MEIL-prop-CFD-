@Arseni10Lk here's the von Kármán comparison with the candidate formula, `CD_von_karman = 0.85 × CD_cone`, where `CD_cone = 2.1·sin²ε + 0.5·sin ε / √(M² − 1)` and `ε = atan(1 / (2f))`.

<!-- Drag vk_candidate_vs_cfd.png into the comment box here -->

| f | Mach | CD CFD (Fluent) | CD candidate | Candidate error vs CFD |
|:-:|:-:|:-:|:-:|:-:|
| 3 | 1.5 | 0.085944 | 0.11074 | +28.8 % |
| 3 | 2 | 0.080258 | 0.08858 | +10.4 % |
| 5 | 1.5 | 0.034413 | 0.05550 | +61.3 % |
| 5 | 2 | 0.032612 | 0.04209 | +29.1 % |
| 7 | 1.5 | 0.018347 | 0.03614 | +97.0 % |
| 7 | 2 | 0.017574 | 0.02654 | +51.0 % |

The candidate over-predicts in every case, by **+10 % to +97 %**. The error grows with f and is larger at Mach 1.5. There are two reasons:

1. **The cone formula it is built on already over-predicts.** Against the exact cone values, its error ranges from +7.5 % to +50 %, worst at high f and low Mach (see the cone results above).
2. **A fixed 0.85 factor is too large.** The real von Kármán / cone ratio from the CFD and the exact cone values is not constant:

| f | Mach 1.5 | Mach 2 |
|:-:|:-:|:-:|
| 3 | 0.760 | 0.840 |
| 5 | 0.696 | 0.773 |
| 7 | 0.647 | 0.720 |

So a single factor on the cone formula won't hit ±2 %. The ratio drops as f rises and as Mach drops.

<details>
<summary>CSV</summary>

```csv
f,mach,cd_cfd,cd_candidate,candidate_error_pct,ratio_vk_cfd_to_cone_exact
3,1.5,0.085944,0.11074,+28.8,0.760
3,2.0,0.080258,0.08858,+10.4,0.840
5,1.5,0.034413,0.05550,+61.3,0.696
5,2.0,0.032612,0.04209,+29.1,0.773
7,1.5,0.018347,0.03614,+97.0,0.647
7,2.0,0.017574,0.02654,+51.0,0.720
```
</details>
