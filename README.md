# Chronic Disease Population Health Dashboard

A five-page interactive Tableau dashboard profiling chronic disease burden, care access gaps, and Medicare hospitalization risk across 3,143 US counties — built to replicate the population health analytics framework used by Optum's care management teams.

---

## Project Overview

Chronic disease is the primary driver of US healthcare spending. But the burden is not evenly distributed — diabetes rates in rural Mississippi are nearly three times the national average, and counties with the highest disease prevalence often have the worst access to care. This dashboard surfaces those patterns at the county level, giving analysts a single view to answer four questions:

- **Where** is the chronic disease burden highest?
- **Who** carries the heaviest load by age, sex, and income?
- **Are they getting care** — or is there a gap?
- **What does unmanaged disease cost** in hospitalizations and Medicare spend?

---

## Dashboard Pages

| Page | Title | Core Question |
|------|-------|---------------|
| 1 | Disease Prevalence Map | Where is the burden highest by condition? |
| 2 | Demographics Breakdown | Who is most affected — by age, sex, income? |
| 3 | Care Gap Analysis | Where do sick populations lack access to care? |
| 4 | Hospitalization Risk Profile | Where does unmanaged disease drive hospitalizations? |
| 5 | Trend Over Time | Is it improving or worsening across states since 2014? |

---

## Data Sources

| Dataset | Source | Granularity | Role |
|---------|--------|-------------|------|
| CDC PLACES 2025 | CDC | County | Disease prevalence, care access metrics |
| CMS Medicare Geographic Variation 2014–2024 | CMS | State / County | ER visits, hospitalizations, Medicare spend |
| CDC BRFSS 2023 | CDC | Individual respondent | Prevalence by age, sex, income, race |
| CMS Chronic Conditions PUF 2010 | CMS | Medicare beneficiary profile | Comorbidity burden, cost escalation |

---

## Tech Stack

| Tool | Purpose |
|------|---------|
| Python (pandas) | Data cleaning, filtering, FIPS joining, pivot operations |
| Tableau Desktop | Dashboard build, LOD expressions, parameter controls |

---

## Data Pipeline

```
Raw Downloads
│
├── CDC BRFSS 2023 (.XPT → CSV)          step1_clean_brfss_v2.py
├── CMS Chronic Conditions PUF (.CSV)     step2_clean_chronic_conditions.py
├── CMS Geographic Variation (.CSV)       step3_clean_geographic_variation.py
└── CDC PLACES 2025 (.CSV)               step4_clean_places.py
                                                  │
                                         step5_model_places.py
                                         (deduplicate to latest year per county)
                                                  │
                                         step6_join_places_geovar.py
                                         (join PLACES + GeoVar on FIPS → county_master.csv)
                                                  │
                                         Tableau Dashboard Build
```

---

## Output Files Loaded into Tableau

| File | Rows | Used For |
|------|------|----------|
| `county_master.csv` | 3,143 | Pages 1, 3, 4 — all map and scatter views |
| `brfss_2023_filtered.csv` | 433,323 | Page 2 — demographic breakdowns |
| `chronic_conditions_cleaned.csv` | 22,003 | Pages 2, 4 — comorbidity and cost views |
| `geographic_variation_all_years.csv` | 35,751 | Page 5 — state trend lines 2014–2024 |

---

## Key Tableau Features Used

- **LOD Expressions** — `FIXED` for national benchmark calculations independent of view filters
- **Parameter Controls** — condition selector driving all sheets on Pages 1 and 2 simultaneously
- **Dual-Axis Charts** — Medicare payment (bar) + inpatient admissions (line) on Page 4
- **Dashboard Filter Actions** — clicking a state on the map filters the scatter plot to that state
- **Table Calculations** — year-over-year ER visit change with `LOOKUP()` on Page 5
- **Reference Lines & Bands** — national average benchmarks and COVID period annotation

---

## Notable Findings

- Diabetes prevalence exceeds 25% in several Deep South counties (national avg ~11%)
- COPD is heavily concentrated in Appalachia — West Virginia dominates the top counties
- Counties in the Priority Intervention Zone (high burden + high care gap) are disproportionately rural and low-income
- Medicare patients with 5+ conditions generate dramatically higher inpatient admissions than those with 1–2 conditions
- ER visit rates range from 67 to 1,236 per 1,000 Medicare beneficiaries across counties — an 18x difference

---

## Repository Structure

```
├── data/
│   └── cleaned/
│       ├── county_master.csv
│       ├── brfss_2023_filtered.csv
│       ├── chronic_conditions_cleaned.csv
│       └── geographic_variation_all_years.csv
│
├── scripts/
│   ├── step1_clean_brfss_v2.py
│   ├── step2_clean_chronic_conditions.py
│   ├── step3_clean_geographic_variation.py
│   ├── step4_clean_places.py
│   ├── step5_model_places.py
│   └── step6_join_places_geovar.py
│
└── README.md
```

---

## Data Notes

- **FIPS codes** are zero-padded to 5 digits throughout (`01001` not `1001`) — critical for Tableau geographic role assignment
- **CDC PLACES** uses crude prevalence (age-adjusted not available at county level in 2025 release)
- **CMS PQI avoidable admission columns** are suppressed at county level — hospitalization risk tier is built from ER visits, inpatient stays, and readmission rate instead
- **BRFSS survey weights** are available in the cleaned file but crude prevalence is used in this dashboard

---

## Related Projects

| Project | Title |
|---------|-------|
| Project 3 | Provider Network Performance Scorecard |
| Project 8 | Medicare Advantage Star Ratings Performance Report |
