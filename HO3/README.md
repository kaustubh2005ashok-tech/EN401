# Handout 3 (HO3): Base Power System Model

This directory contains the MathProg input dataset, problem specification, and solver output log for **Handout 3**.

---

## 🎯 Objective & Problem Scope
Handout 3 establishes the base electricity system linking final electricity demand (`ELC003`) to the grid. In this initial setup, commercial generation technologies are not yet defined, forcing the model to rely on a virtual backstop technology.

* **Planning Horizon**: 2021 – 2035 (15 years)
* **Electricity Demand (`ELC003`)**: 20.0 PJ (2021) expanding linearly to 90.0 PJ (2035) at +5.0 PJ/year
* **Active Technologies**:
  * `MINBACK`: Virtual fuel mining resource
  * `BACKSTOP`: Virtual penalty generator ($99,999/kW capital cost)

---

## 📊 Numerical Optimization Results

* **Optimization Status**: `OPTIMAL`
* **Objective Value (NPV)**: **$51,009,521.16**
* **LP Problem Rows (Constraints)**: 466
* **LP Problem Columns (Variables)**: 240
* **Non-Zero Matrix Elements**: 2,160
* **Solver Runtime**: < 0.05 seconds

### Capacity Additions (GW):
Because no alternative power plants exist, `BACKSTOP` is built every year to meet demand:
* **2021**: 0.7623 GW
* **2022–2035**: 0.1906 GW annually
* **Total Installed Capacity in 2035**: 3.4307 GW

---

## 📁 Files in this Directory
* **[OSeHO3.dat](file:///Users/kaustubhashok/SEM5%20pros/EN401/HO3/OSeHO3.dat)**: MathProg data file.
* **[OSeHO3_solution.txt](file:///Users/kaustubhashok/SEM5%20pros/EN401/HO3/OSeHO3_solution.txt)**: Full GLPK primal and dual solution output.
* **[Hands_on_3.pdf](file:///Users/kaustubhashok/SEM5%20pros/EN401/HO3/Hands_on_3.pdf)**: Assignment problem brief.

---

## 🚀 How to Run

```bash
# From workspace root:
glpsol -m osemosys_project/models/osemosys_fast.txt -d EN401/HO3/OSeHO3.dat -o EN401/HO3/OSeHO3_solution.txt
```
