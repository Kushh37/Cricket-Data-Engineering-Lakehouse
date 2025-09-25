# Cricket Data Engineering Lakehouse

## Project Overview

This project establishes a robust data engineering platform for cricket data using Azure Databricks and Azure Data Lake Storage (ADLS). The goal is to build a scalable, efficient, and well-governed lakehouse architecture that can handle both batch and streaming data. The project utilizes a Medallion Architecture to progressively refine raw data into structured, ready-for-analysis datasets, providing a solid foundation for analytics, machine learning, and data science. I am building this project to showcase my skills and learning in modern data engineering practices, with the ultimate goal of creating a great cricket data analysis project.

## Architecture & Data Flow

The project follows the Medallion Architecture, organizing data into three distinct layers:

1. <b>Bronze Layer (Landing)</b>: The raw JSON files are ingested directly into the `landing` container in ADLS. This layer serves as the immutable source of truth for the raw data.
2. <b>Silver Layer (Delta)</b>: The raw JSON data is read from the Bronze layer, transformed, and flattened into a set of normalized Delta tables. This layer contains clean, structured data, optimized for downstream processing and analysis. The data is partitioned by `season` to improve query performance.
3. <b>Gold Layer (Future State)</b>: The Silver-level tables can be used to build aggregated, business-ready tables for specific use cases (e.g., player performance metrics, team statistics). This layer is not part of the current notebook but represents the next logical step in the pipeline.

## Ingestion Notebook [(`ingestions.ipynb`)](https://github.com/Kushh37/Cricket-Data-Engineering-Lakehouse/blob/main/ingestions.ipynb)

The core of this project is the `ingestions.ipynb` notebook, which automates the following key steps:

- <b>Secure Connection</b>: Establishes a secure connection to ADLS using an Azure Key Vault-managed Service Principal.
- <b>Schema Definition</b>: Defines a detailed PySpark schema to handle the complex, nested structure of the raw JSON data.
- <b>Data Transformation</b>: Normalizes and flattens the raw data into six clean, relational tables:
  - `match_info`
  - `teams`
  - `players_registry`
  - `innings`
  - `deliveries`
  - `wickets`
- <b>Delta Lake & Unity Catalog</b>: Writes the transformed data to the Delta Lake format and registers the tables in Unity Catalog, making them easily discoverable and queryable via SQL.

## Future Enhancements & Project Roadmap

This project is a continuous work in progress. Future plans include implementing the following advanced data engineering concepts:
- <b>Batch & Streaming Processing</b>:
  - <b>Ingestion with Auto Loader</b>: Use Databricks Auto Loader to incrementally and efficiently process new data files as they arrive in cloud storage.
  - Use Databricks Jobs or Azure Data Factory to schedule notebooks for periodic loads and transformations.
  - Implement MERGE INTO statements for idempotent upserts from the Bronze to Silver layer.
  - Incorporate logic for incremental watermarking, partition pruning, and dedup strategies to optimize performance and handle late-arriving data.
  - <b>Conflict Management</b>: Utilize Delta Lake's isolation levels and row-level concurrency features to handle concurrent writes and avoid conflicts, such as with MERGE statements.
- <b>Performance Optimization</b>:
  - <b>Partition Strategy</b>: Implement a more granular partitioning strategy, such as partition by `season/year` then `format_type` or `ingestion_date`, to optimize time-based queries.
  - <b>File Management</b>: Enable optimized writes and auto-compaction. Schedule `OPTIMIZE ... ZORDER BY (...)` on heavy query paths (e.g., ZORDER on `player_id`, `match_date`) to accelerate player lookups.
- <b>Data Quality & Testing</b>:
  - Add data quality expectations using Delta Expectations or Great Expectations for schema and value checks.
  - Implement unit tests for transformation logic using small sample datasets to ensure predictable and correct results.
- </b>Observability & Monitoring</b>:
  - Set up Databricks job metrics and alerts for failed runs.
  - Utilize Unity Catalog audit logs to track data access and lineage.
  - Forward logs to Azure Monitor / Log Analytics for centralized logging and alerting.
