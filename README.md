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

