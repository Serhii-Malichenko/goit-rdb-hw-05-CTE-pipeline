# Homework 5 — CTEs and Materialized Views

## Overview

This project implements a reproducible SQL feature engineering pipeline using PostgreSQL. The goal is to calculate customer-level features from a small, synthetic e-commerce dataset and compare query execution performance using `EXPLAIN (ANALYZE, BUFFERS)`.

The dataset is a small educational dataset inspired by the Olist Brazilian E-commerce Dataset. It is **not the original full Olist dataset**.

## Dataset

The `data/` directory contains five CSV files:

- `olist_customers_dataset.csv`
- `olist_orders_dataset.csv`
- `olist_order_items_dataset.csv`
- `olist_products_dataset.csv`
- `olist_sellers_dataset.csv`

The dataset contains 220 customers, 320 orders, 475 order items, 45 products, and 18 sellers.

The CSV files are included in this repository, so no separate Kaggle download is required.

## Project Structure

```text
goit-rdb-hw-05/
├── data/
│   ├── olist_customers_dataset.csv
│   ├── olist_orders_dataset.csv
│   ├── olist_order_items_dataset.csv
│   ├── olist_products_dataset.csv
│   └── olist_sellers_dataset.csv
├── HW5_CTE_Materialized_Views.ipynb
└── README.md
```

## Implementation

### 1. Raw and Typed Tables

The CSV files are loaded into PostgreSQL raw tables. Typed tables are then created with appropriate data types, primary keys, foreign keys, and constraints.

Row counts are validated between the raw and typed layers.

### 2. CTE Feature Engineering Pipeline

A multi-stage CTE pipeline calculates the following customer-level features:

- `recency_days`
- `frequency`
- `monetary`
- `cohort_quarter`
- `avg_order_value_30d_recent`
- `cancellation_rate`
- `preferred_category`
- `preferred_category_revenue`

The pipeline uses aggregation, window functions, joins, and a dataset-based reference date to produce reproducible results.

### 3. Recursive CTE

A synthetic `hw5_referrals` table represents referral relationships between customers. A recursive CTE calculates referral depth and path, with protection against cycles.

The referral data is synthetic and is not part of the original Olist dataset.

### 4. Materialized View

The full customer feature pipeline is stored in `mv_olist_customer_features`.

A unique index on `customer_id` supports concurrent refreshes using:

```sql
REFRESH MATERIALIZED VIEW CONCURRENTLY mv_olist_customer_features;
```

### 5. Query Performance Analysis

The notebook compares:

- Full CTE pipeline execution.
- Lookup from the materialized view.

Both scenarios are evaluated using `EXPLAIN (ANALYZE, BUFFERS)`.

## Requirements

- Google Colab
- Python
- PostgreSQL
- `pandas`
- `SQLAlchemy`
- `psycopg2`

## How to Run

1. Open the notebook in Google Colab.
2. Run the cells from top to bottom.
3. Ensure the CSV files are loaded from the repository's `data/` directory.
4. Execute all SQL pipeline, recursive CTE, materialized view, and query analysis cells.
5. Use **Runtime → Restart session and run all** to verify reproducibility.

The notebook creates the required database objects and includes query outputs for validation and performance analysis.

## Results

The completed notebook demonstrates customer-level feature engineering, recursive SQL traversal, materialized feature caching, concurrent refresh
