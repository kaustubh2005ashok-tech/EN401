# Handout 5 (HO5): Commercial Thermal Generation Results

## 🎯 Executive Summary
* **Optimization Status**: `OPTIMAL`
* **Objective Function (NPV Total Cost)**: **$19,335.91** (**-99.96% reduction** vs. HO4)
* **Active Technologies Added**: `PWRDSL`, `PWRNGS`, `PWRTRN`, `PWRDIST`
* **LP Dimensions**: 1,502 Rows × 960 Columns × 8,640 Non-Zeros
* **Solve Time**: < 0.08 seconds

---

## 📊 Visual Results & Analytics

### 1. Power Plant Additions (Gas Turbines & Diesel Generators)
![Plant Additions](graphs/ho5_plant_additions.png)

### 2. Dramatic 99.96% Cost Reduction
![Cost Drop](graphs/ho5_cost_drop.png)

---

## 📋 Numerical Output Data Table

| Metric | 2021 | 2023 | 2025 | 2027 | 2029 | 2031 | 2033 | 2035 |
|---|---|---|---|---|---|---|---|---|
| **Gas Mining Supply (PJ/yr)** | 53.72 | 6.94 | 7.58 | 15.16 | 7.58 | 7.58 | 7.58 | 7.58 |
| **Diesel Imports (PJ/yr)** | 10.60 | 11.57 | 21.38 | 10.69 | 10.69 | 10.69 | 10.69 | 10.69 |
| **New Gas Turbine (GW)** | 0.000 | 0.000 | 0.136 | 0.136 | 0.136 | 0.136 | 0.136 | 0.136 |
| **New Diesel Gen (GW)** | 0.000 | 0.000 | 0.109 | 0.148 | 0.148 | 0.148 | 0.148 | 0.148 |

---

## 💡 Economic Takeaway
Introducing realistic power plants eliminates the need for the $99,999/kW virtual backstop generator, collapsing system expenditure to **$19,335.91**. Gas turbines serve baseload, while diesel generators provide flexible peaking support.
