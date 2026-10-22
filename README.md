# Data-modeling-
##  OLTP vs OLAP
### What is OLTP?
Definition: Online Transaction Processing — designed for fast, small, frequent transactions (insert, update, delete).

Examples: Banking systems, airline booking, e‑commerce checkout.
### What is OLAP?
Definition: Online Analytical Processing — designed for complex queries and analytics on large historical datasets.

Examples: BI dashboards, sales forecasting, trend analysis.

### Why We Use Them
OLTP → Ensures reliable, fast transaction processing.

OLAP → Enables decision‑making through analytics.

### When to Use Them
Use OLTP → When building operational systems (banking, retail, reservations).

Use OLAP → When building reporting/analytics systems (dashboards, BI, forecasting).

| Aspect | OLTP | OLAP |
| --- | --- | --- |
| **Purpose** | Run daily operations | Analyze data for insights |
| **Data Size** | Small, frequent | Large, historical |
| **Schema** | Normalized | Denormalized (Star/Snowflake) |
| **Users** | Thousands concurrent | Analysts, managers |
| **Query Type** | Simple inserts/updates | Complex joins, aggregations |

## n Conceptual, Logical, and Physical Data Models

### Conceptual Data Model
What: High‑level view of business entities and their relationships (e.g., Customer, Product, Order).

Why: Helps business stakeholders and IT teams align on scope without technical details.

When to Use: At the requirement gathering stage — workshops, business discussions, early design.
👉 Interview Point: “Conceptual model is for business understanding — no attributes, just entities and relationships.”

### Logical Data Model
What: Adds attributes, primary keys, foreign keys, and relationships — but still DB‑agnostic.

Why: Acts as a blueprint for developers, ensures clarity before choosing technology.

When to Use: During design phase — before deciding on database platform.
👉 Interview Point: “Logical model is the detailed design — attributes, keys, relationships — but not tied to any DB.”

### Physical Data Model
What: Actual implementation in a database — tables, columns, datatypes, indexes, constraints.

Why: Converts design into working database objects.

When to Use: At the deployment stage — when building the database in SQL Server, Snowflake, Databricks, etc.
| Aspect | Conceptual | Logical | Physical |
| --- | --- | --- | --- |
| **Focus** | Business entities | Attributes & relationships | Tables, columns, datatypes |
| **Audience** | Business stakeholders | Developers, architects | DBAs, engineers |
| **Detail Level** | High‑level | Medium | Full technical |
| **Use Case** | Requirement workshops | Design reviews | Implementation |

### Real‑Time Example
Scenario: E‑commerce system

Conceptual → Entities: Customer, Product, Order.

Logical → Attributes: Customer_ID, Name, Product_ID, Price, Order_ID, Date. Keys & relationships defined.

Physical → SQL tables with datatypes (VARCHAR, INT, DATE), indexes on Order_ID, partitions by Order_Date.

## Slowly Changing Dimensions (SCD Types 0, 1, 2, 3) 

### SCD Type 0
What: No changes tracked — once a value is stored, it never changes.

Why: Keeps static attributes (e.g., Date of Birth).

When to Use: Rare, only for values that must remain constant.
👉 Interview Point: “SCD 0 is for fixed attributes that never change.”

### SCD Type 1
What: Overwrites old value with new value.

Why: Keeps only the latest information, no history.

When to Use: Correcting errors or when history is not important (e.g., correcting spelling of a customer name).
👉 Interview Point: “SCD 1 is for keeping only the latest value — no history.”

### SCD Type 2
What: Adds a new row with versioning (start_date, end_date, current_flag).

Why: Preserves full history of changes.

When to Use: Most common in data warehouses — e.g., tracking customer address changes over time.
👉 Interview Point: “SCD 2 is used when historical tracking is required — it’s the most widely used type.”

### SCD Type 3
What: Adds a new column to store previous value alongside current value.

Why: Tracks limited history (current + one prior).

When to Use: When only the immediate past value is needed (e.g., tracking current department and previous department of an employee).
👉 Interview Point: “SCD 3 is for limited history — current and one prior value.”

| Type | How It Works | Why | When to Use |
| --- | --- | --- | --- |
| **SCD 0** | No change | Keep static values | Rare, fixed attributes |
| **SCD 1** | Overwrite | Latest only | Correcting errors |
| **SCD 2** | New row with version/date | Full history | Customer address changes |
| **SCD 3** | New column for prior value | Limited history | Track current + one prior |

### Real‑Time Example (Customer Address)
SCD 0 → Address never changes.

SCD 1 → Old address overwritten by new one.

SCD 2 → New row added with start/end dates for each address.

SCD 3 → Current address + one previous address stored in columns.

##  Star Schema vs Snowflake Schema 

### Star Schema
What: Central fact table (measures like sales, revenue) connected directly to denormalized dimension tables (Customer, Product, Date).

Why: Simple design, fewer joins, faster query performance.

When to Use:

Reporting dashboards (Power BI, Tableau).

When query speed is more important than storage.
👉 Interview Point: “Star schema is denormalized, easy to query, and best for performance in BI tools.”

### Snowflake Schema
What: Central fact table connected to normalized dimension tables (dimensions split into sub‑tables).

Why: Saves storage, reduces redundancy, supports complex hierarchies.

When to Use:

Large dimensions with hierarchical data (e.g., Geography → Country → State → City).

When storage optimization or data integrity is critical.
👉 Interview Point: “Snowflake schema normalizes dimensions, saving space but requiring more joins.”

| Aspect | **Star Schema** | **Snowflake Schema** |
| --- | --- | --- |
| **Design** | Fact + flat dimensions | Fact + normalized dimensions |
| **Performance** | Faster (fewer joins) | Slower (more joins) |
| **Storage** | More redundancy | Less redundancy |
| **Complexity** | Simple | More complex |
| **Use Case** | BI dashboards, quick analytics | Large hierarchical dimensions |

### Real‑Time Example
Sales Data Warehouse

Star Schema → Fact_Sales linked directly to Dimension_Customer, Dimension_Product, Dimension_Date.

Snowflake Schema → Fact_Sales linked to Dimension_Customer, which further links to Sub_Dimension_Geography (Country → State → City).

## Datamart vs Dimensions
### Datamart
What: A subject‑specific subset of a data warehouse (e.g., Sales Mart, Finance Mart).

Why: Provides a curated view for a particular department or business unit.
 
When to Use:

Departmental analytics (Sales team, HR team).

When users don’t need the entire warehouse, only their slice.
👉 Interview Point: “Datamart is a mini‑warehouse focused on one subject area, making analytics faster and simpler for teams.”

### Dimension
What: A descriptive table that provides context to facts (e.g., Customer, Product, Date).

Why: Enables slicing, filtering, and grouping of fact data.

When to Use:

Always in dimensional modeling.

Whenever you need descriptive attributes to analyze facts.
👉 Interview Point: “Dimension is a context table inside schema — it describes facts like who, what, when, where.”

| Aspect | **Datamart** | **Dimension** |
| --- | --- | --- |
| **Definition** | Subset of warehouse | Context table in schema |
| **Scope** | Department‑level (Sales, HR, Finance) | Attribute‑level (Customer, Product, Date) |
| **Purpose** | Curated analytics for teams | Adds descriptive context to facts |
| **Use Case** | Department dashboards | Filtering, grouping, slicing facts |

### Real‑Time Example
E‑commerce Warehouse

Datamart → Sales Mart (only sales data for sales team).

Dimension → Customer Dimension (customer details), Product Dimension (product details), Date Dimension (calendar).

👉 Datamart = slice of warehouse for a team.
👉 Dimension = context table inside schema.

## Primary Key, Foreign Key, Business Key, Hash Key, and Surrogate Key

### Primary Key
What: Unique identifier for each row in a table.

Why: Ensures entity integrity (no duplicates, no NULLs).
When to Use: Always in OLTP systems; optional in OLAP (fact tables often don’t enforce PKs).

**Snowflake**
~~~~~
CREATE TABLE CUSTOMER (
  CUSTOMER_ID INT PRIMARY KEY,
  NAME STRING
);
~~~~~

**Databrick**

~~~~~
CREATE TABLE CUSTOMER (
  CUSTOMER_ID INT,
  NAME STRING,
  CONSTRAINT pk_customer PRIMARY KEY (CUSTOMER_ID)
);
~~~~~
### Foreign Key
What: Column referencing a primary key in another table.

Why: Enforces referential integrity.

When to Use: OLTP systems; in OLAP often logical only (not enforced physically).

**Snowflake**
~~~~~
CREATE TABLE ORDERS (
  ORDER_ID INT PRIMARY KEY,
  CUSTOMER_ID INT REFERENCES CUSTOMER(CUSTOMER_ID)
);
~~~~~
**Databricks Unity Catalog**
~~~~
CREATE TABLE ORDERS (
  ORDER_ID INT,
  CUSTOMER_ID INT,
  CONSTRAINT fk_customer FOREIGN KEY (CUSTOMER_ID) REFERENCES CUSTOMER(CUSTOMER_ID)
);
~~~~~
### Business Key
What: Natural key from business data (e.g., Customer Email, SSN).

Why: Represents real‑world identity.

When to Use: For matching records across systems.

Snowflake/Databricks: Usually stored as a column, not enforced as PK.

👉 Interview Point: “Business keys come from the business domain, but may change or be duplicated, so we often replace them with surrogate keys in warehouses.”

### Surrogate Key
What: Artificial key (usually auto‑increment or UUID).

Why: Stable, unique, independent of business changes.

When to Use: In dimensional modeling (fact → dimension joins).

**Snowflake**
~~~~
CREATE TABLE CUSTOMER_DIM (
  CUSTOMER_SK INT AUTOINCREMENT PRIMARY KEY,
  CUSTOMER_BK STRING
);
~~~~
**Databricks Unity Catalog**
~~~~~
CREATE TABLE CUSTOMER_DIM (
  CUSTOMER_SK BIGINT GENERATED ALWAYS AS IDENTITY,
  CUSTOMER_BK STRING
);
~~~~~
### Hash Key
What: Key generated by hashing one or more columns (e.g., MD5, SHA).

Why: Ensures uniqueness across composite attributes, useful for SCD Type 2.

When to Use:

Detecting changes in dimension attributes.

Creating distributed joins in big data systems.

**Snowflake**
~~~~
SELECT MD5(CONCAT(CUSTOMER_ID, EMAIL, ADDRESS)) AS HASH_KEY FROM CUSTOMER;
~~~~
**Databricks**
~~~~~
SELECT sha2(concat(CUSTOMER_ID, EMAIL, ADDRESS), 256) AS HASH_KEY FROM CUSTOMER;
~~~~~~
| Key Type | Definition | Why | When to Use |
| --- | --- | --- | --- |
| **Primary Key** | Unique row identifier | Entity integrity | OLTP, sometimes OLAP |
| **Foreign Key** | References PK in another table | Referential integrity | OLTP, logical in OLAP |
| **Business Key** | Natural business identifier | Real‑world identity | Matching across systems |
| **Surrogate Key** | Artificial, stable identifier | Avoid business key changes | Dimensional modeling |
| **Hash Key** | Generated via hash function | Detect changes, composite uniqueness | SCD Type 2, big data joins |

# Databrick
### Delta Lake in Databricks
Definition: Delta Lake is a storage layer built on top of Parquet with ACID transactions.

Key Interview Point: “Delta Lake adds reliability to data lakes by enabling ACID, schema enforcement, and time travel.”

### OPTIMIZE vs ZORDER
OPTIMIZE → Compacts small files into larger ones → faster queries.

ZORDER → Clusters data by column values during OPTIMIZE.

Dependency: ZORDER requires OPTIMIZE; without OPTIMIZE, ZORDER does not work.

Disadvantages: Expensive, not incremental, skewed data reduces effectiveness.

Interview One‑liner: “OPTIMIZE reduces small files; ZORDER improves multi‑column filtering but is costly.”

### Liquid Clustering
Definition: Dynamic clustering that adapts automatically as data evolves.

Why required: Handles skew better, reduces manual maintenance, replaces ZORDER.

Interview One‑liner: “Liquid clustering is self‑adjusting and modern, unlike static ZORDER.”

### Versioning & Recovery
Transaction Log → Every commit creates a new version.

**Time Travel:**
~~~~~
SELECT * FROM sales VERSION AS OF 25;
~~~~~
**Restore:**
~~~~~
RESTORE TABLE sales TO VERSION AS OF 25;
~~~~~

Default retention: 30 days (configurable).

Interview Point: “Delta Lake supports time travel and restore via its transaction log.”

### View vs MView vs Volume vs Foreign Table

### View
Definition: Logical layer, no storage, always queries base tables.

~~~~~
CREATE OR REPLACE VIEW sales_view AS
SELECT customer_id, SUM(amount) AS total_sales
FROM sales
GROUP BY customer_id;
~~~~~
Interview Point: “Views are logical, lightweight, and always query the base table.”

### Materialized View (MView)
Definition: Stores precomputed results for faster queries.

~~~~~~
CREATE MATERIALIZED VIEW sales_mview
AS SELECT customer_id, SUM(amount) AS total_sales
FROM sales
GROUP BY customer_id;
~~~~~~
Refresh:

~~~~~~
REFRESH MATERIALIZED VIEW sales_mview;
~~~~~~
Interview Point: “MViews trade storage for speed; they need refresh.”

### Volume
Definition: Managed storage in Unity Catalog for raw files (CSV, Parquet, JSON).

~~~~~
CREATE VOLUME my_volume;
~~~~~
Files can be uploaded into this volume and accessed via SQL or notebooks.

Interview Point: “Volumes are Databricks‑managed storage for raw files.”

### Foreign Table
Definition: References external data sources (S3, ADLS, etc.) without copying data.

~~~~~
CREATE FOREIGN TABLE ext_sales
USING CSV OPTIONS ('path'='/mnt/s3/sales.csv');
~~~~~
Interview Point: “Foreign Tables connect external data lakes directly.”

**Quick Interview Recall**

View → Logical, no storage.

MView → Precomputed, faster, needs refresh.

Volume → Managed storage for raw files.

Foreign Table → External data reference, no copy.
### Internal Table
Definition: A table fully managed by Databricks (Unity Catalog).

Storage: Data + metadata stored inside Databricks‑managed location.

Lifecycle: Dropping the table also deletes the underlying data.

Use Case: Best for new projects where Databricks should control governance, security, and lifecycle.

Interview One‑liner: “Internal tables are Databricks‑managed — simple governance, but less flexible.”

### External Table
Definition: A table that references data stored outside Databricks (e.g., S3, ADLS, GCS).

Storage: Metadata in Databricks, but data files remain in external storage.

Lifecycle: Dropping the table does not delete the underlying data.

Use Case: Best when integrating with existing data lakes or sharing data across multiple platforms.

Interview One‑liner: “External tables give flexibility — you manage the data lifecycle outside Databricks.”
| Feature | **Internal Table** | **External Table** |
| --- | --- | --- |
| Storage | Databricks‑managed | External (S3, ADLS, GCS) |
| Lifecycle | Drop deletes data | Drop keeps data |
| Governance | Simplified, Databricks controls | Flexible, user controls |
| Best Use | New projects, full governance | Existing lakes, cross‑platform sharing |

### ADF 

## Exception Handling in Pipeline

"In ADF, I implemented exception handling using Retry Policies and On-Failure dependencies.

For transient failures such as temporary network issues or source system unavailability, I configured activity-level retry policies with 3 retries and a 30-second interval.

For permanent failures, I used the On-Failure path. If a Copy Activity or Databricks activity failed even after retries, the pipeline redirected execution to an error-handling workflow. In that workflow, I logged the pipeline name, activity name, error message, and timestamp into an audit table and triggered email notifications to the support team.

## Ingest Multiple JSON Files → Snowflake
Ingest files via Copy Activity with wildcard paths in ADLS.

Transform schema differences in Databricks notebooks.

Load results into Snowflake tables via JDBC/Snowpipe.

Orchestrate with ADF pipeline.
## Large‑Volume Data Ingestion (2 TB Oracle → Azure SQL)
Extract data with partitioned copy in ADF.

Enable parallelism and compress files.

Load into Azure SQL using PolyBase/Bulk Insert.

Monitor IR and DB performance.
## Python Modules Used
pandas → data manipulation.

pyodbc/snowflake‑connector → DB connectivity.

logging → error handling.

os/sys → file operations.

json → semi‑structured parsing.

## Security for External Sources

Connect via Self‑Hosted IR.

Store credentials in Azure Key Vault.

Use TLS/SSL encryption.

Apply RBAC in Snowflake.

## Connection Passes Test but Fails at Runtime
Check firewall/network rules.

Verify IR availability.

Validate authentication tokens.

Inspect schema mapping.
## Migrating Teradata Views → Azure SQL
Extract Teradata view definitions.

Convert SQL syntax to T‑SQL.

Migrate base tables via Copy Activity.

Recreate views with Stored Procedure Activity.
## Execute 9 Stored Procedures

Use Stored Procedure Activity in ADF.

Chain sequentially with success dependencies.

Or loop with ForEach activity.

## Multi‑Format Ingestion (CSV, JSON, Parquet)

Ingest via Copy Activity into ADLS.

Transform in Databricks notebooks (PySpark).

Load into Snowflake/Azure SQL via JDBC.

Orchestrate with ADF pipeline.

## Pipeline Fails Intermittently (On‑Prem DB)

Check network stability.

Validate IR health.

Add retry policies.

Monitor logs for transient errors.

## Medallion Architecture
Bronze → raw data.

Silver → cleaned/standardized.

Gold → curated/aggregated.\

## Azure SQL → Snowflake Load

Source: Azure SQL via Linked Service.

Sink: Snowflake via JDBC/Snowpipe.

Optionally stage in ADLS.

## Pipeline Debug (2 min → 20 min)

Check source/target DB performance.

Inspect IR health.

Review query optimization.

Monitor data volume changes.
## Dynamic Tables in Snowflake

Define incrementally refreshed tables.

Auto‑update with refresh schedules.

## Blob → Snowflake Confirmed Tables (E2E)

Ingest raw files into Snowflake staging.

Transform via Bronze → Silver → Gold layers.

Load into confirmed fact/dimension tables.

## Snowflake Tasks

Create scheduled SQL jobs.

Automate incremental loads and transformations.

## Schema Drift (Changing Column Names)

Ingest into staging tables with generic schema.

Apply dynamic mapping in ADF/Databricks.

## CTE vs Subquery
CTE → reusable, improves readability.

Subquery → inline, less reusable.

## SCD Type 2 Transaction
Insert new row for change.

Close old row with End_Date.

Mark active row with Is_Current.
## Deduplication in Snowflake
Use ROW_NUMBER() OVER (PARTITION BY … ORDER BY …).

Keep first occurrence, remove duplicates.

## SP Failure & Audit Logging
Log SP name, timestamp, error message into audit table.

Use ADF on‑failure path to insert logs.
## Git Merge Safety

Pull latest branch before merge.

Run diff checks.

Ensure only your changes are staged.
## Query Optimization

Add clustering keys.

Use result cache.

Avoid SELECT *.

Break queries into smaller steps.

## User Story Dependency & Deadlines

Communicate dependency early.

Parallelize other tasks.

Stretch if necessary to meet deadlines
## Critical Deployment Under Pressure
Prioritize essential changes.

Prepare rollback plan.

Collaborate closely with team.

Monitor post‑deployment.

### ADF Trigger Types
Schedule Trigger → Runs at fixed times (daily/hourly).

Event Trigger → Runs when a file is created/deleted in storage.

Tumbling Window Trigger → Runs for fixed time windows (time‑partitioned data).

Manual Trigger → Developer clicks Trigger Now.
***These are pipeline orchestration triggers — they decide when ADF pipelines run.***
## Spark Streaming Trigger Levels

| Trigger | Description | Default? | Use Case |
| --- | --- | --- | --- |
| **Default (Micro‑batch)** | Processes new data ASAP | ✅ Yes | Most production pipelines |
| **processingTime** | Runs at fixed intervals (e.g., every 30s, 1 min) | ❌ No | Control compute cost |
| **availableNow** | Processes all available data once, then stops | ❌ No | Incremental batch loads |
| **continuous** | Low‑latency continuous processing | ❌ No | Fraud detection, IoT |

## Difference: continuous vs processingTime

~~~~~
df.writeStream.trigger(processingTime="30 seconds").start()
~~~~~
Runs a micro‑batch every 30 seconds.

The "30 seconds" value = processing interval.
~~~~
df.writeStream.trigger(continuous="30 seconds").start()
~~~~~
Runs in true continuous mode (low latency).

The "30 seconds" value = checkpoint interval, not processing interval.
## Production Scenarios
**Directory Listing + Default (Micro‑batch)**
~~~~
spark.readStream \
  .format("cloudFiles") \
  .option("cloudFiles.format","csv") \
  .option("cloudFiles.useNotifications","false") \
  .load("/input") \
  .writeStream \
  .start()
~~~~~~
scans the folder continuously and immediately processes new files in micro‑batches ,the default Auto Loader behavior, mainly suited for small projects or testing.”

** Directory Listing + processingTime**
~~~~
df.writeStream \
  .trigger(processingTime="1 minute") \
  .start()
~~~~
Work → Scans folder every 1 minute → processes files in batches.

Single Line → “Processes files in fixed intervals 

**Directory Listing + availableNow**
~~~~~
df.writeStream \
  .trigger(availableNow=True) \
  .start()
~~~~~
Work → Scans folder once → processes all files → stops automatically.

**Directory Listing + Continuous**
~~~~~
df.writeStream \
  .trigger(continuous="1 second") \
  .start()
~~~~~~
Work → Scans folder continuously → processes files instantly → keeps running.

**File Notification + Default**
~~~~
df.writeStream \
  .option("cloudFiles.useNotifications","true") \
  .start()
~~~~~
Work → Event Grid notification → Auto Loader starts micro‑batch immediately.
**File Notification + processingTime**
~~~~~
df.writeStream \
  .option("cloudFiles.useNotifications","true") \
  .trigger(processingTime="1 minute") \
  .start()
~~~~~
Work → Collects notifications → processes every X minutes.

**File Notification + availableNow**
~~~~~
df.writeStream \
  .option("cloudFiles.useNotifications","true") \
  .trigger(availableNow=True) \
  .start()
~~~~~
Work → Processes all pending notifications once → stops.
**File Notification + Continuous**
~~~~~
df.writeStream \
  .option("cloudFiles.useNotifications","true") \
  .trigger(continuous="1 second") \
  .start()
~~~~~~
Work → Event notification → file processed almost instantly → pipeline keeps running.
| **File Detection** | **Trigger** | **How it works** | **Production Use** |
| --- | --- | --- | --- |
| **Directory Listing** | Default | *Scans folder continuously → processes new files immediately* | Small projects |
| **Directory Listing** | processingTime | *Scans every X minutes → processes batch* | Medium workloads / cost control |
| **Directory Listing** | availableNow | *Scans once → processes all files → stops* | Initial loads, daily batch, migrations |
| **Directory Listing** | Continuous | *Scans nonstop → processes instantly* | Rare (latency due to scanning) |
| **File Notification** | Default | *Event received → micro‑batch starts immediately* | ⭐ Most common (80–90% production) |
| **File Notification** | processingTime | *Collects events → processes every X minutes* | Cost optimization |
| **File Notification** | availableNow | *Processes all pending notifications once → stops* | Incremental batch loads |
| **File Notification** | Continuous | *Event → processed almost instantly* | Ultra‑low latency (IoT, fraud detection, trading) |

## Lakeflow Pipeline Modes

Formerly Delta Live Tables (DLT).

They do not use Spark Structured Streaming triggers (processingTime, continuous, etc.).

Instead, they rely on pipeline execution modes managed by Databricks.

| **Mode** | **How it Works** | **Production Use** |
| --- | --- | --- |
| **Manual Run** | User clicks Run in Databricks UI or via API/CLI → executes once | Development, unit testing, schema validation |
| **Scheduled Run** | Runs automatically on a cron schedule (hourly, daily, weekly) | Batch ETL, reporting, warehouse refresh |
| **Continuous Mode** | Pipeline stays running and continuously processes new data | ⭐ Most common → Real‑time streaming (Kafka, Event Hub, Auto Loader, IoT, CDC) |

Example Architectures
Kafka → Lakeflow Pipeline (Continuous) → Bronze → Silver → Gold → Power BI

ADLS → Auto Loader → Lakeflow Pipeline (Continuous) → Bronze → Silver → Gold

## Production Combinations
| **Source** | **File Detection** | **SDP Pipeline Mode** | **Recommended Use** |
| --- | --- | --- | --- |
| **ADLS** | **File Notification** | **Continuous** | ⭐⭐⭐⭐⭐ Most common |
| **ADLS** | File Notification | Scheduled | Daily/hourly incremental loads |
| **ADLS** | Directory Listing | Scheduled | Small/medium projects |
| **Kafka** | Streaming | Continuous | ⭐⭐⭐⭐⭐ |
| **Event Hub** | Streaming | Continuous | ⭐⭐⭐⭐⭐ |

 Why File Notification + Continuous is Preferred
No folder scanning → avoids repeated directory lookups.

Low latency → files processed immediately after arrival.

Lower storage API costs → fewer calls to ADLS.

Automatic file processing → event‑driven, reliable, scalable.

🔹 Banking Production Example
Oracle CDC → ADLS → Event Grid Notification → Queue → Auto Loader → Lakeflow Pipeline (Continuous) → Bronze → Silver → Gold → Power BI

👉 Why this works best:

Oracle CDC captures changes.

Event Grid pushes notifications to Auto Loader.

Lakeflow Pipeline (Continuous) processes instantly.

Bronze → Silver → Gold layers ensure clean, curated data.

Power BI consumes governed data for reporting.

## How Lakeflow Pipeline Compute is Created
You do not create clusters manually.

Instead, Databricks provisions compute automatically when you define a pipeline.

Steps:

Go to Workspace → Pipelines → Create Pipeline.

Provide details:

Pipeline Name → e.g., customer_pipeline.

Notebook → e.g., customer_pipeline.py.

Target Catalog → e.g., main.

Target Schema → e.g., bronze.

Mode → Triggered (batch) or Continuous (streaming).

Compute → Managed by Databricks.

Click Create → Databricks provisions compute automatically.

### query level turning 


These are optimizations applied directly at the query level to reduce shuffle, skew, and unnecessary computation:

**Broadcast Join** 
Send small DataFrame to all executors to avoid shuffle.
~~~~~~
large_df.join(broadcast(small_df), "id")
~~~~~~
**Repartition Before Join** 
Ensures same join keys go to same partitions.
~~~~~
df1.repartition("id").join(df2.repartition("id"), "id")
~~~~~
**Filter Before Join**
Reduce data size early.
~~~~~~
df1.filter(col("status")=="active").join(df2,"id")
~~~~~~
**Bucketing Optimization**
Pre‑bucket data to reduce shuffle.
~~~~~
df.write.bucketBy(8,"id").saveAsTable("emp_bucket")
~~~~~
**Select Required Columns** 
Avoid moving unnecessary data.
~~~~~
df1.select("id","name").join(df2.select("id","dept"),"id")
~~~~~~
**Handle Skew Joins** 
Adaptive Query Execution (AQE) splits skewed partitions.
~~~~~~
spark.conf.set("spark.sql.adaptive.skewJoin.enabled","true")
~~~~~~~
**Cache Frequently Used DataFrames → Prevent recomputation.**

~~~~
df.cache()
~~~~
Semi Join Instead of Inner Join → Faster existence checks.

Join Order (Small Table First) → Broadcast small table to reduce shuffle.

## Performance‑Level Tuning in PySpark
These improve overall job execution and resource usage:

Partition Pruning & Column Pruning → Read only needed partitions/columns.

Predicate Pushdown → Push filters to data source (Parquet, Delta).

Caching/Persisting → Reuse intermediate results.

Repartition vs Coalesce → Control parallelism.

Adaptive Query Execution (AQE) → Dynamic optimization at runtime.

Shuffle Partitions → Tune partition count to avoid small files.

Spill Handling → Manage memory spills to disk.

Skew Handling → Detect and rebalance skewed keys.

Explain Plan Reading → Use df.explain(True) to identify expensive shuffles, joins, scans.

## Delta Lake Optimization
These focus on storage‑level and metadata optimizations:

OPTIMIZE → Merge small files into larger ones.

ZORDER BY → Co‑locate related data for faster filtering.

Liquid Clustering → Incremental clustering using metadata.

Data Skipping → Skip irrelevant files using min/max metadata.

Partitioning → Organize data by low‑cardinality columns.

VACUUM → Remove obsolete files, free storage.

Optimize Writes → Produce well‑sized output files.

Auto Compaction → Merge small files automatically after writes.

**Most Asked Interview Scenario
Q: “How do you optimize slow queries?”  
A: “I analyze execution plans to identify shuffles, skew joins, sort merge joins, full scans, and missing predicate pushdown or partition pruning.”**

| **Optimization** | **Execution Plan Keyword** | **Interview Point** |
| --- | --- | --- |
| **Broadcast Join** | ``BroadcastHashJoin`` | *Small table broadcast → avoids shuffle.* |
| **Sort Merge Join** | ``SortMergeJoin``, ``Exchange``, ``Sort`` | *Used for large joins → expensive shuffle.* |
| **Predicate Pushdown** | ``PushedFilters:`` | *Filter pushed to source → reduces scan size.* |
| **Partition Pruning** | ``PartitionFilters:`` | *Reads only relevant partitions → faster scans.* |
| **Window Functions** | ``Window``, ``Sort``, ``Exchange`` | *Expensive → optimize by reducing sort/shuffle.* |


## Debugging Slow Spark Jobs
When a Spark job runs slow, the first step is to read the execution plan:

Expensive shuffles → Large data movement across nodes.

Skew joins → One partition has too many rows.

Sort Merge Joins → Heavy shuffle + sort operations.

Partition pruning → If missing, Spark scans unnecessary partitions.

Broadcast joins → If not happening, small tables may be shuffled unnecessarily.

Predicate pushdown → Ensure filters are pushed to the source (Parquet/Delta).

~~
df.explain(True)
~
| **Command** | **Purpose** |
| --- | --- |
| ``ANALYZE ``TABLE`` | Compute stats |
| ``DESCRIBE ``EXTENDED`` | View stats |
| ``df.explain(True)`` | Optimizer plan |

Spark optimizer may choose SortMergeJoin instead of BroadcastHashJoin if statistics are missing.
Fix: Compute table statistics.

**Enterprise Problem
15 TB Delta table
5 million small files (classic small-file problem)
Queries taking 12 minutes
Heavy filtering on customer_id**

Optimization Solutions
Enable Optimize Write

Produces better-sized output files during writes.

Prevents creation of many tiny files.

Enable Auto Compaction

Automatically merges small files after writes.

Run OPTIMIZE

Actively compacts small files into larger ones.

Liquid Clustering (on customer_id, order_date)

Incremental clustering for new changes.

Improves query performance for frequently filtered columns.

Schedule VACUUM weekly

Removes obsolete files no longer referenced in the Delta log.

Saves storage space.

Enable Photon on SQL Warehouse

Vectorized query engine → faster execution.

Partitioning (only for low-cardinality columns like order_date)

Avoids over-partitioning.

Helps with partition pruning when filters are frequent.
**Results
File count reduced dramatically.
Data skipping improves (thanks to metadata min/max).
Query latency drops from 12 minutes → seconds.**



