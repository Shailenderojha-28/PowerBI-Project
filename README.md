# PowerBI-Project
Power BI project must clearly state the business problem solved, technical tools used, and direct impact achieved.
Loan-Default-Prediction-Dashboard
Problem Statement
This Power BI project was developed to enable financial institutions, credit risk analysts, and loan officers to proactively monitor, analyze, and manage loan default risks using a rich dataset containing borrower-level information.
The dataset includes key variables such as income, age, credit score, loan amount, loan purpose, employment type, marital status, default status, and more. Understanding how these variables interact helps uncover patterns that contribute to loan defaults and can support better lending decisions.
To ensure enterprise-grade scalability, refresh automation, and efficient query performance, the solution was architected as follows:
🔗 Use of Microsoft SQL Server
We chose Microsoft SQL Server as the primary data source to simulate a real-world enterprise-grade backend where financial data is stored and updated continuously. SQL Server was ideal for:
•	Handling large volumes of structured loan data
•	Supporting relational integrity across borrower attributes
•	Offering high-performance integration with Power BI
•	Enabling stored procedures or advanced T-SQL if needed later
The raw data (initially from Excel) was imported into SQL Server using SSMS. This mimics how many institutions manage production-grade datasets.
________________________________________
🔁 Use of Power BI Dataflow
To decouple data transformation from reporting logic, we used Power BI Dataflows. This architectural decision improves performance, maintainability, and reusability across multiple reports.
Benefits of Dataflows in this project:
•	Created a reusable layer of cleaned and transformed data
•	Enabled centralized data logic for all users across the workspace
•	Allowed use of scheduled refresh and incremental refresh at the data layer
•	Reduced load time and processing in the Power BI Desktop file (PBIX)
The dataflow connected to SQL Server using DirectQuery/Import, and all transformations (e.g., typecasting, column profiling, removing nulls) were performed within Power Query Online. This keeps the PBIX report lightweight and scalable.
________________________________________
🧠 Business Use Case
Financial institutions and microfinance organizations often issue thousands of loans monthly. Without an automated and intelligent analytics system, they risk:
•	Issuing high-value loans to low-credit or high-risk customers
•	Inadequately pricing interest rates due to poor segmentation
•	Failing to detect shifting trends in default behavior
This dashboard solves those challenges by providing:
•	Executive-Level Summaries: KPIs for loan disbursement, interest rates, and default trends
•	Segment-Level Profiling: Insights by age group, credit bin, employment type, marital status
•	Historical Trends: YoY changes in default and issuance rates
•	Predictive Indicators: Visuals that expose where risk is accumulating (e.g., in low-score, self-employed applicants)
________________________________________
By combining SQL Server's stability, Dataflow’s ETL automation, and Power BI’s interactive visual analytics, this solution models a real-world, production-ready reporting pipeline for risk analysis and loan portfolio management.
________________________________________
Steps Followed
•	Step 1: Loaded the dataset from an Excel file into Microsoft SQL Server using SQL Server Management Studio (SSMS).
•	Step 2: Created a new database Loan, and imported the table as Loan_default using the "Import Flat File" wizard.
•	Step 3: Verified the data structure and previewed columns using:
SELECT * FROM dbo.Loan_default;
 
•	Step 4: Launched Power BI Service and created a Dataflow by connecting to the SQL Server database.  
•	Step 5: In Power Query Editor, enabled “Column profiling based on entire dataset” for deeper analysis.
•	Step 6: Verified and fixed data types, especially converting the Loan Date column to DateTime to allow incremental refresh setup.
•	Step 7: Created a calculated column in Power BI to group applicants into different age groups:
Age Group = 
IF('Loan_default'[Age]<=25, "Young",
IF('Loan_default'[Age]<=45, "Middle Age Adults",
"Older Adults"))
 
•	Step 8: Set up Scheduled Refresh in Power BI Service to update the Dataflow daily at 5:30 AM (EAT).
•	Step 9: Configured Incremental Refresh to store 5 years of data and refresh only the last 10 days using the Loan Date column.
•	Step 10: Created key DAX Measures for financial and risk analytics:
Average Income by Employment type = 
CALCULATE(
    AVERAGE('Loan_default'[Income]),
    ALLEXCEPT('Loan_default', 'Loan_default'[EmploymentType])
)

Default Rate by Employment Type = 
VAR totalrecords = COUNTROWS(ALL('Loan_default'))
VAR defaultcases = COUNTROWS(FILTER('Loan_default', 'Loan_default'[Default] = TRUE()))
RETURN CALCULATE(DIVIDE(defaultcases, totalrecords), ALLEXCEPT('Loan_default', 'Loan_default'[EmploymentType])) * 100
•	Step 11: Built 3 separate report pages in Power BI Desktop:
o	Loan Default Overview
o	Applicant Demographics & Financial Profile
o	Financial Risk Metrics
•	Step 12: Inserted text boxes, KPI cards, bar/line/pie charts, ribbon chart, decomposition tree, and matrix visuals.
•	Step 13: Added slicers for:
o	Employment Type
o	Education
o	Credit Score Bins
o	Loan Purpose
o	Age Group
•	Step 14: Created a custom theme and applied layout formatting for clarity and consistency.
•	Step 15: Published report to Power BI Service and validated all refresh configurations.
________________________________________
Report Snapshots
Page 1: Loan Default Overview
•	Visuals Used:
o	Bar Chart: Loan Amount by Purpose (e.g., Home, Business, Education)
o	Bar Chart: Average Income by Employment Type (Full-time, Self-employed, etc.)
o	Bar Chart: Default Rate (%) by Employment Type
o	Area Chart: Average Loan Amount by Age Group (Adults, Middle Age Adults, Seniors, Teens)
o	Line Chart: Default Rate (%) by Year (2013–2018)   Key Insights:
•	Home and Business loans dominate the portfolio, each exceeding $6.5M in disbursement.
•	Full-time employees report the highest average income, with a slight decline across Self-employed and Part-time workers.
•	Unemployed applicants have the highest default rate (3.39%), followed by Part-time (3.01%), while Full-time workers are the least risky (2.36%).
•	Teen borrowers take the smallest average loan, while Middle Age and Senior Citizens receive nearly equal loan amounts.
•	Default rate peaked in 2016 at 11.75%, with slight fluctuation across years, suggesting potential external or seasonal factors affecting repayment.
________________________________________
Page 2: Applicant Demographics & Financial Profile
•	Visuals Used:
o	Line Chart: Median Loan Amount by Credit Score Category (Low to High)
o	Donut Chart: Avg Loan Amount (High Credit) segmented by Age Group and Marital Status
o	Line Chart: Total Loans (Adults) by Credit Score Bins
o	Grouped Column Chart: Loans for Middle Age Adults by Has Mortgage / Has Dependents
o	Line Chart: Loans by Education Type (Bachelor's to PhD)   Key Insights:
•	Median loan amount drops steadily with improving credit scores—borrowers with "Low" credit scores access the largest median loans.
•	Divorced and Single adults hold relatively high loan values under the high credit category—possibly due to individual responsibility or lack of dual income.
•	Adults with “Low” and “Very Low” credit dominate loan volumes, revealing higher risk exposure.
•	Mortgage holders and borrowers with dependents show similar borrowing patterns among Middle Age Adults.
•	Bachelor’s degree holders receive the highest average loan amount; PhD holders are few in number with minimal loan values.
________________________________________
Page 3: Financial Risk Metrics
•	Visuals Used:
o	Line Chart: YoY Loan Amount Change by Year (2013–2018)
o	Line Chart: YoY Default Loans Change by Year
o	Sankey Diagram: YTD Loan Amount by Credit Score Bins and Marital Status
o	Decomposition Tree: Total Loan Amount segmented by Income Bracket and Employment Type   Key Insights:
•	Loan disbursements decreased in 2014 and 2016 but rebounded with a 7.3% growth in 2018.
•	YoY Default Loan Change shows a spike of +2.7% in 2015 and again +1.9% in 2018, indicating volatility in repayment behavior.
•	Sankey visual shows Married borrowers dominate across all credit bins in total YTD loan amount, despite higher defaults among Low credit segments.
•	High Income → Full-time pathway leads to the largest disbursed loan volume ($21.73bn total), confirming that income and employment stability drive loan size.
________________________________________
Insights
[1] Employment & Risk Patterns
•	Self-Employed applicants show higher default rates compared to full-time employees.
•	Part-Time employees have the lowest average income and highest DTI ratios.
[2] Age & Credit Score
•	Most loans are issued to Middle Age Adults (30–45) with moderate default risk.
•	Low Credit Score borrowers are overrepresented in default cases.
[3] Loan Purpose
•	Debt Consolidation and Home Purchase account for the largest share of high-value loans.
•	Education Loans show higher defaults, especially among young borrowers.
[4] YoY Trends
•	YoY Default Rate increased by nearly 8% from previous year.
•	Loan issuance also rose by 12%, indicating looser lending criteria.
________________________________________
Dataset Overview
Column Name	Description
LoanID	Unique identifier for each loan
Age	Borrower’s age
Income	Borrower's annual income
LoanAmount	Total amount requested/approved
CreditScore	Score indicating borrower creditworthiness (300–850 scale)
MonthsEmployed	Duration employed with current employer
NumCreditLines	Total number of active credit lines
InterestRate	Annual percentage rate on loan
LoanTerm	Loan repayment period in months
DTIRatio	Debt-to-Income ratio
Education	Education level (e.g., Bachelor’s, Master’s)
EmploymentType	Full-Time, Part-Time, Self-Employed, etc.
MaritalStatus	Single, Married, Divorced, etc.
HasMortgage	Yes/No – Indicates existing mortgage
HasDependents	Yes/No – Indicates responsibility for dependents
LoanPurpose	Purpose of loan (e.g., Education, Home Purchase, Debt Consolidation)
HasCoSigner	Yes/No – Indicates if a co-signer is present
Default	Yes/No – Indicates loan default
Loan Date	Date loan was issued (DD/MM/YYYY)
________________________________________
Deployment & Automation
•	Scheduled Refresh: Runs daily at 5:30 AM using Power BI Service.
•	Incremental Refresh: Retains 5 years of historical data; refreshes last 10 days only.
•	Notification Setup: Alerts for refresh failures sent to dataflow owner via email.
•	Power BI Workspace: Report and dataflow published to secured workspace.
________________________________________
Conclusion
This project offers a powerful, automated solution for loan default analysis using Microsoft SQL Server and Power BI. Through custom DAX, dataflows, and advanced visuals, it helps credit managers, data analysts, and financial institutions make informed decisions on lending strategies, customer targeting, and risk reduction.
________________________________________
DAX Measures – Purpose and Output
Below are some of the key DAX measures created during the project, including rationale and interpretation of the results. These measures were instrumental in deriving business insights and powering the visualizations across the three report pages.
________________________________________
1. Average Income by Employment Type
Average Income by Employment type = 
CALCULATE(
    AVERAGE('Loan_default'[Income]),
    ALLEXCEPT('Loan_default', 'Loan_default'[EmploymentType])
)
This measure was designed to compare average income across different employment types (e.g., Full-Time, Part-Time, Self-Employed).
What it shows: Self-Employed applicants have significantly lower average income, which correlates with higher default rates.
________________________________________
2. Average Loan by Age Group
Average Loan by Age Group = 
AVERAGEX(
    VALUES('Loan_default'[Age Group]),
    AVERAGE('Loan_default'[LoanAmount])
)
To assess loan distribution by age group and identify which demographic borrows the most.
Insight: Middle Age Adults (30–45) tend to borrow the highest loan amounts, while younger borrowers request smaller loans.
________________________________________
3. Default Rate by Employment Type
Default Rate by Employment Type = 
VAR totalrecords = COUNTROWS(ALL('Loan_default'))
VAR defaultcases = COUNTROWS(FILTER('Loan_default', 'Loan_default'[Default] = TRUE()))
RETURN 
    CALCULATE(
        DIVIDE(defaultcases, totalrecords), 
        ALLEXCEPT('Loan_default', 'Loan_default'[EmploymentType])
    ) * 100
To evaluate which employment category is riskier in terms of defaults.
Insight: Part-time and Self-Employed categories show notably higher default percentages, signaling a credit risk concern.
________________________________________
4. Default Rate per Year
Default Rate per Year = 
VAR total_loans = 
    CALCULATE(
        COUNTROWS('Loan_default'),
        ALLEXCEPT('Loan_default', 'Loan_default'[Year])
    )
VAR default = 
    CALCULATE(
        COUNTROWS(FILTER('Loan_default', 'Loan_default'[Default] = TRUE())),
        ALLEXCEPT('Loan_default', 'Loan_default'[Year])
    )
RETURN 
    DIVIDE(default, total_loans) * 100
This was used to track how default rates evolved yearly.
Insight: The trend showed an increase in defaults over the last two years, despite stable loan issuance volumes.
________________________________________
5. Year-over-Year (YoY) Default Loan Change
YoY Default Loans Change = 
DIVIDE(
    CALCULATE(
        COUNTROWS(FILTER('Loan_default', 'Loan_default'[Default] = TRUE())), 
        'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY]))
    )
    - CALCULATE(
        COUNTROWS(FILTER('Loan_default', 'Loan_default'[Default] = TRUE())), 
        'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY])) - 1
    ),
    CALCULATE(
        COUNTROWS(FILTER('Loan_default', 'Loan_default'[Default] = TRUE())), 
        'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY])) - 1
    ), 0
) * 100
To understand year-over-year shifts in loan default volume.
Insight: A sharp 7.9% increase in default volume was observed in the last calendar year.
________________________________________
6. YoY Loan Amount Change
YoY Loan Amount Change = 
DIVIDE(
    CALCULATE(
        SUM('Loan_default'[LoanAmount]), 
        'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY]))
    )
    - CALCULATE(
        SUM('Loan_default'[LoanAmount]), 
        'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY])) - 1
    ),
    CALCULATE(
        SUM('Loan_default'[LoanAmount]), 
        'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY])) - 1
    ), 0
) * 100
To monitor annual changes in disbursed loan volume.
Insight: Loan disbursements increased by 11.6%, indicating higher borrowing demand or looser underwriting standards.
________________________________________
7. Median Loan Amount by Credit Score Bin
Median by Credit Score Bins = 
MEDIANX('Loan_default', 'Loan_default'[LoanAmount])
This measure was used to assess loan distributions by creditworthiness brackets.
Insight: Higher credit score bins were associated with larger median loans, aligning with responsible borrowing patterns.
________________________________________
8. Total Loan by Middle Age Adults
Total Loan(Middle Age Adults) = 
SUMX(
    FILTER('Loan_default', 'Loan_default'[Age Group] = "Middle Age Adults"),
    'Loan_default'[LoanAmount]
)
To isolate the segment that contributes the most to the loan portfolio.
Insight: Middle-aged borrowers accounted for 53% of the total loan disbursement volume.
________________________________________
This rich set of measures provides both descriptive and predictive power to the dashboard, helping stakeholders to:
•	Quantify borrower risk
•	Profile demographics
•	Track lending performance
•	Strategize for better credit policy



