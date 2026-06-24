# UD-CFRP-Tensile-Coupon-Validation-Study
HexPly® 8552/IM7 Unidirectional Prepreg — Ansys ACP + Mechanical FEA vs. Published Allowables

## Overview

This repository documents a finite element analysis (FEA) validation study of a unidirectional carbon fiber/epoxy composite tensile coupon modeled in Ansys Composite PrepPost (ACP) and Ansys Mechanical. The objective is to verify that an ACP orthotropic material model accurately reproduces the published elastic and strength properties of Hexcel HexPly® 8552/IM7 prepreg against manufacturer datasheet values and NCAMP qualification data.

---

## Validation Results

| Check | Reference | FEA Result | Error | Status |
|---|---|---|---|---|
| E1 (Longitudinal Modulus) | 164 GPa | 164.0 GPa | < 0.1% | ✅ Pass |
| σ₁₁ at 2,500 N | 63.6 MPa (analytical) | 63.5 MPa | < 0.2% | ✅ Pass |
| Failure Stress Prediction | 2,723 MPa (XT) | 2,729 MPa | < 0.5% | ✅ Pass |
| Failure Index at 2,500 N | < 1.0 (no failure) | 0.023 | N/A | ✅ Pass |

---

## Material System

| Parameter | Value |
|---|---|
| Material | Hexcel HexPly® 8552/IM7 |
| Fiber | HexTow® IM7 (intermediate modulus PAN-based carbon) |
| Matrix | 8552 amine-cured toughened epoxy |
| Form | Unidirectional prepreg tape |
| Nominal CPT | 0.131 mm (35% RC) |
| Fiber Volume Fraction | ~57.7% |
| Cured Density | 1,570 kg/m³ |

---

## Coupon Geometry (ASTM D3039)

| Parameter | Value |
|---|---|
| Total Length | 250 mm |
| Width | 25 mm |
| Tab Length (each end) | 56 mm |
| Gauge Length | 138 mm |
| Layup | [0]₁₂ (12 plies) |
| Thickness | 1.572 mm |
| Cross-sectional Area | 39.3 mm² |

---

## ACP Material Card — HexPly_8552_IM7_UD

### Orthotropic Elasticity

| Property | Symbol | Value | Unit | Source |
|---|---|---|---|---|
| Young's Modulus (fiber) | E1 | 164,000 | MPa | Hexcel IM7 Datasheet |
| Young's Modulus (transverse) | E2 = E3 | 9,000 | MPa | Hexcel 8552 Datasheet |
| Shear Modulus (in-plane) | G12 = G13 | 5,200 | MPa | Hexcel 8552 Datasheet |
| Shear Modulus (out-of-plane) | G23 | 3,400 | MPa | CMH-17 |
| Poisson's Ratio (major) | ν12 = ν13 | 0.34 | — | Hexcel 8552 Datasheet |
| Poisson's Ratio (transverse) | ν23 | 0.45 | — | CMH-17 |
| Density | ρ | 1,570 | kg/m³ | Hexcel Datasheet |

### Orthotropic Stress Limits

| Property | Symbol | Value | Unit | Source |
|---|---|---|---|---|
| Tensile Strength (fiber) | XT | 2,723 | MPa | Hexcel IM7 Datasheet |
| Compressive Strength (fiber) | XC | 1,689 | MPa | Hexcel IM7 Datasheet |
| Tensile Strength (transverse) | YT | 64 | MPa | Hexcel 8552 Datasheet |
| Compressive Strength (transverse) | YC | 280 | MPa | Hexcel 8552 Datasheet |
| In-plane Shear Strength | S12 | 92 | MPa | Hexcel 8552 Datasheet |

---

## FEA Setup

**Software:** Ansys Workbench 2026 R1 — ACP (Pre) + Static Structural

**Geometry:** Surface body (3 zones: left tab / gauge / right tab) created in SolidWorks, exported as Parasolid (.x_t), imported with shared topology in SpaceClaim.

**Layup (ACP Pre):**
- Material: `HexPly_8552_IM7_UD` (homogenized prepreg lamina — no separate fiber/matrix definition required)
- Fabric thickness: 0.131 mm/ply
- Rosette: Parallel, fiber direction = coupon X-axis
- Modeling ply: [0]₁₂, 12 layers, 0° angle
- Transfer to Mechanical: Shell (surface body)

**Mesh:**
- Element type: SHELL181
- Global element size: 2 mm
- Total nodes: 25,551 | Total elements: 25,000

**Boundary Conditions:**

| BC | Location | Definition |
|---|---|---|
| Fixed Support | hold1 (left tab) | All DOF constrained |
| Force | hold2 (right tab) | Fx = 2,500 N (100 N/mm × 25 mm), Fy = Fz = 0 |

---

## Results

### Gauge Section (Named Selection Scoped)

| Result | Value |
|---|---|
| Normal Stress X — Average | 63.5 MPa |
| Normal Elastic Strain X — Average | 3.871 × 10⁻⁴ m/m |
| Total Deformation — Max | 14.09 mm |

### Apparent Modulus Calculation

```
E1_FEA = σ₁₁ / ε₁₁ = 63.5 MPa / 3.871 × 10⁻⁴ = 164.0 GPa
```

### Failure Load Prediction

```
Reserve Factor  = XT / σ₁₁ = 2,723 / 63.5 = 42.9
F_failure       = 2,500 × 42.9 = 107,250 N
σ_failure       = 107,250 / 39.3 = 2,729 MPa  (vs. XT = 2,723 MPa → error < 0.5%)
```

---

## Repository Structure

```
UD-CFRP-Tensile-Validation/
├── README.md
├── docs/
│   ├── UD_CFRP_Validation_Report.docx
│   └── methodology.md
├── geometry/
│   └── tensile_coupon.x_t
├── acp_model/
│   └── material_card.md
├── results/
│   ├── stress_strain.csv
│   └── validation_summary.md
├── reference_data/
│   └── cmh17_properties.csv
└── images/
    ├── bc_setup.png
    ├── normal_stress_gauge.png
    ├── normal_strain_gauge.png
    └── total_deformation_gauge.png
```

---

## References

1. Hexcel Corporation. *HexTow® IM7 Carbon Fiber Product Data Sheet*. 2023.
2. Hexcel Corporation. *HexPly® 8552 Epoxy Matrix Product Data Sheet*. 2023.
3. Marlett, K. *Hexcel 8552 IM7 Unidirectional Prepreg 190 gsm & 35%RC Qualification Material Property Data Report*. NCAMP Test Report CAM-RP-2009-015 Rev A. NIAR, Wichita State University. April 2011.
4. *CMH-17-1G: Composite Materials Handbook, Volume 1. Polymer Matrix Composites*.
5. *ASTM D3039/D3039M: Standard Test Method for Tensile Properties of Polymer Matrix Composite Materials*.

---

## Author

**Khizar Siddiqui** — Aerospace & Mechanical Engineer  
