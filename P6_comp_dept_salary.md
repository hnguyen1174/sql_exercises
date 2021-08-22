### Tables and Prompts


```sql
CREATE TABLE salary (
  id INT, 
  employee_id INT, 
  amount INT,
  pay_date DATE
);

INSERT INTO salary
VALUES 
(1, 1, 9000, '2017-03-31'),
(2, 2, 6000, '2017-03-31'),
(3, 3, 10000, '2017-03-31'),
(4, 1, 7000, '2017-02-28'),
(5, 2, 6000, '2017-02-28'),
(6, 3, 8000, '2017-02-28');

CREATE TABLE employee (
  employee_id INT, 
  department_id INT
);

INSERT INTO employee
VALUES 
(1, 1),
(2, 2),
(3, 2);
```

### Answers:

```sql
# Write your MySQL query statement below

-- Step 1: Get department id in the salary table by left join
-- Step 2: Calculate company wide average salary by windows function partition by month
-- Step 3: Calculate department wide average salary by windows function partition by both month and department_id
-- Step 4: Get comparison column by case when
-- Step 5: Get pay_month column by case when

SELECT  
  DATE_FORMAT(pay_date, "%Y-%m") AS pay_month,
  department_id,
  CASE WHEN dept_avg > comp_avg THEN 'higher'
  WHEN dept_avg < comp_avg THEN 'lower'
  ELSE 'same' END AS comparison
FROM (
  SELECT DISTINCT
    s.pay_date,
    e.department_id,
    AVG(amount) OVER (PARTITION BY s.pay_date) AS comp_avg,
    AVG(amount) OVER (PARTITION BY s.pay_date, e.department_id) AS dept_avg
FROM 
    salary s
LEFT JOIN 
    employee e
ON s.employee_id = e.employee_id 
) T
ORDER BY department_id ASC, pay_month ASC;
```
