# 🚲 BIXI Data Platform

A modern data engineering project built around **BIXI Montréal open data**.

The goal of this project is to design and implement a production-oriented data platform that ingests historical and incremental BIXI bike-sharing data, transforms it through a layered data architecture, and makes it available for analytics and business intelligence.

---

## 🎯 Project Goals

This project demonstrates an end-to-end data engineering workflow including:

* Batch ingestion of historical BIXI data
* Incremental ingestion of new data
* Cloud-based data storage on **Amazon S3**
* Data processing and transformation with **Databricks**
* Medallion architecture: **Bronze → Silver → Gold**
* Data quality and validation
* Incremental processing and idempotency
* Data modeling for analytics
* Job orchestration
* Testing and monitoring
* BI-ready analytical datasets

The project is designed as a realistic portfolio implementation rather than a simple data analysis notebook.

---

## 🏙️ Data Source

The project uses the official **BIXI Montréal Open Data** platform.

Primary data source:

* BIXI Open Data
* Historical trip data
* Station-related data where applicable

Historical datasets currently available in the project include:

```text
2024
2025
```

The data is stored externally in **Amazon S3** and is not committed to this Git repository.

---

## 🏗️ Architecture

The platform follows a Medallion Architecture:

```text
                    BIXI Open Data
                          │
                          ▼
                    Amazon S3
                 ┌───────────────┐
                 │  Raw Data     │
                 │  2024 / 2025  │
                 └───────┬───────┘
                         │
                         ▼
                  ┌─────────────┐
                  │   BRONZE    │
                  │ Raw / Audit │
                  └──────┬──────┘
                         │
                         ▼
                  ┌─────────────┐
                  │   SILVER    │
                  │ Clean /     │
                  │ Validated   │
                  │ / Typed     │
                  └──────┬──────┘
                         │
                         ▼
                  ┌─────────────┐
                  │    GOLD     │
                  │ Analytics / │
                  │ BI Models   │
                  └──────┬──────┘
                         │
                         ▼
                  BI / Analytics
```

### Bronze

The Bronze layer preserves the source data with minimal transformation.

Responsibilities:

* Raw ingestion
* Schema preservation
* Ingestion metadata
* Source tracking
* Load timestamps
* Batch identification

### Silver

The Silver layer contains cleaned and standardized data.

Responsibilities:

* Data type conversion
* Timestamp normalization
* Null handling
* Data quality validation
* Duplicate handling
* Standardized station information
* Business rules

### Gold

The Gold layer contains business-oriented datasets optimized for analytics.

Potential datasets include:

* Trip analytics
* Station performance
* Popular routes
* Usage by arrondissement
* Hourly / daily / monthly demand
* Bike-sharing trends
* Origin-destination analysis

---

## 🔄 Batch & Incremental Processing

The project intentionally supports two different ingestion patterns.

### Historical Load

Historical datasets are loaded as batch data:

```text
BIXI 2024 ───────┐
                 ├──► Bronze ─► Silver ─► Gold
BIXI 2025 ───────┘
```

### Incremental Load

New data is processed incrementally:

```text
New BIXI Data
      │
      ▼
Identify New Records
      │
      ▼
Bronze
      │
      ▼
Silver
      │
      ▼
Gold
```

The pipeline will be designed to avoid unnecessary reprocessing and to support repeatable/idempotent executions.

---

## 🛠️ Technology Stack

| Technology               | Purpose                                    |
| ------------------------ | ------------------------------------------ |
| **Python**               | Data engineering and utility code          |
| **PySpark**              | Distributed data processing                |
| **Databricks**           | Data engineering platform                  |
| **Delta Lake**           | Reliable data storage and table management |
| **Amazon S3**            | Cloud object storage                       |
| **SQL**                  | Data transformation and analytics          |
| **Git / GitHub**         | Source control and collaboration           |
| **Databricks Workflows** | Pipeline orchestration                     |
| **BI Tool**              | Analytics and visualization                |

The technology stack may evolve as the project develops.

---

## 📁 Repository Structure

```text
bixi-data-platform/
│
├── README.md
├── .gitignore
│
├── config/
│
├── docs/
│   └── architecture.md
│
├── notebooks/
│
├── src/
│   ├── bronze/
│   ├── silver/
│   └── gold/
│
└── tests/
```

---

## 📊 Initial Source Schema

The BIXI historical trip data currently being analyzed contains fields such as:

```text
STARTSTATIONNAME
STARTSTATIONARRONDISSEMENT
STARTSTATIONLATITUDE
STARTSTATIONLONGITUDE
ENDSTATIONNAME
ENDSTATIONARRONDISSEMENT
ENDSTATIONLATITUDE
ENDSTATIONLONGITUDE
STARTTIMEMS
ENDTIMEMS
```

The source schema is preserved in the Bronze layer.

Transformations and data-quality rules are applied downstream in the Silver layer.

---

## 🧪 Data Quality

Data quality will be treated as a first-class component of the pipeline.

Planned checks include:

* Required field validation
* Timestamp validation
* Geographic coordinate validation
* Duplicate detection
* Invalid station detection
* Trip duration validation
* Referential consistency
* Null-rate monitoring
* Unexpected schema changes

Example validation:

```text
latitude  ∈ [-90, 90]
longitude ∈ [-180, 180]
end_time >= start_time
```

Records failing critical quality rules will be identified and handled according to the pipeline's data-quality strategy.

---

## 📈 Planned Analytics

The Gold layer will support analytical questions such as:

### Trip Demand

* How does BIXI usage vary by day?
* What are the busiest hours?
* How does demand change by month and season?

### Station Analysis

* Which stations have the highest trip volume?
* Which stations are frequently used as origins?
* Which stations are frequently used as destinations?

### Geographic Analysis

* Which Montréal boroughs have the highest BIXI activity?
* What are the most common origin-destination pairs?
* How does demand vary geographically?

### Temporal Analysis

```text
Year
 └── Month
      └── Day
           └── Hour
```

These analytical datasets will ultimately be suitable for BI dashboards.

---

## 🔐 Security

No credentials, access keys, connection strings, or sensitive configuration should be committed to Git.

Examples:

```text
.env
AWS credentials
private keys
access tokens
```

are excluded through `.gitignore`.

Cloud authentication will use appropriate AWS/Databricks authentication mechanisms rather than hard-coded credentials.

---

## 🚀 Project Roadmap

### Phase 1 — Foundation

* [x] Create Git repository
* [x] Define project structure
* [x] Configure `.gitignore`
* [ ] Document architecture
* [ ] Validate S3 source structure

### Phase 2 — Bronze

* [ ] Connect Databricks to S3
* [ ] Implement historical ingestion
* [ ] Preserve source schema
* [ ] Add ingestion metadata
* [ ] Store Bronze tables using Delta

### Phase 3 — Silver

* [ ] Standardize data types
* [ ] Convert timestamps
* [ ] Implement data-quality rules
* [ ] Handle duplicates
* [ ] Implement business transformations

### Phase 4 — Gold

* [ ] Design analytical data model
* [ ] Create fact tables
* [ ] Create dimension tables
* [ ] Build aggregation tables
* [ ] Optimize for BI workloads

### Phase 5 — Incremental Processing

* [ ] Design incremental ingestion
* [ ] Implement watermarking / change detection
* [ ] Ensure idempotent processing
* [ ] Test repeated pipeline execution

### Phase 6 — Orchestration

* [ ] Create Databricks Workflows
* [ ] Add dependencies between layers
* [ ] Configure scheduling
* [ ] Add failure handling
* [ ] Add monitoring

### Phase 7 — Analytics

* [ ] Build BI datasets
* [ ] Create dashboards
* [ ] Document business metrics
* [ ] Validate analytical results

---

## 📚 Engineering Principles

The project follows several principles commonly used in production data platforms:

**Separation of concerns**

```text
Ingestion ≠ Transformation ≠ Analytics
```

**Idempotency**

Running the same pipeline multiple times should not create unintended duplicate results.

**Data lineage**

Each analytical record should be traceable back to its source data.

**Data quality**

Invalid data should be detected explicitly rather than silently ignored.

**Reproducibility**

The pipeline should be executable repeatedly with predictable results.

**Scalability**

The architecture should remain suitable as data volume and processing requirements increase.

---

## 📌 Project Status

> 🚧 **Active Development**

The project is currently in the foundation and source-data validation phase.

The next implementation step is to connect the existing BIXI data in **Amazon S3** to Databricks and implement the first Bronze ingestion pipeline.

---

## 👤 Author

**Ehsan Sadeghi**

Data Engineering / Backend Development

Technologies of interest:

```text
Databricks
Apache Spark
Python
SQL
AWS
Azure
.NET
Data Engineering
BI
```

---

## 📄 License

This repository contains project code and documentation.

BIXI datasets remain subject to the terms and conditions of their original data source.
