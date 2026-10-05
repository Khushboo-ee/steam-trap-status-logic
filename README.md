# Steam Trap Status Logic

An interactive, animated explainer of how the monitoring system classifies each steam trap from two temperature readings.

**▶ Open the live demo:** https://khushboo-ee.github.io/steam-trap-status-logic/

No login or installation is needed. It works in any modern browser on desktop or mobile.

---

## What the demo shows

| Section | What you can do |
|---|---|
| **Live demo** | Watch an animated trap with inlet and outlet sensors. Press **Play tour**, pick a condition, drag the sliders, or click on the status map. The trap drawing, the map and the decision checklist all update together. |
| **Step 1 – Baselines become limits** | Enter a trap's baseline inlet and outlet temperature and see every limit and hold band calculated from them. |
| **Step 2 – Rule order** | The exact order in which the checks run. The first rule that matches sets the status. |
| **Step 3 – Hysteresis** | How hold bands stop the status from flickering, with two step-by-step walkthroughs. |
| **Step 4 – Over time** | An illustrative month for one trap: blocking, flooding, choking, maintenance and a leak. |
| **Reference** | Status codes, what each means and the suggested action. |

## Status codes

| Code | Status | Meaning |
|---|---|---|
| 1 | Normal | Inlet near steam temperature, outlet within its limit. Condensate leaves, steam is held back. |
| 9 | Leak | Inlet hot and outlet above its limit. Live steam is passing through the trap. |
| 3 | Flooding | Inlet cooled while condensate still reaches the outlet. Condensate is backing up. |
| 6 | Choking | Inlet cooled and outlet cold, or inlet very low. Little or nothing gets through. |
| 5 | Valve closed | Inlet close to room temperature. No steam reaches the trap. |
| 7 | No status | The readings did not match any rule. |
| 8 | Offline | No reading was received from the sensor. |

## How the logic works

Example trap: baseline inlet **191 °C**, baseline outlet **100 °C**, hysteresis **lower 4 °C / upper 0 °C**.

### 1. Baselines become limits

| Status | Limit | How it is set | Value | Hold band |
|---|---|---|---|---|
| Normal | Inlet minimum | 0.8 × baseline inlet | 152.8 °C | 148.8 – 152.8 °C |
| Normal | Outlet maximum | 1.3 × baseline outlet | 130 °C | none |
| Flooding | Inlet minimum | 0.37 × baseline inlet | 70.7 °C | 66.7 – 70.7 °C |
| Flooding | Inlet maximum | 0.8 × baseline inlet | 152.8 °C | none |
| Flooding | Outlet minimum | Fixed | 50 °C | 46 – 50 °C |
| Choking | Inlet minimum | Fixed | 50 °C | 46 – 50 °C |
| Choking | Inlet maximum | 0.8 × baseline inlet | 152.8 °C | none |
| Valve closed | Inlet maximum | Fixed | 50 °C | none |
| Leak | Outlet minimum | 1.3 × baseline outlet | 130 °C | 126 – 130 °C |

### 2. Rules are checked in order

1. No reading from both sensors → **Offline (8)**
2. **Hold check:** the reading is inside a hold band of the trap's previous status, and that status's other limits are still met → **keep previous status**
3. Inlet > 152.8 and outlet ≤ 130 → **Normal (1)**
4. Inlet above 70.7 up to 152.8, and outlet > 50 → **Flooding (3)**
5. Inlet above 50 up to 152.8 → **Choking (6)**
6. Inlet ≤ 50 → **Valve closed (5)**
7. Outlet > 130 → **Leak (9)**
8. Nothing matched → **No status (7)**

### 3. Hysteresis (hold bands)

- Only **minimum** limits have a hold band. It runs from **limit − 4 °C up to the limit**.
- **Maximum** limits have no hold band.
- The 4 °C is applied to the limit, never ± 4 °C around the baseline.

**Example: inlet cooling, outlet held at 112 °C**

| Time | Inlet | Without hold | With hold | Why |
|---|---|---|---|---|
| T1 | 160 | Normal | Normal | Above 152.8 |
| T2 | 154 | Normal | Normal | Still above 152.8 |
| T3 | 151 | Flooding | **Normal** | Inside hold band 148.8–152.8 |
| T4 | 150 | Flooding | **Normal** | Still inside the hold band |
| T5 | 148 | Flooding | Flooding | Below 148.8: hold ends |
| T6 | 150 | Flooding | Flooding | Exact Flooding match |
| T7 | 153 | Normal | Normal | Flooding maximum (152.8) has no hold |

**Example: outlet rising and falling, inlet held at 176 °C**

| Time | Outlet | Without hold | With hold | Why |
|---|---|---|---|---|
| T1 | 120 | Normal | Normal | Within 130 |
| T2 | 130.5 | Leak | Leak | Above 130: no hold on a maximum |
| T3 | 128 | Normal | **Leak** | Inside hold band 126–130 |
| T4 | 125 | Normal | Normal | Below 126: hold ends |

## Files

| File | Purpose |
|---|---|
| `index.html` | The complete demo in a single self-contained file. |
| `README.md` | This document. |

## Updating the demo

Replace `index.html` with the new version and commit. GitHub Pages republishes within a minute or two, and the link stays the same.

## Notes

- All charts and readings in the demo are illustrative, not data from a specific site.
- Limits and hold bands follow the configured baselines and hysteresis for each trap. The example above uses baseline 191 / 100 °C and hysteresis 4 / 0.
- Temperature alone cannot confirm every fault. For a trap flagged as leaking, an ultrasonic or visual check on site confirms the fault before the trap is replaced.
