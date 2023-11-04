## 1. Table Schema

```sql
Create table If Not Exists Employees (employee_id int, employee_name varchar(30), manager_id int);
Truncate table Employees;
insert into Employees (employee_id, employee_name, manager_id) values ('1', 'Boss', '1');
insert into Employees (employee_id, employee_name, manager_id) values ('3', 'Alice', '3');
insert into Employees (employee_id, employee_name, manager_id) values ('2', 'Bob', '1');
insert into Employees (employee_id, employee_name, manager_id) values ('4', 'Daniel', '2');
insert into Employees (employee_id, employee_name, manager_id) values ('7', 'Luis', '4');
insert into Employees (employee_id, employee_name, manager_id) values ('8', 'John', '3');
insert into Employees (employee_id, employee_name, manager_id) values ('9', 'Angela', '8');
insert into Employees (employee_id, employee_name, manager_id) values ('77', 'Robert', '1');
```

```
Write a solution to find employee_id of all employees that directly or indirectly report their work to the head of the company.

The indirect relation between managers will not exceed three managers as the company is small.

Return the result table in any order.

The result format is in the following example.

 

Example 1:

Input: 
Employees table:
+-------------+---------------+------------+
| employee_id | employee_name | manager_id |
+-------------+---------------+------------+
| 1           | Boss          | 1          |
| 3           | Alice         | 3          |
| 2           | Bob           | 1          |
| 4           | Daniel        | 2          |
| 7           | Luis          | 4          |
| 8           | Jhon          | 3          |
| 9           | Angela        | 8          |
| 77          | Robert        | 1          |
+-------------+---------------+------------+
Output: 
+-------------+
| employee_id |
+-------------+
| 2           |
| 77          |
| 4           |
| 7           |
+-------------+
Explanation: 
The head of the company is the employee with employee_id 1.
The employees with employee_id 2 and 77 report their work directly to the head of the company.
The employee with employee_id 4 reports their work indirectly to the head of the company 4 --> 2 --> 1. 
The employee with employee_id 7 reports their work indirectly to the head of the company 7 --> 4 --> 2 --> 1.
The employees with employee_id 3, 8, and 9 do not report their work to the head of the company directly or indirectly. 
```

## 2. SQL Answer

```sql
WITH directly AS (
  SELECT 
    DISTINCT e1.employee_id
  FROM Employees e1
  JOIN Employees e2
    ON e1.manager_id = e2.employee_id
    AND e2.employee_id = 1
    AND e1.employee_id <> e2.employee_id
),

indirectly AS (
  SELECT 
    DISTINCT e1.employee_id
  FROM Employees e1
  JOIN Employees e2
    ON e1.manager_id = e2.employee_id
    AND e1.employee_id <> e2.employee_id
  JOIN Employees e3
    ON e2.manager_id = e3.employee_id
    AND e3.employee_id = 1
    AND e2.employee_id <> e3.employee_id
),

indirectly_l2 AS (
  SELECT 
    DISTINCT e1.employee_id
  FROM Employees e1
  JOIN Employees e2
    ON e1.manager_id = e2.employee_id
    AND e1.employee_id <> e2.employee_id
  JOIN Employees e3
    ON e2.manager_id = e3.employee_id
    AND e2.employee_id <> e3.employee_id
  JOIN Employees e4
    ON e3.manager_id = e4.employee_id
    AND e4.employee_id = 1
    AND e3.employee_id <> e4.employee_id
)

SELECT * FROM directly
UNION
SELECT * FROM indirectly
UNION
SELECT * FROM indirectly_l2
;
```

```sql
-- Best
SELECT e1.employee_id
FROM Employees e1,
     Employees e2,
     Employees e3
WHERE e1.manager_id = e2.employee_id
  AND e2.manager_id = e3.employee_id
  AND e3.manager_id = 1 
  AND e1.employee_id != 1
;
```