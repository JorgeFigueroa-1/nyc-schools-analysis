# School Directory Exploration

This folder contains an exploratory analysis of the **NYC High School Directory (2013–2014)** dataset using Python and Jupyter Notebook.  
The goal of this component is to understand how high schools are distributed across boroughs, how large they are, and which schools offer Grade 9 entry.

---

## 🎯 Objectives

- Clean and standardize the school directory dataset.
- Answer specific questions about Brooklyn high schools.
- Summarize key metrics by borough (schools, students, grade spans).
- Create a simple visualization of the number of schools per borough.

---

## 📁 Files

- `school_directory_analysis.ipynb` – main notebook with all cleaning, analysis, and plots.
- `data/high-school-directory.csv` – NYC high school directory dataset.

---

## 🧹 Data Cleaning

The original column names were inconsistent (mixed case, spaces, special characters).  
To make the dataset easier to work with, all column names were standardized by:

- converting to lowercase,
- trimming leading and trailing spaces,
- replacing spaces with underscores (`_`),
- removing special characters.

This allows safe and consistent column access in pandas.

---

## 🔍 Key Questions & Methods

### 1. How many total schools are in Brooklyn?

- Filtered the dataset to rows where `borough` corresponds to Brooklyn (case- and space-normalized).
- Used the unique school identifier column `dbn` to count distinct schools.

**Result:**  
There are **118 unique high schools** in Brooklyn in this dataset.

---

### 2. How many Brooklyn schools offer Grade 9 entry?

A school is considered to offer Grade 9 entry if:

- `grade_span_20142015_min` ≤ 9, and  
- `grade_span_20142015_max` ≥ 9.

These conditions ensure that schools that start before Grade 9 but extend through Grade 9 are correctly included.

**Result:**  
All **118 Brooklyn schools** in the dataset have a grade span that includes Grade 9.  
In other words, every Brooklyn school in this directory offers Grade 9 entry.

---

### 3. Grouped summaries by borough

Using `dbn` as the school identifier, the analysis computed:

- **Count of unique schools per borough**
- **Average number of students per borough** (column `total_student_1026`)
- **Descriptive statistics of `grade_span_20142015_max` per borough** using `.describe()`  

Examples of results:

- **Number of schools (unique `dbn`)**
  - Bronx: 116  
  - Brooklyn: 118  
  - Manhattan: 101  
  - Queens: 75  
  - Staten Island: 10  

- **Average number of students per school**
  - Bronx: ~521 students  
  - Brooklyn: ~716 students  
  - Manhattan: ~596 students  
  - Queens: ~1,101 students  
  - Staten Island: ~1,839 students  

- **Grade span max (`grade_span_20142015_max`)**
  - Across all boroughs, the median and 75th percentile are 12, showing that most schools go up to Grade 12.

---

## 📊 Visualization

A bar chart was created to show the **number of unique schools per borough**:

- x-axis: borough
- y-axis: number of schools (unique `dbn`)
- Labels on top of each bar for clarity

This plot provides a quick visual comparison of how high schools are distributed across NYC boroughs.

---

## 🧠 Insights

- **Distribution of schools:**  
  Brooklyn has the highest number of schools (118), followed closely by the Bronx (116) and Manhattan (101). Queens has fewer schools (75), and Staten Island has only 10 schools in this dataset.

- **School size:**  
  Bronx schools tend to be smaller on average (~520 students). Brooklyn and Manhattan are mid-sized (~600–720 students). Queens and Staten Island have the largest average school sizes (~1,100 and ~1,840 students respectively).

- **Grade span patterns:**  
  Most schools across all boroughs extend to Grade 12, confirming that the dataset primarily focuses on full high schools rather than partial grade spans.

---

## ✅ Summary

This exploration cleans and standardizes the NYC high school directory, answers targeted questions about Brooklyn schools and Grade 9 entry, and builds borough-level summaries and visualizations. The results provide a clear overview of how high schools are distributed and sized across New York City.

