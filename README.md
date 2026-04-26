# Introduction
This project is a comprehensive analysis of the data analyst job market. My goal was to apply SQL and Python techniques to real-world data to find the most optimal skills for data analysts.

Check SQL queries out here: [project_sql folder](/project_sql/)
# Background
I built this project to answer a fundamental question: What technical skills should I prioritize to maximize my market value? By querying a database of job postings, I’ve moved beyond theory to identify the actual tools that leading companies like AT&T and SmartAsset are demanding from senior-level analysts.

# Tools I Used
To conduct this analysis, I leveraged the following technologies:

**SQL**: For extracting and querying job data from the core database.

**Python (Pandas)**: To clean the data and aggregate results for visualization.

**Matplotlib & Seaborn**: To create professional-grade charts for storytelling.

**VS Code**: My primary environment for writing SQL and Python scripts.

**Git & GitHub**: Essential for version control and documenting my progress.
# The Analysis
## 1.Top Paying Data Analyst Jobs
To understand the salary ceiling, I identified the 10 highest-paying roles. This analysis highlights how diverse the data analyst title can be across different sectors.

```SQL
select
    job_id,
    job_title,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    name as company_name
from 
    job_postings_fact
LEFT JOIN company_dim on job_postings_fact.company_id = company_dim.company_id
where 
    job_title_short = 'Data Analyst' and 
    job_location = 'Anywhere' and 
    salary_year_avg IS NOT NULL
ORDER BY 
    salary_year_avg desc
limit 10
```
Key insights from the top-paying Data Analyst roles in 2023:

* High Salary Ceiling: Salaries peak at $650,000 (Mantys), showing extreme growth potential in specialized data roles.

* Top Employers: Tech giants like Meta ($336,500) and AT&T ($255,829) lead the market in high-tier compensation.

* Role Diversity: High demand spans from technical experts (Principal Data Analysts) to strategic leaders (Director of Analytics).

* Flexibility: The majority of these top-paying positions offer Remote or Hybrid work options.

* Strong Floor: Even the 10th highest-paying role earns a significant $184,000, confirming a lucrative industry standard.

![alt text](/assets/1_top_paying_roles.png)

## 2. Skills for Top Paying Jobs
What do the top 10 positions have in common? I looked at the specific tools mentioned in these high-paying descriptions.

```SQL
with top_paying_jobs as (
SELECT 
    job_id,
    job_title,
    salary_year_avg,
    name as company_name
from 
    job_postings_fact
LEFT JOIN company_dim on job_postings_fact.company_id = company_dim.company_id
where 
    job_title_short = 'Data Analyst' and 
    job_location = 'Anywhere' and 
    salary_year_avg IS NOT NULL
ORDER BY 
    salary_year_avg desc
limit 10
)


select 
    top_paying_jobs.*, skills
from 
    top_paying_jobs
inner join skills_job_dim on top_paying_jobs.job_id = skills_job_dim.job_id
inner join skills_dim on skills_job_dim.skill_id = skills_dim.skill_id 
ORDER BY 
    salary_year_avg desc
```
* The "Must-Have" Duo: SQL and Python are the non-negotiable foundations, appearing in almost every high-paying role.

* Visualization King: Tableau is the most sought-after tool for turning data into insights at the executive level.

* Cloud & Data Warehousing: Proficiency in Snowflake, AWS, and Azure distinguishes the highest-tier salaries.

* Analytic Libraries: Core Python libraries like Pandas and Numpy are essential for roles focused on deep technical analysis.

* Strategic Stack: Beyond coding, tools like Excel and Jira remain vital for project management and business integration in senior positions.

![alt text](/assets/2_image.png)

## 3. In-Demand Skills for Data Analysts
Beyond the highest earners, I analyzed the broader market to see which skills are most frequently requested across all postings.

```SQL 
SELECT  
    skills,
    count(skills_job_dim.job_id) as demand_count
FROM 
    job_postings_fact
INNER JOIN skills_job_dim on job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim on skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' AND
    job_work_from_home = TRUE
GROUP BY 
    skills
ORDER BY demand_count DESC
LIMIT 5
```
Key insights from the most in-demand skills for Data Analysts in 2023:

* SQL Dominance: With 7,291 postings, SQL is the undisputed #1 skill required across the industry.

* The Power Couple: Excel (4,611) and Python (4,330) are almost equally essential, serving as the primary tools for data handling and analysis.

![alt text](/assets/3_image.png)

## 4. Skills Based on Salary
This analysis looks at the average annual salary for each skill. It helps identify which technical expertise is most highly valued by employers monetarily.

```SQL
SELECT  
    skills,
    round(avg(salary_year_avg), 0) as avg_salary
FROM 
    job_postings_fact
INNER JOIN skills_job_dim on job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim on skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' 
    AND
    salary_year_avg IS NOT NULL
    AND
    job_work_from_home = TRUE
GROUP BY 
    skills
ORDER BY 
    avg_salary DESC
LIMIT 25
```
Here's a breakdown of the result for top paying skills:

* Big data tools (PySpark, Databricks, Kubernetes, Airflow) → highest salaries come from handling large-scale data systems
* Machine learning & analytics (Pandas, NumPy, scikit-learn, DataRobot) → higher pay for predictive and advanced analysis skills
* DevOps & engineering tools (GitLab, Jenkins, Bitbucket, Linux) → analysts with engineering workflow skills earn more

![alt text](/assets/4_image.png)

## 5. Most Optimal Skills to Learn
By analyzing the intersection of high salary and high demand, I found that SQL and Python are the most "optimal" skills for any data analyst looking to grow their career in 2023.

```SQL
SELECT 
    skills_dim.skill_id,
    skills_dim.skills,
    count(skills_job_dim.job_id) as demand_count,
    round(avg(salary_year_avg), 0) as avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim on job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim on skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' 
    AND
    salary_year_avg IS NOT NULL
    AND
    job_work_from_home = TRUE
GROUP BY 
    skills_dim.skill_id
HAVING
    count(skills_job_dim.job_id) > 10
ORDER BY
    avg_salary DESC,
    demand_count DESC
LIMIT 25;
```
Key insights for the most optimal skills in 2023:

* Top Earner: Go leads as the highest-paying optimal skill with an average salary of $115,320.

* Cloud Dominance: Snowflake, Azure, and AWS are the most rewarding cloud skills, all commanding salaries over $108,000.

* Big Data Power: Hadoop remains a high-value asset for data analysts, securing a spot in the top 3 with $113,193.

* Strategic Workflow: Knowledge of management and documentation tools like Confluence ($114,210) and Jira ($104,918) is significantly compensated in senior roles.

* High Entry Bar: All top 10 optimal skills offer an average salary above $104,000, highlighting a high return on investment for these specific technical proficiencies.

![alt text](/assets/5_image.png)

# What I Learned
Throughout this data adventure, I’ve turbocharged my SQL toolkit with some serious firepower:

**Architecting Complex Queries:** Utilized CTEs and advanced JOIN logic to streamline data processing.

**Strategic Data Aggregation:** Mastered GROUP BY and HAVING clauses to pinpoint hidden trends.

**Problem-Solving Frameworks:** Developed a framework for translating abstract business challenges into concrete SQL scripts.
 
# Conclusions & Insights
The data revealed a compelling story about the current state of the 2023 analytics market:

**Elite Earning Potential:** Data analysis offers executive-level rewards, with top remote roles hitting a $650,000 ceiling for high-level expertise.

**The "Core Four":** SQL, Python, Tableau, and Excel are the industry backbone; mastering these is the baseline for any high-paying position.

**SQL’s Dominance:** With 7,291 mentions, SQL is the undisputed market leader and the most essential bridge to top employers.

**Specialization Premium:** Engineering-heavy skills like PySpark ($208K+) command the highest salaries, proving that technical depth yields the best ROI.

**Strategic Roadmap:** The most stable high-income path lies at the intersection of Cloud (Snowflake/AWS) and Core Analysis (SQL).

# Closing Thoughts
This project enhanced my SQL skills while providing a strategic audit of the data analyst job market. By quantifying the link between specific skills and market value, I’ve gained a data-driven compass to prioritize my professional development. The findings highlight the importance of a "hybrid profile"—blending analytical depth with technical agility—to stay competitive. This exploration serves as a guide for focusing on high-demand, high-salary skills and underscores the necessity of continuous learning in an ever-evolving digital economy.