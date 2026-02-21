# dbt Transformation Layer – BigQuery Data Warehouse
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-Database-bf248b?style=for-the-badge&logo=postgresql&logoColor=white)
![dbt](https://img.shields.io/badge/dbt-Transformation-FF694B?style=for-the-badge&logo=dbt&logoColor=white)
![Google BigQuery](https://img.shields.io/badge/BigQuery-Data_Warehouse-6e24bf?style=for-the-badge&logo=googlebigquery&logoColor=white)
![Python](https://img.shields.io/badge/Python-3.x-24bf66?style=for-the-badge&logo=python&logoColor=white)
![SQL](https://img.shields.io/badge/SQL-Structured%20Query%20Language-blue?style=for-the-badge&logo=databricks&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-Containerized-bfaf24?style=for-the-badge&logo=docker&logoColor=white)

This project contains the transformation layer of an ELT pipeline built using **dbt (Data Build Tool)** on top of **Google BigQuery**.

The raw data is loaded into BigQuery from a PostgreSQL source (via an external ingestion process).  
This repository focuses exclusively on transforming that raw data into analytics-ready models.

---

## Architecture Overview
```

BigQuery `raw_data` dataset
        ↓
dbt Transformations
        ↓
BigQuery `transform_data` dataset
```

---

##  Technologies Used

- **dbt (v1.8.x)**
- **Google BigQuery**
- **SQL**
- **Service Account Authentication**
- **YAML-based testing & documentation**

---

## Project Structure
```

dbt_transformation/
│
├── models/
│ ├── staging/
│ │ ├── stg_customer.sql
│ │ ├── stg_order.sql
│ │ ├── stg_webshop_db.yml
│ │ └── src_webshop_db.yml
│ │
│ └── marts/
│ ├── dim_customer.sql
│ └── marts_webshop_db.yml
│
├── dbt_project.yml
└── README.md
```

## Data Warehouse Setup

BigQuery Project:

postgresql-store-database


Datasets:
- `raw_data` → Ingested source data
- `transform_data` → dbt-generated models

Authentication:
- Service Account JSON key (not included in repo)
- Configured via `profiles.yml`

---

##  Transformation Layers

### 1- Staging Layer (`staging/`)

Purpose:
- Clean raw data
- Rename columns consistently
- Convert data types
- Normalize timestamps
- Prepare structured base tables

Examples:
- Converted nanosecond timestamps into proper BigQuery `TIMESTAMP`
- Cast string-based monetary fields into `NUMERIC`
- Standardized primary/foreign key naming

---

### 2️- Marts Layer (`marts/`)

Purpose:
- Build analytics-ready dimensional models
- Create aggregated metrics
- Support BI and reporting use cases

Example:
`dim_customer`
- First order date
- Most recent order date
- Number of orders
- Geographic information
- Cleaned customer attributes

Materialization Strategy:
- `staging` → Views
- `marts` → Tables

---

## Data Quality & Testing

Implemented dbt schema tests including:

- `unique`
- `not_null`
- `relationships`
- `accepted_values`

Tests ensure:
- Primary keys are valid
- Foreign keys maintain integrity
- Business rules are enforced

## Generate dbt Documentation

dbt automatically builds interactive documentation.

Generate docs:
```
dbt docs generate
```
Serve docs locally:
```
dbt docs serve
```
Then open:

http://localhost:8080

### How to Run This Project

1️- Install dbt for BigQuery:
```
pip install dbt-bigquery
```
2️- Configure ~/.dbt/profiles.yml with:

- GCP project ID

- Dataset (transform_data)

- Location (EU)

- Service account key path

3️- Validate connection:
```
dbt debug
```
4️- Run transformations:
```
dbt run
```
5️- Run tests:
```
dbt test
```
## Integration with Airflow Project

To load data into BigQuery before running dbt:

Use the Airflow ingestion project:

https://github.com/HaseebAhmad23/airflow-postgres-bigquery

That project:

- Extracts data from PostgreSQL

- Loads into BigQuery raw_data

- Uses Python + Airflow orchestration

Recommended Workflow:

- Run Airflow project → load raw data into BigQuery

- Run dbt project → transform raw data into dimensional models

This separation follows modern ELT architecture best practices.

## Security

- Service account keys are excluded via .gitignore

- No credentials are stored in this repository

## Key Learnings

- Designing staging and dimensional models

- Handling timestamp precision issues (nanoseconds → microseconds)

- Managing data type consistency

- Implementing automated data quality checks

- Structuring dbt projects using best practices

## Author

Haseeb Ahmad (Fullstack Developer)
