# ADR-0001: Target architecture


- **Status:** proposed
- **Date:** 2026-10-05
- **Author:** Carla
- **Lesson:** L01

## Context

Albert's Marketplace currently uses one PostgreSQL database with 25 tables for both operational and analytical workloads.

The web shop writes orders, customers, products and reviews to this database, while analysts query the same database directly. A nightly CSV export also feeds spreadsheets.

This creates several issues. Operational and analytical workloads share the same system, two reports disagree on revenue, and the database also contains sensitive data such as saved cards and passwords.

The platform must support both analytics and machine learning. In particular, the ML team needs units sold per product, per country, per day.

The target architecture therefore needs to separate analytical workloads from the transactional database, support different data types, improve governance and data consistency, and provide datasets that can be consumed by both BI and ML teams.


## Options considered

| Option | For | Against |
|---|---|---|
| Data warehouse | Strong for SQL analytics and BI; good governance; suitable for structured data and dimensional models | Less flexible for semi-structured and unstructured data; less suitable for future ML use cases involving text or images |
| Data lake | Cheap storage; supports structured, semi-structured and unstructured data; flexible for ML | Weaker governance if not managed carefully; risk of becoming a data swamp; less convenient for governed BI metrics |
| Lakehouse | Supports BI and ML on one analytical platform; handles structured, semi-structured and unstructured data; combines low-cost storage with governance and table management | More complex than a simple warehouse; requires strong governance, data quality rules and layer management |


## Criteria

| Criterion | Data warehouse | Data lake | Lakehouse |
|---|---|---|---|
| Workload: BI + ML | + | + | ++ |
| Data types: tables, text, images | - | ++ | ++ |
| Freshness | + | + | + |
| Team and skills | ++ | + | + |
| Cost | + | ++ | + |
| Governance | ++ | - | ++ |

- **Workload:** Albert's Marketplace needs both BI and ML, which strongly favors two-tier and lakehouse architectures.
- **Data types:** the platform must support structured data as well as review text and images, which disadvantages a traditional warehouse.
- **Freshness:** all options can improve on the current nightly CSV export, depending on implementation.
- **Team and skills:** a warehouse is simplest for a SQL-oriented team; a two-tier architecture is the most complex to operate.
- **Cost:** a lake is cheapest for storage, while two-tier requires maintaining two analytical systems.
- **Governance:** warehouse and lakehouse provide stronger support for governed metrics and controlled access than a basic lake.

## Decision

We choose a **lakehouse architecture** with Bronze, Silver and Gold layers.

## Consequences

### Easier

- Analysts no longer need to query the transactional PostgreSQL database directly.
- Business metrics such as revenue can have one agreed definition.
- The ML team can consume prepared datasets at the required grain.
- Historical data can be stored and reprocessed.
- Structured and unstructured data can be handled on the same analytical platform.

### Harder

- The team must build and maintain ingestion and transformation pipelines.
- Data quality rules must be defined, documented and tested.
- The team must manage several data layers instead of one database.

### Things to watch

- Passwords and saved card data must not be exposed in analytical layers.
- Bronze data should not be consumed directly by business users.
- Silver must define one agreed version of each fact.
- Gold tables must be designed for specific analytical or ML use cases.
