# Incident Analysis

This folder contains an analysis of a NYC school incident dataset.  
The work was originally performed in Google Sheets and summarized here as a reproducible, portfolio-ready component of the project.

---

## 🎯 Objectives

- Clean and standardize the incident dataset.
- Answer core descriptive questions about incidents and schools.
- Identify patterns and notable anomalies in incident frequency.

---

## 📁 Files

- `data/School_Safety_Report_Data_Dictionary.xlsx` – data dictionary describing the incident fields.
- `data/school-safety-report.csv` – raw incident dataset.
- `findings.md` – detailed written summary of the analysis and results.

---

## 🧹 Data Cleaning

Column names in the incident dataset were cleaned to make them easier to use programmatically:

- Converted to **lowercase**.
- Trimmed leading/trailing spaces.
- Replaced spaces with **underscores** (`_`).
- Removed special characters.

This standardization ensures consistent column references when filtering, aggregating, or joining the data.

---

## 🔍 Core Questions & Answers

Using the cleaned dataset:

- **Total rows in the dataset:**  
  **6,310** incident records.

- **Total unique schools listed:**  
  **2,266** distinct schools.

- **Most frequent incident type:**  
  **Non-criminal** incidents are the most common category.

- **Percentage of incidents that occurred in the Bronx:**  
  **28.24%** of all incidents took place in the **Bronx**.

These metrics were calculated by counting rows, counting unique school identifiers, finding the mode of the incident type column, and computing the share of incidents where the borough is the Bronx.

---

## 🧠 Notable Findings

- **High coverage across schools**  
  With 6,310 rows and 2,266 unique schools, the dataset covers a wide range of school contexts and locations, making the summary metrics fairly representative.

- **Incidents are mostly non-criminal**  
  The fact that the most frequent incident type is *non-criminal* suggests that many reported events are related to lower-level safety or behavior issues rather than severe crimes. This has implications for how risk is communicated and managed at the district level.

- **Extremely high incident rate at a hospital school**  
  One of the most striking anomalies is *hospital_schools__queens*, which has an estimated **133 incidents per 100 students**.  
  This unusually high rate is likely driven by the school’s very small enrollment and its specialized hospital context, where reporting and classification of incidents differ from traditional schools. It highlights the importance of interpreting per-student rates carefully, especially in edge cases with small populations.

---

## ✅ Summary

This incident analysis provides a high-level view of safety-related events across NYC schools, including overall volume, coverage, and borough-level distribution. It also surfaces an important anomaly in incident rates at a hospital school, illustrating how context and enrollment size can heavily influence metrics such as incidents per 100 students.
