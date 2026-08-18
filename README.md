# MedCare Pharmacy Ltd. — Inventory & Supply Chain Analytics

**Author:** Arigbo Favour ("The British Realtor™" — Data Analytics)
**Tools:** PostgreSQL (or SQL Server) · Excel · Power BI · Python (synthetic data generation)
**Type:** Simulated enterprise dataset & portfolio analytics project

---

## 1. Project Overview

This project simulates a full data-analyst engagement for a fictional pharmacy chain, **MedCare Pharmacy Ltd.**, operating 8 branches, 3 warehouses, 60 suppliers, and ~2,000 SKUs across Nigeria. The brief: identify why the business faces stock-outs, overstocking, drug expiries, supplier delays, and margin pressure, then recommend fixes.

> **Note on the data:** all figures are generated from a synthetic dataset built to be internally realistic in *structure and relationships* (SKUs link to sales, purchase orders link to suppliers, inventory reflects both), so the analytical methodology transfers directly to a real engagement. Absolute totals (e.g. total procurement spend vs. total revenue) are not calibrated to reconcile the way a live company's books would, since the sales and procurement volumes were generated independently. Treat percentage-based, ranked, and trend insights as the reliable output of this project; treat raw currency totals as illustrative rather than audited figures.

## 2. Business Questions Answered

| Domain | Questions |
|---|---|
| Inventory | Stock-out frequency, overstock, expiry loss by category, fast vs. slow movers |
| Procurement | Supplier delays, quality ranking, lead time, preferred-vendor scoring |
| Sales | Revenue by drug/branch/category, payment method mix, age-group demand |
| Warehousing | Turnover, expiry rate by warehouse, cold-chain utilization |
| Branch Performance | Revenue, profit margin, customer satisfaction, wait times |
| Employee Performance | Revenue per pharmacist, experience vs. output, salary efficiency |

## 3. Data Model

10 relational tables, ~470,000 rows total:

```
suppliers ──┐
            ▼
      purchase_orders ──► purchase_order_items ──► drug_master
                                                        │
warehouses ──► inventory_snapshots ◄───────────────────┘
            │
branches ──►│──► sales_transactions ◄── employees (pharmacists)
            │
            └──► customer_feedback
```

Full schema, cleaning scripts, and analytical views: see `/02_Database`.

## 4. Methodology

1. **Data generation** (Python/pandas) — 10 CSVs with enforced referential integrity, Nigeria-specific pharma context (local manufacturers, Naira pricing, realistic therapeutic classes).
2. **Database layer** (PostgreSQL/SQL Server) — schema creation, load, cleaning (duplicates, nulls, impossible values), then 6–7 analytical views, one per business domain, so downstream tools never touch raw tables.
3. **Excel** — first-pass exploration and PivotTable sanity checks before database load; later, live-connected PivotTables reading directly from the database for lightweight reporting.
4. **Power BI** — star-schema model off the SQL views, DAX measures for KPIs (revenue, margin, stock-out count, expiry loss, supplier lead time), 7 dashboard pages matching the 6 business domains plus an executive summary.
5. **Automation** — scheduled view refresh (pg_cron/SQL Server Agent or Task Scheduler) feeding a scheduled Power BI dataset refresh, so the dashboard reflects new data without manual re-import.

## 5. Repository Structure

```
MedCare_Pharmacy_Analytics/
├── 01_Datasets/            → 10 source CSVs
├── 02_Database/            → schema.sql, cleaning.sql, views.sql, automation.sql
├── 03_PowerBI/              → MedCare_Dashboard.pbix, DAX_measures.md
├── 04_Excel/                 → connected workbook
├── 05_Report/                  → Board_Insights.docx, methodology notes
└── README.md
```

## 6. Dashboard Pages

1. Executive Summary — company-wide KPIs and trend
2. Inventory — stock status, reorder alerts, expiry loss by category
3. Procurement/Suppliers — vendor scorecard, delivery reliability
4. Sales — revenue drivers by drug, branch, payment method, age group
5. Warehousing — turnover, expiry rate, cold-chain utilization
6. Branch Performance — revenue, profit, satisfaction by branch
7. Employee Performance — revenue per pharmacist, experience vs. output

## 7. Key Findings

See `05_Report/Board_Insights.docx` for the full board-level write-up. Headline points:

- **~23% of active SKUs sit below reorder level** at the most recent inventory snapshot — a material stock-out exposure across the network.
- **14.2% of purchase orders arrive delayed**, with average lead time of ~26 days — longer than typical for fast-moving OTC categories.
- **Only 14 of 60 suppliers (23%) are flagged "preferred"** despite quality scores that suggest more should qualify — a procurement-policy gap worth investigating.
- **Port Harcourt and Lagos generate ~42% of total revenue** combined, while Benin City and Warri lag — a branch-investment prioritization signal.
- **Chronic Disease Management drugs account for the largest share of expiry loss by value** — pointing to slow-moving, high-cost maintenance medication as the category needing tighter reorder discipline.
- **Only 55 of 150 employees (37%) are logged against a sales transaction** in the pharmacist role — worth checking whether this reflects role mix (non-sales-facing staff) or a data-capture gap in a real deployment.

## 8. How to Reproduce

1. Load `01_Datasets/*.csv` into PostgreSQL or SQL Server using `02_Database/schema.sql`.
2. Run `cleaning.sql`, then `views.sql`.
3. Point Power BI Desktop at the views (Import mode); apply the theme in `03_PowerBI/theme.json`.
4. Publish to Power BI Service; configure scheduled refresh (On-premises Data Gateway required if the database is local, not cloud-hosted).

## 9. Author's Note

Built as a self-directed portfolio project to demonstrate end-to-end analytics capability — from synthetic data engineering through database design, BI development, and business storytelling — ahead of applying for data analyst / BI roles in the healthcare and pharmaceutical sector.
