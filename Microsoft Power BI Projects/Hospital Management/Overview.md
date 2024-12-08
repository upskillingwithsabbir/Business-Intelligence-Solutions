# Healthcare Analytics in Power BI
## Project Link
https://app.powerbi.com/view?r=eyJrIjoiNDUyOWIxMDQtMDdlOS00NDczLWJmZDMtMGVkMTAxYmE3ZGYzIiwidCI6IjdjMjY3ZTkyLWExM2ItNGU1NS05YTc3LTFlZjg2NzE1OTU4NSIsImMiOjEwfQ%3D%3D
## Project Overview
We have developed a Healthcare Analytics Intelligence Solution capable of performing real-time data analysis based on patients and operational dummy data. This solution consolidates & automates all Healthcare reporting and analysis needs in one place, showcasing insights such as **Bed Management, Financial Tracking, Patient flow Management, Discharge & Readmission Rates, Survey & Satisfaction Score, Resource Utilizations, Staff Management.**  Leveraging Statistical Methods, Data Science, Machine Learning & AI, this Solution generates insightful Self-explanatory Analytics which are updated automatically and easy to share with the Stakeholders at different level with different level of access.

_With this Smart Healthcare Analytics Solution, you can make valuable decisions—from tracking patients’ outcomes and managing resource allocations to optimizing budgets and enhancing patient care._

## Data Transformation and Loading
We created these data using Generative AI. Following dataset were used during this project,

**1. C_Doctor:** Contains each doctors information including salary, fees and specialization. <br />
**2. C_TratmentCost:** Contains information about the doctor's fee, test fees, and expenses incurred by the patient. <br />
**3. C_Facility:** Aggregated data categorized by region. <br />
**4. C_Patient Details:** Includes information of each patient, covering both inpatients and outpatients. <br />
**5. C_Nurse:** Contains information of each nurse with department. <br />
**6. C_Supply:** Contains information about every product and its availability. <br />
**7. C_Survey:** Collects information from patients about their hospital experience based on specific parameters. <br />
**8. C_Sat Category:** Collects information from patients about their satisfaction. <br />
**9. C_Survey Qts:** Asks centain questions to every patients. <br />
**10. C_DateTable:** Its a calculated table created by using Visit Date column of C_Patient Details Table.

## DAX Measures
More than 75 DAX were used for this project. Some important DAX are,

<ins>Beds in Use</ins> = 
CALCULATE(
    COUNT('C_Patient Details'[Patient ID]),
    'C_Patient Details'[Admission Date] <> BLANK(),
    'C_Patient Details'[Discharge Date] = BLANK()
)

<ins>Occupancy Rate</ins> = 
DIVIDE([Beds in Use], [Total Beds], 0)

<ins>ICU_BEDS_IN_USE</ins> = 
CALCULATE(
    DISTINCTCOUNT('C_Patient Details'[Patient ID]),
    'C_Patient Details'[Admission Date] <> BLANK(),
    'C_Patient Details'[Discharge Date] = BLANK(),
    'C_Patient Details'[Bed Type] = "ICU Bed"
)

<ins>Census Patients</ins> = 
CALCULATE(
    COUNT('C_Patient Details'[Patient ID]),
    'C_Patient Details'[Admission Date] <> BLANK(),
    'C_Patient Details'[Discharge Date] = BLANK()
)

<ins>Total Discharge Patients</ins> = 
CALCULATE(
    DISTINCTCOUNT('C_Patient Details'[Patient ID]),
    NOT(ISBLANK('C_Patient Details'[Admission Date])),
    NOT(ISBLANK('C_Patient Details'[Discharge Date]))
)

<ins>Total Inpatients</ins> = 
CALCULATE(
    COUNT('C_Patient Details'[Patient ID]),
    'C_Patient Details'[Visit Date] <> BLANK(),
    'C_Patient Details'[Admission Date] <> BLANK()
)

<ins>Total Long Stay Patients</ins> = 
CALCULATE(
    DISTINCTCOUNT('C_Patient Details'[Patient ID]),
    DATEDIFF('C_Patient Details'[Admission Date], 'C_Patient Details'[Discharge Date], DAY) > 10
)

<ins>Total Outpatients</ins> = 
CALCULATE(
    COUNT('C_Patient Details'[Patient ID]),
    'C_Patient Details'[Visit Date] <> BLANK(),
    'C_Patient Details'[Admission Date] = BLANK(),
    'C_Patient Details'[Discharge Date] = BLANK()
)

<ins>Unique Patients Discharged in Last 7 Days from Max Date</ins> = 
VAR MaxDischargeDate = MAX('C_Patient Details'[Discharge Date])
RETURN
CALCULATE(
    DISTINCTCOUNT('C_Patient Details'[Patient ID]),
    'C_Patient Details'[Discharge Date] >= MaxDischargeDate - 6 && 
    'C_Patient Details'[Discharge Date] <= MaxDischargeDate
)

<ins>Satisfaction Rate</ins> = 
DIVIDE(
    CALCULATE(COUNTROWS('C_Survey'), 
        'C_Survey'[Score] IN {4, 5}),
    COUNTROWS('C_Survey')
)

## Data Model
![HealthCare Model](https://github.com/user-attachments/assets/429d5052-c5d6-4342-b117-5514910df918)

## Glance at the Report
### Glossary page
![1 Glossary](https://github.com/user-attachments/assets/43c731fc-8cb7-41a9-806a-b5fed5f69f9e)

### Overview page
![2 Overview](https://github.com/user-attachments/assets/79ab55eb-e8f9-4d23-b607-1486fc5c6741)

### Details page
![3 Details](https://github.com/user-attachments/assets/82d319f5-34e6-439b-bb7c-c553cf75b158)

### Financial page
![4 Financial](https://github.com/user-attachments/assets/964e4e65-b77c-45d9-99af-b17392355ca0)

### Bed Management page
![5 Bed Management](https://github.com/user-attachments/assets/f0df5b32-9494-4801-8b62-0c9063fd8411)

### Division page
![6 Division](https://github.com/user-attachments/assets/d957eaac-2b47-41f4-af92-0b4670ff5557)

### Patient Overview page
![7 Patient Overview](https://github.com/user-attachments/assets/c53a8b11-448d-4847-a79c-677c47dc2d42)

### Doctor page
![8 Doctor](https://github.com/user-attachments/assets/93e7cbd1-4ffe-4138-bf0c-970864f9e77c)

### Satisfaction page
![9 Satisfaction](https://github.com/user-attachments/assets/37c4fc44-03a6-4ab7-b564-10b0e129fc39)

### AI Insights page
![10 AI Insights](https://github.com/user-attachments/assets/d46048eb-76ff-4c86-9799-cc94bf3a0011)

## Conclusion
The Healthcare Analytics Intelligence Solution empowers healthcare organizations to make informed, data-driven decisions by automating reporting, providing real-time insights, and enhancing operational efficiency. By integrating advanced analytics, machine learning, and AI, this solution enables seamless management of resources, patient care, and financial performance while fostering collaboration and transparency across all levels of stakeholders.


