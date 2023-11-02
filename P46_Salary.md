## 1. Question 1

/***
emp: empid, deptid, salary
dept: deptid, deptname 
***/

```sql
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
```

## Question 2

```sql
DROP TABLE IF EXISTS Subscriptions;

CREATE TABLE Subscriptions (
  companyid INT,
  signupdate DATE,
  subscriptiondate DATE,
  terminationdate DATE
);

INSERT INTO Subscriptions VALUES 
(13343128, '2020-02-13', '2020-02-14', NULL),
(13343124, '2020-02-13', '2020-02-14', NULL),
(13343100, '2020-02-13', '2020-02-14', NULL),
(13343090, '2020-02-13', NULL, NULL),
(13343074, '2018-02-13', '2018-02-14', NULL),
(13342758, '2018-03-23', '2018-03-24', NULL), 
(13342732, '2018-04-05', '2018-04-05', NULL),
(13342730, '2018-09-13', NULL, '2018-09-13'),
(11342720, '2017-01-05', '2017-01-06', '2018-02-13'),
(11343128, '2017-02-08', '2017-02-08', '2018-08-13'),
(11342610, '2017-03-23', '2017-04-23', '2018-11-21'),
(11342610, '2017-03-23', '2017-04-23', '2018-11-21'),
(11342720, '2017-03-29', '2017-03-30', NULL)
;

-- Question 1: How many new subscribers have we received every month starting Jan 2017?
WITH new_subscribers AS (
  SELECT 
    *,
    RANK() OVER (PARTITION BY companyid ORDER BY subscriptiondate ASC) AS rn
  FROM Subscriptions
)

SELECT
  DATE_FORMAT(subscriptiondate, '%Y-%m') AS month,
  COUNT(DISTINCT companyid) AS new_subscribers
FROM new_subscribers
WHERE rn = 1
AND subscriptiondate >= DATE('2017-01-01')
GROUP BY 1
;

-- Question 2: How does that metric compare to the same month prior year?
-- First way

WITH cte AS (
  SELECT 
    *,
    RANK() OVER (PARTITION BY companyid ORDER BY subscriptiondate ASC) AS rn
  FROM Subscriptions
),

new_subscribers AS (
  SELECT
    YEAR(subscriptiondate) AS year,
    MONTH(subscriptiondate) AS month,
    subscriptiondate,
    COUNT(DISTINCT companyid) AS new_subscribers
  FROM cte
  WHERE rn = 1
  AND subscriptiondate >= DATE('2017-01-01')
  GROUP BY 1,2,3
)

SELECT
  DATE_FORMAT(n1.subscriptiondate, '%Y-%m') AS month,
  n1.new_subscribers,
  COALESCE(n2.new_subscribers, 0) AS new_subscribers_prior_year
FROM new_subscribers n1
LEFT JOIN new_subscribers n2
  ON n1.month = n2.month
  AND n1.year - 1 = n2.year
;

-- Question 3: What is the conversion rate of signups for Dec 2018?
WITH signup AS (
  SELECT 
    DATE_FORMAT(signupdate, '%Y-%m') AS month,
    COUNT(DISTINCT companyid) AS signups
  FROM Subscriptions
  GROUP BY 1
),

subscription AS (
  SELECT 
    DATE_FORMAT(subscriptiondate, '%Y-%m') AS month,
    COUNT(DISTINCT companyid) AS subscriptions
  FROM Subscriptions
  GROUP BY 1
)

SELECT
  COALESCE(s.month, ss.month) AS month,
  COALESCE(s.signups, 0) AS signups,
  COALESCE(ss.subscriptions, 0) AS subscriptions,
  COALESCE(COALESCE(ss.subscriptions, 0) * 100.0 / COALESCE(s.signups, 0), 0) AS conversion_rate
FROM signup s
LEFT JOIN subscription ss
  ON s.month = ss.month
;

-- Question 4: There's another table (Free_Subs) with companyIDs that got a free subscription. How do we exclude them from our previous metrics?

WHERE companyid NOT IN (SELECT DISTINCT companyid in FreeHubs);
```