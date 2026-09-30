# EN401: Energy Systems Modelling
## Repository Architecture, System Design & Modelling Methodology Guide

---

### Author & Course Information
- **Course**: EN401 — Energy Systems Modelling
- **Author**: Kaustubh Ashok
- **Academic Term**: Semester 5
- **Modelling Framework**: Open Source Energy Modelling System (OSeMOSYS)
- **Mathematical Solver**: GNU Linear Programming Kit (GLPK / `glpsol`)
- **Repository URL**: [https://github.com/kaustubh2005ashok-tech/EN401](https://github.com/kaustubh2005ashok-tech/EN401)

---

## 1. Executive Summary & Purpose

This repository houses the complete academic research, linear programming models, solver output records, executive reports, and cross-scenario analytical workbooks developed for **EN401: Energy Systems Modelling**.

The core objective of the coursework is to model the long-term optimal expansion and operational dispatch of a national energy system over a 15-year planning horizon (**2021–2035**). Using **OSeMOSYS** (an open-source bottom-up linear programming optimization framework) and the **GLPK** MathProg engine, the system evaluates the economic trade-offs between capital investments, variable fuel and operating costs, renewable resource intermittency, and carbon mitigation policies under growing electricity demand.

This guide provides an exhaustive architectural walkthrough of the repository, the mathematical formulation of the underlying models, the progressive evolution across Handouts 1 to 6, and the structure of the data artifacts.

---

## 2. Repository Architecture & Version Control

### 2.1 Directory Layout
The repository is structured to maintain strict separation between problem definitions, computational model inputs, solver raw outputs, and synthesized analytical deliverables:

```text
EN401/
├── README.md                                    # GitHub repository landing page & quickstart
├── EXPLANATORY_GUIDE.md                         # Comprehensive architecture & methodology guide (this file)
├── .gitignore                                   # Version control exclusion rules
│
├── 📊 Synthesized Analysis & Deliverables
│   ├── OSeMOSYS_Handouts_Report.pdf             # Comprehensive report covering Handouts 1 to 6
│   ├── OSeMOSYS_Code_Explained.pdf              # GNU MathProg code & parameter dictionary
│   └── OSeMOSYS_Results.xlsx                    # 10-sheet master Excel results workbook
│
├── 📁 Scenario Data & Solver Solution Logs
│   ├── HO3/                                     # Handout 3: Base Power System Model
│   │   ├── Hands_on_3.pdf                       # Problem statement & technical assumptions
│   │   ├── OSeHO3.dat                           # GLPK MathProg input dataset
│   │   └── OSeHO3_solution.txt                  # Full solver output (primal/dual solution & sensitivity)
│   ├── HO4/                                     # Handout 4: Multi-Fuel Supply Chain
│   │   ├── Hands_on_4.pdf
│   │   ├── OSeHO4.dat
│   │   └── OSeHO4_solution.txt
│   ├── HO5/                                     # Handout 5: Timeslices & Renewable Integration
│   │   ├── Hands_on_5.pdf
│   │   ├── OSeHO5.dat
│   │   └── OSeHO5_solution.txt
│   └── HO6/                                     # Handout 6: Emissions Accounting & Policy Limits
│       ├── Hands_on_6.pdf
│       ├── OSeHO6.dat
│       └── OSeHO6_solution.txt
│
├── 📄 Source Assignment Handouts
│   ├── Hands_on_1_UI_61d5976063.docx            # HO1: Energy modelling concepts & RES
│   ├── Hands_on_2_UI_6b94c1a9ed.docx            # HO2: Energy chain data structure & activity ratios
│   ├── Hands_on_3_UI_0cf2b81000.docx            # HO3: Base model implementation
│   ├── Hands_on_4_UI_b813fa3c10.docx            # HO4: Upstream fuel supply options
│   ├── Hands_on_5_UI_784ea5778c.docx            # HO5: Renewables & timeslice variability
│   └── Hands_on_6_UI_e272a90daf.docx            # HO6: Environmental constraints & carbon caps
│
└── 🖼 extracted_images/                         # Reference diagrams & energy chain schematics (68 items)
```

### 2.2 Version Control & Exclusion Policy (`.gitignore`)
The repository contains rigorous `.gitignore` rules to prevent repository bloat and credential leakage:
- **Operating System Artifacts**: Ignores `.DS_Store`, `._*`, `ehthumbs.db`, and `Thumbs.db` to prevent cross-platform filesystem noise.
- **Office Lock & Temporary Files**: Excludes `~$*.docx` and `~$*.xlsx` generated when viewing or editing documents in Microsoft Word or Excel.
- **Solver & Interpreter Artifacts**: Excludes `*.tmp`, `*.log`, `__pycache__/`, and `*.pyc` generated during model compilation and execution.

---

## 3. The OSeMOSYS Modelling Framework

### 3.1 Linear Programming Paradigm
OSeMOSYS models the energy system as a cost-minimization Linear Program (LP). The objective is to identify the capacity expansion schedule and hourly/seasonal dispatch that satisfies exogenous energy demands at the lowest **Net Present Value (NPV)** of total system costs:

$$\min \text{TotalDiscountedCost} = \sum_{y \in \text{YEAR}} \frac{\text{TotalAnnualCost}_{y}}{(1 + d)^{y - y_0}}$$

Where:
- $d$ is the social discount rate (typically 5% or 10%).
- $y_0$ is the base year (2021).
- $\text{TotalAnnualCost}_y$ aggregates four principal cost streams:
  1. **Discounted Capital Investment Cost** ($\text{CapitalCost} \times \text{NewCapacity}$)
  2. **Discounted Fixed Operating & Maintenance Cost** ($\text{FixedCost} \times \text{TotalCapacityAnnual}$)
  3. **Discounted Variable Operating & Maintenance Cost** ($\text{VariableCost} \times \text{AnnualActivity}$)
  4. **Discounted Fuel / Mining / Import Costs**
  5. **Discounted Carbon Penalty / Emissions Taxes**
  6. *Minus* **Discounted Salvage Value** for assets retaining economic life beyond the 2035 horizon.

### 3.2 Reference Energy System (RES)
The Reference Energy System defines the topology through which primary energy commodities are extracted or imported, transformed into secondary carriers, transmitted via distribution grids, and consumed as useful energy demands:

```
[Primary Resources]          [Energy Conversion]            [Secondary/Final]
MINCOA (Coal Mining)  ───►  COA001 (Coal Power Plant)  ──┐
IMPDSL (Diesel Import) ───►  DSL001 (Diesel Generator)  ──┼──► TRN (Grid) ──► ED (Electricity Demand)
MINNGS (Gas Import)   ───►  NGS001 (Gas Turbine)       ──┤
HYD (Hydro Inflow)    ───►  HYD001 (Hydro Power Plant) ──┤
SOL (Solar Radiation) ───►  SOL001 (Solar PV Array)    ──┘
```

### 3.3 Core Mathematical Balances

#### 1. Demand Balance Constraint
Energy delivered to the grid must meet or exceed final consumer demand in every timeslice ($l$) and year ($y$):
$$\sum_{m} \text{RateOfActivity}_{y, l, \text{TRN}, m} \times \text{OutputActivityRatio}_{\text{TRN}, \text{ELC001}, m} \ge \text{RateOfDemand}_{y, l, \text{ELC001}}$$

#### 2. Capacity Adequacy Constraint
The generation from any technology ($t$) in any timeslice ($l$) cannot exceed its operational installed capacity adjusted for resource availability and scheduled maintenance:
$$\sum_{m} \text{RateOfActivity}_{y, l, t, m} \le \text{TotalCapacityAnnual}_{y, t} \times \text{CapacityFactor}_{y, t, l} \times \text{CapacityToActivityUnit}_{t}$$

#### 3. Cumulative Capacity & Retirement Accounting
Total available capacity in year $y$ equals unretired historical residual capacity plus capacity additions built up to year $y$:
$$\text{TotalCapacityAnnual}_{y, t} = \text{ResidualCapacity}_{y, t} + \sum_{y' \le y, \, y - y' < \text{OperationalLife}_t} \text{NewCapacity}_{y', t}$$

#### 4. Salvage Value Formulation
Capital assets whose operational lifespan ($L_t$) extends beyond the model horizon ($Y_{\text{end}} = 2035$) receive an economic salvage credit:
$$\text{SalvageValue}_{y, t} = \text{CapitalCost}_{y, t} \times \text{NewCapacity}_{y, t} \times \frac{y + L_t - Y_{\text{end}}}{L_t}$$

---

## 4. Scenario Progression & Handout Analysis (HO1 – HO6)

### 4.1 Handout 1 & 2: Structural & Relational Setup
- **HO1 (Foundations)**: Established the spatial and temporal scope. Introduced key concepts of energy carriers, primary energy vs. final demand, and technology efficiency factors.
- **HO2 (Data Structures)**: Defined quantitative activity ratios. Established standard units:
  - Energy: **Petajoules (PJ)**
  - Power / Capacity: **Gigawatts (GW)**
  - Capital Cost: **Million USD per Gigawatt (M$/GW)**
  - Conversion Factor: $1 \text{ GW} \cdot \text{year} = 31.536 \text{ PJ}$.

### 4.2 Handout 3: The Base Electricity System
- **Focus**: Initial operational model linking coal generation (`COA001`), run-of-river hydro (`HYD001`), and grid transmission (`TRN`).
- **Demand Profile**: Electricity demand escalates monotonically from **1.05 PJ (2021)** to **2.07 PJ (2035)**.
- **System Results**:
  - Total Discounted Cost (NPV): **$25,321.4 Million USD**.
  - Coal is prioritized over expensive hydro capital additions, but hydro operates at baseload capacity factors.

### 4.3 Handout 4: Fuel Supply Options & Economic Merit Order
- **Focus**: Adding primary fuel extraction and import chains:
  - Diesel imports (`IMPDSL`) supplying diesel generation (`DSL001`).
  - Natural gas mining (`MINNGS`) supplying open-cycle gas turbines (`NGS001`).
- **Key Insight**:
  - Total System Cost remained exactly **$25,321.4 Million USD**.
  - Neither diesel nor gas was deployed. The solver determined their combined levelized cost of electricity (LCOE) exceeded coal's amortized capital and fuel costs, demonstrating pure least-cost merit order behavior.

### 4.4 Handout 5: Renewable Integration & Sub-Annual Timeslices
- **Focus**: Incorporating diurnal and seasonal variations. The year is segmented into **8 timeslices**:
  - 4 Seasons: Autumn (FA), Winter (WI), Spring (SP), Summer (SU).
  - 2 Diurnal Slices: Day (D) and Night (N).
- **Technologies Added**: Utility-scale Solar PV (`SOL001`) and Wind generation (`WND001`), along with seasonal hydro inflows.
- **System Results**:
  - Total System Cost dropped to **$18,450.2 Million USD** — a massive **27.1% reduction ($6,871.2M saved)**.
  - Solar PV generates heavily during daytime timeslices at zero variable fuel cost, displacing fossil fuel consumption during peak sun hours.

### 4.5 Handout 6: Emissions Accounting & Carbon Constraints
- **Focus**: Environmental policy simulation. Explicit emission coefficients ($\text{kt } \text{CO}_2 / \text{PJ}$) assigned to fossil combustion.
- **Policy Mechanisms**: Implementation of carbon emission taxes and hard annual emission ceilings.
- **System Results**:
  - Total System Cost rose to **$21,140.8 Million USD** (+14.6% vs. HO5).
  - Coal generation is constrained by the carbon cap, forcing the model to invest earlier and more aggressively in Solar PV, Wind, and Hydro to satisfy demand while adhering to legal environmental limits.

### Cross-Scenario Quantitative Summary

| Metric | HO3 (Base System) | HO4 (Fuel Chains) | HO5 (Renewables) | HO6 (CO2 Policy) |
|---|---|---|---|---|
| **Objective Value (NPV)** | $25,321.4M | $25,321.4M | $18,450.2M | $21,140.8M |
| **Cost Delta vs. Base** | 0.0% | 0.0% | **-27.1%** | **-16.5%** |
| **Timeslices** | 1 (Annual) | 1 (Annual) | 8 (Seasonal/Diurnal) | 8 (Seasonal/Diurnal) |
| **Leading Generation** | Coal (COA001) | Coal (COA001) | Solar PV + Coal | Solar PV + Hydro + Wind |
| **Peak Installed Capacity** | 0.098 GW | 0.098 GW | 0.165 GW | 0.192 GW |
| **Carbon Intensity** | Unconstrained | Unconstrained | Moderate | Heavily Constrained |

---

## 5. Overview of Generated Deliverables

### 5.1 `OSeMOSYS_Results.xlsx` (Master Spreadsheet)
A 10-worksheet workbook formatted with professional palettes and automated cross-referencing:
1. **Overview**: Executive dashboard, key metrics, demand growth, and capacity additions.
2. **HO3 Results**: Detailed capacity additions, generation profile, and activity parameters for Handout 3.
3. **HO4 Results**: Parameter inputs for diesel/gas, showing economic non-deployment.
4. **HO5 Results**: 8-timeslice capacity factor matrix, solar/hydro seasonal dispatch.
5. **HO6 Results**: Carbon emissions trajectory, penalty accounting, and clean transition mix.
6. **Capacity by Year**: Complete $15 \times N$ matrix of annual capacity additions (2021–2035).
7. **Cost Comparison**: Comparative cost breakdown, capital investment vs. fuel vs. salvage value.
8. **DAT File Excerpts**: Formatted MathProg code blocks with explanatory commentary.
9. **Parameters Reference**: Comprehensive technical dictionary defining all 17 OSeMOSYS parameters.

### 5.2 `OSeMOSYS_Handouts_Report.pdf`
A formal 10-page executive and technical report presenting:
- Theoretical LP foundations and objective formulation.
- Individual scenario narrative analyses with system diagrams.
- Detailed numerical tables of generation, installed capacity, and emissions.
- Comparative policy insights on carbon mitigation and renewable cost-competitiveness.

### 5.3 `OSeMOSYS_Code_Explained.pdf`
A technical software manual and developer reference:
- Step-by-step breakdown of GNU MathProg syntax in OSeMOSYS.
- Parameter indexing conventions (`Year`, `Technology`, `Commodity`, `Timeslice`).
- Mathematical equation definitions for activity, storage, capacity, and cost accounting.
- GLPK solver flags, execution switches, and troubleshooting guide.

---

## 6. Model Execution & Reproduction Guide

### 6.1 Prerequisites
Install GLPK (GNU Linear Programming Kit) on your system:
```bash
# macOS via Homebrew
brew install glpk

# Ubuntu / Debian Linux
sudo apt-get update && sudo apt-get install -y glpk-utils
```

### 6.2 Solving Individual Scenarios
To compile and execute any handout model using `glpsol`:

```bash
# Solve Handout 3
glpsol -m path/to/osemosys_fast.txt -d HO3/OSeHO3.dat -o HO3/OSeHO3_solution.txt

# Solve Handout 4
glpsol -m path/to/osemosys_fast.txt -d HO4/OSeHO4.dat -o HO4/OSeHO4_solution.txt

# Solve Handout 5
glpsol -m path/to/osemosys_fast.txt -d HO5/OSeHO5.dat -o HO5/OSeHO5_solution.txt

# Solve Handout 6
glpsol -m path/to/osemosys_fast.txt -d HO6/OSeHO6.dat -o HO6/OSeHO6_solution.txt
```

### 6.3 Analyzing the Solver Output
The resulting `_solution.txt` files contain:
- **Optimization Status**: Verifies if solver reached an `INTEGER OPTIMAL` or `OPTIMAL LP SOLUTION`.
- **Primal Variables**: Displays dispatch rates (`RateOfActivity`), annual capacity additions (`NewCapacity`), and cumulative capacities (`TotalCapacityAnnual`).
- **Marginal / Dual Variables (Shadow Prices)**: Displays the economic marginal value of constraints, indicating the shadow price of electricity demand satisfaction and emission caps.
