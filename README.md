# Python_and_SQL_Project_SCD_Type_1_2_3

A modular, hands-on Data Engineering lab demonstrating the end-to-end implementation of **Slowly Changing Dimensions (SCD Type 1, Type 2, and Type 3)**. 

This project bridges **Python (Pandas & SQLAlchemy)** and a relational **SQL database** ( MySQL ) to process dimension updates on sample datasets, persist changes to target tables, and output formatted SQL query snapshots demonstrating how each pattern handles data history.

---

## 📌 Architecture & Design Patterns

Dimensions in a Data Warehouse evolve over time. This project isolates each SCD strategy into distinct execution workflows so you can evaluate the behavioral differences side by side:

| SCD Pattern | Strategy | Historical Tracking | Typical Use Case |
| :--- | :--- | :--- | :--- |
| **SCD Type 1** | **Overwrite** | No history preserved | Fixing typos, correcting bad source data, phone number corrections |
| **SCD Type 2** | **Add Row** | Full historical audit trail (Surrogate keys, Start/End dates, Active flags) | Customer address moves, role transitions, regulatory compliance |
| **SCD Type 3** | **Add Column** | Partial history (Current vs. Previous attribute column) | Sales territory realignment, tracking immediate prior status |

---

## 🛠️ Tech Stack & Prerequisites

* **Language:** Python 3.8+
* **Database Driver / ORM:** SQLAlchemy, PyMySQL / Psycopg2 (or built-in `sqlite3`)
* **Data Processing:** Pandas
* **Terminal Formatting:** Jupyter Notebook / SQL Server (for SQL result rendering)

---

## 📂 Repository Structure

```text
scd-dimensional-modeling/
├── config/
├── src/
│   ├── Sql_in_Pandas_SCD_Type1.py         # Dedicated pipeline: SCD Type 1 execution
│   ├── Sql_in_Pandas_SCD_Type2.py         # Dedicated pipeline: SCD Type 2 execution
│   └── Sql_in_Pandas_SCD_Type3.py         # Dedicated pipeline: SCD Type 3 execution
├── data/
│   ├── orders.csv # Baseline dimension snapshot (T0)
│   ├── returns.csv # Baseline dimension snapshot (T1)
│   └── products.csv # Delta updates & changes (T2)
├── sql/                  # SQL runner to execute modules interactively
├── sql_execution_screenshots    # SQL Execution of queries
└── README.md
```
---
## ⚙️ How It Works (Step-by-Step)
## 1. SCD Type 1: In-Place Overwrite
**Logic:** Finds matching business keys (order_id / product_id) and updates existing records with incoming delta values.\
**Result:** Historical data is overwritten. No row additions occur.

---
## 2. SCD Type 2: Row Versioning & Historical Tracking
**Logic:** When an attribute changes:
Closes the currently active record by setting end_date = CURRENT_TIMESTAMP and is_current = FALSE.\
Inserts a new row with a new surrogate key (product_key), start_date = CURRENT_TIMESTAMP, end_date = 31/12/9999, and is_current = TRUE.\
**Result:** Complete point-in-time timeline preservation.

---
## 3. SCD Type 3: Current vs. Previous Column
**Logic:** Dimension table contains current_price and previous_price. When an update arrives:\
The existing value in current_price is copied into previous_price.\
The incoming update is written into current_price.\
**Result:** Retains limited history (only the last two current_price & previous_price).

---
## 💡 Key Takeaways
**SCD Type 1** maximizes storage efficiency and query simplicity at the expense of auditability.

**SCD Type 2** is the standard dimensional modeling technique in enterprise data warehouses (Snowflake, BigQuery, Redshift, Databricks Delta) to support point-in-time reporting and historical analytics.

**SCD Type 3** provides a lightweight middle ground when reporting queries only require comparison between current performance and the immediately preceding status.

---
