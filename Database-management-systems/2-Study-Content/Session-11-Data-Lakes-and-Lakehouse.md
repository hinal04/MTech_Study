# Session 11: Data Lakes and Lakehouse

> BITS Pilani — **SSZG507: Modern Database Systems**

---

## Table of Contents

- [11.1 Data Lake Fundamentals](#111-data-lake-fundamentals)
- [11.2 Data Lake Architecture](#112-data-lake-architecture)
- [11.3 Lakehouse Concepts](#113-lakehouse-concepts)
- [11.4 Delta Lake Technology](#114-delta-lake-technology)
- [11.5 Schema Management](#115-schema-management)
- [11.6 Data Governance](#116-data-governance)
- [11.7 Data Lake Challenges](#117-data-lake-challenges)

---

## 11.1 Data Lake Fundamentals

### What is a Data Lake?

A **data lake** is a large-scale, centralized repository that stores raw, unprocessed data in its native format.

**Key characteristics:**

| Characteristic | Description |
|---|---|
| **Raw data** | Data stored in original format |
| **Any data type** | Structured, semi-structured, unstructured |
| **Any scale** | Petabyte+ capacity |
| **Cost-effective storage** | Cheap object storage (S3, ADLS) |
| **Schema-on-read** | Schemas applied during query time |
| **Flexible retention** | Long-term archival capability |

### Data Lake vs. Data Warehouse

| Aspect | Data Lake | Data Warehouse |
|---|---|---|
| **Data** | Raw, unprocessed | Processed, cleansed |
| **Format** | Any format | Structured tables |
| **Schema** | Schema-on-read | Schema-on-write |
| **Processing** | As-needed | Pre-processed for BI |
| **Users** | Data scientists, engineers | Business analysts, executives |
| **Tools** | Spark, Hadoop, notebooks | BI tools, SQL |
| **Cost** | Cheaper storage | Higher cost (processing) |
| **Flexibility** | Very flexible | Less flexible |
| **Data quality** | Lower | Higher |

### Data Lake Use Cases

1. **Data Exploration** - Scientists explore new datasets
2. **Advanced Analytics** - Machine learning on raw data
3. **Archival** - Long-term storage of all data
4. **Real-time Analytics** - Stream data ingestion
5. **Data Hub** - Central collection point for all enterprise data
6. **IoT Data** - Store sensor data at scale
7. **Log Analytics** - Application and infrastructure logs

---

## 11.2 Data Lake Architecture

### Typical Data Lake Stack

```
┌────────────────────────────────────────────────┐
│ Applications & Analytics Layer                │
│ - Notebooks (Jupyter)                         │
│ - BI Tools (Tableau, Power BI)               │
│ - SQL Engines (Presto, Trino)                │
│ - ML Frameworks (Spark ML, TensorFlow)       │
└────────────────────────────────────────────────┘
              ↓
┌────────────────────────────────────────────────┐
│ Processing & Query Layer                      │
│ - Apache Spark                                │
│ - Apache Hive                                 │
│ - Presto / Trino                             │
│ - Lambda/Kappa Architecture                  │
└────────────────────────────────────────────────┘
              ↓
┌────────────────────────────────────────────────┐
│ Metadata & Governance Layer                   │
│ - Metadata catalog (Hive Metastore)          │
│ - Data lineage tracking                       │
│ - Access control (IAM)                        │
│ - Data quality monitoring                     │
└────────────────────────────────────────────────┘
              ↓
┌────────────────────────────────────────────────┐
│ Storage Layer                                 │
│ - Object Storage (S3, ADLS, GCS)             │
│ - Format: Parquet, Avro, ORC, JSON          │
└────────────────────────────────────────────────┘
              ↓
┌────────────────────────────────────────────────┐
│ Data Sources                                  │
│ - Databases, APIs, files, logs, streams      │
└────────────────────────────────────────────────┘
```

### Storage Organization

**Typical data lake folder structure:**

```
s3://company-data-lake/
├─ /raw/
│  ├─ /customers/        (customer system exports)
│  ├─ /transactions/     (transaction logs)
│  └─ /logs/             (application logs)
├─ /staging/
│  ├─ /processed_customers/
│  └─ /processed_transactions/
├─ /gold/
│  ├─ /customer_analytics/
│  ├─ /sales_summary/
│  └─ /ml_features/
└─ /archive/
   └─ /historical/
```

**Zone descriptions:**

- **Raw/Bronze:** Ingested data in original format
- **Staging/Silver:** Cleaned, deduplicated, partially processed
- **Gold/Refined:** Processed data ready for analytics
- **Archive:** Historical data, rarely accessed

### File Formats

**Columnar Formats (Preferred for analytics):**
- **Parquet:** Compressed, nested data support, wide adoption
- **ORC:** Optimized row columnar, good compression
- **Delta/Iceberg:** Columnar with transaction support

**Row Formats:**
- **CSV:** Simple, universal, inefficient
- **JSON:** Semi-structured, flexible schema
- **Avro:** Binary, schema-evolving

**Columnar advantages:**
- Compression efficiency
- Fast aggregations (column-wise)
- Predicate pushdown optimization
- Lazy loading

---

## 11.3 Lakehouse Concepts

### The Lakehouse Paradigm

A **lakehouse** combines:
- Data lake's flexibility and cost (raw data storage)
- Data warehouse's structure and governance (SQL, ACID)

```
Data Lake (Flexibility, Scale)
         ∪
Data Warehouse (Structure, Governance)
         ↓
    Lakehouse
```

### Lakehouse Characteristics

| Characteristic | Implementation |
|---|---|
| **SQL Queries** | Query raw data directly with SQL |
| **ACID Transactions** | Atomic, consistent, isolated, durable |
| **Schema Enforcement** | Schemas managed by metadata layer |
| **Data Versioning** | Query previous data versions |
| **Time Travel** | AS OF queries for point-in-time |
| **Governance** | Centralized metadata management |
| **Cost Efficiency** | Cheap storage + optimized compute |

### Lakehouse Benefits

1. **Unified data platform** - Single source of truth
2. **Cost efficiency** - Cheap object storage, pay-per-query compute
3. **Query flexibility** - SQL, Spark, Python in same platform
4. **Governance** - Centralized access control, auditing
5. **Data freshness** - Real-time and batch processing
6. **Interoperability** - Works with various tools

### Lakehouse Technologies

**Delta Lake** (Databricks):
```
Delta Lake Layer
    ↓
Apache Spark / Presto
    ↓
S3 / Cloud Storage
```

**Apache Iceberg** (Netflix):
- Table format with ACID transactions
- Snapshots and time-travel queries
- Schema evolution
- Hidden partitioning

**Apache Hudi** (Uber):
- Incremental processing framework
- Upserts and deletes on data lake
- Incremental consumption

---

## 11.4 Delta Lake Technology

### Delta Lake Overview

**Delta Lake** is an open-source format adding ACID transactions to data lakes.

**Built on Parquet** but adds:
- Transaction log
- Schema enforcement
- ACID semantics
- Time-travel queries

### Delta Lake Architecture

```
Parquet Files
    ↓
Delta Transaction Log (_delta_log)
    ↓
Metadata (schema, statistics)
    ↓
Version Control
```

### Key Features

**ACID Transactions:**
```python
# Atomicity - all or nothing
df.write.mode("overwrite").saveAsTable("my_table")  # Atomic

# Consistency - schema preserved
# Isolation - readers unaffected by writers
# Durability - committed data persists
```

**Schema Enforcement:**
```python
# Schema mismatch detected and rejected
df_with_extra_column.write.mode("append").save(delta_table)
# Error: Trying to write extra column
```

**Time Travel:**
```python
# Query previous version
spark.read.format("delta").option("versionAsOf", 3).load(table_path)

# Query as of timestamp
spark.read.format("delta").option("timestampAsOf", "2024-01-01").load(table_path)
```

**Data Versioning:**
```
Version 0: Original data
  ↓ (insert 1000 rows)
Version 1: 1000 rows
  ↓ (update 100 rows)
Version 2: 1000 rows (100 changed)
  ↓ (rollback to v1)
Version 3: 1000 rows (original 100 restored)
```

### Delta Operations

**Merge (Upsert/Delete):**
```python
# Update or insert based on condition
delta_table.alias("target").merge(
    source_df.alias("source"),
    "target.id = source.id"
).whenMatched().update(
    set={"target.value": "source.value"}
).whenNotMatched().insert(
    values={"id": "source.id", "value": "source.value"}
).execute()
```

**Delete:**
```python
# Delete with condition
delta_table.delete("id > 1000")
```

**Update:**
```python
# Update specific columns
delta_table.update(set={"status": "'active'"}, where="age > 30")
```

---

## 11.5 Schema Management

### Schema-on-Read vs. Schema-on-Write

**Schema-on-Write (Traditional warehouse):**
```
Schema Defined
      ↓
Write Data
      ↓
Store (enforced format)
      ↓
Read Data (pre-defined schema)
```

**Schema-on-Read (Data lake):**
```
Write Data (any format)
      ↓
Store (flexible)
      ↓
Schema Applied During Query
      ↓
Read Data (flexible schema)
```

### Schema Evolution

**Problem:** Data format changes over time
- New columns added
- Columns removed
- Data types change
- Nested structures evolve

**Delta Lake support:**

```python
# Schema evolution - append new column
df_new_schema.write.format("delta").mode("append").option("mergeSchema", "true").save(table_path)

# Explicit schema addition
delta_table.alter().add(StructField("new_column", IntegerType()))
```

### Metadata Management

**Hive Metastore** stores table metadata:
- Table name and location
- Column names and types
- Partition information
- File format
- Table statistics

**Benefits:**
- Centralized schema registry
- Interoperability across tools
- Query planning optimization
- Data discovery

---

## 11.6 Data Governance

### Data Catalog

A **data catalog** is centralized registry of all data assets:

**Capabilities:**
- **Asset discovery** - Find datasets by name, owner, tags
- **Metadata** - Column names, types, descriptions
- **Lineage** - Track data origins and transformations
- **Quality metrics** - Row counts, nulls, freshness
- **Ownership** - Who owns, maintains, contacts
- **Tags** - Business glossary mappings

**Tools:** Apache Atlas, Collibra, Alation

### Data Lineage

**Data lineage** tracks data flow through transformations:

```
Customer DB (source)
    ↓ (extract)
Raw customer data (raw zone)
    ↓ (deduplicate, clean)
Clean customer data (silver zone)
    ↓ (aggregate by region)
Regional customer summary (gold zone)
    ↓ (load to dashboard)
Sales dashboard (business user)
```

**Reverse lineage (impact analysis):**
- If customer data schema changes
- Which dashboards affected?
- Which reports break?

### Data Quality

**Data quality dimensions:**
- **Accuracy** - Data correctly represents real-world
- **Completeness** - No missing required data
- **Consistency** - Data same across systems
- **Timeliness** - Data current and fresh
- **Validity** - Data conforms to formats/ranges

**Monitoring:**
- Row counts
- Null percentages
- Value distributions
- Schema changes
- Latency tracking

### Access Control

**Role-based access control (RBAC):**

```
Data Owner
    ↓
Data Steward (maintains quality)
    ↓
Analyst (can query)
    ↓
Consumer (can view reports only)
```

**Column-level security:**
- Some users see salary data
- Others don't (redacted/null)
- Enforced by query engine

---

## 11.7 Data Lake Challenges

### Common Problems

**Data Swamp:**
- Data lake degenerates into disorganized dump
- Undocumented datasets
- Unclear ownership
- Poor data quality
- Difficult to find/use data

**Mitigation:**
- Strong governance from start
- Metadata requirements
- Data quality standards
- Clear naming conventions

**Performance Issues:**
- Large full table scans
- Inefficient query patterns
- Lack of statistics
- No index optimization

**Mitigation:**
- Partitioning strategy
- Columnar formats
- Query result caching
- Statistics collection

**Data Quality:**
- Garbage in, garbage out
- No validation on ingestion
- Duplicate records
- Invalid values

**Mitigation:**
- Data validation framework
- Duplicate detection/removal
- Schema validation
- Quality metrics tracking

### Data Lake Security

**Challenges:**
- Vast amounts of sensitive data
- Multiple access patterns (SQL, Spark, Python)
- Complex permission management
- Audit trail requirements

**Solutions:**
- Encryption at rest and in transit
- Row/column-level security
- Access logging
- Data masking (redaction)
- Identity management (IAM)

---

## Summary

Data lakes and lakehouses represent the evolution of data management:

1. **Data lake fundamentals** - Raw, flexible, cost-effective data storage at scale
2. **Data lake architecture** - Layered approach with raw, staging, and gold zones
3. **Lakehouse paradigm** - Combining lake flexibility with warehouse governance
4. **Delta Lake technology** - Adding ACID transactions and versioning to object storage
5. **Schema management** - Schema-on-read flexibility with schema evolution support
6. **Data governance** - Catalogs, lineage, quality, and access control
7. **Challenge mitigation** - Avoiding data swamps and ensuring quality

Modern data platforms increasingly adopt lakehouse architecture for flexibility, scalability, and cost-efficiency while maintaining governance and quality standards.
