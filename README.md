# EN401 — Energy Systems Modelling (OSeMOSYS)

This repository contains the complete coursework, models, data files, analysis reports, and result workbooks for **EN401: Energy Systems Modelling**, implemented using **OSeMOSYS** (Open Source Energy Modelling System) and solved with **GLPK** (`glpsol`).

---

## 📚 Overview

The coursework systematically develops a national / regional energy system planning model over a 15-year planning horizon (**2021–2035**). Starting from a simple single-technology power balance (Handouts 1–3), the model expands into a multi-technology, multi-fuel energy network incorporating renewables, sub-annual timeslices, capacity limits, and carbon emissions caps (Handouts 4–6).

```
   [Primary Fuels]               [Power Generation]              [Final Demand]
   Coal Mining (MINCOA)   ───►  Coal Power Plant (COA001)  ──┐
   Diesel Import (IMPDSL) ───►  Diesel Generator (DSL001)  ──┼──► Transmission ──► Electricity
   Gas Import (MINNGS)    ───►  Gas Turbine (NGS001)       ──┤     Grid (TRN)      Demand (ED)
   Solar Resource (SOL)   ───►  Solar PV (SOL001)          ──┤
   Hydro Inflow (HYD)     ───►  Hydro Power (HYD001)       ──┘
```

---

## 🗂 Repository Structure

```text
EN401/
├── README.md                          # Comprehensive documentation (this file)
├── .gitignore                         # Git exclusion rules
│
├── 📊 Executive Reports, Visual Atlases & Guides
│   ├── RESULTS_AND_INPUTS_GRAPHS.md           # Visual atlas with embedded charts of all inputs & results
│   ├── OSeMOSYS_Results_and_Inputs_Visualized.pdf # Full standalone PDF visual atlas
│   ├── EXPLANATORY_GUIDE.md                   # In-depth repository architecture & methodology guide
│   ├── EN401_Architecture_and_Methodology_Guide.pdf # Publication-quality methodology PDF
│   ├── OSeMOSYS_Handouts_Report.pdf           # Complete analysis of Handouts 1 to 6 & results
│   ├── OSeMOSYS_Code_Explained.pdf            # In-depth GNU MathProg code & parameter guide
│   └── OSeMOSYS_Results.xlsx                  # 10-sheet master Excel workbook with cross-scenario data
│
├── 📁 Model Scenarios & Solution Files
│   ├── HO3/                           # Handout 3: Base Power System
│   │   ├── Hands_on_3.pdf             # Handout specification
│   │   ├── OSeHO3.dat                 # GLPK MathProg input data
│   │   └── OSeHO3_solution.txt        # Full solver primal & dual solution
│   ├── HO4/                           # Handout 4: Multi-Fuel Supply Chain
│   │   ├── Hands_on_4.pdf
│   │   ├── OSeHO4.dat
│   │   └── OSeHO4_solution.txt
│   ├── HO5/                           # Handout 5: Timeslices & Renewable Integration
│   │   ├── Hands_on_5.pdf
│   │   ├── OSeHO5.dat
│   │   └── OSeHO5_solution.txt
│   └── HO6/                           # Handout 6: Emissions Accounting & Policy Limits
│       ├── Hands_on_6.pdf
│       ├── OSeHO6.dat
│       └── OSeHO6_solution.txt
│
├── 📄 Original Course Handout Documents
│   ├── Hands_on_1_UI_61d5976063.docx  # HO1: Energy modelling concepts & RES
│   ├── Hands_on_2_UI_6b94c1a9ed.docx  # HO2: Energy chain data structure
│   ├── Hands_on_3_UI_0cf2b81000.docx  # HO3: Base model implementation
│   ├── Hands_on_4_UI_b813fa3c10.docx  # HO4: Fuel supply options
│   ├── Hands_on_5_UI_784ea5778c.docx  # HO5: Renewables & timeslice variability
│   └── Hands_on_6_UI_e272a90daf.docx  # HO6: Environmental constraints
│
└── 🖼 extracted_images/               # Reference diagrams & energy chain schematics
```

---

## 🔬 Summary of Handouts (HO1 – HO6)

| Handout | Focus | Key Additions / Modifications | Objective Value (NPV) |
|---|---|---|---|
| **HO1 & HO2** | Conceptual Foundations | Reference Energy System (RES), sets, commodities, and units definition | *Formulation phase* |
| **HO3** | Base Electricity System | Demand growth (1.05 PJ → 2.07 PJ), Coal (`COA001`), Hydro (`HYD001`), and Grid (`TRN`) | **$25,321.4M** |
| **HO4** | Fuel Supply Network | Added Diesel import (`IMPDSL`) and Natural Gas mining (`MINNGS`); unconstrained supply | **$25,321.4M** (diesel/gas unchosen due to higher unit costs) |
| **HO5** | Renewables & Timeslices | 8 seasonal/day-night timeslices, Solar PV (`SOL001`), Wind (`WND001`), capacity factor profiles | **$18,450.2M** |
| **HO6** | Emissions Accounting | CO2 emission factors by fuel, carbon tax/penalty, emission caps | **$21,140.8M** |

---

## 📈 Key Deliverables

### 1. `OSeMOSYS_Handouts_Report.pdf`
Comprehensive report detailing:
- Mathematical formulation of linear programming in energy systems.
- Step-by-step problem statements for each handout.
- System cost evolution, technology selection, and carbon trajectory.
- Economic and engineering insights explaining why specific technologies were deployed.

### 2. `OSeMOSYS_Code_Explained.pdf`
Exhaustive code walkthrough:
- Parameter and variable taxonomy (sets, decision variables, parameters).
- Core equations: Demand balance, capacity adequacy, investment constraints, and salvage value calculation.
- GNU MathProg syntax patterns and GLPK execution flags.

### 3. `OSeMOSYS_Results.xlsx`
10-sheet structured workbook:
- **Overview**: High-level KPI summary, cost comparisons, total installed capacity by milestone year.
- **HO3 to HO6 Sheets**: Detailed scenario-specific capacity additions, generation profiles, and parameters.
- **Capacity by Year**: Full multi-year capacity addition matrix (2021–2035).
- **Cost Comparison**: Capital expenditure, fixed/variable O&M, fuel costs, and salvage breakdown.
- **DAT File Excerpts**: Commented code snippets of `.dat` files for easy reference.
- **Parameters Reference**: Comprehensive dictionary of all 17 OSeMOSYS parameters.

---

## ⚙️ How to Reproduce & Solve

### Prerequisites
- **GLPK (GNU Linear Programming Kit)**:
  ```bash
  # macOS (Homebrew)
  brew install glpk
  ```
- **OSeMOSYS MathProg model file** (`osemosys_fast.txt` or `osemosys.txt`).

### Running a Scenario
To solve any handout directly with GLPK:

```bash
# Handout 3
glpsol -m path/to/osemosys_fast.txt -d HO3/OSeHO3.dat -o HO3/OSeHO3_solution.txt

# Handout 4
glpsol -m path/to/osemosys_fast.txt -d HO4/OSeHO4.dat -o HO4/OSeHO4_solution.txt

# Handout 5
glpsol -m path/to/osemosys_fast.txt -d HO5/OSeHO5.dat -o HO5/OSeHO5_solution.txt

# Handout 6
glpsol -m path/to/osemosys_fast.txt -d HO6/OSeHO6.dat -o HO6/OSeHO6_solution.txt
```

---

## 👤 Author
Coursework completed by **Kaustubh Ashok**  
Semester 5 — Energy Systems Modelling (`EN401`)
