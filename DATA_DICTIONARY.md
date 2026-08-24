# Data Dictionary
 
Schemas as implemented in `notebooks/silver/` and `notebooks/gold/`.
 
## Silver Layer
 
### `silver.sales`
| Column | Type | Notes |
|---|---|---|
| sales_id | INT (PK) | Row-level identifier, deduplicated on load |
| order_id | STRING | Not unique per row — one order can have multiple line items |
| customer_id | STRING | FK to `silver.customer` |
| product_id | STRING | FK to `silver.products` |
| postal_code | STRING | |
| region | STRING | Carried as a flat/degenerate attribute — see Key Design Decisions in README |
| order_date | DATE | |
| ship_date | DATE | |
| ship_mode | STRING | |
| sales | DECIMAL(18,4) | |
| discount | DOUBLE | Rate (e.g. 0.15), not a dollar amount |
| quantity | INT | |
| profit | DECIMAL(18,4) | |
| load_timestamp | TIMESTAMP | |
 
### `silver.customer` (SCD Type 2)
| Column | Type | Notes |
|---|---|---|
| customer_id | STRING NOT NULL | Business key |
| customer_name | STRING | |
| customer_segment | STRING | |
| customer_hash | STRING NOT NULL | SHA-256 of customer_id + name + segment; used to detect changes |
| valid_from | TIMESTAMP NOT NULL | |
| valid_to | TIMESTAMP | Null/`9999-12-31` while current |
| is_current_flag | BOOLEAN NOT NULL | |
| load_timestamp | TIMESTAMP NOT NULL | |
 
### `silver.products` (SCD Type 2)
| Column | Type | Notes |
|---|---|---|
| product_id | STRING | Business key |
| category | STRING | |
| sub_category | STRING | |
| product_name | STRING | |
| product_hash | STRING NOT NULL | SHA-256 of product_id + category + sub_category + name |
| valid_from | TIMESTAMP NOT NULL | |
| valid_to | TIMESTAMP | |
| is_current_flag | BOOLEAN NOT NULL | |
| load_timestamp | TIMESTAMP NOT NULL | |
 
## Gold Layer
 
### `gold.dim_customer`
`customer_key` (surrogate, `ROW_NUMBER()`) + all columns from `silver.customer`, including full SCD2 history.
 
### `gold.dim_products`
`product_key` (surrogate) + `product_id`, `product_name`, `category`, `sub_category`, `product_hash`, `valid_from`, `valid_to`, `is_current_flag`, `load_timestamp`.
 
### `gold.dim_date`
Generated calendar table, 2014-01-01 to 2018-12-31: `date_key`, `full_date`, `year`, `quarter`, `quarter_name`, `month`, `month_name`, `month_name_short`, `month_year_sort`, `month_year`, `quarter_year`, `week_of_year`, `day_of_month`, `day_of_week`, `day_name`, `is_weekend`, `date_string`.
 
### `gold.fact_sales`
| Column | Type / Source | Notes |
|---|---|---|
| sales_id | from silver.sales | |
| order_id | from silver.sales | |
| customer_key | FK → dim_customer | |
| product_key | FK → dim_products | |
| region | from silver.sales | Degenerate dimension — no separate geography table |
| order_date_key / ship_date_key | FK → dim_date | |
| order_date, ship_date, ship_mode, sales, discount, quantity, profit | from silver.sales | |
| delivery_days | derived | `date_diff(ship_date, order_date)` |
| discount_amount | derived | `sales * discount` — additive, aggregates correctly |
| gross_sales | derived | `sales / (1 - discount)` — pre-discount sales value |
 
> **Note:** `dim_geography` and `silver.geography` still exist in the repo (`notebooks/silver/geography_silver.ipynb`, `notebooks/gold/gold_dim_geography.ipynb`) but are not referenced by `fact_sales`. Region is carried directly on the fact table instead. Decide whether to delete these two notebooks or keep them documented as a superseded approach.
 
