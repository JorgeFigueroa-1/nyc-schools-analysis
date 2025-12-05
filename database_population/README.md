# Database Population – SAT Results

This folder contains the data cleaning and database integration work for the NYC SAT results dataset.  
The goal is to transform a messy real-world CSV into a clean, relational-friendly table and load it into a PostgreSQL database.

---

## 🎯 Objectives

- Inspect and understand the raw SAT results dataset.
- Clean and normalize the data for analytical and relational use.
- Design a minimal, meaningful schema for SAT scores at the school level.
- Load the cleaned data into a PostgreSQL table using Python.

---

## 📁 Files

- `data/sat-results.csv` – raw SAT results dataset.
- `cleaned_sat_results.csv` – cleaned and standardized output dataset.
- `sat_modeling.ipynb` – notebook containing the full cleaning logic and database loading code.

---

## 🔍 Dataset Exploration

The analysis starts by loading `sat-results.csv` and examining:

- Shape: **493 rows × 11 columns**
- Column names and basic data types
- Missing values and obviously duplicated or redundant fields

Example raw columns include:

- `DBN` – school identifier  
- `SCHOOL NAME` – school name  
- `Num of SAT Test Takers`  
- `SAT Critical Reading Avg. Score`  
- `SAT Math Avg. Score`  
- `SAT Writing Avg. Score`  
- `SAT Critical Readng Avg. Score` (duplicate with a typo)  
- `internal_school_id`  
- `contact_extension`  
- `pct_students_tested`  
- `academic_tier_rating`  

The initial inspection shows a mix of numeric and string-encoded numeric fields, duplicated score columns, and percentage values stored as strings such as `"78%"`.

---

## 🧹 Data Cleaning Steps

All cleaning is implemented in Python using **pandas** (with help from **NumPy**).

### 1. Helper functions

Two key helper functions encapsulate the “messy” rules:

- `clean_sat_score(value)`  
  - Parses a value as a SAT score.  
  - If the value is missing or non-numeric → returns `NaN`.  
  - Enforces valid SAT range: **only values between 200 and 800** are kept; others become `NaN`.

- `parse_percentage(value)`  
  - Converts raw percentage-like strings to numeric values:  
    - `"78%"` → `78.0`  
    - `"92 %"` → `92.0`  
    - `"0.85"` → `85.0` (interpreted as a proportion)  
  - Handles blank or invalid values by returning `NaN`.

These functions keep the main notebook code clean and make the transformation logic reusable and explicit.

---

### 2. Merging duplicate reading score columns

The dataset contains two reading score columns:

- `SAT Critical Reading Avg. Score`  
- `SAT Critical Readng Avg. Score` (typo)

The notebook merges them into a single `reading_score_raw` column by:

- Preferring the main column when available
- Falling back to the typo column when the main one is missing

This removes redundancy without losing information.

---

### 3. Normalizing SAT scores and numeric fields

For each school, the following cleaned numeric fields are created:

- `reading_score` – cleaned reading score (200–800)
- `math_score` – cleaned math score (200–800)
- `writing_score` – cleaned writing score (200–800)
- `num_test_takers` – numeric version of `Num of SAT Test Takers`
- `pct_students_tested_clean` – numeric percentage of students tested
- `academic_tier_rating_clean` – numeric academic tier rating
- `internal_school_id_clean` – coerced to numeric where possible

Out-of-range SAT scores and invalid values are converted to `NaN` to avoid polluting downstream analyses.

---

### 4. Handling duplicates and missing values

The cleaning process includes:

- **Removing duplicate rows by `DBN`**  
  Only the first occurrence of each school (`DBN`) is kept.

  - 15 duplicate rows removed based on `DBN`.

- **Dropping rows with no SAT scores at all**  
  Any row where all three section scores (`reading_score`, `math_score`, `writing_score`) are missing is dropped.

  - 57 rows removed due to all scores being missing.

After these steps, the intermediate cleaned dataframe has:

- **421 rows**  
- **19 columns** (including intermediate cleaning columns)

---

## 🧱 Final Schema Design

A final, analysis-friendly dataframe is built with standardized column names:

- `dbn` – school identifier  
- `school_name` – school name  
- `internal_school_id` – cleaned internal school ID  
- `num_test_takers` – number of SAT test takers  
- `reading_score` – cleaned critical reading score  
- `math_score` – cleaned math score  
- `writing_score` – cleaned writing score  
- `pct_students_tested` – numeric percentage of students tested  
- `academic_tier_rating` – numeric academic tier rating  

This final table has:

- **421 rows × 9 columns**

and is exported as:

- `cleaned_sat_results.csv`

---

## 🗄️ PostgreSQL Integration

The cleaned data is then loaded into a **PostgreSQL** database hosted on **Neon**.

- A SQLAlchemy engine is created from a `DATABASE_URL`.
- The target schema and table are defined as:

  - Schema: `nyc_schools`  
  - Table: `jorge_figueroa_sat_results`

The notebook uses `pandas.DataFrame.to_sql()` to write the cleaned dataframe:

    ```python
    df_clean.to_sql(
        name=TARGET_TABLE,
        con=engine,
        schema=TARGET_SCHEMA,
        if_exists="replace",   # or "append" for incremental loads
        index=False
    )

A quick verification query (with `LIMIT 5`) confirms that the data in PostgreSQL matches the cleaned CSV.

---

## ✅ Summary

This component of the project:

1. Loaded the raw SAT results CSV (`sat-results.csv`).
2. Identified and removed duplicate or redundant columns.
3. Normalized column names and converted string-encoded numbers and percentages to proper numeric types.
4. Enforced valid SAT score ranges (200–800) and removed out-of-range or invalid values.
5. Removed duplicate schools and rows with no valid SAT scores.
6. Produced a clean, compact dataset (`cleaned_sat_results.csv`) with 421 rows and 9 key columns.
7. Loaded the cleaned data into a PostgreSQL table (`nyc_schools.jorge_figueroa_sat_results`) using SQLAlchemy.

Overall, this folder demonstrates a complete mini-ETL pipeline: from raw CSV, through robust cleaning, to a well-structured relational table ready for further analysis.

