# Handout 4 (HO4): Fuel Supply Results & Analysis

## 🎯 Executive Summary
* **Optimization Status**: `OPTIMAL`
* **Objective Function (NPV Total Cost)**: **$51,009,521.16** (0.0% delta vs. HO3)
* **Active Technologies Added**: `MINNGS` (gas mining), `IMPDSL` (diesel import)
* **LP Dimensions**: 872 Rows (+406) × 480 Columns (+240) × 4,320 Non-Zeros
* **Solve Time**: < 0.05 seconds

---

## 📊 Visual Results & Analytics

### System Cost Invariance (HO3 vs. HO4)
![Cost Invariance](graphs/ho4_cost_invariance.png)

---

## 💡 The Economic Merit Order Lesson
Although natural gas mining (`MINNGS`) and diesel imports (`IMPDSL`) were provided at low unit extraction costs, **neither fuel was utilized by the solver**. 

Because power conversion technologies (gas turbines or diesel generators) were not yet connected to the Reference Energy System, there was no physical pathway to convert primary fuels into electricity (`ELC003`). The solver correctly maintained backstop dispatch, resulting in an identical objective value of **$51,009,521.16**.
