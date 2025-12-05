# Database Queries

This folder contains a Jupyter notebook that connects to a **PostgreSQL** database (hosted on Neon) and uses **SQL + Python (pandas)** to analyze NYC school data in the `nyc_schools` schema.

The focus of this component is to answer a set of analytical questions by joining multiple tables and running SQL queries from Python.

---

## 🎯 Objectives

- Set up a reliable Python connection to a PostgreSQL database.
- Explore the `nyc_schools` schema (tables and columns).
- Use SQL to answer borough-level questions about:
  - School distribution
  - English Language Learners (ELL)
  - Special education (SPED) concentration
- Present results and interpretations in a clear, reproducible notebook.

---

## 📁 Files

- `sql_analysis.ipynb` – main notebook that:
  - establishes the database connection,
  - explores the schema,
  - runs the analytical SQL queries,
  - and explains the results in Markdown cells.

---

## 🗄️ Database & Connection

The analysis connects to a PostgreSQL database containing several NYC school-related tables, including:

- `nyc_schools.high_school_directory`
- `nyc_schools.school_demographics`
- `nyc_schools.school_safety_report`
- multiple SAT result tables

The connection is managed via **SQLAlchemy**. In a production setting, credentials should be stored securely (e.g. environment variables or a `.env` file), and not hard-coded in the notebook.

A small helper function, `run_query(sql: str) -> pd.DataFrame`, wraps `pandas.read_sql()` so that queries can be executed and returned as DataFrames with a single call.

---

## 🔍 Schema Exploration

Before writing analytical queries, the notebook inspects the database structure:

1. **List all tables in the `nyc_schools` schema**

   ```sql
   SELECT table_schema, table_name
   FROM information_schema.tables
   WHERE table_schema = 'nyc_schools'
   ORDER BY table_name;

2. **Inspect key tables and their columns**

- `high_school_directory` – school identifiers, names, boroughs, and metadata  
- `school_demographics` – enrollment, ELL, SPED, and demographic breakdowns  
- `school_safety_report` – safety and incident-related fields  

This step ensures that the correct columns and join keys (`dbn`) are used in later queries.

---

## 🧮 Analytical Questions & Queries

### 1. School Distribution – How many schools are there in each borough?

**Goal:** Count unique schools per borough.

**Approach:**

- Use the `nyc_schools.high_school_directory` table.  
- Group by `borough`.  
- Count distinct `dbn` values to avoid double-counting schools that appear multiple times.

   ```sql
   SELECT
    borough,
    COUNT(DISTINCT dbn) AS school_count
   FROM nyc_schools.high_school_directory
   GROUP BY borough
   ORDER BY school_count DESC;

**Interpretation**

This query returns, for each borough, the number of unique schools.  
Counting `DISTINCT dbn` ensures that schools are not over-counted due to multiple program entries, giving a more accurate picture of the geographic distribution of high schools.

---

### 2. Language Learners – What is the average % of ELL per borough?

**Goal:** Compute the average percentage of **English Language Learners (ELL)** for each borough.

**Approach:**

- Use `nyc_schools.school_demographics` as the source of `ell_percent`.  
- Join with `nyc_schools.high_school_directory` on the shared school identifier `dbn`.  
- Group by `borough`.  
- Calculate `AVG(ell_percent)` for each borough.

   ```sql
   SELECT
    d.borough,
    AVG(s.ell_percent) AS avg_ell_percent
   FROM nyc_schools.school_demographics AS s
   JOIN nyc_schools.high_school_directory AS d
    ON s.dbn = d.dbn
   GROUP BY d.borough
   ORDER BY d.borough;

**Interpretation**

This query provides a borough-level view of the ELL population across all available records in the demographics dataset.  
Boroughs with higher `avg_ell_percent` likely have a greater concentration of English Language Learners and may require more language support services (ESL programs, bilingual education, etc.).

---

### 3. Special Education – Top 3 records per borough by `sped_percent`

**Goal:** Identify the three records in each borough with the **highest percentage of special education students (`sped_percent`)**.

**Approach:**

- Use `nyc_schools.school_demographics` as the source of `sped_percent`.  
- Join with `nyc_schools.high_school_directory` on `dbn` to access `borough` and `school_name`.  
- Use a window function `ROW_NUMBER()` to rank records within each borough by `sped_percent` (descending).  
- Filter to the top 3 ranked records per borough.

   ```sql
   WITH sped_ranked AS (
    SELECT
        d.borough,
        d.school_name,
        s.dbn,
        s.sped_percent,
        ROW_NUMBER() OVER (
            PARTITION BY d.borough
            ORDER BY s.sped_percent DESC
        ) AS rn
    FROM nyc_schools.school_demographics AS s
    JOIN nyc_schools.high_school_directory AS d
        ON s.dbn = d.dbn
    WHERE s.sped_percent IS NOT NULL
        )
    SELECT
        borough,
        rn AS borough_rank,
        school_name,
        dbn,
        sped_percent
    FROM sped_ranked
    WHERE rn <= 3
    ORDER BY borough, borough_rank;

**Interpretation**

This query highlights the **most extreme SPED percentages** in each borough.  
Because the analysis is done at the record level (not aggregated by school), the same school can appear multiple times if it has multiple high-SPED records across different years. A follow-up analysis could aggregate by school to compute a single SPED value per school.

---

## 🧠 Summary & Insights

- The notebook demonstrates how to **combine SQL and Python** to explore a real PostgreSQL database.  
- By joining directory and demographics data, it answers questions about:
  - how schools are distributed across boroughs,
  - how ELL populations vary geographically,
  - and where special education concentrations are highest.
- The use of `COUNT(DISTINCT dbn)`, joins on `dbn`, and window functions (`ROW_NUMBER()`) shows practical SQL patterns that are common in real-world analytics work.

Overall, this component turns raw relational data into interpretable borough-level insights using a clean SQL + Python workflow.

