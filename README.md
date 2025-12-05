# NYC Schools Data Analysis

End-to-end NYC public schools data project using **Python** and **PostgreSQL** for data cleaning, exploration, database loading, and SQL-based reporting.

This repository reorganizes my onboarding work into a portfolio-ready project, structured by **analysis task** rather than by day.

---

## 🎯 Project Overview

The goal of this project is to:

- Explore NYC public school data (directory and related datasets).
- Analyze school-level incident and safety data.
- Clean and transform raw CSV files into a database-ready schema.
- Load cleaned data into a PostgreSQL database.
- Run SQL queries from Python to answer analytical questions.
- Clearly document methods, assumptions, and key findings.

Each subfolder contains its own `README.md` with task-specific details and results.

---

## 📂 Repository Structure

   ```text
    nyc-schools-analysis/
    ├── incident_analysis/
    ├── school_directory_exploration/
    ├── database_queries/
    └── database_population/


- `incident_analysis/`  
  Exploratory analysis of school incident data.  
  Answers questions such as:
  - How many incidents are recorded?
  - How many unique schools are involved?
  - What is the most frequent incident type?
  - What share of incidents occur in the Bronx?  
    Includes a discussion of notable patterns and anomalies (e.g., very high incident rates in specific hospital schools).

- `school_directory_exploration/`  
  Exploration of the NYC High School Directory (2013–2014).  
  Covers:
  - Cleaning and standardizing column names
  - Counting schools per borough
  - Identifying how many Brooklyn schools offer Grade 9 entry
  - Summarizing average school size and grade spans by borough
  - Visualizing the number of schools per borough.

- `database_queries/`  
  Jupyter notebook that connects to a PostgreSQL database (Neon) and runs SQL queries via Python.  
  Focuses on:
  - Counting schools per borough from the high school directory
  - Computing average ELL (English Language Learner) percentage by borough
  - Using window functions to find the top records per borough by special education percentage (`sped_percent`).

- `database_population/`  
  Data cleaning and database population pipeline for SAT results.  
  Implements:
  - Validation and cleaning of SAT scores (enforcing the 200–800 range)
  - Parsing and normalizing percentage fields (e.g. `"78%"`)
  - Removing duplicates and rows without valid scores
  - Exporting a cleaned CSV (`cleaned_sat_results.csv`)
  - Loading the cleaned data into a PostgreSQL table (`nyc_schools.jorge_figueroa_sat_results`) using SQLAlchemy.

Each of these folders has a dedicated `README.md` with more detailed explanations of the approach and insights.

---

## 🧠 Key Insights (High Level)

Across the different components of the project:

- **Incidents**  
  Most reported incidents are _non-criminal_, and around a quarter of all incidents occur in the Bronx.  
  Certain special contexts (such as hospital schools) can show extremely high incident rates per 100 students, highlighting the importance of interpreting metrics in context.

- **School directory**  
  Brooklyn has the highest number of high schools in the directory, followed closely by the Bronx and Manhattan.  
  Queens and Staten Island have fewer schools but tend to have larger average enrollments per school.

- **ELL and special education**  
  Joining demographics with the high school directory makes it possible to compute average ELL percentages and to identify records with particularly high concentrations of special education students in each borough.

- **SAT results**  
  Careful cleaning is required to handle inconsistent numeric formats, duplicated columns, and invalid SAT scores.  
  After cleaning, the results can be safely integrated into a relational database and used alongside other NYC school datasets.

---

## 🛠 Tech Stack

- **Language:** Python  
- **Database:** PostgreSQL (Neon)  
- **Libraries & Tools:**
  - pandas
  - NumPy
  - SQLAlchemy
  - psycopg2
  - Jupyter Notebook
  - Matplotlib (for visualizations)

---

## 🚀 How to Use

1. **Clone the repository:**

   ```bash
   git clone https://github.com/JorgeFigueroa-1/nyc-schools-analysis.git
   cd nyc-schools-analysis

2. **(Optional) Create and activate a virtual environment.**

3. **Install dependencies** (if a `requirements.txt` file is added):

   ```bash
   pip install -r requirements.txt

4. **Open the notebooks** (for example with Jupyter Lab or VS Code) and run them step by step:

   - `school_directory_exploration/school_directory_analysis.ipynb`
   - `database_queries/sql_analysis.ipynb`
   - `database_population/sat_modeling.ipynb`

5. For database-related notebooks, make sure you have valid PostgreSQL connection settings (and do not commit real credentials to version control).

---

## 📌 Notes

This project was originally created as part of an onboarding assignment and then refactored into a single, public portfolio repository.  
The structure and documentation are designed to make the workflow easy to follow for reviewers, teammates, or future employers.

