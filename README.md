# Jaffle Shop — dbt Fundamentals Project

A dbt project built while completing the **dbt Fundamentals certification**, transforming raw e-commerce and payment data into clean, tested, documented staging and mart models on **Snowflake**.

This project demonstrates a full modern ELT transformation workflow: raw sources → staging → marts, with source freshness monitoring, data quality tests, documentation, and a git-based deployment workflow (feature branch → PR → merge to main).

---

## 🧱 Tech Stack

- **dbt Cloud** — transformation orchestration, IDE, scheduling
- **Snowflake** — cloud data warehouse
- **Git / GitHub** — version control and PR-based deployment workflow
- **YAML** — source declarations, schema tests, documentation

---

## 📂 Project Structure

```
models/
├── staging/
│   ├── jaffle_shop/
│   │   ├── _jaffle_shop__sources.yml      # source declarations + freshness config
│   │   ├── _stg_jaffle_shop.yml           # staging model docs & tests
│   │   ├── stg_jaffle_shop__customers.sql
│   │   └── stg_jaffle_shop__orders.sql
│   └── stripe/
│       ├── _stripe__sources.yml           # Stripe source declaration
│       └── stg_stripe__payment.sql
└── marts/
    ├── marketing/
    │   └── dim_customers.sql              # customer dimension
    └── finance/
        └── fct_orders.sql                 # order + payment fact table
```

---

## 🔄 Data Sources

| Source        | Table      | Description                                  |
|---------------|-----------|-----------------------------------------------|
| `jaffle_shop` | customers | Raw customer records from the jaffle shop app |
| `jaffle_shop` | orders    | Raw order records                             |
| `stripe`      | payment   | Raw payment transactions from Stripe          |

Source freshness is configured on the `jaffle_shop` source (warn after 12 hours, error after 999 days) using `loaded_at_field: _etl_loaded_at`.

---

## 🏗️ Models

**Staging layer** — 1:1 with source tables, light renaming/casting only:
- `stg_jaffle_shop__customers`
- `stg_jaffle_shop__orders`
- `stg_stripe__payment`

**Marts layer** — business-ready, analytics-friendly tables:
- `dim_customers` — customer dimension, joined from staging customer data
- `fct_orders` — order fact table, joining orders with Stripe payment data

---

## ✅ Data Quality Tests

Applied via schema YAML (`data_tests`) across staging and mart models:
- `unique` / `not_null` on primary keys (`customer_id`, `order_id`)
- `accepted_values` on `order_status` (placed, shipped, completed, returned, return_pending)
- `relationships` — referential integrity between `fct_orders.customer_id` and `dim_customers.customer_id`

Run tests with:
```bash
dbt test
```

---

## 📖 Documentation

Column and model descriptions are defined in YAML and doc blocks (`{{ doc('order_status') }}`). Generate and view the full documentation site with:
```bash
dbt docs generate
dbt docs serve
```

---

## 🚀 Deployment Workflow

Changes are developed on feature branches and merged to `main` via GitHub pull requests, following a standard PR review workflow before being picked up by the production deployment job.

---

## 🎓 About This Project

Built as part of the **dbt Fundamentals** certification to practice:
- Structuring a dbt project (staging → marts)
- Declaring and testing sources
- Writing modular, DRY SQL using `ref()` and `source()`
- Implementing data quality tests and documentation
- Git-based development and deployment workflows

> Note: This project was originally developed in a dbt Cloud training environment. The dbt Cloud project itself has since been repurposed for further learning, but this repository is preserved as-is to showcase the completed work.
