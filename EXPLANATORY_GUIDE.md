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

$$
\min \text{TotalDiscountedCost} = \sum_{y \in \text{YEAR}} \frac{\text{TotalAnnualCost}_{y}}{(1 + d)^{y - y_0}}
$$

Where:
* $d = 0.10$ (social discount rate)
* $y_0 = 2021$ (base year)
* $\text{TotalAnnualCost}_y$ aggregates principal expenditure streams:
  1. **Discounted Capital Investment Cost**: Capital expenditure for installing new capacity (`CapitalCost × NewCapacity`).
  2. **Discounted Fixed O&M Cost**: Recurring operations & maintenance per unit of installed capacity (`FixedCost × TotalCapacityAnnual`).
  3. **Discounted Variable O&M Cost**: Operational expenses scaling with generation activity (`VariableCost × AnnualActivity`).
  4. **Discounted Fuel / Mining / Import Costs**: Upstream fuel procurement costs.
  5. *Minus* **Discounted Salvage Value**: Economic credit for assets retaining operational life beyond 2035.

### 3.2 Reference Energy System (RES)
The Reference Energy System defines the topology through which primary energy commodities are extracted or imported, transformed into secondary carriers, transmitted via distribution grids, and consumed as useful energy demands:

```
[Primary Resources]            [Power Generation]               [Network & Demand]
MINBACK (Virtual Mining)  ───► BACKSTOP (Penalty Generator) ──┐
MINNGS  (Gas Extraction)  ───► PWRNGS   (Gas Turbine)       ──┼──► PWRTRN ──► PWRDIST ──► ELC003
IMPDSL  (Diesel Imports)  ───► PWRDSL   (Diesel Generator)  ──┤    (Grid)     (Dist)     (Demand)
MINHYD  (Hydro Inflow)    ───► PWRHYD   (Hydro Plant)       ──┤
MINBIO  (Biomass Source)  ───► PWRBIO   (Biomass Plant)     ──┘
```

### 3.3 Core Mathematical Balances

#### 1. Demand Balance Constraint
Energy delivered to the grid must meet or exceed final consumer demand in every timeslice ($l$) and year ($y$):

$$
\sum_{m} \text{RateOfActivity}_{y, l, \text{PWRDIST}, m} \cdot \text{OutputActivityRatio} \ge \text{RateOfDemand}_{y, l, \text{ELC003}}
$$

#### 2. Capacity Adequacy Constraint
The generation from any technology ($t$) in any timeslice ($l$) cannot exceed its operational installed capacity adjusted for resource availability:

$$
\sum_{m} \text{RateOfActivity}_{y, l, t, m} \le \text{TotalCapacityAnnual}_{y, t} \cdot \text{CapacityFactor}_{y, t, l} \cdot \text{CapacityToActivityUnit}_{t}
$$

#### 3. Cumulative Capacity & Retirement Accounting
Total available capacity in year $y$ equals unretired historical residual capacity plus capacity additions built up to year $y$:

$$
\text{TotalCapacityAnnual}_{y, t} = \text{ResidualCapacity}_{y, t} + \sum_{\substack{y' \le y \\ y - y' < \text{OperationalLife}_t}} \text{NewCapacity}_{y', t}
$$

#### 4. Salvage Value Formulation
Capital assets whose operational lifespan ($L_t$) extends beyond the model horizon ($Y_{\text{end}} = 2035$) receive an economic salvage credit:

$$
\text{SalvageValue}_{y, t} = \text{CapitalCost}_{y, t} \cdot \text{NewCapacity}_{y, t} \cdot \frac{y + L_t - Y_{\text{end}}}{L_t}
$$

---

## 4. Scenario Progression & Handout Analysis (HO1 – HO6)

### 4.1 Handout 1 & 2: Structural & Relational Setup
* **HO1 (Foundations)**: Established the spatial and temporal scope. Introduced key concepts of energy carriers, primary energy vs. final demand, and technology efficiency factors.
* **HO2 (Data Structures)**: Defined quantitative activity ratios. Established standard units:
  * Energy: **Petajoules (PJ)**
  * Power / Capacity: **Gigawatts (GW)**
  * Capital Cost: **Million USD per Gigawatt (M$/GW)**
  * Conversion Factor: $1 \text{ GW} \cdot \text{year} = 31.536 \text{ PJ}$.

### 4.2 Handout 3: The Base Electricity System
* **Focus**: Initial operational model establishing demand growth (20.0 PJ $\rightarrow$ 90.0 PJ). Because no commercial generation is defined yet, virtual `BACKSTOP` ($99,999/kW) satisfies all demand.
* **Demand Profile**: Electricity demand escalates monotonically from **20 PJ (2021)** to **90 PJ (2035)** (+5 PJ/year).
* **System Results**:
  * Total Discounted Cost (NPV): **$51,009,521.16**.
  * Backstop capacity built every year to meet demand, totaling 3.43 GW by 2035.

### 4.3 Handout 4: Upstream Fuel Supply Options & Economic Merit Order
* **Focus**: Adding primary fuel extraction and import chains:
  * Diesel imports (`IMPDSL`) supplying diesel commodities (`DSL`).
  * Natural gas mining (`MINNGS`) supplying raw gas (`NGS`).
* **Key Insight**:
  * Total System Cost remained exactly **$51,009,521.16** (0.0% change).
  * Neither diesel nor gas was deployed because power conversion plants (gas turbines, diesel generators) were not yet available in the Reference Energy System to turn fuel into electricity.

### 4.4 Handout 5: Commercial Thermal Power Generation
* **Focus**: Introducing commercial power conversion and network infrastructure:
  * Diesel Power Plants (`PWRDSL`): $1,200/kW capital cost, $30/kW/yr fixed O&M.
  * Gas Turbines (`PWRNGS`): $1,200/kW capital cost, $25/kW/yr fixed O&M.
  * Transmission Lines (`PWRTRN`): $700/kW capital cost.
  * Distribution Network (`PWRDIST`): $1,500/kW capital cost.
* **System Results**:
  * Total System Cost dropped to **$19,335.91** — a **99.96% cost reduction** as realistic thermal units completely displaced the penalty backstop generator.
  * Natural gas mining scaled up to 53.7 PJ/yr in 2021 to supply baseload electricity.

### 4.5 Handout 6: Clean Energy Decarbonization & Seasonality
* **Focus**: Clean transition incorporating renewables and sub-annual seasonal variability across 4 timeslices (`RD`, `RN`, `DD`, `DN`):
  * Hydro Power Plants (`PWRHYD`): $2,500/kW capital cost, 50-year operating life, zero fuel cost.
  * Biomass Power Plants (`PWRBIO`): $1,800/kW capital cost, 25-year operating life.
* **Policy & Seasonal Dynamics**:
  * Hydro capacity factor drops from **65% in Rainy Season (`RD`, `RN`)** to **40% in Dry Season (`DD`, `DN`)** (-38.5% capacity reduction).
* **System Results**:
  * Total System Cost dropped to **$8,664.56** (**-55.2% reduction** vs. HO5).
  * Clean hydro expands to 4.85 GW by 2035, delivering **88% of total generation**, with gas turbines operating as flexible peaking backup during the dry season.

### Cross-Scenario Quantitative Summary

| Metric | HO3 (Base System) | HO4 (Fuel Chains) | HO5 (Thermal Era) | HO6 (Renewables) |
|---|---|---|---|---|
| **Objective Value (NPV)** | $51,009,521.16 | $51,009,521.16 | **$19,335.91** | **$8,664.56** |
| **Cost Delta vs. Previous** | Baseline | 0.0% | **-99.96%** | **-55.2%** |
| **Timeslices** | 4 (RD, RN, DD, DN) | 4 (RD, RN, DD, DN) | 4 (RD, RN, DD, DN) | 4 (RD, RN, DD, DN) |
| **Leading Generation** | Virtual `BACKSTOP` | Virtual `BACKSTOP` | Gas (`PWRNGS`) + Diesel (`PWRDSL`) | **Clean Hydro (`PWRHYD`, 88%)** |
| **Peak Installed Capacity** | 3.43 GW | 3.43 GW | 2.80 GW | **4.85 GW** |
| **Primary Fuel Source** | Virtual Penalty | Virtual Penalty | Gas Mining + Diesel Imports | Water Inflow (`MINHYD`) |

---

## 5. Overview of Generated Deliverables

### 5.1 Master Spreadsheets & Interactive Markdown Atlases
1. **[RESULTS_AND_INPUTS_GRAPHS.md](RESULTS_AND_INPUTS_GRAPHS.md)**: Interactive visual atlas with 13 embedded high-resolution charts.
2. **[HANDOUTS_REPORT.md](HANDOUTS_REPORT.md)**: Full Markdown conversion of the Handouts 1 to 6 analytical report.
3. **[CODE_EXPLAINED.md](CODE_EXPLAINED.md)**: GNU MathProg code guide with a 17-parameter dictionary.
4. **[OSeMOSYS_Results.xlsx](OSeMOSYS_Results.xlsx)**: 10-sheet structured results workbook.


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
