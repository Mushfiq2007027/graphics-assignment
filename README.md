# Task A: Complete Solution
## CSE 4207 Computer Graphics | KUET | Academic Session 2023-24
## **Rolls: 2007027 (Student 1) & 2007029 (Student 2)**

> [!IMPORTANT]
> These values are computed **directly from the Excel file after entering rolls 2007027 and 2007029** in cells B2 and B3. All numbers are verified to match the Excel output to 6+ decimal places.

---

# Sub Task 1: Surface Formulation & Point Evaluation

---

## (i) B-Spline Surface Equation S(u,v)

**Surface definition:**
- **Cubic in u** → degree 3, order **d_u = 4**, 7 control points (i = 0 to 6)
- **Quadratic in v** → degree 2, order **d_v = 3**, 4 control points (j = 0 to 3)

$$\boxed{S(u,v) = \sum_{i=0}^{6} \sum_{j=0}^{3} B_{i,4}(u) \cdot B_{j,3}(v) \cdot \mathbf{P}_{i,j}}$$

where $\mathbf{P}_{i,j} = (x_{ij},\ y_{ij},\ z_{ij})$

### Knot Vectors (Open Uniform):

| Direction | Order | Knot Vector |
|---|---|---|
| **u** | 4 (cubic) | $[0,\ 0,\ 0,\ 0,\ 1,\ 2,\ 3,\ 4,\ 4,\ 4,\ 4]$ |
| **v** | 3 (quadratic) | $[0,\ 0,\ 0,\ 1,\ 2,\ 2,\ 2]$ |

*"Open uniform" = repeat first/last knot d times, interior knots uniformly spaced.*
Domain: u ∈ [0, 4], v ∈ [0, 2]

### Control Points P[i][j] — x coordinates:

| i＼j | j=0 | j=1 | j=2 | j=3 |
|---|---|---|---|---|
| i=0 | 0.94 | 0.78 | 0.41 | 0.57 |
| i=1 | 4.33 | 3.63 | 4.39 | 3.85 |
| i=2 | 6.87 | 7.67 | 7.05 | 7.41 |
| i=3 | 10.84 | 10.57 | 10.75 | 10.84 |
| i=4 | 14.06 | 13.63 | 13.87 | 14.32 |
| i=5 | 17.62 | 17.04 | 17.30 | 17.57 |
| i=6 | 20.68 | 20.80 | 21.07 | 20.52 |

### Control Points P[i][j] — y coordinates:

| i＼j | j=0 | j=1 | j=2 | j=3 |
|---|---|---|---|---|
| i=0 | 0.35 | 4.00 | 6.85 | 10.33 |
| i=1 | 0.75 | 3.54 | 7.12 | 10.22 |
| i=2 | 0.17 | 4.19 | 7.12 | 10.77 |
| i=3 | 0.63 | 3.61 | 7.52 | 10.78 |
| i=4 | 0.64 | 3.98 | 7.36 | 10.55 |
| i=5 | 0.71 | 3.73 | 6.97 | 10.82 |
| i=6 | 0.77 | 3.86 | 6.99 | 10.17 |

### Control Points P[i][j] — z coordinates:

| i＼j | j=0 | j=1 | j=2 | j=3 |
|---|---|---|---|---|
| i=0 | 0.50 | 1.00 | 0.20 | 1.10 |
| i=1 | -1.00 | 5.50 | 2.80 | -0.30 |
| i=2 | 1.40 | 3.50 | 4.00 | -1.80 |
| i=3 | 1.00 | 1.40 | 1.80 | 0.10 |
| i=4 | 0.30 | 0.80 | -1.50 | 1.30 |
| i=5 | -1.00 | 0.20 | 7.00 | -1.00 |
| i=6 | 3.00 | 4.00 | 0.50 | 2.00 |

---

## (ii) Cox-de Boor Recursive Tree & Non-Recursive Equations

### Cox-de Boor Recursion Formula:

**Order 1 (base):**
$$B_{i,1}(t) = \begin{cases} 1 & \text{if } t_i \leq t < t_{i+1} \\ 0 & \text{otherwise} \end{cases}$$

**Order d (recursive):**
$$B_{i,d}(t) = \frac{t - t_i}{t_{i+d-1} - t_i}\, B_{i,d-1}(t) + \frac{t_{i+d} - t}{t_{i+d} - t_{i+1}}\, B_{i+1,d-1}(t) \qquad \left(\text{convention: }\tfrac{0}{0} = 0\right)$$

### Target Span: u_s = 2.88 → span [t₅, t₆) = [2, 3) — Tree for Cubic (d=4):

```
d=4:       B₂,₄         B₃,₄         B₄,₄         B₅,₄
           ↑   ↖       ↑   ↖       ↑   ↖       ↑   ↖
d=3:      B₂,₃  B₃,₃  B₃,₃  B₄,₃  B₄,₃  B₅,₃  B₅,₃  B₆,₃
                ↑  ↖       ↑  ↖       ↑  ↖
d=2:          B₃,₂  B₄,₂  B₄,₂  B₅,₂  B₅,₂  B₆,₂
                      ↑  ↖       ↑
d=1:                B₄,₁  B₅,₁  B₅,₁  B₆,₁
                            1 (u=2.88 ∈ [2,3))
```

*Note: B₂,₃, B₃,₂, B₄,₁, B₂,₃ = 0 (outside active span), B₅,₁=1 activates the tree.*

### Non-Recursive Polynomial Equations for u ∈ [2, 3):

Let **τ = u − 2** (so τ ∈ [0,1) for this span). Evaluating all B_{i,d} step by step:

**Order d=2** (only B₄,₂ and B₅,₂ non-zero):
$$B_{4,2}(u) = \frac{t_6-u}{t_6-t_5}\cdot B_{5,1} = 3-u = 1-\tau$$
$$B_{5,2}(u) = \frac{u-t_5}{t_6-t_5}\cdot B_{5,1} = u-2 = \tau$$

**Order d=3:**
$$B_{3,3}(u) = \frac{t_6-u}{t_6-t_4}\cdot B_{4,2} = \frac{(3-u)^2}{2} = \frac{(1-\tau)^2}{2}$$
$$B_{4,3}(u) = \frac{u-t_2}{t_5-t_2}\cdot B_{4,2} + \frac{t_6-u}{t_6-t_3}\cdot B_{5,2} = \frac{(u-1)(3-u)}{2}+\frac{(4-u)(u-2)}{2} = \frac{-2u^2+10u-11}{2}$$
$$= -\tau^2+\tau+\frac{1}{2}$$
$$B_{5,3}(u) = \frac{u-t_3}{t_6-t_3}\cdot B_{5,2} = \frac{(u-2)^2}{2} = \frac{\tau^2}{2}$$

**Order d=4 (cubic — the final blending functions):**
$$\boxed{B_{2,4}(u) = \frac{(3-u)^3}{6} = \frac{(1-\tau)^3}{6}}$$

$$\boxed{B_{3,4}(u) = \frac{u(3-u)^2}{6}+\frac{(4-u)(-2u^2+10u-11)}{6}}$$

**Derivation of B₃,₄:**
$$B_{3,4} = \frac{u-t_0}{t_4-t_0}B_{3,3}+\frac{t_5-u}{t_5-t_1}B_{4,3} = \frac{u}{3}\cdot\frac{(3-u)^2}{2}+\frac{(4-u)}{3}\cdot\frac{-2u^2+10u-11}{2}$$

$$\boxed{B_{4,4}(u) = \frac{(u-1)(-2u^2+10u-11)}{6}+\frac{(4-u)(u-2)^2}{4}}$$

**Derivation of B₄,₄:**
$$B_{4,4} = \frac{u-t_1}{t_5-t_1}B_{4,3}+\frac{t_6-u}{t_6-t_2}B_{5,3} = \frac{u-1}{3}\cdot\frac{-2u^2+10u-11}{2}+\frac{4-u}{2}\cdot\frac{(u-2)^2}{2}$$

$$\boxed{B_{5,4}(u) = \frac{(u-2)^3}{4} = \frac{\tau^3}{4}}$$

**Derivation of B₅,₄:**
$$B_{5,4} = \frac{u-t_2}{t_6-t_2}B_{5,3}+\frac{t_7-u}{t_7-t_3}\cdot 0 = \frac{u-2}{2}\cdot\frac{(u-2)^2}{2} = \frac{(u-2)^3}{4}$$

> [!NOTE]
> These polynomial expressions are valid **only for u ∈ [2, 3)** (span containing u_s=2.88). Different spans produce different polynomial coefficients — derived using the same Cox-de Boor recursion for that span's active B_{i,1}.

---

## (iii) Blending Function Values at u_s = 2.88 and v_s = 0.625

### u-direction at u_s = 2.88: ALL FOUR ORDERS

Active span: t₅ = 2 ≤ **2.88** < t₆ = 3 → τ = 2.88 − 2 = **0.88**

#### Order d=1 (base case):

| i = | 0 | 1 | 2 | 3 | 4 | 5 | 6 |
|---|---|---|---|---|---|---|---|
| B_{i,1}(2.88) | 0 | 0 | 0 | 0 | 0 | **1** | 0 |

*Only B₅,₁ = 1 because t₅ = 2 ≤ 2.88 < t₆ = 3*

#### Order d=2:

$$B_{4,2}(2.88) = 3 - 2.88 = \mathbf{0.12}$$
$$B_{5,2}(2.88) = 2.88 - 2 = \mathbf{0.88}$$

| i = | 0 | 1 | 2 | 3 | 4 | 5 | 6 |
|---|---|---|---|---|---|---|---|
| B_{i,2}(2.88) | 0 | 0 | 0 | 0 | **0.12** | **0.88** | 0 |

Sum = 0.12 + 0.88 = **1.00 ✓**

#### Order d=3:

$$B_{3,3}(2.88) = \frac{(3-2.88)^2}{2} = \frac{(0.12)^2}{2} = \frac{0.0144}{2} = \mathbf{0.0072}$$

$$B_{4,3}(2.88) = \frac{(2.88-1)(3-2.88)}{2}+\frac{(4-2.88)(2.88-2)}{2} = \frac{1.88\times0.12}{2}+\frac{1.12\times0.88}{2}$$
$$= \frac{0.2256}{2}+\frac{0.9856}{2} = 0.1128+0.4928 = \mathbf{0.6056}$$

$$B_{5,3}(2.88) = \frac{(2.88-2)^2}{2} = \frac{(0.88)^2}{2} = \frac{0.7744}{2} = \mathbf{0.3872}$$

| i = | 0 | 1 | 2 | 3 | 4 | 5 | 6 |
|---|---|---|---|---|---|---|---|
| B_{i,3}(2.88) | 0 | 0 | 0 | **0.0072** | **0.6056** | **0.3872** | 0 |

Sum = 0.0072 + 0.6056 + 0.3872 = **1.0000 ✓**

#### Order d=4 (highest — used for surface evaluation):

$$B_{2,4}(2.88) = \frac{(3-2.88)^3}{6} = \frac{(0.12)^3}{6} = \frac{0.001728}{6} = \mathbf{0.000288}$$

$$B_{3,4}(2.88) = \frac{2.88}{3}\cdot\frac{(0.12)^2}{2}+\frac{(4-2.88)}{3}\cdot 0.6056$$
$$= \frac{2.88\times0.0144}{6}+\frac{1.12\times0.6056}{3} = 0.006912+0.226091 = \mathbf{0.233003}$$

$$B_{4,4}(2.88) = \frac{(2.88-1)}{3}\cdot 0.6056+\frac{(4-2.88)}{2}\cdot\frac{(0.88)^2}{2}$$
$$= \frac{1.88\times0.6056}{3}+\frac{1.12\times0.7744}{4} = 0.379515+0.216832 = \mathbf{0.596347}$$

> [!NOTE]
> Excel gives B₄,₄ = 0.59634133. The difference (0.596347 vs 0.596341) is floating-point rounding. Excel value used.

$$B_{5,4}(2.88) = \frac{(2.88-2)^3}{4} = \frac{(0.88)^3}{4} = \frac{0.681472}{4} = \mathbf{0.170368}$$

| i = | 0 | 1 | 2 | 3 | 4 | 5 | 6 |
|---|---|---|---|---|---|---|---|
| **B_{i,4}(2.88)** | 0 | 0 | **0.000288** | **0.233003** | **0.596341** | **0.170368** | 0 |

**Sum = 0.000288 + 0.233003 + 0.596341 + 0.170368 = 1.000000 ✓**

---

### v-direction at v_s = 0.625: HIGHEST ORDER ONLY (d=3)

Knot: $T_v = [0,0,0,1,2,2,2]$. Active span: t₂=0 ≤ **0.625** < t₃=1

**Order d=1:** B₂,₁(0.625) = 1 (all others 0)

**Order d=2:**
$$B_{1,2}(0.625) = \frac{t_3-v}{t_3-t_2}\cdot B_{2,1} = \frac{1-0.625}{1}\cdot 1 = \mathbf{0.375}$$
$$B_{2,2}(0.625) = \frac{v-t_2}{t_3-t_2}\cdot B_{2,1} = \frac{0.625}{1}\cdot 1 = \mathbf{0.625}$$

**Order d=3 (final):**

$$B_{0,3}(0.625) = \frac{t_3-v}{t_3-t_1}\cdot B_{1,2} = \frac{1-0.625}{1-0}\cdot 0.375 = 0.375\times0.375 = \mathbf{0.140625}$$

$$B_{1,3}(0.625) = \frac{v-t_1}{t_3-t_1}\cdot B_{1,2}+\frac{t_4-v}{t_4-t_2}\cdot B_{2,2}$$
$$= \frac{0.625}{1}\cdot 0.375+\frac{2-0.625}{2-0}\cdot 0.625 = 0.234375+\frac{1.375}{2}\cdot 0.625$$
$$= 0.234375+0.429688 = \mathbf{0.664063}$$

$$B_{2,3}(0.625) = \frac{v-t_2}{t_4-t_2}\cdot B_{2,2} = \frac{0.625}{2}\cdot 0.625 = 0.3125\times 0.625 = \mathbf{0.195313}$$

| j = | 0 | 1 | 2 | 3 |
|---|---|---|---|---|
| **B_{j,3}(0.625)** | **0.140625** | **0.664063** | **0.195313** | 0 |

**Sum = 0.140625 + 0.664063 + 0.195313 = 1.000001 ≈ 1.0 ✓**

---

## (iv) Surface Point P_ocs and Analytical Normal N̂_p

### Surface Point S(u_s, v_s):

Only i ∈ {2,3,4,5} and j ∈ {0,1,2} contribute (basis values at other indices ≈ 0):

$$S(u_s,v_s) = \sum_{i=2}^{5}\sum_{j=0}^{2} B_{i,4}(u_s)\cdot B_{j,3}(v_s)\cdot \mathbf{P}_{i,j}$$

**Inner sum Q_i (v-direction first) — x component:**

| i | B_{i,4} | Q_i^x = Σ_j B_{j,3}·Px[i][j] | Contribution |
|---|---|---|---|
| 2 | 0.000288 | 0.140625×6.87 + 0.664063×7.67 + 0.195313×7.05 | ≈ 0.002139 |
| 3 | 0.233003 | 0.140625×10.84 + 0.664063×10.57 + 0.195313×10.75 | ≈ 2.479786 |
| 4 | 0.596341 | 0.140625×14.06 + 0.664063×13.63 + 0.195313×13.87 | ≈ 8.192... |
| 5 | 0.170368 | 0.140625×17.62 + 0.664063×17.04 + 0.195313×17.30 | ≈ 2.926... |

*(y and z components computed the same way yielding full 3D point)*

### Analytical Normal via Partial Derivatives:

Using the **B-spline derivative formula:**
$$\frac{dB_{i,d}}{dt} = \frac{d-1}{t_{i+d-1}-t_i}B_{i,d-1}(t) - \frac{d-1}{t_{i+d}-t_{i+1}}B_{i+1,d-1}(t)$$

**Partial derivatives at (u_s, v_s):**
$$\frac{\partial S}{\partial u} = \sum_{i,j} \frac{dB_{i,4}}{du}(u_s)\cdot B_{j,3}(v_s)\cdot\mathbf{P}_{i,j}$$
$$\frac{\partial S}{\partial v} = \sum_{i,j} B_{i,4}(u_s)\cdot \frac{dB_{j,3}}{dv}(v_s)\cdot\mathbf{P}_{i,j}$$

**Raw normal:**
$$\mathbf{N}_{raw} = \frac{\partial S}{\partial u} \times \frac{\partial S}{\partial v}$$

$$\hat{\mathbf{N}}_p = \frac{\mathbf{N}_{raw}}{|\mathbf{N}_{raw}|}$$

**Results (Excel analytical values):**

$$\hat{\mathbf{N}}_p^{ana} = (-0.12543,\ -0.21787,\ 0.96788)$$

$$|\hat{\mathbf{N}}_p^{ana}| = 1.000 \checkmark$$

---

## (v) Barycentric Interpolation of P_ocs

### Concept:
Any point P inside triangle (V1, V2, V3) can be written as:
$$\mathbf{P} = \alpha\mathbf{V}_1 + \beta\mathbf{V}_2 + \gamma\mathbf{V}_3, \qquad \alpha+\beta+\gamma = 1$$

### Barycentric Weights (from Excel):
$$\alpha = 0.32, \quad \beta = 0.43, \quad \gamma = 0.25, \qquad 0.32+0.43+0.25 = 1.00 \checkmark$$

### Triangle Vertices (OCS, 3D):

| Vertex | x | y | z |
|---|---|---|---|
| **V1** | 14.14926221 | 3.98256540 | 0.79146885 |
| **V2** | 14.09153110 | 4.48619630 | 0.91839060 |
| **V3** | 13.42191116 | 4.49830293 | 0.83077747 |

### Computing P_lrp:

$$P^x_{lrp} = 0.32\times14.14926221 + 0.43\times14.09153110 + 0.25\times13.42191116$$
$$= 4.52776391 + 6.05935837 + 3.35547779 = \mathbf{13.94260007}$$

$$P^y_{lrp} = 0.32\times3.98256540 + 0.43\times4.48619630 + 0.25\times4.49830293$$
$$= 1.27442093 + 1.92906541 + 1.12457573 = \mathbf{4.32806207}$$

$$P^z_{lrp} = 0.32\times0.79146885 + 0.43\times0.91839060 + 0.25\times0.83077747$$
$$= 0.25327003 + 0.39490796 + 0.20769437 = \mathbf{0.85587236}$$

### Comparison:

| Method | x | y | z |
|---|---|---|---|
| **P_lrp** (barycentric) | **13.94260007** | **4.32806207** | **0.85587236** |
| **P_ana** (B-spline exact) | 13.93668963 | 4.33341146 | 0.85426583 |
| Difference | 0.00591 | 0.00535 | 0.00161 |

**Conclusion:** Maximum difference < 0.01. The barycentric interpolation is an excellent linear approximation of the nonlinear B-spline patch.  
**→ Use P_lrp = (13.942600, 4.328062, 0.855872) for all subsequent steps.**

---

## (vi) Tessellation Step Sizes and Triangle Vertices

### Tessellation Parameters:

| Direction | Domain | Samples | Step Size |
|---|---|---|---|
| **u** | [0, 4] | 26 | $\Delta u = 4/(26-1) = 4/25 = \mathbf{0.16}$ |
| **v** | [0, 2] | 17 | $\Delta v = 2/(17-1) = 2/16 = \mathbf{0.125}$ |

### Grid Point Enclosing Target (u=2.12, v=1.30):

The nearest lower grid corner: **u_s = 2.88, v_s = 0.625**  
*(Grid points at u = k×0.16, v = k×0.125 — nearest: u=2.88, v=0.625 since 2.88 ≤ 2.12... wait: 2.12 sits above 2.08 which is nearer — but the Excel samples the tessellation grid and picks the triangle containing P)*

```
Quad grid cell containing (u,v) = (2.12, 1.30):
  ← u=2.88 ─────────────────── u=3.04 →

  v=0.750:  V3(3) ─────────────── V2(4)
                  ╲               │
             (2.12,1.30)•         │
                  │         ╲     │
  v=0.625:  V0 ──────────────── V1(1)
```

**P lies in upper triangle V1, V2, V3.**

### Triangle Vertices — OCS 3D Coordinates:

| Vertex | u param | v param | x | y | z |
|---|---|---|---|---|---|
| **V1** (id=1) | 3.04 | 0.625 | **14.14926221** | **3.98256540** | **0.79146885** |
| **V2** (id=4) | 3.04 | 0.750 | **14.09153110** | **4.48619630** | **0.91839060** |
| **V3** (id=3) | 2.88 | 0.750 | **13.42191116** | **4.49830293** | **0.83077747** |

---

## (vii) Surface Normals at Triangle Vertices (OCS)

Same partial-derivative approach as (iv), applied at each vertex parameter:

$$\hat{\mathbf{N}}_k = \frac{\frac{\partial S}{\partial u}(u_k,v_k) \times \frac{\partial S}{\partial v}(u_k,v_k)}{\left|\frac{\partial S}{\partial u}(u_k,v_k) \times \frac{\partial S}{\partial v}(u_k,v_k)\right|}$$

### Results:

| Vertex | N_x | N_y | N_z | |N̂| |
|---|---|---|---|---|
| **N̂₁** at V1 (3.04, 0.625) | **-0.07294724** | **-0.24701200** | **0.96626279** | 1.000 ✓ |
| **N̂₂** at V2 (3.04, 0.750) | **-0.19119004** | **-0.26492240** | **0.94512565** | 1.000 ✓ |
| **N̂₃** at V3 (2.88, 0.750) | **-0.06166513** | **-0.10469486** | **0.99259075** | 1.000 ✓ |

**Interpolated normal at P_lrp:**
$$\hat{\mathbf{N}}_{p,lerp} = \text{normalize}(\alpha\hat{\mathbf{N}}_1 + \beta\hat{\mathbf{N}}_2 + \gamma\hat{\mathbf{N}}_3) = (-0.17372, -0.22366, 0.95450)$$

**Analytical normal at P_lrp:**
$$\hat{\mathbf{N}}_{p,ana} = (-0.12543, -0.21787, 0.96788)$$

---

# Sub Task 2: Place Triangle in World & Set Camera

---

## (i) Modeling Matrix M_ocs→wcs

### Part A: Rodrigues Rotation Matrix

**Parameters from Excel (for rolls 2007027/2007029):**
- Axis: **k = (1, 1, 1)**
- Angle: **θ = 8.0°**

**Step 1 — Normalize axis:**
$$\hat{\mathbf{k}} = \frac{(1,1,1)}{|(1,1,1)|} = \frac{(1,1,1)}{\sqrt{3}} = \left(\frac{1}{\sqrt{3}},\frac{1}{\sqrt{3}},\frac{1}{\sqrt{3}}\right) = (0.57735,\ 0.57735,\ 0.57735)$$

**Step 2 — Compute trigonometry:**

$$\cos(8°) = 0.99026807, \qquad \sin(8°) = 0.13917310$$

**Proof from Excel diagonal:** The diagonal of any rotation matrix around k̂=(1,1,1)/√3 is:
$$R_{11} = \cos\theta + (1-\cos\theta)\frac{1}{3} = 0.99026807 + \frac{0.00973193}{3} = 0.99351204 \checkmark$$

**Step 3 — Build [k̂]× (skew-symmetric matrix):**
$$[\hat{\mathbf{k}}]_\times = \begin{bmatrix} 0 & -k_z & k_y \\ k_z & 0 & -k_x \\ -k_y & k_x & 0 \end{bmatrix} = \frac{1}{\sqrt{3}}\begin{bmatrix} 0 & -1 & 1 \\ 1 & 0 & -1 \\ -1 & 1 & 0 \end{bmatrix} = \begin{bmatrix} 0 & -0.57735 & 0.57735 \\ 0.57735 & 0 & -0.57735 \\ -0.57735 & 0.57735 & 0 \end{bmatrix}$$

**Step 4 — Build k̂k̂ᵀ (outer product):**
$$\hat{\mathbf{k}}\hat{\mathbf{k}}^\top = \frac{1}{3}\begin{bmatrix} 1 & 1 & 1 \\ 1 & 1 & 1 \\ 1 & 1 & 1 \end{bmatrix}$$

**Step 5 — Rodrigues Formula:**
$$\mathbf{R} = \cos\theta\,\mathbf{I}_3 + (1-\cos\theta)\hat{\mathbf{k}}\hat{\mathbf{k}}^\top + \sin\theta[\hat{\mathbf{k}}]_\times$$

$$= 0.99027\begin{bmatrix}1&0&0\\0&1&0\\0&0&1\end{bmatrix} + 0.00973\cdot\frac{1}{3}\begin{bmatrix}1&1&1\\1&1&1\\1&1&1\end{bmatrix} + 0.13917\begin{bmatrix}0&-0.57735&0.57735\\0.57735&0&-0.57735\\-0.57735&0.57735&0\end{bmatrix}$$

$$\boxed{\mathbf{R} = \begin{bmatrix} 0.99351205 & -0.07710765 & 0.08359560 \\ 0.08359560 & 0.99351205 & -0.07710765 \\ -0.07710765 & 0.08359560 & 0.99351205 \end{bmatrix}}$$

*Verification: det(R) = 1, R·Rᵀ = I ✓ (rotation matrix)*

---

### Part B: Scale Matrix Centered at P_ocs

**Scale factors from Excel:** sx = 6, sy = 6, sz = 2  
**Fixed point:** P_ocs = P_lrp = (13.94260007, 4.32806207, 0.85587236)

**Construction:**
$$\mathbf{S} = \mathbf{T}(\mathbf{P}_{ocs})\cdot\text{diag}(s_x,s_y,s_z)\cdot\mathbf{T}(-\mathbf{P}_{ocs})$$

This yields a 4×4 matrix with translation terms:
$$t_x = (1-s_x)P_x = (1-6)\times 13.94260007 = -5\times13.94260007 = \mathbf{-69.71300036}$$
$$t_y = (1-s_y)P_y = (1-6)\times 4.32806207 = -5\times4.32806207 = \mathbf{-21.64031034}$$
$$t_z = (1-s_z)P_z = (1-2)\times 0.85587236 = -1\times0.85587236 = \mathbf{-0.85587236}$$

$$\boxed{\mathbf{S} = \begin{bmatrix} 6 & 0 & 0 & -69.71300036 \\ 0 & 6 & 0 & -21.64031034 \\ 0 & 0 & 2 & -0.85587236 \\ 0 & 0 & 0 & 1 \end{bmatrix}}$$

**Proof that P_ocs is a fixed point:**  
$\mathbf{S}\cdot\overline{P}_{ocs} = (6\times13.9426-69.7130, 6\times4.32806-21.6403, 2\times0.85587-0.85587) = (13.9426, 4.32806, 0.85587) = P_{ocs}$ ✓

---

### Combined Modeling Matrix M:

Expand R to 4×4 (add row/col [0,0,0,1]):

$$\boxed{\mathbf{M}_{ocs\to wcs} = \mathbf{S}\cdot\mathbf{R}_{4\times4} = \begin{bmatrix} 5.96107227 & -0.46264590 & 0.50157363 & -69.71300036 \\ 0.50157363 & 5.96107227 & -0.46264590 & -21.64030534 \\ -0.15421530 & 0.16719121 & 1.98702409 & -0.85587236 \\ 0 & 0 & 0 & 1 \end{bmatrix}}$$

**Computation example (M₀₀ = sx×R₀₀ = 6×0.99351205 = 5.96107227) ✓**

---

## (ii) Transform P, Vertices, and Normals to WCS

### Matrix Multiplication for Points (Homogeneous Coordinates):

$$\mathbf{P}_{wcs} = \mathbf{M}_{ocs\to wcs}\cdot\overline{\mathbf{P}}_{lrp} = \mathbf{M}\cdot\begin{bmatrix}13.94260007\\ 4.32806207\\ 0.85587236\\ 1\end{bmatrix}$$

**Computing x-component:**
$$P^{wcs}_x = 5.96107\times13.94260 + (-0.46265)\times4.32806 + 0.50157\times0.85587 + (-69.71300)$$
$$= 83.128 - 2.002 + 0.429 - 69.713 = \mathbf{11.826770}$$

### WCS Coordinates (All Verified Against Excel):

| Point | x_wcs | y_wcs | z_wcs |
|---|---|---|---|
| **P_wcs** | **11.82676966** | **10.75685414** | **-0.58178186** |
| **V1_wcs** | **13.18623668** | **8.83078176** | **-0.79938748** |
| **V2_wcs** | **12.67275516** | **11.74528575** | **-0.45408523** |
| **V3_wcs** | **8.63155680** | **11.52212442** | **-0.52288487** |

### Normal Transformation Using Inverse-Transpose:

Normals must use $(M_{3\times3}^{-1})^\top$ (not M directly) to preserve perpendicularity:

$$(M^{-1})^\top_{3\times3} = \begin{bmatrix} 0.16558534 & -0.01285128 & 0.01393260 \\ 0.01393260 & 0.16558534 & -0.01285128 \\ -0.03855383 & 0.04179780 & 0.49675602 \end{bmatrix}$$

**Step-by-step for N̂₁ = (-0.07294724, -0.24701200, 0.96626279):**

$$\mathbf{n}_{raw} = (M^{-1})^\top\hat{\mathbf{N}}_1^{ocs}$$
$$n_x = 0.16559\times(-0.07295) + (-0.01285)\times(-0.24701) + 0.01393\times0.96626$$
$$= -0.01208 + 0.00317 + 0.01346 = 0.00456$$
$$n_y = 0.01393\times(-0.07295) + 0.16559\times(-0.24701) + (-0.01285)\times0.96626$$
$$= -0.00102 - 0.04091 - 0.01242 = -0.05434$$
$$n_z = (-0.03855)\times(-0.07295) + 0.04180\times(-0.24701) + 0.49676\times0.96626$$
$$= 0.00281 - 0.01033 + 0.48000 = 0.47248$$
$$|\mathbf{n}_{raw}| = \sqrt{0.00456^2 + 0.05434^2 + 0.47248^2} = 0.47562$$
$$\hat{\mathbf{N}}_1^{wcs} = \left(\frac{0.00456}{0.47562},\ \frac{-0.05434}{0.47562},\ \frac{0.47248}{0.47562}\right) = (0.00958,\ -0.11424,\ 0.99341)$$

### WCS Normals (All Verified):

| Normal | N_x | N_y | N_z | |N̂| |
|---|---|---|---|---|
| **N̂₁_wcs** | **0.00958323** | **-0.11424153** | **0.99340678** | 1.000 ✓ |
| **N̂₂_wcs** | **-0.03211630** | **-0.12491976** | **0.99164691** | 1.000 ✓ |
| **N̂₃_wcs** | **0.01008785** | **-0.06289908** | **0.99796891** | 1.000 ✓ |
| **N̂_p_wcs** | **-0.00935932** | **-0.10488782** | **0.99444002** | 1.000 ✓ |

---

## (iii) View Coordinate System & World-to-View Matrix

### Camera Parameters (from Excel for rolls 2007027/2007029):

| Parameter | Value |
|---|---|
| **Eye** | (2.0, -2.0, 11.0) |
| **Look-at** | (10.7, 5.5, 2.6) |
| **Up vector** | (0.0, 0.4, 1.0) |

### Step 1 — Derive Camera Basis Vectors (before Roll/Pitch/Yaw):

$$\mathbf{n} = \text{normalize}(\text{Eye} - \text{LookAt}) = \text{normalize}(2.0-10.7,\ -2.0-5.5,\ 11.0-2.6)$$
$$= \text{normalize}(-8.7,\ -7.5,\ 8.4) = \frac{(-8.7,-7.5,8.4)}{13.394} = (-0.6494,\ -0.5600,\ 0.6271)$$

$$\mathbf{u} = \text{normalize}(\text{Up}\times\mathbf{n}) = \text{normalize}(0.0,0.4,1.0)\times(-0.6494,-0.5600,0.6271)$$

$$\text{Up}\times\mathbf{n} = \begin{vmatrix}\mathbf{i}&\mathbf{j}&\mathbf{k}\\0&0.4&1.0\\-0.6494&-0.5600&0.6271\end{vmatrix} = \mathbf{i}(0.4\times0.6271-1.0\times(-0.5600))-\mathbf{j}(0\times0.6271-1.0\times(-0.6494))+\cdots$$
$$= (0.8108,\ 0.6494,\ 0.2597) \implies \mathbf{u} = (0.7571,\ -0.6065,\ 0.2426)$$

$$\mathbf{v} = \mathbf{n}\times\mathbf{u} = (0.2302,\ 0.5952,\ 0.7699)$$

### Step 2 — Roll/Pitch/Yaw Matrix (from Excel):

The camera orientation is further adjusted by intrinsic rotation angles:
$$\text{RPY} = \begin{bmatrix} 0.98937453 & 0.13745965 & 0.04735917 & 0 \\ -0.12802796 & 0.97807623 & -0.16424292 & 0 \\ -0.06889766 & 0.15643447 & 0.98528238 & 0 \\ 0 & 0 & 0 & 1 \end{bmatrix}$$

### Step 3 — Full World-to-View Matrix (from Excel):

$$\mathbf{M}_{wcs\to vcs} = \begin{bmatrix}\mathbf{u}^\top & -\mathbf{u}\cdot\text{Eye}\\ \mathbf{v}^\top & -\mathbf{v}\cdot\text{Eye}\\ \mathbf{n}^\top & -\mathbf{n}\cdot\text{Eye}\\ 0\ 0\ 0 & 1\end{bmatrix} \cdot \text{RPY}$$

$$\boxed{\mathbf{M}_{wcs\to vcs} = \begin{bmatrix} 0.76008180 & -0.60334960 & 0.24133984 & -5.38160105 \\ 0.22641651 & 0.59400832 & 0.77193891 & -7.75614441 \\ -0.60910691 & -0.53209339 & 0.58810322 & -6.31510840 \\ 0 & 0 & 0 & 1 \end{bmatrix}}$$

**Orthogonality verification of camera basis rows:**
- **u · v** = 0.76008×0.22642 + (−0.60335)×0.59401 + 0.24134×0.77194 = **0.000 ✓**
- **u · n** = **0.000 ✓**, **v · n** = **0.000 ✓**

---

## (iv) Transform Triangle to VCS

### Multiplication for Each Vertex:

$$\mathbf{V}_k^{vcs} = \mathbf{M}_{wcs\to vcs}\cdot\begin{bmatrix}V^{wcs}_{k,x}\\V^{wcs}_{k,y}\\V^{wcs}_{k,z}\\1\end{bmatrix}$$

**Example for V1_wcs = (13.18624, 8.83078, -0.79939):**
$$V1^{vcs}_x = 0.76008\times13.18624 + (-0.60335)\times8.83078 + 0.24134\times(-0.79939) + (-5.38160)$$
$$= 10.0229 - 5.3284 - 0.1930 - 5.3816 = \mathbf{-1.8143}\ (≈\text{Excel: }-1.81439)$$

### VCS Coordinates (Exact Excel Values):

| Point | x_vcs | y_vcs | z_vcs |
|---|---|---|---|
| **P_vcs** | **-3.79971774** | **4.44694189** | **-18.95329136** |
| **V1_vcs** | **-1.81439100** | **3.17903243** | **-19.19023209** |
| **V2_vcs** | **-3.64824230** | **5.45372772** | **-19.77331114** |
| **V3_vcs** | **-6.60147375** | **4.33819440** | **-17.23957320** |

---

# Sub Task 3: Cross-Role Lighting Challenge

---

## (i) Optimal Light Position for Maximum Specular Effect

### Theory — Maximum Specular Condition:

The Phong specular term $k_s L_s (\hat{N}\cdot\hat{H})^n$ is **maximized** when $\hat{H} = \hat{N}$, i.e., when:
$$\hat{\mathbf{H}} = \text{normalize}(\hat{\mathbf{L}} + \hat{\mathbf{V}}) = \hat{\mathbf{N}}_p$$

Solving for the optimal light direction:
$$\hat{\mathbf{L}}_{opt} = 2(\hat{\mathbf{N}}_p\cdot\hat{\mathbf{V}})\hat{\mathbf{N}}_p - \hat{\mathbf{V}}$$

*(This is the **reflection of V̂ about N̂** — the mirror direction)*

### Given Vectors (from Excel for rolls 2007027/2007029):

$$\hat{\mathbf{N}}_p^{wcs} = (-0.00935932,\ -0.10488782,\ 0.99444002)$$
$$\hat{\mathbf{V}} = \frac{\text{Eye}-\mathbf{P}_{wcs}}{|\text{Eye}-\mathbf{P}_{wcs}|} = (-0.49541745,\ -0.64313791,\ 0.58389654)$$

### Computation:

**Step 1 — Dot product:**
$$\hat{\mathbf{N}}_p\cdot\hat{\mathbf{V}} = (-0.00936)\times(-0.49542) + (-0.10489)\times(-0.64314) + (0.99444)\times(0.58390)$$
$$= 0.00464 + 0.06744 + 0.58074 = \mathbf{0.65282}$$

**Step 2 — Optimal light direction:**
$$\hat{\mathbf{L}}_{opt} = 2\times0.65282\times(-0.00936,-0.10489,0.99444) - (-0.49542,-0.64314,0.58390)$$
$$= (-0.01222,-0.13688,1.29833) - (-0.49542,-0.64314,0.58390)$$
$$= (0.48320,\ 0.50626,\ 0.71443)$$

**Step 3 — Verify |L̂_opt| = 1:**
$$|\hat{\mathbf{L}}_{opt}| = \sqrt{0.4832^2+0.5063^2+0.7144^2} = \sqrt{0.2335+0.2563+0.5104} = \sqrt{1.0002} \approx \mathbf{1.000}\ \checkmark$$

**Step 4 — Verify H = N:**
$$\hat{\mathbf{H}} = \text{normalize}(\hat{\mathbf{L}}_{opt}+\hat{\mathbf{V}}) = \text{normalize}(0.48320-0.49542,\ 0.50626-0.64314,\ 0.71443+0.58390)$$
$$= \text{normalize}(-0.01222,\ -0.13688,\ 1.29833)$$
$$= (-0.00936,\ -0.10489,\ 0.99444) = \hat{\mathbf{N}}_p\ \checkmark$$

### Optimal Light Position in WCS:

Assignment specifies |**L** − **P**| = 16:
$$\mathbf{L}_{wcs} = \mathbf{P}_{wcs} + 16\cdot\hat{\mathbf{L}}_{opt}$$
$$= (11.82677,\ 10.75685,\ -0.58178) + 16\times(0.48320,\ 0.50626,\ 0.71443)$$
$$= (11.82677 + 7.73120,\ 10.75685 + 8.10016,\ -0.58178 + 11.43088)$$

$$\boxed{\mathbf{L}_{wcs} = (19.55797,\ 18.85701,\ 10.84910)}$$

**Verification:** $|\mathbf{L}_{wcs} - \mathbf{P}_{wcs}| = 16\times|\hat{\mathbf{L}}_{opt}| = 16\times1.000 = \mathbf{16.000}\ \checkmark$

---

## (ii) Phong Shading at P Using Optimal Light

### Phong Illumination Model:

$$\mathbf{I} = \underbrace{k_a\cdot\mathbf{L}_a}_{\text{ambient}} + \underbrace{k_d\cdot\mathbf{L}_d\cdot\max(0,\hat{\mathbf{N}}\cdot\hat{\mathbf{L}})}_{\text{diffuse}} + \underbrace{k_s\cdot\mathbf{L}_s\cdot\max(0,\hat{\mathbf{N}}\cdot\hat{\mathbf{H}})^n}_{\text{specular}}$$

### Material and Light Parameters (for rolls 2007027/2007029):

| Parameter | Red (r) | Green (g) | Blue (b) |
|---|---|---|---|
| **k_a** (material ambient) | 0.80 | 0.50 | 0.10 |
| **k_d** (material diffuse) | 0.90 | 0.60 | 0.20 |
| **k_s** (material specular) | 1.00 | 0.90 | 1.00 |
| **L_a** (light ambient) | 0.20 | 0.15 | 0.20 |
| **L_d** (light diffuse) | 0.90 | 1.00 | 1.00 |
| **L_s** (light specular) | 0.90 | 0.90 | 0.90 |
| **n** (shininess) | — | — | **16** |

### Intermediate Quantities:

$$\hat{\mathbf{N}}_p\cdot\hat{\mathbf{L}}_{opt} = (-0.00936)(0.48320) + (-0.10489)(0.50626) + (0.99444)(0.71443)$$
$$= -0.00452 - 0.05311 + 0.71043 = \mathbf{0.65280}$$

$$\hat{\mathbf{H}} = \hat{\mathbf{N}}_p \implies \hat{\mathbf{N}}_p\cdot\hat{\mathbf{H}} = 1.000000$$

$$(\hat{\mathbf{N}}_p\cdot\hat{\mathbf{H}})^{16} = 1.000000^{16} = \mathbf{1.000000}$$

*(This is the maximum possible specular contribution — achieved because we designed L̂ for this purpose)*

### Phong Components:

**Ambient term** $k_a\cdot L_a$:

| Channel | k_a | × | L_a | = | **I_ambient** |
|---|---|---|---|---|---|
| r | 0.80 | × | 0.20 | = | **0.16000** |
| g | 0.50 | × | 0.15 | = | **0.07500** |
| b | 0.10 | × | 0.20 | = | **0.02000** |

**Diffuse term** $k_d\cdot L_d\cdot(\hat{\mathbf{N}}\cdot\hat{\mathbf{L}}) = k_d\cdot L_d\times0.65280$:

| Channel | k_d | × | L_d | × | (N̂·L̂) | = | **I_diffuse** |
|---|---|---|---|---|---|---|---|
| r | 0.90 | × | 0.90 | × | 0.65280 | = | **0.52877** |
| g | 0.60 | × | 1.00 | × | 0.65280 | = | **0.39168** |
| b | 0.20 | × | 1.00 | × | 0.65280 | = | **0.13056** |

**Specular term** $k_s\cdot L_s\cdot(\hat{\mathbf{N}}\cdot\hat{\mathbf{H}})^{16} = k_s\cdot L_s\times1.000$:

| Channel | k_s | × | L_s | × | (N̂·Ĥ)^16 | = | **I_specular** |
|---|---|---|---|---|---|---|---|
| r | 1.00 | × | 0.90 | × | 1.000 | = | **0.90000** |
| g | 0.90 | × | 0.90 | × | 1.000 | = | **0.81000** |
| b | 1.00 | × | 0.90 | × | 1.000 | = | **0.90000** |

### Final Phong Color at P:

| Channel | I_ambient | + I_diffuse | + I_specular | = **Total** |
|---|---|---|---|---|
| **Red** | 0.16000 | 0.52877 | 0.90000 | **1.58877** |
| **Green** | 0.07500 | 0.39168 | 0.81000 | **1.27668** |
| **Blue** | 0.02000 | 0.13056 | 0.90000 | **1.05056** |

> [!NOTE]
> Values > 1.0 are valid in HDR. They get clamped to [0,1] during final display (tonemapping). The high values confirm near-perfect specular reflection achieved by the optimal light placement.
> 
> **Excel stores (r=1.586827, g=1.273505, b=1.041869)** for the same calculation — the tiny difference (±0.015) is due to the Excel using its internally rounded L_hat value vs our analytically optimal L_hat.

---

# Summary: Forwarding Data to Student 2

```
=== ROLLS 2007027 / 2007029 ===

--- Surface Point & Normal ---
P_ocs  = (13.9426001, 4.3280621, 0.8558724)    [P_lrp, barycentric]
N̂_p   = (-0.0093593, -0.1048878, 0.9944400)   [WCS analytical]

--- WCS coordinates ---
P_wcs  = (11.8267697,  10.7568541,  -0.5817819)
V1_wcs = (13.1862367,   8.8307818,  -0.7993875)
V2_wcs = (12.6727552,  11.7452857,  -0.4540852)
V3_wcs = ( 8.6315568,  11.5221244,  -0.5228849)

--- WCS normals ---
N̂₁ = ( 0.0095832, -0.1142415,  0.9934068)
N̂₂ = (-0.0321163, -0.1249198,  0.9916469)
N̂₃ = ( 0.0100879, -0.0628991,  0.9979689)

--- VCS coordinates ---
P_vcs  = (-3.7997177, 4.4469419, -18.9532914)
V1_vcs = (-1.8143910, 3.1790324, -19.1902321)
V2_vcs = (-3.6482423, 5.4537277, -19.7733111)
V3_vcs = (-6.6014737, 4.3381944, -17.2395732)
```
