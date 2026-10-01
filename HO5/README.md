# Handout 5 (HO5): Commercial Thermal Generation Era

This directory contains the MathProg input dataset, problem specification, and solver output log for **Handout 5**.

---

## 🎯 Objective & Problem Scope
Handout 5 completes the fossil power generation chain by introducing realistic commercial power plants, grid transmission lines, and distribution networks:
* **Diesel Power Plant (`PWRDSL`)**: Capital cost $1,200/kW, Fixed O&M $30/kW/yr, Life 20 yrs
* **Gas Turbine (`PWRNGS`)**: Capital cost $1,200/kW, Fixed O&M $25/kW/yr, Life 25 yrs
* **Transmission Lines (`PWRTRN`)**: Capital cost $700/kW, Life 40 yrs
* **Distribution Network (`PWRDIST`)**: Capital cost $1,500/kW, Life 30 yrs

---

## 📊 Numerical Optimization Results

* **Optimization Status**: `OPTIMAL`
* **Objective Value (NPV)**: **$19,335.91** (**-99.96% cost drop** vs. HO4)
* **Backstop Generation**: **0.00 GW** (phased out completely)
* **LP Problem Rows (Constraints)**: 1,502
* **LP Problem Columns (Variables)**: 960
* **Non-Zero Matrix Elements**: 8,640
* **Solver Runtime**: < 0.08 seconds

### Optimal Generation Mix & Additions:
* **Gas Mining (`MINNGS`)**: Extracted at 53.7 PJ/yr in 2021, providing the bulk baseload energy carrier.
* **Diesel Imports (`IMPDSL`)**: Imports 10.6 to 21.4 PJ/yr to satisfy peaking requirements.
* **Power Capacity Additions**:
  * Gas Turbines (`PWRNGS`): Added from 2025 onwards (~0.136 GW/yr).
  * Diesel Generators (`PWRDSL`): Added from 2025 onwards (~0.148 GW/yr).

---

## 📁 Files in this Directory
* 📊 **[RESULTS.md](RESULTS.md)**: Interactive Markdown report with embedded result charts and full data tables.
* 📄 **[RESULTS.pdf](RESULTS.pdf)**: Publication-grade standalone PDF results report for Handout 5.
* 🖼 **[graphs/](graphs/)**: Dedicated scenario charts (gas/diesel plant additions, 99.96% cost drop).
* **[OSeHO5.dat](OSeHO5.dat)**: MathProg data file.
* **[OSeHO5_solution.txt](OSeHO5_solution.txt)**: Full GLPK primal and dual solution output.
* **[Hands_on_5.pdf](Hands_on_5.pdf)**: Assignment problem brief.

---

## 🚀 How to Run

```bash
# From workspace root:
glpsol -m osemosys_project/models/osemosys_fast.txt -d EN401/HO5/OSeHO5.dat -o EN401/HO5/OSeHO5_solution.txt
```
