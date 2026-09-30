# Handout 4 (HO4): Upstream Fuel Supply Options

This directory contains the MathProg input dataset, problem specification, and solver output log for **Handout 4**.

---

## 🎯 Objective & Problem Scope
Handout 4 expands the upstream energy supply chain by introducing primary fuel mining and international import commodities:
* **Natural Gas Extraction (`MINNGS`)**: Produces raw natural gas (`NGS`).
* **Diesel Imports (`IMPDSL`)**: Imports refined diesel (`DSL`).
* **Active Technologies**: `MINBACK`, `BACKSTOP`, `MINNGS`, `IMPDSL`.

---

## 📊 Numerical Optimization Results

* **Optimization Status**: `OPTIMAL`
* **Objective Value (NPV)**: **$51,009,521.16** (identical to HO3)
* **LP Problem Rows (Constraints)**: 872 (+406 rows vs. HO3)
* **LP Problem Columns (Variables)**: 480 (+240 cols vs. HO3)
* **Non-Zero Matrix Elements**: 4,320
* **Solver Runtime**: < 0.05 seconds

### Key Takeaway — Economic Merit Order:
Although `MINNGS` and `IMPDSL` were available at low extraction costs, the model **did not construct or utilize any gas or diesel supply**. Because power conversion plants (gas turbines or diesel generators) were not yet introduced into the Reference Energy System, there was no physical mechanism to turn fuel into electricity. Consequently, the virtual backstop generator continued to supply 100% of demand, resulting in an unchanged NPV cost.

---

## 📁 Files in this Directory
* **[OSeHO4.dat](file:///Users/kaustubhashok/SEM5%20pros/EN401/HO4/OSeHO4.dat)**: MathProg data file.
* **[OSeHO4_solution.txt](file:///Users/kaustubhashok/SEM5%20pros/EN401/HO4/OSeHO4_solution.txt)**: Full GLPK primal and dual solution output.
* **[Hands_on_4.pdf](file:///Users/kaustubhashok/SEM5%20pros/EN401/HO4/Hands_on_4.pdf)**: Assignment problem brief.

---

## 🚀 How to Run

```bash
# From workspace root:
glpsol -m osemosys_project/models/osemosys_fast.txt -d EN401/HO4/OSeHO4.dat -o EN401/HO4/OSeHO4_solution.txt
```
