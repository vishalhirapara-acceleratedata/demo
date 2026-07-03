# Design: Monthly Revenue Reporting Mart

`design.md` is the single durable plan + evidence ledger for this intent.

## Architecture

- **Grain:** `fct_revenue_monthly` — one row per calendar month.
- **Materialization:** Staging as views; intermediate as ephemeral; mart as table.
- **Technical approach:** dbt project targeting DuckDB (`demo-without-eph`). Seed data (`sample_orders`) is authored as a CSV in the dbt project `seeds/` directory and loaded by `dbt seed`. Models follow the 3-layer medallion pattern: staging → intermediate → mart.
- **Key decisions:**
  - **Synthetic seed data:** No external source system; sample transactional data is authored as a dbt seed. This keeps the intent self-contained and reproducible.
  - **DuckDB target:** `vd-domain.yml` declares `destination.type: duckdb`; the entire pipeline runs against the local DuckDB file.
  - **Single mart:** One aggregate fact table (`fct_revenue_monthly`) rather than multiple dimensional drill-downs, keeping scope tight for the first iteration. Drill-downs by product or region are noted as future extensions.

## Model Inventory

| Model | Layer | Grain | Materialization | Dependencies |
|---|---|---|---|---|
| `sample_orders` | seed | One row per order line | seed | — |
| `stg_sample__orders` | staging | One row per order line | view | `sample_orders` |
| `int_orders_enriched` | intermediate | One row per order line with computed revenue | ephemeral | `stg_sample__orders` |
| `fct_revenue_monthly` | mart | One row per calendar month | table | `int_orders_enriched` |

## Source Mapping / Discovery

- **Source system:** Synthetic seed data (`seeds/sample_orders.csv`).
- **Bronze Adequacy:** Ready — the seed data is authored alongside the dbt project, so schema and content are fully controlled. No external bronze tables to profile.
- **Seed schema (planned):**
  - `order_id` (STRING) — unique order identifier
  - `order_date` (DATE) — date the order was placed
  - `customer_id` (STRING) — customer identifier
  - `product_category` (STRING) — product category for potential drill-downs
  - `quantity` (INTEGER) — number of units
  - `unit_price` (DECIMAL) — price per unit
  - `total_amount` (DECIMAL) — `quantity * unit_price`

## Change Impact

No existing artifacts impacted — fresh build target. The workspace has no prior dbt models, seeds, or manifests.

## Build Plan

| Step | Phase | Goal | Skill | Status | Evidence |
|---|---|---|---|---|---|
| `01-scaffold` | Build | Scaffold DuckDB dbt workspace | `scaffolding-duckdb-dbt-workspace` | working | — |
| `02-seed` | Build | Create sample_orders seed CSV | `generating-dbt-model` | working | — |
| `03-staging` | Build | Generate stg_sample__orders staging model | `generating-dbt-model` | working | — |
| `04-intermediate` | Build | Generate int_orders_enriched intermediate model | `generating-dbt-model` | working | — |
| `05-mart` | Build | Generate fct_revenue_monthly mart model | `generating-dbt-model` | working | — |
| `06-sandbox` | Build | Run dbt build in DuckDB sandbox | `running-dbt-in-sandbox` | working | — |
| `07-tests` | Verify | Write and run dbt data tests | `dbt-unit-testing` | working | — |
| `08-evaluate` | Verify | Run dbt_project_evaluator audit | `evaluating-dbt-project` | working | — |
| `09-docs` | Verify | Document models in schema.yml | `documenting-dbt-models` | working | — |
| `10-verify` | Verify | Final completion claim verification | `verifying-completion-claims` | working | — |
| `11-publish` | Publish | Commit contracts, docs, and PR-ready branch | `publishing-data-product` | working | — |

## Gate Ledger

- ✅ Intent gate
- ⬜ Design gate
- ⬜ Build gate
- ⬜ Verify gate
- ⬜ Publish gate

## Approvals

- [x] User approved intent — `2026-07-03 11:44` (UTC)
- [ ] User approved design — `YYYY-MM-DD HH:MM` (UTC)
- [ ] User approved ship — `YYYY-MM-DD HH:MM` (UTC)
- [ ] User approved breaking schema delta — `YYYY-MM-DD HH:MM` (UTC, if applicable)
