# Session 10: Data Warehousing and Business Intelligence

> BITS Pilani — **SSZG507: Modern Database Systems**

---

## Table of Contents

- [10.1 Data Warehouse Fundamentals](#101-data-warehouse-fundamentals)
- [10.2 OLTP vs OLAP](#102-oltp-vs-olap)
- [10.3 Dimensional Modeling](#103-dimensional-modeling)
- [10.4 Fact and Dimension Tables](#104-fact-and-dimension-tables)
- [10.5 Star and Snowflake Schemas](#105-star-and-snowflake-schemas)
- [10.6 OLAP Operations](#106-olap-operations)
- [10.7 ETL and Data Integration](#107-etl-and-data-integration)
- [10.8 Business Intelligence and Analytics](#108-business-intelligence-and-analytics)

---

## 10.1 Data Warehouse Fundamentals

### What is a Data Warehouse?

A **data warehouse** is a centralized repository for integrated, subject-oriented, time-variant, non-volatile historical data supporting analytical decision-making.

**Key characteristics:**

| Characteristic | Description |
|---|---|
| **Integrated** | Data from multiple heterogeneous sources unified |
| **Subject-oriented** | Organized around business subjects (customers, products, sales) |
| **Time-variant** | Retains historical data, not just current state |
| **Non-volatile** | Data is append-only, rarely updated or deleted |
| **Analytical** | Optimized for complex queries, not transactions |

### Data Warehouse vs. Operational Database

| Aspect | Operational DB | Data Warehouse |
|---|---|---|
| **Purpose** | Daily operations | Business analysis |
| **Users** | Clerks, operatives | Analysts, executives |
| **Query Type** | Simple, known | Complex, ad-hoc |
| **Data** | Current state | Historical |
| **Updates** | Frequent | Periodic batch |
| **Optimization** | Write-heavy (OLTP) | Read-heavy (OLAP) |
| **Response Time** | Sub-second (expected) | Minutes/hours (acceptable) |
| **Data Volume** | Gigabytes-Terabytes | Terabytes-Petabytes |

### Data Warehouse Architecture

**Three-tier architecture:**

```
┌─────────────────────────────────────────────────────────┐
│ Presentation Layer (BI Tools, Dashboards, Reports)     │
│ - Tableau, Power BI, Looker                            │
│ - Self-service analytics                               │
└─────────────────────────────────────────────────────────┘
              ↑
              │ (SQL Queries)
              ↓
┌─────────────────────────────────────────────────────────┐
│ OLAP Server / Semantic Layer (Data Models)             │
│ - Analysis Services, Tableau Server                    │
│ - Pre-built metrics and dimensions                     │
└─────────────────────────────────────────────────────────┘
              ↑
              │ (SQL Queries)
              ↓
┌───────────���─────────────────────────────────────────────┐
│ Data Warehouse (Physical Database)                     │
│ - Fact tables, dimension tables                        │
│ - Indexes for OLAP queries                             │
│ - Historical data, aggregations                        │
└─────────────────────────────────────────────────────────┘
              ↑
              │ (ETL/ELT)
              ↓
┌─────────────────────────────────────────────────────────┐
│ Data Sources                                           │
│ - ERP systems, CRM, operational databases              │
│ - External data, logs                                  │
│ - APIs, files, streams                                 │
└─────────────────────────────────────────────────────────┘
```

---

## 10.2 OLTP vs OLAP

### OLTP: Online Transaction Processing

**Purpose:** Handle daily business transactions efficiently

**Characteristics:**
- Short, frequent queries
- Many small writes (INSERT, UPDATE, DELETE)
- Normalized schema (3NF)
- Current data only
- Real-time consistency critical
- Low latency required

**Example queries:**
```sql
-- Retrieve current customer info
SELECT * FROM customers WHERE customer_id = 12345;

-- Process purchase
INSERT INTO orders (customer_id, product_id, quantity, price) 
VALUES (12345, 789, 2, 49.99);
```

**Use cases:**
- E-commerce: order processing, inventory
- Banking: transactions, account management
- ERP: purchase orders, invoices
- CRM: customer interactions

### OLAP: Online Analytical Processing

**Purpose:** Analyze historical data for decision-making

**Characteristics:**
- Complex, infrequent queries
- Many large reads (SELECT with aggregations)
- Denormalized schema (star/snowflake)
- Historical data with time dimension
- Consistency can be eventual
- Latency of seconds-minutes acceptable

**Example queries:**
```sql
-- Analyze sales by region and product
SELECT 
  d_region.region_name,
  d_product.product_category,
  SUM(f_sales.amount) as total_sales,
  COUNT(*) as transaction_count
FROM fact_sales f_sales
JOIN dim_region d_region ON f_sales.region_key = d_region.region_key
JOIN dim_product d_product ON f_sales.product_key = d_product.product_key
WHERE f_sales.year = 2024
GROUP BY d_region.region_name, d_product.product_category;
```

**Use cases:**
- Sales analysis and forecasting
- Customer segmentation
- Financial reporting
- Trend analysis and business intelligence

### Why Not Use Operational DB for Analytics?

**Problems:**
1. **Performance impact** - Heavy queries slow down operations
2. **Schema complexity** - 3NF is hard for analytics
3. **Joins** - Many tables to join, slow aggregations
4. **Historical data** - Operational DB doesn't retain history
5. **Consistency locks** - OLTP locks block slow queries

---

## 10.3 Dimensional Modeling

### Dimensional Modeling Overview

**Dimensional modeling** organizes data around **dimensions** (descriptive) and **facts** (measurable).

**Key concept:** Analyze business events (facts) across various perspectives (dimensions).

### Core Components

**Fact Table:**
- Contains measurable, additive quantities
- Foreign keys to dimension tables
- Typically large (millions-billions of rows)
- Example: Sales transactions, clicks, page views

**Dimension Table:**
- Contains descriptive attributes
- Text, dates, categories
- Typically small (thousands-millions of rows)
- Example: Customers, products, time, locations

**Measure:**
- Numerical values in fact table
- Subject to aggregation (sum, average, count)
- Example: Sales amount, quantity, profit

**Grain:**
- Level of detail in fact table
- Finest level of transaction captured
- Example: One row per sales transaction, per day, per week
- Determines what can be queried accurately

### Example: Retail Sales Warehouse

```
Fact Table: Sales_Fact
├─ transaction_id (PK)
├─ date_key (FK to Date dimension)
├─ customer_key (FK to Customer dimension)
├─ product_key (FK to Product dimension)
├─ store_key (FK to Store dimension)
├─ quantity_sold
├─ unit_price
├─ sales_amount
└─ profit

Dimension Tables:
├─ Date_Dim: date_id, year, month, quarter, day_of_week, ...
├─ Customer_Dim: customer_id, name, segment, region, ...
├─ Product_Dim: product_id, name, category, subcategory, supplier, ...
└─ Store_Dim: store_id, name, location, region, ...
```

### Benefits of Dimensional Modeling

| Benefit | Impact |
|---|---|
| **Understandable** | Business users understand facts and dimensions |
| **Navigable** | Easy to drill-down and explore |
| **Optimal performance** | Dimension joins are fast |
| **Flexible** | Add new dimensions or facts easily |
| **Conformed dimensions** | Reuse across multiple fact tables |

---

## 10.4 Fact and Dimension Tables

### Fact Table Design

**Fact table characteristics:**
- Atomic level (not aggregated)
- Foreign keys to all relevant dimensions
- Additive measures (sum-able)
- Semi-additive measures (sum by some dimensions)
- Non-additive measures (not sum-able)

**Types of measures:**

| Measure Type | Example | Aggregation |
|---|---|---|
| **Additive** | Sales amount | Sum across all dimensions |
| **Semi-additive** | Account balance | Cannot sum across time |
| **Non-additive** | Average price | Use average/distinct aggregation |

**Example fact table:**
```sql
CREATE TABLE sales_fact (
  transaction_id BIGINT PRIMARY KEY,
  date_key INT NOT NULL,          -- FK to date dimension
  customer_key INT NOT NULL,      -- FK to customer dimension
  product_key INT NOT NULL,       -- FK to product dimension
  store_key INT NOT NULL,         -- FK to store dimension
  quantity_sold INT,              -- Additive measure
  unit_price DECIMAL(10,2),       -- Non-additive measure
  sales_amount DECIMAL(12,2),     -- Additive measure
  discount_percent DECIMAL(5,2),  -- Semi-additive
  profit_margin DECIMAL(5,2)      -- Non-additive
);
```

### Dimension Table Design

**Dimension table characteristics:**
- Primary key (usually surrogate key)
- Descriptive attributes (many text columns)
- Slowly changing dimension handling
- Dates for effective periods

**Example dimension table:**
```sql
CREATE TABLE customer_dim (
  customer_key INT PRIMARY KEY,       -- Surrogate key
  customer_id VARCHAR(20),            -- Natural key
  customer_name VARCHAR(100),
  segment VARCHAR(50),                -- Business segment
  region VARCHAR(50),
  country VARCHAR(50),
  phone VARCHAR(20),
  email VARCHAR(100),
  registration_date DATE,
  effective_date DATE,
  end_date DATE,
  is_current BOOLEAN
);
```

### Slowly Changing Dimensions (SCD)

Dimensions change over time (customers move, products renamed, etc.).

**SCD Type 1 - Overwrite:**
```
Customer Address changes: 123 Main St → 456 Oak Ave
Previous value lost, only new address stored
```
**Use:** When history not important

**SCD Type 2 - Add new row:**
```
Customer address changes:
Row 1: customer_key=1, address="123 Main St", effective_date=2020-01-01, end_date=2024-01-15
Row 2: customer_key=1, address="456 Oak Ave", effective_date=2024-01-16, end_date=null
```
**Use:** When history important, enables point-in-time analysis

**SCD Type 3 - Add attribute:**
```
Create columns: current_address, previous_address
customer_key=1, previous_address="123 Main St", current_address="456 Oak Ave"
```
**Use:** When only need current and previous value

---

## 10.5 Star and Snowflake Schemas

### Star Schema

**Star schema** has central fact table directly connected to denormalized dimension tables.

```
                 ┌─── Date_Dim ───┐
                 │                 │
                 ↓                 ↓
      ┌─────────────────────────────────────┐
      │       Sales_Fact_Table              │
      │  (Central fact table)               │
      └─────────────────────────────────────┘
      ↑          ↑          ↑
      │          │          │
      ↓          ↓          ↓
Customer_Dim  Product_Dim  Store_Dim
```

**Characteristics:**
- Dimension tables denormalized (redundant data)
- Simple schema (easy to understand)
- Fewer joins required
- Faster query performance
- Larger storage (denormalization)

**Star schema example:**
```sql
-- Fact table
CREATE TABLE sales_fact (
  fact_id BIGINT PRIMARY KEY,
  date_key INT,
  customer_key INT,
  product_key INT,
  store_key INT,
  quantity INT,
  amount DECIMAL(12,2)
);

-- Denormalized dimension (all attributes in one table)
CREATE TABLE customer_dim (
  customer_key INT PRIMARY KEY,
  customer_id VARCHAR(20),
  name VARCHAR(100),
  segment VARCHAR(50),
  region VARCHAR(50),
  country VARCHAR(50)
);
```

### Snowflake Schema

**Snowflake schema** has central fact table connected to normalized dimension tables (which may further normalize).

```
                 ┌─── Date_Dim ───┐
                 │                 │
                 ↓                 ↓
      ┌─────────────────────────────────────┐
      │       Sales_Fact_Table              │
      └─────────────────────────────────────┘
            ↑          ↑          ↑
            │          │          │
      Region_Dim  Category_Dim  Store_Dim
            ↑          ↑          ↑
            │          │          │
      Country_Dim  Sub_Category_Dim  Location_Dim
```

**Characteristics:**
- Dimension tables normalized (no redundancy)
- More complex schema (more joins)
- More joins needed (slower)
- Smaller storage (normalized)
- Maintains referential integrity

### Star vs. Snowflake Comparison

| Aspect | Star Schema | Snowflake Schema |
|---|---|---|
| **Joins** | Few (dimension is flat) | Many (normalized) |
| **Query Performance** | Faster | Slower |
| **Storage** | More (denormalized) | Less (normalized) |
| **Maintenance** | Simpler | Complex |
| **Understandability** | Easier | Harder |
| **Data redundancy** | Yes | No |
| **Use Case** | Most data warehouses | Complex dimensions |

**Recommendation:** Star schema for most cases; snowflake for very large dimensions with many hierarchies.

---

## 10.6 OLAP Operations

### OLAP Operations Overview

OLAP operations navigate and analyze multidimensional data:

**Core operations:** Roll-up, Drill-down, Slice, Dice, Pivot

### Roll-up (Aggregation)

**Roll-up** moves from detailed to summary level by reducing granularity.

```
Detailed Level: Sales by store, by day
     ↓ (aggregate)
Summary Level: Sales by region, by month
     ↓ (aggregate)
Top Level: Total sales by year
```

**SQL example:**
```sql
-- Detailed
SELECT store, date, SUM(amount)
FROM sales_fact
GROUP BY store, date;

-- Roll-up to region level
SELECT region, DATE_TRUNC('month', date), SUM(amount)
FROM sales_fact
JOIN store_dim ON sales_fact.store_key = store_dim.store_key
GROUP BY region, DATE_TRUNC('month', date);

-- Roll-up to total
SELECT SUM(amount) as total_sales
FROM sales_fact;
```

### Drill-down (Disaggregation)

**Drill-down** moves from summary to detailed level by increasing granularity.

```
Top Level: Total sales
     ↓ (break down)
Region Level: Sales by region
     ↓ (break down)
Store Level: Sales by store
     ↓ (break down)
Transaction Level: Individual sales
```

### Slice

**Slice** selects subset of data along single dimension.

```
All data along all dimensions
     ↓ (slice on date = "2024-01-01")
Data for single date across all other dimensions
```

**SQL example:**
```sql
-- Slice on date
SELECT *
FROM sales_fact
WHERE date_key = 20240101;

-- Slice on region
SELECT *
FROM sales_fact
WHERE store_key IN (
  SELECT store_key FROM store_dim WHERE region = 'North'
);
```

### Dice

**Dice** selects subset across multiple dimensions.

```
All dimensions
     ↓ (dice on region=North, product_category=Electronics, date>2024-01-01)
Subset matching all criteria
```

**SQL example:**
```sql
-- Dice across multiple dimensions
SELECT *
FROM sales_fact f
JOIN store_dim s ON f.store_key = s.store_key
JOIN product_dim p ON f.product_key = p.product_key
WHERE s.region = 'North'
  AND p.category = 'Electronics'
  AND f.date_key >= 20240101;
```

### Pivot (Rotation)

**Pivot** reorients dimensions for different perspectives.

```
Original view:
  Product | 2023 | 2024 | 2025
  --------|------|------|------
  A       | 100  | 150  | 200
  B       | 80   | 120  | 160

Pivoted view:
  Year | Product_A | Product_B
  ----|-----------|----------
  2023| 100       | 80
  2024| 150       | 120
  2025| 200       | 160
```

**SQL example:**
```sql
-- Pivot sales by product and year
SELECT 
  year,
  SUM(CASE WHEN product = 'A' THEN amount ELSE 0 END) as Product_A,
  SUM(CASE WHEN product = 'B' THEN amount ELSE 0 END) as Product_B
FROM sales
GROUP BY year;
```

---

## 10.7 ETL and Data Integration

### ETL Process

**ETL** (Extract, Transform, Load) integrates data from sources to warehouse:

```
┌────────────┐
│  Extract   │  Read from multiple data sources
└────────────┘
       ↓
┌────────────┐
│ Transform  │  Clean, standardize, validate, enrich
└────────────┘
       ↓
┌────────────┐
│   Load     │  Write to data warehouse
└────────────┘
```

### Extract Phase

**Sources:** ERP, CRM, operational databases, files, APIs, web services

**Challenges:**
- Different formats (SQL, CSV, JSON, XML)
- Inconsistent schemas
- Real-time vs. batch extraction
- Incremental vs. full extraction

**Techniques:**
- **Full extract:** Copy entire dataset
- **Incremental extract:** Only changed records (via timestamps, change log)
- **CDC (Change Data Capture):** Capture database changes in real-time

### Transform Phase

**Data cleaning:**
- Remove duplicates
- Fix invalid/null values
- Standardize formats
- Handle missing data

**Data validation:**
- Check business rules
- Validate foreign keys
- Check data types and ranges
- Identify outliers

**Data enrichment:**
- Join with reference data
- Calculate derived fields
- Aggregate or summarize
- Apply business logic

**Example:**
```python
# Clean customer data
def transform_customer(raw_data):
    # Remove duplicates
    data = raw_data.drop_duplicates(subset=['customer_id'])
    
    # Standardize email
    data['email'] = data['email'].str.lower().str.strip()
    
    # Fix null phone numbers
    data['phone'] = data['phone'].fillna('Unknown')
    
    # Create derived field
    data['segment'] = categorize_segment(data['annual_spending'])
    
    return data
```

### Load Phase

**Loading strategies:**
- **Full load:** Replace entire warehouse table
- **Incremental load:** Append or update only changes
- **Merge load:** Update existing, insert new

**Considerations:**
- Performance (batch vs. incremental)
- Consistency (referential integrity)
- Atomicity (all-or-nothing)
- Recoverability (restart capability)

### ELT: Modern Alternative

**ELT** (Extract, Load, Transform) loads raw data first, transforms in warehouse:

```
┌────────────┐
│  Extract   │  Read from data sources
└────────────┘
       ↓
┌────────────┐
│   Load     │  Load raw data to warehouse (fast!)
└────────────┘
       ↓
┌────────────┐
│ Transform  │  Transform using warehouse engine (SQL)
└────────────┘
```

**Advantages:**
- Cloud data warehouses (Snowflake, BigQuery) excel at this
- Avoid intermediate staging servers
- Transformation using warehouse compute
- Simpler architecture (no separate ETL tool)

**When to use ELT:**
- Cloud data warehouse environment
- Data transformation needs warehouse capacity
- Raw data storage acceptable

---

## 10.8 Business Intelligence and Analytics

### Business Intelligence

**Business Intelligence (BI)** uses data warehouse data for decision-making.

**BI components:**
1. **Dashboards** - Real-time visual summaries
2. **Reports** - Detailed formatted data for distribution
3. **Scorecards** - KPI tracking against targets
4. **Ad-hoc queries** - Self-service analytics

### Key Performance Indicators (KPI)

**KPIs** are quantifiable measures of business performance.

**Examples:**
- Sales: Revenue, units sold, average order value
- Customer: Retention rate, acquisition cost, lifetime value
- Operations: On-time delivery, defect rate, throughput
- Finance: Profit margin, ROI, cash flow

**KPI properties:**
- Measurable and quantifiable
- Tied to business objectives
- Tracked over time
- Actionable (can influence result)

### Dashboard and Reporting

**Dashboards:**
- Real-time or near-real-time updates
- Visual format (charts, gauges, tables)
- Interactive drill-down
- Executive summary level

**Reports:**
- Detailed, formatted output
- Scheduled distribution (daily, weekly)
- Detailed metrics and comparisons
- Operational or tactical level

**Tools:** Tableau, Power BI, Looker, QlikView

### Self-service Analytics

**Enabling business users to:**
- Query data themselves
- Create visualizations
- Explore data interactively
- Build custom reports

**Requirements:**
- Simple data models (star schema)
- Pre-calculated common metrics
- Governed semantic layer
- Performance optimization

---

## Summary

Data warehousing and business intelligence provide structured analytical capabilities:

1. **Data warehouse fundamentals** - Integrated, subject-oriented, time-variant data repository
2. **OLTP vs. OLAP** - Different optimization for operational vs. analytical workloads
3. **Dimensional modeling** - Organizing data around facts (measures) and dimensions (descriptors)
4. **Star and snowflake schemas** - Different design approaches with trade-offs
5. **OLAP operations** - Roll-up, drill-down, slice, dice, pivot for analysis
6. **ETL/ELT** - Integrating data from sources to warehouse
7. **Business intelligence** - Converting warehouse data into actionable insights

These concepts enable organizations to harness historical data for strategic decision-making and business optimization.
