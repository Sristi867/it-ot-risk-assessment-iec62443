# Securing the Factory Floor

Tata Steel summer internship project (June-July 2026): a risk assessment of a simulated steel plant's IT and OT networks against the IEC 62443 standard, with a Streamlit dashboard that shows where the plant is most exposed, zone by zone.

> **All data in this project is simulated.** The plant, the devices, the findings, and the costs are made up to show how the method works. Nothing here is real company data.

<!-- TODO: add a dashboard screenshot here after you build it -->
<!-- ![Dashboard screenshot](docs/dashboard_screenshot.png) -->

## Background

I built this during my summer internship at Tata Steel (June-July 2026) as a cybersecurity project. The plant and all the data here are simulated.

<!-- TODO: keep this section only after your mentor says it is okay. If not, delete it. -->

## The problem

A steel plant has two sides:

- **IT (the office):** laptops, email, HR systems.
- **OT (the workshop):** the controllers, sensors, and machines that run the furnace.

If the two sides are not properly separated, someone who breaks into the office network can reach the machines. In the office, the worst case is usually leaked data. In the workshop, the worst case is people getting hurt or the plant stopping.

This project checks how well the two sides are separated, finds the weak spots, and ranks the fixes.

## What it does

1. Takes a simulated list of 398 security findings across 346 devices in 6 plant zones.
2. Checks each finding against IEC 62443, the international standard for securing industrial systems.
3. Gives every finding a risk score from 0 to 100, using a simple rule (explained below).
4. Ranks the fixes by how important the device is against what the fix costs.
5. Shows the results in a dashboard so non-technical teams can see where the plant is most exposed.

## How the risk score works

Each finding gets three points from 1 to 4:

| Factor | What it asks | 1 | 2 | 3 | 4 |
|---|---|---|---|---|---|
| Criticality | How important is this device to running the plant? | Low | Medium | High | Critical |
| Reach | How easy is it to get to? | Isolated | Segmented | Partially segmented | Open path, or not segmented |
| Gap | How serious is the problem? | No gap | Logging, patching (SR 4, 6, 7) | Logins, access, tampering (SR 1, 2, 3) | Open traffic between zones (SR 5) |

The formula:

```
likelihood = (reach + gap) / 2
risk score = criticality x likelihood / 16 x 100
```

Risk levels: 0-25 Low, 26-50 Medium, 51-75 High, 76-100 Critical.

Every score can be checked by hand from the three columns in the data. For example, a Critical device with an open path and an SR 5 gap scores 4 x 4 / 16 x 100 = 100.

The weights are a judgment call. IEC 62443 does not give a scoring formula, so this is a simple, transparent method, not an official one.

## What the simulated data shows

- Supervisory Control is the riskiest zone (average score about 52). Enterprise IT is the safest (about 34).
- 68 findings are "open paths" where one zone can reach another without a proper lock. 67 of them are rated High or Critical.
- 35 findings are Critical. Fixing them costs about Rs 1.41 crore, which is about 26% of the total fix cost of Rs 5.4 crore.

These are results from made-up data. They show what the method produces, not real facts about any plant.

## The data

The main file is `data/plant_risk_assessment.csv`. Each row is one finding: one problem on one device.

| Column | Meaning |
|---|---|
| `asset_name` | Device name, like `FW-IDMZ-01` |
| `asset_type` | Kind of device: server, switch, PLC, firewall, and so on |
| `zone` | Enterprise IT, DMZ, Industrial DMZ, Operations Management, Supervisory Control, or Field/Device |
| `it_ot_domain` | IT or OT |
| `segmentation_status` | How well the device is separated: Segmented, Partially segmented, Isolated, Not segmented, Open path, or Not specified |
| `exposure_path` | For open paths, where the path leads |
| `iec_62443_gap` | The problem, tagged with the IEC 62443 requirement it breaks |
| `operational_criticality` | Low, Medium, High, or Critical |
| `criticality_points`, `reach_points`, `gap_points` | The three 1-4 scores |
| `risk_score`, `risk_level` | The final score and its level |
| `remediation_action` | The fix |
| `remediation_cost_inr` | Cost of the fix in rupees |

## How to run it

You need Python 3.9 or newer.

```
git clone https://github.com/<your-username>/securing-the-factory-floor.git
cd securing-the-factory-floor
pip install -r requirements.txt
streamlit run app.py
```

<!-- TODO: update these steps if your file names or commands are different -->

## Project structure

```
securing-the-factory-floor/
├── README.md
├── requirements.txt
├── app.py                         # the Streamlit dashboard
├── data/
│   └── plant_risk_assessment.csv  # the simulated findings
├── scripts/
│   └── score_risk.py              # calculates the risk scores
└── docs/
    └── scoring_method.md          # the scoring rule in more detail
```

## Limits

- The data is simulated, so the results are for demonstration only.
- Gap points depend on the IEC 62443 requirement family, so two findings under the same requirement get the same gap points.
- The scoring weights are my own choices, not part of the standard.
- This looks at network separation and the main IEC 62443 requirements. It is not a full audit.

## Built with

Python, pandas, Streamlit

## Author

Sristi Bose
<!-- TODO: add your LinkedIn link -->
