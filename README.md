# Telecom Revenue & Customer Churn SQL + Power BI KPI Dashboard

An end-to-end relational SQL analytics and Business Intelligence reporting pipeline built on **7,032 IBM Telco customer records**, utilizing SQL Common Table Expressions (CTEs), Window Functions (`RANK`, `DENSE_RANK`), `CASE` tenure cohort segmentation, and Power BI DAX measures to quantify monthly recurring revenue (MRR) at risk and prioritize retention campaigns[cite: 14, 15].

## Analytical Architecture Flow

```text
IBM Telco Dataset (7,043 raw rows)
        │
        ▼
Python ETL & Data Cleaning (Coerce TotalCharges, drop 11 NaNs -> 7,032 records)
        │
        ▼
Relational SQL Warehouse (SQLite3: telco_customers table)
        │
        ├──▶ Query 1 (CTE + CASE): Executive KPIs & Monthly Revenue Lost ($139.13K MRR)
        ├──▶ Query 2 (GROUP BY + RANK): Contract & Internet Service Churn Ranking
        ├──▶ Query 3 (CASE Cohorts): Tenure Lifecycle Attrition (0–12m vs. 49+m)
        └──▶ Query 4 (DENSE_RANK Partition): Top High-Bill At-Risk Active Customers
        │
        ▼
Power BI Interactive KPI Dashboard (DAX Measures & Retention Action Matrix)
```

## Key SQL & BI Findings
- **Executive Revenue & Churn KPIs:** Across `7,032` active and churned accounts (`$455,661.00` total MRR), overall customer churn is **26.58%** (`1,869` churned customers), representing **$139,130.85/month** (`30.53%`) in lost recurring revenue[cite: 14, 15].
- **Highest-Risk Segment:** **Month-to-Month Fiber Optic** subscribers exhibit a **54.67%** churn rate and rank **#1** in total monthly revenue loss (`RANK() OVER (ORDER BY monthly_revenue_lost DESC)`)[cite: 14, 15].
- **Tenure Lifecycle Attrition:** Early-stage customers (`0–12 Months`) churn at **47.68%** compared to **9.51%** among long-term loyal customers (`49+ Months`), pinpointing the first-year onboarding window as the primary retention lever[cite: 14, 15].
- **Targeted Retention List:** Used `DENSE_RANK() OVER (PARTITION BY Contract ORDER BY MonthlyCharges DESC)` to isolate the highest-paying active Month-to-Month customers in their first 12 months for proactive contract-upgrade offers[cite: 15].

## Core Power BI DAX Measures
```dax
Total Customers = COUNTROWS('telco_customers')
Churned Customers = CALCULATE(COUNTROWS('telco_customers'), 'telco_customers'[Churn] = "Yes")
Churn Rate % = DIVIDE([Churned Customers], [Total Customers], 0)
Monthly Revenue Lost = CALCULATE(SUM('telco_customers'[MonthlyCharges]), 'telco_customers'[Churn] = "Yes")
```

## Tech Stack
- **SQL & Relational Querying:** SQL (`CTEs`, `RANK()`, `DENSE_RANK()`, `CASE`, `GROUP BY`), SQLite3[cite: 14, 15]
- **Data Ingestion & Cleaning:** Python, Pandas[cite: 14, 15]
- **Business Intelligence:** Microsoft Power BI (DAX Measures, Power Query ETL, KPI Cards)[cite: 14, 15]

## Setup & Execution
1. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```
2. Open `telco_revenue_churn_sql_bi.ipynb` in Jupyter Notebook or Google Colab and run all cells to execute the SQL queries and view the output tables[cite: 15].
