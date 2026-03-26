# Sleep Health Analysis
### Analyzing the Demographic and Lifestyle Factors Associated with Sleep Quality

---

## Overview

Exploratory analysis of demographic and lifestyle factors associated with sleep quality and sleep disorder prevalence, using SQL for data cleaning and aggregation, and Tableau for visualization.

**Author:** Aiden Mac Nab
**Date:** March 2026
**Dataset:** [Sleep Health and Lifestyle Dataset](https://www.kaggle.com/) — provided by Laksika Tharmalingam

---

## Tools & Technologies

| Tool | Purpose |
|------|---------|
| PostgreSQL | Data cleaning, normalization, and aggregation |
| Tableau | Data visualization and dashboard creation |

---

## Objective

Identify which demographic and lifestyle attributes are most associated with higher sleep quality and elevated stress levels. Factors examined include age, gender, occupation, physical activity, stress levels, and BMI category.

---

## Key Findings

- **Stress level** was the strongest predictor of sleep quality — average scores ranged from `8.97` at low stress to `5.86` at high stress
- **BMI category** showed the most dramatic disparity — overweight individuals were nearly **12x more likely** to report a sleep disorder than those with a normal weight classification (`87.2%` vs `7.4%`)
- **Age** was positively associated with sleep quality — individuals in their 50s averaged `8.17` vs `5.79` for those in their 20s
- **Occupation** played a meaningful role — Engineers reported the highest average sleep quality (`8.41`), while Salespersons reported the lowest (`6.00`)

---

## Project Structure

```
sleep-health-analysis/
├── Capstone Project/
│   ├── sleep_quality_analysis.md   # Full analysis write-up with SQL and visualizations
│   └── Images/                     # Chart exports from Tableau
└── README.md
```

---

## Visualizations

Interactive dashboards are available on Tableau Public:

🔗 [View on Tableau Public](https://public.tableau.com/views/SleepHealthVisualization/AgeSleepQualityGraph?:language=en-US&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link)

---

## Full Analysis

The complete write-up, including SQL logic and chart interpretations, is available in [`sleep_quality_analysis.md`](Capstone%20Project/sleep_quality_analysis.md).
