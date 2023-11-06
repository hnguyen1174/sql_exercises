## 1. SQL Schema

```sql
Create table If Not Exists Candidates (employee_id int, experience ENUM('Senior', 'Junior'), salary int);
Truncate table Candidates;
insert into Candidates (employee_id, experience, salary) values ('1', 'Junior', '10000');
insert into Candidates (employee_id, experience, salary) values ('9', 'Junior', '15000');
insert into Candidates (employee_id, experience, salary) values ('2', 'Senior', '20000');
insert into Candidates (employee_id, experience, salary) values ('11', 'Senior', '16000');
insert into Candidates (employee_id, experience, salary) values ('13', 'Senior', '50000');
insert into Candidates (employee_id, experience, salary) values ('4', 'Junior', '40000');
```

```
A company wants to hire new employees. The budget of the company for the salaries is $70000. The company's criteria for hiring are:

Keep hiring the senior with the smallest salary until you cannot hire any more seniors.
Use the remaining budget to hire the junior with the smallest salary.
Keep hiring the junior with the smallest salary until you cannot hire any more juniors.
Write a solution to find the ids of seniors and juniors hired under the mentioned criteria.

Return the result table in any order.

The result format is in the following example.

 

Example 1:

Input:
Candidates table:
+-------------+------------+--------+
| employee_id | experience | salary |
+-------------+------------+--------+
| 1           | Junior     | 10000  |
| 9           | Junior     | 15000  |
| 2           | Senior     | 20000  |
| 11          | Senior     | 16000  |
| 13          | Senior     | 50000  |
| 4           | Junior     | 40000  |
+-------------+------------+--------+
Output: 
+-------------+
| employee_id |
+-------------+
| 11          |
| 2           |
| 1           |
| 9           |
+-------------+
Explanation: 
We can hire 2 seniors with IDs (11, 2). Since the budget is $70000 and the sum of their salaries is $36000, we still have $34000 but they are not enough to hire the senior candidate with ID 13.
We can hire 2 juniors with IDs (1, 9). Since the remaining budget is $34000 and the sum of their salaries is $25000, we still have $9000 but they are not enough to hire the junior candidate with ID 4.
Example 2:

Input:
Candidates table:
+-------------+------------+--------+
| employee_id | experience | salary |
+-------------+------------+--------+
| 1           | Junior     | 25000  |
| 9           | Junior     | 10000  |
| 2           | Senior     | 85000  |
| 11          | Senior     | 80000  |
| 13          | Senior     | 90000  |
| 4           | Junior     | 30000  |
+-------------+------------+--------+
Output: 
+-------------+
| employee_id |
+-------------+
| 9           |
| 1           |
| 4           |
+-------------+
Explanation: 
We cannot hire any seniors with the current budget as we need at least $80000 to hire one senior.
We can hire all three juniors with the remaining budget.
```

## 2. SQL Answer

```sql
WITH CTE AS (
  SELECT 
    employee_id, 
    experience, 
    salary,
    SUM(salary) OVER (PARTITION BY experience ORDER BY salary ASC) AS RN 
  FROM Candidates
)

SELECT employee_id FROM CTE WHERE experience = 'Senior' AND RN < 70000
UNION
SELECT employee_id FROM CTE WHERE experience = 'Junior' AND RN < (SELECT 70000 - IFNULL(MAX(RN),0) FROM CTE WHERE experience = 'Senior' AND RN < 70000)
;
```

```sql
-- Problem 2004
WITH CTE AS (
  SELECT 
    employee_id, 
    experience, 
    salary,
    SUM(salary) OVER (PARTITION BY experience ORDER BY salary ASC) AS RN 
  FROM Candidates
),

CTE2 AS (
    SELECT employee_id, experience FROM CTE WHERE experience = 'Senior' AND RN < 70000
    UNION
    SELECT employee_id, experience FROM CTE WHERE experience = 'Junior' AND RN < (SELECT 70000 - IFNULL(MAX(RN),0) FROM CTE WHERE experience = 'Senior' AND RN < 70000)
)

SELECT 
    t.experience,
    COALESCE(COUNT(employee_id), 0) AS accepted_candidates
FROM (SELECT DISTINCT experience FROM Candidates) t
LEFT JOIN CTE2 c
    ON t.experience = c.experience
GROUP BY t.experience
;
```