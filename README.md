# 👥 HR Analytics Dashboard

> Excel data analysis project analyzing 200 employee records using Power Query, Power Pivot, and DAX, featuring an interactive multi-page dashboard covering attrition, performance, compensation, training, and satisfaction.

An end-to-end Excel data analysis project that uncovers what drives employee attrition, how performance varies across departments and roles, and where the organization's biggest people-risks are hiding.

The entire pipeline — from raw data to interactive dashboard — was built inside Microsoft Excel using:

- **Power Query** for data ingestion, cleaning, and transformation
- **Power Pivot** for building a relational Star Schema
- **DAX** for calculated KPIs and measures across five analytical axes
- **PivotCharts + Slicers** for interactive visualization

**Key business outcomes:**

- Identified the primary drivers behind a 19% attrition rate
- Pinpointed which departments and tenure stages carry the highest exit risk
- Found that performance is largely disconnected from satisfaction, engagement, and training hours
- Surfaced a compensation gap between high-performing and high-paying departments

## 📋 Table of Contents

- [Dataset Description](#-dataset-description)
- [Data Model — Star Schema](#️-data-model--star-schema)
- [ETL Process — Power Query](#️-etl-process--power-query)
- [DAX Measures](#-dax-measures)
- [Dashboard Pages & Analysis](#-dashboard-pages--analysis)
- [Key Insights](#-key-insights)
- [Recommendations](#-recommendations)
- [Tools & Technologies](#️-tools--technologies)
- [File Structure](#-file-structure)

## 📦 Dataset Description

The raw data contains one record per employee, merged from two source CSV files, and includes the following fields:

| Field | Description |
|---|---|
| EmployeeID | Unique identifier for each employee |
| Name | Employee name |
| Department | Finance, HR, IT, Marketing, Operations, Sales |
| JobRole | Specific role (Account Manager, Data Analyst, BI Developer, etc.) |
| Gender | Male / Female |
| Age | Employee age (used to build Age Group) |
| HireDate | Date the employee joined |
| ExitDate | Date the employee left (blank if still active) |
| Salary | Monthly salary |
| PerformanceScore | Performance rating out of 5 |
| SatisfactionScore | Job satisfaction rating out of 5 |
| EngagementScore | Engagement rating out of 5 |
| AbsenceDays | Number of absence days |
| OvertimeHours | Overtime hours logged |
| TrainingHours | Hours spent in training |
| TrainingCost | Cost of training per employee |

**Calculated columns added:** Employee Status (Active/Exited), Age Group, Salary Band, Tenure, Training Hours Band

## 🗂️ Data Model — Star Schema

The data is modeled as a classic Star Schema in Power Pivot, with one central fact table connected to five dimension tables via surrogate keys.

![Star Schema](https://github.com/Huda-Talaat/HR-Analytics-Dashboard/raw/main/HR%20StarSchema.jpg)

**Tables:**

**Fact_Employees** (many side)
- Contains all transactional measures: Salary, PerformanceScore, SatisfactionScore, EngagementScore, AbsenceDays, OvertimeHours, TrainingHours, TrainingCost, Tenure, HireDate, ExitDate, Employee Status
- Foreign keys: Dep. Key, JobRole Key, SalaryBand Key, AgeGroup Key, Date_Key

**Dim_Department** — Dep. Key → Department
**Dim_JobRole** — JobRole Key → JobRole
**Dim_SalaryBand** — SalaryBand Key → Salary Band (Low / Medium / High)
**Dim_AgeGroup** — AgeGroup Key → Age Group (22-30 / 31-40 / 41-50)
**Dim_Date** — Date, Year, Quarter, Month Name — drives both HireDate and ExitDate analysis

**Why Star Schema?**

- Enables fast aggregations via PivotTables across all five analytical axes
- Clean separation between descriptive attributes (dimensions) and numeric facts
- Makes DAX measures simpler and more readable
- Allows slicers on dimension tables (Department, JobRole, Age Group, Salary Band, Year) to filter every visual simultaneously

## ⚙️ ETL Process — Power Query

Before loading data into the model, Power Query was used to merge, clean, and prepare the data:

1. **Merge two source files** — combined into a single unified employee table
2. **Remove duplicates** — ensured one record per employee
3. **Handle nulls** — blank ExitDate values used to flag active vs exited employees
4. **Data type enforcement** — dates parsed correctly, numeric fields validated
5. **Surrogate key generation** — integer keys added to dimension tables for clean relationships
6. **Dimension table extraction** — unique values from Department, JobRole, Salary Band, and Age Group extracted into separate dimension tables
7. **Calculated columns added:**
   - `Employee Status` — Active vs Exited (based on ExitDate)
   - `Tenure` — years between HireDate and ExitDate (or today, if active)
   - `Age Group` — 22-30, 31-40, 41-50
   - `Salary Band` — Low (<8K), Medium (8K-12K), High (>12K)
   - `Training Hours Band` — Low, Medium, High

## 📐 DAX Measures

Key calculated measures used across the dashboard:

```
-- Total Employees
Total Employees = COUNTROWS(Fact_Employees)

-- Active Employees
Active Employees = CALCULATE(COUNTROWS(Fact_Employees), Fact_Employees[Employee Status] = "Active")

-- Total Leavers
Total Leavers = CALCULATE(COUNTROWS(Fact_Employees), Fact_Employees[Employee Status] = "Exited")

-- Attrition Rate
Attrition Rate = DIVIDE([Total Leavers], [Total Employees])

-- AVG Tenure (Exited)
AVG Tenure-Exited = CALCULATE(AVERAGE(Fact_Employees[Tenure]), Fact_Employees[Employee Status] = "Exited")

-- AVG Performance Score
AVG Performance = AVERAGE(Fact_Employees[PerformanceScore])

-- AVG Performance (Exited)
AVG Performance-Exited = CALCULATE(AVERAGE(Fact_Employees[PerformanceScore]), Fact_Employees[Employee Status] = "Exited")

-- AVG Satisfaction Score
AVG Satisfaction Score = AVERAGE(Fact_Employees[SatisfactionScore])

-- AVG Salary
AVG Salary = AVERAGE(Fact_Employees[Salary])
```

## 📊 Dashboard Pages & Analysis

All pages share the same **Department**, **JobRole**, **Age Group**, **Salary Band**, and **Year** slicers, so every visual updates together when a filter is applied.

### Home Page

Navigation hub for the six dashboard pages.

![Home Page](https://github.com/Huda-Talaat/HR-Analytics-Dashboard/raw/main/HR%20Home%20Page.jpg)

### Overview

High-level summary of headcount, attrition, salary, and performance.

![Overview](https://github.com/Huda-Talaat/HR-Analytics-Dashboard/raw/main/HR%20Overview.jpg)

**KPIs:** Total Employees (200) — Attrition Rate (19%) — AVG Salary (9,582) — AVG Performance Score (3.77) — AVG Satisfaction Score (3.11) — Active vs Exited (162 / 38)

### Attrition Analysis

Explores who is leaving, from which department, salary band, age group, and when.

![Attrition](https://github.com/Huda-Talaat/HR-Analytics-Dashboard/raw/main/HR%20Attrition.jpg)

**Purpose:** Understand what drives the 19% attrition rate and which segments carry the highest exit risk.

**Attrition by Department** — Operations leads at 30%, followed by Sales at 23%, IT at 19%, HR at 18%, Finance and Marketing at 13% each.

**Attrition by Salary Band** — Fairly even across bands: High earners 18%, Medium 19%, Low 19% — salary band alone doesn't explain who leaves.

**AVG Tenure (Exited)** — 1.7 years. Employees are leaving early in their journey, not after years of service.

**Total Leavers by Year** — A sharp spike in 2023 with 17 exits, compared to single digits in every other year (2018–2024).

**Attrition by Age Group** — 41-50 group has the highest rate at 21%, followed by 31-40 at 19%, and 22-30 at 16%.

**Attrition by Gender** — Female 18%, Male 20% — no major gender skew.

**AVG Satisfaction by Employee Status** — Exited employees averaged 3.13, almost identical to Active employees at 3.10 — satisfaction alone doesn't predict who leaves.

### Performance

Analyzes performance scores across job role, department, gender, salary band, age group, and training level.

![Performance](https://github.com/Huda-Talaat/HR-Analytics-Dashboard/raw/main/HR%20Performans.jpg)

**Purpose:** Understand what actually drives performance — and what doesn't.

**Performance by Department** — Sales leads at 3.96, followed by Marketing (3.83), HR (3.80), Finance (3.77), Operations (3.61), and IT at the bottom with 3.51.

**Performance by Job Role** — Account Manager tops the list at 4.13. Data Analyst is lowest at 3.04.

**Performance by Gender** — Female 3.78, Male 3.76 — negligible difference.

**Performance by Salary Band** — High earners score 3.88, Medium 3.74, Low 3.68 — a mild upward trend, but not a strong driver.

**Performance by Age Group** — 31-40 scores highest at 3.82, followed closely by 22-30 (3.81), then 41-50 at 3.70.

**Performance by Training Hours Band** — Counterintuitively, the Low training group scores highest at 3.88, while High training scores 3.71 — training appears reactive rather than performance-driving.

### More Details

Deep dive into training, compensation, satisfaction, and engagement by department and role.

![More Details](https://github.com/Huda-Talaat/HR-Analytics-Dashboard/raw/main/HR%20MoreDetails.jpg)

**AVG Training Hours by Department** — Sales highest (20.95), IT lowest (17.73).

**AVG Salary by Department** — IT highest (10,520), Sales lowest (8,950) — despite Sales having the best performance score in the company.

**AVG Training Cost by Department** — Finance highest (1,146), Sales lowest (984).

**AVG Salary by Job Role** — DBA and Financial Analyst lead at ~10,800, while HR Specialist is lowest at 8,117.

### Insights & Recommendations

Key findings and actionable recommendations derived from the full analysis.

![Insights and Recommendations](https://github.com/Huda-Talaat/HR-Analytics-Dashboard/raw/main/HR%20Report.jpg)

## 🔍 Key Insights

1. **Attrition rate stands at 19%**, which is relatively high — Operations leads with 30%, followed by Sales at 22.7%.
2. **Employees leave on average after just 1.7 years**, showing weak early-stage retention.
3. **A notable spike occurred in 2023**, with 17 exits in a single year.
4. **Overall Performance Score is 3.77/5.** Sales performs best (3.96), while IT is the lowest (3.51).
5. **No significant correlation exists** between Performance and Satisfaction, Engagement, or Training Hours.
6. **Training seems reactive** — employees with fewer training hours tend to perform slightly better, suggesting training is assigned to fix problems rather than build capability.
7. **Finance has the highest average salary**, while HR and Sales have lower compensation levels — despite Sales posting the best performance in the company.

## 💡 Recommendations

1. **Reduce Attrition** — Conduct structured exit interviews for all leavers, especially from Operations and Sales. Strengthen onboarding programs specifically for the first two years of tenure.
2. **Enhance Performance** — Target performance improvement initiatives in IT and Operations, the two lowest-scoring departments.
3. **Shift Training from Reactive to Proactive** — Redesign training to focus on skill-building and career growth, not just remediation, since current data shows it does not correlate with higher performance.
4. **Review Compensation** — Benchmark and review salaries in Sales and HR to improve competitiveness, particularly given Sales' top performance score paired with the lowest department salary.
5. **Create Clear Career Paths** — Build visible promotion tracks for senior employees to reduce flight risk among tenured staff.
6. **Run Continuous Follow-up** — Conduct employee satisfaction surveys every 6 months and closely monitor attrition trends going into 2026, particularly watching for another spike like 2023.

## 🛠️ Tools & Technologies

| Tool | Purpose |
|---|---|
| Microsoft Excel | Primary development environment |
| Power Query | ETL — data cleaning, merging & transformation |
| Power Pivot | In-memory data model, star schema, relationships |
| DAX | Calculated measures and KPIs across five analytical axes |
| PivotCharts | Interactive dashboard visuals |

## 📁 File Structure

```
HR-Analytics-Dashboard/
│
├── HR Project.xlsm              # Main Excel file (dashboard + data model)
└── README.md                    # Project documentation
```
