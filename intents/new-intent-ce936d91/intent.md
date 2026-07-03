# Intent: Monthly Revenue Reporting Mart

## Goal
Build a dbt mart model that aggregates transactional sample data into monthly revenue metrics, enabling business users to analyze revenue trends over time.

## Source system
Sample seed data (synthetic transactional records) representing orders/invoices with line items, dates, and amounts. No external system connection required.

## Target
DuckDB lakehouse (`demo-without-eph`) in the `main` schema, deployed via dbt.

## Objects in scope
- dbt seed file(s) with sample transactional data (orders, order lines, or invoices)
- dbt staging model(s) to clean and type the seed data
- dbt intermediate model(s) to calculate line-level revenue
- dbt mart model `mart_monthly_revenue` aggregated by month
- dbt project scaffold (profiles, packages, project config)

## Success criteria
- `dbt build` succeeds for all models with no compilation or runtime errors
- `mart_monthly_revenue` produces one row per month with correct revenue totals
- Revenue calculation logic is deterministic and documented
- All models have basic schema documentation

## Out of scope
- Connection to real production data sources
- BI dashboard or semantic layer (exposure definitions only, no Power BI / Tableau artifacts)
- Orchestration schedule (dbt models only, no pipeline runner)
- Data freshness monitoring or alerting

## Open questions
- What dimensions should the sample data include (e.g., product, region, customer)?
- What time range should the sample data cover?
- Should the mart support drilled-down monthly revenue (by product, region, etc.) or only a single total?

## Classification
- **action:** work
- **type:** transformation
- **track:** dbt

## Approvals

The coordinator flips this only after a successful `AskUserQuestion` response of `approved`. Do not check by inference. Design, ship, and breaking-schema-delta approvals consolidate in `design.md`.

- [x] User approved intent — `2026-07-03 11:44` (UTC)
