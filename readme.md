# Employee Management System (EMS) — HR Analytics Dashboard
 
**Tools:** Python · PostgreSQL · Power BI  
**Data:** Kenya-specific · 300 Employees · 2024  
**Scale:** 82,800 rows across 4 tables
 
---

## Project Overview
 
This project simulates a real-world HR analytics system for a mid-sized Kenyan organisation with 300 employees. The goal was to build an end-to-end data pipeline — from raw data generation in Python, through structured storage in PostgreSQL, to an interactive three-page Power BI dashboard that answers key workforce, payroll, and attendance questions.
 
The project was designed to reflect Kenya-specific employment data, including KRA PINs, NSSF deductions, PAYE calculations, SHA contributions, and local bank details.
 
---

## Tools & Technologies
 
| Tool | Purpose |
|---|---|
| Python (Jupyter Notebook) | Data generation and database loading |
| PostgreSQL | Relational database storage |
| Power BI Desktop | Data modelling and dashboard visualisation |
| Power Query (within Power BI) | Data transformation and table merging |
 
---

## Project Structure
 
The project is organised into four sequential phases:
 
```
EMS Project
├── Phase 1 — Data Generation (Python/Jupyter)
├── Phase 2 — Database Setup (PostgreSQL)
├── Phase 3 — Data Modelling (Power BI)
└── Phase 4 — Dashboard Build (Power BI)
```
 
---
 
## Phase 1 — Data Generation (Python)
 
All data was synthetically generated using Python in a Jupyter Notebook environment. The data was designed to reflect realistic Kenyan HR records.
 
### Tables Generated
 
**employees (300 rows)**  
Personal and employment information for each employee, including first name, last name, full name, national ID, KRA PIN, SHA number, NSSF number, phone number, bank name, bank account number, email address, date of birth, gender, hire date, and employment status.
 
**positions (300 rows)**  
Job-related information linked to each employee, including department, position title, seniority level, basic salary, gross salary, net salary, NSSF deduction, PAYE deduction, and bonus.
 
**attendance (78,600 rows)**  
One record per employee per working day in 2024 (262 working days × 300 employees). Each record captures the date, attendance status (Present, Absent, or Leave), and a leave_days_remaining counter that counts down throughout the year.
 
**payroll (3,600 rows)**  
One record per employee per month for all 12 months of 2024 (12 × 300 employees). Each record includes basic salary, gross salary, net salary, PAYE, NSSF deduction, bonus, and payment status. November and December are marked as Pending to simulate real-world payroll lag.
 
### Total Scale
 
| Table | Rows |
|---|---|
| employees | 300 |
| positions | 300 |
| attendance | 78,600 |
| payroll | 3,600 |
| **Total** | **82,800** |
 
---
 
## Phase 2 — Database Setup (PostgreSQL)
 
All four tables were loaded into a PostgreSQL database using Python's sqlalchemy library via Jupyter Notebook.
 
### Schema Design
 
Each table uses employee_id as the primary or foreign key:
 
- **employees.employee_id** — Primary Key
- **positions.employee_id** — Foreign Key → employees
- **attendance.employee_id** — Foreign Key → employees
- **payroll.employee_id** — Foreign Key → employees
This design follows a star schema pattern with employees as the central dimension table and attendance and payroll as fact tables.
 
### Connecting Power BI to PostgreSQL
 
Power BI Desktop was connected directly to the PostgreSQL database using the built-in PostgreSQL connector:
 
1. Home → Get Data → PostgreSQL Database
2. Enter server address and database name
3. Select all four tables and load
---
 
## Phase 3 — Data Modelling (Power BI)
 
Setting up the data model correctly before building any visuals was the most critical step in the project. Incorrect relationships produce wrong numbers silently — the visuals will appear to work but the aggregations will be corrupted.
 
### Relationship Structure
 
The final model uses a star schema with employees as the central table:
 
| Relationship | Cardinality | Filter Direction |
|---|---|---|
| employees → attendance | One-to-Many | Single |
| employees → payroll | One-to-Many | Single |
| employees → positions | One-to-One | Merged (see below) |
 
### Handling the Positions Table
 
The positions table had a one-to-one relationship with employees, which caused Power BI to force a bidirectional cross-filter that could not be changed. The solution was to merge positions into employees using Power Query:
 
1. Power Query Editor → Merge Queries
2. Merge employees with positions on employee_id using a Left Outer Join
3. Expand all positions columns into the employees table
4. Disable load on the original positions table
This removed the problematic relationship entirely while preserving all salary and department data inside the employees table.
 
### Why Single Filter Direction Matters
 
Bidirectional filtering allows child tables (attendance, payroll) to filter back into the parent table (employees). This corrupts headcount counts and any employee-level measure. All relationships were set to Single direction — filters flow from employees outward only.
 

 
## Phase 4 — Dashboard Build (Power BI)
 
The dashboard is organised across three pages, each answering a specific set of business questions.
 
---
 
### Page 1 — Workforce Summary
 
**Business questions answered:**
- How many employees does the organisation have?
- How experienced is the workforce?
- Is the workforce balanced by gender?
- Which departments have the most employees?
- Is the organisation growing or shrinking over time?
**Visuals:**
- Headcount card — 300 total employees
- Average tenure card — 6.54 years
- Gender donut chart — 54.33% Male, 45.67% Female
- Attendance records by status donut — Present, Absent, Leave breakdown
- Employees per department bar chart — Procurement is the largest department
- New hires per year line chart — 2021 recorded the highest number of new hires
- Department slicer for filtering all visuals

![Workforce Summary](IMAGES/Workforce.png)
---
 
### Page 2 — Payroll Analysis
 
**Business questions answered:**
- What is the total payroll cost for 2024?
- What is the average annual salary per employee?
- Which departments have the highest salary costs?
- What is the status of payroll payments?
- How do PAYE and NSSF deductions trend across the year?
**Visuals:**
- Total Gross Salary 2024 card — 282M
- Average Annual Net Salary per Employee card — 731.91K
- Net salary by department bar chart — Procurement and Legal are the highest cost departments
- Monthly deductions clustered bar chart — consistent PAYE and NSSF split across all 12 months
- Payroll payment status donut — 91.77% Paid, 8.23% Pending (Nov–Dec)

![Payroll Analysis](IMAGES\payrollanalysis.png)
---
 
### Page 3 — Attendance & Leave Analysis
 
**Business questions answered:**
- How many employees are showing up each month?
- Which months have the highest absence rates?
- How much leave is remaining across departments?
- What are the total present, absent, and leave day counts?
**Visuals:**
- Average leave days remaining card — 38.64 days per employee
- Total Leave Days card — 5,354
- Total Present Days card — 67K
- Total Absent Days card — 6,569
- Attendance status by department clustered bar chart
- Attendance status by month stacked bar chart — all 12 months of 2024
- Leave days remaining by department ranked bar chart
- Month slicer — filters all cards and charts dynamically

![Attendance Analysis](IMAGES\Attendance.png)
---
 
## Key Insights
 
| Insight | Value |
|---|---|
| Total employees | 300 |
| Average tenure | 6.54 years |
| Largest department | Procurement |
| Total gross payroll (2024) | 282M KES |
| Payroll pending (Nov–Dec) | 18M KES (8.23%) |
| Overall attendance rate | ~85% |
| Average leave days remaining | 38.64 days |
| Peak hiring year | 2021 |
 
---
 
## Challenges & Solutions
 
**Challenge 1: Bidirectional filter lock on positions table**  
Power BI forced bidirectional filtering on the one-to-one relationship between employees and positions and would not allow it to be changed. The solution was to merge positions into employees using a Left Outer Join in Power Query, then disable the original positions table from loading into the model.
 
**Challenge 2: Wrong cardinality on relationships**  
The initial model had employees → attendance set as Many-to-One instead of One-to-Many, which would have produced incorrect aggregations. The correct setup is employees on the one side and all fact tables (attendance, payroll) on the many side.
 
**Challenge 3: Default aggregations producing wrong numbers**  
Power BI defaults to Average for numeric columns in some visual types. All salary measures were manually verified to use SUM, not Average. This is critical — Average of gross salary across 12 months produces a meaningless number for payroll analysis.
 
**Challenge 4: Determining which visuals to build**  
The approach used was to define 3–5 specific business questions per page before opening the report view. Each visual was only added if it directly answered one of those questions. This prevented both dashboard overload and visual paralysis.
 
---
 
## Data Limitations
 
The attendance data was synthetically generated with a fixed probability distribution applied uniformly across all departments and months. As a result, the attendance rate is consistently 84–85% across every department and every month, making department-level or month-level attendance rate comparisons flat and uninformative.
 
In a real-world deployment, attendance patterns would vary by department, season, and individual, which would make rate-based analysis more valuable. This is a known limitation of the synthetic data generation approach used in this project.
 
---
 
## How to Run This Project
 
### Prerequisites
 
- Python 3.8 or higher
- PostgreSQL 13 or higher
- Power BI Desktop (free download from Microsoft)
- Python libraries: pandas, sqlalchemy, faker, numpy
### Steps
 
1. Clone the repository
2. Run the Jupyter notebooks in order to generate and load data into PostgreSQL
3. Open Power BI Desktop and connect to your PostgreSQL instance
4. Load all four tables and set up relationships as described in Phase 3
5. Merge positions into employees using Power Query as described
6. Build visuals or open the provided .pbix file if included
---
*Project by Cameline Njoroge  | Kenya, 2026*