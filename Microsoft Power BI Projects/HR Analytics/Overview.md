# HR Analytics in Power BI
## Project Link
https://app.powerbi.com/view?r=eyJrIjoiOWM3MjlkMmItNDE3ZC00YjZiLWFlZGQtY2UwMjUxMjkzMGM2IiwidCI6IjdjMjY3ZTkyLWExM2ItNGU1NS05YTc3LTFlZjg2NzE1OTU4NSIsImMiOjEwfQ%3D%3D

## Project Overview

We have developed an HR Analytics Intelligence Solution capable of performing real-time data analysis based on your employee data. This solution consolidates & automates all HR reporting and analysis needs in one place, showcasing insights such as **9-box Grid, Employee Turnover/Attrition, Survey & Satisfaction Score, Budget, Increment & Salary Distributions, Resource Utilization, and Leave Management.** Leveraging Statistical Methods, Data Science, Machine Learning & AI, this Solution generates insightful Self-explanatory Analytics which are updated automatically and easy to share with the Stakeholders at different levels with different levels of access.
With this Smart HR Analytics Solution you can make valuable decisions—from tracking employee performance and managing increments to optimizing budgets.

_With this Smart HR Analytics Solution you can make valuable decisions—from tracking employee performance and managing increments to optimizing budgets._

## Data Transformation and Loading
We collected data from a Industry expert and open source. Following dataset were used during this project,

**1. FactPerformanceRating:** Contains information about each employee's job performance ratings across different parameters, along with their manager's ratings of them. <br />
**2. DimEmployee:** Contains employee's personal information, promotion, hiredate, salary and others. <br />
**3. Salary:** Contains employee's salary releted information. <br />
**4. DimEducation:** Contains employee's educational level information upto doctorate degree. <br />
**5. DimSatisfiedLevel:** Contains information about satisfaction levels, rated on a scale from 1 to 5. <br />
**6. DimRatingLevel:** Contains information about rating levels, rated on a scale from 1 to 5. <br />
**7. LeaveRecords:** Contains information about employees' current leave status, including approved and in-process leave requests. <br />
**8. CapexComponents:** Collects information about products purchased by the company for employee use. <br />
**9. DimDate:** Its a calculated table created based on DimEmployee[HireDate].

## DAX Measures
More than 65 DAX were used for this project. Some important DAX are,

<ins>Dynamic Variance %</ins> = 
// MONTH
var latestMonth = CALCULATE(MAX('DimDate'[MonthRelative]), 'DimDate'[Date] = MAX(DimEmployee[HireDate])) <br />
var priorMonthAttr = CALCULATE([% Attrition Rate Date], 'DimDate'[MonthRelative] = latestMonth - 1) <br />
var latestMonthAttr = CALCULATE([% Attrition Rate Date], 'DimDate'[MonthRelative] = latestMonth) <br />
var monthAttrChange = DIVIDE(latestMonthAttr - priorMonthAttr, priorMonthAttr) <br />
var formattedMonthAttr = IF(monthAttrChange < 0, "▼ " & FORMAT(ABS(monthAttrChange), "0.00%"), "▲ " & FORMAT(monthAttrChange, "0.00%")) & " vs previous month" <br />
// QUARTER <br />
var latestQuarter = CALCULATE(MAX('DimDate'[QuarterRelative]), 'DimDate'[Date] = MAX(DimEmployee[HireDate])) <br />
var priorQuarterAttr = CALCULATE([% Attrition Rate Date], 'DimDate'[QuarterRelative] = latestQuarter - 1) <br />
var latestQuarterAttr = CALCULATE([% Attrition Rate Date], 'DimDate'[QuarterRelative] = latestQuarter) <br />
var quarterAttrChange = DIVIDE(latestQuarterAttr - priorQuarterAttr, priorQuarterAttr) <br />
var formattedQuarterAttr = IF(quarterAttrChange < 0, "▼ " & FORMAT(ABS(quarterAttrChange), "0.00%"), "▲ " & FORMAT(quarterAttrChange, "0.00%")) & " vs previous quarter" <br />
// YEAR <br />
var latestYear = CALCULATE(MAX('DimDate'[YearRelative]), 'DimDate'[Date] = MAX(DimEmployee[HireDate])) <br />
var priorYearAttr = CALCULATE([% Attrition Rate Date], 'DimDate'[YearRelative] = latestYear - 1) <br />
var latestYearAttr = CALCULATE([% Attrition Rate Date], 'DimDate'[YearRelative] = latestYear) <br />
var yearAttrChange = DIVIDE(latestYearAttr - priorYearAttr, priorYearAttr) <br />
var formattedYearAttr = IF(yearAttrChange < 0, "▼ " & FORMAT(ABS(yearAttrChange), "0.00%"), "▲ " & FORMAT(yearAttrChange, "0.00%")) & " vs previous year" <br />
// FINAL RETURN <br />
return SWITCH( <br />
        TRUE(), <br />
             // Use the field parameter value for switching <br />
            SELECTEDVALUE('DATE'[DATE  Order]) = 0, formattedMonthAttr, <br />
            SELECTEDVALUE('DATE'[DATE  Order]) = 1, formattedQuarterAttr, <br />
            SELECTEDVALUE('DATE'[DATE  Order]) = 2, formattedYearAttr, <br />
            BLANK() <br />
) <br />

<ins>Total High_Performer_High_Potential</ins> = CALCULATE(COUNTAX(DimEmployee, DimEmployee[PerformanceNew]), DimEmployee[Attrition] = "No", DimEmployee[PerformanceNew] = "Exceeded Expectations",DimEmployee[PotentialNew]="High")

<ins>Coming Year New Salary with What IF</ins> =  
SUMX(
    'DimEmployee',
    DimEmployee[Salary] * (1+ DimEmployee[9BoxKPI_Value])
)

<ins>Total Meet_Performer_High_Potential</ins> = CALCULATE(COUNTAX(DimEmployee, DimEmployee[PerformanceNew]), DimEmployee[Attrition] = "No",DimEmployee[PerformanceNew] = "Met Expectations",DimEmployee[PotentialNew]="High")

<ins>Total_Meet_Performer_Low_Potential</ins> = <br /> 
CALCULATE( <br />
    COUNTAX(DimEmployee,DimEmployee[PerformanceNew]),  <br />
    DimEmployee[Attrition/Retention] = "Retention", <br />
    DimEmployee[PerformanceNew] = "Met Expectations", <br />
    DimEmployee[PotentialNew] = "Low" <br />
)

<ins>ActiveEmployees</ins> = CALCULATE([TotalEmployees], FILTER(DimEmployee, DimEmployee[Attrition] = "No"))

<ins>InactiveEmployees</ins> = CALCULATE([TotalEmployees], FILTER(DimEmployee, DimEmployee[Attrition] = "Yes"))

<ins>Employee Multirow Details</ins> =  <br />
"Attrition date: " & FORMAT(CALCULATE(MIN('DimEmployee'[HireDate]),DimEmployee[Attrition] = "Yes"), "DD MMM, YYYY") & UNICHAR(10) & <br />
"Avg. Satisfaction Score: " &ROUND(AVERAGE('FactPerformanceRatingUnpivot'[Satisfaction Score]), 2) & UNICHAR(10) &  <br />
"Performance Rating: " & MIN(FactPerformanceRating[ManagerRating]) & UNICHAR(10) & <br />
"Monthly Income: " & MIN(DimEmployee[Salary]) & UNICHAR(10) & <br />
"Education Level: " & MIN(DimEducationLevel[EducationLevel]) & UNICHAR(10) & <br />
"Total Stay: " & MIN(Salary[YearsAtCompany]) & UNICHAR(10)  <br />

<ins>EnvironmentSatisfaction</ins> = 
CALCULATE (
    MAX ( FactPerformanceRating[EnvironmentSatisfaction] ),
    USERELATIONSHIP ( FactPerformanceRating[EnvironmentSatisfaction], DimSatisfiedLevel[SatisfactionID] )
)

<ins>Matrix Conditional Formatting</ins> =  <br />
var empCount = CALCULATE(DISTINCTCOUNT('FactPerformanceRatingUnpivot'[EmployeeID])) <br />
var attRetVal = MIN(DimEmployee[Attrition/Retention]) <br />
//This switch checks the attrition/retention value and uses a different color set for each <br />
return SWITCH( <br />
        TRUE(),  <br />
            attRetVal = "Attrition" && empCount > 64, "#AB3438", <br />
            attRetVal = "Attrition" && empCount >= 57, "#bd595c", <br />
            attRetVal = "Attrition" && empCount >= 52, "#d9797c", <br />
            attRetVal = "Attrition", "#737070", <br />
            attRetVal = "Retention" && empCount > 386, "#484848", <br />
            attRetVal = "Retention" && empCount > 360, "#737070", <br />
            attRetVal = "Retention" && empCount > 244, "#737070", <br />
            attRetVal = "Retention", "#737070", <br />
        BLANK() <br />
) <br />

## Data Model
![HR Model](https://github.com/user-attachments/assets/a2930665-9a1a-4629-bb4b-5a74f6c58973)

## Glance at the Report
### Glossary page
![1 Glossary](https://github.com/user-attachments/assets/21950d09-af2f-444d-bc6f-2d0f334fe41f)

### HR Overview page
![2 HR Overview](https://github.com/user-attachments/assets/e9d06da8-d64b-4ba1-b1ea-a341b56fa21e)

### 9 Box Grid page
![3 (9 Box Grid)](https://github.com/user-attachments/assets/68a38636-53f7-44d4-bb74-667b733ec7b6)

### 9 Box Grid Drill Through page
![3 1 (9 box grid 2)](https://github.com/user-attachments/assets/0add48dd-7faf-4b33-994a-221d159415e1)

### Demographics page
![3 Demographic](https://github.com/user-attachments/assets/e2dc4a38-23b7-488f-8c63-a76fb6cd90c7)

### Increment page
![4 Increment](https://github.com/user-attachments/assets/87f1d9e4-c6b0-42ef-8e2d-1b281ff06452)

### Admin page
![5 Admin](https://github.com/user-attachments/assets/05952950-99b5-4ab3-aaba-ce191070a8fb)

### Attrition page
![6 Attrition](https://github.com/user-attachments/assets/589244d9-7af6-4049-b225-b54e5896afec)

### HR Satisfaction page
![7 HR Satisfaction](https://github.com/user-attachments/assets/912cb6aa-d853-45ab-832f-db548829a420)

### Leave page
![8 Leave](https://github.com/user-attachments/assets/6b180713-1ee7-4a06-b820-9e16dc04890a)

## Conclusion
The HR Analytics Intelligence Solution enables organizations to make strategic, data-driven decisions by automating HR reporting and delivering real-time insights. By integrating advanced analytics, machine learning, and AI, this solution streamlines HR processes, from tracking employee performance and satisfaction to managing budgets and resource allocation. With self-explanatory and easily shareable analytics tailored for stakeholders at various levels, this tool enhances transparency, optimizes workforce management, and drives organizational growth.

