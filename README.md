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

