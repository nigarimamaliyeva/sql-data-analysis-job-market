# Data Analyst Job Market & Optimal Skills Analysis (SQL Project)

## 1. Introduction
Welcome to my SQL Data Analysis portfolio project! In this project, I evaluate job market data to analyze demand, average compensation, and optimal technical skills for remote Data Analyst roles.

Alongside my main project queries, I included `sql_notes2` in this repository to document my learning progression. I used `sql_notes2` to practice essential SQL syntax step-by-step—from basic `SELECT` queries to CTEs, multi-table joins, and aggregations—ensuring a solid hands-on foundation before completing this final analysis.

---

## 2. Background
I structured this project to determine which technical tools provide the most effective combination of high market demand and strong financial return.

My analysis addresses three main objectives:
- Identifying the most requested technical skills in remote Data Analyst job postings.
- Measuring the average salary associated with each skill.
- Isolating the optimal skills that offer both high volume and top compensation.

---

## 3. Tools I Used
- **SQL (PostgreSQL)**: My core tool for data retrieval, joining relational tables, writing CTEs, and calculating aggregations.
- **Visual Studio Code**: My development workspace for writing practice scripts (`sql_notes2`) and executing project queries.
- **Git & GitHub**: Used for version control and hosting my project portfolio.

---

## 4. The Analysis
To evaluate optimal skills, I wrote a query using two Common Table Expressions (CTEs): `skills_demand` to count total job postings per skill, and `average_salary` to calculate the average annual compensation. I then joined both CTEs, filtered for skills appearing in more than 10 job postings, and sorted the dataset by salary and demand.

```sql
WITH skills_demand AS (
    SELECT 
        skills_dim.skill_id,
        skills_dim.skills,
        COUNT(skills_job_dim.job_id) AS demand_count
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id   
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id   
    WHERE job_title_short = 'Data Analyst'
      AND salary_year_avg IS NOT NULL
      AND job_work_from_home = TRUE
    GROUP BY skills_dim.skill_id, skills_dim.skills
), 
average_salary AS (
    SELECT 
        skills_dim.skill_id,
        skills_dim.skills,
        ROUND(AVG(salary_year_avg), 0) AS avg_salary
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id   
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id   
    WHERE job_title_short = 'Data Analyst'
      AND salary_year_avg IS NOT NULL
      AND job_work_from_home = TRUE
    GROUP BY skills_dim.skill_id, skills_dim.skills
)
SELECT 
    skills_demand.skill_id,
    skills_demand.skills,
    demand_count,
    avg_salary
FROM skills_demand
INNER JOIN average_salary ON skills_demand.skill_id = average_salary.skill_id
WHERE demand_count > 10
ORDER BY avg_salary DESC, demand_count DESC
LIMIT 25;


