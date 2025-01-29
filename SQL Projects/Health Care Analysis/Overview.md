# Hospital Data Analysis 
## Business Question Provided by Enayetur Raheem
The dataset represents ten years (1999-2008) of clinical care at 130 US hospitals and integrated delivery networks. It includes over 50 features representing patient and hospital outcomes. Information was extracted from the database for encounters that satisfied the following criteria.

(1)	It is an inpatient encounter (a hospital admission).<br>
(2)	It is a diabetic encounter, that is, one during which any kind of diabetes was entered into the system as a diagnosis.<br>
(3)	The length of stay was at least 1 day and at most 14 days.<br>
(4)	Laboratory tests were performed during the encounter.<br>
(5)	Medications were administered during the encounter.<br>

- What do the instances in this dataset represent?<br>
→ The instances represent hospitalized patient records diagnosed with diabetes.

- Are there recommended data splits?<br>
→ No recommendation. The standard train-test split could be used. Can use three-way holdout split (i.e., train-validation-test) when doing model selection.

- Does the dataset contain data that might be considered sensitive in any way?<br>
→ Yes. The dataset contains information about the age, gender, and race of the patients.

- Has Missing Values?<br>
→ Yes 

Dataset link: https://archive.ics.uci.edu/ml/datasets/diabetes+130-us+hospitals+for+years+1999-2008

## Queries and Results
### Google BigQuery
```sql
SELECT * FROM `dataset-project.Hospital_data.Hospital` limit 10;

-- Total 27 Columns and 101766 Rows
```
`Result`
| encounter_id | patient_nbr | race            | gender | age     | weight | admission_type_id | discharge_disposition_id | admission_source_id | time_in_hospital | payer_code | medical_specialty      | num_lab_procedures | num_procedures | num_medications | number_outpatient | number_emergency | number_inpatient | diag_1 | diag_2 | diag_3 | number_diagnoses | A1Cresult | insulin | change | diabetesMed | readmitted |
|--------------|-------------|-----------------|--------|---------|--------|-------------------|--------------------------|---------------------|------------------|------------|------------------------|--------------------|----------------|-----------------|-------------------|------------------|------------------|--------|--------|--------|------------------|-----------|---------|--------|-------------|------------|
| 15245874     | 7436034     | Caucasian       | Female | [80-90) | ?      | 1                 | 3                        | 1                   | 8                | ?          | InternalMedicine       | 47                 | 0              | 11              | 0                 | 0                | 1                | 3      | 276    | 276    | 8                | None      | No      | No     | TRUE        | NO         |
| 44118072     | 20660841    | Other           | Female | [80-90) | ?      | 6                 | 1                        | 17                  | 3                | ?          | Family/GeneralPractice | 71                 | 0              | 6               | 0                 | 0                | 0                | 3      | 276    | 276    | 6                | None      | No      | No     | TRUE        | NO         |
| 46242192     | 10993320    | Caucasian       | Female | [70-80) | ?      | 1                 | 1                        | 7                   | 5                | ?          | InternalMedicine       | 54                 | 2              | 14              | 0                 | 0                | 0                | 3      | 578    | 401    | 9                | None      | No      | No     | FALSE       | NO         |
| 99745974     | 24640821    | Caucasian       | Female | [60-70) | ?      | 2                 | 11                       | 4                   | 3                | ?          | Cardiology             | 45                 | 3              | 32              | 0                 | 0                | 0                | 3      | 785    | 427    | 7                | None      | No      | No     | TRUE        | NO         |
| 117268386    | 23872788    | Caucasian       | Female | [60-70) | ?      | 3                 | 1                        | 1                   | 7                | MC         | InternalMedicine       | 45                 | 1              | 24              | 0                 | 0                | 0                | 3      | 785    | 276    | 8                | None      | No      | No     | FALSE       | >30        |
| 133884690    | 93890304    | Caucasian       | Male   | [50-60) | ?      | 1                 | 1                        | 7                   | 4                | BC         | ?                      | 86                 | 3              | 10              | 0                 | 0                | 0                | 3      | 578    | 283    | 9                | None      | No      | No     | FALSE       | >30        |
| 153707826    | 85570290    | Caucasian       | Male   | [40-50) | ?      | 1                 | 1                        | 7                   | 7                | MC         | ?                      | 72                 | 4              | 23              | 0                 | 0                | 0                | 3      | 496    | 780    | 9                | None      | No      | No     | FALSE       | NO         |
| 155457882    | 103398138   | Caucasian       | Female | [70-80) | ?      | 3                 | 1                        | 1                   | 7                | SP         | Pulmonology            | 45                 | 0              | 7               | 0                 | 0                | 0                | 3      | 276    | 787    | 6                | None      | No      | No     | FALSE       | NO         |
| 169418952    | 56452356    | Caucasian       | Male   | [70-80) | ?      | 2                 | 1                        | 1                   | 5                | ?          | InternalMedicine       | 2                  | 1              | 9               | 0                 | 0                | 0                | 3      | 428    | 276    | 9                | None      | No      | No     | FALSE       | NO         |
| 205293672    | 89910360    | AfricanAmerican | Female | [30-40) | ?      | 2                 | 1                        | 7                   | 5                | MD         | Emergency/Trauma       | 71                 | 2              | 8               | 0                 | 1                | 0                | 3      | 780    | 564    | 9                | Norm      | No      | No     | FALSE       | >30        |

## SQL Queries and Results
1. Number of records
```sql
SELECT count(*) FROM `dataset-project.Hospital_data.Hospital`; --Total 101766 records
```
---
2. Profile analysis
```sql
select 
  encounter_id, 
  patient_nbr, 
  race, 
  gender, 
  age, 
  weight, 
  admission_type_id, 
  discharge_disposition_id, 
  a1cresult, 
  readmitted
from `dataset-project.Hospital_data.Hospital`
limit 7;
```
`Result`
| encounter_id | patient_nbr | race      | gender | age     | weight | admission_type_id | discharge_disposition_id | a1cresult | readmitted |
|--------------|-------------|-----------|--------|---------|--------|-------------------|--------------------------|-----------|------------|
| 15245874     | 7436034     | Caucasian | Female | [80-90) | ?      | 1                 | 3                        | None      | NO         |
| 44118072     | 20660841    | Other     | Female | [80-90) | ?      | 6                 | 1                        | None      | NO         |
| 46242192     | 10993320    | Caucasian | Female | [70-80) | ?      | 1                 | 1                        | None      | NO         |
| 99745974     | 24640821    | Caucasian | Female | [60-70) | ?      | 2                 | 11                       | None      | NO         |
| 117268386    | 23872788    | Caucasian | Female | [60-70) | ?      | 3                 | 1                        | None      | >30        |
| 133884690    | 93890304    | Caucasian | Male   | [50-60) | ?      | 1                 | 1                        | None      | >30        |
| 153707826    | 85570290    | Caucasian | Male   | [40-50) | ?      | 1                 | 1                        | None      | NO         |
---
3. Distinct counts for each attributes
```sql
WITH Distinct_Counts AS (
  SELECT 
    COUNT(DISTINCT encounter_id) AS encounter_id, 
    COUNT(DISTINCT patient_nbr) AS patient_nbr, 
    COUNT(DISTINCT race) AS race, 
    COUNT(DISTINCT gender) AS gender, 
    COUNT(DISTINCT age) AS age, 
    COUNT(DISTINCT weight) AS weight, 
    COUNT(DISTINCT admission_type_id) AS admission_type_id, 
    COUNT(DISTINCT discharge_disposition_id) AS discharge_disposition_id, 
    COUNT(DISTINCT a1cresult) AS a1cresult, 
    COUNT(DISTINCT readmitted) AS readmitted
  FROM `dataset-project.Hospital_data.Hospital`
)

SELECT Attributes, Distinct_Counts
FROM Distinct_Counts
UNPIVOT (
  Distinct_Counts FOR Attributes IN (encounter_id, patient_nbr, race, gender, age, weight, 
                            admission_type_id, discharge_disposition_id, a1cresult, readmitted)
);
```
`Result`
| Attributes               | Distinct_Counts |
|--------------------------|-----------------|
| encounter_id             | 101766          |
| patient_nbr              | 71518           |
| race                     | 6               |
| gender                   | 3               |
| age                      | 10              |
| weight                   | 10              |
| admission_type_id        | 8               |
| discharge_disposition_id | 26              |
| a1cresult                | 4               |
| readmitted               | 3               |
---
4.  Unique Patients
```sql
select 
encounter_id,
count(patient_nbr) as unique_patient,  
from `dataset-project.Hospital_data.Hospital`
group by encounter_id;
```
---
5. List of patient who have appeared more than 5 times
```sql
select 
patient_nbr,
count(patient_nbr) as unique_patient,  
from `dataset-project.Hospital_data.Hospital`
group by patient_nbr
having count(patient_nbr) > 5;
```
---
6. Count distinct patients
```sql
select 
count(distinct patient_nbr) as unique_patient,
from `dataset-project.Hospital_data.Hospital`

-- Total 71518 distinct patients
```
---
7. Summary table (part 1): Showing the counts and percentages of encounters by admission type
```sql
WITH AdmissionCounts AS (
  SELECT 
    CASE 
      WHEN admission_type_id = 1 THEN 'Emergency'
      WHEN admission_type_id = 2 THEN 'Urgent'
      WHEN admission_type_id = 3 THEN 'Elective'
      WHEN admission_type_id = 4 THEN 'Newborn'
      WHEN admission_type_id = 5 THEN 'Not_Available'
      WHEN admission_type_id = 6 THEN 'NULL (Missing)'
      WHEN admission_type_id = 7 THEN 'Trauma_Center'
      WHEN admission_type_id = 8 THEN 'Not_Mapped'
      ELSE 'Unknown'
    END AS Characteristic,
    COUNT(admission_type_id) AS N
  FROM `dataset-project.Hospital_data.Hospital`
  GROUP BY Characteristic

),
AdjustedTotalCount AS (
  SELECT COUNT(admission_type_id) AS AdjustedTotal 
  FROM `dataset-project.Hospital_data.Hospital`
  WHERE admission_type_id <> 6
),
FinalCounts AS (
  SELECT 
    AC.Characteristic,
    CASE
      WHEN AC.Characteristic = 'NULL (Missing)' THEN 
        CAST(AC.N AS STRING)
      ELSE 
        CONCAT(
          CAST(AC.N AS STRING), ' / ',                  
          CAST(ATC.AdjustedTotal AS STRING), 
          ' (',
          CASE 
            WHEN (AC.N > 0 AND ROUND(100 * (AC.N / ATC.AdjustedTotal), 1) > 0.1) THEN 
              CONCAT(ROUND(100 * (AC.N / ATC.AdjustedTotal), 1), '%')
            ELSE 
              '<0.1%'
          END,
          ')'
        )
    END AS Count_Percentage
  FROM AdmissionCounts AC
  CROSS JOIN AdjustedTotalCount ATC
)
SELECT * FROM FinalCounts
UNION ALL
SELECT 'Admission Type' AS Characteristic, '' AS Count_Percentage
ORDER BY 
  CASE 
    WHEN Characteristic = 'Admission Type' THEN 0
    WHEN Characteristic = 'Elective' THEN 1
    WHEN Characteristic = 'Emergency' THEN 2
    WHEN Characteristic = 'Newborn' THEN 3
    WHEN Characteristic = 'Not_Available' THEN 4
    WHEN Characteristic = 'Not_Mapped' THEN 5
    WHEN Characteristic = 'Trauma_Center' THEN 6
    WHEN Characteristic = 'Urgent' THEN 7
    ELSE 8
  END;
```
`Result`
| Characteristic | Count_Percentage      |
|----------------|-----------------------|
| Admission Type |                       |
| Elective       | 18869 / 96475 (19.6%) |
| Emergency      | 53990 / 96475 (56%)   |
| Newborn        | 10 / 96475 (<0.1%)    |
| Not_Available  | 4785 / 96475 (5%)     |
| Not_Mapped     | 320 / 96475 (0.3%)    |
| Trauma_Center  | 21 / 96475 (<0.1%)    |
| Urgent         | 18480 / 96475 (19.2%) |
| NULL (Missing) | 5291                  |

---
8. Summary table (part 2): Do the admission types differ between genders when it comes to admission at the emergency department?
```sql
select 
  distinct(gender) -- 3 unique gender- 'Female', 'Male' and 'Unknown/Invalid'
FROM `dataset-project.Hospital_data.Hospital`;
```
```sql
WITH AdmissionCounts AS (
  SELECT 
    CASE 
      WHEN admission_type_id = 1 THEN 'Emergency'
      WHEN admission_type_id = 2 THEN 'Urgent'
      WHEN admission_type_id = 3 THEN 'Elective'
      WHEN admission_type_id = 4 THEN 'Newborn'
      WHEN admission_type_id = 5 THEN 'Not_Available'
      WHEN admission_type_id = 6 THEN 'NULL (Missing)'
      WHEN admission_type_id = 7 THEN 'Trauma_Center'
      WHEN admission_type_id = 8 THEN 'Not_Mapped'
      ELSE 'Unknown'
    END AS Characteristic,
    COUNT(CASE WHEN gender = 'Male' THEN 1 ELSE NULL END) AS Male,
    COUNT(CASE WHEN gender = 'Female' THEN 1 ELSE NULL END) AS Female,
    COUNT(CASE WHEN gender = 'Unknown/Invalid' THEN 1 ELSE NULL END) AS Invalid
  FROM `dataset-project.Hospital_data.Hospital`
  GROUP BY Characteristic
),
AdjustedTotalCount AS (
  SELECT 
  COUNT(case when admission_type_id <> 6 THEN 1 ELSE NULL END) AS AdjustedTotal,
  COUNT(case when admission_type_id = 6 and gender = 'Male' THEN 1 ELSE NULL END) AS missing_male,
  COUNT(case when admission_type_id = 6 and gender = 'Female' THEN 1 ELSE NULL END) AS missing_female,
  COUNT(case when admission_type_id = 6 and gender = 'Unknown/Invalid' THEN 1 ELSE NULL END) AS missing_invalid
  FROM `dataset-project.Hospital_data.Hospital`
),

FinalCounts_Male AS (
  SELECT 
    AC.Characteristic,
    CASE
      WHEN AC.Characteristic = 'NULL (Missing)' THEN 
        CAST(ATC.missing_male AS STRING)
      ELSE 
        CONCAT(
          CAST(AC.Male AS STRING), ' / ',                  
          CAST(ATC.AdjustedTotal AS STRING), 
          ' (',
          CASE 
            WHEN (AC.Male > 0 AND ROUND(100 * (AC.Male / ATC.AdjustedTotal), 1) > 0.1) THEN 
              CONCAT(ROUND(100 * (AC.Male / ATC.AdjustedTotal), 1), '%')
            ELSE 
              '<0.1%'
          END,
          ')'
        )
    END AS Male
  FROM AdmissionCounts AC
  CROSS JOIN AdjustedTotalCount ATC
),
FinalCounts_Female AS (
  SELECT 
    AC.Characteristic,
    CASE
      WHEN AC.Characteristic = 'NULL (Missing)' THEN 
        CAST(ATC.missing_female AS STRING)
      ELSE 
        CONCAT(
          CAST(AC.Female AS STRING), ' / ',                  
          CAST(ATC.AdjustedTotal AS STRING), 
          ' (',
          CASE 
            WHEN (AC.Female > 0 AND ROUND(100 * (AC.Female / ATC.AdjustedTotal), 1) > 0.1) THEN 
              CONCAT(ROUND(100 * (AC.Female / ATC.AdjustedTotal), 1), '%')
            ELSE 
              '<0.1%'
          END,
          ')'
        )
    END AS Female
  FROM AdmissionCounts AC
  CROSS JOIN AdjustedTotalCount ATC
),
FinalCounts_Invalid AS (
  SELECT 
    AC.Characteristic,
    CASE
      WHEN AC.Characteristic = 'NULL (Missing)' THEN 
        CAST(ATC.missing_invalid AS STRING)
      ELSE 
        CONCAT(
          CAST(AC.Invalid AS STRING), ' / ',                  
          CAST(ATC.AdjustedTotal AS STRING), 
          ' (',
          CASE 
            WHEN AC.Invalid > 0  THEN 
               '<0.1%'
            ELSE 
              '0%'
          END,
          ')'
        )
    END AS Invalid
  FROM AdmissionCounts AC
  CROSS JOIN AdjustedTotalCount ATC
),
FinalCounts_Gender AS (
  SELECT 
    AC.Characteristic,
    FF.Female,
    FM.Male,
    FI.Invalid
  FROM AdmissionCounts AC
  JOIN FinalCounts_Female FF ON AC.Characteristic = FF.Characteristic
  JOIN FinalCounts_Male FM ON AC.Characteristic = FM.Characteristic
  JOIN FinalCounts_Invalid FI ON AC.Characteristic = FI.Characteristic
)
SELECT * FROM FinalCounts_Gender 
UNION ALL
SELECT 'Admission Type' AS Characteristic, '' AS Male,'' AS Female,'' AS Invalid
ORDER BY 
  CASE 
    WHEN Characteristic = 'Admission Type' THEN 0
    WHEN Characteristic = 'Elective' THEN 1
    WHEN Characteristic = 'Emergency' THEN 2
    WHEN Characteristic = 'Newborn' THEN 3
    WHEN Characteristic = 'Not_Available' THEN 4
    WHEN Characteristic = 'Not_Mapped' THEN 5
    WHEN Characteristic = 'Trauma_Center' THEN 6
    WHEN Characteristic = 'Urgent' THEN 7
    ELSE 8
  END;
```
`Result`
| Characteristic | Female                | Male                  | Invalid           |
|----------------|-----------------------|-----------------------|-------------------|
| Admission Type |                       |                       |                   |
| Elective       | 9840 / 96475 (10.2%)  | 9028 / 96475 (9.4%)   | 1 / 96475 (<0.1%) |
| Emergency      | 29448 / 96475 (30.5%) | 24540 / 96475 (25.4%) | 2 / 96475 (<0.1%) |
| Newborn        | 3 / 96475 (<0.1%)     | 7 / 96475 (<0.1%)     | 0 / 96475 (0%)    |
| Not_Available  | 2609 / 96475 (2.7%)   | 2176 / 96475 (2.3%)   | 0 / 96475 (0%)    |
| Not_Mapped     | 176 / 96475 (0.2%)    | 144 / 96475 (<0.1%)   | 0 / 96475 (0%)    |
| Trauma_Center  | 9 / 96475 (<0.1%)     | 12 / 96475 (<0.1%)    | 0 / 96475 (0%)    |
| Urgent         | 9894 / 96475 (10.3%)  | 8586 / 96475 (8.9%)   | 0 / 96475 (0%)    |
| NULL (Missing) | 2729                  | 2562                  | 0                 |
---

9. Summary table (part 3): Using the patients admitted through the emergency only, create a frequency table for encounter volume by age group.
```sql
select -- 10 unique group
  distinct(age) 
FROM `dataset-project.Hospital_data.Hospital`;

-- 10 unique group in Age
```
```sql
select 
  count(admission_type_id) as emergency_total
FROM `dataset-project.Hospital_data.Hospital`
where medical_specialty = 'Emergency/Trauma';

-- Emergency Total 7565 Records
```
```sql
with em_age as 
(
select 
  age,
  count(admission_type_id) as N
FROM `dataset-project.Hospital_data.Hospital`
where medical_specialty = 'Emergency/Trauma'
group by age
order by age
),

em_count as
( select
    count(admission_type_id) as em_total
FROM `dataset-project.Hospital_data.Hospital`
where medical_specialty = 'Emergency/Trauma'
),

final_table as (
select 
age as Characteristic,
concat(
(cast (N as string)),
'(',
  CONCAT(ROUND(100 * (ea.N / ec.em_total), 1)),'%',
')'
) as N
from em_age as ea
cross join em_count as ec
order by age
)

select * from final_table
union all
select 'Age' as Characteristic, '' as N
order by Characteristic;
```
`Result`
| Characteristic | N           |
|----------------|-------------|
| Age            |             |
| [0-10)         | 1(0%)       |
| [10-20)        | 30(0.4%)    |
| [20-30)        | 132(1.7%)   |
| [30-40)        | 255(3.4%)   |
| [40-50)        | 796(10.5%)  |
| [50-60)        | 1172(15.5%) |
| [60-70)        | 1502(19.9%) |
| [70-80)        | 1692(22.4%) |
| [80-90)        | 1650(21.8%) |
| [90-100)       | 335(4.4%)   |

---
10. Business Solution 1: Is there any association between admission type and blood glucose level (A1Cresult)?
- Consider admission type 1, 2, 3 only
- Exclude the cases where A1C result is not available or None
  
```sql
WITH PartA AS (
  SELECT 
    CASE 
      WHEN admission_type_id = 1 THEN 1
      WHEN admission_type_id = 2 THEN 2
      WHEN admission_type_id = 3 THEN 3
    END AS Characteristic,
    COUNT(CASE WHEN A1Cresult = ">7" OR A1Cresult = ">8" THEN 1 END) AS Diabetic,
    COUNT(CASE WHEN A1Cresult = "Norm" THEN 1 END) AS Normal
  FROM `dataset-project.Hospital_data.Hospital`
  WHERE A1Cresult <> "None" AND admission_type_id IN (1, 2, 3)
  GROUP BY 1
  ORDER BY 1 ASC
),
Total_cal AS (
  SELECT
    COUNT(CASE WHEN A1Cresult = ">7" OR A1Cresult = ">8" THEN 1 END) AS Total_Diabetic,
    COUNT(CASE WHEN A1Cresult = "Norm" THEN 1 END) AS Total_Normal
  FROM `dataset-project.Hospital_data.Hospital`
  WHERE A1Cresult <> "None" AND admission_type_id IN (1, 2, 3)
),
PartB AS (
  SELECT
    cast(a.Characteristic AS STRING) AS Characteristic,
    CONCAT(
      CAST(a.Diabetic AS STRING), " (", 
      ROUND(100 * (a.Diabetic / T.Total_Diabetic), 1), "%)"
    ) AS Diabetic,
    CONCAT(
      CAST(a.Normal AS STRING), " (", 
      ROUND(100 * (a.Normal / T.Total_Normal), 1), "%)"
    ) AS Normal
  FROM PartA AS a
  CROSS JOIN Total_cal AS T 
)

SELECT * FROM PartB
UNION ALL
SELECT 'admiss_type' AS Characteristic, ' ' AS Diabetic, ' ' AS Normal
ORDER BY 
  CASE 
    WHEN Characteristic = 'admiss_type' THEN 0
    WHEN Characteristic = '1' THEN 1
    WHEN Characteristic = '2' THEN 2
    WHEN Characteristic = '3' THEN 3
  END;
```

`Result`

| Characteristic | Diabetic     | Normal       |
|----------------|--------------|--------------|
| admiss_type    |              |              |
| 1              | 7207 (66.7%) | 3161 (67.4%) |
| 2              | 2248 (20.8%) | 945 (20.1%)  |
| 3              | 1348 (12.5%) | 585 (12.5%)  |
---

11. Business Question 2:Are there patients admitted through the emergency, having A1C results 8 or more who expired(died)?
 ```sql
WITH case1 AS (
  SELECT 
    age, 
    COUNT(CASE WHEN gender = 'Male' THEN 1 ELSE NULL END) AS Male,
    COUNT(CASE WHEN gender = 'Female' THEN 1 ELSE NULL END) AS Female
  FROM `dataset-project.Hospital_data.Hospital`
  WHERE 
    admission_type_id = 1 AND 
    A1Cresult <> "None" AND 
    A1Cresult = ">8" AND
    discharge_disposition_id = 11
  GROUP BY age
  ORDER BY age ASC
),

total_count AS (
  SELECT 
    COUNT(CASE WHEN gender = 'Male' THEN 1 ELSE NULL END) AS total_male,
    COUNT(CASE WHEN gender = 'Female' THEN 1 ELSE NULL END) AS total_female
  FROM `dataset-project.Hospital_data.Hospital`
  WHERE 
    admission_type_id = 1 AND 
    A1Cresult <> "None" AND 
    A1Cresult = ">8" AND
    discharge_disposition_id = 11
),

final AS (
  SELECT 
    c.age AS Characteristic,
    CONCAT(
      CAST(c.Male AS STRING), " (",
      ROUND(100 * (c.Male / t.total_male), 1), "%)"
    ) AS Male,
    CONCAT(
      CAST(c.Female AS STRING), " (",
      ROUND(100 * (c.Female / t.total_female), 1), "%)"
    ) AS Female
  FROM case1 AS c
  CROSS JOIN total_count AS t
)

SELECT * FROM final
UNION ALL
SELECT "Age Group" as Characteristic, " " as Male, "" as Female
ORDER BY Characteristic ASC;
```

`Result`
| Characteristic | Male      | Female    |
|----------------|-----------|-----------|
| Age Group      |           |           |
| [20-30)        | 1 (3.4%)  | 0 (0%)    |
| [40-50)        | 4 (13.8%) | 0 (0%)    |
| [50-60)        | 4 (13.8%) | 0 (0%)    |
| [60-70)        | 7 (24.1%) | 0 (0%)    |
| [70-80)        | 7 (24.1%) | 3 (33.3%) |
| [80-90)        | 5 (17.2%) | 5 (55.6%) |
| [90-100)       | 1 (3.4%)  | 1 (11.1%) |
---

12. Business Question 3:Are there patients admitted through the emergency, having A1C results 8 or more who were readmitted within 30 days?
- age, Gender(male, female) filter by emergency and A1C level >8 and readmitted <30
- admission_type_id = 1 THEN 'Emergency'
- A1Cresult = ">8"
- A1Cresult <> "None"
- readmitted = "<30"

```sql
WITH case1 AS (
  SELECT 
    age, 
    COUNT(CASE WHEN gender = 'Male' THEN 1 ELSE NULL END) AS Male,
    COUNT(CASE WHEN gender = 'Female' THEN 1 ELSE NULL END) AS Female
  FROM `dataset-project.Hospital_data.Hospital`
  WHERE 
    admission_type_id = 1 AND 
    A1Cresult <> "None" AND 
    A1Cresult = ">8" AND
    readmitted = "<30"
  GROUP BY age
  ORDER BY age ASC
),

total_count AS (
  SELECT 
    COUNT(CASE WHEN gender = 'Male' THEN 1 ELSE NULL END) AS total_male,
    COUNT(CASE WHEN gender = 'Female' THEN 1 ELSE NULL END) AS total_female
  FROM `dataset-project.Hospital_data.Hospital`
  WHERE 
    admission_type_id = 1 AND 
    A1Cresult <> "None" AND 
    A1Cresult = ">8" AND
    readmitted = "<30"
),

final AS (
  SELECT 
    c.age AS Characteristic,
    CONCAT(
      CAST(c.Male AS STRING), " (",
      ROUND(100 * (c.Male / t.total_male), 1), "%)"
    ) AS Male,
    CONCAT(
      CAST(c.Female AS STRING), " (",
      ROUND(100 * (c.Female / t.total_female), 1), "%)"
    ) AS Female
  FROM case1 AS c
  CROSS JOIN total_count AS t
)

SELECT * FROM final
UNION ALL
SELECT "Age Group" as Characteristic, " " as Male, "" as Female
ORDER BY Characteristic ASC
```

`Result`

| Characteristic | Male       | Female     |
|----------------|------------|------------|
| Age Group      |            |            |
| [0-10)         | 1 (0.4%)   | 0 (0%)     |
| [10-20)        | 2 (0.8%)   | 4 (1.5%)   |
| [20-30)        | 6 (2.5%)   | 12 (4.5%)  |
| [30-40)        | 17 (7.1%)  | 24 (9%)    |
| [40-50)        | 46 (19.3%) | 38 (14.2%) |
| [50-60)        | 39 (16.4%) | 51 (19.1%) |
| [60-70)        | 53 (22.3%) | 50 (18.7%) |
| [70-80)        | 60 (25.2%) | 58 (21.7%) |
| [80-90)        | 11 (4.6%)  | 24 (9%)    |
| [90-100)       | 3 (1.3%)   | 6 (2.2%)   |
