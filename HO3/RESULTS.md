# Handout 3 (HO3): Base Power System Results & Analysis

## 🎯 Executive Summary
* **Optimization Status**: `OPTIMAL`
* **Objective Function (NPV Total Cost)**: **$51,009,521.16**
* **Active Technologies**: `MINBACK` (mining), `BACKSTOP` (generator)
* **LP Dimensions**: 466 Rows × 240 Columns × 2,160 Non-Zero Elements
* **Solve Time**: < 0.05 seconds

---

## 📊 Visual Results & Analytics

### 1. Electricity Demand Met (2021–2035)
Demand escalates linearly from **20 PJ in 2021** to **90 PJ in 2035** (+5 PJ/yr):
![Demand Met](graphs/ho3_demand_met.png)

### 2. Virtual Backstop Capacity Accumulation
Because no commercial power plants were defined, the virtual backstop generator was built every year:
![Backstop Capacity](graphs/ho3_backstop_capacity.png)

---

## 📋 Numerical Output Data Table

| Metric | 2021 | 2023 | 2025 | 2027 | 2029 | 2031 | 2033 | 2035 |
|---|---|---|---|---|---|---|---|---|
| **Demand Met (PJ/yr)** | 20.0 | 30.0 | 40.0 | 50.0 | 60.0 | 70.0 | 80.0 | 90.0 |
| **New Capacity (GW)** | 0.762 | 0.191 | 0.191 | 0.191 | 0.191 | 0.191 | 0.191 | 0.191 |
| **Cumulative Capacity (GW)** | 0.762 | 1.144 | 1.525 | 1.906 | 2.287 | 2.668 | 3.050 | 3.431 |

---

## 💡 Economic Takeaway
The **$51.0 Million** objective value represents an artificial penalty cost ($99,999/kW capital cost) demonstrating that the model correctly identifies supply deficits and penalizes unmet demand.
