## 1. Question 1

/***
emp: empid, deptid, salary
dept: deptid, deptname 
***/

-- 1. Select departments with at least one employee
SELECT
  DISTINCT d.deptname
FROM dept d
JOIN emp e
  ON d.deptid = e.deptid
  
-- 2. Select the person with the highest salary in the department (the fifth highest)?
-- First way
WITH cte AS (
  SELECT
    *,
    DENSE_RANK() OVER (PARTITION BY deptid ORDER BY salary DESC) AS salary_rank 
  FROM emp
)

SELECT DISTINCT empid FROM cte WHERE salary_rank = 1;

-- Second way
WITH cte AS (
  SELECT
    deptid,
    MAX(salary) AS max_salary_dept
  FROM emp
  GROUP BY deptid
)

SELECT
  DISTINCT emp.empid
FROM emp
JOIN cte
  ON emp.deptid = cte.deptid
  AND emp.salary = cte.max_salary_dept

-- 3. Some departments do not have employees. If there are no employees, the default value of the highest salary assignment is required. 
WITH cte AS (
  SELECT
    *,
    DENSE_RANK() OVER (PARTITION BY deptid ORDER BY salary DESC) AS salary_rank 
  FROM emp
)

SELECT
  d.deptid,
  COALESCE(c.salary, 10000) AS max_salary
FROM dept d
LEFT JOIN (SELECT * FROM cte WHERE salary_rank = 1) c
  ON d.deptid = c.deptid
;