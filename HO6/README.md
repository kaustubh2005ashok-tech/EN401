# Handout 6 (HO6): Clean Energy Decarbonization & Seasonality

This directory contains the MathProg input dataset, problem specification, and solver output log for **Handout 6**.

---

## 🎯 Objective & Problem Scope
Handout 6 simulates the clean energy transition by introducing renewable resources (Hydro and Biomass) and modeling sub-annual seasonal variability across 4 timeslices (`RD`, `RN`, `DD`, `DN`):
* **Hydro Power Plant (`PWRHYD`)**: Capital cost $2,500/kW, Operating life 50 yrs, Variable O&M $1.0/PJ
* **Biomass Power Plant (`PWRBIO`)**: Capital cost $1,800/kW, Operating life 25 yrs
* **Seasonal Hydrology**: Capacity factor drops from **65% in Rainy Season (`RD`, `RN`)** to **40% in Dry Season (`DD`, `DN`)** (-38.5% capacity reduction)

---

## 📊 Numerical Optimization Results

* **Optimization Status**: `OPTIMAL`
* **Objective Value (NPV)**: **$8,664.56** (**-55.2% cost reduction** vs. HO5)
* **LP Problem Rows (Constraints)**: 2,162
* **LP Problem Columns (Variables)**: 1,440
* **Non-Zero Matrix Elements**: 12,960
* **Solver Runtime**: < 0.12 seconds

### Generation & Transition Dynamics:
* **88% Clean Hydro Power**: Hydro dominates the electricity mix due to zero fuel costs and 50-year asset life.
* **Hydro Additions**: Built aggressively from 0.185 GW in 2021 to 0.500 GW/yr in later years, reaching **4.85 GW cumulative capacity by 2035**.
* **Fossil Fuel Phase-Out**: Natural gas mining drops to **0 PJ after 2026**.
* **Seasonal Peaking**: Gas turbines (`PWRNGS`) operate during the Dry Day timeslice (`DD`) to bridge the water deficit when hydro capacity factor drops to 40%.

---

## 📁 Files in this Directory
* 📊 **[RESULTS.md](RESULTS.md)**: Unified interactive report containing executive summary, visualized result charts, complete multi-year data tables, and the full verbatim GLPK solver solution output.
* 📄 **[RESULTS.pdf](RESULTS.pdf)**: Publication-grade standalone PDF results report for Handout 6.
* 🖼 **[graphs/](graphs/)**: Dedicated scenario charts (hydro additions scaling, -55.2% cost savings).
* **[OSeHO6.dat](OSeHO6.dat)**: MathProg data file.
* **[Hands_on_6.pdf](Hands_on_6.pdf)**: Assignment problem brief.

---

## 🚀 How to Run

```bash
# From workspace root:
glpsol -m osemosys_project/models/osemosys_fast.txt -d EN401/HO6/OSeHO6.dat -o EN401/HO6/OSeHO6_solution.txt
```
